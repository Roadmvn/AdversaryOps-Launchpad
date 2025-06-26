# HTTP/HTTPS Enumeration

## 🌐 Vue d'ensemble

L'énumération HTTP/HTTPS est **cruciale** car les applications web sont souvent le **point d'entrée le plus facile** dans un système. C'est comme chercher des fenêtres ouvertes dans une maison - on peut souvent rentrer par là même si la porte principale est verrouillée.

> **💡 Explication Simple** : Quand on trouve un serveur web, on doit découvrir quelles pages existent, quelles technologies sont utilisées, et quelles vulnérabilités peuvent être exploitées.

## ⚠️ **IMPORTANT - À propos des exemples**

> **🎯 TOUS les domaines, IPs, répertoires dans ce guide sont des EXEMPLES FICTIFS !**

### 📝 Exemples vs Réalité :

**Dans le guide (EXEMPLE FICTIF) :**
```
target.com
/admin/, /config/, /backup/
Apache/2.4.7, PHP/5.6.40
PHPSESSID=abc123
```

**Dans la vraie vie, vous devez adapter :**
```
entreprise-cible.com
/wp-admin/, /phpmyadmin/, /manager/
Apache/2.4.52, PHP/8.1.2
JSESSIONID=xyz789
```

### 🔄 Comment adapter les exemples :
1. **Remplacez** `target.com` par votre vraie cible
2. **Remplacez** `/admin/` par les vrais répertoires trouvés
3. **Remplacez** les versions par celles réellement détectées
4. **Adaptez** les commandes selon votre environnement
5. **Modifiez** les wordlists selon le contexte

## 🎯 Objectifs de l'Énumération Web

1. **Identifier les technologies** - Serveur, langage, framework, CMS
2. **Découvrir les répertoires cachés** - /admin, /config, /backup
3. **Trouver les fichiers sensibles** - config.php, .env, database.sql
4. **Identifier les paramètres** - Variables GET/POST exploitables
5. **Détecter les vulnérabilités** - Failles de sécurité communes

## 🔍 Phase 1 : Reconnaissance Initiale

### Banner Grabbing et Headers
```bash
# 1. Headers HTTP basiques
curl -I http://target.com
# Rechercher : Server, X-Powered-By, Set-Cookie

# 2. Headers détaillés avec verbose
curl -v http://target.com

# 3. Avec wget pour plus d'infos
wget --server-response --spider http://target.com

# 4. Nmap pour headers
nmap --script http-headers target.com -p 80
```

**Que chercher dans les headers ?**
- `Server: Apache/2.4.7` → Version du serveur web
- `X-Powered-By: PHP/5.6.40` → Version du langage
- `Set-Cookie: PHPSESSID=...` → Technologie de session
- `X-Generator: WordPress 5.8` → CMS utilisé

### Identification des Technologies
```bash
# 1. Whatweb - Identification automatique
whatweb http://target.com

# 2. Wappalyzer en ligne de commande
wappalyzer http://target.com

# 3. Nmap scripts pour techno
nmap --script http-generator,http-server-header target.com -p 80

# 4. Manuel avec curl
curl -s http://target.com | grep -i "generator\|powered\|version"
```

## 🗂️ Phase 2 : Directory Fuzzing

### Gobuster (Recommandé - Plus rapide)
```bash
# 1. Scan basique
gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/common.txt

# 2. Avec extensions spécifiques
gobuster dir -u http://target.com \
    -w /usr/share/seclists/Discovery/Web-Content/common.txt \
    -x php,html,txt,js,css,xml,config,bak

# 3. Scan récursif (attention à la charge)
gobuster dir -u http://target.com -w wordlist.txt -r

# 4. Avec threads et timeout
gobuster dir -u http://target.com -w wordlist.txt -t 50 --timeout 10s

# 5. Ignorer certains codes de réponse
gobuster dir -u http://target.com -w wordlist.txt -b 404,403
```

### Dirb (Plus de fonctionnalités intégrées)
```bash
# 1. Scan basique avec wordlist
dirb http://target.com

# 2. Avec extensions
dirb http://target.com /usr/share/dirb/wordlists/common.txt -X .php,.html,.txt

# 3. Scan récursif
dirb http://target.com /usr/share/dirb/wordlists/common.txt -r

# 4. Avec authentification si nécessaire
dirb http://target.com /usr/share/dirb/wordlists/common.txt -u username:password
```

