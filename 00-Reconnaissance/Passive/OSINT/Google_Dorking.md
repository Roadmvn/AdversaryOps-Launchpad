# Google Dorking - Reconnaissance OSINT

## 📋 Vue d'ensemble
Le Google Dorking utilise des opérateurs de recherche avancés pour découvrir des informations sensibles indexées par les moteurs de recherche.

## 🎯 Objectifs
- Découvrir des fichiers exposés
- Identifier des vulnérabilités web
- Collecter des informations sur l'organisation
- Trouver des configurations par défaut

## 🔍 Opérateurs essentiels

### Opérateurs de base
```
site:example.com          # Recherche sur un site spécifique
filetype:pdf              # Type de fichier spécifique
intitle:"admin login"     # Texte dans le titre
inurl:admin               # Texte dans l'URL
intext:"password"         # Texte dans le contenu
```

### Combinaisons puissantes
```
site:example.com filetype:pdf
site:example.com inurl:admin
site:example.com intitle:"index of"
```

## 🎯 Dorks utiles pour la reconnaissance

### Fichiers sensibles
```
filetype:sql "INSERT INTO"
filetype:log "error"
filetype:bak inurl:"backup"
```

### Panels d'administration
```
intitle:"Admin Panel"
inurl:admin.php
intitle:"Dashboard" inurl:admin
```

### Informations techniques
```
site:example.com filetype:xml
site:example.com "server at" intext:"port 80"
```

## 🛡️ Bonnes pratiques
- Utiliser des VPN pour l'anonymat
- Varier les requêtes pour éviter la détection
- Documenter tous les résultats
- Respecter les termes d'utilisation

## 📚 Ressources
- Google Hacking Database (GHDB)
- Exploit-DB Google Dorks
- Outils automatisés (Pagodo, etc.) 