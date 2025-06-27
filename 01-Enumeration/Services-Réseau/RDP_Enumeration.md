# Énumération RDP (Remote Desktop Protocol)

## Objectif
Identifier la présence d'un service RDP, la version, et tester les accès faibles ou mal configurés.

---

## Commandes essentielles et explications

### 1. Détection de service et version
```bash
nmap -sV -p 3389 target.com
```
- **-sV** : Détecte la version du service.
- **-p 3389** : Scanne le port RDP par défaut.
- **Exemple de sortie** :
```
3389/tcp open  ms-wbt-server  Microsoft Terminal Services
```
- **À surveiller** : Version vulnérable, service inattendu sur le port 3389.

### 2. Enumération automatisée avec Nmap
```bash
nmap --script rdp-enum-encryption,rdp-vuln-ms12-020 -p 3389 target.com
```
- **rdp-enum-encryption** : Enumère les méthodes de chiffrement supportées.
- **rdp-vuln-ms12-020** : Vérifie la vulnérabilité MS12-020 (exécution de code à distance).
- **Exemple de sortie** :
```
| rdp-enum-encryption: 
|   Security layer: RDP
|   Encryption level: High
| rdp-vuln-ms12-020: 
|   VULNERABLE: Remote Desktop Protocol Remote Code Execution Vulnerability (MS12-020)
```
- **À surveiller** : Niveau de chiffrement faible, vulnérabilité détectée.

### 3. Test d'accès avec rdesktop ou xfreerdp
```bash
rdesktop target.com
# ou
xfreerdp /v:target.com
```
- **rdesktop/xfreerdp** : Clients RDP pour se connecter à distance.
- **Astuce débutant** : Tester sans credentials pour voir si l'accès invité est activé.

---

## Conseils pour débutants
- Toujours commencer par la détection de version avant de tenter une connexion.
- Vérifier le niveau de chiffrement et la présence de vulnérabilités connues.
- Ne pas insister sur les tentatives de connexion : risque de blocage ou d'alerte.
- Lire la documentation de chaque outil pour découvrir des options avancées.

---

## Pour aller plus loin
- [PayloadsAllTheThings - RDP](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/RDP%20Methodology)
- [Nmap RDP NSE Scripts](https://nmap.org/nsedoc/categories/rdp.html) 