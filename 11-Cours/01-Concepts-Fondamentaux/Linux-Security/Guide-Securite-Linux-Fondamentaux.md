# Sécurité Linux - Concepts Fondamentaux

## 🐧 Vue d'ensemble

Linux est le **système d'exploitation le plus utilisé au monde** (serveurs, Android, IoT). Comprendre sa sécurité est **ESSENTIEL** pour tout pentester.

> **💡 Analogie Simple** : Linux c'est comme une forteresse médiévale avec des murs (permissions), des gardes (processus), des portes (services) et un roi (root).

## ⚠️ **IMPORTANT - À propos des exemples**

> **🎯 TOUS les noms d'utilisateurs, chemins, IPs dans ce guide sont FICTIFS !**

### 📝 Exemples vs Réalité :

**Dans le guide (EXEMPLE FICTIF) :**
```
user@serveur-test:/home/alice$ 
192.168.1.100
/var/log/auth.log
alice, bob, service-web
```

**Dans la vraie vie, vous devez adapter :**
```
votre-user@vraie-machine:/home/vrai-user$
IP-réelle-du-serveur
/var/log/[vrai-fichier-log]
vrais-utilisateurs-trouvés
```

## 🎯 Objectifs d'apprentissage

Après cette section, vous saurez :
1. **Architecture sécurisée Linux** - Comment c'est protégé
2. **Système de permissions** - Qui peut faire quoi
3. **Services et démons** - Qu'est-ce qui tourne en arrière-plan
4. **Logs et surveillance** - Comment traquer les activités
5. **Vulnérabilités communes** - Où sont les failles typiques

## 🏗️ Architecture de Sécurité Linux

### 📊 Modèle de sécurité en couches
```
    ┌─────────────────────────────────────────────────────────────┐
    │                    USER SPACE                               │
    │                  (Espace Utilisateur)                      │
    │                                                             │
    │  ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐   │
    │  │ APPLICATIONS│ │  SERVICES   │ │    SHELL/TERMINAL   │   │
    │  │             │ │             │ │                     │   │
    │  │ • firefox   │ │ • apache2   │ │ • bash              │   │
    │  │ • vim       │ │ • sshd      │ │ • zsh               │   │
    │  │ • nmap      │ │ • mysql     │ │ • fish              │   │
    │  └─────────────┘ └─────────────┘ └─────────────────────┘   │
    └─────────────────────────────────────────────────────────────┘
                                    ↕ syscalls
    ┌─────────────────────────────────────────────────────────────┐
    │                    KERNEL SPACE                             │
    │                  (Espace Noyau)                            │
    │                                                             │
    │  ┌─────────────────────────────────────────────────────────┐ │
    │  │                  LINUX KERNEL                           │ │
    │  │                                                         │ │
    │  │ • Process Management    • Memory Management             │ │
    │  │ • File System Security  • Network Stack                │ │
    │  │ • Access Control        • Device Drivers               │ │
    │  │ • Capabilities          • Namespaces                   │ │
    │  │ • SELinux/AppArmor      • Seccomp                      │ │
    │  └─────────────────────────────────────────────────────────┘ │
    └─────────────────────────────────────────────────────────────┘
                                    ↕ hardware interface
    ┌─────────────────────────────────────────────────────────────┐
    │                     HARDWARE                                │
    │                                                             │
    │ CPU • RAM • Storage • Network Cards • USB • Périphériques  │
    └─────────────────────────────────────────────────────────────┘
```

## 👤 Utilisateurs et Permissions

### **Le système UID/GID - EXPLICATIONS DÉTAILLÉES**

#### **UID (User ID) - Identifiant Utilisateur**
> **💭 Définition :** Numéro unique identifiant chaque utilisateur dans le système.

**Plages importantes :**
```
UID 0        = root (super-utilisateur, TOUT pouvoir)
UID 1-99     = Comptes système (daemon, bin, sys)
UID 100-999  = Services système (www-data, mysql, sshd)
UID 1000+    = Utilisateurs normaux (alice, bob, charlie)
```

**Exemples concrets :**
```bash
# Voir les UIDs des utilisateurs
cat /etc/passwd | grep -E "(root|alice|www-data)"
root:x:0:0:root:/root:/bin/bash
alice:x:1000:1000:Alice Martin:/home/alice:/bin/bash
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
```

#### **GID (Group ID) - Identifiant Groupe**
> **💭 Définition :** Numéro unique identifiant chaque groupe dans le système.

**Groupes système importants :**
```
GID 0    = root (groupe administrateur)
GID 4    = adm (lecture des logs système)
GID 27   = sudo (exécution avec sudo)
GID 33   = www-data (serveur web)
GID 1000+ = Groupes utilisateurs
```

### **Permissions Unix - SYSTÈME DÉTAILLÉ**

