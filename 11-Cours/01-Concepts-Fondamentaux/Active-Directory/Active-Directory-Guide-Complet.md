# Active Directory - Concepts Fondamentaux

## 🏢 Vue d'ensemble

Active Directory (AD) est le **système nerveux central** des entreprises Microsoft. C'est l'annuaire qui gère TOUS les utilisateurs, ordinateurs, serveurs et ressources d'une organisation.

> **💡 Analogie Simple** : Imaginez Active Directory comme l'annuaire téléphonique + le système de badges + l'organigramme + les règlements de l'entreprise, TOUT centralisé dans un seul système géant.

## ⚠️ **IMPORTANT - À propos des exemples**

> **🎯 TOUS les noms de domaines, utilisateurs, SID dans ce guide sont FICTIFS !**

### 📝 Exemples vs Réalité :

**Dans le guide (EXEMPLE FICTIF) :**
```
ENTREPRISE.LOCAL                              # Nom de domaine fictif
jean.martin, marie.durand                     # Comptes utilisateurs fictifs
S-1-5-21-123456789-987654321-555666777        # SID (Security Identifier) fictif
PC-FINANCE$, SRV-WEB$                          # Comptes ordinateurs fictifs
```

**Dans la vraie vie, vous devez adapter :**
```
VOTREENTREPRISE.LOCAL                          # Votre vrai domaine
prenom.nom, admin.local                        # Vrais utilisateurs trouvés
S-1-5-21-[vrais-chiffres-du-domaine]          # Vrai SID du domaine cible
POSTE-COMPTABILITE$, SERVEUR-SQL$              # Vrais noms d'ordinateurs

⚠️ IMPORTANT: Le symbole $ à la fin = COMPTE D'ORDINATEUR
• PC-FINANCE$ = Compte d'ordinateur nommé "PC-FINANCE"
• SRV-WEB$ = Compte d'ordinateur nommé "SRV-WEB"
• Convention Microsoft pour différencier des comptes utilisateurs
```

## 🎯 Objectifs d'apprentissage

Après cette section, vous saurez :
1. **Architecture d'Active Directory** - Comment c'est organisé
2. **Objets AD principaux** - Utilisateurs, groupes, ordinateurs
3. **Relations de confiance** - Comment les domaines communiquent
4. **Sécurité AD** - Où sont les vulnérabilités courantes
5. **Attaques sur AD** - Pourquoi c'est souvent la cible #1

## 🏗️ Architecture Active Directory

### 📊 Vue d'ensemble hiérarchique
```
    ┌──────────────────────────────────────────────────────────────────┐
    │                        FOREST                                    │
    │                   (Forêt = Entreprise)                           │
    │                                                                  │
    │  ┌─────────────────────────────────────────────────────────────┐ │
    │  │                    ENTERPRISE.LOCAL                         │ │
    │  │                     (Domain Tree)                           │ │
    │  │                                                             │ │
    │  │  ┌─────────────────┐            ┌─────────────────────────┐ │ │
    │  │  │   PARIS.LOCAL   │            │    LONDON.LOCAL         │ │ │
    │  │  │   (Child Domain)│            │    (Child Domain)       │ │ │
    │  │  │                 │            │                         │ │ │
    │  │  │ ┌─────────────┐ │            │ ┌─────────────────────┐ │ │ │
    │  │  │ │    OU       │ │            │ │         OU          │ │ │ │
    │  │  │ │ Marketing   │ │            │ │     Sales           │ │ │ │
    │  │  │ │             │ │            │ │                     │ │ │ │
    │  │  │ │ ┌─────────┐ │ │            │ │ ┌─────────────────┐ │ │ │ │
    │  │  │ │ │ Users   │ │ │            │ │ │     Users       │ │ │ │ │
    │  │  │ │ │ Groups  │ │ │            │ │ │     Groups      │ │ │ │ │
    │  │  │ │ │Computers│ │ │            │ │ │     Computers   │ │ │ │ │
    │  │  │ │ └─────────┘ │ │            │ │ └─────────────────┘ │ │ │ │
    │  │  │ └─────────────┘ │            │ └─────────────────────┘ │ │ │
    │  │  └─────────────────┘            └─────────────────────────┘ │ │
    │  └─────────────────────────────────────────────────────────────┘ │
    └──────────────────────────────────────────────────────────────────┘
```

### 🔍 Définitions des composants

#### **Forest (Forêt)**
> **💭 Définition :** La plus grande unité logique dans AD, contient un ou plusieurs domaines.
> **🌳 Analogie :** Une multinationale avec plusieurs filiales dans différents pays.

