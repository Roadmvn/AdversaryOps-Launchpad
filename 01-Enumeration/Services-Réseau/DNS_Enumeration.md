# DNS Enumeration

## 🌐 Vue d'ensemble

Le DNS (Domain Name System) est l'**annuaire d'Internet** qui traduit les noms de domaine en adresses IP. Une énumération DNS approfondie peut révéler des **sous-domaines**, **serveurs** et **infrastructures cachées**.

> **💡 Explication Simple** : DNS c'est comme l'annuaire téléphonique d'Internet. En fouillant dans cet annuaire, on peut découvrir tous les "numéros de téléphone" (IPs) et "adresses" (sous-domaines) d'une organisation.

**Port DNS :** **53** (UDP/TCP)

## ⚠️ **IMPORTANT - À propos des exemples**

> **🎯 TOUS les domaines dans ce guide sont des EXEMPLES FICTIFS !**

### 📝 Exemples vs Réalité :

**Dans le guide (EXEMPLE FICTIF) :**
```
target.com
dev.target.com, mail.target.com
192.168.1.100
```

**Dans la vraie vie, vous devez adapter :**
```
entreprise-cible.com
intranet.entreprise-cible.com, vpn.entreprise-cible.com
203.0.113.15
```

## 🎯 Objectifs de l'Énumération DNS

1. **Découvrir les sous-domaines** - dev.target.com, admin.target.com
2. **Identifier les serveurs de noms** - NS records
3. **Collecter les adresses IP** - A, AAAA records
4. **Trouver les serveurs mail** - MX records
5. **Zone transfer** - Si mal configuré

## 🔍 Phase 1 : Reconnaissance DNS de Base

### Commandes DNS Essentielles
```bash
# 1. Résolution basique
nslookup target.com
dig target.com

# 2. Types d'enregistrements spécifiques
dig target.com A      # Adresses IPv4
dig target.com AAAA   # Adresses IPv6
dig target.com MX     # Serveurs mail
dig target.com NS     # Serveurs de noms
dig target.com TXT    # Enregistrements texte
dig target.com SOA    # Start of Authority

# 3. Recherche inverse
dig -x 192.168.1.100  # IP vers nom
```

### Analyse des Enregistrements DNS
```bash
# 1. Enregistrements TXT (souvent riches en info)
dig target.com TXT

# Rechercher dans les résultats :
# - SPF records (serveurs mail autorisés)
# - DKIM keys (clés de signature mail)
# - Verification strings (Google, Microsoft, etc.)

# 2. Serveurs de noms autoritaires
dig target.com NS

# 3. Informations SOA
dig target.com SOA
# Révèle : serveur primaire, admin email, numéros de série
```

## 🕵️ Phase 2 : Découverte de Sous-domaines

### Brute Force de Sous-domaines

#### Avec dnsrecon
```bash
# 1. Brute force basique
dnsrecon -d target.com -D /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t brt

# 2. Zone walking (si possible)
dnsrecon -d target.com -t zonewalk

# 3. Énumération complète
dnsrecon -d target.com -t std,brt,srv,axfr
```

#### Avec sublist3r
```bash
# 1. Énumération automatique (sources multiples)
sublist3r -d target.com

# 2. Avec brute force intégré
sublist3r -d target.com -b

# 3. Verbeux avec threads
sublist3r -d target.com -v -t 100
```

#### Avec gobuster DNS
```bash
# 1. Mode DNS
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# 2. Avec wildcard detection
gobuster dns -d target.com -w wordlist.txt --wildcard

# 3. Parallélisation
gobuster dns -d target.com -w wordlist.txt -t 50
```

#### Avec amass
```bash
# 1. Énumération passive
amass enum -passive -d target.com

# 2. Énumération active
amass enum -active -d target.com

# 3. Avec sources spécifiques
amass enum -d target.com -src crtsh,virustotal
```

### Méthodes Avancées

#### Certificate Transparency Logs
```bash
# 1. Via crt.sh
curl -s "https://crt.sh/?q=%25.target.com&output=json" | jq -r '.[].name_value' | sort -u

# 2. Via Google CT
curl -s "https://transparencyreport.google.com/transparencyreport/api/v3/httpsct/leafcerts?include_expired=true&include_subdomains=true&domain=target.com"

# 3. Avec certspotter
curl -s "https://certspotter.com/api/v0/certs?domain=target.com" | jq -r '.[].dns_names[]' | sort -u
```

## 🔧 Phase 3 : Zone Transfer Testing

### AXFR (Zone Transfer)
```bash
# 1. Découvrir les serveurs de noms
dig target.com NS

# 2. Tenter zone transfer sur chaque NS
dig @ns1.target.com target.com AXFR
dig @ns2.target.com target.com AXFR

# 3. Avec dnsrecon
dnsrecon -d target.com -t axfr

# 4. Test automatique sur tous les NS
for ns in $(dig +short target.com NS); do
    echo "Testing $ns"
    dig @$ns target.com AXFR
done
```

### Zone Walking (si DNSSEC activé)
```bash
# 1. Avec dnsrecon
dnsrecon -d target.com -t zonewalk

# 2. Avec ldns-walk
ldns-walk target.com

# 3. Manuel avec nsec records
dig target.com NSEC
```

## 🔍 Phase 4 : Techniques Spécialisées

### Reverse DNS Enumeration
```bash
# 1. Pour un range d'IPs
for ip in 192.168.1.{1..254}; do
    dig -x $ip +short
done

# 2. Avec dnsrecon
dnsrecon -r 192.168.1.0/24

# 3. Avec nmap
nmap -sL 192.168.1.0/24
```

