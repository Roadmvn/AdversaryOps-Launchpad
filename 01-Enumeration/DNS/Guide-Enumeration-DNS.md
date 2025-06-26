# Énumération DNS - Guide Complet

## 🌐 Vue d'ensemble

L'**énumération DNS** (Domain Name System) est l'art de collecter un maximum d'informations sur une infrastructure réseau en exploitant les serveurs DNS. C'est souvent la première étape cruciale d'un pentest car le DNS révèle l'architecture cachée d'une organisation.

> **💡 Analogie Simple** : Le DNS c'est comme l'annuaire téléphonique d'Internet - mais contrairement à un annuaire classique, on peut le "feuilleter" de différentes façons pour découvrir bien plus d'informations que prévu !

## ⚠️ **IMPORTANT - À propos des exemples**

> **🎯 TOUS les domaines, IPs, serveurs dans ce guide sont FICTIFS !**

### 📝 Exemples vs Réalité :

**Dans le guide (EXEMPLE FICTIF) :**
```
exemple-entreprise.com
192.168.1.50
mail.exemple-entreprise.com
ns1.exemple-entreprise.com
ftp.test-interne.local
```

**Dans la vraie vie, vous devez adapter :**
```
[DOMAINE-CIBLE-AUTORISÉ]
[IP-RÉELLE-CIBLE]
[SERVEUR-MAIL-RÉEL]
[SERVEUR-DNS-RÉEL]
[SERVICE-INTERNE-RÉEL]
```

## 🎯 Objectifs d'apprentissage

Après cette section, vous saurez :
1. **Fondamentaux DNS** - Comprendre architecture et types d'enregistrements
2. **Reconnaissance passive** - Collecter infos sans alerter la cible
3. **Énumération active** - Techniques directes d'interrogation DNS
4. **Zone transfers** - Exploiter mauvaises configurations DNS
5. **Outils avancés** - Maîtriser dnsrecon, fierce, gobuster

## 🏗️ Fondamentaux DNS

### **Architecture DNS hiérarchique**

#### **Composants de l'infrastructure DNS**
```bash
# === HIÉRARCHIE DNS MONDIALE ===

# NIVEAU 1 : Root Servers (.)
# 13 serveurs racine mondiaux (a.root-servers.net à m.root-servers.net)
dig @a.root-servers.net .

# NIVEAU 2 : TLD Servers (Top-Level Domain)
# .com, .org, .fr, .uk, etc.
dig @a.gtld-servers.net com

# NIVEAU 3 : Authoritative Name Servers
# Serveurs DNS de l'organisation (exemple-entreprise.com)
dig @ns1.exemple-entreprise.com exemple-entreprise.com

# NIVEAU 4 : Recursive Resolvers
# DNS de votre FAI ou publics (8.8.8.8, 1.1.1.1)
dig @8.8.8.8 www.exemple-entreprise.com

# EXPLICATION RÉSOLUTION DNS :
# 1. Votre PC demande à DNS local : "Où est www.exemple.com ?"
# 2. DNS local demande aux root servers : "Qui gère .com ?"
# 3. Root répond : "Demandez aux serveurs .com"
# 4. DNS local demande aux .com : "Qui gère exemple.com ?"
# 5. .com répond : "Demandez à ns1.exemple.com"
# 6. DNS local demande à ns1.exemple.com : "Où est www ?"
# 7. ns1.exemple.com répond : "192.168.1.10"
```

### **Types d'enregistrements DNS**

#### **Enregistrements essentiels**
```bash
# === TYPES DNS CRITIQUES POUR PENTEST ===

# A = Adresse IPv4
dig A www.exemple-entreprise.com
# RÉSULTAT : www.exemple-entreprise.com. 300 IN A 192.168.1.10

# AAAA = Adresse IPv6  
dig AAAA www.exemple-entreprise.com
# RÉSULTAT : www.exemple-entreprise.com. 300 IN AAAA 2001:db8::1

# CNAME = Alias canonique
dig CNAME mail.exemple-entreprise.com
# RÉSULTAT : mail.exemple-entreprise.com. 300 IN CNAME exchange.exemple-entreprise.com.

# MX = Serveurs de messagerie
dig MX exemple-entreprise.com
# RÉSULTAT : 
# exemple-entreprise.com. 300 IN MX 10 mail.exemple-entreprise.com.
# exemple-entreprise.com. 300 IN MX 20 backup-mail.exemple-entreprise.com.

# NS = Serveurs de noms
dig NS exemple-entreprise.com
# RÉSULTAT :
# exemple-entreprise.com. 300 IN NS ns1.exemple-entreprise.com.
# exemple-entreprise.com. 300 IN NS ns2.exemple-entreprise.com.

# PTR = Résolution inverse (IP → nom)
dig -x 192.168.1.10
# RÉSULTAT : 10.1.168.192.in-addr.arpa. 300 IN PTR www.exemple-entreprise.com.

# TXT = Enregistrements texte (SPF, DKIM, vérifications)
dig TXT exemple-entreprise.com
# RÉSULTAT :
# exemple-entreprise.com. 300 IN TXT "v=spf1 include:_spf.google.com ~all"
# exemple-entreprise.com. 300 IN TXT "google-site-verification=ABC123DEF456"

# SRV = Services spécifiques
dig SRV _sip._tcp.exemple-entreprise.com
# RÉSULTAT : _sip._tcp.exemple-entreprise.com. 300 IN SRV 10 5 5060 sipserver.exemple-entreprise.com.

# SOA = Start of Authority (infos zone DNS)
dig SOA exemple-entreprise.com
# RÉSULTAT : exemple-entreprise.com. 300 IN SOA ns1.exemple-entreprise.com. admin.exemple-entreprise.com. 2024012001 3600 1800 604800 86400
```