### Fuzzing avec FFuf (Moderne et Rapide)
```bash
# 1. Directory fuzzing
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt \
     -u http://target.com/FUZZ

# 2. File fuzzing avec extensions
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt \
     -u http://target.com/FUZZ.php

# 3. Parameter fuzzing GET
ffuf -w wordlist.txt -u http://target.com/page?FUZZ=value

# 4. Filtrer par taille de réponse
ffuf -w wordlist.txt -u http://target.com/FUZZ -fs 1234
```

## 🕵️ Phase 3 : Analyse Manuelle

### Exploration du Contenu
```bash
# 1. Page d'accueil - source code
curl -s http://target.com | html2text | less

# 2. Recherche de commentaires dans le source
curl -s http://target.com | grep -E "<!--.*-->"

# 3. Recherche de mots-clés sensibles
curl -s http://target.com | grep -i "password\|user\|admin\|config\|database"

# 4. Robots.txt toujours vérifier !
curl http://target.com/robots.txt

# 5. Sitemap.xml pour structure
curl http://target.com/sitemap.xml
```

### Tests de Fichiers Sensibles
```bash
# Fichiers de configuration communs
curl -I http://target.com/config.php
curl -I http://target.com/.env
curl -I http://target.com/wp-config.php
curl -I http://target.com/database.sql
curl -I http://target.com/backup.zip

# Répertoires sensibles
curl -I http://target.com/admin/
curl -I http://target.com/administrator/
curl -I http://target.com/panel/
curl -I http://target.com/phpmyadmin/
curl -I http://target.com/.git/
```

## ⚒️ Phase 4 : Enumération avec Outils

### Nikto - Scanner de Vulnérabilités Web
```bash
# 1. Scan basique
nikto -h http://target.com

# 2. Scan avec output formaté
nikto -h http://target.com -Format htm -output nikto_results.html

# 3. Scan de port spécifique
nikto -h http://target.com -port 8080

# 4. Tests spécific
nikto -h http://target.com -Tuning x  # x = type de test (1-9)
```

**Que trouve Nikto ?**
- Serveur web mal configuré
- Fichiers/scripts dangereux
- Versions obsolètes
- Headers de sécurité manquants

### Nmap Scripts HTTP
```bash
# 1. Énumération générale HTTP
nmap --script http-enum target.com -p 80

# 2. Informations sur les méthodes HTTP
nmap --script http-methods target.com -p 80

# 3. Check for common vulnerabilities
nmap --script http-vuln-* target.com -p 80

# 4. HTTP headers analysis
nmap --script http-security-headers target.com -p 80

# 5. Waf detection
nmap --script http-waf-detect target.com -p 80
```

### Metasploit pour Énumération Web
```bash
msfconsole

# 1. Directory scanner
use auxiliary/scanner/http/dir_scanner
set RHOSTS target.com
set DICTIONARY /usr/share/metasploit-framework/data/wordlists/directory.txt
run

# 2. HTTP version scanner
use auxiliary/scanner/http/http_version
set RHOSTS target.com
run

# 3. Tomcat manager scanner
use auxiliary/scanner/http/tomcat_mgr_login
set RHOSTS target.com
run

# 4. WordPress scanner
use auxiliary/scanner/http/wordpress_scanner
set RHOSTS target.com
run

# 5. HTTP title scanner
use auxiliary/scanner/http/title
set RHOSTS target.com
run
```

## 🎯 Énumération CMS Spécifique

### WordPress
```bash
# 1. WPScan - L'outil dédié
wpscan --url http://target.com

# 2. Énumération des utilisateurs
wpscan --url http://target.com --enumerate u

# 3. Énumération des plugins/thèmes
wpscan --url http://target.com --enumerate p,t

# 4. Scan de vulnérabilités
wpscan --url http://target.com --enumerate vp,vt

# 5. Avec authentification
wpscan --url http://target.com --username admin --passwords passwords.txt
```

### Drupal
```bash
# 1. Droopescan
droopescan scan drupal -u http://target.com

# 2. Manuel - Fichiers spécifiques Drupal
curl http://target.com/CHANGELOG.txt
curl http://target.com/README.txt
curl http://target.com/sites/default/settings.php
```

