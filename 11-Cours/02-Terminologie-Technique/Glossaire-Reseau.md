# Glossaire Réseau - Terminologie Technique

## 📚 Vue d'ensemble

Ce glossaire définit **TOUS les termes réseau** essentiels pour la cybersécurité. Chaque terme est expliqué **simplement** avec des analogies du quotidien.

---

## 🌐 Protocoles de Base

### **TCP (Transmission Control Protocol)**
> **💭 Définition :** Protocole fiable qui garantit la livraison des données dans l'ordre.
> **📮 Analogie :** Courrier recommandé avec accusé de réception - tout arrive dans l'ordre.

**Caractéristiques :** Connection-oriented, fiable, lent

### **UDP (User Datagram Protocol)**
> **💭 Définition :** Protocole rapide mais sans garantie de livraison.
> **📮 Analogie :** Courrier normal - rapide mais peut se perdre.

**Caractéristiques :** Connectionless, non-fiable, rapide

### **IP (Internet Protocol)**
> **💭 Définition :** Protocole qui route les paquets entre réseaux.
> **🗺️ Analogie :** Système postal mondial qui trouve le chemin vers n'importe quelle adresse.

### **HTTP/HTTPS**
> **💭 Définition :** Protocole pour naviguer sur le web.
> **🌐 Analogie :** Langage standard pour demander des pages web, HTTPS = version cryptée.

**Ports :** 80 (HTTP), 443 (HTTPS)

### **DNS (Domain Name System)**
> **💭 Définition :** Service qui traduit les noms en adresses IP.
> **📞 Analogie :** Annuaire téléphonique qui traduit "google.com" en "172.217.22.14".

**Port :** 53

### **SSH (Secure Shell)**
> **💭 Définition :** Protocole pour connexion sécurisée à distance.
> **📞 Analogie :** Téléphone crypté pour contrôler un ordinateur à distance.

**Port :** 22

### **FTP (File Transfer Protocol)**
> **💭 Définition :** Protocole pour transférer des fichiers.
> **📁 Analogie :** Service de livraison spécialisé dans les documents.

**Ports :** 21 (contrôle), 20 (données)

### **SMTP (Simple Mail Transfer Protocol)**
> **💭 Définition :** Protocole pour envoyer des emails.
> **✉️ Analogie :** Bureau de poste qui expédie le courrier.

**Port :** 25

---

## 🏠 Adressage et Réseaux

### **Adresse IP**
> **💭 Définition :** Identifiant unique d'un appareil sur le réseau.
> **🏠 Analogie :** Adresse postale de votre maison sur Internet.

### **IPv4**
> **💭 Définition :** Version 4 du protocole IP (32 bits).
> **�� Analogie :** Code postal à 4 chiffres - limité mais largement utilisé.

**Format :** `192.168.1.1` (4 nombres de 0-255)

### **IPv6**
> **💭 Définition :** Version 6 du protocole IP (128 bits).
> **📍 Analogie :** Code postal très long - beaucoup plus d'adresses possibles.

**Format :** `2001:0db8:85a3:0000:0000:8a2e:0370:7334`

### **Masque de sous-réseau (Subnet Mask)**
> **💭 Définition :** Détermine quelle partie de l'IP identifie le réseau vs l'hôte.
> **🏘️ Analogie :** Code postal qui sépare la ville du numéro de rue.

**Exemple :** `255.255.255.0` ou `/24`

### **CIDR (Classless Inter-Domain Routing)**
> **💭 Définition :** Notation compacte pour les réseaux (IP/masque).
> **📝 Analogie :** Façon courte d'écrire une adresse "Rue des Lilas/Ville".

**Exemple :** `192.168.1.0/24` = réseau avec 254 adresses

### **Passerelle (Gateway)**
> **💭 Définition :** Routeur qui connecte votre réseau local à Internet.
> **🚪 Analogie :** Porte de sortie de votre quartier vers le monde extérieur.

### **DHCP (Dynamic Host Configuration Protocol)**
> **💭 Définition :** Service qui distribue automatiquement les adresses IP.
> **🏠 Analogie :** Service qui attribue automatiquement les numéros de maison dans un nouveau quartier.

**Port :** 67/68

---

## 🔌 Couches Réseau (Modèle OSI)

### **Couche 1 - Physique**
> **💭 Définition :** Transmission des bits sur le support physique.
> **⚡ Analogie :** Les câbles, ondes radio, signaux électriques.

### **Couche 2 - Liaison (Data Link)**
> **💭 Définition :** Communication entre équipements directement connectés.
> **🔗 Analogie :** Conversation directe entre voisins immédiats.

**Exemples :** Ethernet, WiFi, MAC addresses

### **Couche 3 - Réseau**
> **💭 Définition :** Routage des paquets entre réseaux.
> **🗺️ Analogie :** GPS qui trouve le chemin entre deux villes.

**Protocole :** IP

### **Couche 4 - Transport**
> **💭 Définition :** Livraison fiable des données de bout en bout.
> **📦 Analogie :** Service de livraison qui garantit l'arrivée du colis.

**Protocoles :** TCP, UDP

### **Couche 7 - Application**
> **💭 Définition :** Interface avec les applications utilisateur.
> **📱 Analogie :** L'app que vous utilisez (navigateur, email, etc.).