## 🔍 Reconnaissance Passive DNS

### **OSINT DNS sans contact direct**

#### **Moteurs de recherche spécialisés**
```bash
# === RECHERCHE PASSIVE AVEC SHODAN ===

# 1. Installer shodan CLI
pip3 install shodan
shodan init YOUR_API_KEY

# 2. Rechercher par nom d'organisation
shodan search "exemple-entreprise.com"

# 3. Rechercher serveurs DNS exposés
shodan search "port:53 exemple-entreprise"

# RÉSULTATS TYPIQUES SHODAN :
# 203.0.113.10:53 (DNS Server)
# 203.0.113.15:53 (DNS Server) 
# Banner: "BIND 9.11.4-P2"
# Location: Paris, France
# ISP: OVH SAS

# === UTILISATION CENSYS ===
# Interface web : https://censys.io

# Recherches utiles :
# "exemple-entreprise.com" AND services.port:53
# "exemple-entreprise" AND protocols:("53/dns")
# autonomous_system.description:"ENTREPRISE-AS"
```

#### **Archives DNS et historiques**
```bash
# === ARCHIVES DNS HISTORIQUES ===

# 1. SecurityTrails (API + web)
curl -H "APIKEY: YOUR_API_KEY" \
     "https://api.securitytrails.com/v1/domain/exemple-entreprise.com/subdomains"

# 2. DNS History avec dig
# Rechercher dans anciennes zones DNS archivées
dig @1.1.1.1 exemple-entreprise.com ANY +noall +answer

# 3. Wayback Machine pour DNS
# Chercher dans archives web des références à sous-domaines
# URL: https://web.archive.org/web/*/exemple-entreprise.com

# 4. Certificats SSL/TLS (révèlent sous-domaines)
# Certificate Transparency logs
curl -s "https://crt.sh/?q=%25.exemple-entreprise.com&output=json" | jq -r '.[].name_value' | sort -u

# EXEMPLE RÉSULTATS crt.sh :
# www.exemple-entreprise.com
# mail.exemple-entreprise.com  
# admin.exemple-entreprise.com
# *.dev.exemple-entreprise.com
# vpn.exemple-entreprise.com
# test.exemple-entreprise.com

# 5. Google Dorks pour DNS
# site:exemple-entreprise.com -www
# inurl:"exemple-entreprise.com" -site:exemple-entreprise.com
# "exemple-entreprise.com" filetype:txt
```

### **Whois et informations de domaine**

#### **Énumération Whois complète**
```bash
# === INFORMATIONS WHOIS ===

# 1. Whois domaine principal
whois exemple-entreprise.com

# INFORMATIONS CRITIQUES :
# Registrant: EXEMPLE ENTREPRISE SAS
# Admin Email: admin@exemple-entreprise.com
# Tech Email: tech@exemple-entreprise.com
# Name Servers: ns1.exemple-entreprise.com, ns2.exemple-entreprise.com
# Registrar: OVH
# Creation Date: 2010-03-15
# Expiration Date: 2025-03-15

# 2. Whois IP (pour connaître plages réseau)
whois 203.0.113.10

# RÉSULTAT EXEMPLE :
# NetName: EXEMPLE-NET
# NetRange: 203.0.113.0 - 203.0.113.255
# CIDR: 203.0.113.0/24
# Organization: Exemple Entreprise (EE-123)
# Address: 123 Rue de la Paix, 75001 Paris

# 3. Whois serveurs DNS
whois ns1.exemple-entreprise.com
whois ns2.exemple-entreprise.com

# 4. Recherche email contacts dans whois
whois exemple-entreprise.com | grep -i email

# 5. Whois domaines similaires (typosquatting)
whois exemple-enterprise.com
whois example-entreprise.com
whois exemple-entreprise.net
```

## 🎯 Énumération Active DNS

### **Reconnaissance directe avec dig**

