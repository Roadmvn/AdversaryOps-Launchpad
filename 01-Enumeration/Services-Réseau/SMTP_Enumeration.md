# Énumération SMTP

## Objectif
Découvrir les utilisateurs, tester la configuration du serveur mail, et repérer d’éventuelles failles (relai ouvert, comptes valides, etc.).

---

## Commandes essentielles et explications

### 1. Détection de service et version
```bash
nmap -sV -p 25,465,587 target.com
```
- **-sV** : Détecte la version du service.
- **-p 25,465,587** : Scanne les ports SMTP classiques (25, 465, 587).
- **Exemple de sortie** :
```
25/tcp open  smtp  Postfix smtpd
465/tcp open  smtps  Exim
587/tcp open  submission  Microsoft ESMTP
```
- **À surveiller** : Version vulnérable, service inattendu, ports ouverts.

### 2. Banner grabbing (récupération de la bannière)
```bash
nc target.com 25
```
- **nc** : Netcat, permet de se connecter au port SMTP.
- **Exemple de sortie** :
```
220 mail.target.com ESMTP Postfix (Ubuntu)
```
- **À surveiller** : Infos sur le serveur, version, messages personnalisés.

### 3. Enumération des utilisateurs (VRFY, EXPN)
```bash
telnet target.com 25
VRFY admin
EXPN postmaster
```
- **VRFY** : Vérifie si un utilisateur existe.
- **EXPN** : Dévoile la liste des membres d’une mailing-list.
- **Exemple de sortie** :
```
250 2.1.5 admin
550 5.1.1 User unknown
```
- **À surveiller** : Utilisateurs valides, réponses différentes selon l’utilisateur.

### 4. Enumération automatisée avec Nmap
```bash
nmap --script smtp-enum-users -p 25 target.com
```
- **smtp-enum-users** : Script Nmap pour tester des noms d’utilisateurs courants.
- **Exemple de sortie** :
```
| smtp-enum-users: 
|   admin: exists
|   user: exists
|   test: does not exist
```
- **À surveiller** : Comptes valides, réponses différentes selon le nom testé.

---

## Conseils pour débutants
- Toujours commencer par la détection de version et la bannière.
- Tester VRFY/EXPN uniquement si le serveur les accepte (souvent désactivés).
- Utiliser Nmap pour automatiser la détection de comptes valides.
- Lire la documentation de chaque outil pour découvrir des options avancées.

---

## Pour aller plus loin
- [PayloadsAllTheThings - SMTP](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/SMTP%20Methodology)
- [Nmap SMTP NSE Scripts](https://nmap.org/nsedoc/categories/smtp.html) 