**Caractéristiques techniques :**
- **Schéma unique** - Structure de base de données commune à toute la forêt
- **Configuration globale** - Paramètres partagés (sites, liens, réplication)
- **Relations de confiance automatiques** - Tous les domaines se font confiance
- **Catalogue global (GC)** - Index de tous les objets de la forêt
- **Racine de la forêt** - Premier domaine créé, ne peut pas être supprimé

#### **Domain (Domaine)**
> **💭 Définition :** Unité administrative de base contenant les objets (users, computers, etc.).
> **🏘️ Analogie :** Une filiale de l'entreprise avec ses propres employés et règles locales.

**Formats de noms :**
- **NetBIOS** : `ENTREPRISE` (15 caractères max, ancien format)
- **DNS** : `entreprise.local` ou `entreprise.com` (moderne, recommandé)
- **Distinguished Name** : `DC=entreprise,DC=local` (format LDAP)

**Fonctions du domaine :**
- **Réplication** - Synchronisation des données entre DCs
- **Authentification** - Validation des identités (Kerberos/NTLM)
- **Autorisation** - Gestion des permissions et accès
- **Politiques de groupe** - Distribution des GPO (Group Policy Objects)

#### **Domain Controller (DC)**
> **💭 Définition :** Serveur qui stocke la base AD et authentifie les utilisateurs.
> **🏛️ Analogie :** Le siège social qui a tous les dossiers des employés et valide leur identité.

```
    ┌─────────────────────────────────────────────────────────────┐
    │                  DOMAIN CONTROLLER                          │
    │                    (Windows Server)                         │
    │                                                             │
    │  ┌─────────────────────────────────────────────────────────┐ │
    │  │           ACTIVE DIRECTORY DATABASE                     │ │
    │  │                   (NTDS.DIT)                           │ │
    │  │    Localisation: C:\Windows\NTDS\ntds.dit             │ │
    │  │                                                         │ │
    │  │ ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐ │ │
    │  │ │   USERS     │ │  COMPUTERS  │ │       GROUPS        │ │ │
    │  │ │             │ │             │ │                     │ │ │
    │  │ │ jean.martin │ │ PC-FINANCE$ │ │ Domain Admins       │ │ │
    │  │ │ marie.durand│ │ SRV-WEB$    │ │ Domain Users        │ │ │
    │  │ │ admin.local │ │ LAPTOP-CEO$ │ │ Finance Team        │ │ │
    │  │ └─────────────┘ └─────────────┘ └─────────────────────┘ │ │
    │  └─────────────────────────────────────────────────────────┘ │
    │                                                             │
    │  ┌─────────────────────────────────────────────────────────┐ │
    │  │                   SERVICES AD                           │ │
    │  │                                                         │ │
    │  │ • Kerberos (port 88) - Authentification moderne        │ │
    │  │ • LDAP (port 389) - Requêtes annuaire                 │ │
    │  │ • LDAPS (port 636) - LDAP sécurisé                    │ │
    │  │ • DNS (port 53) - Résolution de noms                   │ │
    │  │ • SMB (port 445) - Partage SYSVOL/NETLOGON            │ │
    │  │ • RPC (port 135) - Communication inter-processus       │ │
    │  └─────────────────────────────────────────────────────────┘ │
    └─────────────────────────────────────────────────────────────┘
```

**Types de contrôleurs de domaine :**
- **PDC (Primary Domain Controller)** - Contrôleur principal (concept legacy)
- **BDC (Backup Domain Controller)** - Contrôleur de sauvegarde (concept legacy)
- **RODC (Read-Only Domain Controller)** - DC en lecture seule (sites distants)
- **GC (Global Catalog)** - Contient un index de tous les objets de la forêt

#### **Organizational Unit (OU)**
> **💭 Définition :** Conteneurs pour organiser et administrer les objets AD.
> **🗂️ Analogie :** Les départements dans l'entreprise (Finance, RH, IT, etc.).

**Utilités pratiques :**
- **Liaison GPO** - Appliquer des politiques spécifiques (mot de passe, logiciels)
- **Délégation** - Donner des droits admin limités (reset password sur une OU)
- **Organisation logique** - Structurer selon la géographie ou les métiers
- **Héritage** - Les sous-OUs héritent des politiques parentes

**Exemple de structure OU :**
```
ENTREPRISE.LOCAL
├── Computers
│   ├── Servers
│   └── Workstations
├── Users
│   ├── Finance
│   ├── HR
│   └── IT
└── Groups
    ├── Security Groups
    └── Distribution Lists
```

## 👥 Objets Active Directory

