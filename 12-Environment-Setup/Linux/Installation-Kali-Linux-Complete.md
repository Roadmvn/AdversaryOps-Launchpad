# Installation et Configuration Kali Linux

## 🐉 Vue d'ensemble

**Kali Linux** est la distribution de référence pour le pentesting et l'audit de sécurité. Développée par Offensive Security, elle intègre plus de 600 outils de sécurité pré-installés et optimisés.

> **💡 Analogie Simple** : Kali Linux c'est comme la boîte à outils ultime du serrurier - tous les instruments nécessaires pour tester et analyser les serrures (systèmes) sont déjà inclus et affûtés.

## ⚠️ **IMPORTANT - Usage Légal et Éthique**

> **🎯 Kali Linux est un OUTIL PROFESSIONNEL de sécurité !**

### 📝 Usage Autorisé vs Interdit :

**✅ UTILISATION LÉGALE :**
```
• Tests sur VOS propres systèmes
• Environnements de lab autorisés
• Missions de pentest avec contrat
• Formation et certification professionnelle
• Recherche académique en cybersécurité
• Bug bounty programs autorisés
```

**❌ STRICTEMENT INTERDIT :**
```
• Piratage de systèmes non autorisés
• Tests sur infrastructures tierces
• Activités cybercriminelles
• Violation de la vie privée
• Contournement de protections sans permission
```

## 🎯 Objectifs d'apprentissage

Après cette section, vous saurez :
1. **Installation complète** - Différentes méthodes d'installation
2. **Configuration optimale** - Personnalisation pour le pentesting
3. **Outils essentiels** - Package selection et outils additionnels
4. **Sécurisation** - Hardening et bonnes pratiques
5. **Maintenance** - Mises à jour et troubleshooting

## 💿 Méthodes d'Installation

### **Installation Bare Metal (Recommandée)**

#### **Prérequis matériels**
```
CONFIGURATION MINIMALE :
• CPU : x86-64 compatible
• RAM : 2GB (4GB recommandé)
• Stockage : 20GB (50GB+ recommandé)
• GPU : Compatible avec pilotes libres
• Réseau : Ethernet ou WiFi compatible

CONFIGURATION OPTIMALE :
• CPU : Intel i5/i7 ou AMD Ryzen 5/7
• RAM : 16GB+ (pour VMs multiples)
• Stockage : SSD 256GB+ (performances)
• GPU : Support hardware acceleration
• USB : Multiple ports USB 3.0+
```

#### **Téléchargement officiel**
```bash
# Site officiel SEULEMENT
https://www.kali.org/get-kali/

# Vérification de l'intégrité (IMPORTANT)
wget https://kali.download/base-images/kali-2024.1/kali-linux-2024.1-installer-amd64.iso
wget https://kali.download/base-images/kali-2024.1/SHA256SUMS

# Vérifier le checksum
sha256sum kali-linux-2024.1-installer-amd64.iso
cat SHA256SUMS | grep installer-amd64.iso

# Les checksums DOIVENT correspondre !
```

#### **Création de média d'installation**
```bash
# Sur Linux
sudo dd if=kali-linux-2024.1-installer-amd64.iso of=/dev/sdX bs=4M status=progress

# Sur Windows (PowerShell admin)
# Utiliser Rufus ou Win32DiskImager

# Sur macOS
sudo dd if=kali-linux-2024.1-installer-amd64.iso of=/dev/diskX bs=4m

# ATTENTION : Remplacer X par le bon périphérique !
```

### **Installation en Machine Virtuelle**

#### **VMware Workstation/Fusion (Recommandé)**
```
CONFIGURATION VM :
• Nom : Kali-Linux-2024
• OS : Linux → Debian 12.x 64-bit
• RAM : 4GB minimum (8GB recommandé)
• CPU : 2 cores minimum (4 recommandé)
• Stockage : 60GB dynamique
• Réseau : NAT (isolation) ou Bridged (accès réseau)

PARAMÈTRES AVANCÉS :
• VT-x/AMD-V : Activé
• 3D Acceleration : Activé
• USB Controller : USB 3.0
• Sound : Désactivé (optionnel)
```

#### **VirtualBox (Gratuit)**
```
CONFIGURATION VM :
• Type : Linux
• Version : Debian (64-bit)
• RAM : 4096MB minimum
• Stockage : VDI dynamique 60GB
• Réseau : NAT ou Host-Only

OPTIMISATIONS :
• Extension Pack installé
• Guest Additions après installation
• Nested Paging activé
• Hardware Virtualization activé
```

