# Nmap - Scanner Réseau Ultime

## 🌐 Vue d'ensemble

**Nmap** (Network Mapper) est **LE** scanner réseau de référence en cybersécurité. C'est l'outil le plus utilisé pour la découverte réseau et l'audit de sécurité.

> **💡 Analogie Simple** : Nmap c'est comme un radar ultra-puissant qui scanne tout ce qui bouge sur le réseau - machines, services, versions, OS.

## ⚠️ **IMPORTANT - À propos des exemples**

> **🎯 TOUTES les IPs, domaines, ports dans ce guide sont FICTIFS !**

### 📝 Exemples vs Réalité :

**Dans le guide (EXEMPLE FICTIF) :**
```
nmap 192.168.1.100
nmap exemple.local
Host 10.10.10.50 appears to be up
```

**Dans la vraie vie, vous devez adapter :**
```
nmap [IP-CIBLE-RÉELLE]
nmap [DOMAINE-CIBLE-RÉEL]
Host [IP-DÉCOUVERTE] appears to be up
```

## 🎯 Objectifs d'apprentissage

Après cette section, vous saurez :
1. **Découverte d'hôtes** - Qui est en ligne sur le réseau
2. **Scan de ports** - Quels services tournent où
3. **Détection de versions** - Identifier les logiciels et versions
4. **Détection d'OS** - Reconnaître les systèmes d'exploitation
5. **Scripts NSE** - Automatiser les tests de vulnérabilités

## 🚀 Installation et Concepts de Base

### **Installation**
```bash
# Debian/Ubuntu
sudo apt update && sudo apt install nmap

# Red Hat/CentOS
sudo yum install nmap

# Kali Linux (pré-installé)
nmap --version

# Vérification installation
nmap --version
Nmap version 7.94 ( https://nmap.org )
```

### **Syntaxe générale**
```bash
nmap [Scan Type] [Options] {target specification}

# Exemples de base
nmap 192.168.1.1                    # Scan simple
nmap 192.168.1.1-254               # Plage d'IPs
nmap 192.168.1.0/24                # Notation CIDR
nmap exemple.com                    # Nom de domaine
```

## 🔍 Découverte d'Hôtes (Host Discovery)

### **Techniques de découverte**

#### **Ping Scan (-sn)**
> **💭 Principe :** Découvrir quelles machines sont en ligne sans scanner les ports.

```bash
# Ping scan d'un réseau
nmap -sn 192.168.1.0/24

# Résultat typique
Nmap scan report for 192.168.1.1
Host is up (0.0012s latency).
Nmap scan report for 192.168.1.10  
Host is up (0.0089s latency).
Nmap scan report for 192.168.1.50
Host is up (0.0156s latency).
```

#### **Types de pings disponibles**
```bash
# ICMP Echo Request (ping classique)
nmap -sn -PE 192.168.1.0/24

# TCP SYN Ping sur port 80
nmap -sn -PS80 192.168.1.0/24

# TCP ACK Ping sur port 443  
nmap -sn -PA443 192.168.1.0/24

# UDP Ping
nmap -sn -PU53 192.168.1.0/24

# Combinaison de techniques
nmap -sn -PE -PS22,80,443 -PA80,443 192.168.1.0/24
```

### **Éviter la détection lors de la découverte**
```bash
# Désactiver le ping (traiter toutes IPs comme up)
nmap -Pn 192.168.1.1

# Utiliser des ports inattendus
nmap -sn -PS8080,8443,9000 192.168.1.0/24

# Source IP spoofing (nécessite privilèges)
nmap -sn -S 192.168.1.5 192.168.1.100
```

## 🚪 Scan de Ports

### **Types de scans principaux**

#### **TCP SYN Scan (-sS) - DEFAULT**
> **💭 Principe :** Envoie SYN, écoute SYN/ACK (ouvert) ou RST (fermé). Stealth car ne complète pas la connexion.