### 📊 Types d'objets principaux
```
    ┌─────────────────────────────────────────────────────────────┐
    │                    OBJETS ACTIVE DIRECTORY                  │
    │                                                             │
    │  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
    │  │    USER     │  │  COMPUTER   │  │       GROUP         │  │
    │  │   ACCOUNT   │  │   ACCOUNT   │  │                     │  │
    │  │             │  │             │  │                     │  │
    │  │ Exemples:   │  │ Exemples:   │  │ Types:              │  │
    │  │ jean.martin │  │ PC-FINANCE$ │  │ • Security Groups   │  │
    │  │ marie.durand│  │ SRV-WEB$    │  │ • Distribution      │  │
    │  │ admin.local │  │ LAPTOP-CEO$ │  │   Groups            │  │
    │  │             │  │             │  │                     │  │
    │  │ Attributs:  │  │ Attributs:  │  │ Scopes:             │  │
    │  │ • Username  │  │ • Hostname  │  │ • Domain Local      │  │
    │  │ • Password  │  │ • OS Version│  │ • Global            │  │
    │  │ • Email     │  │ • Last Logon│  │ • Universal         │  │
    │  │ • Groups    │  │ • Domain    │  │                     │  │
    │  │ • SID       │  │ • SID       │  │                     │  │
    │  └─────────────┘  └─────────────┘  └─────────────────────┘  │
    │                                                             │
    │  ⚠️ IMPORTANT - Le symbole $ dans les noms d'ordinateurs:   │
    │  • PC-FINANCE$ = COMPTE D'ORDINATEUR (pas utilisateur)     │
    │  • Convention Microsoft pour identifier les machines       │
    │  • Créé automatiquement lors du join au domaine            │
    │  • Mot de passe complexe auto-généré (120 caractères)      │
    │  • Rotation automatique tous les 30 jours                  │
    └─────────────────────────────────────────────────────────────┘
```

### **User Accounts (Comptes Utilisateurs)**

#### **Types de comptes utilisateurs**
```
    ┌─────────────────────────────────────────────────────────┐
    │                 TYPES DE COMPTES                        │
    │                                                         │
    │ ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐ │
    │ │   NORMAL    │ │  SERVICE    │ │     PRIVILEGED      │ │
    │ │    USER     │ │  ACCOUNT    │ │     ACCOUNT         │ │
    │ │             │ │             │ │                     │ │
    │ │ jean.martin │ │ svc_sql     │ │ Domain Admins       │ │
    │ │ marie.durand│ │ svc_backup  │ │ Enterprise Admins   │ │
    │ │ bob.taylor  │ │ svc_web     │ │ Schema Admins       │ │
    │ │             │ │             │ │                     │ │
    │ │ Permissions:│ │ Permissions:│ │ Permissions:        │ │
    │ │ • Read Only │ │ • Service   │ │ • Full Control      │ │
    │ │ • Basic     │ │   Specific  │ │ • Domain Wide       │ │
    │ │   Access    │ │ • Automated │ │ • Admin Rights      │ │
    │ └─────────────┘ └─────────────┘ └─────────────────────┘ │
    └─────────────────────────────────────────────────────────┘
```

#### **Attributs critiques des utilisateurs**

**Identifiants principaux :**
- **userPrincipalName (UPN)** : `jean.martin@entreprise.local` (nom complet moderne)
- **sAMAccountName** : `jmartin` (nom pré-Windows 2000, 20 caractères max)
- **distinguishedName** : `CN=Jean Martin,OU=Finance,DC=entreprise,DC=local`
- **objectGUID** : Identifiant unique permanent (ne change jamais)
- **objectSid** : Security Identifier, format S-1-5-21-domain-RID

**Attributs d'authentification :**
- **passwordLastSet** : Timestamp de dernière modification du mot de passe
- **lastLogon** : Dernière connexion (répliqué seulement localement)
- **lastLogonTimestamp** : Dernière connexion (répliqué, mise à jour limitée)
- **logonCount** : Nombre de connexions depuis la création
- **badPwdCount** : Nombre de tentatives échouées consécutives

**Contrôles d'accès :**
- **userAccountControl** : Flags de contrôle (activé, password required, etc.)
  - `512` = Compte normal activé
  - `514` = Compte désactivé  
  - `66048` = Compte activé, password jamais expire
  - `2080` = Workstation trust account
  - `4096` = Workstation trust account

**Appartenance aux groupes :**
- **memberOf** : Liste des groupes d'appartenance
- **primaryGroupID** : Groupe principal (généralement Domain Users = 513)

### **Computer Accounts (Comptes Ordinateurs)**

> **💭 Pourquoi ?** Chaque machine Windows jointe au domaine a son propre compte avec mot de passe.

#### **Convention de nommage avec $**
```
⚠️ EXPLICATION DU SYMBOLE $ :

Format: NOM-MACHINE$
Exemples: PC-FINANCE$, SRV-WEB$, DC01$

POURQUOI LE $ ?
• Convention Microsoft pour identifier les comptes MACHINES
• Différenciation visuelle avec les comptes UTILISATEURS  
• Évite les conflits de noms (jean vs jean$)
• Standard dans tous les systèmes Microsoft depuis NT
```

