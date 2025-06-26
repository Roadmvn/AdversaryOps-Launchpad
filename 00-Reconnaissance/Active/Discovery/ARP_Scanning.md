# ARP Scanning - Découverte de réseau local

## 📋 Vue d'ensemble
Le scanning ARP (Address Resolution Protocol) est une technique de découverte d'hôtes particulièrement efficace sur les réseaux locaux Ethernet.

## 🎯 Objectifs
- Découvrir tous les hôtes actifs sur un LAN
- Contourner les firewalls qui bloquent ICMP/TCP
- Identifier les adresses MAC et constructeurs
- Cartographier l'infrastructure réseau locale

## 🔍 Principe du protocole ARP

### Fonctionnement
```
1. Envoi d'une requête ARP broadcast : "Qui a l'IP X.X.X.X ?"
2. L'hôte concerné répond : "C'est moi, voici ma MAC"
3. Toutes les machines du LAN reçoivent la requête
4. Seule la machine cible répond
```

### Avantages sur ICMP
- Fonctionne même si ICMP est bloqué
- Obligatoire pour la communication Ethernet
- Très difficile à bloquer sans casser le réseau
- Plus rapide sur les LAN

## 🛠️ Techniques et outils

### Nmap ARP Scan
```bash
# Scan ARP basique
sudo nmap -sn -PR 192.168.1.0/24

# ARP scan avec informations détaillées
sudo nmap -sn -PR 192.168.1.0/24 --packet-trace

# Combiné avec découverte OS
sudo nmap -sn -PR -O 192.168.1.0/24
```

### Arp-scan (outil spécialisé)
```bash
# Installation
sudo apt install arp-scan

# Scan du réseau local
sudo arp-scan -l

# Scan d'une plage spécifique
sudo arp-scan 192.168.1.0/24

# Avec informations constructeur
sudo arp-scan -l -v
```

### Netdiscover
```bash
# Installation
sudo apt install netdiscover

# Mode passif (écoute)
sudo netdiscover -p -i eth0

# Mode actif
sudo netdiscover -r 192.168.1.0/24
```

## 🔬 Techniques avancées

### ARP Scan manuel avec Scapy
```python
#!/usr/bin/env python3
from scapy.all import *
import sys

def arp_scan(network):
    # Créer une requête ARP
    arp_request = ARP(pdst=network)
    broadcast = Ether(dst="ff:ff:ff:ff:ff:ff")
    arp_request_broadcast = broadcast / arp_request
    
    # Envoyer et recevoir
    answered_list = srp(arp_request_broadcast, timeout=2, verbose=False)[0]
    
    clients = []
    for element in answered_list:
        client_dict = {"ip": element[1].psrc, "mac": element[1].hwsrc}
        clients.append(client_dict)
    
    return clients

# Utilisation
results = arp_scan("192.168.1.0/24")
for client in results:
    print(f"IP: {client['ip']} - MAC: {client['mac']}")
```

### Script bash personnalisé
```bash
#!/bin/bash
# ARP scan simple

network="192.168.1"
for i in {1..254}; do
    ip="${network}.${i}"
    arping -c 1 -W 1 $ip 2>/dev/null | grep "reply" && echo "$ip is up"
done
```

## 📊 Analyse des résultats

### Informations extraites
```bash
# Exemple de sortie arp-scan
192.168.1.1     00:1b:2f:12:34:56    Cisco Systems, Inc.
192.168.1.10    08:00:27:ab:cd:ef    PCS Systemtechnik GmbH
192.168.1.20    00:0c:29:12:34:56    VMware, Inc.
```

### Identification des constructeurs
- **00:0c:29** : VMware
- **08:00:27** : VirtualBox
- **00:1b:2f** : Cisco
- **00:50:56** : VMware ESX

### Table ARP système
```bash
# Visualiser la table ARP locale
arp -a

# Nettoyer la table ARP
sudo arp -d -a

# Ajouter une entrée statique
sudo arp -s 192.168.1.1 00:11:22:33:44:55
```

## ⚡ Optimisation et performance

### Paramètres de timing
```bash
# Arp-scan rapide
sudo arp-scan -t 500 192.168.1.0/24

# Nmap avec timing agressif
sudo nmap -sn -PR -T4 192.168.1.0/24

# Plusieurs interfaces simultanément
for iface in eth0 wlan0; do
    sudo arp-scan -I $iface -l &
done
```

### Scripts parallèles
```bash
#!/bin/bash
# ARP scan parallèle par chunks
network="192.168.1"

for chunk in {0..15}; do
    start=$((chunk * 16 + 1))
    end=$((start + 15))
    
    (for i in $(seq $start $end); do
        arping -c 1 -W 1 "${network}.${i}" 2>/dev/null | grep "reply" && echo "${network}.${i}"
    done) &
done
wait
```

## 🔐 Détection et défense

### Détection d'ARP Scan
```bash
# Monitoring des requêtes ARP suspectes
sudo tcpdump -i eth0 arp | grep "who-has"

# Analyse avec tshark
tshark -i eth0 -f "arp" -T fields -e arp.opcode -e arp.src.hw_mac -e arp.src.proto_ipv4
```

### Protection contre ARP Scanning
- **Static ARP entries** : Entries ARP statiques
- **ARP inspection** : Inspection DHCP + ARP
- **Network segmentation** : Segmentation réseau
- **Monitoring** : Surveillance des requêtes anormales

## 🚨 Considérations éthiques et légales

### Limitations
- Fonctionne seulement sur le réseau local
- Peut être détecté par les systèmes de monitoring
- Génère du trafic broadcast

### Bonnes pratiques
- Autorisation préalable requise
- Documentation de toutes les activités
- Respect des fenêtres de maintenance
- Utilisation de comptes dédiés aux tests

## 📋 Méthodologie recommandée

1. **Préparation**
   - Identifier l'interface réseau active
   - Vérifier la connectivité de base
   - Noter la plage IP du réseau local

2. **Scan ARP initial**
   - Utiliser arp-scan ou nmap -sn -PR
   - Documenter tous les hôtes découverts
   - Noter les adresses MAC et constructeurs

3. **Validation des résultats**
   - Corréler avec la table ARP système
   - Vérifier avec d'autres techniques
   - Identifier les assets critiques

4. **Documentation**
   - Cartographier la topologie réseau
   - Identifier les types d'équipements
   - Préparer la phase de scanning de ports

## 📚 Références
- RFC 826 (Address Resolution Protocol)
- Ethernet Frame Format IEEE 802.3
- NMAP Network Discovery Guide 