#### **Interrogation DNS basique**
```bash
# === COMMANDES DIG ESSENTIELLES ===

# 1. Résolution simple
dig www.exemple-entreprise.com

# ANALYSE RÉPONSE DIG :
# ;; QUESTION SECTION:
# ;www.exemple-entreprise.com.   IN  A
# 
# ;; ANSWER SECTION:  
# www.exemple-entreprise.com. 300 IN A 192.168.1.10
#
# ;; AUTHORITY SECTION:
# exemple-entreprise.com. 300 IN NS ns1.exemple-entreprise.com.
# exemple-entreprise.com. 300 IN NS ns2.exemple-entreprise.com.

# 2. Interroger serveur DNS spécifique
dig @ns1.exemple-entreprise.com www.exemple-entreprise.com

# 3. Tous les types d'enregistrements
dig exemple-entreprise.com ANY

# 4. Résolution inverse (IP vers nom)
dig -x 192.168.1.10

# 5. Trace complet de résolution
dig +trace www.exemple-entreprise.com

# TRACE MONTRE CHEMIN COMPLET :
# . (root) → com. → exemple-entreprise.com. → www
# Utile pour comprendre délégations DNS

# 6. Réponse courte seulement
dig +short www.exemple-entreprise.com
# 192.168.1.10

# 7. Informations verboses
dig +noall +answer +authority +additional exemple-entreprise.com ANY
```

#### **Énumération serveurs de messagerie**
```bash
# === ANALYSE INFRASTRUCTURE MAIL ===

# 1. Serveurs MX (priorités)
dig MX exemple-entreprise.com

# ANALYSE PRIORITÉS MX :
# exemple-entreprise.com. 300 IN MX 10 mail.exemple-entreprise.com.     ← Priorité 10 (principal)
# exemple-entreprise.com. 300 IN MX 20 backup-mail.exemple-entreprise.com. ← Priorité 20 (backup)
# Plus le chiffre est PETIT, plus la priorité est ÉLEVÉE

# 2. Résoudre IPs des serveurs mail
dig A mail.exemple-entreprise.com
dig A backup-mail.exemple-entreprise.com

# 3. Vérifier enregistrements SPF (anti-spam)
dig TXT exemple-entreprise.com | grep spf

# EXEMPLE SPF :
# "v=spf1 include:_spf.google.com include:mailgun.org ~all"
# SIGNIFICATION :
# v=spf1 = Version SPF 1
# include:_spf.google.com = Autorise serveurs Google
# include:mailgun.org = Autorise serveurs Mailgun  
# ~all = Soft fail (suspect mais accepte)

# 4. Enregistrements DKIM (authentification)
dig TXT selector1._domainkey.exemple-entreprise.com
dig TXT default._domainkey.exemple-entreprise.com

# 5. Politique DMARC
dig TXT _dmarc.exemple-entreprise.com

# EXEMPLE DMARC :
# "v=DMARC1; p=quarantine; rua=mailto:dmarc@exemple-entreprise.com"
# p=quarantine = Mettre en quarantaine emails suspects
# rua= = Adresse pour rapports d'agrégation
```

### **Bruteforce de sous-domaines**

#### **Énumération avec gobuster**
```bash
# === GOBUSTER DNS ENUMERATION ===

# 1. Installation gobuster
sudo apt install gobuster

# 2. Wordlist sous-domaines commune
# Fichier : /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt

# 3. Bruteforce sous-domaines basique
gobuster dns -d exemple-entreprise.com -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt

# SORTIE EXEMPLE :
# Found: www.exemple-entreprise.com
# Found: mail.exemple-entreprise.com  
# Found: ftp.exemple-entreprise.com
# Found: admin.exemple-entreprise.com
# Found: test.exemple-entreprise.com
# Found: dev.exemple-entreprise.com
# Found: staging.exemple-entreprise.com

# 4. Gobuster avec threads multiples (plus rapide)
gobuster dns -d exemple-entreprise.com -w wordlist.txt -t 50

# 5. Gobuster avec wildcard detection
gobuster dns -d exemple-entreprise.com -w wordlist.txt --wildcard

# 6. Sauvegarder résultats
gobuster dns -d exemple-entreprise.com -w wordlist.txt -o subdomains_found.txt

# 7. Wordlist personnalisée pour entreprise
echo -e "www\nmail\nftp\nadmin\ntest\ndev\nstaging\nprod\napi\nvpn\nremote\nportal\nintranet\nextranet\nblog\nshop\necommerce\ncrm\nerp\nhr\nrh\nfinance\ncompta\nit\nhelp\nsupport\nticket\nmonitoring\nbackup\nfile\nfiles\ndocs\nwiki\nconf\nconfig\nmanage\nmanagement\ngit\nsvn\njenkins\nci\ncd\nstats\nanalytics\nlog\nlogs\ndb\ndatabase\nsql\nmysql\npostgres\noracle\nldap\nad\ndc\ndc1\ndc2\nns\nns1\nns2\ndns\nweb\nweb1\nweb2\napp\napp1\napp2" > custom_subdomains.txt

gobuster dns -d exemple-entreprise.com -w custom_subdomains.txt
```

