# 📋 Méthodologie Générale - AdversaryOps

## 📋 Vue d'ensemble
Cette méthodologie unifiée structure l'approche pour tous types d'audits de sécurité offensive, du pentest traditionnel aux opérations Red Team sophistiquées.

## 🎯 Objectifs méthodologiques
- **Standardiser** les approches et processus
- **Maximiser** l'efficacité et la couverture
- **Assurer** la traçabilité et la reproductibilité
- **Optimiser** la gestion des risques et OPSEC

## 📊 Framework général

### Phase 1: 🔍 Reconnaissance (00-Reconnaissance/)
**Objectif**: Cartographier la surface d'attaque sans detection

#### 1.1 Reconnaissance Passive (OPSEC Maximale)
```
┌─ Intelligence Gathering
│  ├── OSINT sur l'organisation
│  ├── Footprinting DNS/Infrastructure
│  └── Recherche de fuites de données
│
├─ Cartographie Infrastructure
│  ├── ASN et plages IP
│  ├── Subdomains et certificats
│  └── Technologies et services
│
└─ Analyse de la surface d'attaque
   ├── Points d'entrée potentiels
   ├── Technologies exposées
   └── Vecteurs d'attaque probables
```

#### 1.2 Reconnaissance Active (Détectable)
```
┌─ Découverte d'hôtes
│  ├── Ping sweeps ICMP/TCP/UDP
│  ├── ARP scanning (réseaux locaux)
│  └── DNS reverse lookup
│
├─ Port scanning
│  ├── Scan rapide (top ports)
│  ├── Scan complet (si autorisé)
│  └── Service fingerprinting
│
└─ Analyse et priorisation
   ├── Classification par criticité
   ├── Identification des vulnérabilités
   └── Préparation énumération
```

### Phase 2: 📝 Énumération (01-Enumeration/)
**Objectif**: Analyser en profondeur les services découverts

#### 2.1 Énumération des Services
```
┌─ Services Réseau
│  ├── SSH (22) → Bannières, auth methods
│  ├── FTP (21) → Anonymous, versions
│  ├── SMB (445) → Shares, null sessions
│  ├── HTTP/S (80,443) → Directories, technologies
│  ├── DNS (53) → Zone transfers, records
│  └── SNMP (161) → Community strings, MIBs
│
├─ Services de Base de Données
│  ├── MySQL (3306) → Users, privileges
│  ├── MSSQL (1433) → SA accounts, xp_cmdshell
│  └── PostgreSQL (5432) → Roles, extensions
│
└─ Services d'Administration
   ├── RDP (3389) → Users, policies
   ├── WinRM (5985) → Authentication
   └── VNC (5900+) → Authentication bypass
```

#### 2.2 Énumération des Systèmes
```
┌─ Systèmes Linux
│  ├── Distribution et version
│  ├── Services et processus
│  ├── Utilisateurs et groupes
│  └── Configuration système
│
└─ Systèmes Windows
   ├── Version et patches
   ├── Domain/Workgroup info
   ├── Users et local groups
   └── Policies et configurations
```

### Phase 3: ⚔️ Exploitation (02-Exploitation/)
**Objectif**: Obtenir un accès initial aux systèmes

#### 3.1 Exploitation Web
```
┌─ Injection Attacks
│  ├── SQL Injection
│  ├── Command Injection
│  ├── LDAP/XPath Injection
│  └── Template Injection
│
├─ Cross-Site Attacks
│  ├── XSS (Reflected, Stored, DOM)
│  ├── CSRF
│  └── Clickjacking
│
└─ File-based Attacks
   ├── File Inclusion (LFI/RFI)
   ├── File Upload vulnerabilities
   └── Directory Traversal
```

#### 3.2 Exploitation Réseau
```
┌─ Buffer Overflow
│  ├── Stack-based overflow
│  ├── Heap-based overflow
│  └── Format string bugs
│
├─ Service Exploitation
│  ├── Known CVEs
│  ├── Default credentials
│  └── Misconfigurations
│
└─ Protocol Attacks
   ├── Man-in-the-Middle
   ├── Protocol downgrade
   └── Cryptographic weaknesses
```