### **Installation WSL2 (Windows)**
```powershell
# Activer WSL2 (PowerShell Admin)
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

# Redémarrer Windows

# Installer Kali depuis Microsoft Store
# Ou via ligne de commande :
wsl --install -d kali-linux

# Première configuration
wsl -d kali-linux
sudo apt update && sudo apt upgrade -y
```

## 🔧 Configuration Post-Installation

### **Première connexion et mise à jour**
```bash
# Connexion en tant que kali
# Mot de passe par défaut : kali (À CHANGER IMMÉDIATEMENT)

# Changer le mot de passe root
sudo passwd

# Changer le mot de passe utilisateur
passwd

# Mise à jour complète du système
sudo apt update && sudo apt upgrade -y
sudo apt dist-upgrade -y
sudo apt autoremove -y
```

### **Configuration du réseau**
```bash
# Vérifier la configuration réseau
ip addr show
ip route show

# Configuration interface (si nécessaire)
sudo dhclient eth0

# Configuration WiFi
sudo iwconfig
sudo wpa_passphrase "SSID" "password" | sudo tee -a /etc/wpa_supplicant/wpa_supplicant.conf
sudo wpa_supplicant -B -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf
sudo dhclient wlan0

# Vérifier connectivité
ping -c 4 8.8.8.8
curl -I https://www.kali.org/
```

### **Configuration des dépôts Kali**
```bash
# Vérifier les sources actuelles
cat /etc/apt/sources.list

# Configuration standard (doit contenir) :
echo "deb https://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware" | sudo tee /etc/apt/sources.list

# Ajouter dépôts additionnels si nécessaire
echo "deb-src https://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware" | sudo tee -a /etc/apt/sources.list

# Mettre à jour la liste des paquets
sudo apt update
```

## 🛠️ Installation des Outils

### **Meta-packages Kali essentiels**
```bash
# Outils Top 10 (essentiels)
sudo apt install -y kali-tools-top10

# Tous les outils Kali (lourd - 15GB+)
sudo apt install -y kali-linux-everything

# Outils par catégorie (recommandé)
sudo apt install -y kali-tools-web          # Outils web
sudo apt install -y kali-tools-wireless     # Outils WiFi
sudo apt install -y kali-tools-forensics    # Forensics
sudo apt install -y kali-tools-social-engineering  # Social engineering
sudo apt install -y kali-tools-passwords    # Password attacks
sudo apt install -y kali-tools-post-exploitation   # Post-exploitation
```

### **Outils additionnels recommandés**
```bash
# Outils de développement
sudo apt install -y build-essential git vim curl wget python3-pip

# Outils système
sudo apt install -y htop neofetch tree tmux screen

# Outils réseau additionnels
sudo apt install -y masscan zmap fierce sublist3r amass

# Frameworks et languages
sudo apt install -y golang-go nodejs npm ruby-dev

# Outils de virtualization
sudo apt install -y docker.io docker-compose

# Outils multimedia (optionnel)
sudo apt install -y vlc gimp obs-studio
```

### **Outils depuis GitHub**
```bash
# Créer dossier outils personnalisés
mkdir -p ~/tools && cd ~/tools

# LinPEAS/WinPEAS
git clone https://github.com/carlospolop/PEASS-ng.git

# PayloadsAllTheThings
git clone https://github.com/swisskyrepo/PayloadsAllTheThings.git

# SecLists wordlists
git clone https://github.com/danielmiessler/SecLists.git

# Impacket (outils Windows/AD)
git clone https://github.com/SecureAuthCorp/impacket.git
cd impacket && pip3 install . && cd ..

# Bloodhound
git clone https://github.com/BloodHoundAD/BloodHound.git

# Empire (post-exploitation)
git clone https://github.com/EmpireProject/Empire.git
```

## 🎨 Personnalisation et Optimisation

