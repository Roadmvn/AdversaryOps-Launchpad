# Phishing par Email - Techniques Avancées

## 🎣 Vue d'ensemble

Le **phishing par email** est la technique d'ingénierie sociale la plus répandue. Elle consiste à usurper l'identité d'une organisation de confiance pour obtenir des informations sensibles ou compromettre des systèmes.

> **💡 Analogie Simple** : C'est comme se déguiser en facteur pour entrer dans une maison - vous utilisez une identité de confiance pour tromper la vigilance.

## ⚠️ **IMPORTANT - Cadre Légal et Éthique**

> **🎯 CE GUIDE EST UNIQUEMENT À DES FINS ÉDUCATIVES !**

### 📝 Usage Autorisé vs Interdit :

**✅ AUTORISÉ :**
```
• Tests de sensibilisation en entreprise (avec autorisation)
• Campagnes de formation cybersécurité
• Exercices Red Team autorisés
• Recherche académique et apprentissage
```

**❌ STRICTEMENT INTERDIT :**
```
• Phishing non autorisé (= CYBERCRIMINALITÉ)
• Vol d'identifiants réels
• Fraude financière
• Usurpation d'identité malveillante
```

## 🎯 Objectifs d'apprentissage

Après cette section, vous saurez :
1. **Psychologie du phishing** - Comment les attaques fonctionnent
2. **Techniques de spoofing** - Usurpation d'identité email
3. **Ingénierie sociale** - Manipulation psychologique
4. **Outils et plateformes** - Technologies de phishing
5. **Détection et protection** - Se défendre contre les attaques

## 🧠 Psychologie du Phishing

### **Principes psychologiques exploités**

#### **1. Autorité**
> **💭 Principe :** Les gens obéissent aux figures d'autorité.

```
EXEMPLES D'AUTORITÉ :
• "Message de votre banque"
• "Service informatique de l'entreprise"
• "Administration fiscale"
• "Direction générale"
• "Service de sécurité"
```

#### **2. Urgence**
> **💭 Principe :** La pression temporelle pousse à l'action immédiate.

```
PHRASES D'URGENCE :
• "Votre compte sera suspendu dans 24h"
• "Action requise immédiatement"
• "Dernière chance de confirmer"
• "Échéance aujourd'hui"
• "Sécurité compromise - action immédiate"
```

#### **3. Peur**
> **💭 Principe :** La peur paralyse le jugement critique.

```
VECTEURS DE PEUR :
• "Activité suspecte détectée"
• "Tentative de connexion non autorisée"
• "Virus détecté sur votre ordinateur"
• "Compte piraté"
• "Données compromises"
```

#### **4. Curiosité**
> **💭 Principe :** L'humain veut savoir ce qui se cache derrière.

```
APPÂTS DE CURIOSITÉ :
• "Photo compromettante de vous"
• "Message confidentiel"
• "Vous avez gagné..."
• "Quelqu'un parle de vous"
• "Document classé secret"
```

### **Profils de victimes ciblées**

#### **Dirigeants (Whale Phishing)**
```
CARACTÉRISTIQUES :
• Accès privilégiés aux systèmes
• Pouvoir de décision financière
• Agenda public (LinkedIn, etc.)
• Moins sensibilisés techniquement

APPÂTS EFFICACES :
• Demandes de virements urgents
• Documents légaux importants
• Invitations à des événements VIP
• Rapports financiers confidentiels
```

#### **Employés RH/Finance**
```
CARACTÉRISTIQUES :
• Accès aux données personnelles
• Habitude de recevoir des CV
• Contact avec externes fréquent
• Gestion des paiements

APPÂTS EFFICACES :
• CV avec malware
• Factures à payer
• Demandes d'informations employés
• Formulaires de remboursement
```

#### **Support IT**
```
CARACTÉRISTIQUES :
• Accès administrateur systèmes
• Habitude d'aider les utilisateurs
• Contact technique fréquent
• Confiance dans les outils

APPÂTS EFFICACES :
• Demandes d'aide technique
• Alertes de sécurité système
• Outils d'administration piégés
• Mises à jour logicielles
```

## 📧 Techniques de Spoofing Email

### **Spoofing de l'expéditeur**

#### **Display Name Spoofing**
> **💭 Principe :** Changer le nom affiché sans modifier l'adresse réelle.

