# SMB Enumeration

## 📁 Vue d'ensemble

SMB (Server Message Block) est un protocole de **partage de fichiers** utilisé principalement dans les environnements Windows. C'est souvent une **mine d'or d'informations** car les administrateurs configurent mal les permissions ou laissent des partages ouverts.

> **💡 Explication Simple** : SMB c'est comme un classeur partagé dans un bureau. Si les tiroirs ne sont pas verrouillés, on peut voir tous les documents confidentiels de l'entreprise !

**Ports SMB :**
- **Port 139** : NetBIOS Session Service  
- **Port 445** : Microsoft-DS (SMB direct over TCP)

## 🎯 Objectifs de l'Énumération SMB

1. **Identifier la version SMB** - SMBv1 (vulnérable), SMBv2, SMBv3
2. **Lister les partages disponibles** - Quels dossiers sont partagés ?
3. **Énumérer les utilisateurs** - Qui a accès au système ?
4. **Tester les permissions** - Peut-on lire/écrire dans les partages ?
5. **Collecter les informations système** - Nom de domaine, OS, etc.

## 🔍 Phase 1 : Reconnaissance SMB

### Detection et Version
```bash
# 1. Nmap pour détecter SMB
nmap -p 139,445 target.com

# 2. Version détaillée du service SMB
nmap -sC -sV -p 139,445 target.com

# 3. Scripts Nmap spécifiques SMB
nmap --script smb-protocols target.com
nmap --script smb-security-mode target.com
nmap --script smb-os-discovery target.com

# 4. Banner grabbing avec netcat
echo "" | nc target.com 445
```

**Informations importantes à noter :**
- Version SMB (v1 = très vulnérable !)
- OS version (Windows 7, 10, Server 2016...)
- Nom NetBIOS et domaine
- Mode de sécurité (user-level vs share-level)

### Metasploit - SMB Version Detection
```bash
msfconsole
use auxiliary/scanner/smb/smb_version
set RHOSTS target.com
run

# Résultats typiques :
# [+] target.com:445 - Host is running Windows 7 Professional SP1 (build:7601) (name:VICTIM-PC) (domain:WORKGROUP)
```

## 🗂️ Phase 2 : Énumération des Partages

### Méthodes Manuelles

#### smbclient (Outil principal Linux)
```bash
# 1. Lister les partages (connexion anonyme)
smbclient -L //target.com -N

# 2. Avec null session (utilisateur vide)
smbclient -L //target.com -U ""

# 3. Si authentification requise
smbclient -L //target.com -U username

# 4. Se connecter à un partage spécifique
smbclient //target.com/sharename -N

# 5. Une fois connecté, commandes utiles :
# smb: \> ls                    # Lister les fichiers
# smb: \> get filename          # Télécharger un fichier
# smb: \> put localfile        # Uploader un fichier
# smb: \> cd directory         # Changer de répertoire
# smb: \> pwd                  # Répertoire actuel
```

#### enum4linux (L'outil tout-en-un)
```bash
# 1. Énumération complète
enum4linux -a target.com

# 2. Énumération spécifique par type
enum4linux -S target.com    # Partages seulement
enum4linux -U target.com    # Utilisateurs seulement
enum4linux -G target.com    # Groupes seulement
enum4linux -P target.com    # Politique de mots de passe

# 3. Avec authentification
enum4linux -u username -p password -a target.com

# 4. Verbose pour plus de détails
enum4linux -v -a target.com
```

**Que chercher dans les résultats enum4linux :**
- `Share Enumeration` : Liste des partages
- `Users/Groups` : Comptes utilisateurs
- `Password Policy` : Politique de mots de passe
- `Domain Information` : Infos sur le domaine

#### smbmap (Mapping avancé)
```bash
# 1. Cartographie des partages avec permissions
smbmap -H target.com

# 2. Avec authentification
smbmap -H target.com -u username -p password

# 3. Recherche de fichiers par pattern
smbmap -H target.com -R --pattern "*.txt"

# 4. Download automatique de fichiers
smbmap -H target.com -R -A "*.config"

# 5. Execute commands (si droits admin)
smbmap -H target.com -u admin -p password -x 'whoami'
```