#### **Énumération avec dnsrecon**
```bash
# === DNSRECON ADVANCED ENUMERATION ===

# 1. Installation dnsrecon
sudo apt install dnsrecon

# 2. Énumération standard
dnsrecon -d exemple-entreprise.com

# 3. Bruteforce avec wordlist
dnsrecon -d exemple-entreprise.com -D /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -t brt

# 4. Zone transfer attempt
dnsrecon -d exemple-entreprise.com -t axfr

# 5. Reverse DNS lookup sur plage IP
dnsrecon -r 192.168.1.0/24

# EXEMPLE REVERSE LOOKUP :
# 192.168.1.1     → gw.exemple-entreprise.com
# 192.168.1.10    → www.exemple-entreprise.com
# 192.168.1.15    → mail.exemple-entreprise.com
# 192.168.1.20    → db.exemple-entreprise.com
# 192.168.1.25    → backup.exemple-entreprise.com

# 6. Google dorks enumeration
dnsrecon -d exemple-entreprise.com -t goo

# 7. Énumération complète avec rapport
dnsrecon -d exemple-entreprise.com -t std,brt,srv,axfr -D wordlist.txt --xml dnsrecon_report.xml

# 8. Cache snooping (si serveur DNS récursif)
dnsrecon -d exemple-entreprise.com -t snoop -D cache_domains.txt
```

### **Fierce - Énumération semi-agressive**

#### **Utilisation de Fierce**
```bash
# === FIERCE DNS SCANNER ===

# 1. Installation fierce
sudo apt install fierce

# 2. Scan basique avec wordlist interne
fierce --domain exemple-entreprise.com

# SORTIE FIERCE TYPIQUE :
# DNS Servers for exemple-entreprise.com:
#     ns1.exemple-entreprise.com
#     ns2.exemple-entreprise.com
# 
# Trying zone transfer first...
#     Testing ns1.exemple-entreprise.com
#         Request timed out or transfer not allowed.
#     Testing ns2.exemple-entreprise.com  
#         Request timed out or transfer not allowed.
# 
# Unsuccessful in zone transfer (it was worth a shot)
# Okay, trying the good old fashioned way... brute force
# 
# Checking for wildcard DNS...
# Nope. Good.
# Now performing 2280 test(s)...
# 
# Found: admin.exemple-entreprise.com (192.168.1.30)
# Found: ftp.exemple-entreprise.com (192.168.1.40)
# Found: mail.exemple-entreprise.com (192.168.1.15)

# 3. Fierce avec wordlist personnalisée
fierce --domain exemple-entreprise.com --wordlist custom_subdomains.txt

# 4. Recherche dans plage IP adjacente
fierce --domain exemple-entreprise.com --range 192.168.1.1-192.168.1.50

# 5. Output vers fichier
fierce --domain exemple-entreprise.com > fierce_results.txt

# 6. Mode verbose pour debugging
fierce --domain exemple-entreprise.com --verbose
```

## 🔄 Zone Transfers (AXFR)

### **Principe et exploitation**

#### **Qu'est-ce qu'un zone transfer**
```bash
# === ZONE TRANSFER CONCEPTS ===

# ZONE TRANSFER = Mécanisme légitime DNS
# Permet synchronisation entre serveurs DNS primaire/secondaire
# PROBLÈME : Parfois mal configuré → accessible publiquement

# TYPES DE ZONE TRANSFERS :
# AXFR = Full Zone Transfer (toute la zone)
# IXFR = Incremental Zone Transfer (modifications seulement)

# SI SUCCESSFUL → On obtient TOUTE la zone DNS !
# = Liste COMPLÈTE des sous-domaines, IPs, services

# ANALOGIE : C'est comme voler l'annuaire interne complet
# au lieu de deviner nom par nom
```

#### **Test zone transfer manuel**
```bash
# === TENTATIVE ZONE TRANSFER ===

# 1. Identifier serveurs DNS autoritaires
dig NS exemple-entreprise.com

# RÉSULTAT :
# exemple-entreprise.com. 300 IN NS ns1.exemple-entreprise.com.
# exemple-entreprise.com. 300 IN NS ns2.exemple-entreprise.com.

# 2. Résoudre IPs des serveurs DNS
dig A ns1.exemple-entreprise.com
dig A ns2.exemple-entreprise.com

# 3. Tenter AXFR sur chaque serveur DNS
dig @ns1.exemple-entreprise.com exemple-entreprise.com AXFR

# SI ZONE TRANSFER AUTORISÉ (VULNÉRABILITÉ) :
# exemple-entreprise.com. 86400 IN SOA ns1.exemple-entreprise.com. admin.exemple-entreprise.com. 2024012001 3600 1800 604800 86400
# exemple-entreprise.com. 300 IN NS ns1.exemple-entreprise.com.
# exemple-entreprise.com. 300 IN NS ns2.exemple-entreprise.com.
# exemple-entreprise.com. 300 IN MX 10 mail.exemple-entreprise.com.
# admin.exemple-entreprise.com. 300 IN A 192.168.1.30
# api.exemple-entreprise.com. 300 IN A 192.168.1.50
# backup.exemple-entreprise.com. 300 IN A 192.168.1.60
# db.exemple-entreprise.com. 300 IN A 192.168.1.70
# dev.exemple-entreprise.com. 300 IN A 192.168.1.80
# ftp.exemple-entreprise.com. 300 IN A 192.168.1.40
# intranet.exemple-entreprise.com. 300 IN A 192.168.1.90
# mail.exemple-entreprise.com. 300 IN A 192.168.1.15
# monitoring.exemple-entreprise.com. 300 IN A 192.168.1.100
# test.exemple-entreprise.com. 300 IN A 192.168.1.110
# vpn.exemple-entreprise.com. 300 IN A 192.168.1.120
# www.exemple-entreprise.com. 300 IN A 192.168.1.10

# 4. Tester avec nslookup (alternative)
nslookup
> server ns1.exemple-entreprise.com
> set type=AXFR
> exemple-entreprise.com

# 5. Test avec host command
host -t AXFR exemple-entreprise.com ns1.exemple-entreprise.com
```