#### **Détails techniques d'un compte ordinateur**
```
    Computer Account: PC-FINANCE$
    ┌─────────────────────────────────────────────────────────────┐
    │                    INFORMATIONS DÉTAILLÉES                  │
    ├─────────────────────────────────────────────────────────────┤
    │ • Name: PC-FINANCE$                                         │
    │ • sAMAccountName: PC-FINANCE$                              │
    │ • DNS Name: pc-finance.entreprise.local                    │
    │ • Operating System: Windows 10 Enterprise                  │
    │ • OS Version: 10.0 (Build 19044)                          │
    │ • Last Logon: 2024-03-15 08:30:00                         │
    │ • Password: [120 char random] - Auto-rotation 30j         │
    │ • SID: S-1-5-21-xxx-xxx-xxx-1104                          │
    │ • Groups: Domain Computers, Workstation-Users             │
    │ • userAccountControl: 4096 (Workstation Trust Account)    │
    │ • Service Principal Names (SPN):                           │
    │   - HOST/PC-FINANCE                                        │
    │   - HOST/pc-finance.entreprise.local                      │
    │   - TERMSRV/PC-FINANCE                                     │
    │   - TERMSRV/pc-finance.entreprise.local                   │
    └─────────────────────────────────────────────────────────────┘
```

#### **Processus de join au domaine**
```
ÉTAPES JOIN DOMAINE :

1. PREPARATION
   • Admin local tape: "Ajouter au domaine ENTREPRISE.LOCAL"
   • Credentials d'un Domain User demandés

2. CREATION COMPTE
   • DC crée automatiquement PC-FINANCE$ dans OU=Computers
   • Génère mot de passe aléatoire 120 caractères
   • Assigne SID unique

3. CONFIGURATION MACHINE  
   • Machine télécharge GPO du domaine
   • Configure résolution DNS vers DCs
   • Établit canal sécurisé (Secure Channel)

4. AUTHENTIFICATION
   • Machine s'authentifie avec son compte PC-FINANCE$
   • Renouvelle mot de passe tous les 30 jours
   • Valide que le DC lui fait confiance
```

### **Groups (Groupes)**

#### **Types de groupes par fonction**
- **Security Groups** : Assignation de permissions sur ressources
- **Distribution Groups** : Listes de diffusion email (Exchange/Outlook)

#### **Portée des groupes (Scope) - DÉTAILLÉ**
```
    ┌─────────────────────────────────────────────────────────────┐
    │                     GROUP SCOPES                            │
    │                                                             │
    │ ┌─────────────┐ ┌─────────────┐ ┌─────────────────────────┐ │
    │ │ DOMAIN      │ │   GLOBAL    │ │      UNIVERSAL          │ │
    │ │ LOCAL       │ │             │ │                         │ │
    │ │             │ │             │ │                         │ │
    │ │ Membres:    │ │ Membres:    │ │ Membres:                │ │
    │ │ • Users     │ │ • Users     │ │ • Users (any domain)    │ │
    │ │ • Global    │ │ • Computers │ │ • Global Groups         │ │
    │ │   Groups    │ │   (same     │ │ • Universal Groups      │ │
    │ │ • Universal │ │   domain)   │ │   (any domain in forest)│ │
    │ │   Groups    │ │             │ │                         │ │
    │ │             │ │ Portée:     │ │ Portée:                 │ │
    │ │ Portée:     │ │ • Tout      │ │ • Toute la forêt        │ │
    │ │ • Domaine   │ │   domaine   │ │ • Réplication GC        │ │
    │ │   local     │ │   de la     │ │ • Coût réseau élevé     │ │
    │ │   seulement │ │   forêt     │ │                         │ │
    │ └─────────────┘ └─────────────┘ └─────────────────────────┘ │
    │                                                             │
    │ RECOMMANDATION MICROSOFT : A-G-DL-P                        │
    │ • Account → Global group → Domain Local group → Permission │ │
    │                                                             │ │
    │ EXEMPLE :                                                   │
    │ jean.martin → "Finance Users" → "Finance Full Access" → ACL │
    │ (Account)   → (Global)        → (Domain Local)       → (Permission) │
    └─────────────────────────────────────────────────────────────┘
```

### **Groupes privilégiés critiques**

#### **Domain Admins (SID se termine par -512)**
> **⚠️ CRITIQUE** : Contrôle total sur le domaine

**Permissions exactes :**
- **Full Control** sur tous les ordinateurs du domaine (via GPO et ACL)
- **Modification de la base Active Directory** - Lecture/écriture NTDS.DIT
- **Reset des mots de passe** de TOUS les utilisateurs (sauf Enterprise Admins)
- **Installation de logiciels** sur tous les postes via GPO
- **Gestion des GPO** - Création, modification, liaison
- **Droits locaux étendus** - Act as operating system, Backup files, etc.