```
TECHNIQUE :
From: "Support Microsoft" <attaquant@maildomain.com>
To: victime@entreprise.com

PROBLÈME :
• Le nom affiché semble légitime
• L'adresse réelle est masquée dans la plupart des clients email
• Très simple à implémenter
```

#### **Domain Spoofing**
> **💭 Principe :** Utiliser un domaine similaire au légitime.

```
EXEMPLES DE DOMAINES SIMILAIRES :
Légitime : microsoft.com
Spoofés : 
• microsft.com (lettre manquante)
• microsooft.com (lettre doublée)
• microsoft.co (TLD différent)
• m1crosoft.com (chiffre au lieu de lettre)
• microsoft-security.com (sous-domaine piégé)
```

#### **Homograph Attack**
> **💭 Principe :** Utiliser des caractères Unicode similaires.

```
CARACTÈRES TROMPEURS :
• а (cyrillic a) au lieu de a (latin a)
• о (cyrillic o) au lieu de o (latin o)
• е (cyrillic e) au lieu de e (latin e)

EXEMPLE :
Légitime : paypal.com
Spoofé : pаypal.com (avec cyrillic a)
```

### **Contournement des protections email**

#### **SPF (Sender Policy Framework)**
```bash
# Vérification SPF d'un domaine
dig TXT legitime.com | grep spf

# Résultat typique
"v=spf1 include:_spf.google.com ~all"

# Contournement :
• Utiliser des domaines sans SPF
• Exploiter les "include" trop permissifs
• Abuser des qualifiers "~all" (soft fail)
```

#### **DKIM (DomainKeys Identified Mail)**
```bash
# Vérification DKIM
dig TXT default._domainkey.legitime.com

# Contournement :
• Utiliser des domaines sans DKIM
• Exploiter les clés faibles ou expirées
• Ne pas signer les headers critiques
```

#### **DMARC (Domain-based Message Authentication)**
```bash
# Vérification DMARC
dig TXT _dmarc.legitime.com

# Résultat typique
"v=DMARC1; p=quarantine; rua=mailto:dmarc@legitime.com"

# Contournement :
• Cibler domaines avec p=none
• Exploiter l'alignment SPF/DKIM
• Utiliser des sous-domaines non protégés
```

## 🛠️ Outils et Plateformes de Phishing

### **Frameworks de phishing**

#### **Gophish (Légal/Formation)**
```bash
# Installation
wget https://github.com/gophish/gophish/releases/download/v0.12.1/gophish-v0.12.1-linux-64bit.zip
unzip gophish-v0.12.1-linux-64bit.zip
chmod +x gophish

# Configuration
./gophish
# Interface web : https://127.0.0.1:3333
# Login par défaut : admin / gophish

# Fonctionnalités :
• Templates d'emails personnalisables
• Pages de landing piégées
• Tracking des clics et soumissions
• Rapports détaillés
• API REST complète
```

#### **King Phisher**
```bash
# Installation sur Kali
apt update && apt install king-phisher

# Lancement
sudo king-phisher-server
king-phisher-client

# Fonctionnalités :
• Interface graphique intuitive
• Templates prédéfinis
• Géolocalisation des victimes
• Campagnes multi-étapes
• Export de données
```

#### **SET (Social Engineering Toolkit)**
```bash
# Disponible sur Kali Linux
setoolkit

# Menu principal :
1) Social-Engineering Attacks
   1) Spear-Phishing Attack Vectors
      1) Send a Phishing Email to target
      2) Create a FileFormat Payload
      3) Send a Phishing Email with attachment

# Fonctionnalités :
• Templates email intégrés
• Génération de payloads
• Clonage de sites web
• Attaques mass-mailing
```

### **Services de phishing cloud**

#### **Evilginx2 (Proxy Phishing)**
```bash
# Installation
go get -u github.com/kgretzky/evilginx2

# Configuration
evilginx2 -p ./phishlets

# Technique :
• Proxy transparent entre victime et site légitime
• Capture des cookies de session
• Bypass de l'authentification 2FA
• Phishing en temps réel
```

#### **Modlishka**
```bash
# Installation
go get -u github.com/drk1wi/Modlishka

# Configuration
./Modlishka -config config.json

# Avantages :
• Reverse proxy automatique
• Capture des tokens
• Support HTTPS automatique
• Logs détaillés
```

### **Générateurs de templates**

