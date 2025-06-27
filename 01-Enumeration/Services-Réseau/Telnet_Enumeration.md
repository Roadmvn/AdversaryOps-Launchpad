# Énumération Telnet

## Objectif
Découvrir la présence d’un service Telnet, tester les accès faibles, et repérer d’éventuelles failles (accès anonyme, bannière, etc.).

---

## Commandes essentielles et explications

### 1. Détection de service et version
```bash
nmap -sV -p 23 target.com
```
- **-sV** : Détecte la version du service.
- **-p 23** : Scanne le port Telnet par défaut.
- **Exemple de sortie** :
```
23/tcp open  telnet  Linux telnetd
```
- **À surveiller** : Version vulnérable, service inattendu sur le port 23.

### 2. Connexion et récupération de la bannière
```bash
telnet target.com 23
```
- **telnet** : Client Telnet pour se connecter au service.
- **Exemple de sortie** :
```
Trying 192.168.1.10...
Connected to target.com.
Escape character is '^]'.
login:
```
- **À surveiller** : Présence d’un prompt de login, bannière affichée, messages personnalisés.

### 3. Test d’accès avec des credentials par défaut
- Essayer des logins classiques (“admin”, “root”, “guest”, “password”).
- **Astuce débutant** : Beaucoup de systèmes embarqués (routeurs, caméras IP) laissent des accès Telnet ouverts avec des mots de passe par défaut.

### 4. Enumération automatisée avec Nmap
```bash
nmap --script telnet-encryption,telnet-ntlm-info -p 23 target.com
```
- **telnet-encryption** : Enumère les méthodes de chiffrement supportées.
- **telnet-ntlm-info** : Récupère des infos sur l’authentification NTLM (si activée).
- **Exemple de sortie** :
```
| telnet-encryption: 
|   Supported encryption types: none
| telnet-ntlm-info: 
|   NTLM authentication not supported
```
- **À surveiller** : Absence de chiffrement, infos sur l’authentification.

---

## Conseils pour débutants
- Toujours commencer par la détection de version et la bannière.
- Tester des credentials faibles ou par défaut avant d’essayer des listes longues.
- Attention : Telnet n’est pas chiffré, tout passe en clair ! (risque de vol de credentials)
- Lire la documentation de chaque outil pour découvrir des options avancées.

---

## Pour aller plus loin
- [PayloadsAllTheThings - Telnet](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/Telnet%20Methodology)
- [Nmap Telnet NSE Scripts](https://nmap.org/nsedoc/categories/telnet.html) 