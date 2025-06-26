# Nmap - Guide Complet de Référence

## 📋 Vue d'ensemble
Nmap (Network Mapper) est l'outil de référence pour la découverte réseau, l'audit de sécurité et l'administration système. Ce guide exhaustif couvre tous les aspects de l'utilisation de Nmap.

## 🎯 Objectifs d'apprentissage
- Maîtriser tous les types de scans Nmap
- Comprendre le fonctionnement interne des techniques
- Optimiser les performances selon les contextes
- Interpréter correctement les résultats
- Intégrer Nmap dans des workflows d'audit

## 📚 Fondamentaux théoriques

### Principe de fonctionnement
Nmap utilise des paquets IP bruts pour déterminer :
- Les hôtes disponibles sur le réseau
- Les services (ports) qu'ils offrent
- Le système d'exploitation qu'ils exécutent
- Le type de pare-feu/filtres utilisés

### Architecture modulaire
```
Nmap Core Engine
├── Host Discovery Module
├── Port Scanning Module
├── Service/Version Detection
├── OS Detection Module
├── Nmap Scripting Engine (NSE)
└── Output and Timing Engine
```

## 🔍 Types de scans détaillés

### TCP Scans
```bash
# SYN Scan (-sS) - "Half-open" scan
sudo nmap -sS target
# Envoie SYN, attend SYN-ACK, puis RST (ne termine pas la connexion)

# Connect Scan (-sT) - Full TCP connection
nmap -sT target
# Utilise l'API connect() du système

# ACK Scan (-sA) - Détection de firewall
sudo nmap -sA target
# Envoie ACKs pour identifier les règles de filtrage

# Window Scan (-sW) - Basé sur la taille de la fenêtre TCP
sudo nmap -sW target

# Maimon Scan (-sM) - FIN/ACK probe
sudo nmap -sM target
```

### UDP Scans
```bash
# UDP Scan (-sU)
sudo nmap -sU target
# Plus lent, basé sur les réponses ICMP "port unreachable"

# Optimisation UDP
sudo nmap -sU --top-ports 100 target
sudo nmap -sU -T4 --max-retries 1 target
```

### Scans exotiques
```bash
# NULL Scan (-sN) - Aucun flag TCP
sudo nmap -sN target

# FIN Scan (-sF) - Flag FIN uniquement
sudo nmap -sF target

# Xmas Scan (-sX) - Flags FIN, PSH, URG
sudo nmap -sX target

# Scan personnalisé avec flags TCP
sudo nmap --scanflags SYNFIN target
```

## 🎚️ Host Discovery avancé

### Techniques de ping
```bash
# ICMP Ping
nmap -sn -PE target        # Echo Request
nmap -sn -PP target        # Timestamp Request
nmap -sn -PM target        # Address Mask Request

# TCP Ping
nmap -sn -PS80,443 target  # TCP SYN ping
nmap -sn -PA80,443 target  # TCP ACK ping

# UDP Ping
nmap -sn -PU53,161 target  # UDP ping

# Combinaisons
nmap -sn -PE -PS80,443 -PU53 target
```

### Configuration avancée discovery
```bash
# Désactiver complètement le ping
nmap -Pn target

# ARP ping (réseaux locaux)
sudo nmap -sn -PR 192.168.1.0/24

# Liste spécifique de techniques
nmap -sn -PE -PP -PS21,22,23,25,53,80,113,31339 -PA80,113,443,10042 target
```

## 🔬 Service et Version Detection

### Détection de services
```bash
# Détection basique
nmap -sV target

# Intensité de détection (0-9)
nmap -sV --version-intensity 0 target  # Light
nmap -sV --version-intensity 9 target  # Comprehensive

# Seulement les ports spécifiés
nmap -sV --version-light target

# All ports version detection
nmap -sV -p- target
```

### Techniques de détection
```bash
# Probes personnalisés
nmap -sV --version-trace target

# Base de données de signatures
locate nmap-service-probes

# Forcer détection sur ports fermés
nmap -sV --allports target
```

## 🖥️ OS Detection approfondi

### Options de base
```bash
# OS Detection standard
sudo nmap -O target

# Aggressive OS detection
sudo nmap -O --osscan-guess target

# Limiter tentatives
sudo nmap -O --max-os-tries 1 target
```

### Fingerprinting avancé
```bash
# Détails du fingerprinting
sudo nmap -O --osscan-limit target

# Sans restriction de hosts
sudo nmap -O --fuzzy target

# Combiné avec détection de services
sudo nmap -A target  # = -O -sV -sC --traceroute
```

