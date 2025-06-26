# Shodan & Censys - Reconnaissance d'infrastructure

## 📋 Vue d'ensemble
Shodan et Censys sont des moteurs de recherche spécialisés dans les dispositifs connectés à Internet, essentiels pour la reconnaissance passive d'infrastructure.

## 🎯 Objectifs
- Découvrir les assets exposés d'une organisation
- Identifier les services vulnérables
- Analyser la surface d'attaque
- Collecter des informations sur les technologies utilisées

## 🔍 Shodan

### Recherches de base
```
org:"Target Company"
net:192.168.1.0/24
hostname:example.com
ssl:"example.com"
```

### Filtres avancés
```
port:22 country:FR
product:"Apache" version:"2.4"
vuln:CVE-2017-5638
```

### Combinaisons efficaces
```
org:"Target" port:80,443,8080
ssl.cert.subject.cn:"example.com"
http.favicon.hash:12345678
```

## 🔍 Censys

### Syntaxe de recherche
```
parsed.names: example.com
autonomous_system.organization: "Target Company"
protocols: "443/https"
```

### Recherche par certificats
```
parsed.subject.common_name: "*.example.com"
parsed.issuer.organization: "Let's Encrypt"
```

## 🛠️ Techniques avancées

### API Shodan
```python
import shodan

api = shodan.Shodan('YOUR_API_KEY')
results = api.search('org:"Target Company"')
for result in results['matches']:
    print(f"{result['ip_str']}:{result['port']}")
```

### API Censys
```python
import censys.certificates
import censys.ipv4

certificates = censys.certificates.CensysCertificates()
hosts = censys.ipv4.CensysIPv4()
```

## 📊 Analyse des résultats
- **Services exposés** : Ports ouverts et services
- **Versions logicielles** : Identification de vulnérabilités
- **Certificats SSL** : Domaines et sous-domaines
- **Géolocalisation** : Localisation des assets

## ⚠️ Considérations
- Limitations des comptes gratuits
- Respect des conditions d'utilisation
- Données pas toujours en temps réel
- Corrélation avec d'autres sources OSINT

## 📚 Ressources
- Shodan Search Guide
- Censys User Guide
- Awesome Shodan Queries 