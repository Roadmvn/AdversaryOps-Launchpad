# 06-Wireless-Security

## 📡 Vue d'ensemble

Cette section couvre les techniques d'évaluation de sécurité des technologies sans fil, incluant WiFi, Bluetooth, RFID/NFC et radiofréquences. Les attaques wireless représentent un vecteur d'entrée majeur dans les environnements modernes.

## 🗂️ Structure

### WiFi/
- **WPA_WPA2_Cracking.md** - Techniques de crack des réseaux WiFi
- **WPS_Attacks.md** - Exploitation des vulnérabilités WPS
- **Evil_Twin.md** - Création de points d'accès malveillants
- **Capture_Handshake.md** - Capture et analyse des handshakes
- **WiFi_Recon.md** - Reconnaissance des réseaux sans fil

### Bluetooth/
- **Bluetooth_Scanning.md** - Découverte des dispositifs Bluetooth
- **Bluejacking_Bluesnarfing.md** - Attaques Bluetooth classiques
- **BLE_Attacks.md** - Exploitation Bluetooth Low Energy

### RFID-NFC/
- **RFID_Cloning.md** - Clonage de cartes RFID
- **NFC_Attacks.md** - Exploitation des communications NFC
- **Badge_Analysis.md** - Analyse des badges d'accès

### Radio-Frequencies/
- **SDR_Basics.md** - Introduction aux SDR (Software Defined Radio)
- **Signal_Analysis.md** - Analyse des signaux radio
- **Jamming_Techniques.md** - Techniques de brouillage

### _Tools/
- **Aircrack-Suite.md** - Suite complète pour WiFi
- **Kismet.md** - Détecteur de réseaux sans fil
- **Wireshark_Wireless.md** - Analyse de trafic wireless
- **HackRF_Usage.md** - Utilisation du HackRF One
- **Flipper_Zero.md** - Guide d'utilisation Flipper Zero

## 🎯 Méthodologie Wireless

### Phase 1 : Reconnaissance
```bash
# Découverte des réseaux WiFi
sudo airodump-ng wlan0mon

# Scan Bluetooth
hcitool scan
bluetoothctl scan on

# Analyse RFID/NFC
proxmark3 hw tune
```

### Phase 2 : Analyse
- Identification des protocoles utilisés
- Évaluation de la force de chiffrement
- Recherche de vulnérabilités connues

### Phase 3 : Exploitation
- Attaques par dictionnaire/bruteforce
- Exploitation de vulnérabilités protocolaires
- Attaques man-in-the-middle

### Phase 4 : Post-Exploitation
- Analyse du trafic capturé
- Pivot vers le réseau interne
- Maintien de l'accès

## ⚠️ Considérations Légales

**ATTENTION** : Les tests de sécurité wireless nécessitent des précautions particulières :

- ✅ **Autorisation écrite** obligatoire
- ✅ **Environnement contrôlé** (lab/test)
- ❌ **Jamais sur des réseaux publics**
- ❌ **Respect de la réglementation radiofréquences**

## 🔧 Outils Recommandés

### Hardware
- **Cartes WiFi** : Alfa AWUS036NHA, TP-Link AC600
- **Bluetooth** : Ubertooth One, BladeRF
- **RFID/NFC** : Proxmark3, ChameleonMini
- **SDR** : HackRF One, RTL-SDR, Flipper Zero

### Software
- **Aircrack-ng Suite** - Standard pour WiFi
- **Kismet** - Détection IDS wireless
- **GNU Radio** - Traitement de signal
- **Universal Radio Hacker** - Analyse RF

## 📚 Ressources d'Apprentissage

### Labs Pratiques
- **WiFi Security Lab** - Attaques WPA/WPA2
- **Bluetooth Hacking** - Exploitation BLE
- **RFID/NFC Workshop** - Clonage et analyse

### Formations
- **OSWP** (Offensive Security Wireless Professional)
- **CWSP** (Certified Wireless Security Professional)

---
*Section maintenue par : [Votre nom]*  
*Dernière mise à jour : [Date]* 