#### **Exploitation automatisée zone transfers**
```bash
# === SCRIPTS AUTOMATISÉS AXFR ===

# 1. Script bash simple test AXFR
#!/bin/bash
# axfr_test.sh

DOMAIN=$1
if [ -z "$DOMAIN" ]; then
    echo "Usage: $0 domain.com"
    exit 1
fi

echo "[+] Testing zone transfer for $DOMAIN"

# Obtenir serveurs NS
NS_SERVERS=$(dig +short NS $DOMAIN)

for ns in $NS_SERVERS; do
    echo "[*] Testing $ns"
    
    # Résoudre IP du serveur NS
    NS_IP=$(dig +short A $ns)
    echo "    IP: $NS_IP"
    
    # Tenter AXFR
    echo "    Attempting zone transfer..."
    AXFR_RESULT=$(dig @$ns $DOMAIN AXFR +noall +answer)
    
    if [ ! -z "$AXFR_RESULT" ]; then
        echo "    [VULNERABLE] Zone transfer successful!"
        echo "$AXFR_RESULT" > "axfr_${DOMAIN}_${ns}.txt"
        echo "    Results saved to axfr_${DOMAIN}_${ns}.txt"
    else
        echo "    [PROTECTED] Zone transfer denied"
    fi
    echo ""
done

# Utilisation :
chmod +x axfr_test.sh
./axfr_test.sh exemple-entreprise.com

# 2. Test AXFR avec dnsrecon
dnsrecon -d exemple-entreprise.com -t axfr

# 3. Test AXFR avec dnsenum
dnsenum --enum exemple-entreprise.com

# 4. Massive zone transfer testing
#!/bin/bash
# mass_axfr.sh

while read domain; do
    echo "Testing $domain"
    timeout 10 dig @$(dig +short NS $domain | head -1) $domain AXFR +noall +answer > /dev/null
    if [ $? -eq 0 ]; then
        echo "[VULNERABLE] $domain allows zone transfer"
    fi
done < domains.txt
```

## 🔍 DNS Enumeration Avancée

### **Détection de wildcards**

#### **Problème des wildcards DNS**
```bash
# === DNS WILDCARDS ===

# WILDCARD DNS = *.exemple-entreprise.com → même IP
# PROBLÈME : Fausse tous sous-domaines comme "existants"

# 1. Test détection wildcard
dig randomstring123456.exemple-entreprise.com

# SI WILDCARD ACTIVÉ :
# randomstring123456.exemple-entreprise.com. 300 IN A 192.168.1.200
# ↑ Résout vers IP même si sous-domaine n'existe pas vraiment

# 2. Contournement wildcard avec gobuster
gobuster dns -d exemple-entreprise.com -w wordlist.txt --wildcard

# 3. Test wildcard intelligent
#!/bin/bash
# wildcard_test.sh

DOMAIN=$1
RANDOM_SUBDOMAIN="test$(date +%s)$(shuf -i 1000-9999 -n 1)"

echo "Testing wildcard for $DOMAIN"
echo "Random test: $RANDOM_SUBDOMAIN.$DOMAIN"

WILDCARD_IP=$(dig +short $RANDOM_SUBDOMAIN.$DOMAIN)

if [ ! -z "$WILDCARD_IP" ]; then
    echo "[WARNING] Wildcard detected: $WILDCARD_IP"
    echo "All DNS enumeration results should be filtered against this IP"
else
    echo "[OK] No wildcard detected"
fi

# 4. Filtrage résultats contre wildcard
# Si wildcard détecté sur 192.168.1.200, filtrer :
gobuster dns -d exemple-entreprise.com -w wordlist.txt | grep -v "192.168.1.200"
```

### **IPv6 et DNS**

