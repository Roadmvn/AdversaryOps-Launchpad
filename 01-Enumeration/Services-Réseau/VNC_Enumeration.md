# Énumération VNC (Virtual Network Computing)

## Objectif
Découvrir la présence d’un service VNC, tester les accès faibles, et repérer d’éventuelles failles (accès sans mot de passe, version vulnérable, etc.).

---

## Commandes essentielles et explications

### 1. Détection de service et version
```bash
nmap -sV -p 5900 target.com
```
- **-sV** : Détecte la version du service.
- **-p 5900** : Scanne le port VNC par défaut.
- **Exemple de sortie** :
```
5900/tcp open  vnc  VNC (protocol 3.8)
```
- **À surveiller** : Version vulnérable, service inattendu sur le port 5900.

### 2. Enumération automatisée avec Nmap
```bash
nmap --script vnc-info,vnc-title,vnc-brute -p 5900 target.com
```
- **vnc-info** : Récupère des infos sur le serveur VNC (version, sécurité).
- **vnc-title** : Affiche le titre de la fenêtre VNC.
- **vnc-brute** : Teste des mots de passe courants.
- **Exemple de sortie** :
```
| vnc-info: 
|   Protocol version: 3.8
|   Security types: None, VNC Authentication
| vnc-title: 
|   Title: target-desktop
| vnc-brute: 
|   No authentication required
```
- **À surveiller** : Accès sans mot de passe, version vulnérable, titre révélateur.

### 3. Test d’accès avec un client VNC
```bash
vncviewer target.com:5900
```
- **vncviewer** : Client graphique pour se connecter au serveur VNC.
- **Astuce débutant** : Tester sans mot de passe, puis avec des mots de passe faibles (“password”, “admin”, etc.).

---

## Conseils pour débutants
- Toujours commencer par la détection de version et l’automatisation avec Nmap.
- Tester l’accès sans mot de passe, puis avec des mots de passe faibles.
- Attention : VNC n’est pas chiffré par défaut, tout passe en clair ! (risque de vol de credentials)
- Lire la documentation de chaque outil pour découvrir des options avancées.

---

## Pour aller plus loin
- [PayloadsAllTheThings - VNC](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/VNC%20Methodology)
- [Nmap VNC NSE Scripts](https://nmap.org/nsedoc/categories/vnc.html) 