### Metasploit - SMB Enumeration
```bash
msfconsole

# 1. Énumération des partages
use auxiliary/scanner/smb/smb_enumshares
set RHOSTS target.com
set SMBUser ""         # Pour null session
set SMBPass ""
run

# 2. Énumération des utilisateurs
use auxiliary/scanner/smb/smb_enumusers
set RHOSTS target.com
run

# 3. Énumération avec SID (Security Identifier)
use auxiliary/scanner/smb/smb_lookupsid
set RHOSTS target.com
run

# 4. Informations système via SMB
use auxiliary/scanner/smb/smb_ms17_010  # Check EternalBlue
set RHOSTS target.com
run
```

## 👥 Phase 3 : Énumération des Utilisateurs

### Techniques Manuelles
```bash
# 1. Énumération via RPC (Remote Procedure Call)
rpcclient -U "" -N target.com

# Une fois connecté à rpcclient :
# rpcclient $> enumdomusers         # Liste utilisateurs du domaine
# rpcclient $> enumdomgroups        # Liste groupes du domaine  
# rpcclient $> queryuser 500        # Info sur utilisateur RID 500 (admin)
# rpcclient $> querygroupmem 512    # Membres du groupe admin (RID 512)

# 2. Avec enum4linux pour utilisateurs
enum4linux -U target.com

# 3. Brute force SID pour trouver utilisateurs
for i in $(seq 500 1100); do
    rpcclient -N -U "" target.com -c "queryuser $i" 2>/dev/null | grep "User Name"
done
```

### Metasploit - User Enumeration
```bash
msfconsole

# 1. SMB User enumeration
use auxiliary/scanner/smb/smb_enumusers
set RHOSTS target.com
run

# 2. SID Lookup pour brute force users
use auxiliary/scanner/smb/smb_lookupsid
set RHOSTS target.com
set MaxRID 1000      # Jusqu'à RID 1000
run

# 3. SAMR (Security Account Manager Remote) enumeration
use auxiliary/scanner/smb/smb_enumusers_domain
set RHOSTS target.com
run
```

## 🔐 Phase 4 : Test des Credentials et Permissions

### Tests de Connexion
```bash
# 1. Test avec credentials par défaut
smbclient -L //target.com -U guest -N
smbclient -L //target.com -U administrator

# 2. Test avec liste d'utilisateurs
for user in admin administrator guest; do
    echo "Testing $user"
    smbclient -L //target.com -U $user -N 2>/dev/null
done

# 3. Test avec Hydra (brute force)
hydra -L users.txt -P passwords.txt target.com smb

# 4. Test avec Metasploit
msfconsole
use auxiliary/scanner/smb/smb_login
set RHOSTS target.com
set USER_FILE users.txt
set PASS_FILE passwords.txt
run
```

### Test des Permissions sur Partages
```bash
# 1. Test lecture/écriture avec smbclient
smbclient //target.com/sharename -N
# smb: \> put testfile.txt    # Test écriture
# smb: \> get existing.txt    # Test lecture

# 2. Avec smbmap pour permissions détaillées
smbmap -H target.com -u guest -p ""

# Résultat type :
# [+] Finding open SMB ports....
# [+] Guest SMB session established on target.com...
# [+] IP: target.com:445  Name: unknown
#         Disk            Permissions
#         ----            -----------
#         ADMIN$          NO ACCESS
#         C$              NO ACCESS  
#         IPC$            NO ACCESS
#         Share           READ, WRITE
```

## 🕵️ Phase 5 : Recherche de Fichiers Sensibles

### Recherche Automatisée
```bash
# 1. Avec smbmap - patterns de fichiers
smbmap -H target.com -R --pattern "*.config"
smbmap -H target.com -R --pattern "password*"
smbmap -H target.com -R --pattern "*.sql"
smbmap -H target.com -R --pattern "*.txt"

# 2. Download automatique de fichiers sensibles
smbmap -H target.com -R -A "password.*"  # Auto-download
smbmap -H target.com -R -A "*.config"

# 3. Recherche dans contenu de fichiers (si accès lecture)
smbclient //target.com/share -N -c "recurse ON; prompt OFF; mget *"
grep -r "password" downloaded_files/
```

### Fichiers à Rechercher Prioritairement
```bash
# Fichiers de configuration
*.config, *.conf, *.cfg
web.config, app.config

# Fichiers de credentials  
*.txt (notes.txt, passwords.txt)
*.doc, *.docx (documentation)
*.xls, *.xlsx (inventaires)

# Fichiers de sauvegarde
*.bak, *.backup, *.old
*.sql (dumps de base)

# Scripts et code source
*.bat, *.ps1, *.vbs
*.php, *.asp, *.jsp
```