```bash
# Scan SYN (par défaut en root)
nmap -sS 192.168.1.100

# Résultat typique
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
```

#### **TCP Connect Scan (-sT)**
> **💭 Principe :** Connexion TCP complète. Plus lent mais fonctionne sans privilèges root.

```bash
# TCP Connect (utilisé automatiquement sans root)
nmap -sT 192.168.1.100

# Force TCP Connect même en root
nmap -sT 192.168.1.100
```

#### **UDP Scan (-sU)**
> **💭 Principe :** Scan des services UDP (DNS, SNMP, DHCP). Plus lent car UDP est sans connexion.

```bash
# Scan UDP des ports courants
nmap -sU 192.168.1.100

# UDP sur ports spécifiques
nmap -sU -p 53,161,514 192.168.1.100

# Combinaison TCP + UDP
nmap -sS -sU -p T:80,443,U:53,161 192.168.1.100
```

#### **Scans Stealth Avancés**

**TCP FIN Scan (-sF)**
```bash
# Envoie packets FIN - contourne certains firewalls
nmap -sF 192.168.1.100
```

**TCP NULL Scan (-sN)**  
```bash
# Envoie packets sans flags - très furtif
nmap -sN 192.168.1.100
```

**TCP Xmas Scan (-sX)**
```bash
# Envoie FIN+PSH+URG - "sapin de Noël"
nmap -sX 192.168.1.100
```

### **Sélection de ports**

#### **Plages et listes de ports**
```bash
# Port spécifique
nmap -p 80 192.168.1.100

# Plage de ports
nmap -p 1-1000 192.168.1.100

# Ports spécifiques
nmap -p 22,80,443,3389 192.168.1.100

# Tous les ports TCP (1-65535)
nmap -p- 192.168.1.100

# Top 1000 ports (défaut)
nmap --top-ports 1000 192.168.1.100

# Top 100 ports (rapide)
nmap --top-ports 100 192.168.1.100
```

#### **Ports par protocole**
```bash
# TCP seulement
nmap -p T:1-1000 192.168.1.100

# UDP seulement  
nmap -p U:1-1000 192.168.1.100

# Mélange TCP/UDP
nmap -p T:80,443,U:53,161 192.168.1.100
```

## 🔬 Détection de Services et Versions

### **Banner Grabbing et Version Detection**

#### **Service Detection (-sV)**
> **💭 Principe :** Interroge les services pour déterminer nom, version, info supplémentaires.

```bash
# Détection de versions basique
nmap -sV 192.168.1.100

# Résultat détaillé
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3
80/tcp   open  http    Apache httpd 2.4.41
443/tcp  open  ssl/http Apache httpd 2.4.41
3306/tcp open  mysql   MySQL 8.0.25-0ubuntu0.20.04.1
```

#### **Intensité de la détection**
```bash
# Détection légère (rapide)  
nmap -sV --version-intensity 0 192.168.1.100

# Détection intense (exhaustive)
nmap -sV --version-intensity 9 192.168.1.100

# Détection agressive de versions
nmap -sV --version-all 192.168.1.100
```

### **Détection d'OS (-O)**

#### **OS Fingerprinting**
```bash
# Détection OS basique
nmap -O 192.168.1.100

# Résultat typique
Device type: general purpose
Running: Linux 3.X|4.X  
OS CPE: cpe:/o:linux:linux_kernel:3
OS details: Linux 3.2 - 4.9
```

#### **Options avancées OS**
```bash
# Forcer la détection OS
nmap -O --osscan-guess 192.168.1.100

# Détection OS agressive
nmap -O --osscan-limit 192.168.1.100

# Limite aux hôtes avec au moins 1 port ouvert
nmap -O --osscan-limit 192.168.1.100
```

## ⚡ Optimisation de Performance

### **Timing Templates (-T)**

