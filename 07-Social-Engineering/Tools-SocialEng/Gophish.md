# Gophish - Plateforme de Phishing Open Source

## 🗂️ Workflow de campagne Gophish
1. Installation et configuration de la plateforme
   ↓
2. Création de templates d'emails et pages
   ↓
3. Configuration des groupes cibles
   ↓
4. Lancement et monitoring de campagne
   ↓
5. Analyse des résultats et reporting
   ↓
6. Formation et sensibilisation

## 📋 Vue d'ensemble

Gophish est une plateforme open source conçue pour les tests de phishing et la sensibilisation à la sécurité. Elle permet de créer, gérer et analyser des campagnes de phishing dans un cadre éthique et légal.

> **💡 Explication Simple** : C'est comme un simulateur de vol pour le phishing - il permet de tester les réactions des utilisateurs face aux tentatives d'hameçonnage sans danger réel.

## ⚡ Installation et Configuration

### Installation Docker
```bash
# Via Docker (recommandé)
docker pull gophish/gophish
docker run -d -p 3333:3333 -p 8080:8080 --name gophish gophish/gophish

# Accès interface web
https://localhost:3333
# User: admin, Password: (généré aléatoirement)
```

### Installation manuelle
```bash
# Download release
wget https://github.com/gophish/gophish/releases/latest/download/gophish-linux-64bit.zip
unzip gophish-linux-64bit.zip
chmod +x gophish

# Configuration SSL (recommandé)
./gophish
```

### Configuration initiale
```json
// config.json
{
  "admin_server": {
    "listen_url": "0.0.0.0:3333",
    "use_tls": true,
    "cert_path": "gophish_admin.crt",
    "key_path": "gophish_admin.key"
  },
  "phish_server": {
    "listen_url": "0.0.0.0:8080",
    "use_tls": true,
    "cert_path": "phishing.crt",
    "key_path": "phishing.key"
  }
}
```

## 🎯 Création de Templates

### Email Templates
```html
<!-- Template email professionnel -->
Subject: [URGENT] Mise à jour de sécurité requise

<html>
<body>
<div style="font-family: Arial, sans-serif;">
    <img src="{{.URL}}/static/images/logo.png" alt="Logo Entreprise">
    
    <h2>Mise à jour de sécurité critique</h2>
    
    <p>Cher {{.FirstName}} {{.LastName}},</p>
    
    <p>Notre équipe de sécurité a détecté une activité suspecte sur votre compte. 
    Pour des raisons de sécurité, nous devons vérifier votre identité.</p>
    
    <p><a href="{{.URL}}" style="background: #0066cc; color: white; padding: 10px 20px; text-decoration: none;">
    Vérifier mon compte maintenant</a></p>
    
    <p>Ce lien expire dans 24 heures.</p>
    
    <p>Cordialement,<br>
    L'équipe de sécurité</p>
    
    {{.Tracker}}
</div>
</body>
</html>
```

### Landing Pages
```html
<!-- Page de capture credentials -->
<!DOCTYPE html>
<html>
<head>
    <title>Connexion - Portail Entreprise</title>
    <style>
        body { font-family: Arial, sans-serif; background: #f5f5f5; }
        .login-box { 
            width: 400px; margin: 100px auto; 
            background: white; padding: 30px; 
            border-radius: 5px; box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        input[type="text"], input[type="password"] {
            width: 100%; padding: 12px; margin: 8px 0;
            border: 1px solid #ddd; border-radius: 4px;
        }
        .btn { 
            background: #0066cc; color: white; 
            padding: 12px 20px; border: none; 
            border-radius: 4px; cursor: pointer; width: 100%;
        }
    </style>
</head>
<body>
    <div class="login-box">
        <img src="/static/images/company-logo.png" alt="Logo" style="width: 200px;">
        <h2>Connexion sécurisée</h2>
        
        <form method="post" action="/submit">
            <input type="text" name="username" placeholder="Nom d'utilisateur" required>
            <input type="password" name="password" placeholder="Mot de passe" required>
            <button type="submit" class="btn">Se connecter</button>
        </form>
        
        <p><small>Votre connexion est sécurisée et chiffrée.</small></p>
    </div>
    
    {{.Tracker}}
</body>
</html>
```

## 📊 Gestion des Groupes Cibles

### Import CSV
```csv
First Name,Last Name,Email,Position
John,Doe,john.doe@company.com,Manager
Jane,Smith,jane.smith@company.com,Developer
Bob,Johnson,bob.johnson@company.com,HR Director
```

### API pour import massif
```python
import requests
import json

# Configuration API Gophish
api_key = "your_api_key_here"
base_url = "https://localhost:3333"
headers = {"Authorization": f"Bearer {api_key}"}

# Création groupe
group_data = {
    "name": "IT Department",
    "targets": [
        {
            "first_name": "John",
            "last_name": "Doe", 
            "email": "john.doe@company.com",
            "position": "IT Manager"
        }
    ]
}

response = requests.post(
    f"{base_url}/api/groups/",
    headers=headers,
    json=group_data,
    verify=False
)
```

## 🚀 Configuration SMTP

### SMTP interne
```json
{
  "name": "Internal SMTP",
  "interface_type": "SMTP",
  "host": "mail.company.com:587",
  "username": "phishing@company.com",
  "password": "secure_password",
  "from_address": "security@company.com",
  "ignore_cert_errors": false
}
```

### SMTP externe (développement)
```json
{
  "name": "Mailgun",
  "interface_type": "SMTP", 
  "host": "smtp.mailgun.org:587",
  "username": "postmaster@sandbox123.mailgun.org",
  "password": "api_key",
  "from_address": "noreply@legitimate-company.com"
}
```

