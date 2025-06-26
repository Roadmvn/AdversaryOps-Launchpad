# Masscan - Guide Terrain Reconnaissance

## 📋 Vue d'ensemble
Guide pratique d'utilisation de Masscan pour la reconnaissance rapide à grande échelle. Masscan est l'outil idéal pour les scans initiaux sur de larges plages d'adresses.

## ⚡ Commandes essentielles terrain

### Scans de découverte rapide
```bash
# Scan web services sur plage importante
sudo masscan 10.0.0.0/16 -p 80,443,8080,8443 --rate 1000

# Services critiques sur réseau local
sudo masscan 192.168.0.0/16 -p 22,23,21,25,53,80,110,143,443,993,995,3389 --rate 5000

# Tous les ports sur cible spécifique (rapide)
sudo masscan 192.168.1.10 -p 1-65535 --rate 10000
```

### Scans par catégories de services
```bash
# Administration à distance
sudo masscan $TARGET -p 22,23,3389,5900,5985,5986 --rate 1000

# Base de données
sudo masscan $TARGET -p 1433,1521,3306,5432,6379,27017 --rate 1000

# Services web étendus
sudo masscan $TARGET -p 80,443,8000,8080,8443,9000,9443,10000 --rate 1000
```

## 🎯 Workflows de reconnaissance

### Workflow standard
```bash
#!/bin/bash
TARGET=$1
RATE=1000

echo "[+] Phase 1: Découverte rapide ports critiques"
sudo masscan $TARGET -p 21,22,23,25,53,80,135,139,443,445,993,995,3389,5985 \
    --rate $RATE -oG critical_ports.txt

echo "[+] Phase 2: Services web étendus"
sudo masscan $TARGET -p 80,443,8000,8080,8443,9000,9080,9443,10000 \
    --rate $RATE -oG web_services.txt

echo "[+] Phase 3: Validation avec Nmap"
grep "open" critical_ports.txt | awk '{print $2":"$4}' | \
while IFS=: read ip port; do
    nmap -sS -sV -p $port $ip
done
```

### Workflow réseau d'entreprise
```bash
# Découverte infrastructure
sudo masscan 10.0.0.0/8 172.16.0.0/12 192.168.0.0/16 \
    -p 80,443,22,3389,445,135,139 --rate 2000 \
    --exclude-file exclude_ranges.txt -oJ enterprise_scan.json

# Focus sur les services d'administration
sudo masscan 10.0.0.0/8 \
    -p 22,23,80,443,3389,5985,5986,8080,9000 \
    --rate 1000 -oG admin_services.txt
```

## 📊 Analyse et filtrage rapide

### Extraction ciblée
```bash
# Extraire seulement les IPs avec ports ouverts
grep "open" masscan_results.txt | cut -d' ' -f4 | sort -u > live_hosts.txt

# Services web découverts
grep "80\|443\|8080\|8443" masscan_results.txt | \
awk '{print $4":"$6}' | sort -u > web_targets.txt

# Grouper par service
awk '/open/ {ports[$6] = ports[$6] " " $4} END {for (port in ports) print port ":" ports[port]}' \
masscan_results.txt
```

### Intégration avec autres outils
```bash
# Préparation pour Nmap
grep "22/tcp" masscan_results.txt | cut -d' ' -f4 | \
sudo nmap -sS -sV -p 22 -iL - -oA ssh_detailed

# Export pour Nuclei
grep "80\|443" masscan_results.txt | \
awk '{print $4":"$6}' | nuclei -t cves/

# Génération de listes pour Metasploit
grep "445/tcp" masscan_results.txt | cut -d' ' -f4 > smb_targets.txt
```

## 🔧 Configuration optimisée

### Performance selon contexte
```bash
# Réseau local (LAN) - Très rapide
sudo masscan 192.168.1.0/24 -p 1-65535 --rate 50000

# Internet / WAN - Modéré pour éviter detection
sudo masscan $TARGET -p 80,443 --rate 100 --randomize-hosts

# Réseau d'entreprise - Équilibré
sudo masscan $TARGET -p 1-1000 --rate 1000 --wait 1
```

### Configuration par fichier
```bash
# masscan_recon.conf
rate = 2000
output-format = list
ports = 21,22,23,25,53,80,110,111,135,139,143,443,993,995,1723,3306,3389,5432,5900,6379
randomize-hosts = true
seed = 1234567890

# Utilisation
sudo masscan -c masscan_recon.conf $TARGET
```

## 🎪 Techniques d'évasion terrain

