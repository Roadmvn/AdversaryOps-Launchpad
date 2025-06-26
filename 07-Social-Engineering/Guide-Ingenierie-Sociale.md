# 07-Social-Engineering

## 🎭 Vue d'ensemble

L'ingénierie sociale exploite les faiblesses humaines plutôt que techniques. Cette section couvre les techniques de manipulation psychologique, de pretexting, et d'attaques basées sur l'humain. Souvent le vecteur d'attaque le plus efficace.

## 🗂️ Structure

### Phishing/
- **Email_Phishing.md** - Campagnes d'hameçonnage par email
- **Spear_Phishing.md** - Attaques ciblées personnalisées
- **SMS_Phishing.md** - Hameçonnage par SMS (Smishing)
- **Voice_Phishing.md** - Hameçonnage vocal (Vishing)
- **Website_Cloning.md** - Clonage de sites web malveillants

### Pretexting/
- **Scenario_Building.md** - Construction de scénarios crédibles
- **Authority_Impersonation.md** - Usurpation d'autorité
- **Technical_Support.md** - Faux support technique
- **Emergency_Situations.md** - Exploitation de l'urgence

### Physical-Security/
- **Tailgating.md** - Techniques d'intrusion physique
- **Badge_Cloning.md** - Clonage de badges d'accès
- **Lock_Picking.md** - Crochetage de serrures
- **RFID_Skimming.md** - Vol de données RFID
- **Dumpster_Diving.md** - Récupération d'informations

### OSINT-Humain/
- **LinkedIn_Intel.md** - Reconnaissance sur LinkedIn
- **Social_Media_OSINT.md** - Intelligence via réseaux sociaux
- **Company_Research.md** - Recherche d'informations entreprise
- **Employee_Profiling.md** - Profilage des employés

### _Tools/
- **Gophish.md** - Plateforme de phishing
- **Social_Engineer_Toolkit.md** - SET Framework
- **TheHarvester.md** - Collecte d'emails et informations
- **Maltego.md** - Analyse de liens et relations
- **Recon-ng.md** - Framework de reconnaissance

## 🧠 Psychologie de l'Ingénierie Sociale

### Principes de Cialdini
1. **Réciprocité** - Obligation de rendre service
2. **Engagement/Cohérence** - Besoin de cohérence
3. **Preuve sociale** - Suivre le comportement des autres
4. **Autorité** - Respect de l'autorité
5. **Sympathie** - Influence des personnes appréciées
6. **Rareté** - Désir des choses rares

### Biais Cognitifs Exploitables
- **Biais de confirmation** - Chercher des informations confirmant nos croyances
- **Effet de halo** - Généraliser à partir d'une caractéristique positive
- **Biais d'autorité** - Accepter sans questionner une figure d'autorité
- **Pression temporelle** - Mauvaises décisions sous pression

## 🎯 Méthodologie OSINT Humaine

### Phase 1 : Reconnaissance
```bash
# Collecte d'emails et informations
theHarvester -d target.com -b all

# Recherche LinkedIn
# (Recherche manuelle ciblée)

# Réseaux sociaux
# Sherlock, SpiderFoot
```

### Phase 2 : Profilage
- Identification des employés clés
- Cartographie des relations
- Analyse des habitudes/horaires
- Identification des technologies utilisées

### Phase 3 : Développement de Scénarios
- Construction de pretexts crédibles
- Adaptation au contexte de l'entreprise
- Personnalisation des approches

### Phase 4 : Exécution
- Tests de phishing ciblés
- Appels de pretexting
- Tentatives d'intrusion physique

## 📧 Campagnes de Phishing

### Types d'Attaques
- **Mass Phishing** - Campagnes de masse
- **Spear Phishing** - Attaques ciblées
- **Whaling** - Ciblage des dirigeants
- **Clone Phishing** - Copie d'emails légitimes

### Vecteurs d'Attaque
- **Email** - Le plus commun
- **SMS/WhatsApp** - Mobile
- **Réseaux sociaux** - LinkedIn, Facebook
- **Appels téléphoniques** - Vishing

## 🏢 Sécurité Physique

### Techniques d'Intrusion
- **Tailgating** - Suivre une personne autorisée
- **Piggyback** - Demander explicitement l'accès
- **Maintenance Impersonation** - Se faire passer pour la maintenance
- **Delivery Impersonation** - Faux livreur

### Équipement
- **Lockpicks** - Crochetage manuel
- **Bump Keys** - Clés à percussion
- **RFID Cloner** - Proxmark3, Flipper Zero
- **Hidden Cameras** - Surveillance cachée

## ⚠️ Considérations Éthiques

**RÈGLES STRICTES** pour l'ingénierie sociale :

- ✅ **Contrat écrit** avec limites claires
- ✅ **No-Go zones** définies (vie privée, famille)
- ✅ **Débriefing** obligatoire post-test
- ❌ **Jamais de dommages psychologiques**
- ❌ **Respect de la dignité humaine**

## 🔧 Outils et Frameworks

### Phishing
- **Gophish** - Plateforme complète
- **King Phisher** - Framework avancé
- **Social Engineer Toolkit** - SET

### OSINT
- **Maltego** - Analyse de relations
- **Recon-ng** - Framework modulaire
- **SpiderFoot** - Automatisation OSINT

### Physique
- **Proxmark3** - RFID/NFC
- **Flipper Zero** - Multi-tool
- **Lock Pick Sets** - Crochetage

## 📚 Formation et Certification

### Certifications
- **SCYBER** - Social Cyber Security
- **OSCP** - Sections social engineering
- **CEH** - Certified Ethical Hacker

### Ressources
- **"The Art of Deception"** - Kevin Mitnick
- **"Social Engineering: The Science of Human Hacking"** - Christopher Hadnagy
- **Social-Engineer.org** - Ressources communautaires

---
*Section maintenue par : [Votre nom]*  
*Dernière mise à jour : [Date]* 