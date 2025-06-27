# 🚀 AdversaryOps-Launchpad

## 📋 Vue d'ensemble

**AdversaryOps-Launchpad** est une plateforme complète d'apprentissage et de référence pour les opérations adversaires (Red Team, Pentest, Bug Bounty). Cette ressource structure méthodiquement les connaissances essentielles du domaine de la cybersécurité offensive.

## 🎯 Objectifs du projet

- **📚 Formation structurée** : Progression logique des bases aux techniques avancées
- **🛠️ Références pratiques** : Guides détaillés pour l'utilisation d'outils
- **🧪 Laboratoires pratiques** : Exercices sur plateformes dédiées
- **📖 Méthodologies éprouvées** : Approches standardisées et best practices
- **🔄 Mise à jour continue** : Évolution avec les nouvelles techniques et outils

## 🗂️ Structure du projet

```
AdversaryOps-Launchpad/
├── 00-Reconnaissance/              # 🔍 Phase de collecte d'informations
├── 01-Enumeration/                 # 📝 Énumération des services et systèmes
├── 02-Exploitation/                # ⚔️ Techniques d'exploitation
├── 03-Post-Exploitation/           # 🔓 Actions post-compromise
├── 04-Privilege-Escalation/        # ⬆️ Élévation de privilèges
├── 05-Red-Team/                    # 🎭 Opérations Red Team avancées
├── 06-Wireless-Security/           # 📡 Sécurité des technologies sans fil
├── 07-Social-Engineering/          # 🎭 Ingénierie sociale et manipulation
├── 08-Cloud-Security/              # ☁️ Sécurité des infrastructures cloud
├── 09-Reporting-Documentation/     # 📊 Rapports et documentation
├── 10-Labs-Pratiques/              # 🧪 Laboratoires et challenges
├── 11-Cours/                       # 📚 Fondements théoriques
├── 12-Environment-Setup/           # ⚙️ Configuration d'environnements
└── 13-Methodology-Guides/          # 📋 Guides méthodologiques
```

## 🚀 Démarrage rapide

### 1. Navigation dans la structure
Chaque dossier principal contient :
- **Techniques spécialisées** organisées par sous-catégories
- **Dossier `Tools-Reconnaissance/`** avec les guides d'outils spécifiques
- **README.md** local avec navigation contextuelle

### 2. Progression recommandée

Schéma textuel de la progression :

12-Environment-Setup
   ↓
11-Cours
   ↓
00-Reconnaissance
   ↓
01-Enumeration
   ↓
02-Exploitation
   ↓
03-Post-Exploitation
   ↓
04-Privilege-Escalation
   ↓
05-Red-Team
   ↓
06-Wireless-Security
   ↓
07-Social-Engineering
   ↓
08-Cloud-Security
   ↓
09-Reporting-Documentation

(En parallèle :)
10-Labs-Pratiques → 00-Reconnaissance, 01-Enumeration, 02-Exploitation
13-Methodology-Guides → 12-Environment-Setup

## 📚 Modules principaux

### 🔍 [00-Reconnaissance](00-Reconnaissance/)
Techniques de collecte d'informations passive et active
- **Passive** : OSINT, Footprinting réseau
- **Active** : Discovery, Port Scanning
- **Outils** : Nmap, Masscan, Amass

### 📝 [01-Enumeration](01-Enumeration/)
Énumération approfondie des services découverts
- **Services Réseau** : SSH, FTP, SMB, HTTP, DNS, SNMP
- **Systèmes** : Linux, Windows
- **Outils** : Enum4linux, dirb, gobuster

### ⚔️ [02-Exploitation](02-Exploitation/)
Techniques d'exploitation de vulnérabilités
- **Web** : SQL Injection, XSS, File Inclusion
- **Réseaux** : Buffer Overflow, exploits RDP
- **Applications** : Binary exploitation, Reverse engineering
- **Outils** : Metasploit Framework

### 🔓 [03-Post-Exploitation](03-Post-Exploitation/)
Actions après compromission initiale
- **Persistence** : Services, tâches planifiées
- **Credential Access** : Mimikatz, LSASS dumping
- **Lateral Movement** : Pass-the-Hash, RDP hijacking
- **Exfiltration** : DNS tunneling, HTTP C2

