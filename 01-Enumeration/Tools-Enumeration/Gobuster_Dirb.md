# Gobuster & Dirb - Fuzzing de Répertoires Web

## 🗂️ Workflow de fuzzing de répertoires
1. Reconnaissance initiale (curl, whatweb)
   ↓
2. Fuzzing de répertoires avec wordlist basique
   ↓
3. Fuzzing de fichiers avec extensions spécifiques
   ↓
4. Fuzzing récursif sur les répertoires trouvés
   ↓
5. Fuzzing de paramètres et virtual hosts (si applicable)
   ↓
6. Analyse des résultats et validation manuelle

## 📋 Vue d'ensemble

Gobuster et Dirb sont des outils de fuzzing web qui permettent de découvrir des répertoires et fichiers cachés sur les serveurs web. Gobuster est plus rapide et moderne, Dirb est plus traditionnel avec des fonctionnalités intégrées.

> **💡 Explication Simple** : C'est comme frapper à toutes les portes d'un immeuble pour voir lesquelles s'ouvrent. On teste des milliers de noms de dossiers/fichiers pour trouver ceux qui existent.

## 🚀 Gobuster - L'outil moderne

### Fuzzing de répertoires basique
```bash
# Scan basique avec wordlist commune
gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/common.txt

# Avec extensions de fichiers
gobuster dir -u http://target.com -w /usr/share/seclists/Discovery/Web-Content/common.txt -x php,html,txt,js

# Avec threads et timeout optimisés
gobuster dir -u http://target.com -w wordlist.txt -t 50 --timeout 10s

# Ignorer certains codes de réponse
gobuster dir -u http://target.com -w wordlist.txt -b 404,403
```

### Fuzzing avancé
```bash
# Fuzzing récursif (attention à la charge)
gobuster dir -u http://target.com -w wordlist.txt -r

# Avec authentification HTTP
gobuster dir -u http://target.com -w wordlist.txt -U username -P password

# Avec cookies de session
gobuster dir -u http://target.com -w wordlist.txt -c "SESSIONID=abc123"

# Avec user-agent personnalisé
gobuster dir -u http://target.com -w wordlist.txt -a "Mozilla/5.0 Custom Agent"
```

### Modes spécialisés Gobuster
```bash
# Mode DNS (subdomains)
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Mode VHOST (virtual hosts)
gobuster vhost -u http://target.com -w wordlist.txt

# Mode S3 (buckets AWS)
gobuster s3 -w wordlist.txt
```

## 🏛️ Dirb - L'outil traditionnel

### Utilisation basique
```bash
# Scan avec wordlist par défaut
dirb http://target.com

# Avec wordlist spécifique
dirb http://target.com /usr/share/dirb/wordlists/common.txt

# Avec extensions
dirb http://target.com /usr/share/dirb/wordlists/common.txt -X .php,.html,.txt
```

### Options avancées Dirb
```bash
# Scan récursif
dirb http://target.com /usr/share/dirb/wordlists/common.txt -r

# Avec authentification
dirb http://target.com /usr/share/dirb/wordlists/common.txt -u username:password

# Ignorer certains codes
dirb http://target.com wordlist.txt -N 404

# Avec délai entre requêtes
dirb http://target.com wordlist.txt -z 1000
```

## ⚡ FFuf - L'alternative moderne

### Fuzzing rapide avec FFuf
```bash
# Directory fuzzing
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://target.com/FUZZ

# File fuzzing avec extensions
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://target.com/FUZZ.php

# Parameter fuzzing GET
ffuf -w wordlist.txt -u http://target.com/page?FUZZ=value

# Filtrer par taille de réponse
ffuf -w wordlist.txt -u http://target.com/FUZZ -fs 1234

# Filtrer par nombre de lignes
ffuf -w wordlist.txt -u http://target.com/FUZZ -fl 20
```

## 📚 Wordlists Recommandées

### Wordlists par contexte
```bash
# Wordlists génériques
/usr/share/wordlists/dirb/common.txt                    # Basique, rapide
/usr/share/seclists/Discovery/Web-Content/common.txt    # Plus complète
/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt  # Exhaustive

# Wordlists spécialisées
/usr/share/seclists/Discovery/Web-Content/CMS/           # CMS spécifiques
/usr/share/seclists/Discovery/Web-Content/api/          # Endpoints API
/usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt  # RAFT directories

# Wordlists par technologie
/usr/share/seclists/Discovery/Web-Content/tomcat.txt    # Apache Tomcat
/usr/share/seclists/Discovery/Web-Content/nginx.txt     # Nginx
/usr/share/seclists/Discovery/Web-Content/iis.txt       # Microsoft IIS
```