## 📈 Lancement et Monitoring

### Configuration campagne
```json
{
  "name": "Q1 Security Awareness Test",
  "template": "urgent_security_update",
  "page": "office365_login",
  "smtp": "internal_smtp",
  "groups": ["it_department", "finance_team"],
  "launch_date": "2024-01-15T09:00:00Z",
  "send_by_date": "2024-01-15T17:00:00Z"
}
```

### Monitoring en temps réel
- **Emails envoyés** : Tracking de la délivrance
- **Emails ouverts** : Suivi des ouvertures via pixels
- **Liens cliqués** : Redirection vers landing page
- **Données soumises** : Capture des credentials
- **Emails signalés** : Détection des utilisateurs vigilants

## 🔍 Analyse des Résultats

### Métriques clés
```python
# Script d'analyse des résultats
import gophish

# Connexion API
api = gophish.Gophish(api_key, host='https://localhost:3333', verify=False)

# Récupération campagne
campaign = api.campaigns.get(campaign_id=1)

# Calcul métriques
total_targets = len(campaign.results)
emails_sent = sum(1 for r in campaign.results if r.status == "Email Sent")
emails_opened = sum(1 for r in campaign.results if r.status == "Email Opened") 
links_clicked = sum(1 for r in campaign.results if r.status == "Clicked Link")
data_submitted = sum(1 for r in campaign.results if r.status == "Submitted Data")

print(f"Taux d'ouverture: {(emails_opened/emails_sent)*100:.1f}%")
print(f"Taux de clic: {(links_clicked/emails_sent)*100:.1f}%")
print(f"Taux de soumission: {(data_submitted/emails_sent)*100:.1f}%")
```

### Rapport détaillé
```html
<!-- Template de rapport -->
<h2>Résultats Campagne de Sensibilisation</h2>

<div class="metrics">
    <div class="metric">
        <h3>{{.EmailsSent}}</h3>
        <p>Emails envoyés</p>
    </div>
    <div class="metric">
        <h3>{{.OpenRate}}%</h3>
        <p>Taux d'ouverture</p>
    </div>
    <div class="metric">
        <h3>{{.ClickRate}}%</h3>
        <p>Taux de clic</p>
    </div>
    <div class="metric">
        <h3>{{.SubmissionRate}}%</h3>
        <p>Données soumises</p>
    </div>
</div>

<h3>Recommandations</h3>
<ul>
    <li>Formation additionnelle pour les départements vulnérables</li>
    <li>Mise en place de bannières d'avertissement</li>
    <li>Tests de phishing plus fréquents</li>
</ul>
```

## 🛡️ Considérations OPSEC

### Légalité et éthique
- **Autorisation écrite** obligatoire avant toute campagne
- **Notification DPO** pour conformité RGPD
- **Périmètre défini** clairement dans les contrats
- **Objectifs pédagogiques** prioritaires sur les tests

### Sécurité infrastructure
```bash
# Isolation réseau
iptables -A INPUT -p tcp --dport 3333 -s trusted_network -j ACCEPT
iptables -A INPUT -p tcp --dport 3333 -j DROP

# HTTPS obligatoire
openssl req -x509 -newkey rsa:4096 -keyout gophish.key -out gophish.crt -days 365

# Backup sécurisé
tar -czf gophish_backup_$(date +%Y%m%d).tar.gz gophish.db static/ templates/
gpg -c gophish_backup_$(date +%Y%m%d).tar.gz
```

## ⚠️ Erreurs fréquentes

### Configuration
- Oublier la configuration HTTPS (données sensibles)
- Utiliser des domaines trop évidents (security-test.com)
- Ne pas tester les templates avant lancement
- Ignorer la configuration SPF/DKIM/DMARC

### Campagne
- Cibler trop d'utilisateurs simultanément
- Lancer pendant les périodes critiques business
- Ne pas prévoir de canal de signalement
- Oublier la phase de débriefing pédagogique

## 💡 Astuces

### Templates réalistes
- Copier le design exact des emails légitimes internes
- Utiliser les logos et signatures authentiques
- Adapter le ton et vocabulaire de l'organisation
- Intégrer des éléments d'urgence crédibles

### Amélioration continue
```python
# Script d'optimisation des templates
def analyze_campaign_success(campaign_results):
    # Analyse par département
    dept_stats = {}
    for result in campaign_results:
        dept = result.position.split()[0]  # Premier mot du poste
        if dept not in dept_stats:
            dept_stats[dept] = {'total': 0, 'clicked': 0}
        dept_stats[dept]['total'] += 1
        if result.status == "Clicked Link":
            dept_stats[dept]['clicked'] += 1
    
    # Recommandations personnalisées
    for dept, stats in dept_stats.items():
        click_rate = stats['clicked'] / stats['total']
        if click_rate > 0.3:
            print(f"{dept}: Formation urgente recommandée")
```

## 🔗 Pour aller plus loin

- [Gophish Documentation](https://docs.getgophish.com/)
- [NIST Phishing Guide](https://www.nist.gov/itl/applied-cybersecurity/nice/resources/phishing-simulation-tools)
- [SANS Security Awareness](https://www.sans.org/security-awareness-training/)
- [Open Source Phishing Framework](https://github.com/gophish/gophish)

## 🧭 Navigation

- [Guide Social Engineering SET](./SET.md)
- [Guide King Phisher](./King-Phisher.md)
- [Retour aux outils social engineering](./README.md)
- [Social Engineering général](../Guide-Ingenierie-Sociale.md) 