### ⬆️ [04-Privilege-Escalation](04-Privilege-Escalation/)
Élévation de privilèges sur systèmes compromis
- **Linux** : Sudo misconfig, kernel exploits
- **Windows** : Service abuse, UAC bypass
- **Outils** : PEAS (LinPEAS, WinPEAS)

### 🎭 [05-Red-Team](05-Red-Team/)
Opérations Red Team et techniques furtives
- **Threat Emulation** : Simulation d'attaquants réels
- **Initial Access** : Phishing, exploits zéro-day
- **Command & Control** : Infrastructure C2
- **OPSEC & Evasion** : Techniques d'évasion
- **Outils** : Cobalt Strike, Sliver, Mythic

### 📡 [06-Wireless-Security](06-Wireless-Security/)
Techniques d'évaluation de sécurité sans fil
- **WiFi** : WPA/WPA2 cracking, Evil Twin
- **Bluetooth** : BLE attacks, Bluejacking
- **RFID/NFC** : Clonage, Badge analysis
- **Radio-Frequencies** : SDR, Signal analysis
- **Outils** : Aircrack-ng, Kismet, HackRF

### 🎭 [07-Social-Engineering](07-Social-Engineering/)
Techniques d'ingénierie sociale et manipulation
- **Phishing** : Email, SMS, Voice phishing
- **Pretexting** : Construction de scénarios
- **Physical Security** : Tailgating, Lock picking
- **OSINT Humain** : LinkedIn, Social media
- **Outils** : Gophish, SET, TheHarvester

### ☁️ [08-Cloud-Security](08-Cloud-Security/)
Sécurité des infrastructures cloud modernes
- **AWS** : IAM enumeration, S3 testing
- **Azure** : Azure AD enum, Storage accounts
- **GCP** : GCP IAM, Storage buckets
- **Containers** : Docker, Kubernetes security
- **Outils** : ScoutSuite, Prowler, Pacu

### 📊 [09-Reporting-Documentation](09-Reporting-Documentation/)
Méthodologies de documentation et reporting
- Modèles de rapports professionnels
- Bonnes pratiques de screenshots
- Checklists de livrables

### 🧪 [10-Labs-Pratiques](10-Labs-Pratiques/)
Exercices pratiques sur plateformes dédiées
- **VulnHub** : Machines virtuelles vulnérables
- **HackTheBox** : Challenges et machines
- **TryHackMe** : Parcours d'apprentissage

### 📚 [11-Cours](11-Cours/)
Fondements théoriques indispensables
- **Virtualisation** : Configuration labs
- **Réseaux** : TCP/IP, modèle OSI
- **Systèmes** : Linux, Windows
- **Outils Généraux** : Guides de référence détaillés

### ⚙️ [12-Environment-Setup](12-Environment-Setup/)
Configuration d'environnements de test
- **Linux** : Installation Kali, post-install
- **Windows** : VMs Windows, outils essentiels
- **Réseau** : Topologies lab, virtualisation

### 📋 [13-Methodology-Guides](13-Methodology-Guides/)
Guides méthodologiques et ressources
- Glossaire technique
- Méthodologie générale
- Ressources recommandées

## 🎓 Niveaux d'apprentissage

### 🟢 Débutant
- Commencer par `12-Environment-Setup`
- Étudier `11-Cours` pour les fondamentaux
- Pratiquer sur `10-Labs-Pratiques/TryHackMe`

### 🟡 Intermédiaire
- Maîtriser `00-Reconnaissance` et `01-Enumeration`
- Explorer `02-Exploitation` et `03-Post-Exploitation`
- Découvrir `06-Wireless-Security` et `07-Social-Engineering`
- Challenges `10-Labs-Pratiques/VulnHub`

### 🔴 Avancé
- Approfondir `04-Privilege-Escalation`
- Étudier `05-Red-Team` pour les techniques furtives
- Explorer `08-Cloud-Security` pour les environnements modernes
- Défis `10-Labs-Pratiques/HackTheBox`

## 🛠️ Outils intégrés