**Membres par défaut :**
- `Administrator` (compte built-in)
- `Domain Admins` (membres explicites ajoutés)

**SID Format :** `S-1-5-21-[DOMAIN]-512`

#### **Enterprise Admins (SID se termine par -519)**
> **⚠️ ULTRA-CRITIQUE** : Contrôle total sur toute la forêt

**Permissions exactes :**
- **Ajout/suppression de domaines** dans la forêt
- **Contrôle sur tous les domaines** de la forêt (héritage automatique)
- **Modification des trusts** entre forêts
- **Gestion des sites AD** et topologie de réplication
- **Accès aux RODC** (Read-Only Domain Controllers)

**Particularité :** Existe SEULEMENT dans le domaine racine de la forêt

**SID Format :** `S-1-5-21-[ROOT-DOMAIN]-519`

#### **Schema Admins (SID se termine par -518)**
> **⚠️ STRUCTUREL** : Peut modifier la structure même d'AD

**Permissions exactes :**
- **Modification du schéma Active Directory** - Ajout de classes d'objets
- **Extension des attributs** - Nouveaux champs pour tous les objets
- **Modification des contraintes** - Types de données, validation
- **Réplication forcée** - Schema changes répliqués immédiatement

**Sécurité :** Généralement VIDE sauf intervention ponctuelle

**SID Format :** `S-1-5-21-[ROOT-DOMAIN]-518`

#### **Autres groupes privilégiés importants**

**Backup Operators (SID -551)**
- Sauvegarde/restauration de fichiers (bypass ACL)
- Accès physique aux serveurs
- **Risque** : Peut extraire NTDS.DIT !

**Account Operators (SID -548)**  
- Gestion des comptes utilisateurs/groupes
- **Limitation** : PAS les comptes administrateurs
- Modification des OU et objets non-protégés

**Server Operators (SID -549)**
- Gestion des services système
- Connexion locale aux serveurs
- **Risque** : Peut installer des services malveillants

**Print Operators (SID -550)**
- Gestion des imprimantes de domaine
- **Risque** : Exploitation via print spooler

## 🔗 Relations de Confiance (Trusts)

### 📊 Types de relations de confiance
```
    ┌─────────────────────────────────────────────────────────────┐
    │                    TRUST RELATIONSHIPS                      │
    │                                                             │
    │ ┌─────────────────────────────────────────────────────────┐ │
    │ │                AUTOMATIC TRUSTS                         │ │
    │ │                                                         │ │
    │ │  PARENT.COM ←────────────────────→ CHILD.PARENT.COM     │ │
    │ │     │               bidirectional                 │     │ │
    │ │     │               transitive                    │     │ │
    │ │     └─────────────────────────────────────────────┘     │ │
    │ └─────────────────────────────────────────────────────────┘ │
    │                                                             │
    │ ┌─────────────────────────────────────────────────────────┐ │
    │ │                 MANUAL TRUSTS                           │ │
    │ │                                                         │ │
    │ │  ENTREPRISE.LOCAL ←──────────→ PARTENAIRE.COM           │ │
    │ │                  external trust                         │ │
    │ │                                                         │ │
    │ │  DIVISION-A.LOCAL ←──────────→ DIVISION-B.LOCAL         │ │
    │ │                  forest trust                           │ │
    │ └─────────────────────────────────────────────────────────┘ │
    └─────────────────────────────────────────────────────────────┘
```

### **Types de confiance détaillés**

#### **Parent-Child Trust**
> **💭 Automatique** : Créé automatiquement entre domaine parent et enfant

**Caractéristiques :**
- **Direction** : Bidirectionnelle (A trusts B ET B trusts A)
- **Transitivité** : Transitive (si A→B et B→C alors A→C)
- **Exemple** : `corp.com` ↔ `sales.corp.com`
- **Protocole** : Kerberos avec référrals automatiques

#### **Tree-Root Trust**
> **💭 Automatique** : Entre la racine de forêt et les autres arbres

**Caractéristiques :**
- **Direction** : Bidirectionnelle  
- **Transitivité** : Transitive
- **Exemple** : `entreprise.local` ↔ `division.local`
- **Implication** : Utilisateurs de division.local peuvent accéder à entreprise.local

#### **External Trust**
> **💭 Manuel** : Vers des domaines externes à la forêt

**Caractéristiques :**
- **Direction** : Unidirectionnelle ou bidirectionnelle (au choix)
- **Transitivité** : NON-transitive (sécurité)
- **Usage** : Accès à des ressources d'autres organisations
- **Protocole** : NTLM ou Kerberos selon configuration