#### **Templates email réalistes**
```html
<!-- Template "Support Microsoft" -->
<!DOCTYPE html>
<html>
<head>
    <title>Notification de Sécurité Microsoft</title>
    <style>
        body { font-family: Segoe UI, Tahoma, Geneva, Verdana, sans-serif; }
        .header { background: #0078d4; color: white; padding: 20px; }
        .content { padding: 20px; }
        .button { background: #0078d4; color: white; padding: 10px 20px; 
                 text-decoration: none; border-radius: 3px; }
        .footer { font-size: 12px; color: #666; }
    </style>
</head>
<body>
    <div class="header">
        <h2>🛡️ Microsoft Security</h2>
    </div>
    <div class="content">
        <p>Bonjour,</p>
        <p>Nous avons détecté une tentative de connexion suspecte sur votre compte depuis :</p>
        <ul>
            <li><strong>Localisation :</strong> [PAYS_ETRANGER]</li>
            <li><strong>Appareil :</strong> [APPAREIL_INCONNU]</li>
            <li><strong>Date :</strong> [DATE_ACTUELLE]</li>
        </ul>
        <p><strong>Action requise immédiatement :</strong></p>
        <p>Cliquez ci-dessous pour sécuriser votre compte :</p>
        <a href="[LIEN_PIEGE]" class="button">Vérifier l'Activité</a>
        <p>Si vous ne reconnaissez pas cette activité, votre compte pourrait être compromis.</p>
    </div>
    <div class="footer">
        <p>© 2024 Microsoft Corporation. Tous droits réservés.</p>
    </div>
</body>
</html>
```

## 🎭 Ingénierie Sociale Avancée

### **Techniques de prétexte**

#### **Business Email Compromise (BEC)**
```
SCÉNARIO TYPE :
1. Reconnaissance du dirigeant (LinkedIn, site web)
2. Compromission ou spoofing de son email
3. Demande de virement "urgent" au service finance
4. Justification crédible (acquisition, projet secret)
5. Pression temporelle ("avant fermeture banque")

EXEMPLE DE MESSAGE :
"De : PDG <pdg@entreprise.com>
Objet : URGENT - Virement confidentiel

Bonjour [NOM_COMPTABLE],

Je suis en déplacement client et ai besoin d'un virement 
urgent pour finaliser l'acquisition dont nous avons parlé.

Montant : 50 000€
IBAN : [COMPTE_ATTAQUANT]
Référence : ACQ-2024-CONF

Merci de traiter cela avant 17h aujourd'hui.
Confidentialité absolue requise.

Cordialement,
[NOM_PDG]"
```

#### **IT Support Impersonation**
```
SCÉNARIO TYPE :
1. Appel téléphonique se présentant comme IT
2. "Problème de sécurité détecté sur votre poste"
3. Demande d'installation d'un "outil de diagnostic"
4. L'outil est en réalité un malware
5. Accès à distance obtenu

SCRIPT TÉLÉPHONIQUE :
"Bonjour, service informatique à l'appareil.
Nous avons détecté une activité anormale sur votre 
ordinateur. Pour votre sécurité, nous devons lancer 
une vérification immédiate.

Pouvez-vous ouvrir votre email et cliquer sur le lien 
de diagnostic que je viens de vous envoyer ?"
```

### **Social Media Intelligence (SOCMINT)**

#### **Reconnaissance sur LinkedIn**
```bash
# Informations récoltables :
• Organigramme de l'entreprise
• Projets en cours
• Déplacements professionnels
• Relations professionnelles
• Technologies utilisées
• Processus internes

# Outils utiles :
• theHarvester : Collecte d'emails
• linkedin2username : Génération d'usernames
• CrossLinked : Énumération LinkedIn
```

#### **Surveillance des réseaux sociaux**
```bash
# Informations exploitables :
• Vacances/absences (timing d'attaque)
• Photos géolocalisées (bureau, domicile)
• Relations familiales (social engineering)
• Centres d'intérêt (appâts personnalisés)
• Événements professionnels

# Outils OSINT :
• Sherlock : Recherche de comptes
• Social Mapper : Cartographie sociale
• Twint : Analyse Twitter
```

## 🛡️ Détection et Protection

### **Indicateurs techniques de phishing**

#### **Analyse des headers email**
```bash
# Headers suspects à vérifier :
Return-Path: # Différent du From
Reply-To: # Adresse de réponse piégée
Received: # Serveurs de transit suspects
Authentication-Results: # Échecs SPF/DKIM/DMARC

# Outils d'analyse :
• MXToolbox Header Analyzer
• Google Message Header Analyzer
• Microsoft Message Analyzer
```

