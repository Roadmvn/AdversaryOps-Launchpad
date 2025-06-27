# Énumération POP3/IMAP

## Objectif
Découvrir la présence de services de messagerie POP3/IMAP, tester les accès faibles, et repérer d’éventuelles failles.

---

## Commandes essentielles et explications

### 1. Détection de service et version
```bash
nmap -sV -p 110,995,143,993 target.com
```
- **-sV** : Détecte la version du service.
- **-p 110,995,143,993** : Scanne les ports POP3 (110, 995) et IMAP (143, 993).
- **Exemple de sortie** :
```
110/tcp open  pop3  Dovecot pop3d
995/tcp open  pop3s  Dovecot pop3d
143/tcp open  imap  Dovecot imapd
993/tcp open  imaps  Dovecot imapd
```
- **À surveiller** : Version vulnérable, service inattendu, ports ouverts.

### 2. Banner grabbing (récupération de la bannière)
```bash
nc target.com 110
```
- **nc** : Netcat, permet de se connecter au port POP3.
- **Exemple de sortie** :
```
+OK Dovecot ready.
```
- **À surveiller** : Infos sur le serveur, version, messages personnalisés.

### 3. Test d’accès avec des credentials par défaut
```bash
openssl s_client -connect target.com:995
USER test
PASS test
```
- **openssl s_client** : Permet de se connecter en SSL/TLS.
- **USER/PASS** : Commandes POP3 pour tester un login.
- **Astuce débutant** : Tester des credentials faibles ou par défaut.

### 4. Enumération automatisée avec Nmap
```bash
nmap --script pop3-brute,imap-brute -p 110,143 target.com
```
- **pop3-brute/imap-brute** : Scripts Nmap pour tester des combinaisons utilisateurs/mots de passe courants.
- **Exemple de sortie** :
```
| pop3-brute: 
|   test:test - Valid credentials
| imap-brute: 
|   admin:admin - Valid credentials
```
- **À surveiller** : Credentials valides, réponses différentes selon le nom testé.

---

## Conseils pour débutants
- Toujours commencer par la détection de version et la bannière.
- Tester des credentials faibles ou par défaut avant d’essayer des listes longues.
- Utiliser Nmap pour automatiser la détection de comptes valides.
- Lire la documentation de chaque outil pour découvrir des options avancées.

---

## Pour aller plus loin
- [PayloadsAllTheThings - POP3/IMAP](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/POP3%20IMAP%20Methodology)
- [Nmap POP3 NSE Scripts](https://nmap.org/nsedoc/categories/pop3.html)
- [Nmap IMAP NSE Scripts](https://nmap.org/nsedoc/categories/imap.html) 