#### **Forest Trust**
> **💭 Manuel** : Entre deux forêts complètes

**Caractéristiques :**
- **Direction** : Bidirectionnelle recommandée
- **Transitivité** : Transitive au sein de chaque forêt
- **Sélectivité** : Peut être limitée par SID filtering
- **Usage** : Fusion d'entreprises, partenariats

## 🔐 Sécurité Active Directory

### 🚨 Vulnérabilités communes - DÉTAILS TECHNIQUES

#### **1. Service Principal Names (SPN) exposés**
```
⚠️ PROBLÈME DÉTAILLÉ :
• Comptes de service avec SPN enregistrés → Cible Kerberoasting
• SPN = Service Principal Name (ex: HTTP/webserver.corp.com)
• Kerberos émet un TGS chiffré avec le hash du compte de service
• Attaquant peut demander TGS et le craquer offline

🔍 DÉTECTION :
• GetUserSPNs.py -request -dc-ip 10.10.10.10 DOMAIN/user
• ldapsearch -x -h dc.corp.com -b "DC=corp,DC=com" "servicePrincipalName=*"

⚡ IMPACT :
• Récupération du hash NT du compte de service
• Crack possible si mot de passe faible
• Accès avec privilèges du compte de service

🛡️ PROTECTION :
• Managed Service Accounts (MSA/gMSA)
• Mots de passe complexes > 25 caractères
• Rotation fréquente des mots de passe
```

#### **2. Comptes sans pré-authentification (AS-REP Roasting)**
```
⚠️ PROBLÈME DÉTAILLÉ :
• userAccountControl avec flag DONT_REQ_PREAUTH (0x400000)
• Kerberos AS-REQ sans pre-authentication
• AS-REP response contient hash crackable

🔍 DÉTECTION :
• GetNPUsers.py DOMAIN/ -usersfile users.txt -no-pass -dc-ip 10.10.10.10
• ldapsearch pour userAccountControl & 0x400000

⚡ IMPACT :
• Hash AS-REP récupérable sans credentials
• Crack offline du mot de passe utilisateur
• Souvent plus facile que Kerberoasting

🛡️ PROTECTION :
• Désactiver "Do not require Kerberos preauthentication"
• Audit régulier des flags userAccountControl
• Mots de passe robustes pour tous les comptes
```

#### **3. Délégation Kerberos non contrainte**
```
⚠️ PROBLÈME DÉTAILLÉ :
• userAccountControl avec TRUSTED_FOR_DELEGATION (0x80000)
• Serveur peut déléguer vers N'IMPORTE QUEL service
• TGT des utilisateurs stockés en mémoire du serveur

🔍 DÉTECTION :
• ldapsearch "userAccountControl:1.2.840.113556.1.4.803:=524288"
• BloodHound → "Find computers with Unconstrained Delegation"

⚡ IMPACT :
• Compromise du serveur = accès aux TGT utilisateurs
• Pass-the-ticket vers n'importe quel service
• Escalade vers Domain Admin possible

🛡️ PROTECTION :
• Constrained Delegation seulement
• Resource-Based Constrained Delegation (RBCD)
• Pas de comptes admin sur serveurs délégués
```

#### **4. Permissions ACL excessives**
```
⚠️ PROBLÈME DÉTAILLÉ :
• GenericAll/GenericWrite/WriteDACL sur objets sensibles
• Utilisateurs normaux avec droits sur Domain Admins
• Héritage d'ACL mal configuré

🔍 DÉTECTION :
• BloodHound → "Find Shortest Path to Domain Admins"
• PowerView → Get-ObjectAcl | Where-Object {$_.ActiveDirectoryRights -match "Generic"}

⚡ IMPACT :
• Modification de mots de passe d'admins
• Ajout dans groupes privilégiés
• Escalade de privilèges directe

🛡️ PROTECTION :
• Audit régulier des ACL avec Pinkham
• Principe du moindre privilège
• AdminSDHolder pour protéger les groupes sensibles
```

#### **5. Vulnérabilités de délégation RBCD**
```
⚠️ PROBLÈME DÉTAILLÉ :
• msDS-AllowedToActOnBehalfOfOtherIdentity mal configuré
• Comptes peuvent configurer RBCD sur eux-mêmes
• Machine Account Quota > 0 permet création de comptes

🔍 DÉTECTION :
• Impacket addcomputer.py pour créer machine account
• rbcd.py pour configurer délégation
• getST.py pour obtenir ticket d'impersonation

⚡ IMPACT :
• Impersonation d'administrateurs
• Accès non autorisé aux ressources
• Persistence via comptes machines créés

🛡️ PROTECTION :
• ms-DS-MachineAccountQuota = 0
• Audit des modifications msDS-AllowedToActOnBehalfOfOtherIdentity
• Monitoring création de comptes machines
```