### Phase 4: 🔓 Post-Exploitation (03-Post-Exploitation/)
**Objectif**: Consolider l'accès et progresser dans l'infrastructure

#### 4.1 Persistence
```
┌─ Windows Persistence
│  ├── Services et scheduled tasks
│  ├── Registry modifications
│  ├── WMI event subscriptions
│  └── Golden/Silver tickets
│
└─ Linux Persistence
   ├── Cron jobs et systemd
   ├── SSH keys injection
   ├── Bashrc/Profile modifications
   └── Kernel modules
```

#### 4.2 Credential Access
```
┌─ Credential Dumping
│  ├── LSASS memory (Mimikatz)
│  ├── SAM/SYSTEM registry
│  ├── NTDS.dit (Domain Controllers)
│  └── Browser saved passwords
│
├─ Hash Cracking
│  ├── Rainbow tables
│  ├── Dictionary attacks
│  └── Brute force attacks
│
└─ Credential Reuse
   ├── Password spraying
   ├── Credential stuffing
   └── Pass-the-Hash/Ticket
```

#### 4.3 Lateral Movement
```
┌─ Windows Lateral Movement
│  ├── Pass-the-Hash
│  ├── Pass-the-Ticket
│  ├── WMI/PowerShell remoting
│  └── RDP session hijacking
│
└─ Cross-Platform Movement
   ├── SSH key reuse
   ├── Shared service accounts
   └── Application vulnerabilities
```

### Phase 5: ⬆️ Privilege Escalation (04-Privilege-Escalation/)
**Objectif**: Obtenir des privilèges administrateur/root

#### 5.1 Windows Privilege Escalation
```
┌─ Service Abuse
│  ├── Unquoted service paths
│  ├── Weak service permissions
│  ├── DLL hijacking
│  └── Service binary modification
│
├─ Token Manipulation
│  ├── Token impersonation
│  ├── Process injection
│  └── UAC bypass
│
└─ Kernel Exploitation
   ├── Known kernel exploits
   ├── Driver vulnerabilities
   └── Hardware abstraction layer
```

#### 5.2 Linux Privilege Escalation
```
┌─ SUID/SGID Abuse
│  ├── Binary exploitation
│  ├── Path manipulation
│  └── Environment variables
│
├─ Sudo Misconfigurations
│  ├── Wildcard abuse
│  ├── GTFOBins exploitation
│  └── Sudo version exploits
│
└─ Kernel Exploitation
   ├── Local kernel exploits
   ├── Container escape
   └── Dirty COW variants
```

### Phase 6: 🎭 Advanced Operations (05-Red-Team/)
**Objectif**: Opérations sophistiquées long-terme

#### 6.1 Threat Emulation
```
┌─ APT Simulation
│  ├── TTPs mapping (MITRE ATT&CK)
│  ├── Timeline realistic
│  └── Behavioral patterns
│
└─ Campaign Planning
   ├── Objectives definition
   ├── Success criteria
   └── Exit strategies
```

#### 6.2 Command & Control
```
┌─ C2 Infrastructure
│  ├── Domain fronting
│  ├── CDN utilization
│  ├── Redirectors setup
│  └── Traffic encryption
│
└─ Communication Channels
   ├── HTTP/HTTPS
   ├── DNS tunneling
   ├── Social media APIs
   └── Cloud services abuse
```

## 🕐 Chronologie et Planning

### Audit Court Terme (1-5 jours)
```
Jour 1: Reconnaissance passive + scan initial
Jour 2: Énumération services critiques
Jour 3: Exploitation vulnérabilités évidentes
Jour 4: Post-exploitation et privilege escalation
Jour 5: Reporting et présentation
```

### Audit Moyen Terme (1-2 semaines)
```
Semaine 1:
├── Reconnaissance exhaustive (2 jours)
├── Énumération approfondie (2 jours)
└── Exploitation initiale (1 jour)

Semaine 2:
├── Post-exploitation avancée (2 jours)
├── Lateral movement (2 jours)
└── Reporting détaillé (1 jour)
```

