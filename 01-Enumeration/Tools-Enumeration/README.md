# Tools-Enumeration - Outils d'Énumération

## 📋 Vue d'ensemble

Ce dossier contient les guides pratiques des outils spécialisés pour l'énumération et la découverte de services, systèmes et applications.

## 🛠️ Outils Disponibles

### Énumération SMB/NetBIOS
- **[Enum4linux.md](Enum4linux.md)** - Énumération complète SMB, partages, utilisateurs, groupes

### Fuzzing et Découverte Web
- **[Gobuster_Dirb.md](Gobuster_Dirb.md)** - Fuzzing de répertoires, fichiers et sous-domaines

### Énumération Web (à venir)
- **Nikto** - Scanner de vulnérabilités web
- **WhatWeb** - Identification de technologies web
- **Wfuzz** - Fuzzing web avancé

### Énumération Réseau (à venir)
- **Nmap Scripts** - Scripts NSE spécialisés pour énumération
- **Masscan** - Scanning haute vitesse
- **DNSrecon** - Énumération DNS avancée

## 🔄 Workflow Standard

1. **Reconnaissance préliminaire** → [00-Reconnaissance/Tools-Reconnaissance/](../../00-Reconnaissance/Tools-Reconnaissance/)
2. **Énumération des services** → [Services-Réseau/](../Services-Réseau/)
3. **Énumération système** → [Systèmes/](../Systèmes/)
4. **Outils spécialisés** → Ce dossier
5. **Exploitation** → [02-Exploitation/Tools-Exploitation/](../../02-Exploitation/Tools-Exploitation/)

## 🎯 Utilisation par Service

### Pour SMB/CIFS
- [Enum4linux](Enum4linux.md) + [Guide SMB](../Services-Réseau/SMB_Enumeration.md)

### Pour HTTP/HTTPS
- [Gobuster](Gobuster_Dirb.md) + [Guide HTTP](../Services-Réseau/HTTP_HTTPS_Enumeration.md)

### Pour DNS
- DNSrecon + [Guide DNS](../DNS/)

### Pour FTP
- Scripts Nmap + [Guide FTP](../Services-Réseau/FTP_Enumeration.md)

## 📚 Guides Associés

### Méthodologies
- [Guide principal d'énumération](../Guide-Enumeration-Pentest.md)
- [Énumération Linux](../Systèmes/Linux_Enumeration.md)
- [Énumération Windows](../Systèmes/Windows_Enumeration.md)
- [Énumération Active Directory](../Systèmes/Active_Directory.md)

### Phases connexes
- [Reconnaissance passive](../../00-Reconnaissance/Passive/)
- [Reconnaissance active](../../00-Reconnaissance/Active/)
- [Post-énumération exploitation](../../02-Exploitation/)

## 🛡️ Conseils OPSEC

- **Discrétion** : Utiliser des délais entre scans
- **Rotation** : Varier les sources IP si possible
- **Logging** : Surveiller les logs de détection
- **Limitation** : Éviter les scans exhaustifs sur production

## ⚠️ Bonnes Pratiques

1. **Toujours** commencer par l'énumération passive
2. **Documenter** tous les résultats trouvés
3. **Prioriser** les services les plus prometteurs
4. **Croiser** les informations entre outils
5. **Respecter** les limitations de bande passante

## 🔗 Navigation

- [⬅️ Retour à l'énumération](../Guide-Enumeration-Pentest.md)
- [🔍 Reconnaissance](../../00-Reconnaissance/)
- [⚔️ Exploitation](../../02-Exploitation/Tools-Exploitation/)
- [🏠 Accueil du projet](../../README.md) 