## 🔧 Nmap Scripting Engine (NSE)

### Catégories de scripts
```bash
# Scripts par défaut (safe + intrusive)
nmap -sC target
nmap --script default target

# Par catégorie
nmap --script auth target
nmap --script broadcast target
nmap --script brute target
nmap --script discovery target
nmap --script dos target
nmap --script exploit target
nmap --script external target
nmap --script fuzzer target
nmap --script intrusive target
nmap --script malware target
nmap --script safe target
nmap --script version target
nmap --script vuln target
```

### Scripts spécifiques
```bash
# Script particulier
nmap --script http-enum target

# Plusieurs scripts
nmap --script "http-*" target

# Scripts avec arguments
nmap --script http-brute --script-args userdb=users.txt,passdb=passwords.txt target

# Information sur un script
nmap --script-help http-enum
```

### Scripts personnalisés
```lua
-- Exemple de script NSE simple
local shortport = require "shortport"
local stdnse = require "stdnse"

description = [[
Script d'exemple pour détecter un service personnalisé
]]

author = "Votre Nom"
license = "Same as Nmap--See https://nmap.org/book/man-legal.html"
categories = {"discovery", "safe"}

portrule = shortport.port_or_service(8080, "http-alt")

action = function(host, port)
    return "Service personnalisé détecté!"
end
```

## ⚡ Optimisation des performances

### Templates de timing
```bash
# T0: Paranoid (300 secondes entre probes)
nmap -T0 target

# T1: Sneaky (15 secondes entre probes)
nmap -T1 target

# T2: Polite (0.4 secondes entre probes)
nmap -T2 target

# T3: Normal (défaut)
nmap -T3 target

# T4: Aggressive (performances)
nmap -T4 target

# T5: Insane (très rapide, peut manquer résultats)
nmap -T5 target
```

### Contrôle fin du timing
```bash
# Contrôle des timeouts
nmap --min-rtt-timeout 100ms --max-rtt-timeout 200ms --initial-rtt-timeout 100ms target
nmap --host-timeout 15m target
nmap --scan-delay 1s target
nmap --max-scan-delay 10s target

# Parallélisme
nmap --min-parallelism 1 --max-parallelism 100 target
nmap --min-hostgroup 1 --max-hostgroup 64 target

# Taux de paquets
nmap --min-rate 100 --max-rate 1000 target

# Nombre de tentatives
nmap --max-retries 1 target
```

## 🛡️ Techniques d'évasion avancées

### Fragmentation
```bash
# Fragmentation IP basique
nmap -f target

# MTU personnalisé (multiple de 8)
nmap --mtu 24 target

# Sans fragmentation
nmap -Df target
```

### Source spoofing
```bash
# Adresse source spécifique
nmap -S 192.168.1.100 target

# Interface source
nmap -e eth0 target

# Port source fixe
nmap --source-port 53 target
nmap -g 80 target
```

### Decoy scanning
```bash
# Leurres aléatoires
nmap -D RND:10 target

# Leurres spécifiques (ME = votre vraie IP)
nmap -D decoy1,decoy2,ME,decoy3 target

# Générer liste de leurres
nmap -D $(for i in {1..10}; do echo -n "192.168.1.$((RANDOM%254+1)),"; done)ME target
```

### Proxy et rebond
```bash
# FTP bounce scan (rare aujourd'hui)
nmap -b username:password@ftpserver:port target

# Idle scan (zombi)
nmap -sI zombie_host target
```

## 📊 Formats de sortie et parsing

### Formats standard
```bash
# Sortie XML (recommandé pour parsing)
nmap -oX scan.xml target

# Sortie greppable
nmap -oG scan.gnmap target

# Sortie normale (lisible)
nmap -oN scan.nmap target

# Tous les formats
nmap -oA scan_results target

# Sortie vers stdout et fichier
nmap -oX - target | tee scan.xml
```

### Parsing XML avec outils
```bash
# xmllint pour extractions simples
xmllint --xpath "//host[status/@state='up']/address/@addr" scan.xml

# python pour parsing complexe
python3 -c "
import xml.etree.ElementTree as ET
tree = ET.parse('scan.xml')
for host in tree.findall('.//host[status[@state=\"up\"]]'):
    ip = host.find('address').get('addr')
    ports = [p.get('portid') for p in host.findall('.//port[@state=\"open\"]')]
    print(f'{ip}: {ports}')
"

# nmap-parse-output (outil tiers)
nmap-parse-output scan.xml
```

