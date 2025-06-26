# Nmap - Bases du Port Scanning

## 📋 Vue d'ensemble
Nmap (Network Mapper) est l'outil de référence pour la découverte réseau et l'audit de sécurité. Il permet d'identifier les ports ouverts, les services et les systèmes d'exploitation.

## 🎯 Objectifs
- Identifier les ports ouverts sur les cibles
- Déterminer les services qui s'exécutent
- Détecter les versions des logiciels
- Identifier les systèmes d'exploitation
- Découvrir les vulnérabilités potentielles

## 🔍 Types de scans essentiels

### TCP SYN Scan (-sS)
```bash
# Scan SYN par défaut (stealth)
sudo nmap -sS 192.168.1.10

# Ports spécifiques
sudo nmap -sS -p 22,80,443 192.168.1.10

# Plage de ports
sudo nmap -sS -p 1-1000 192.168.1.10
```

### TCP Connect Scan (-sT)
```bash
# Scan complet TCP (sans privilèges root)
nmap -sT 192.168.1.10

# Plus bruyant mais fonctionne toujours
nmap -sT -p 1-65535 192.168.1.10
```

### UDP Scan (-sU)
```bash
# Scan UDP (plus lent)
sudo nmap -sU 192.168.1.10

# Ports UDP communs
sudo nmap -sU -p 53,161,137,123 192.168.1.10
```

## 🎚️ Options de timing et performance

### Templates de timing
```bash
# Paranoid (très lent, furtif)
nmap -T0 192.168.1.10

# Sneaky (lent)
nmap -T1 192.168.1.10

# Polite (normal, respectueux)
nmap -T2 192.168.1.10

# Normal (par défaut)
nmap -T3 192.168.1.10

# Aggressive (rapide)
nmap -T4 192.168.1.10

# Insane (très rapide, bruyant)
nmap -T5 192.168.1.10
```

### Contrôle fin des performances
```bash
# Parallélisme
nmap --min-parallelism 100 --max-parallelism 256 192.168.1.0/24

# Timeouts
nmap --host-timeout 5m --scan-delay 1s 192.168.1.10

# Taux de paquets
nmap --min-rate 1000 --max-rate 5000 192.168.1.10
```

## 🔍 Détection de services et versions

### Service Detection (-sV)
```bash
# Détection de version basique
nmap -sV 192.168.1.10

# Détection intensive
nmap -sV --version-intensity 9 192.168.1.10

# Version light (plus rapide)
nmap -sV --version-light 192.168.1.10
```

### OS Detection (-O)
```bash
# Détection OS
sudo nmap -O 192.168.1.10

# OS detection avec force
sudo nmap -O --osscan-guess 192.168.1.10

# Combiné avec service detection
sudo nmap -O -sV 192.168.1.10
```

## 📊 Formats de sortie

### Options de sortie standard
```bash
# Sortie normale
nmap 192.168.1.10

# Sortie XML
nmap -oX scan_results.xml 192.168.1.10

# Sortie greppable
nmap -oG scan_results.gnmap 192.168.1.10

# Tous les formats
nmap -oA scan_results 192.168.1.10
```

### Formats spécialisés
```bash
# Sortie scriptable
nmap --script-args=newtargets -oG - 192.168.1.0/24 | grep "Up"

# Export pour d'autres outils
nmap -oX - 192.168.1.10 | xsltproc nmap.xsl -
```

## 🛡️ Techniques d'évasion

### Fragmentation
```bash
# Fragmentation IP
nmap -f 192.168.1.10

# Fragmentation personnalisée
nmap --mtu 16 192.168.1.10
```

### Decoy scanning
```bash
# Utiliser des leurres
nmap -D RND:10 192.168.1.10

# Leurres spécifiques
nmap -D 192.168.1.100,192.168.1.101,ME 192.168.1.10
```

### Source port spoofing
```bash
# Port source spécifique
nmap --source-port 53 192.168.1.10

# Port source aléatoire
nmap -g 53 192.168.1.10
```

## 🎯 Sélection de cibles

### Spécification des cibles
```bash
# Hôte unique
nmap 192.168.1.10

# Plage IP
nmap 192.168.1.1-50

# Réseau CIDR
nmap 192.168.1.0/24

# Liste d'hôtes
nmap -iL targets.txt

# Exclure des hôtes
nmap 192.168.1.0/24 --exclude 192.168.1.1,192.168.1.10
```

### Sélection de ports
```bash
# Ports par défaut (top 1000)
nmap 192.168.1.10

# Tous les ports TCP
nmap -p- 192.168.1.10

# Ports spécifiques
nmap -p 22,53,80,443 192.168.1.10

# Plages de ports
nmap -p 1-1000,8000-9000 192.168.1.10

# Ports top 100
nmap --top-ports 100 192.168.1.10
```

## 📈 Scans complets recommandés

### Scan initial rapide
```bash
# Découverte rapide des services principaux
nmap -T4 -F 192.168.1.0/24
```

### Scan complet détaillé
```bash
# Scan exhaustif (à utiliser avec parcimonie)
sudo nmap -sS -sU -T4 -A -v -PE -PP -PS80,443 -PA3389 -PU40125 -PY -g 53 --script "default or (discovery and safe)" 192.168.1.10
```

### Scan de production
```bash
# Équilibré entre vitesse et discrétion
nmap -sS -T3 -p- --version-light -O --script "default and not intrusive" 192.168.1.10
```

## ⚠️ Considérations importantes

### Détection et logs
- Nmap génère des logs dans les firewalls/IDS
- Les scans SYN sont moins détectables que les scans Connect
- Utiliser des timings appropriés pour éviter la détection

### Performance réseau
- Les scans UDP sont très lents
- Éviter les scans -T5 sur des réseaux lents
- Limiter le parallélisme sur des cibles fragiles

### Légalité
- Autorisation préalable obligatoire
- Respecter les périmètres de test
- Documenter toutes les activités

## 📋 Méthodologie recommandée

1. **Découverte d'hôtes**
   ```bash
   nmap -sn 192.168.1.0/24
   ```

2. **Scan rapide des ports principaux**
   ```bash
   nmap -T4 -F 192.168.1.0/24
   ```

3. **Scan complet des hôtes intéressants**
   ```bash
   nmap -sS -T3 -p- -sV 192.168.1.10
   ```

4. **Détection OS et scripts**
   ```bash
   sudo nmap -O -sC 192.168.1.10
   ```

5. **Documentation et analyse**
   - Analyser les services découverts
   - Identifier les vulnérabilités potentielles
   - Préparer la phase d'énumération

## 📚 Références
- Nmap Official Documentation
- Nmap Network Scanning Book
- NMAP Scripting Engine (NSE) Reference 