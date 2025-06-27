# 🚀 AdversaryOps Launchpad - Plateforme Cybersécurité Offensive

## 🎯 Vision du Projet

**AdversaryOps Launchpad** est une plateforme d'apprentissage complète dédiée à la cybersécurité offensive et au Red Team. Ce projet rassemble méthodologies, outils, techniques et ressources pédagogiques pour former les professionnels de la sécurité informatique.

> **🎓 Objectif Pédagogique** : Fournir un parcours structuré et progressif pour maîtriser les techniques d'audit de sécurité, de test d'intrusion et d'opérations Red Team.

## 📋 Table des Matières

### 🔍 [00-Reconnaissance](00-Reconnaissance/)
**Collecte d'informations et reconnaissance**
- **Passive** : OSINT, DNS, certificats, footprinting
- **Active** : Port scanning, service discovery, analyse résultats
- **Tools-Reconnaissance** : Amass, Nmap, Recon-ng, theHarvester

### 🕵️ [01-Enumeration](01-Enumeration/)
**Énumération détaillée des cibles**
- **Services Réseau** : SMB, HTTP/HTTPS, DNS, FTP, SSH
- **Systèmes** : Linux, Windows, Active Directory 
- **Tools-Enumeration** : Enum4linux, Gobuster/Dirb, Nikto, WhatWeb

### ⚔️ [02-Exploitation](02-Exploitation/)
**Exploitation des vulnérabilités**
- **Applications** : SQL Injection, XSS, LFI/RFI
- **Réseaux** : Buffer Overflow, WinRM, Database
- **Systèmes** : Linux/Windows Privilege Escalation
- **Tools-Exploitation** : Metasploit, SQLmap, Burp Suite

### 🎭 [03-Post-Exploitation](03-Post-Exploitation/)
**Maintien d'accès et mouvement latéral**
- **Data Exfiltration** : Techniques basiques et avancées
- **Persistance** : Backdoors, Lateral Movement
- **Tools-PostExploitation** : Empire, Cobalt Strike, Persistence tools

### 🔴 [04-Red-Team](04-Red-Team/)
**Opérations Red Team avancées**
- **C2 Infrastructure** : Command & Control
- **Evasion** : AV bypass, obfuscation
- **Advanced TTPs** : Techniques sophistiquées
- **Tools-RedTeam** : Sliver, Mythic, C2 frameworks

### 📡 [05-Wireless-Security](05-Wireless-Security/)
**Sécurité des réseaux sans fil**
- WiFi auditing et exploitation
- Bluetooth et protocoles radio
- **Tools-Wireless** : Aircrack-ng, Kismet, WiFi-Pumpkin

### 🎭 [06-Social-Engineering](06-Social-Engineering/)
**Ingénierie sociale et manipulation**
- Phishing avancé et campagnes
- Techniques OSINT humain
- **Tools-SocialEng** : Gophish, SET, King Phisher

### ☁️ [07-Cloud-Security](07-Cloud-Security/)
**Sécurité Cloud et conteneurs**
- AWS, Azure, GCP
- Kubernetes et Docker
- **Tools-Cloud** : ScoutSuite, Prowler, Pacu

### 🎓 [08-Cours](08-Cours/)
**Formation théorique et concepts**
- **Concepts Fondamentaux** : Windows Auth, AD, Linux
- **Terminologie** : Glossaires spécialisés
- **Outils Généraux** : Metasploit, guides détaillés

### 🧪 [09-Labs-Pratiques](09-Labs-Pratiques/)
**Environnements d'entraînement**
- TryHackMe, HackTheBox, VulnHub
- Challenges et CTF

### ⚙️ [10-Environment-Setup](10-Environment-Setup/)
**Configuration des environnements**
- **Linux** : Kali Linux, post-installation
- **Windows** : VMs, outils Windows

### 📋 [11-Methodology-Guides](11-Methodology-Guides/)
**Méthodologies et guides**
- Méthodologie générale de pentest
- Checklists complètes
- Glossaires techniques

## 🎯 Parcours d'Apprentissage Recommandés

### 🟢 **Niveau Débutant (1-3 mois)**
```
1. Configuration environnement [10-Environment-Setup]
   ↓
2. Concepts fondamentaux [08-Cours/01-Concepts-Fondamentaux]
   ↓
3. Reconnaissance passive [00-Reconnaissance/Passive]
   ↓
4. Énumération basique [01-Enumeration/Services-Réseau]
   ↓
5. Labs pratiques [09-Labs-Pratiques/TryHackMe]
```

### 🟡 **Niveau Intermédiaire (3-6 mois)**
```
1. Reconnaissance active [00-Reconnaissance/Active]
   ↓
2. Énumération avancée [01-Enumeration/Systèmes]
   ↓
3. Exploitation web [02-Exploitation/Applications]
   ↓
4. Escalade de privilèges [02-Exploitation/Systèmes]
   ↓
5. Post-exploitation [03-Post-Exploitation]
```