#### **Le triplet rwx expliqué**
```
EXEMPLE : -rwxr-xr-- alice www-data fichier.txt

┌─── Type de fichier
│    - = fichier normal
│    d = dossier
│    l = lien symbolique
│    
│  ┌─── Permissions PROPRIÉTAIRE (alice)
│  │    rwx = lecture, écriture, exécution
│  │    
│  │  ┌─── Permissions GROUPE (www-data)  
│  │  │    r-x = lecture, pas écriture, exécution
│  │  │    
│  │  │  ┌─── Permissions AUTRES
│  │  │  │    r-- = lecture seulement
│  │  │  │
-  rwx r-x r--  alice www-data fichier.txt
```

#### **Notation octale des permissions**
```
rwx = 111 binaire = 7 décimal
r-x = 101 binaire = 5 décimal  
r-- = 100 binaire = 4 décimal

DONC : rwxr-xr-- = 754

EXEMPLES COURANTS :
755 = rwxr-xr-x (exécutable standard)
644 = rw-r--r-- (fichier normal)
600 = rw------- (fichier privé)
777 = rwxrwxrwx (tout le monde peut tout)
000 = --------- (personne ne peut rien)
```

### **Permissions spéciales - SUID, SGID, Sticky Bit**

#### **SUID (Set User ID) - 4xxx**
> **💭 Définition :** Le fichier s'exécute avec les privilèges du propriétaire, pas de l'utilisateur.

```bash
# Exemple : passwd s'exécute toujours en root
ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 68208 /usr/bin/passwd
      ↑
      s = SUID activé

# Danger : SUID root = escalade de privilèges potentielle
find / -perm -4000 -type f 2>/dev/null
```

#### **SGID (Set Group ID) - 2xxx**
> **💭 Définition :** Le fichier s'exécute avec les privilèges du groupe propriétaire.

```bash
# Sur un dossier : nouveaux fichiers héritent du groupe
chmod 2755 /shared/project
ls -ld /shared/project
drwxr-sr-x 2 alice developers /shared/project
       ↑
       s = SGID activé
```

#### **Sticky Bit - 1xxx**
> **💭 Définition :** Dans un dossier, seul le propriétaire peut supprimer ses fichiers.

```bash
# Exemple : /tmp - tout le monde peut écrire, mais pas supprimer les fichiers des autres
ls -ld /tmp
drwxrwxrwt 10 root root 4096 /tmp
        ↑
        t = Sticky bit activé
```

## 🔐 Utilisateur root et sudo

### **Le compte root - EXPLICATIONS COMPLÈTES**

#### **Pourquoi root est si puissant ?**
```
ROOT (UID 0) PEUT :
• Lire/modifier TOUS les fichiers (même /etc/shadow)
• Tuer TOUS les processus
• Modifier la configuration système
• Installer/désinstaller des logiciels
• Gérer les utilisateurs et groupes
• Accéder directement au hardware
• Bypasser la plupart des restrictions sécuritaires
```

#### **Connexion root vs sudo**
```bash
# CONNEXION DIRECTE ROOT (DANGEREUX)
su -                    # Devient root complètement
ssh root@serveur       # Connexion SSH directe en root

# SUDO (RECOMMANDÉ)  
sudo commande          # Exécute UNE commande en root
sudo -i               # Shell interactif root temporaire
sudo su - alice       # Devenir un autre utilisateur
```

### **Configuration sudo - /etc/sudoers**

#### **Format du fichier sudoers**
```bash
# Syntaxe : user hostname=(runas) commands
alice    ALL=(ALL:ALL) ALL
bob      web-server=(www-data) /usr/bin/systemctl restart apache2
charlie  ALL=(ALL) NOPASSWD: /bin/mount, /bin/umount

# Groupes
%sudo    ALL=(ALL:ALL) ALL
%wheel   ALL=(ALL) NOPASSWD: ALL
```

## 📊 Services et Démons

### **systemd - Le gestionnaire moderne**

#### **Qu'est-ce qu'un service ?**
> **💭 Définition :** Programme qui tourne en arrière-plan, généralement sans interface utilisateur.

```bash
# Lister tous les services
systemctl list-units --type=service

# Services critiques à surveiller
systemctl status sshd          # SSH
systemctl status apache2       # Serveur web  
systemctl status mysql         # Base de données
systemctl status fail2ban      # Protection anti-brute force
```

#### **Gestion des services**
```bash
# Commandes essentielles
systemctl start service        # Démarrer
systemctl stop service         # Arrêter
systemctl restart service      # Redémarrer
systemctl reload service       # Recharger config
systemctl enable service       # Activer au démarrage
systemctl disable service      # Désactiver au démarrage
systemctl status service       # État détaillé
```

### **Services réseau dangereux**

#### **Services souvent vulnérables**
```bash
# SSH (port 22) - Attaques par force brute
systemctl status sshd

# FTP (port 21) - Souvent mal configuré
systemctl status vsftpd

# Telnet (port 23) - Non chiffré
systemctl status telnet

# SNMP (port 161) - Community strings faibles
systemctl status snmpd

# Services web (80/443) - Vulnérabilités applicatives
systemctl status apache2
systemctl status nginx
```

## 📋 Logs et Surveillance

### **Fichiers de logs critiques**