## 🎯 Attaques sur Active Directory

### **1. Kerberoasting**
> **💭 Principe** : Demander des tickets TGS pour les comptes de service, puis les craquer offline.

```
    ÉTAPE 1: Énumération SPN    ÉTAPE 2: Demande TGS      ÉTAPE 3: Crack offline
    
    ┌─────────────────┐         ┌───────────────────┐      ┌─────────────────┐
    │ GetUserSPNs.py  │ enum    │ Request TGS       │ hash │ hashcat         │
    │ → Liste des     │────────▶│ for service       │─────▶│ → cleartext     │
    │   comptes SPN   │         │ accounts          │ cat  │   password      │
    └─────────────────┘         └───────────────────┘      └─────────────────┘
```

### **2. DCSync**
> **💭 Principe** : Simuler un Domain Controller pour demander tous les hashes.

```
Permissions requises: DS-Replication-Get-Changes-All
Outils: Mimikatz, SecretsDump.py
Résultat: TOUS les hashes du domaine (y compris KRBTGT)
```

### **3. Golden Ticket**
> **�� Principe** : Forger un TGT avec le hash KRBTGT pour un accès permanent.

```
Prérequis: Hash KRBTGT + Domain SID
Validité: Jusqu'au changement du password KRBTGT (rare)
Impact: Accès Domain Admin permanent et furtif
```

## 📋 Résumé Pratique

### ✅ Points clés pour la sécurité :

1. **Forest = périmètre de sécurité** - Si compromis, tout est compromis
2. **Domain Admins = cible principale** - Accès total au domaine
3. **Service accounts = vector d'attaque** - Souvent mots de passe faibles
4. **KRBTGT = clé master** - Permet Golden tickets
5. **Trusts = chemins latéraux** - Mouvement entre domaines

### 🎯 Pour les pentesters :

**Reconnaissance AD :**
1. **Énumération LDAP** - ldapsearch, BloodHound
2. **Recherche SPN** - GetUserSPNs.py
3. **Comptes sans preauth** - GetNPUsers.py  
4. **Analyse des permissions** - PowerView, BloodHound

**Exploitation AD :**
1. **Kerberoasting** - Crack service account passwords
2. **AS-REP Roasting** - Exploit no-preauth accounts
3. **DCSync** - Si droits de réplication obtenus
4. **Golden/Silver tickets** - Persistence avec hashes

## 📚 Glossaire des Termes Techniques AD

### **SID (Security Identifier)**
> **💭 Définition :** Identifiant unique de sécurité pour chaque objet AD.

