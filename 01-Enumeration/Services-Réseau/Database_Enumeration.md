# Énumération des Bases de Données (MySQL, PostgreSQL, MSSQL)

## Objectif
Découvrir les bases de données, les utilisateurs, les permissions et les failles potentielles sur les serveurs de bases de données.

---

## Commandes essentielles et explications

### 1. Détection de service et version
```bash
nmap -sV -p 3306,5432,1433 target.com
```
- **-sV** : Détecte la version du service.
- **-p 3306,5432,1433** : Scanne les ports MySQL (3306), PostgreSQL (5432), MSSQL (1433).
- **Exemple de sortie** :
```
3306/tcp open  mysql     MySQL 5.7.29
5432/tcp open  postgresql PostgreSQL DB 9.6.17
1433/tcp open  ms-sql-s  Microsoft SQL Server 2017
```
- **À surveiller** : Versions obsolètes, services inattendus.

### 2. Connexion anonyme ou par défaut (MySQL)
```bash
mysql -h target.com -u root -p
```
- **mysql** : Client MySQL.
- **-u root** : Utilisateur "root" (souvent le compte admin par défaut).
- **Astuce débutant** : Tester sans mot de passe ou avec des mots de passe faibles ("root", "admin", "password").
- **Exemple de sortie** :
```
Enter password:
Welcome to the MySQL monitor...
mysql>
```
- **À surveiller** : Accès sans mot de passe, comptes admin accessibles.

### 3. Enumération des bases et utilisateurs (MySQL)
```sql
SHOW DATABASES;
SELECT user, host FROM mysql.user;
```
- **SHOW DATABASES;** : Liste toutes les bases présentes.
- **SELECT user, host FROM mysql.user;** : Liste les utilisateurs et leurs permissions.
- **Astuce** : Chercher des bases "test", "backup", ou des utilisateurs avec accès depuis "%".

### 4. Enumération automatisée avec Nmap
```bash
nmap --script mysql-enum,mysql-users,mysql-databases -p 3306 target.com
```
- **mysql-enum** : Enumère les bases, utilisateurs, variables.
- **mysql-users** : Tente de lister les utilisateurs.
- **mysql-databases** : Tente de lister les bases.
- **Exemple de sortie** :
```
| mysql-enum: 
|   databases: information_schema, test, prod
|   users: root@%, admin@localhost
```
- **À surveiller** : Bases sensibles, utilisateurs avec accès large.

### 5. Enumération PostgreSQL et MSSQL
- Utiliser `psql` (PostgreSQL) ou `sqsh`/`mssqlclient.py` (MSSQL) pour tester les accès et lister les bases.
- Scripts Nmap : `pgsql-brute`, `ms-sql-info`, `ms-sql-empty-password`, etc.

---

## Conseils pour débutants
- Toujours tester les accès par défaut avant d'essayer des credentials complexes.
- Chercher des bases "test", "backup", ou des utilisateurs "admin", "root".
- Utiliser Nmap pour automatiser la détection et l'énumération.
- Lire la documentation de chaque outil pour découvrir des options avancées.

---

## Pour aller plus loin
- [PayloadsAllTheThings - Database](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/Database%20Methodology)
- [Nmap Database NSE Scripts](https://nmap.org/nsedoc/categories/database.html) 