### Évasion basique
```bash
# Taux très lent pour éviter détection
sudo masscan $TARGET -p 80,443 --rate 10 --randomize-hosts

# Fragmentation
sudo masscan $TARGET -p 80 --rate 100 --mtu 1200

# Port source spécifique (contournement firewall)
sudo masscan $TARGET -p 80,443 --source-port 53 --rate 500
```

### Répartition temporelle
```bash
#!/bin/bash
# Scan étalé dans le temps
TARGET=$1
PORTS="80,443,22,21,25,53,135,139,445"

for hour in {08..17}; do
    echo "[+] Scan à ${hour}h00"
    sudo masscan $TARGET -p $PORTS --rate 100 \
        -oG scan_${hour}h.txt
    sleep 3600  # Attendre 1 heure
done
```

## 📋 Checks et validations

### Vérification des résultats
```bash
# Compter les découvertes
echo "Hosts avec ports ouverts: $(grep 'open' masscan_results.txt | cut -d' ' -f4 | sort -u | wc -l)"
echo "Total ports ouverts: $(grep 'open' masscan_results.txt | wc -l)"

# Services les plus fréquents
grep 'open' masscan_results.txt | cut -d' ' -f6 | sort | uniq -c | sort -rn
```

### Validation croisée
```bash
# Confirmer quelques résultats avec Nmap
grep "open" masscan_results.txt | head -10 | \
while read timestamp state protocol port ip; do
    echo "Validation $ip:$port"
    nmap -sS -p ${port%/*} $ip
done
```

## ⚠️ Précautions terrain

### Limitations réseau
```bash
# Vérifier la bande passante avant scan
iftop -i eth0 &
IFTOP_PID=$!

# Surveiller l'impact
watch -n 5 'ss -tuln | grep :61000'  # Port par défaut Masscan

# Arrêter monitoring
kill $IFTOP_PID
```

### Détection et logging
```bash
# Vérifier les logs pour détection
# Sur la cible (si accessible)
tail -f /var/log/syslog | grep -i "scan\|flood\|attack"

# Monitoring local
tcpdump -i eth0 -c 100 "tcp[tcpflags] & tcp-rst != 0"
```

## 🔗 Intégration workflows

### Pipeline de reconnaissance complète
```bash
#!/bin/bash
# Reconnaissance automatisée avec Mascan + Nmap

TARGET=$1
WORKSPACE="recon_$(date +%Y%m%d_%H%M%S)"
mkdir -p $WORKSPACE

cd $WORKSPACE

# Phase 1: Découverte Masscan
echo "[+] Masscan discovery phase..."
sudo masscan $TARGET -p 1-65535 --rate 5000 \
    -oG masscan_all.txt \
    --excludefile ../exclude_private.txt

# Phase 2: Extraction et tri
echo "[+] Processing results..."
grep "open" masscan_all.txt | \
awk '{print $4":"$6}' | sort -u > discovered_services.txt

# Phase 3: Validation Nmap critique
echo "[+] Nmap validation of critical services..."
grep -E ":(22|80|443|3389|445|21|25|53|135|139)/" discovered_services.txt | \
cut -d: -f1 | sort -u | \
sudo nmap -sS -sV -A -iL - -oA critical_services

# Phase 4: Scan web détaillé
echo "[+] Detailed web services scan..."
grep -E ":(80|443|8080|8443|9000)/" discovered_services.txt | \
sudo nmap -sS -sV --script http-enum,http-headers -iL - \
-oA web_services_detailed

echo "[+] Reconnaissance complete in $WORKSPACE/"
```

## 📚 Références rapides

### Ports communs par service
```bash
# Web: 80,443,8000,8080,8443,9000,9443
# SSH: 22
# FTP: 21
# Telnet: 23
# SMTP: 25,587
# DNS: 53
# HTTP Alt: 8000,8080,9000,10000
# HTTPS Alt: 8443,9443,10443
# RDP: 3389
# SMB: 135,139,445
# SNMP: 161,162
# Databases: 1433,3306,5432,6379,27017
```

### Commandes de base mémorisées
```bash
# Quick web discovery
sudo masscan $TARGET -p 80,443,8080 --rate 1000

# Full TCP scan fast
sudo masscan $TARGET -p 1-65535 --rate 10000

# Enterprise common services
sudo masscan $TARGET -p 21,22,23,25,53,80,135,139,443,445,3389 --rate 2000

# Database services
sudo masscan $TARGET -p 1433,3306,5432,6379,27017 --rate 1000
```

## 🔗 Voir aussi
- [Guide Masscan complet](../../02-Exploitation/_Tools/Masscan_Rapide.md)
- [Analyse des résultats](../Active/Port-Scanning/Analyse_Résultats.md)
- [Intégration Nmap](./Nmap.md) 