#### **Templates de vitesse**
```bash
# T0 - Paranoid (très lent, furtif)
nmap -T0 192.168.1.100

# T1 - Sneaky (lent, furtif)  
nmap -T1 192.168.1.100

# T2 - Polite (lent, poli)
nmap -T2 192.168.1.100

# T3 - Normal (défaut)
nmap -T3 192.168.1.100

# T4 - Aggressive (rapide)
nmap -T4 192.168.1.100

# T5 - Insane (très rapide, bruyant)
nmap -T5 192.168.1.100
```

### **Contrôle fin du timing**
```bash
# Délai minimum entre paquets (millisecondes)
nmap --scan-delay 1000ms 192.168.1.100

# Nombre de sondes en parallèle
nmap --max-parallelism 10 192.168.1.100

# Timeout par hôte
nmap --host-timeout 30m 192.168.1.100
```

## 🎭 Évasion et Furtivité

### **Fragmentation de paquets**
```bash
# Fragmentation simple
nmap -f 192.168.1.100

# Double fragmentation  
nmap -ff 192.168.1.100

# Taille MTU personnalisée
nmap --mtu 24 192.168.1.100
```

### **Spoofing et decoys**
```bash
# IP source spoofée
nmap -S 192.168.1.5 192.168.1.100

# Decoy scans (mélanger vraie IP avec decoys)
nmap -D 192.168.1.5,192.168.1.6,ME 192.168.1.100

# Random decoys
nmap -D RND:5 192.168.1.100
```

### **Manipulation des ports sources**
```bash
# Port source spécifique (souvent 53 pour DNS)
nmap --source-port 53 192.168.1.100

# Plusieurs tentatives de ports sources
nmap --source-port 53,80,443 192.168.1.100
```

## 🔧 Scripts NSE (Nmap Scripting Engine)

### **Scripts par catégories**

#### **Scripts par défaut (-sC ou --script=default)**
```bash
# Scripts safe et populaires
nmap -sC 192.168.1.100

# Équivalent à :
nmap --script=default 192.168.1.100
```

#### **Scripts de vulnérabilités**
```bash
# Tous les scripts de vulns
nmap --script=vuln 192.168.1.100

# Vulnérabilités spécifiques
nmap --script=ssl-* 192.168.1.100
nmap --script=smb-vuln-* 192.168.1.100
nmap --script=http-vuln-* 192.168.1.100
```

#### **Scripts d'énumération**
```bash
# Énumération générale
nmap --script=discovery 192.168.1.100

# Énumération SMB
nmap --script=smb-enum-* 192.168.1.100

# Énumération DNS  
nmap --script=dns-* 192.168.1.100
```

### **Scripts populaires par service**

#### **HTTP/HTTPS**
```bash
# Détection de technologies web
nmap --script=http-enum 192.168.1.100

# Directories et fichiers cachés
nmap --script=http-brute-dirs 192.168.1.100

# Headers HTTP
nmap --script=http-headers 192.168.1.100

# Vulnérabilités SSL/TLS
nmap --script=ssl-enum-ciphers 192.168.1.100
```

#### **SMB (Windows)**
```bash
# Énumération SMB complète
nmap --script=smb-enum-shares,smb-enum-users 192.168.1.100

# Vulnérabilités SMB (EternalBlue, etc.)
nmap --script=smb-vuln-* 192.168.1.100

# OS et version NetBIOS
nmap --script=smb-os-discovery 192.168.1.100
```

#### **SSH**
```bash
# Algorithmes et versions SSH
nmap --script=ssh2-enum-algos 192.168.1.100

# Clés d'hôte SSH
nmap --script=ssh-hostkey 192.168.1.100

# Brute force SSH (attention !)
nmap --script=ssh-brute 192.168.1.100
```

## 📊 Output et Reporting

### **Formats de sortie**