### DNS Cache Snooping
```bash
# 1. Test de cache snooping
dig @8.8.8.8 target.com +norecurse

# 2. Avec dnsrecon
dnsrecon -d target.com -t snoop -D /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

### Wildcard Detection
```bash
# 1. Test manuel
dig nonexistent123456.target.com

# 2. Comparer avec
dig existant.target.com

# Si même IP = wildcard actif

# 3. Avec dnsrecon
dnsrecon -d target.com -t std --disable_check_recursion
```

## 📊 Phase 5 : Outils Automatisés

### Fierce
```bash
# 1. Scan basique
fierce -dns target.com

# 2. Avec wordlist custom
fierce -dns target.com -wordlist /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# 3. Range de sous-réseaux
fierce -dns target.com -range 192.168.1.0-192.168.1.255
```

### theHarvester
```bash
# 1. Recherche dans moteurs
theHarvester -d target.com -b google

# 2. Sources multiples
theHarvester -d target.com -b google,bing,yahoo,duckduckgo

# 3. Avec DNS brute force
theHarvester -d target.com -b all -f target_results
```

### DNSmap
```bash
# 1. Brute force basique
dnsmap target.com

# 2. Avec wordlist
dnsmap target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# 3. Avec délai (stealth)
dnsmap target.com -d 1000
```

## 🎯 Phase 6 : Techniques d'Évasion

### DNS over HTTPS (DoH)
```bash
# 1. Via Cloudflare DoH
curl -H "accept: application/dns-json" "https://cloudflare-dns.com/dns-query?name=target.com&type=A"

# 2. Via Google DoH
curl -H "accept: application/dns-json" "https://dns.google/resolve?name=target.com&type=A"
```

### DNS over TLS (DoT)
```bash
# 1. Avec kdig
kdig -d @1.1.1.1 +tls target.com

# 2. Configuration TLS
dig @1.1.1.1 -p 853 +tcp target.com
```

### Rotation de Serveurs DNS
```bash
# 1. Liste de serveurs DNS publics
DNS_SERVERS="8.8.8.8 1.1.1.1 208.67.222.222 9.9.9.9"

# 2. Rotation pour éviter rate limiting
for subdomain in $(cat subdomains.txt); do
    for dns in $DNS_SERVERS; do
        dig @$dns $subdomain.target.com +short
        sleep 1
    done
done
```

## 📋 Checklist DNS Enumeration

### ✅ Énumération de Base :
1. **A/AAAA Records** - Adresses IP principales
2. **MX Records** - Serveurs de messagerie
3. **NS Records** - Serveurs de noms
4. **TXT Records** - Informations diverses
5. **SOA Records** - Informations administratives

### ✅ Découverte Avancée :
1. **Subdomain Brute Force** - www, mail, dev, admin, test
2. **Zone Transfer** - AXFR sur tous les NS
3. **Reverse DNS** - Range d'IPs de l'organisation
4. **Certificate Transparency** - Sous-domaines via certificats SSL

### ✅ Sources Externes :
1. **Search Engines** - Google dorking
2. **Shodan/Censys** - Infrastructure indexée
3. **VirusTotal** - Domaines associés
4. **Archive.org** - Historique des sous-domaines

## 🛠️ Script d'Énumération DNS Automatisé

```bash
#!/bin/bash
# DNS Enumeration Script

DOMAIN=$1
if [ -z "$DOMAIN" ]; then
    echo "Usage: $0 <domain>"
    exit 1
fi

echo "[+] Starting DNS enumeration for $DOMAIN"
mkdir ${DOMAIN}_dns_enum

# 1. Basic DNS records
echo "[+] Getting basic DNS records..."
dig $DOMAIN A > ${DOMAIN}_dns_enum/A_records.txt
dig $DOMAIN AAAA > ${DOMAIN}_dns_enum/AAAA_records.txt
dig $DOMAIN MX > ${DOMAIN}_dns_enum/MX_records.txt
dig $DOMAIN NS > ${DOMAIN}_dns_enum/NS_records.txt
dig $DOMAIN TXT > ${DOMAIN}_dns_enum/TXT_records.txt
dig $DOMAIN SOA > ${DOMAIN}_dns_enum/SOA_records.txt

# 2. Zone transfer attempts
echo "[+] Attempting zone transfers..."
for ns in $(dig +short $DOMAIN NS); do
    echo "Testing $ns"
    dig @$ns $DOMAIN AXFR >> ${DOMAIN}_dns_enum/zone_transfer.txt
done

# 3. Subdomain enumeration
echo "[+] Enumerating subdomains..."
sublist3r -d $DOMAIN -o ${DOMAIN}_dns_enum/subdomains_sublist3r.txt

# 4. DNS brute force
gobuster dns -d $DOMAIN -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -o ${DOMAIN}_dns_enum/subdomains_gobuster.txt

# 5. Certificate transparency
echo "[+] Checking certificate transparency..."
curl -s "https://crt.sh/?q=%25.$DOMAIN&output=json" | jq -r '.[].name_value' | sort -u > ${DOMAIN}_dns_enum/cert_transparency.txt

echo "[+] DNS enumeration completed!"
echo "[+] Results saved in ${DOMAIN}_dns_enum/"
```

## ⚠️ Considérations de Sécurité

### Rate Limiting
- Beaucoup de serveurs DNS limitent les requêtes
- Utiliser des délais entre requêtes
- Rotation de serveurs DNS publics

### Détection
- DNS queries sont loggées
- Éviter les patterns évidents
- Utiliser DoH/DoT pour l'évasion

### Légalité
- DNS enumeration est généralement légal
- Respecter les ToS des services tiers
- Documenter les méthodes utilisées

---
*Cette section couvre l'énumération DNS complète. Pour l'exploitation des vulnérabilités DNS, voir la section 02-Exploitation/Réseaux/*
