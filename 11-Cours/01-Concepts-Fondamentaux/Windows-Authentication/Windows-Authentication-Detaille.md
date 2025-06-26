# Windows Authentication - Concepts Fondamentaux

## 🔐 Vue d'ensemble

L'authentification Windows est le **cœur de la sécurité Microsoft**. Comprendre comment Windows gère les identités et les autorisations est **essentiel** pour la sécurité informatique.

> **💡 Analogie Simple** : Imaginez un hôtel géant avec des milliers de chambres, des employés, des services. L'authentification Windows, c'est le système de badges, de clés et de registres qui détermine qui peut aller où et faire quoi.

## 🎯 Objectifs d'apprentissage

Après cette section, vous saurez :
1. **Comment Windows vérifie l'identité** des utilisateurs
2. **Où sont stockés les mots de passe** et sous quelle forme
3. **Comment fonctionne Active Directory** dans une entreprise
4. **Pourquoi certaines attaques fonctionnent** (Pass-the-Hash, etc.)
5. **Les différences entre local et domaine**

## 🏗️ Architecture d'Authentification Windows

### 📊 Schéma général
```
    ┌─────────────────────────────────────────────────────────────────┐
    │                    WINDOWS AUTHENTICATION                       │
    │                                                                 │
    │  ┌───────────────────┐              ┌──────────────────────────┐ │
    │  │   LOCAL LOGIN     │              │      DOMAIN LOGIN        │ │
    │  │                   │              │                          │ │
    │  │  User: john       │              │  User: marie.martin      │ │
    │  │  Computer: PC-01  │              │  Domain: ENTREPRISE.LOCAL│ │
    │  │                   │              │                          │ │
    │  │  ┌─────────────┐  │              │  ┌─────────────────────┐ │ │
    │  │  │ SAM Database│  │              │  │ Active Directory    │ │ │
    │  │  │             │  │              │  │ Database            │ │ │
    │  │  │ Stores:     │  │              │  │                     │ │ │
    ┌──────┤ - Users     │  │              │  │ Stores:             │ │ │
    │  │  │ - Groups    │  │              │  │ - Domain Users      │ │ │
    │  │  │ - Passwords │  │              │  │ - Groups            │ │ │
    │  │  └─────────────┘  │              │  │ - Policies          │ │ │
    │  └───────────────────┘              │  │ - Computers         │ │ │
    │                                     │  └─────────────────────┘ │ │
    │                                     └──────────────────────────┘ │
    │                                                                 │
    └─────────────────────────────────────────────────────────────────┘
                    ▲                                    ▲
                    │                                    │
            ┌───────────────┐                   ┌───────────────────┐
            │ Workstation   │                   │ Domain Controller │
            │ Standalone    │                   │ (Server)          │
            └───────────────┘                   └───────────────────┘
```

## 🔑 Types d'Authentification

### 1. **Authentification Locale**
> **💭 C'est quoi ?** L'utilisateur se connecte directement sur la machine, sans réseau.

#### Processus détaillé :
```
ÉTAPE 1: Saisie            ÉTAPE 2: Vérification      ÉTAPE 3: Accès
┌─────────────┐           ┌─────────────────┐         ┌─────────────────┐
│ User tape   │ winlogon  │ LSA vérifie     │ success │ Session         │
│ john:pass123│──────────▶│ dans SAM        │────────▶│ Créée           │
│             │   .exe    │ Database        │         │                 │
└─────────────┘           └─────────────────┘         └─────────────────┘
       │                          │                           │
       ▼                          ▼                           ▼
┌─────────────┐           ┌─────────────────┐         ┌─────────────────┐
│ Keyboard    │           │ Hash comparison │         │ Access Token    │
│ Input       │           │ NTLM vs stored  │         │ Generated       │
│             │           │                 │         │                 │
└─────────────┘           └─────────────────┘         └─────────────────┘
```

#### 🗄️ Stockage local - Base SAM
```
Registry: HKLM\SAM\SAM\Domains\Account\Users\
┌─────────────────────────────────────────────┐
│              SAM DATABASE                   │
├─────────────────────────────────────────────┤
│ RID 500: Administrator                      │
│ ├─ Name: Administrator                      │
│ ├─ NTLM: aad3b435b51404eeaad3b435b51404ee   │
│ ├─ LM:   [empty if disabled]               │
│ └─ Last Login: 2024-03-15 09:30:00         │
├─────────────────────────────────────────────┤
│ RID 1001: john                             │
│ ├─ Name: john                              │
│ ├─ NTLM: 5e884898da28047151d0e56f8dc6292773│
│ ├─ Groups: Users, Remote Desktop Users     │
│ └─ Status: Enabled                         │
└─────────────────────────────────────────────┘
```