## 🎯 Techniques Avancées

### Null Session Attack
```bash
# 1. Connexion null session
net use \\target.com\ipc$ "" /user:""

# 2. Énumération après null session
enum -S target.com        # Partages
enum -U target.com        # Utilisateurs  
enum -P target.com        # Politique mots de passe

# 3. Nettoyage
net use \\target.com\ipc$ /delete
```

### SMB Relay et NTLM
```bash
# 1. Check si SMB signing désactivé (vulnérable au relay)
nmap --script smb-security-mode target.com -p 445

# 2. Responder pour capturer hashes (attaque active)
responder -I eth0 -wrf

# 3. ntlmrelayx pour relay attacks
ntlmrelayx.py -tf targets.txt -smb2support
```

### Montage de Partages SMB
```bash
# 1. Créer point de montage
mkdir /mnt/smb_share

# 2. Monter le partage
mount -t cifs //target.com/sharename /mnt/smb_share -o username=guest,password=

# 3. Exploration avec outils Linux standards
ls -la /mnt/smb_share/
find /mnt/smb_share/ -name "*.txt" -exec grep -l "password" {} \;

# 4. Démonter
umount /mnt/smb_share
```

## 📋 Checklist SMB Enumeration

### ✅ Étapes Essentielles :
1. **Detection SMB** - Ports 139/445 ouverts ?
2. **Version SMB** - SMBv1 (critique!) vs v2/v3 ?
3. **OS Information** - Windows version et nom machine
4. **Share Enumeration** - Quels partages existent ?
5. **Null Session** - Connexion anonyme possible ?
6. **User Enumeration** - Liste des utilisateurs ?
7. **Permission Testing** - Lecture/écriture sur partages ?
8. **Sensitive Files** - Fichiers intéressants trouvés ?

### ✅ Vulnérabilités à Vérifier :
- **SMBv1 Enabled** → EternalBlue (MS17-010)
- **Null Session** → Information disclosure
- **Weak Passwords** → Brute force attack
- **World-Writable Shares** → Malware upload
- **SMB Signing Disabled** → SMB Relay attack

## 🛠️ Script d'Énumération Automatisé

```bash
#!/bin/bash
# SMB Enumeration Script

TARGET=$1
if [ -z "$TARGET" ]; then
    echo "Usage: $0 <target_ip>"
    exit 1
fi

echo "[+] Starting SMB enumeration for $TARGET"
mkdir ${TARGET}_smb_enum

# 1. Port scan
echo "[+] Checking SMB ports..."
nmap -p 139,445 $TARGET > ${TARGET}_smb_enum/ports.txt

# 2. SMB version detection  
echo "[+] Getting SMB version..."
nmap --script smb-os-discovery,smb-protocols $TARGET > ${TARGET}_smb_enum/version.txt

# 3. Share enumeration
echo "[+] Enumerating shares..."
smbclient -L //$TARGET -N > ${TARGET}_smb_enum/shares.txt 2>&1
enum4linux -S $TARGET >> ${TARGET}_smb_enum/shares.txt

# 4. User enumeration
echo "[+] Enumerating users..."
enum4linux -U $TARGET > ${TARGET}_smb_enum/users.txt

# 5. Full enum4linux scan
echo "[+] Running full enum4linux..."
enum4linux -a $TARGET > ${TARGET}_smb_enum/full_enum.txt

# 6. smbmap scan
echo "[+] Running smbmap..."
smbmap -H $TARGET > ${TARGET}_smb_enum/smbmap.txt

echo "[+] SMB enumeration completed!"
echo "[+] Results saved in ${TARGET}_smb_enum/"
```

## ⚠️ Considérations de Sécurité

### Détection et Logs
- SMB enumeration génère beaucoup de logs
- Utiliser des délais entre requêtes
- Éviter les scans agressifs sur les domaines Active Directory

### Aspects Légaux
- SMB enumeration peut être considéré comme intrusion
- S'assurer d'avoir l'autorisation avant de procéder
- Documenter toutes les actions pour le rapport

---
*Cette section couvre l'énumération SMB complète. Pour l'exploitation des vulnérabilités SMB, voir la section 02-Exploitation/Réseaux/* 