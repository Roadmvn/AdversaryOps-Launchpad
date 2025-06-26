# Glossaire Windows - Terminologie Technique

## 📚 Vue d'ensemble

Ce glossaire définit **TOUS les termes techniques Windows** que vous rencontrerez dans les guides. Chaque terme est expliqué **simplement** avec des analogies du quotidien.

> **💡 Conseil d'usage :** Gardez cette page ouverte pendant vos lectures. Dès qu'un terme vous échappe, revenez ici !

---

## 🔐 Authentification & Sécurité

### **AD (Active Directory)**
> **💭 Définition :** L'annuaire centralisé de Microsoft qui gère tous les utilisateurs, ordinateurs et ressources d'une entreprise.
> **🏢 Analogie :** C'est comme l'annuaire téléphonique + le système de badges + les organigrammes d'une entreprise, tout en un.

### **ADCS (Active Directory Certificate Services)**
> **💭 Définition :** Service qui gère les certificats numériques dans l'entreprise.
> **📜 Analogie :** Comme un bureau qui délivre des cartes d'identité numériques officielles.

### **Domain Controller (DC)**
> **💭 Définition :** Serveur qui stocke et gère la base Active Directory d'un domaine.
> **🏛️ Analogie :** Le serveur central qui fait autorité sur qui peut accéder à quoi dans l'entreprise.

### **Domain**
> **💭 Définition :** Ensemble logique d'ordinateurs et d'utilisateurs gérés ensemble.
> **🏘️ Analogie :** Comme un quartier dans une ville - tous les habitants suivent les mêmes règles du quartier.

**Format :** `ENTREPRISE.LOCAL` ou `company.com`

### **Forest** 
> **💭 Définition :** Collection de domaines Active Directory qui se font confiance.
> **🌳 Analogie :** Plusieurs quartiers (domaines) qui forment une ville complète (forest).

### **GPO (Group Policy Object)**
> **💭 Définition :** Règles de configuration appliquées automatiquement aux ordinateurs/utilisateurs.
> **📜 Analogie :** Le règlement intérieur automatique de l'entreprise qui s'applique dès qu'on arrive.

### **KRBTGT**
> **💭 Définition :** Compte de service spécial d'Active Directory qui signe tous les tickets Kerberos.
> **🔑 Analogie :** Le grand patron qui signe tous les badges d'accès de l'entreprise.

### **LSA (Local Security Authority)**
> **💭 Définition :** Service Windows qui gère l'authentification locale et les politiques de sécurité.
> **👮 Analogie :** Le service de sécurité d'un bâtiment qui vérifie les badges à l'entrée.

### **LSASS (LSA Subsystem Service)**
> **💭 Définition :** Le processus Windows qui exécute LSA et garde les credentials en mémoire.
> **🧠 Analogie :** La mémoire du vigile qui se souvient de tous les badges valides actuellemnet.

**Particularité :** **CIBLE PRINCIPALE** des attaques - contient tous les secrets !

### **SAM (Security Account Manager)**
> **💭 Définition :** Base de données locale qui stocke les comptes utilisateurs d'une machine Windows.
> **📖 Analogie :** Le carnet d'adresses local d'une machine avec tous ses utilisateurs autorisés.

**Localisation :** `HKLM\SAM` dans le registre Windows

### **SID (Security Identifier)**
> **💭 Définition :** Identifiant unique permanent pour chaque utilisateur/groupe/ordinateur.
> **🆔 Analogie :** Comme un numéro de sécurité sociale - unique et ne change jamais.

**Format :** `S-1-5-21-123456789-987654321-555666777-1001`
- `S-1-5-21` = préfixe standard
- `123456789-987654321-555666777` = identifiant du domaine
- `1001` = RID (Relative ID) de l'utilisateur

### **RID (Relative Identifier)**
> **💭 Définition :** La partie finale du SID qui identifie spécifiquement un objet.
> **🔢 Analogie :** Comme le numéro d'appartement dans une adresse complète.

**RIDs spéciaux :**
- `500` = Administrator (toujours)
- `501` = Guest (toujours)
- `1000+` = Utilisateurs créés

---

## 🔑 Mots de Passe & Hashes

### **NTLM Hash**
> **💭 Définition :** Empreinte cryptographique du mot de passe Windows (MD4).
> **🔐 Analogie :** Comme l'empreinte digitale du mot de passe - unique mais on ne peut pas retrouver le mot de passe original.

**Format :** 32 caractères hexadécimaux
**Exemple :** `5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8`

### **LM Hash (LAN Manager)**
> **💭 Définition :** Ancien format de hash Windows, très faible cryptographiquement.
> **⚠️ Statut :** Obsolète et désactivé par défaut depuis Windows Vista.

### **Plaintext Password**
> **💭 Définition :** Mot de passe en clair, lisible directement.
> **🏆 Analogie :** Le vrai mot de passe tel que l'utilisateur l'a tapé - le "Saint Graal" !

### **WDigest**
> **💭 Définition :** Protocole d'authentification qui stocke les mots de passe en CLAIR en mémoire.
> **⚠️ Danger :** Désactivé par défaut depuis Windows 8.1 car trop risqué.

