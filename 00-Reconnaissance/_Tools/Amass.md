# Amass - Reconnaissance de Subdomains

## 📋 Vue d'ensemble
Amass est un outil de reconnaissance OSINT spécialisé dans la découverte de subdomains et la cartographie d'infrastructures réseau. Il excelle dans la reconnaissance passive et active.

## 🎯 Objectifs terrain
- Découvrir tous les subdomains d'une organisation
- Cartographier l'infrastructure DNS
- Identifier les assets cachés et oubliés
- Corréler les informations OSINT multiples

## ⚡ Commandes essentielles

### Reconnaissance passive (recommandée en premier)
```bash
# Énumération passive basique
amass enum -passive -d example.com

# Passive avec toutes les sources
amass enum -passive -d example.com -o passive_results.txt

# Multiples domaines
amass enum -passive -df domains.txt -o multi_passive.txt

# Avec résolution DNS
amass enum -passive -d example.com -ip -o passive_with_ips.txt
```

### Reconnaissance active (plus intrusive)
```bash
# Énumération active
amass enum -active -d example.com -o active_results.txt

# Active avec brute force
amass enum -active -brute -d example.com -o brute_results.txt

# Avec wordlist personnalisée
amass enum -active -brute -w custom_wordlist.txt -d example.com

# Active + passive combinés
amass enum -passive -active -d example.com -o complete_enum.txt
```

## 🔍 Techniques spécialisées

### Énumération avec sources API
```bash
# Configuration des clés API (optionnel mais recommandé)
cat > ~/.config/amass/config.ini << EOF
[data_sources.AlienVault]
[data_sources.BinaryEdge]
apikey = YOUR_API_KEY

[data_sources.Censys]
api_id = YOUR_API_ID
secret = YOUR_SECRET

[data_sources.Shodan]
apikey = YOUR_API_KEY

[data_sources.SecurityTrails]
apikey = YOUR_API_KEY

[data_sources.VirusTotal]
apikey = YOUR_API_KEY
EOF

# Énumération avec APIs configurées
amass enum -passive -d example.com -config ~/.config/amass/config.ini
```

### Reconnaissance par organisation
```bash
# Par nom d'organisation
amass enum -org "Target Company" -o org_domains.txt

# Par ASN (Autonomous System Number)
amass enum -asn 64496 -o asn_results.txt

# Par plage CIDR
amass enum -cidr 203.0.113.0/24 -o cidr_results.txt
```

## 📊 Analyse et visualisation

### Intelligence et tracking
```bash
# Mode intelligence (historical data)
amass intel -d example.com -whois -o intel_results.txt

# Reverse whois par email/organisation
amass intel -org "Target Company" -o reverse_whois.txt

# Tracking de l'infrastructure
amass track -d example.com
```

### Visualisation des résultats
```bash
# Génération de graphiques
amass viz -d3 -d example.com

# Export pour Neo4j
amass viz -neo4j neo4j_import.json -d example.com

# Graphique Maltego
amass viz -maltego maltego_import.csv -d example.com
```

## 🎯 Workflows pratiques

### Reconnaissance complète méthodique
```bash
#!/bin/bash
TARGET_DOMAIN=$1
WORKSPACE="amass_recon_$(date +%Y%m%d_%H%M%S)"
mkdir -p $WORKSPACE && cd $WORKSPACE

echo "[+] Phase 1: Intelligence gathering"
amass intel -d $TARGET_DOMAIN -whois -o 01_intelligence.txt

echo "[+] Phase 2: Passive enumeration"
amass enum -passive -d $TARGET_DOMAIN -o 02_passive_enum.txt

echo "[+] Phase 3: Active enumeration"
amass enum -active -d $TARGET_DOMAIN -o 03_active_enum.txt

echo "[+] Phase 4: Brute force"
amass enum -brute -d $TARGET_DOMAIN -w /usr/share/wordlists/amass/subdomains.txt \
    -o 04_brute_force.txt

echo "[+] Phase 5: Consolidation"
cat *.txt | sort -u > final_subdomains.txt

echo "[+] Phase 6: Resolution and validation"
amass enum -passive -d $TARGET_DOMAIN -ip -o final_with_ips.txt

echo "[+] Results in $WORKSPACE/"
```

### Monitoring continu
```bash
#!/bin/bash
# Script de monitoring pour nouveaux subdomains
DOMAIN=$1
BASELINE="baseline_${DOMAIN}.txt"
CURRENT="current_${DOMAIN}.txt"

# Premier run - création baseline
if [ ! -f $BASELINE ]; then
    echo "[+] Creating baseline for $DOMAIN"
    amass enum -passive -d $DOMAIN -o $BASELINE
    exit 0
fi

# Runs suivants - détection changements
echo "[+] Scanning for new subdomains..."
amass enum -passive -d $DOMAIN -o $CURRENT

# Comparer avec baseline
NEW_DOMAINS=$(comm -13 <(sort $BASELINE) <(sort $CURRENT))

if [ ! -z "$NEW_DOMAINS" ]; then
    echo "[!] New subdomains discovered:"
    echo "$NEW_DOMAINS"
    
    # Mise à jour baseline
    cp $CURRENT $BASELINE
    
    # Validation des nouveaux domaines
    echo "$NEW_DOMAINS" | while read domain; do
        echo "[+] Validating $domain"
        dig +short $domain
        nmap -sS -p 80,443 $domain 2>/dev/null | grep open
    done
else
    echo "[=] No new subdomains found"
fi
```

