# Masscan - Scanning Haute Vitesse

## 📋 Vue d'ensemble
Masscan est un scanner de ports ultra-rapide capable de scanner Internet entier en moins de 6 minutes. Il est idéal pour les scans de reconnaissance rapide sur de larges plages d'adresses.

## 🎯 Objectifs
- Scanner rapidement de grandes plages IP
- Identifier les services ouverts à grande échelle
- Effectuer des scans de reconnaissance préliminaire
- Découvrir des assets sur de vastes réseaux

## 🚀 Caractéristiques principales

### Vitesse exceptionnelle
- **Transmission asynchrone** : Jusqu'à 25 millions de paquets/seconde
- **Stack TCP/IP intégré** : Contourne la stack système
- **Parallélisme massif** : Utilisation optimale des ressources

### Limitations
- Pas de détection de services/versions
- Scanning basique TCP/UDP uniquement  
- Nécessite des privilèges root
- Plus bruyant que Nmap

## 🔧 Installation et configuration

### Installation
```bash
# Ubuntu/Debian
sudo apt install masscan

# Depuis les sources
git clone https://github.com/robertdavidgraham/masscan
cd masscan
make
sudo make install
```

### Configuration réseau
```bash
# Vérifier les interfaces
ip addr show

# Configuration firewall (important)
# Masscan génère des réponses TCP RST
sudo iptables -I INPUT -p tcp --dport 61000 -j DROP
```

## 🎯 Utilisation de base

### Scans simples
```bash
# Scan d'un port sur une plage
sudo masscan 192.168.1.0/24 -p 80

# Plusieurs ports
sudo masscan 192.168.1.0/24 -p 80,443,22

# Plage de ports
sudo masscan 192.168.1.0/24 -p 1-1000

# Tous les ports
sudo masscan 192.168.1.0/24 -p 1-65535
```

### Contrôle de vitesse
```bash
# Limiter le taux (paquets/seconde)
sudo masscan 192.168.1.0/24 -p 80 --rate 1000

# Vitesse maximale
sudo masscan 192.168.1.0/24 -p 80 --rate 100000

# Vitesse conservative
sudo masscan 192.168.1.0/24 -p 80 --rate 100
```

## 🔍 Techniques avancées

### Scans UDP
```bash
# UDP scan
sudo masscan 192.168.1.0/24 -pU:53,161,123

# Combinaison TCP/UDP
sudo masscan 192.168.1.0/24 -p 80,443 -pU:53,161
```

### Configuration avancée
```bash
# Spécifier l'interface source
sudo masscan 192.168.1.0/24 -p 80 -e eth0

# Adresse source spécifique
sudo masscan 192.168.1.0/24 -p 80 --source-ip 192.168.1.100

# Port source spécifique
sudo masscan 192.168.1.0/24 -p 80 --source-port 61000
```

### Randomisation
```bash
# Ordre aléatoire des cibles
sudo masscan 192.168.1.0/24 -p 80 --randomize-hosts

# Seed pour reproductibilité
sudo masscan 192.168.1.0/24 -p 80 --seed 12345
```

## 📊 Formats de sortie

### Formats supportés
```bash
# Format par défaut (liste)
sudo masscan 192.168.1.0/24 -p 80

# Format XML
sudo masscan 192.168.1.0/24 -p 80 -oX results.xml

# Format JSON
sudo masscan 192.168.1.0/24 -p 80 -oJ results.json

# Format binaire (plus rapide)
sudo masscan 192.168.1.0/24 -p 80 -oB results.bin
```

### Sortie personnalisée
```bash
# Format greppable
sudo masscan 192.168.1.0/24 -p 80 -oG results.grep

# Sortie simple pour scripting
sudo masscan 192.168.1.0/24 -p 80 | awk '{print $6,$4}' | sort
```

## ⚡ Optimisation des performances

### Configuration système
```bash
# Augmenter les limites de fichiers
ulimit -n 65535

# Configuration réseau optimale
echo 'net.core.rmem_default = 262144' >> /etc/sysctl.conf
echo 'net.core.rmem_max = 16777216' >> /etc/sysctl.conf
sudo sysctl -p
```

### Stratégies de scanning
```bash
# Scan par chunks
sudo masscan 10.0.0.0/16 -p 80 --rate 10000 --exclude-file excludes.txt

# Scan de validation avec nmap
sudo masscan 10.0.0.0/16 -p 80 --rate 10000 | \
awk '{print $6}' | sudo nmap -sS -iL - -p 80
```

## 🎯 Cas d'usage pratiques

### Reconnaissance initiale
```bash
# Découverte rapide de web servers
sudo masscan 0.0.0.0/0 -p 80,443 --rate 1000 --exclude-file rfc1918.txt

# Services d'administration
sudo masscan 192.168.0.0/16 -p 22,23,3389,5985 --rate 5000
```

### Scan de validation
```bash
# Confirmer les résultats avec nmap
sudo masscan 192.168.1.0/24 -p 1-65535 --rate 1000 -oG - | \
grep "open" | cut -d' ' -f4,5 | \
while read ip port; do
    nmap -sS -p ${port##*/} $ip
done
```

## 📋 Configuration via fichier

### Fichier de configuration
```bash
# Créer masscan.conf
cat > masscan.conf << EOF
rate = 1000
output-format = xml
output-filename = scan_results.xml
ports = 80,443,22,23,25,53,110,995,143,993
exclude-file = excludes.txt
EOF

# Utiliser la configuration
sudo masscan -c masscan.conf 192.168.1.0/24
```

### Fichier d'exclusion
```bash
# excludes.txt
192.168.1.1
192.168.1.254
10.0.0.0/8
```

## 🔐 Évasion et furtivité

### Techniques d'évasion
```bash
# Taux réduit pour l'évasion
sudo masscan 192.168.1.0/24 -p 80 --rate 10

# Randomisation temporelle
sudo masscan 192.168.1.0/24 -p 80 --wait 1

# Fragmentation
sudo masscan 192.168.1.0/24 -p 80 --mtu 1200
```

### Considérations réseau
- Éviter de saturer les liens
- Surveiller les logs de détection
- Utiliser des proxies si nécessaire
- Fragmenter les scans dans le temps

## 🚨 Précautions importantes

### Impact réseau
```bash
# Monitor l'utilisation réseau
watch -n 1 'cat /proc/net/dev'

# Limiter l'impact
sudo masscan 192.168.1.0/24 -p 80 --rate 100 --wait 5
```

### Détection
- Masscan est très bruyant
- Logs facilement détectables dans les firewalls
- Générer beaucoup de trafic RST
- Utiliser avec parcimonie en production

## 📈 Intégration avec d'autres outils

### Pipeline avec Nmap
```bash
#!/bin/bash
# Scan rapide puis détaillé

# Phase 1: Découverte rapide avec Masscan
sudo masscan $1 -p 1-65535 --rate 1000 -oG masscan_results.txt

# Phase 2: Scan détaillé avec Nmap
grep "open" masscan_results.txt | \
awk '{print $2}' | sort -u | \
sudo nmap -sS -sV -A -iL - -oA detailed_scan
```

### Avec d'autres outils
```bash
# Export vers Metasploit
sudo masscan 192.168.1.0/24 -p 445 -oX smb_targets.xml

# Vers Nuclei pour vulnérabilité scanning
sudo masscan 192.168.1.0/24 -p 80,443 | \
awk '{print $6":"$4}' | nuclei -t cves/
```

## 📚 Références
- Masscan GitHub Repository
- Robert Graham's Blog
- Black Hat 2013 Presentation
- Internet-Wide Scanning Best Practices 