### 2. **Authentification de Domaine (Active Directory)**
> **💭 C'est quoi ?** L'utilisateur se connecte via un serveur central qui gère tous les comptes de l'entreprise.

#### Processus Kerberos détaillé :
```
    ÉTAPE 1               ÉTAPE 2               ÉTAPE 3               ÉTAPE 4
    Initial Auth          TGT Request           Service Access        Resource Access
    
    Client                Domain Controller     Client                File Server
      │                        │                 │                      │
      │ 1. AS-REQ             │                 │                      │
      │ "Je suis marie.martin" │                 │                      │
      ├──────────────────────▶│                 │                      │
      │                        │                 │                      │
      │ 2. AS-REP              │                 │                      │
      │ "Voici ton TGT"        │                 │                      │
      │◄──────────────────────┤                 │                      │
      │                        │                 │                      │
      │                        │ 3. TGS-REQ      │                      │
      │                        │ "TGT + veux SMB"│                      │
      │                        │◄────────────────┤                      │
      │                        │                 │                      │
      │                        │ 4. TGS-REP      │                      │
      │                        │ "Voici ticket SMB"                     │
      │                        ├────────────────▶│                      │
      │                        │                 │                      │
      │                        │                 │ 5. AP-REQ            │
      │                        │                 │ "Ticket SMB"         │
      │                        │                 ├─────────────────────▶│
      │                        │                 │                      │
      │                        │                 │ 6. "Accès OK"        │
      │                        │                 │◄─────────────────────┤
```

## 🔍 Terminologie Essentielle

### **SAM (Security Account Manager)**
> **💭 Définition :** Base de données locale qui stocke les comptes utilisateurs d'une machine Windows.

**📍 Localisation :** `C:\Windows\System32\config\SAM`
**🔒 Protection :** Chiffré, accessible seulement par SYSTEM
**📊 Contenu :** Utilisateurs locaux, groupes, hashes des mots de passe

### **LSA (Local Security Authority)**
> **💭 Définition :** Service Windows qui gère l'authentification et les politiques de sécurité.

**🔧 Rôle :** Vérifier les credentials, créer les tokens d'accès, gérer les privilèges
**📍 Processus :** `lsass.exe` (Local Security Authority Subsystem Service)

### **LSASS (LSA Subsystem Service)**
> **💭 Définition :** Le processus qui exécute LSA et stocke les credentials en mémoire.

```
    ┌─────────────────────────────────────────────┐
    │              LSASS.EXE                      │
    │            (Process ID: variable)           │
    ├─────────────────────────────────────────────┤
    │           MEMORY CONTENT:                   │
    │                                             │
    │ ┌─────────────────────────────────────────┐ │
    │ │ Authentication Packages:                │ │
    │ │                                         │ │
    │ │ • MSV1_0   - NTLM authentication       │ │
    │ │ • Kerberos - Domain authentication     │ │
    │ │ • WDigest  - Clear text passwords      │ │
    │ │ • TsPkg    - Terminal Services         │ │
    │ │ • LiveSSP  - Microsoft account         │ │
    │ └─────────────────────────────────────────┘ │
    │                                             │
    │ ┌─────────────────────────────────────────┐ │
    │ │ Cached Credentials:                     │ │
    │ │                                         │ │
    │ │ • Plaintext passwords (if WDigest on)   │ │
    │ │ • NTLM hashes                           │ │
    │ │ • Kerberos tickets (TGT/TGS)            │ │
    │ │ • LSA secrets                           │ │
    │ └─────────────────────────────────────────┘ │
    └─────────────────────────────────────────────┘
```

### **NTLM (NT LAN Manager)**
> **💭 Définition :** Protocole d'authentification Microsoft, successeur de LM.

**🔢 Format hash :** 32 caractères hexadécimaux
**💡 Exemple :** `5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8`
**⚠️ Faiblesse :** Peut être utilisé pour Pass-the-Hash sans connaître le mot de passe

### **Kerberos**
> **💭 Définition :** Protocole d'authentification réseau sécurisé utilisé par Active Directory.

**🎫 Principe :** Système de "tickets" temporaires pour accéder aux services
**🔐 Avantage :** Plus sécurisé que NTLM, authentification mutuelle
**⏱️ Durée :** Tickets ont une durée de vie limitée (par défaut 10h)

## 🎯 Types de Comptes Windows