### Conversion et présentation
```bash
# Conversion HTML
xsltproc scan.xml -o scan.html

# Conversion CSV
python3 nmap2csv.py scan.xml > scan.csv

# Import dans bases de données
nmap --script database scan.xml
```

## 🎯 Méthodologies d'utilisation

### Reconnaissance méthodique
```bash
# Phase 1: Découverte rapide
nmap -sn 192.168.1.0/24 -oG hosts_up.txt

# Phase 2: Scan rapide des ports principaux
nmap -T4 -F $(grep Up hosts_up.txt | cut -d' ' -f2) -oA quick_scan

# Phase 3: Scan complet des services intéressants
nmap -sS -sV -p- $(grep "80\|443\|22" quick_scan.gnmap | cut -d' ' -f2) -oA full_scan

# Phase 4: Scripts et vulnérabilités
nmap -sC --script vuln -iL interesting_hosts.txt -oA vuln_scan
```

### Audit de sécurité complet
```bash
# Script d'audit automatisé
#!/bin/bash
TARGET=$1
OUTPUT_DIR="nmap_audit_$(date +%Y%m%d_%H%M%S)"
mkdir -p $OUTPUT_DIR

# Découverte d'hôtes
nmap -sn $TARGET -oA $OUTPUT_DIR/01_host_discovery

# Scan de ports rapide
nmap -T4 -F $TARGET -oA $OUTPUT_DIR/02_port_scan_fast

# Scan complet
nmap -sS -sV -O -p- $TARGET -oA $OUTPUT_DIR/03_full_scan

# Scripts de sécurité
nmap -sC --script vuln $TARGET -oA $OUTPUT_DIR/04_security_scripts

# Rapport final
echo "Audit terminé dans $OUTPUT_DIR"
```

## 🚨 Détection et contournement

### Signatures communes détectées par IDS
- Scan de ports séquentiels
- Probe vers ports communs (22,23,25,53,80,110,443)
- User-Agent Nmap dans requêtes HTTP
- Patterns de timing réguliers

### Techniques de contournement
```bash
# Randomisation de l'ordre des ports
nmap --randomize-hosts target

# Espacement temporel irrégulier
nmap --scan-delay $(shuf -i 1-10 -n 1)s target

# Source IP rotation (nécessite configuration réseau)
for ip in 192.168.1.{100..110}; do
    nmap -S $ip -p $((RANDOM%65535+1)) target &
done
```

## ⚠️ Limitations et précautions

### Limitations techniques
- Les scans UDP sont intrinsèquement lents
- La détection OS nécessite au moins un port ouvert et un fermé
- Les firewalls stateful peuvent fausser les résultats
- La charge réseau peut impacter les résultats

### Précautions légales et éthiques
- **Toujours obtenir une autorisation écrite**
- Respecter les périmètres définis
- Documenter toutes les activités
- Éviter les scans pouvant impacter la production

### Bonnes pratiques opérationnelles
```bash
# Toujours commencer par des scans légers
nmap -T2 --top-ports 100 target

# Progression graduelle
nmap -T3 -p 1-1000 target
nmap -T4 -p- target  # Seulement si autorisé

# Logs et documentation
nmap target 2>&1 | tee scan_log.txt
```

## 📚 Ressources avancées

### Documentation officielle
- Nmap Official Documentation
- Nmap Network Scanning Book (Gordon Lyon)
- NSE Script Documentation

### Outils complémentaires
```bash
# ndiff pour comparaison de scans
ndiff scan1.xml scan2.xml

# ncat pour tests manuels
ncat target 80

# nping pour tests de connectivité avancés
nping --tcp -p 80,443 target
```

### Bases de données et références
- `/usr/share/nmap/` : Fichiers de configuration
- `nmap-services` : Base des services par port
- `nmap-os-db` : Signatures OS
- `scripts/` : Scripts NSE disponibles

## 🎓 Exercices pratiques

### Débutant
1. Scan de découverte d'un réseau local
2. Identification des services web
3. Détection des versions de services SSH

### Intermédiaire
1. Contournement de firewall basique
2. Utilisation de scripts NSE pour énumération
3. Optimisation de scans sur réseaux lents

### Avancé
1. Développement de scripts NSE personnalisés
2. Integration dans pipelines d'automatisation
3. Techniques d'évasion contre IDS/IPS modernes

## 📖 Références
- RFC 793 (TCP Protocol)
- RFC 768 (UDP Protocol)
- RFC 792 (ICMP Protocol)
- NIST SP 800-115 (Network Security Testing)
- OWASP Testing Guide 