#### **Énumération IPv6**
```bash
# === IPV6 DNS ENUMERATION ===

# IPv6 souvent négligé mais peut révéler infos !

# 1. Enregistrements AAAA
dig AAAA www.exemple-entreprise.com

# 2. Énumération IPv6 complète
for subdomain in www mail ftp admin; do
    echo "Testing $subdomain"
    dig AAAA $subdomain.exemple-entreprise.com +short
done

# 3. Reverse IPv6 (plus complexe)
# IPv6: 2001:db8::1 devient 1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.8.b.d.0.1.0.0.2.ip6.arpa
dig -x 2001:db8::1

# 4. IPv6 avec gobuster
gobuster dns -d exemple-entreprise.com -w wordlist.txt --resolver-timeout 3s -6

# 5. Script énumération IPv6
#!/bin/bash
# ipv6_enum.sh

DOMAIN=$1
echo "IPv6 enumeration for $DOMAIN"

# Test enregistrements AAAA
echo "[*] Testing AAAA records..."
dig AAAA $DOMAIN +short

# Test sous-domaines IPv6
echo "[*] Testing subdomain AAAA records..."
for sub in www mail ftp admin api vpn; do
    ipv6=$(dig AAAA $sub.$DOMAIN +short)
    if [ ! -z "$ipv6" ]; then
        echo "Found: $sub.$DOMAIN → $ipv6"
    fi
done
```

### **DNS over HTTPS (DoH) et DNS over TLS (DoT)**

#### **Énumération via DoH**
```bash
# === DNS OVER HTTPS ENUMERATION ===

# Certaines organisations utilisent DoH/DoT
# Peut révéler infos différentes des DNS classiques

# 1. DoH avec curl (Cloudflare)
curl -H "accept: application/dns-json" \
     "https://1.1.1.1/dns-query?name=exemple-entreprise.com&type=A"

# RÉPONSE JSON :
{
  "Status": 0,
  "TC": false,
  "RD": true,
  "RA": true,
  "AD": true,
  "CD": false,
  "Question": [
    {
      "name": "exemple-entreprise.com.",
      "type": 1
    }
  ],
  "Answer": [
    {
      "name": "exemple-entreprise.com.",
      "type": 1,
      "TTL": 300,
      "data": "192.168.1.10"
    }
  ]
}

# 2. DoH via Google
curl "https://dns.google/resolve?name=exemple-entreprise.com&type=A"

# 3. Énumération DoH automatisée
#!/bin/bash
# doh_enum.sh

DOMAIN=$1
DOH_SERVER="https://1.1.1.1/dns-query"

for type in A AAAA MX NS TXT; do
    echo "Querying $type records for $DOMAIN"
    curl -s -H "accept: application/dns-json" \
         "$DOH_SERVER?name=$DOMAIN&type=$type" | \
         jq -r '.Answer[]?.data // empty'
    echo ""
done

# 4. DoT avec kdig (knot-utils)
sudo apt install knot-utils
kdig @1.1.1.1 +tls exemple-entreprise.com
```

## 🛠️ Outils Avancés DNS

### **DNSEnum - Énumération complète**

#### **Utilisation DNSEnum**
```bash
# === DNSENUM COMPREHENSIVE SCANNING ===

# 1. Installation dnsenum
sudo apt install dnsenum

# 2. Scan basique
dnsenum exemple-entreprise.com

# FONCTIONNALITÉS DNSENUM :
# - Host address (A records)
# - Name servers (NS)
# - Mail exchangers (MX)  
# - Zone transfer attempts
# - Subdomain brute forcing
# - Reverse lookup on Class C
# - Google scraping

# 3. Énumération avec wordlist personnalisée
dnsenum --dnsserver ns1.exemple-entreprise.com \
        --file custom_subdomains.txt \
        exemple-entreprise.com

# 4. Énumération avec scraping Google
dnsenum --google-scraping exemple-entreprise.com

# 5. Reverse lookup sur plage réseau
dnsenum --class-c 192.168.1 exemple-entreprise.com

# 6. Output vers fichier XML
dnsenum --output dnsenum_results.xml exemple-entreprise.com

# 7. Configuration complète
dnsenum --threads 10 \
        --timeout 10 \
        --retry 3 \
        --dnsserver 8.8.8.8 \
        --file /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt \
        --output results.xml \
        exemple-entreprise.com
```

### **Sublist3r - Énumération OSINT**

#### **Sublist3r pour OSINT passif**
```bash
# === SUBLIST3R OSINT ENUMERATION ===

# 1. Installation Sublist3r
git clone https://github.com/aboul3la/Sublist3r.git
cd Sublist3r
pip3 install -r requirements.txt

# 2. Énumération basique
python3 sublist3r.py -d exemple-entreprise.com

# SOURCES SUBLIST3R :
# - Google
# - Yahoo  
# - Bing
# - Baidu
# - Ask
# - Netcraft
# - DNSdumpster
# - VirusTotal
# - ThreatCrowd
# - SSL Certificates
# - PassiveDNS

# 3. Énumération avec bruteforce
python3 sublist3r.py -d exemple-entreprise.com -b

# 4. Utiliser seulement certaines sources
python3 sublist3r.py -d exemple-entreprise.com -e google,yahoo,virustotal

# 5. Output vers fichier
python3 sublist3r.py -d exemple-entreprise.com -o sublist3r_results.txt

# 6. Énumération avec threads
python3 sublist3r.py -d exemple-entreprise.com -t 40

# 7. Verbose mode
python3 sublist3r.py -d exemple-entreprise.com -v
```

