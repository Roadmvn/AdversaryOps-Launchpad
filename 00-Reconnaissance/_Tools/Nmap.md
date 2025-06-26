# Nmap - Guide de Référence Terrain

## 📋 Vue d'ensemble
Ce guide est optimisé pour l'utilisation terrain de Nmap dans le contexte de la reconnaissance active. Pour le guide complet, consultez [08-Cours/04-Outils_Généraux/Nmap.md](../../08-Cours/04-Outils_Généraux/Nmap.md).

## ⚡ Commandes essentielles pour la reconnaissance

### Découverte d'hôtes rapide
```bash
# Ping sweep basique
nmap -sn 192.168.1.0/24

# Ping sweep avec différentes techniques
nmap -sn -PE -PS80,443 -PU53 192.168.1.0/24
```

### Scans de ports prioritaires
```bash
# Top 1000 ports (rapide)
nmap -T4 -F 192.168.1.0/24

# Ports critiques seulement
nmap -p 21,22,23,25,53,80,110,111,135,139,143,443,993,995,1723,3306,3389,5432,5900,6379 192.168.1.0/24

# Scan complet mais optimisé
nmap -sS -T4 -p- --min-rate 5000 192.168.1.10
```

### Détection de services essentiels
```bash
# Version detection rapide
nmap -sV --version-light 192.168.1.10

# OS detection
sudo nmap -O 192.168.1.10

# Scripts de base non-intrusifs
nmap -sC 192.168.1.10
```

## 🎯 Scans ciblés par phase

### Phase initiale (découverte)
```bash
# Scan initial très rapide
nmap -T4 -F -oA initial_scan 192.168.1.0/24
```

### Phase intermédiaire (détails)
```bash
# Scan détaillé des hôtes actifs
nmap -sS -sV -T4 -p- -oA detailed_scan $(cat initial_scan.gnmap | grep Up | cut -d' ' -f2)
```

### Phase finale (vulnérabilités)
```bash
# Scripts de vulnérabilités
nmap --script vuln -oA vuln_scan 192.168.1.10
```

## 📊 Analyse rapide des résultats

### Extraction d'informations clés
```bash
# Ports ouverts par service
grep "open" scan.gnmap | cut -d' ' -f4- | sort | uniq -c

# Liste des hôtes avec web services
grep "80/open\|443/open\|8080/open" scan.gnmap | cut -d' ' -f2

# Services SSH découverts
grep "22/open" scan.gnmap | cut -d' ' -f2 > ssh_targets.txt
```

## ⚠️ Considérations terrain

### Détection et discrétion
- Utiliser `-T2` ou `-T3` pour éviter la détection
- Fragmenter les scans dans le temps
- Utiliser des leurres si nécessaire : `nmap -D RND:10`

### Performance réseau
- Adapter `--min-rate` selon la bande passante
- Utiliser `--max-retries 1` sur réseaux lents
- Éviter les scans UDP en première approche

## 🔗 Voir aussi
- [Guide complet Nmap](../../08-Cours/04-Outils_Généraux/Nmap.md)
- [Analyse des résultats](../Active/Port-Scanning/Analyse_Résultats.md)
- [Techniques de scanning](../Active/Port-Scanning/) 