**Format détaillé :** `S-1-5-21-XXXXXXXXX-XXXXXXXXX-XXXXXXXXX-RID`
- `S` = Security Identifier
- `1` = Revision (toujours 1)
- `5` = NT Authority
- `21` = Non-unique Authority
- `XXXXXXXXX-XXXXXXXXX-XXXXXXXXX` = Domain Identifier (48 bits)
- `RID` = Relative Identifier (identifie l'objet dans le domaine)

**RIDs bien connus :**
- `500` = Administrator
- `501` = Guest  
- `512` = Domain Admins
- `513` = Domain Users
- `515` = Domain Computers
- `518` = Schema Admins (domaine racine seulement)
- `519` = Enterprise Admins (domaine racine seulement)

### **NTDS.DIT**
> **💭 Définition :** Base de données Active Directory stockant TOUS les objets.

**Détails techniques :**
- **Localisation :** `C:\Windows\NTDS\ntds.dit`
- **Format :** Base ESE (Extensible Storage Engine)
- **Contenu :** Utilisateurs, groupes, ordinateurs, hashes NT
- **Taille :** Peut atteindre plusieurs GB en entreprise
- **Protection :** Accessible seulement en tant que SYSTEM
- **Réplication :** Synchronisée entre tous les DCs

### **SYSVOL et NETLOGON**
> **💭 Définition :** Partages réseau contenant scripts et politiques.

**SYSVOL (`\\domain.com\SYSVOL`) :**
- Scripts de logon/logoff
- Group Policy Templates (GPT)
- Fichiers publics du domaine
- **Réplication :** FRS ou DFSR entre DCs

**NETLOGON (`\\domain.com\NETLOGON`) :**
- Scripts de connexion legacy
- Lien symbolique vers SYSVOL\domain.com\scripts
- **Accès :** Authenticated Users en lecture

### **Comptes Machine avec $ - EXPLICATIONS COMPLÈTES**

#### **Pourquoi le symbole $ ?**
1. **Convention Microsoft** depuis Windows NT pour différencier machines vs utilisateurs
2. **Évite les conflits** de noms (jean vs JEAN$)  
3. **Identification visuelle** rapide du type de compte
4. **Standard NTLM/Kerberos** reconnu par tous les services

#### **Exemples de noms avec $**
```
DC01$                    # Domain Controller nommé DC01
PC-FINANCE$              # Poste Windows nommé PC-FINANCE  
SRV-WEB$                 # Serveur Web nommé SRV-WEB
LAPTOP-CEO$              # Ordinateur portable du CEO
PRINT-HP-FINANCE$        # Imprimante réseau
```

#### **Processus automatique de création**
```
JOINTURE AU DOMAINE :

1. DEMANDE UTILISATEUR
   • Admin tape credentials domain user
   • Machine contacte DC via LDAP

2. AUTHENTIFICATION
   • DC valide les droits de join (Add workstations to domain)
   • Par défaut : 10 machines max par utilisateur

3. CRÉATION COMPTE MACHINE
   • DC crée COMPUTERNAME$ dans OU=Computers
   • Génère mot de passe 120 caractères aléatoires
   • Assigne SID unique (S-1-5-21-domain-RID)

4. CONFIGURATION LOCALE
   • Machine reconfigure DNS → DCs
   • Télécharge GPO initiales
   • Établit Secure Channel pour futures auth

5. MAINTENANCE AUTOMATIQUE
   • Renouvellement password tous les 30 jours
   • Heartbeat régulier vers DC
   • Application automatique des GPO
```

#### **Attributs spéciaux des comptes machines**
```
Computer Account: PC-FINANCE$

• sAMAccountName: PC-FINANCE$
• userAccountControl: 4096 (WORKSTATION_TRUST_ACCOUNT)
• dNSHostName: pc-finance.entreprise.local
• operatingSystem: Windows 10 Enterprise
• operatingSystemVersion: 10.0 (19044)
• lastLogonTimestamp: [timestamp dernière auth]
• pwdLastSet: [timestamp dernier changement password]
• servicePrincipalName: 
  - HOST/PC-FINANCE
  - HOST/pc-finance.entreprise.local
  - TERMSRV/PC-FINANCE [si Remote Desktop activé]
  - TERMSRV/pc-finance.entreprise.local
```

#### **Sécurité des comptes machines**
```
⚠️ IMPLICATIONS SÉCURITÉ :

AVANTAGES :
• Authentification mutuelle machine ↔ domaine
• Pas d'accès interactif (pas de logon local)
• Rotation automatique des mots de passe
• Révocation possible (suppression du compte)

RISQUES :
• Extraction possible via DCSync
• Silver tickets sur services HOST/TERMSRV
• Lateral movement si machine compromise
• Persistence via modification des SPNs
```

### **userAccountControl - FLAGS DÉTAILLÉS**
> **💭 Définition :** Champ bitmask contrôlant les propriétés des comptes.

**Flags les plus importants :**
```
0x0002 (2)      = ACCOUNTDISABLE - Compte désactivé
0x0008 (8)      = HOMEDIR_REQUIRED - Dossier home requis  
0x0010 (16)     = LOCKOUT - Compte verrouillé
0x0020 (32)     = PASSWD_NOTREQD - Mot de passe non requis
0x0040 (64)     = PASSWD_CANT_CHANGE - Ne peut pas changer password
0x0080 (128)    = ENCRYPTED_TEXT_PWD_ALLOWED - Password crypté autorisé
0x0100 (256)    = TEMP_DUPLICATE_ACCOUNT - Compte temporaire
0x0200 (512)    = NORMAL_ACCOUNT - Compte utilisateur normal ✅
0x0800 (2048)   = INTERDOMAIN_TRUST_ACCOUNT - Compte trust inter-domaine
0x1000 (4096)   = WORKSTATION_TRUST_ACCOUNT - Compte machine ✅
0x2000 (8192)   = SERVER_TRUST_ACCOUNT - Compte serveur/DC ✅
0x10000 (65536) = DONT_EXPIRE_PASSWORD - Password n'expire jamais
0x40000 (262144) = SMARTCARD_REQUIRED - Smartcard obligatoire  
0x80000 (524288) = TRUSTED_FOR_DELEGATION - Délégation autorisée ⚠️
0x100000 (1048576) = NOT_DELEGATED - Pas de délégation
0x200000 (2097152) = USE_DES_KEY_ONLY - DES seulement (faible)
0x400000 (4194304) = DONT_REQ_PREAUTH - Pas de pré-auth Kerberos ⚠️
0x800000 (8388608) = PASSWORD_EXPIRED - Password expiré
0x1000000 (16777216) = TRUSTED_TO_AUTH_FOR_DELEGATION - Constrained delegation ⚠️
```

---
*Ce guide Active Directory complet explique TOUS les détails techniques essentiels pour comprendre et attaquer ces environnements. Le symbole $ n'aura plus de secrets pour vous !*