---

## 🎫 Kerberos

### **TGT (Ticket Granting Ticket)**
> **💭 Définition :** Ticket principal qui permet de demander l'accès aux services.
> **🎫 Analogie :** Votre badge d'employé qui vous permet de demander l'accès aux différents bureaux.

**Durée de vie :** 10 heures par défaut

### **TGS (Ticket Granting Service)**
> **💭 Définition :** Ticket pour accéder à un service spécifique (SMB, HTTP, SQL, etc.).
> **🚪 Analogie :** La clé temporaire pour une salle spécifique que vous avez demandée avec votre badge.

### **Service Principal Name (SPN)**
> **💭 Définition :** Nom unique qui identifie un service dans Active Directory.
> **📋 Analogie :** L'adresse exacte d'un service (comme "Salle de réunion A, Étage 3, Bâtiment Est").

**Format :** `SERVICE/hostname.domain.com`
**Exemples :**
- `HTTP/webserver.entreprise.local`
- `MSSQL/database.entreprise.local`

### **AS-REQ / AS-REP**
> **💭 Définition :** Messages d'authentification initiale dans Kerberos.
> **AS-REQ :** "Je suis Marie Martin, voici ma preuve d'identité"
> **AS-REP :** "OK Marie, voici ton badge principal (TGT)"

### **TGS-REQ / TGS-REP**
> **💭 Définition :** Messages de demande d'accès aux services dans Kerberos.
> **TGS-REQ :** "J'ai mon badge, je veux accéder à la salle de réunion"
> **TGS-REP :** "OK, voici ta clé temporaire pour cette salle (TGS)"

---

## 🖥️ Processus & Services

### **winlogon.exe**
> **💭 Définition :** Processus qui gère l'écran de connexion Windows (Ctrl+Alt+Del).
> **🖥️ Analogie :** L'écran d'accueil qui vous demande votre nom et mot de passe.

### **LogonUI.exe**
> **💭 Définition :** Interface graphique moderne de connexion Windows.
> **🎨 Analogie :** La jolie interface de connexion qu'on voit maintenant (au lieu de l'ancienne boîte bleue).

### **csrss.exe (Client Server Runtime Process)**
> **💭 Définition :** Processus système critique qui gère l'interface utilisateur Windows.
> **⚙️ Analogie :** Le moteur qui fait fonctionner toutes les fenêtres et boutons de Windows.

### **services.exe**
> **💭 Définition :** Gestionnaire de services Windows (Service Control Manager).
> **👔 Analogie :** Le chef d'équipe qui démarre, arrête et surveille tous les services Windows.

### **smss.exe (Session Manager Subsystem)**
> **💭 Définition :** Premier processus utilisateur lancé par le noyau Windows.
> **🚀 Analogie :** Le processus qui démarre tout le reste de Windows après le noyau.

---

## 🌐 Réseau & Protocoles

### **SMB (Server Message Block)**
> **💭 Définition :** Protocole de partage de fichiers et d'imprimantes Windows.
> **📁 Analogie :** Le système qui permet de partager des dossiers entre ordinateurs comme s'ils étaient locaux.

**Ports :** 445 (SMB), 139 (NetBIOS over TCP/IP)

### **NetBIOS (Network Basic Input/Output System)**
> **💭 Définition :** API ancienne pour la communication réseau Windows.
> **📞 Analogie :** L'ancien système téléphonique de Windows pour communiquer sur le réseau local.

### **LDAP (Lightweight Directory Access Protocol)**
> **💭 Définition :** Protocole pour interroger les annuaires comme Active Directory.
> **📞 Analogie :** Le langage standard pour parler aux annuaires d'entreprise.

**Ports :** 389 (LDAP), 636 (LDAPS sécurisé)

### **DNS (Domain Name System)**
> **💭 Définition :** Service qui traduit les noms en adresses IP.
> **📞 Analogie :** L'annuaire téléphonique d'Internet qui traduit "google.com" en "172.217.22.14".

### **DHCP (Dynamic Host Configuration Protocol)**
> **💭 Définition :** Service qui distribue automatiquement les adresses IP.
> **🏠 Analogie :** Le service qui attribue automatiquement les numéros de maison dans un nouveau quartier.

---

## 💾 Registre Windows

### **HKLM (HKEY_LOCAL_MACHINE)**
> **💭 Définition :** Ruche du registre contenant la configuration de la machine.
> **⚙️ Analogie :** Les réglages généraux de l'ordinateur qui s'appliquent à tous les utilisateurs.

### **HKCU (HKEY_CURRENT_USER)**
> **💭 Définition :** Ruche du registre contenant la configuration de l'utilisateur actuel.
> **👤 Analogie :** Les préférences personnelles de l'utilisateur connecté.

### **Registry Hive**
> **💭 Définition :** Fichier physique qui stocke une partie du registre Windows.
> **📄 Analogie :** Comme un classeur qui contient une section des réglages Windows.