### **Amass - Framework complet**

#### **Amass pour énumération professionnelle**
```bash
# === AMASS FRAMEWORK ===

# 1. Installation Amass
sudo apt install amass

# 2. Énumération passive (OSINT seulement)
amass enum -passive -d exemple-entreprise.com

# 3. Énumération active (avec bruteforce)
amass enum -active -d exemple-entreprise.com

# 4. Configuration avec API keys (plus de résultats)
# Créer fichier config : ~/.config/amass/config.ini
[data_sources]
[data_sources.AlienVault]
[data_sources.AlienVault.Credentials]
apikey = YOUR_API_KEY

[data_sources.Shodan]
[data_sources.Shodan.Credentials]  
apikey = YOUR_API_KEY

[data_sources.SecurityTrails]
[data_sources.SecurityTrails.Credentials]
apikey = YOUR_API_KEY

# 5. Amass avec config et sources API
amass enum -config ~/.config/amass/config.ini -d exemple-entreprise.com

# 6. Énumération sur plusieurs domaines
amass enum -df domains.txt

# Contenu domains.txt :
# exemple-entreprise.com
# subsidiary.com  
# partner-company.net

# 7. Output formats multiples
amass enum -d exemple-entreprise.com -json amass_results.json
amass enum -d exemple-entreprise.com -dir ./amass_output

# 8. Visualisation résultats
amass viz -d3 -dir ./amass_output

# 9. Intelligence gathering
amass intel -org "Exemple Entreprise"
amass intel -asn 64512
```

## 📊 Analyse et Corrélation

### **Analyse des résultats**

#### **Corrélation multi-sources**
```bash
# === ANALYSE CROISÉE RÉSULTATS ===

# 1. Consolidation résultats de tous outils
cat gobuster_results.txt fierce_results.txt sublist3r_results.txt | \
    grep -E '^[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$' | \
    sort -u > all_subdomains.txt

# 2. Résolution IPs pour tous sous-domaines
#!/bin/bash
# resolve_all.sh

while read subdomain; do
    ip=$(dig +short A $subdomain | head -1)
    if [ ! -z "$ip" ] && [[ $ip =~ ^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+$ ]]; then
        echo "$subdomain,$ip"
    fi
done < all_subdomains.txt > subdomain_ips.csv

# 3. Regroupement par plage IP
#!/bin/bash
# ip_ranges.sh

cat subdomain_ips.csv | while IFS=, read subdomain ip; do
    network=$(echo $ip | cut -d. -f1-3)
    echo "$network.0/24,$subdomain,$ip"
done | sort > networks.csv

# 4. Identification services par port
#!/bin/bash
# port_scan_discovered.sh

while IFS=, read subdomain ip; do
    echo "Scanning $subdomain ($ip)"
    nmap -Pn -sS -F $ip | grep -E '^[0-9]+/(tcp|udp)' | \
        while read port_line; do
            echo "$subdomain,$ip,$port_line"
        done
done < subdomain_ips.csv > services.csv

# 5. Analyse technologies web
#!/bin/bash
# web_tech_scan.sh

while IFS=, read subdomain ip; do
    # Test HTTP/HTTPS
    for proto in http https; do
        response=$(curl -s -I -m 5 $proto://$subdomain 2>/dev/null)
        if [ $? -eq 0 ]; then
            server=$(echo "$response" | grep -i "server:" | cut -d: -f2- | tr -d ' \r')
            title=$(curl -s -m 5 $proto://$subdomain | grep -i "<title>" | sed 's/<title>//I;s/<\/title>//I' | tr -d '\r\n' | cut -c1-50)
            echo "$subdomain,$ip,$proto,$server,$title"
        fi
    done
done < subdomain_ips.csv > web_technologies.csv
```

#### **Reporting et visualisation**
```bash
# === GÉNÉRATION RAPPORTS ===

# 1. Rapport texte simple
#!/bin/bash
# dns_report.sh

DOMAIN=$1
echo "DNS Enumeration Report for $DOMAIN"
echo "=================================="
echo ""

echo "Total subdomains found: $(wc -l < all_subdomains.txt)"
echo ""

echo "IP Ranges discovered:"
cut -d, -f1 networks.csv | sort -u
echo ""

echo "Web services found:"
grep -E "80|443|8080|8443" services.csv | cut -d, -f1-2
echo ""

echo "Mail servers:"
dig MX $DOMAIN +short
echo ""

echo "Name servers:"  
dig NS $DOMAIN +short
echo ""

# 2. Export CSV pour Excel
echo "subdomain,ip,http_status,https_status,server,title" > final_report.csv

while IFS=, read subdomain ip; do
    # Test HTTP
    http_status=$(curl -s -o /dev/null -w "%{http_code}" -m 5 http://$subdomain 2>/dev/null || echo "000")
    https_status=$(curl -s -o /dev/null -w "%{http_code}" -m 5 https://$subdomain 2>/dev/null || echo "000")
    
    # Récupérer infos serveur
    server=$(curl -s -I -m 5 http://$subdomain 2>/dev/null | grep -i "server:" | cut -d: -f2- | tr -d ' \r' || echo "Unknown")
    title=$(curl -s -m 5 http://$subdomain 2>/dev/null | grep -i "<title>" | sed 's/<title>//I;s/<\/title>//I' | tr -d '\r\n' | cut -c1-30 || echo "No title")
    
    echo "$subdomain,$ip,$http_status,$https_status,$server,$title" >> final_report.csv
done < subdomain_ips.csv

# 3. Génération diagramme réseau (avec graphviz)
sudo apt install graphviz

cat > network_diagram.dot << EOF
digraph network {
    rankdir=LR;
    node [shape=box];
    
    "$DOMAIN" [style=filled,fillcolor=lightblue];
EOF

while IFS=, read subdomain ip; do
    echo "    \"$subdomain\" [label=\"$subdomain\\n$ip\"];" >> network_diagram.dot
    echo "    \"$DOMAIN\" -> \"$subdomain\";" >> network_diagram.dot
done < subdomain_ips.csv

echo "}" >> network_diagram.dot

# Générer image
dot -Tpng network_diagram.dot -o network_diagram.png
```