#### **Formats standards**
```bash
# Output normal (défaut)
nmap 192.168.1.100

# Output XML (parsable)
nmap -oX scan.xml 192.168.1.100

# Output greppable  
nmap -oG scan.grep 192.168.1.100

# Tous les formats
nmap -oA scan_complet 192.168.1.100
```

#### **Verbosité**
```bash
# Verbeux (voir progression)
nmap -v 192.168.1.100

# Très verbeux (détails complets)
nmap -vv 192.168.1.100

# Debug (pour troubleshooting)
nmap -d 192.168.1.100
```

## 🎯 Scans Pré-configurés Utiles

### **Reconnaissance initiale**
```bash
# Scan rapide des hôtes up
nmap -sn 192.168.1.0/24

# Top 1000 ports avec détection de service
nmap -sS -sV --top-ports 1000 192.168.1.100

# Scan complet mais rapide
nmap -T4 -A -v 192.168.1.100
```

### **Scans stealth**
```bash
# Ultra-furtif (lent)
nmap -sS -T1 -f --data-length 200 192.168.1.100

# Fragmentation avec decoys
nmap -sS -f -D RND:10 192.168.1.100

# Scan NULL très discret
nmap -sN -T0 192.168.1.100
```

### **Scans de vulnérabilités**
```bash
# Détection vulns communes
nmap -sS -sV --script=vuln 192.168.1.100

# Focus SMB/NetBIOS
nmap -sS -sU -p 137,138,139,445 --script=smb-vuln-* 192.168.1.100

# Focus web
nmap -sS -p 80,443,8080,8443 --script=http-vuln-* 192.168.1.100
```

## ⚠️ Considérations Légales et Éthiques

### **Règles d'usage**
```bash
# ✅ AUTORISÉ :
• Vos propres machines/réseaux
• Environnements de test (VulnHub, HackTheBox)
• Avec autorisation écrite explicite

# ❌ INTERDIT :
• Réseaux tiers sans permission
• Infrastructure critique (hôpitaux, aéroports)
• Scans agressifs en production
```

### **Bonnes pratiques**
```bash
# Commencer léger
nmap -sn target                    # Juste ping
nmap -T2 --top-ports 100 target   # Scan poli

# Progresser graduellement  
nmap -sS -sV target               # Avec détection
nmap -sS -sV -A target            # Scan agressif

# Éviter de casser
nmap -T1 --scan-delay 5s target   # Lent et sûr
```

## 📋 Cheat Sheet Nmap

### **Commandes essentielles**
```bash
# DÉCOUVERTE RÉSEAU
nmap -sn 192.168.1.0/24           # Ping sweep
nmap -sS --top-ports 1000 IP      # Top ports rapide

# SCAN COMPLET  
nmap -sS -sV -O -A IP             # Tout-en-un
nmap -p- -sV IP                   # Tous les ports

# STEALTH
nmap -sS -f -T1 IP                # Furtif
nmap -sN --scan-delay 10s IP      # Ultra-discret

# VULNÉRABILITÉS
nmap --script=vuln IP             # Scripts de vulns
nmap --script=safe IP             # Scripts sûrs

# SERVICES SPÉCIFIQUES
nmap -p 80,443 --script=http-* IP # Web
nmap -p 445 --script=smb-* IP     # SMB
```

### **Scénarios d'usage courants**
```bash
# PENTEST EXTERNE
nmap -sS --top-ports 1000 -T4 -oA external_scan target.com

# PENTEST INTERNE  
nmap -sn 10.0.0.0/8              # Découverte hôtes
nmap -sS -sV -p- -T4 targets     # Scan exhaustif

# BUG BOUNTY
nmap -sS --top-ports 100 -T3 targets.txt

# AUDIT RÉSEAU
nmap -sU --top-ports 20 -sV network/24  # Services UDP
```

---
*Nmap est l'outil de reconnaissance réseau le plus puissant au monde. Maîtrisez-le et vous maîtrisez la première phase de tout pentest !* 