**Localisation :** `C:\Windows\System32\config\`

---

## 🔧 Outils & Commandes

### **PsExec**
> **💭 Définition :** Outil Sysinternals pour exécuter des commandes à distance.
> **🔧 Analogie :** Une télécommande pour faire tourner des programmes sur d'autres ordinateurs.

### **RunAs**
> **💭 Définition :** Commande pour exécuter un programme avec les droits d'un autre utilisateur.
> **👤 Analogie :** Se "déguiser" temporairement en quelqu'un d'autre pour faire une action.

**Syntaxe :** `runas /user:DOMAIN\username cmd`

### **Net Commands**
> **💭 Définition :** Famille de commandes pour gérer les ressources réseau Windows.
> **🌐 Analogie :** La boîte à outils réseau de Windows.

**Exemples :**
- `net user` = lister les utilisateurs
- `net share` = lister les partages
- `net use` = connecter des lecteurs réseau

### **SC (Service Control)**
> **💭 Définition :** Commande pour gérer les services Windows.
> **⚙️ Analogie :** Le tableau de bord pour démarrer/arrêter les services.

**Syntaxe :** `sc query`, `sc start`, `sc stop`

---

## 🏢 Architecture Active Directory

### **OU (Organizational Unit)**
> **💭 Définition :** Conteneur dans Active Directory pour organiser les objets.
> **🗂️ Analogie :** Comme des dossiers dans un classeur pour ranger les utilisateurs par service.

### **Schema**
> **💭 Définition :** Définit quels types d'objets peuvent exister dans Active Directory.
> **📋 Analogie :** Le formulaire officiel qui définit quelles informations on peut stocker sur chaque employé.

### **Global Catalog**
> **💭 Définition :** Index partiel de tous les objets de la forêt Active Directory.
> **📚 Analogie :** L'index d'une encyclopédie qui permet de trouver rapidement où est chaque information.

### **FSMO Roles (Flexible Single Master Operations)**
> **💭 Définition :** Rôles spéciaux dans Active Directory qui ne peuvent être tenus que par un seul serveur.
> **👑 Analogie :** Les postes de direction unique dans une entreprise (CEO, CFO, etc.).

**Les 5 rôles :**
1. **Schema Master** - Modifie le schéma
2. **Domain Naming Master** - Ajoute/supprime des domaines  
3. **RID Master** - Distribue les RID
4. **PDC Emulator** - Synchronisation temporelle
5. **Infrastructure Master** - Références inter-domaines

---

## 📊 Schéma Récapitulatif

```
┌─────────────────────────────────────────────────────────────────────┐
│                        ÉCOSYSTÈME WINDOWS                           │
│                                                                     │
│  ┌─────────────────────┐         ┌─────────────────────────────────┐ │
│  │    CLIENT WINDOWS   │         │       DOMAIN CONTROLLER        │ │
│  │                     │         │                                 │ │
│  │ ┌─────────────────┐ │         │ ┌─────────────────────────────┐ │ │
│  │ │   PROCESSES     │ │         │ │     ACTIVE DIRECTORY        │ │ │
│  │ │                 │ │         │ │                             │ │ │
│  │ │ • winlogon.exe  │ │ Kerberos│ │ • Users                     │ │ │
│  │ │ • lsass.exe     │◄─────────┼─┤ • Groups                    │ │ │
│  │ │ • csrss.exe     │ │         │ │ • Computers                 │ │ │
│  │ └─────────────────┘ │         │ │ • GPOs                      │ │ │
│  │                     │         │ └─────────────────────────────┘ │ │
│  │ ┌─────────────────┐ │         │                                 │ │
│  │ │    REGISTRY     │ │         │ ┌─────────────────────────────┐ │ │
│  │ │                 │ │         │ │        SERVICES             │ │ │
│  │ │ • HKLM\SAM      │ │         │ │                             │ │ │
│  │ │ • HKLM\SYSTEM   │ │         │ │ • DNS                       │ │ │
│  │ │ • HKLM\SOFTWARE │ │         │ │ • DHCP                      │ │ │
│  │ └─────────────────┘ │         │ │ • ADCS                      │ │ │
│  └─────────────────────┘         │ └─────────────────────────────┘ │ │
│                                  └─────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🎯 Termes par Catégorie

### 🔴 **CRITIQUE** - À connaître absolument
- **LSASS** - Processus contenant tous les secrets
- **SAM** - Base locale des comptes
- **Active Directory** - Annuaire central de l'entreprise
- **NTLM Hash** - Empreinte du mot de passe
- **Domain Controller** - Serveur maître du domaine

### 🟡 **IMPORTANT** - Souvent rencontré
- **Kerberos**, **TGT/TGS** - Système d'authentification moderne
- **SID/RID** - Identifiants uniques
- **SMB/NetBIOS** - Protocoles de partage
- **GPO** - Politiques de groupe

### 🟢 **UTILE** - Bon à savoir
- **FSMO Roles** - Rôles AD spéciaux
- **Schema** - Structure AD
- **WDigest** - Protocole auth ancien
- **OU** - Unités organisationnelles

---

*Ce glossaire est votre dictionnaire de référence. N'hésitez pas à y revenir régulièrement !* 