### **Configuration du shell (Zsh)**
```bash
# Installer Zsh et Oh-My-Zsh
sudo apt install -y zsh fonts-powerline
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Changer shell par défaut
chsh -s /usr/bin/zsh

# Thèmes et plugins recommandés (.zshrc)
ZSH_THEME="agnoster"  # ou "robbyrussell"
plugins=(git sudo zsh-autosuggestions zsh-syntax-highlighting)

# Alias utiles
echo "alias ll='ls -alF'" >> ~/.zshrc
echo "alias la='ls -A'" >> ~/.zshrc
echo "alias l='ls -CF'" >> ~/.zshrc
echo "alias grep='grep --color=auto'" >> ~/.zshrc
echo "alias nmap='nmap --reason'" >> ~/.zshrc
echo "alias ports='netstat -tulanp'" >> ~/.zshrc
```

### **Configuration Vim/Neovim**
```bash
# Installation Neovim (éditeur moderne)
sudo apt install -y neovim

# Configuration de base (~/.vimrc ou ~/.config/nvim/init.vim)
cat > ~/.vimrc << 'EOF'
syntax on
set number
set tabstop=4
set shiftwidth=4
set expandtab
set autoindent
set hlsearch
set incsearch
set mouse=a
colorscheme desert
EOF

# Plugins Vim utiles
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

### **Thème et interface graphique**
```bash
# Installer thèmes sombres
sudo apt install -y arc-theme papirus-icon-theme

# Configuration GNOME (si utilisé)
gsettings set org.gnome.desktop.interface gtk-theme 'Arc-Dark'
gsettings set org.gnome.desktop.interface icon-theme 'Papirus-Dark'

# Installer Terminator (terminal avancé)
sudo apt install -y terminator

# Configuration Terminator
mkdir -p ~/.config/terminator
cat > ~/.config/terminator/config << 'EOF'
[global_config]
[keybindings]
[profiles]
  [[default]]
    background_darkness = 0.9
    background_type = transparent
    font = Monospace 11
    foreground_color = "#ffffff"
    show_titlebar = False
EOF
```

## 🔒 Sécurisation de Kali Linux

### **Hardening de base**
```bash
# Désactiver les services inutiles
sudo systemctl disable bluetooth
sudo systemctl disable cups
sudo systemctl disable avahi-daemon

# Configuration firewall (UFW)
sudo apt install -y ufw
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Autorisations spécifiques si nécessaire
sudo ufw allow ssh
sudo ufw allow 4444  # Pour reverse shells en lab

# Vérifier le statut
sudo ufw status verbose
```

### **SSH sécurisé**
```bash
# Configuration SSH (/etc/ssh/sshd_config)
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup

# Modifications recommandées
sudo sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin no/' /etc/ssh/sshd_config
sudo sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo sed -i 's/#Port 22/Port 2222/' /etc/ssh/sshd_config

# Générer clés SSH
ssh-keygen -t rsa -b 4096 -C "kali@machine"
ssh-keygen -t ed25519 -C "kali@machine"

# Redémarrer SSH
sudo systemctl restart ssh
```

### **Chiffrement et sauvegardes**
```bash
# Chiffrer le home directory (si pas fait à l'installation)
sudo apt install -y ecryptfs-utils
sudo modprobe ecryptfs

# Outils de sauvegarde
sudo apt install -y rsync borgbackup

# Script de backup
cat > ~/backup.sh << 'EOF'
#!/bin/bash
# Backup des configurations importantes
rsync -avz --progress ~/.zshrc ~/.vimrc ~/.ssh/ ~/backup/configs/
rsync -avz --progress ~/tools/ ~/backup/tools/
rsync -avz --progress ~/Documents/ ~/backup/documents/
EOF
chmod +x ~/backup.sh
```

## 📦 Configurations Spécialisées

### **Pentesting Web**
```bash
# Outils web spécialisés
sudo apt install -y burpsuite zaproxy dirb gobuster nikto sqlmap

# Configuration Burp Suite
# Certificat : http://127.0.0.1:8080 → CA Certificate
# Proxy : 127.0.0.1:8080 dans navigateur

# Firefox avec extensions utiles
sudo apt install -y firefox-esr
# Extensions recommandées :
# - FoxyProxy Standard
# - Wappalyzer
# - Cookie Editor
# - User-Agent Switcher
```

### **Pentesting Réseau/Infrastructure**
```bash
# Outils réseau avancés
sudo apt install -y masscan zmap naabu httpx subfinder

# Responder pour LLMNR/NBT-NS
sudo apt install -y responder

# Outils Active Directory
sudo apt install -y crackmapexec evil-winrm bloodhound