### Création de wordlists personnalisées
```bash
# Basée sur le contenu du site
cewl http://target.com -m 3 -w custom_wordlist.txt

# Combination de plusieurs wordlists
cat /usr/share/dirb/wordlists/common.txt \
    /usr/share/seclists/Discovery/Web-Content/common.txt | \
    sort -u > combined_wordlist.txt

# Wordlist avec mutations
echo "admin
administrator
backup
config
test" | tee base_words.txt

# Ajouter extensions et variations
for word in $(cat base_words.txt); do
    echo "$word"
    echo "${word}s"
    echo "${word}_old"
    echo "${word}.bak"
done > mutated_wordlist.txt
```

## 🎯 Stratégies de Fuzzing

### Approche progressive
```bash
# 1. Scan rapide initial
gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/common.txt -t 50

# 2. Scan plus exhaustif sur les résultats
gobuster dir -u http://target.com/admin/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt

# 3. Fuzzing de fichiers spécifiques
gobuster dir -u http://target.com -w wordlist.txt -x php,asp,aspx,jsp,html,txt,xml,config,bak
```

### Fuzzing par technologie détectée
```bash
# Pour du PHP
gobuster dir -u http://target.com -w wordlist.txt -x php,php3,php4,php5,phtml,inc

# Pour du ASP.NET
gobuster dir -u http://target.com -w wordlist.txt -x asp,aspx,ashx,asmx

# Pour du Java
gobuster dir -u http://target.com -w wordlist.txt -x jsp,jspx,do,action
```

## 📊 Analyse des Résultats

### Codes de réponse à analyser
```
200 - Contenu trouvé (analyser)
301 - Redirection permanente (suivre)
302 - Redirection temporaire (analyser destination)
403 - Interdit (peut cacher du contenu)
401 - Non autorisé (potentiel bypass)
500 - Erreur serveur (potentielle faille)
```

### Validation manuelle
```bash
# Vérifier les répertoires trouvés
curl -I http://target.com/admin/
curl -s http://target.com/backup/ | head -20

# Recherche de fichiers sensibles dans les répertoires
gobuster dir -u http://target.com/admin/ -w sensitive_files.txt
```

### Extraction automatique
```bash
# Extraire seulement les 200 OK
gobuster dir -u http://target.com -w wordlist.txt | grep "Status: 200"

# Sauvegarder les résultats
gobuster dir -u http://target.com -w wordlist.txt -o scan_results.txt

# Formater les résultats
cat scan_results.txt | grep "Status: 200" | awk '{print $1}' > found_directories.txt
```

## 🛡️ Conseils OPSEC
- Limiter le nombre de threads pour éviter la détection (-t 10 au lieu de 50)
- Utiliser des délais entre requêtes pour les sites de production
- Varier les user-agents pour éviter le blocage
- Ne jamais fuzzer sans autorisation explicite

## ⚠️ Erreurs fréquentes
- Utiliser des wordlists trop petites (manquer du contenu)
- Ne pas tester les extensions de fichiers appropriées
- Ignorer les codes 403 (peuvent être bypassés)
- Lancer des scans trop agressifs qui saturent le serveur
- Oublier de valider manuellement les résultats trouvés

## 💡 Astuces
- Combiner plusieurs outils (gobuster + dirb + ffuf) pour plus de couverture
- Créer des wordlists personnalisées basées sur le contenu du site (cewl)
- Utiliser le fuzzing récursif avec prudence (peut exploser exponentiellement)
- Toujours commencer par une wordlist rapide avant les scans exhaustifs
- Sauvegarder tous les résultats pour analyse offline

## 🔗 Pour aller plus loin
- [Gobuster GitHub](https://github.com/OJ/gobuster)
- [FFuf](https://github.com/ffuf/ffuf)
- [SecLists Wordlists](https://github.com/danielmiessler/SecLists)
- [PayloadsAllTheThings - Web](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/Web%20Methodology)
- [HackTricks - Web](https://book.hacktricks.xyz/pentesting-web)

## 🧭 Navigation
- [Guide HTTP/HTTPS Enumeration](../Services-Réseau/HTTP_HTTPS_Enumeration.md)
- [Guide général d'énumération](../Guide-Enumeration-Pentest.md)
- [Outils d'exploitation](../../02-Exploitation/Tools-Exploitation/) 