## 📋 Checklist Énumération DNS

### **Reconnaissance passive**
```bash
☐ Informations Whois collectées (domaine, contacts, serveurs)
☐ Recherche dans archives DNS (SecurityTrails, etc.)
☐ Certificats SSL analysés (crt.sh, Censys)
☐ Google dorks exécutés pour sous-domaines
☐ Recherche OSINT via Shodan/Censys
☐ Sublist3r ou Amass exécuté (sources multiples)
☐ Recherche domaines similaires (typosquatting)
☐ Informations ASN et plages IP identifiées
```

### **Énumération active**
```bash
☐ Serveurs DNS autoritaires identifiés
☐ Zone transfers testés (AXFR) sur tous serveurs NS
☐ Enregistrements DNS basiques collectés (A, AAAA, MX, NS, TXT)
☐ SPF, DKIM, DMARC analysés
☐ Bruteforce sous-domaines avec wordlists multiples
☐ Test détection wildcard DNS effectué
☐ Énumération IPv6 (AAAA records)
☐ Reverse DNS sur plages IP découvertes
```

### **Outils et techniques**
```bash
☐ dig utilisé pour requêtes DNS manuelles
☐ nslookup testé en backup de dig
☐ gobuster DNS exécuté avec wordlists adaptées
☐ dnsrecon utilisé pour énumération complète
☐ fierce exécuté pour bruteforce semi-agressif
☐ dnsenum lancé pour scan compréhensif
☐ Amass configuré avec API keys pour max résultats
☐ Scripts personnalisés adaptés au contexte
```

### **Analyse et reporting**
```bash
☐ Résultats de tous outils consolidés
☐ Doublons supprimés et données nettoyées  
☐ IPs résolues pour tous sous-domaines
☐ Services web identifiés (HTTP/HTTPS)
☐ Technologies serveurs détectées
☐ Plages réseau regroupées et analysées
☐ Rapport final généré (CSV + texte)
☐ Diagramme réseau créé si pertinent
```

### **Sécurisation et bonnes pratiques**
```bash
☐ Rate limiting respecté (pas de flood DNS)
☐ Requêtes distribuées sur plusieurs serveurs
☐ Logs d'activité maintenus pour traçabilité
☐ Autorisation de test confirmée avant énumération active
☐ Résultats sensibles protégés et chiffrés
☐ Nettoyage fichiers temporaires effectué
☐ Recommandations sécurité DNS rédigées
☐ Plan remediation proposé si vulnérabilités
```

## 🎯 Ressources et Références

### **Outils essentiels**
- **dig** : https://linux.die.net/man/1/dig
- **gobuster** : https://github.com/OJ/gobuster
- **dnsrecon** : https://github.com/darkoperator/dnsrecon
- **fierce** : https://github.com/mschwager/fierce
- **Amass** : https://github.com/OWASP/Amass

### **Wordlists DNS**
- **SecLists DNS** : https://github.com/danielmiessler/SecLists/tree/master/Discovery/DNS
- **All.txt** : https://gist.github.com/jhaddix/f64c97d0863a78454e44c2f7119c2a6a
- **Subdomains-top1million** : https://wordlists-cdn.assetnote.io/data/manual/best-dns-wordlist.txt

### **Services OSINT**
- **Certificate Transparency** : https://crt.sh/
- **SecurityTrails** : https://securitytrails.com/
- **DNSdumpster** : https://dnsdumpster.com/
- **VirusTotal** : https://www.virustotal.com/

### **RFC et documentation**
- **RFC 1035** : Domain Names - Implementation and Specification
- **RFC 1996** : A Mechanism for Prompt Notification of Zone Changes (DNS NOTIFY)
- **RFC 5936** : DNS Zone Transfer Protocol (AXFR)

---
*Le DNS est souvent le premier maillon faible d'une infrastructure. Une énumération DNS méthodique révèle bien plus que prévu !* 