# Configuration Bloodhound
sudo apt install -y neo4j
sudo systemctl enable neo4j
sudo systemctl start neo4j
# Interface : http://localhost:7474
# Login initial : neo4j/neo4j
```

### **Pentesting Wireless**
```bash
# Vérifier compatibilité carte WiFi
lsusb
dmesg | grep -i wireless

# Outils WiFi
sudo apt install -y aircrack-ng reaver pixiewps

# Mode monitor (adapter selon carte)
sudo airmon-ng check kill
sudo airmon-ng start wlan0

# Revenir en mode normal
sudo airmon-ng stop wlan0mon
sudo systemctl restart NetworkManager
```

## 🔄 Maintenance et Mises à Jour

### **Routine de maintenance**
```bash
# Script de maintenance quotidienne
cat > ~/maintenance.sh << 'EOF'
#!/bin/bash
echo "=== Maintenance Kali Linux ==="

# Mise à jour des paquets
echo "Mise à jour des paquets système..."
sudo apt update && sudo apt upgrade -y

# Nettoyage
echo "Nettoyage du système..."
sudo apt autoremove -y
sudo apt autoclean

# Mise à jour des outils GitHub
echo "Mise à jour des outils personnalisés..."
cd ~/tools
for dir in */; do
    if [ -d "$dir/.git" ]; then
        echo "Updating $dir"
        cd "$dir" && git pull && cd ..
    fi
done

# Vérification de l'espace disque
echo "Espace disque utilisé :"
df -h /

echo "=== Maintenance terminée ==="
EOF
chmod +x ~/maintenance.sh
```

### **Sauvegarde de configuration**
```bash
# Script de sauvegarde configuration
cat > ~/save-config.sh << 'EOF'
#!/bin/bash
BACKUP_DIR=~/kali-backup-$(date +%Y%m%d)
mkdir -p $BACKUP_DIR

# Fichiers de configuration importants
cp ~/.zshrc $BACKUP_DIR/
cp ~/.vimrc $BACKUP_DIR/
cp -r ~/.ssh $BACKUP_DIR/
cp -r ~/.config/terminator $BACKUP_DIR/

# Liste des paquets installés
dpkg --get-selections > $BACKUP_DIR/packages.list

# Outils personnalisés
rsync -av ~/tools/ $BACKUP_DIR/tools/

echo "Sauvegarde créée dans $BACKUP_DIR"
EOF
chmod +x ~/save-config.sh
```

## 📋 Checklist Post-Installation

### **Configuration système**
```bash
☐ Mots de passe sécurisés configurés
☐ Réseau fonctionnel (ethernet/wifi)
☐ Dépôts Kali configurés correctement
☐ Système mis à jour complètement
☐ Firewall UFW activé et configuré
☐ SSH sécurisé (si nécessaire)
☐ Utilisateur ajouté au groupe sudo
☐ Timezone configurée correctement
```

### **Outils et logiciels**
```bash
☐ Meta-packages Kali installés
☐ Outils additionnels depuis GitHub clonés
☐ Burp Suite configuré et testé
☐ Browser avec extensions pentesting
☐ Terminal et shell personnalisés
☐ Éditeur de texte configuré
☐ Docker installé et fonctionnel
☐ Outils de développement installés
```

### **Sécurité et maintenance**
```bash
☐ Services inutiles désactivés
☐ Système de sauvegarde configuré
☐ Script de maintenance créé
☐ Clés SSH générées
☐ Configuration hardening appliquée
☐ Monitoring de base configuré
☐ Documentation système créée
☐ Plan de récupération défini
```

## 🎯 Ressources et Références

### **Documentation officielle**
- **Kali Linux** : https://www.kali.org/
- **Kali Documentation** : https://www.kali.org/docs/
- **Offensive Security** : https://www.offensive-security.com/
- **Kali Tools** : https://www.kali.org/tools/

### **Communauté et support**
- **Kali Forums** : https://forums.kali.org/
- **Discord Kali** : https://discord.gg/kali-linux
- **Reddit** : r/Kalilinux
- **GitHub** : https://github.com/offensive-security

### **Formation et certification**
- **Kali Linux Revealed** : https://kali.training/
- **OSCP** : https://www.offensive-security.com/pwk-oscp/
- **OSEP** : https://www.offensive-security.com/pen300-osep/
- **OSWA** : https://www.offensive-security.com/awae-oswa/

---
*Kali Linux est votre compagnon de route vers l'expertise en cybersécurité. Configurez-le bien, il vous servira longtemps !* 