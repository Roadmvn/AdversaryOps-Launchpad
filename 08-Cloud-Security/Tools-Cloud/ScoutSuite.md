# ScoutSuite - Audit de Sécurité Cloud Multi-Plateformes

## 🗂️ Workflow d'audit cloud avec ScoutSuite
1. Configuration des credentials cloud (AWS/Azure/GCP)
   ↓
2. Lancement de l'audit automatisé
   ↓
3. Analyse du rapport HTML généré
   ↓
4. Priorisation des vulnérabilités détectées
   ↓
5. Implémentation des correctifs recommandés
   ↓
6. Suivi et monitoring continu

## 📋 Vue d'ensemble

ScoutSuite est un outil open source d'audit de sécurité pour les environnements cloud. Il analyse automatiquement les configurations AWS, Azure, GCP, Oracle Cloud et Alibaba Cloud pour identifier les vulnérabilités et mauvaises pratiques.

> **💡 Explication Simple** : C'est comme un scanner de sécurité qui vérifie automatiquement toutes les configurations de votre infrastructure cloud pour trouver les portes mal fermées et les réglages dangereux.

## ⚡ Installation et Configuration

### Installation via pip
```bash
# Installation standard
pip3 install scoutsuite

# Installation développement
git clone https://github.com/nccgroup/ScoutSuite.git
cd ScoutSuite
pip3 install -r requirements.txt
python3 scout.py --help
```

### Configuration Docker
```bash
# Via Docker
docker pull nccgroup/scoutsuite

# Exécution avec volumes
docker run -v ~/.aws:/root/.aws \
           -v $(pwd)/report:/opt/scoutsuite-report \
           nccgroup/scoutsuite aws
```

## ☁️ Configuration par Provider

### AWS Configuration
```bash
# Configuration AWS CLI
aws configure
# AWS Access Key ID: AKIA...
# AWS Secret Access Key: ...
# Default region: us-east-1

# Ou via variables d'environnement
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
export AWS_DEFAULT_REGION="us-east-1"

# Audit AWS
python3 scout.py aws
```

### Azure Configuration
```bash
# Installation Azure CLI
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Authentification
az login

# Audit Azure
python3 scout.py azure --cli
```

### GCP Configuration
```bash
# Installation gcloud SDK
curl https://sdk.cloud.google.com | bash

# Authentification
gcloud auth login
gcloud auth application-default login

# Audit GCP
python3 scout.py gcp --user-account
```

## 🎯 Exécution d'Audits

### Audit AWS complet
```bash
# Audit standard toutes régions
python3 scout.py aws --force

# Audit régions spécifiques
python3 scout.py aws --regions us-east-1,eu-west-1

# Audit avec profil spécifique
python3 scout.py aws --profile production

# Audit services spécifiques
python3 scout.py aws --services iam,s3,ec2
```

### Audit Azure
```bash
# Audit subscription complète
python3 scout.py azure --cli

# Audit avec service principal
python3 scout.py azure --service-principal \
  --tenant-id YOUR_TENANT_ID \
  --client-id YOUR_CLIENT_ID \
  --client-secret YOUR_CLIENT_SECRET

# Audit subscription spécifique
python3 scout.py azure --subscription-ids SUBSCRIPTION_ID
```

### Audit GCP avancé
```bash
# Audit avec clés de service
python3 scout.py gcp --service-account service-account.json

# Audit projets spécifiques
python3 scout.py gcp --project-id my-project-123

# Audit avec quotas étendus
python3 scout.py gcp --max-workers 10
```

## 📊 Analyse des Résultats

### Structure du rapport
```
scoutsuite-report/
├── inc-scoutsuite/          # Assets CSS/JS
├── inc-awsconfig/           # Config AWS
├── scoutsuite-results/      # Données JSON
└── report.html              # Rapport principal
```

### Lecture du dashboard
- **🔴 Critical** : Vulnérabilités critiques nécessitant action immédiate
- **🟠 High** : Risques élevés à corriger rapidement  
- **🟡 Medium** : Améliorations de sécurité recommandées
- **🔵 Low** : Optimisations mineures

### Exemples de findings critiques
```json
// S3 buckets publics
{
  "description": "S3 bucket with public read access",
  "risk": "Critical",
  "service": "S3",
  "finding": "bucket-public-read-access",
  "affected_items": ["my-public-bucket"]
}

// Security groups trop ouverts
{
  "description": "Security group allows unrestricted access (0.0.0.0/0)",
  "risk": "High", 
  "service": "EC2",
  "finding": "security-group-unrestricted-access-ssh"
}
```

## 🔧 Customisation et Filtres

### Règles personnalisées
```python
# custom_rules.py
from ScoutSuite.providers.aws.rules.findings.finding import Finding

class CustomS3Finding(Finding):
    def __init__(self):
        super().__init__()
        self.description = "Custom S3 security check"
        
    def check_bucket_custom_policy(self, bucket):
        # Logique de vérification personnalisée
        if not bucket.get('custom_security_policy'):
            return True
        return False
```

### Configuration des filtres
```json
// filters.json
{
  "filters": {
    "s3": {
      "buckets": {
        "exclude": ["backup-*", "logs-*"],
        "include_tags": {"Environment": "production"}
      }
    },
    "ec2": {
      "instances": {
        "exclude_regions": ["ap-southeast-2"],
        "include_tags": {"Criticality": "high"}
      }
    }
  }
}
```

## 🛡️ Règles de Sécurité Analysées