### Joomla
```bash
# 1. JoomScan
joomscan -u http://target.com

# 2. Manuel - Configuration Joomla
curl http://target.com/configuration.php
curl http://target.com/administrator/
```

## 🔍 Techniques Avancées

### Parameter Discovery
```bash
# 1. Avec Arjun (parameter discovery)
arjun -u http://target.com/page.php

# 2. Avec FFuf pour paramètres GET
ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt \
     -u http://target.com/page.php?FUZZ=test

# 3. Test manuel de paramètres communs
curl "http://target.com/page.php?id=1"
curl "http://target.com/page.php?user=admin"
curl "http://target.com/page.php?file=index.php"
```

### Subdomain Enumeration (si applicable)
```bash
# 1. Avec Gobuster en mode DNS
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# 2. Avec FFuf pour subdomains
ffuf -w wordlist.txt -u http://FUZZ.target.com

# 3. Avec Amass
amass enum -d target.com
```

### HTTP Methods Testing
```bash
# 1. Tester les méthodes HTTP supportées
curl -X OPTIONS http://target.com -v

# 2. Test des méthodes dangereuses
curl -X PUT http://target.com/test.txt -d "test content"
curl -X DELETE http://target.com/test.txt
curl -X TRACE http://target.com

# 3. Avec Nmap
nmap --script http-methods target.com -p 80
```

## 📋 Checklist d'Énumération HTTP

### ✅ Étapes Obligatoires :
1. **Banner Grabbing** - Version serveur/technos
2. **Robots.txt & Sitemap** - Structure révélée  
3. **Directory Fuzzing** - Répertoires cachés
4. **File Fuzzing** - Fichiers sensibles
5. **Technology Stack** - Framework/CMS/Version
6. **Error Pages** - Informations dans erreurs
7. **HTTP Methods** - Méthodes dangereuses autorisées
8. **Security Headers** - Headers de sécurité manquants

### ✅ Tests Spécialisés :
- **CMS Enumeration** si WordPress/Drupal/Joomla détecté
- **Parameter Discovery** sur pages dynamiques
- **Backup Files** (.bak, .backup, .old, ~)
- **Source Code** (.git/, .svn/, source.zip)
- **Database Files** (.sql, .db, database.txt)

## 🛡️ Considérations de Sécurité

### Rate Limiting et Stealth
```bash
# Limitation du nombre de threads
gobuster dir -u http://target.com -w wordlist.txt -t 10 --delay 100ms

# User-Agent randomization
curl -H "User-Agent: Mozilla/5.0 (compatible; bot)" http://target.com

# Proxy usage
gobuster dir -u http://target.com -w wordlist.txt --proxy http://127.0.0.1:8080
```

### WAF Detection et Bypass
```bash
# 1. Détection de WAF
wafw00f http://target.com

# 2. Avec Nmap
nmap --script http-waf-detect target.com -p 80

# 3. Tests de bypass basiques
curl -H "X-Forwarded-For: 127.0.0.1" http://target.com
curl -H "X-Real-IP: 127.0.0.1" http://target.com
```

## 📝 Documentation et Reporting

### Automatisation des Résultats
```bash
#!/bin/bash
TARGET=$1
mkdir ${TARGET}_web_enum

# Headers et technos
curl -I http://$TARGET > ${TARGET}_web_enum/headers.txt
whatweb http://$TARGET > ${TARGET}_web_enum/technologies.txt

# Directory fuzzing
gobuster dir -u http://$TARGET \
    -w /usr/share/seclists/Discovery/Web-Content/common.txt \
    -x php,html,txt > ${TARGET}_web_enum/directories.txt

# Nikto scan
nikto -h http://$TARGET -output ${TARGET}_web_enum/nikto.txt

# Nmap HTTP scripts
nmap --script http-enum,http-headers,http-methods http://$TARGET \
    > ${TARGET}_web_enum/nmap_http.txt

echo "Web enumeration completed for $TARGET"
echo "Results saved in ${TARGET}_web_enum/"
```

---
*Cette section couvre l'énumération HTTP/HTTPS complète. Pour l'exploitation des vulnérabilités trouvées, voir la section 02-Exploitation/Web/* 