### Opération Red Team (1-3 mois)
```
Phase 1 (2 semaines): Intelligence et infrastructure
Phase 2 (4 semaines): Accès initial et persistence
Phase 3 (4 semaines): Lateral movement et escalation
Phase 4 (2 semaines): Objectives accomplishment
Phase 5 (1 semaine): Clean-up et reporting
```

## 📊 Matrices de Priorité

### Criticité des Vulnérabilités
```
CRITIQUE (Action immédiate):
├── RCE sans authentification
├── Privilege escalation direct
├── Credential exposure
└── Data breach potential

ÉLEVÉ (24-48h):
├── RCE avec authentification
├── Injection SQL avec DB access
├── Misconfiguration critique
└── Known CVE avec exploit public

MOYEN (1 semaine):
├── Information disclosure
├── DoS vulnerabilities
├── Weak cryptography
└── Default configurations

FAIBLE (Planning):
├── Information leakage minor
├── Security headers missing
└── Non-exploitable findings
```

### Priorisation des Cibles
```
Priorité 1 - Critical Assets:
├── Domain Controllers
├── Database servers
├── Administrative interfaces
└── Backup systems

Priorité 2 - High Value:
├── Web applications with data
├── Email servers
├── File servers
└── Development environments

Priorité 3 - Standard:
├── Workstations users
├── Printers et IoT
├── Network devices
└── Monitoring systems
```

## 🛡️ Considérations OPSEC

### Niveaux d'OPSEC
```
NIVEAU 1 - Aucune trace:
├── Reconnaissance passive uniquement
├── Pas d'interaction directe
└── Sources publiques exclusivement

NIVEAU 2 - Traces minimales:
├── Scans espacés dans le temps
├── Fragmentation des sources
└── Techniques d'évasion basiques

NIVEAU 3 - Empreinte contrôlée:
├── Attribution distribuée
├── Techniques d'évasion avancées
└── Cleanup systematique

NIVEAU 4 - Opération assumée:
├── Tests autorisés explicitement
├── Coordination avec équipe défense
└── Documentation complète
```

## 📝 Documentation et Reporting

### Artifacts à Conserver
```
Reconnaissance:
├── Scans et résultats bruts
├── Screenshots des découvertes
├── Logs d'outils automatisés
└── Timeline des activités

Exploitation:
├── Payloads utilisés
├── Preuves de compromission
├── Captures d'écran
└── Commandes exécutées

Post-Exploitation:
├── Données extraites (échantillons)
├── Paths de lateral movement
├── Persistence mechanisms
└── Impact assessment
```

### Structure de Rapport
```
1. Executive Summary
2. Methodology Applied
3. Attack Timeline
4. Technical Findings
5. Risk Assessment
6. Recommendations
7. Appendices (Technical Details)
```

## 🔄 Amélioration Continue

### Retours d'Expérience
- Documentation des échecs et succès
- Identification des gaps méthodologiques
- Mise à jour des techniques selon évolution
- Intégration des nouvelles vulnérabilités

### Veille Technologique
- Suivi des CVE et exploits
- Évolution des techniques défensives
- Nouveaux outils et méthodologies
- Threat intelligence updates

---

## ⚖️ Aspects Éthiques et Légaux

### Pré-requis Obligatoires
- ✅ **Autorisation écrite** explicite et détaillée
- ✅ **Périmètre défini** clairement avec exclusions
- ✅ **Contact d'urgence** disponible 24/7
- ✅ **Insurance** professionnelle à jour

### Limites et Responsabilités
- 🚫 **Aucune action** en dehors du périmètre autorisé
- 🚫 **Pas d'exploitation** des données personnelles
- 🚫 **Aucune perturbation** des services critiques
- 🚫 **Pas de divulgation** d'informations sensibles

---

**🎯 Cette méthodologie évolue constamment avec les nouvelles techniques et menaces. Elle doit être adaptée selon le contexte spécifique de chaque mission.** 