**Protocoles :** HTTP, SMTP, FTP, SSH

---

## 🔐 Sécurité Réseau

### **Firewall**
> **💭 Définition :** Système qui filtre le trafic réseau selon des règles.
> **🚧 Analogie :** Vigile à l'entrée qui vérifie les autorisations.

### **NAT (Network Address Translation)**
> **💭 Définition :** Traduction d'adresses IP privées en publique.
> **🏢 Analogie :** Standard téléphonique d'entreprise - une ligne publique, plusieurs postes internes.

### **VPN (Virtual Private Network)**
> **💭 Définition :** Tunnel sécurisé à travers Internet.
> **🚇 Analogie :** Métro souterrain sécurisé pour voyager en privé.

### **SSL/TLS**
> **💭 Définition :** Chiffrement des communications web.
> **🔒 Analogie :** Enveloppe scellée pour protéger vos lettres.

### **MAC Address**
> **💭 Définition :** Identifiant unique de la carte réseau.
> **🆔 Analogie :** Numéro de série de votre carte d'identité réseau.

**Format :** `00:1B:44:11:3A:B7` (6 octets en hexadécimal)

---

## 📊 Outils et Monitoring

### **Ping**
> **💭 Définition :** Test de connectivité réseau.
> **📞 Analogie :** "Allô, tu m'entends ?" pour vérifier qu'on peut joindre quelqu'un.

### **Traceroute**
> **💭 Définition :** Trace le chemin des paquets vers une destination.
> **🗺️ Analogie :** GPS qui montre tous les ronds-points traversés pour arriver.

### **Netstat**
> **💭 Définition :** Affiche les connexions réseau actives.
> **👁️ Analogie :** Caméra de surveillance qui montre qui entre et sort.

### **nmap**
> **💭 Définition :** Scanner de ports et de services réseau.
> **🔍 Analogie :** Jumelles qui regardent quelles fenêtres sont ouvertes dans les maisons.

### **Wireshark**
> **💭 Définition :** Analyseur de trafic réseau (packet sniffer).
> **🎧 Analogie :** Micro qui écoute toutes les conversations sur le réseau.

---

## 🏢 Réseaux d'Entreprise

### **VLAN (Virtual LAN)**
> **💭 Définition :** Réseau logique séparé sur la même infrastructure physique.
> **🏢 Analogie :** Étages différents dans un immeuble - même bâtiment, espaces séparés.

### **Switch**
> **💭 Définition :** Équipement qui connecte plusieurs appareils sur un réseau local.
> **🔌 Analogie :** Multiprise intelligente qui dirige les messages vers le bon appareil.

### **Router**
> **💭 Définition :** Équipement qui connecte différents réseaux.
> **🚦 Analogie :** Rond-point qui dirige le trafic vers les bonnes routes.

### **Access Point (AP)**
> **�� Définition :** Point d'accès WiFi.
> **📡 Analogie :** Antenne radio qui diffuse Internet sans fil.

---

## 🚨 Attaques Réseau

### **Man-in-the-Middle (MITM)**
> **💭 Définition :** Interception des communications entre deux parties.
> **📞 Analogie :** Espion qui écoute et peut modifier les conversations téléphoniques.

### **DDoS (Distributed Denial of Service)**
> **💭 Définition :** Attaque qui sature un service avec du trafic.
> **🚗 Analogie :** Embouteillage provoqué par des milliers de voitures fantômes.

### **ARP Spoofing**
> **💭 Définition :** Usurpation d'identité au niveau liaison de données.
> **🎭 Analogie :** Se faire passer pour le facteur pour intercepter le courrier.

### **DNS Poisoning**
> **💭 Définition :** Corruption des réponses DNS pour rediriger le trafic.
> **🗺️ Analogie :** Modifier l'annuaire téléphonique pour rediriger les appels.

### **Port Scanning**
> **💭 Définition :** Test systématique des ports ouverts sur une cible.
> **🚪 Analogie :** Essayer toutes les poignées de porte pour voir lesquelles s'ouvrent.

---

## 📊 Schéma Réseau Type

```
    Internet
        │
    ┌───▼────┐
    │ Router │ (Passerelle)
    │ NAT    │
    └───┬────┘
        │
    ┌───▼────┐
    │ Switch │
    └─┬─┬─┬──┘
      │ │ │
  ┌───▼─┐ ┌▼──┐ ┌▼───────┐
  │ PC1 │ │PC2│ │ Server │
  └─────┘ └───┘ └────────┘
```

---

## 🎯 Ports Importants à Retenir

### **Services Web**
- `80` - HTTP
- `443` - HTTPS
- `8080` - HTTP alternatif

### **Administration**
- `22` - SSH
- `23` - Telnet
- `3389` - RDP (Windows)

### **Fichiers**
- `21` - FTP
- `22` - SFTP
- `445` - SMB (Windows)

### **Base de données**
- `3306` - MySQL
- `5432` - PostgreSQL
- `1433` - MS SQL Server

### **Email**
- `25` - SMTP
- `110` - POP3
- `143` - IMAP

---

*Ce glossaire couvre les concepts réseau essentiels pour la cybersécurité.*