### AWS Security Checks
```bash
# IAM
- Root account usage
- MFA disabled on root account
- Unused access keys
- Overprivileged policies
- Password policy compliance

# S3
- Public buckets
- Unencrypted buckets
- Bucket logging disabled
- Versioning disabled

# EC2
- Security groups too permissive
- Instances without security patches
- Unencrypted EBS volumes
- Public AMIs

# VPC
- Flow logs disabled
- Default security groups
- Unused network ACLs
```

### Azure Security Checks
```bash
# Active Directory
- Weak password policies
- Privileged users without MFA
- Guest user access
- Inactive users with access

# Storage
- Unencrypted storage accounts
- Public blob containers
- Missing access logs

# Virtual Machines
- Missing endpoint protection
- OS vulnerabilities
- Unencrypted disks

# Network Security
- Open network security groups
- No DDoS protection
- Missing WAF on App Gateway
```

## 📈 Intégration CI/CD

### GitHub Actions
```yaml
# .github/workflows/security-audit.yml
name: Cloud Security Audit

on:
  schedule:
    - cron: '0 6 * * 1'  # Lundi 6h
  workflow_dispatch:

jobs:
  security-audit:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - name: Setup Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.9'
    
    - name: Install ScoutSuite
      run: pip install scoutsuite
    
    - name: Run AWS Audit
      env:
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      run: |
        scout aws --force --report-dir ./reports
    
    - name: Upload Report
      uses: actions/upload-artifact@v2
      with:
        name: security-report
        path: ./reports/
```

### Jenkins Pipeline
```groovy
pipeline {
    agent any
    
    triggers {
        cron('H 6 * * 1')  // Hebdomadaire
    }
    
    stages {
        stage('Cloud Security Audit') {
            steps {
                script {
                    docker.image('nccgroup/scoutsuite').inside() {
                        withCredentials([aws(credentialsId: 'aws-creds')]) {
                            sh 'scout aws --force'
                        }
                    }
                }
            }
        }
        
        stage('Publish Report') {
            steps {
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'scoutsuite-report',
                    reportFiles: 'report.html',
                    reportName: 'Cloud Security Report'
                ])
            }
        }
    }
}
```

## 🔄 Automatisation et Monitoring

### Script de monitoring continu
```python
#!/usr/bin/env python3
import subprocess
import json
import smtplib
from email.mime.text import MIMEText
from datetime import datetime

def run_scout_audit(provider):
    """Exécute audit ScoutSuite"""
    cmd = f"scout {provider} --force --no-browser"
    result = subprocess.run(cmd, shell=True, capture_output=True)
    return result.returncode == 0

def parse_results(report_path):
    """Parse les résultats JSON"""
    with open(f"{report_path}/scoutsuite_results.json", 'r') as f:
        data = json.load(f)
    
    critical_findings = []
    for service, findings in data.get('findings', {}).items():
        for finding_type, details in findings.items():
            if details.get('level') == 'danger':
                critical_findings.append(f"{service}: {finding_type}")
    
    return critical_findings

def send_alert(findings):
    """Envoie alerte email si findings critiques"""
    if not findings:
        return
    
    msg = MIMEText(f"Critical security findings detected:\n" + 
                   "\n".join(findings))
    msg['Subject'] = f"[ALERT] Cloud Security Issues - {datetime.now()}"
    msg['From'] = "security@company.com"
    msg['To'] = "admin@company.com"
    
    smtp = smtplib.SMTP('localhost')
    smtp.send_message(msg)
    smtp.quit()

if __name__ == "__main__":
    providers = ['aws', 'azure', 'gcp']
    
    for provider in providers:
        print(f"Auditing {provider}...")
        if run_scout_audit(provider):
            findings = parse_results(f"scoutsuite-report")
            send_alert(findings)
        else:
            print(f"Audit failed for {provider}")
```

## ⚠️ Bonnes Pratiques

### Sécurité des credentials
```bash
# Permissions minimales requises
# AWS - ReadOnlyAccess + SecurityAudit
# Azure - Security Reader
# GCP - Security Reviewer

# Rotation régulière des clés
aws iam create-access-key --user-name scout-audit
aws iam delete-access-key --access-key-id OLD_KEY

# Credentials temporaires (STS)
aws sts assume-role --role-arn arn:aws:iam::123456789:role/SecurityAuditRole
```

### Gestion des rapports
- Stockage sécurisé des rapports (chiffrement)
- Contrôle d'accès strict aux résultats
- Archivage et rétention selon politique
- Anonymisation des données sensibles

## 💡 Astuces d'Optimisation

### Performance
```bash
# Audit parallèle multi-comptes
python3 scout.py aws --assume-role-list roles.json --max-workers 5

# Cache des résultats
python3 scout.py aws --profile prod --force --database-name prod-$(date +%Y%m%d)

# Audit incrémental
python3 scout.py aws --services-only s3,iam
```

### Personnalisation avancée
- Développement de règles métier spécifiques
- Intégration avec SIEM pour monitoring continu
- Automatisation des corrections via Infrastructure as Code
- Tableaux de bord personnalisés avec API

## 🔗 Pour aller plus loin

- [ScoutSuite Documentation](https://github.com/nccgroup/ScoutSuite)
- [AWS Security Best Practices](https://aws.amazon.com/architecture/security-identity-compliance/)
- [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/)
- [GCP Security Command Center](https://cloud.google.com/security-command-center)

## 🧭 Navigation

- [Guide Prowler](./Prowler.md)
- [Guide CloudGoat](./CloudGoat.md)
- [Retour aux outils cloud](./README.md)
- [Sécurité cloud générale](../Guide-Securite-Cloud.md) 