## 🔧 Configuration avancée

### Optimisation performances
```bash
# Configuration personnalisée
cat > amass_config.ini << EOF
[scope]
# Ports à scanner
port = 80,443,8080,8443

# Sources désactivées (trop lentes)
[data_sources.Ask]
disabled = true

[data_sources.Baidu]
disabled = true

# Limites de taux
[settings]
minimum_ttl = 1440
EOF

# Utilisation
amass enum -config amass_config.ini -d example.com
```

### Intégration avec autres outils
```bash
# Pipeline avec subfinder et assetfinder
subfinder -d example.com -o subfinder_results.txt
assetfinder example.com > assetfinder_results.txt
amass enum -passive -d example.com -o amass_results.txt

# Consolidation et déduplication
cat *_results.txt | sort -u > all_subdomains.txt

# Validation avec httpx
cat all_subdomains.txt | httpx -silent -o live_subdomains.txt

# Screenshots avec gowitness
cat live_subdomains.txt | gowitness file -f - -P screenshots/
```

## 📈 Analyse des résultats

### Extraction d'informations
```bash
# Compter les découvertes
echo "Total subdomains: $(wc -l < results.txt)"

# Grouper par TLD
awk -F. '{print $NF}' results.txt | sort | uniq -c | sort -rn

# Extraire les IPs
grep -oE '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' results.txt | sort -u

# Identifier patterns intéressants
grep -E "(dev|test|staging|admin|internal|vpn)" results.txt
```

### Priorisation des cibles
```bash
# Subdomains avec services web actifs
cat subdomains.txt | httpx -silent -status-code -title -o web_services.txt

# Services critiques
cat subdomains.txt | while read domain; do
    nmap -sS -p 22,80,443,3389 $domain | grep open && echo "Critical: $domain"
done

# Certificats SSL expirants
cat subdomains.txt | while read domain; do
    echo "Checking SSL for $domain"
    openssl s_client -connect $domain:443 -servername $domain 2>/dev/null | \
    openssl x509 -noout -dates
done
```

## 🔐 OPSEC et discrétion

### Reconnaissance passive uniquement
```bash
# Uniquement sources publiques (pas de DNS actif)
amass enum -passive -d example.com -o passive_only.txt

# Éviter les sources bruyantes
amass enum -passive -d example.com -src censys,virustotal,shodan
```

### Limitation du taux
```bash
# Configuration discrète
cat > discrete_config.ini << EOF
[settings]
minimum_ttl = 3600
max_dns_queries = 10

[resolvers]
resolver = 8.8.8.8,1.1.1.1
EOF

amass enum -config discrete_config.ini -passive -d example.com
```

## ⚠️ Considérations opérationnelles

### Gestion des faux positifs
```bash
# Filtrer les CDN et services tiers
grep -v -E "(cloudfront|amazonaws|azurewebsites|herokuapp)" results.txt > filtered_results.txt

# Vérifier la propriété réelle
cat subdomains.txt | while read domain; do
    whois $domain | grep -i "registrant\|organization" | head -1
done
```

### Validation et vérification
```bash
# Test de résolution DNS
cat subdomains.txt | while read domain; do
    if dig +short $domain | grep -E '^[0-9]'; then
        echo "VALID: $domain"
    else
        echo "INVALID: $domain"
    fi
done

# Test de connectivité HTTP/HTTPS
cat subdomains.txt | httprobe -c 50 > live_web_services.txt
```

## 📚 Sources de données intégrées

### Sources passives principales
- **Certificate Transparency Logs**
- **Search Engines** (Google, Bing, Yahoo)
- **DNS Databases** (SecurityTrails, Shodan)
- **Social Media APIs**
- **Code Repositories** (GitHub, GitLab)

### Sources actives (plus détectables)
- **DNS Zone Transfers**
- **DNS Brute Force**
- **Web Crawling**
- **Certificate Requests**

## 🔗 Intégration workflows

### Export vers autres outils
```bash
# Vers Nmap
amass enum -passive -d example.com -ip | cut -d' ' -f1 | \
sudo nmap -sS -iL - -oA nmap_from_amass

# Vers Nuclei
amass enum -passive -d example.com | httprobe | \
nuclei -t cves/ -o nuclei_results.txt

# Vers Dirb/Gobuster
amass enum -passive -d example.com | httprobe | \
while read url; do
    dirb $url /usr/share/wordlists/dirb/common.txt
done
```

## 📖 Références et ressources

### Wordlists recommandées
- `/usr/share/amass/wordlists/` : Listes intégrées
- `SecLists/Discovery/DNS/` : Listes communautaires
- Listes personnalisées selon l'industrie

### Sources d'information complémentaires
- Shodan pour l'infrastructure
- Censys pour les certificats
- SecurityTrails pour l'historique DNS
- VirusTotal pour les domaines malveillants

## 🔗 Voir aussi
- [Footprinting réseau](../Passive/Footprinting_Réseau.md)
- [Google Dorking](../Passive/OSINT/Google_Dorking.md)
- [Shodan & Censys](../Passive/OSINT/Shodan_Censys.md) 