#### **Localisation standard : /var/log/**
```bash
# LOGS D'AUTHENTIFICATION
/var/log/auth.log         # Connexions SSH, sudo, su
/var/log/secure           # RedHat/CentOS équivalent

# LOGS SYSTÈME
/var/log/syslog           # Messages système généraux
/var/log/kern.log         # Messages du kernel
/var/log/dmesg            # Messages de démarrage

# LOGS SERVICES
/var/log/apache2/         # Logs serveur web Apache
/var/log/nginx/           # Logs serveur web Nginx
/var/log/mysql/           # Logs base de données
```

#### **Commandes de surveillance**
```bash
# Surveillance en temps réel
tail -f /var/log/auth.log
tail -f /var/log/syslog

# Recherche dans les logs
grep "Failed password" /var/log/auth.log
grep "BREAK-IN" /var/log/auth.log

# Logs avec journalctl (systemd)
journalctl -f                    # Suivi temps réel
journalctl -u sshd               # Logs d'un service
journalctl --since "1 hour ago"  # Logs récents
```

## 🚨 Vulnérabilités Linux Communes

### **1. Escalade de privilèges**

#### **SUID/SGID malveillants**
```bash
# Recherche de binaires SUID/SGID suspects
find / -perm -4000 -type f 2>/dev/null | xargs ls -la
find / -perm -2000 -type f 2>/dev/null | xargs ls -la

# Binaires souvent exploitables
/bin/cp                # Si SUID, peut copier /etc/shadow
/usr/bin/find         # Exécution de commandes
/usr/bin/vim          # Édition de fichiers sensibles
/bin/bash             # Shell root direct !
```

#### **Sudo mal configuré**
```bash
# Vérifier les droits sudo actuels
sudo -l

# Configurations dangereuses
(ALL) NOPASSWD: ALL           # Sudo sans mot de passe
(ALL) /bin/bash               # Accès shell direct
(ALL) /usr/bin/vim            # Édition fichiers = escalade
```

### **2. Services mal configurés**

#### **SSH exposé et faible**
```bash
# Configuration SSH dangereuse (/etc/ssh/sshd_config)
PermitRootLogin yes           # Root peut se connecter
PasswordAuthentication yes    # Mots de passe autorisés  
PermitEmptyPasswords yes      # Mots de passe vides
Port 22                       # Port standard = cible
```

#### **Services inutiles actifs**
```bash
# Services souvent inutiles et dangereux
systemctl status telnet      # Non chiffré
systemctl status rsh         # Non chiffré  
systemctl status rlogin      # Non chiffré
systemctl status finger      # Information disclosure
```

### **3. Permissions de fichiers**

#### **Fichiers sensibles lisibles**
```bash
# Fichiers qui ne devraient PAS être lisibles
ls -la /etc/shadow           # Hashes des mots de passe
ls -la /etc/passwd           # Informations utilisateurs
ls -la /root/                # Dossier root
ls -la /home/*/.ssh/         # Clés SSH privées
```

#### **Dossiers avec permissions trop larges**
```bash
# Recherche de fichiers world-writable
find / -perm -2 -type f 2>/dev/null
find / -perm -002 -type d 2>/dev/null

# Fichiers de configuration modifiables
ls -la /etc/passwd           # Ne doit pas être writable
ls -la /etc/sudoers          # Très sensible
```

## 🛡️ Hardening Linux

### **Sécurisation des comptes**
```bash
# Désactiver le compte root
passwd -l root

# Forcer l'expiration des mots de passe  
chage -M 90 alice            # 90 jours max

# Verrouiller après échecs
pam_tally2 --user alice      # Voir les échecs
```

### **Sécurisation des services**
```bash
# Désactiver services inutiles
systemctl disable telnet
systemctl disable rsh-server
systemctl disable finger

# Configurer fail2ban (anti-brute force)
systemctl enable fail2ban
systemctl start fail2ban
```

### **Audit et monitoring**
```bash
# Installer auditd pour traçabilité
apt install auditd
systemctl enable auditd

# Surveiller les fichiers sensibles
auditctl -w /etc/passwd -p wa -k passwd_changes
auditctl -w /etc/shadow -p wa -k shadow_changes
```

## 📋 Résumé Pratique

### ✅ Points clés pour la sécurité :

1. **UID 0 = root = danger** - Limiter l'accès au maximum
2. **SUID root = escalade possible** - Auditer régulièrement  
3. **Services = surface d'attaque** - Désactiver l'inutile
4. **Logs = détection** - Surveiller auth.log et syslog
5. **Permissions = première défense** - Principe du moindre privilège

### 🎯 Pour les pentesters :

**Énumération :**
1. **Users/groups** - cat /etc/passwd, /etc/group
2. **SUID/SGID** - find / -perm -4000 ou -2000
3. **Services** - systemctl list-units, netstat -tulpn
4. **Cron jobs** - crontab -l, /etc/crontab

**Escalade de privilèges :**
1. **sudo -l** - Vérifier droits sudo
2. **LinPEAS** - Script d'énumération automatisé
3. **Kernel exploits** - uname -a, searchsploit
4. **Services locaux** - ps aux, netstat interne

---
*Ce guide couvre les fondamentaux sécuritaires de Linux. Pour l'exploitation avancée, voir les sections Privilege-Escalation et Post-Exploitation.* 