### 🔴 **Niveau Avancé (6+ mois)**
```
1. Opérations Red Team [04-Red-Team]
   ↓
2. Sécurité Cloud [07-Cloud-Security]
   ↓
3. Social Engineering [06-Social-Engineering]
   ↓
4. Wireless Security [05-Wireless-Security]
   ↓
5. Développement d'exploits personnalisés
```

## 🛠️ Outils Intégrés et Couverts

### Reconnaissance et Énumération
- **Nmap** - Scanner réseau de référence
- **Amass** - Découverte de sous-domaines
- **Gobuster/Dirb** - Fuzzing web
- **Enum4linux** - Énumération SMB/Linux
- **theHarvester** - OSINT et email harvesting

### Exploitation
- **Metasploit** - Framework d'exploitation
- **Burp Suite** - Proxy et scanner web
- **SQLmap** - Exploitation SQL injection
- **Nuclei** - Scanner de vulnérabilités
- **John/Hashcat** - Cracking de mots de passe

### Post-Exploitation et Red Team
- **Cobalt Strike** - Plateforme C2 commerciale
- **Empire/Starkiller** - Framework PowerShell
- **Sliver** - Framework C2 moderne
- **Mythic** - Plateforme C2 collaborative

## 📚 Méthodologie Pédagogique

### Structure Standardisée des Guides
Chaque guide technique suit une structure cohérente :

```
🗂️ Workflow textuel étape par étape
📋 Vue d'ensemble et contexte
🔍 Techniques détaillées avec exemples
🛠️ Outils et commandes pratiques
🛡️ Conseils OPSEC (sécurité opérationnelle)
⚠️ Erreurs fréquentes à éviter
💡 Astuces d'experts
🔗 Ressources pour approfondir
🧭 Navigation vers contenus liés
```

### Approche Pratique
- **Exemples concrets** pour chaque technique
- **Commandes ready-to-use** avec explications
- **Scripts d'automatisation** quand pertinent
- **Workflows complets** de bout en bout

## 🎯 Utilisation Responsable et Éthique

### ⚖️ **Cadre Légal Strict**
- ✅ Tests uniquement sur infrastructure autorisée
- ✅ Environnements de lab personnels
- ✅ Programmes bug bounty légitimes
- ✅ Missions d'audit avec contrat
- ❌ Jamais sur systèmes tiers sans autorisation

### 🎯 **Objectifs Pédagogiques**
- Former des professionnels de la cybersécurité
- Améliorer la sécurité par la compréhension des attaques
- Développer des compétences défensives efficaces
- Préparer aux certifications (OSCP, CEH, CISSP)

## 🚀 Commencer Rapidement

### Installation et Configuration
```bash
# Cloner le repository
git clone [URL_REPO] AdversaryOps-Launchpad
cd AdversaryOps-Launchpad

# Explorer la structure
tree -L 2

# Commencer par l'environnement
cd 10-Environment-Setup/Linux/
cat Installation-Kali-Linux-Complete.md
```

### Premier Parcours Recommandé
1. **Setup** : [Kali Linux Installation](10-Environment-Setup/Linux/Installation-Kali-Linux-Complete.md)
2. **Concepts** : [Windows Authentication](08-Cours/01-Concepts-Fondamentaux/Windows-Authentication/)
3. **Pratique** : [Guide Reconnaissance](00-Reconnaissance/Guide-Reconnaissance-Complete.md)
4. **Lab** : [TryHackMe Starter](09-Labs-Pratiques/TryHackMe/)

## 📊 Statistiques du Projet

- **150+ guides** techniques détaillés
- **500+ commandes** prêtes à l'emploi
- **50+ outils** couverts avec exemples
- **20+ workflows** complets
- **Mises à jour** régulières avec nouvelles techniques

## 🤝 Contribution et Communauté

### Comment Contribuer
- **Issues** : Signaler des erreurs ou suggestions
- **Pull Requests** : Améliorer le contenu existant
- **Nouveaux Guides** : Ajouter des techniques manquantes
- **Traductions** : Adapter le contenu dans d'autres langues

### Standards de Contribution
- Respecter la structure pédagogique existante
- Fournir des exemples pratiques testés
- Inclure les conseils OPSEC appropriés
- Maintenir la qualité et la précision technique

## 📞 Support et Contact

- **GitHub Issues** : Pour les bugs et suggestions
- **Discussions** : Pour les questions techniques
- **Wiki** : Documentation collaborative
- **Releases** : Versions stables du projet

---

## 🎉 Commencez Votre Parcours Cybersécurité

**AdversaryOps Launchpad** vous donne tous les outils pour devenir un expert en cybersécurité offensive. Que vous soyez débutant curieux ou professionnel confirmé, vous trouverez ici les ressources pour progresser efficacement.

> **🚀 Prêt à commencer ?** Direction [10-Environment-Setup](10-Environment-Setup/) pour configurer votre lab, puis [08-Cours](08-Cours/) pour acquérir les bases solides !

---

**⚖️ Rappel Éthique** : Ces techniques sont destinées exclusivement à l'apprentissage et à l'audit autorisé. L'utilisation malveillante est strictement interdite et peut constituer un délit.

**🎯 Bon apprentissage et développement responsable de vos compétences en cybersécurité !** 