#### **Analyse des URLs**
```bash
# Techniques de masquage URL :
• Redirections multiples
• URL shorteners (bit.ly, tinyurl)
• Homographs Unicode
• Sous-domaines trompeurs

# Vérification sécurisée :
• VirusTotal URL Scanner
• URLVoid
• Sucuri SiteCheck
• PhishTank Database
```

### **Formation et sensibilisation**

#### **Programmes de sensibilisation**
```
ÉLÉMENTS CLÉS :
• Tests de phishing réguliers
• Formation sur les indicateurs
• Procédures de signalement
• Récompenses pour signalements
• Mise à jour continue

MÉTRIQUES À SUIVRE :
• Taux de clics sur phishing tests
• Temps de signalement
• Amélioration dans le temps
• Récidives par utilisateur
```

#### **Mise en place d'une campagne de test**
```bash
# Étapes recommandées :
1. Définir les objectifs
2. Segmenter les utilisateurs
3. Créer des templates adaptés
4. Planifier les envois
5. Analyser les résultats
6. Former les utilisateurs échoués
7. Reconduire périodiquement

# Outils recommandés :
• Gophish (open source)
• KnowBe4 (commercial)
• Proofpoint Security Awareness
• Microsoft Attack Simulation
```

### **Protections techniques**

#### **Configuration email sécurisée**
```bash
# SPF record exemple
v=spf1 include:_spf.google.com -all

# DKIM configuration
default._domainkey.domain.com TXT "v=DKIM1; k=rsa; p=[PUBLIC_KEY]"

# DMARC policy stricte
v=DMARC1; p=reject; rua=mailto:dmarc@domain.com; 
ruf=mailto:forensic@domain.com; sp=reject; adkim=s; aspf=s;
```

#### **Filtrage anti-phishing**
```bash
# Solutions recommandées :
• Microsoft Defender for Office 365
• Proofpoint Email Protection
• Mimecast Email Security
• Barracuda Email Security Gateway
• SpamTitan Anti-Phishing

# Fonctionnalités importantes :
• URL rewriting et sandboxing
• Safe attachments
• Anti-impersonation
• DMARC alignment
• Threat intelligence feeds
```

## 📋 Checklist Campagne de Phishing (Légale)

### **Phase de préparation**
```bash
☐ Autorisation écrite obtenue
☐ Objectifs clairement définis
☐ Scope et limitations établis
☐ Équipe projet constituée
☐ Planning de campagne validé
☐ Métriques de succès définies
☐ Plan de formation post-test
☐ Procédure d'incident préparée
```

### **Phase d'exécution**
```bash
☐ Infrastructure de test déployée
☐ Templates créés et testés
☐ Listes de cibles préparées
☐ Campagne lancée selon planning
☐ Monitoring en temps réel
☐ Support utilisateurs assuré
☐ Collecte de métriques active
☐ Documentation des incidents
```

### **Phase d'analyse**
```bash
☐ Résultats compilés et analysés
☐ Rapport détaillé produit
☐ Recommandations formulées
☐ Sessions de formation organisées
☐ Mesures correctives implémentées
☐ Nouvelle campagne planifiée
☐ ROI de la sensibilisation calculé
☐ Amélioration continue initiée
```

## 🎯 Ressources et Références

### **Outils de phishing éthique**
- **Gophish** : https://getgophish.com/
- **King Phisher** : https://github.com/rsmusllp/king-phisher
- **SET** : https://github.com/trustedsec/social-engineer-toolkit
- **Evilginx2** : https://github.com/kgretzky/evilginx2

### **Bases de données de phishing**
- **PhishTank** : https://www.phishtank.com/
- **OpenPhish** : https://openphish.com/
- **Anti-Phishing Working Group** : https://apwg.org/
- **CERT-FR** : https://www.cert.ssi.gouv.fr/

### **Formation et certification**
- **SANS SEC504** : Hacker Tools, Techniques, Exploits
- **CISSP** : Certified Information Systems Security Professional  
- **CEH** : Certified Ethical Hacker
- **OSCP** : Offensive Security Certified Professional

---
*Le phishing reste la porte d'entrée principale des cyberattaques. Comprendre ces techniques est essentiel pour mieux s'en défendre !* 