### Outils de reconnaissance
- **Nmap** : Scanner réseau de référence
- **Masscan** : Scanning haute vitesse
- **Amass** : Découverte de subdomains
- **Recon-ng** : Framework de reconnaissance

### Outils d'exploitation
- **Metasploit** : Framework d'exploitation
- **Burp Suite** : Proxy web et scanner
- **SQLmap** : Exploitation SQL injection
- **Nuclei** : Scanner de vulnérabilités

### Outils Red Team
- **Cobalt Strike** : Plateforme C2 commerciale
- **Sliver** : Framework C2 open-source
- **Empire** : Post-exploitation PowerShell
- **Mythic** : Plateforme C2 modulaire

## 📖 Conventions et standards

### Structure des documents
- **📋 Vue d'ensemble** : Description et contexte
- **🎯 Objectifs** : Buts spécifiques
- **🔍 Techniques** : Méthodes détaillées
- **🛠️ Outils** : Outils recommandés avec exemples
- **⚠️ Considérations** : Limitations et précautions
- **📚 Références** : Sources et documentations

### Nomenclature des fichiers
- **PascalCase** pour les noms de techniques
- **snake_case** pour les scripts
- Extensions appropriées (`.md`, `.py`, `.sh`)

## 🔄 Contribution et mise à jour

### Principes de contribution
1. **Précision technique** : Vérification des commandes et exemples
2. **Clarté pédagogique** : Explications détaillées et progressives
3. **Sécurité responsable** : Respect des aspects éthiques et légaux
4. **Mise à jour régulière** : Évolution avec les nouvelles techniques

### Structure des mises à jour
- Révision trimestrielle des techniques
- Ajout de nouvelles vulnérabilités
- Mise à jour des versions d'outils
- Intégration de nouveaux labs

## ⚖️ Aspects légaux et éthiques

### 🚨 AVERTISSEMENT IMPORTANT
```
⚠️  Ce contenu est destiné EXCLUSIVEMENT à des fins éducatives et de 
    recherche en sécurité informatique dans un cadre légal et autorisé.

🔒  L'utilisation de ces techniques sur des systèmes sans autorisation 
    explicite est ILLÉGALE et peut entraîner des poursuites judiciaires.

✅  Toujours obtenir une autorisation écrite avant de procéder à des tests
    de sécurité sur des systèmes qui ne vous appartiennent pas.
```

### Bonnes pratiques
- **Autorisation préalable** obligatoire
- **Périmètre défini** clairement
- **Documentation** de toutes les activités
- **Responsabilité** dans l'utilisation des connaissances

## 📞 Support et ressources

### Documentation complémentaire
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [MITRE ATT&CK](https://attack.mitre.org/)

### Communautés recommandées
- [HackTheBox Community](https://www.hackthebox.com/)
- [TryHackMe](https://tryhackme.com/)
- [Reddit r/netsec](https://www.reddit.com/r/netsec/)

## 📈 Roadmap du projet

### Version actuelle : 1.0
- ✅ Structure de base complète
- ✅ Modules de reconnaissance et énumération
- ✅ Guides d'outils essentiels
- ✅ Laboratoires de base

### Version 2.0 (Planifiée)
- 🔄 Modules avancés Red Team
- 🔄 Intégration API et automation
- 🔄 Challenges personnalisés
- 🔄 Interface web interactive

---

## 🎉 Commencer maintenant

### Installation initiale
```bash
# Cloner le repository
git clone [URL_REPO] AdversaryOps-Launchpad
cd AdversaryOps-Launchpad

# Consulter la structure
tree -L 2

# Commencer par l'environnement
cd 09-Environment-Setup
```

### Premier parcours recommandé
1. **Configuration** : `09-Environment-Setup/Linux/`
2. **Fondamentaux** : `08-Cours/02-Réseaux/`
3. **Pratique** : `07-Labs-Pratiques/TryHackMe/`
4. **Application** : `00-Reconnaissance/Passive/`

---

**🎯 Bonne exploration et apprentissage responsable !**

*Ce projet évolue constamment. N'hésitez pas à contribuer et à partager vos retours d'expérience.* 