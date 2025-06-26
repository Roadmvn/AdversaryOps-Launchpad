# Footprinting Réseau - Reconnaissance Passive

## 📋 Vue d'ensemble
Le footprinting réseau passif consiste à collecter des informations sur l'infrastructure réseau d'une cible sans interaction directe avec ses systèmes.

## 🎯 Objectifs
- Cartographier l'infrastructure réseau
- Identifier les plages d'adresses IP
- Découvrir les technologies utilisées
- Analyser la topologie réseau

## 🔍 Techniques de footprinting

### 1. Recherche de plages IP
```bash
# Via whois
whois -h whois.arin.net "Target Company"
whois -h whois.ripe.net "Target Company"

# Via BGP Looking Glass
# Utilisation de sites comme bgp.he.net
```

### 2. Analyse DNS passive
```bash
# Recherche de sous-domaines via certificats
curl -s "https://crt.sh/?q=example.com&output=json" | jq -r '.[].name_value' | sort -u

# Via DNS Dumpster
# Utilisation de l'interface web dnsdumpster.com
```

### 3. Recherche d'ASN (Autonomous System Number)
```bash
# Identification de l'ASN
whois -h whois.cymru.com " -v 8.8.8.8"

# Recherche des plages IP d'un ASN
whois -h whois.radb.net -- '-i origin AS15169'
```

## 🛠️ Outils recommandés

### Outils en ligne de commande
- `whois` : Informations d'enregistrement
- `dig` : Requêtes DNS
- `host` : Résolution d'hôtes
- `nslookup` : Requêtes DNS alternatives

### Services web
- **BGP.he.net** : Informations BGP et ASN
- **DNSdumpster.com** : Cartographie DNS
- **Crt.sh** : Transparency logs des certificats
- **Virustotal** : Analyse passive de domaines

### Scripts et APIs
```bash
# Amass pour la découverte passive
amass enum -passive -d example.com

# Subfinder pour les sous-domaines
subfinder -d example.com -passive
```

## 📊 Cartographie des résultats

### Structure typique découverte
```
example.com
├── Infrastructure IP : 203.0.113.0/24
├── ASN : AS64496
├── Sous-domaines :
│   ├── www.example.com
│   ├── mail.example.com
│   ├── ftp.example.com
│   └── api.example.com
└── Serveurs de noms :
    ├── ns1.example.com
    └── ns2.example.com
```

## 🔍 Sources d'information

### Bases de données publiques
- **ARIN/RIPE/APNIC** : Allocation d'adresses IP
- **Certificate Transparency** : Certificats SSL/TLS
- **BGP Tables** : Routage Internet
- **DNS Records** : Enregistrements publics

### Métadonnées exploitables
- **TTL DNS** : Configuration serveur
- **Mail Servers** : Infrastructure email
- **CDN Usage** : Services tiers utilisés
- **SSL Certificates** : Domaines et infrastructures

## ⚠️ Limites et précautions
- Informations parfois obsolètes
- Masquage via services de proxy
- Respect des limites de taux des APIs
- Validation croisée nécessaire

## 📚 Méthodologie recommandée
1. **Collecte initiale** : Whois, DNS, certificats
2. **Expansion** : Sous-domaines, plages IP
3. **Corrélation** : Liens entre les assets
4. **Documentation** : Cartographie complète
5. **Validation** : Vérification des informations

## 📖 Références
- OWASP Testing Guide
- NIST SP 800-115
- PTES Technical Guidelines 