### 📊 Hiérarchie des comptes
```
    ┌─────────────────────────────────────────────────────────────┐
    │                     COMPTES WINDOWS                         │
    │                                                             │
    │  ┌─────────────────┐              ┌─────────────────────────┐ │
    │  │  LOCAUX         │              │       DOMAINE           │ │
    │  │                 │              │                         │ │
    │  │ ┌─────────────┐ │              │ ┌─────────────────────┐ │ │
    │  │ │Administrator│ │              │ │ Domain Admins       │ │ │
    │  │ │RID: 500     │ │              │ │ (Admins du domaine) │ │ │
    │  │ └─────────────┘ │              │ └─────────────────────┘ │ │
    │  │                 │              │                         │ │
    │  │ ┌─────────────┐ │              │ ┌─────────────────────┐ │ │
    │  │ │Guest        │ │              │ │ Domain Users        │ │ │
    │  │ │RID: 501     │ │              │ │ (Users du domaine)  │ │ │
    │  │ │(Désactivé)  │ │              │ └─────────────────────┘ │ │
    │  │ └─────────────┘ │              │                         │ │
    │  │                 │              │ ┌─────────────────────┐ │ │
    │  │ ┌─────────────┐ │              │ │ Service Accounts    │ │ │
    │  │ │Utilisateurs │ │              │ │ (Comptes services)  │ │ │
    │  │ │Créés        │ │              │ └─────────────────────┘ │ │
    │  │ │RID: 1000+   │ │              │                         │ │
    │  │ └─────────────┘ │              │ ┌─────────────────────┐ │ │
    │  └─────────────────┘              │ │ Computer Accounts   │ │ │
    │                                   │ │ (Comptes machines)  │ │ │
    │                                   │ └─────────────────────┘ │ │
    │                                   └─────────────────────────┘ │
    └─────────────────────────────────────────────────────────────┘
```

### **1. Comptes Locaux**

#### **Administrator (RID 500)**
- **Rôle :** Super-administrateur de la machine locale
- **Particularité :** Ne peut jamais être supprimé, même si renommé
- **Danger :** Hash souvent réutilisé entre machines (Pass-the-Hash)

#### **Guest (RID 501)**  
- **Rôle :** Accès invité (normalement désactivé)
- **Particularité :** Pas de mot de passe par défaut
- **Sécurité :** Doit rester désactivé

### **2. Comptes de Domaine**

#### **Domain Admins**
- **Rôle :** Administrateurs de tout le domaine Active Directory
- **Danger :** Accès à TOUS les serveurs et postes du domaine
- **Cible :** Objectif principal des attaquants

#### **Service Accounts** 
- **Rôle :** Comptes pour faire tourner les services (SQL, IIS, etc.)
- **Particularité :** Souvent avec des mots de passe complexes mais fixes
- **Vulnérabilité :** Cible des attaques Kerberoasting

## 🚨 Vulnérabilités Communes

### **Pass-the-Hash**
> **💭 Principe :** Utiliser le hash NTLM directement sans connaître le mot de passe.

```
    ATTAQUE NORMALE           PASS-THE-HASH
    ┌─────────────┐          ┌─────────────────┐
    │ Password    │          │ NTLM Hash       │
    │ "MyPass123!"│          │ 5e884898da2804..│
    └─────────────┘          └─────────────────┘
           │                          │
           ▼                          ▼
    ┌─────────────┐          ┌─────────────────┐
    │ Hash MD4    │          │ Utilisation     │
    │ Creation    │          │ Directe         │
    └─────────────┘          └─────────────────┘
           │                          │
           ▼                          ▼
    ┌─────────────┐          ┌─────────────────┐
    │ Network     │          │ Network         │
    │ Auth        │          │ Auth            │
    └─────────────┘          └─────────────────┘
```

### **Golden Ticket**
> **💭 Principe :** Forger un ticket Kerberos TGT avec le hash du compte KRBTGT.

**🎯 Impact :** Accès à TOUT le domaine pendant des années
**🔑 Prérequis :** Hash du compte KRBTGT (uniquement via Domain Admin)

### **Silver Ticket**
> **💭 Principe :** Forger un ticket TGS pour un service spécifique.

**🎯 Impact :** Accès à un service spécifique (SMB, HTTP, SQL, etc.)
**🔑 Prérequis :** Hash du compte de service

## 📋 Résumé Pratique

### ✅ Points clés à retenir :

1. **LSASS = trésor** - Ce processus contient tous les secrets en mémoire
2. **SAM = coffre local** - Base des comptes locaux de chaque machine  
3. **Active Directory = coffre-fort central** - Base de tous les comptes d'entreprise
4. **NTLM = empreinte** - Hash du mot de passe, réutilisable tel quel
5. **Kerberos = système de tickets** - Plus sécurisé mais complexe

### 🎯 Pour les pentesters :

**Cibles prioritaires :**
1. **Processus LSASS** - Extraction de credentials en mémoire
2. **Base SAM** - Dump des comptes locaux  
3. **Hash KRBTGT** - Golden ticket sur tout le domaine
4. **Comptes de service** - Souvent avec mots de passe faibles
5. **Domain Admins** - Contrôle total du domaine

---
*Cette fiche couvre les concepts essentiels de l'authentification Windows. Maîtriser ces bases est crucial pour comprendre les attaques sur environnements Microsoft.* 