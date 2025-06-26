# 08-Cloud-Security

## ☁️ Vue d'ensemble

La sécurité cloud couvre l'évaluation des infrastructures et services cloud modernes. Avec la migration massive vers le cloud, cette discipline devient critique pour identifier les mauvaises configurations et vulnérabilités spécifiques aux environnements cloud.

## 🗂️ Structure

### AWS/
- **IAM_Enumeration.md** - Énumération des permissions AWS
- **S3_Bucket_Testing.md** - Tests de sécurité S3
- **EC2_Assessment.md** - Évaluation des instances EC2
- **Lambda_Security.md** - Sécurité des fonctions serverless
- **CloudTrail_Analysis.md** - Analyse des logs CloudTrail

### Azure/
- **Azure_AD_Enum.md** - Énumération Azure Active Directory
- **Storage_Account_Testing.md** - Tests des comptes de stockage
- **VM_Assessment.md** - Évaluation des machines virtuelles
- **Function_Apps.md** - Sécurité des Azure Functions
- **Resource_Group_Analysis.md** - Analyse des groupes de ressources

### GCP/
- **GCP_IAM_Testing.md** - Tests IAM Google Cloud
- **Storage_Bucket_Assessment.md** - Évaluation des buckets GCS
- **Compute_Engine_Security.md** - Sécurité Compute Engine
- **Cloud_Functions.md** - Tests des Cloud Functions
- **BigQuery_Security.md** - Sécurité BigQuery

### Docker-Containers/
- **Container_Escape.md** - Techniques d'évasion de conteneurs
- **Docker_Misconfiguration.md** - Mauvaises configurations Docker
- **Registry_Security.md** - Sécurité des registres Docker
- **Runtime_Security.md** - Sécurité à l'exécution

### Kubernetes/
- **K8s_Enumeration.md** - Énumération des clusters Kubernetes
- **RBAC_Testing.md** - Tests du contrôle d'accès
- **Pod_Security.md** - Sécurité des pods
- **Network_Policies.md** - Tests des politiques réseau
- **Secrets_Management.md** - Gestion des secrets

### _Tools/
- **ScoutSuite.md** - Audit multi-cloud
- **Prowler.md** - Audit de sécurité AWS
- **CloudMapper.md** - Cartographie cloud
- **Pacu.md** - Framework d'exploitation AWS
- **Kubectl.md** - Commandes Kubernetes essentielles

## 🎯 Méthodologie Cloud Security

### Phase 1 : Reconnaissance Cloud
```bash
# Énumération AWS
aws sts get-caller-identity
aws s3 ls

# Énumération Azure
az account show
az storage account list

# Énumération GCP
gcloud auth list
gcloud projects list
```

### Phase 2 : Audit de Configuration
- Vérification des politiques IAM/RBAC
- Audit des configurations de stockage
- Analyse des groupes de sécurité/règles firewall
- Vérification du chiffrement

### Phase 3 : Tests d'Exploitation
- Escalade de privilèges cloud
- Accès non autorisé aux ressources
- Exploitation de mauvaises configurations
- Tests d'évasion de conteneurs

### Phase 4 : Évaluation de l'Impact
- Cartographie des accès obtenus
- Évaluation des données accessibles
- Tests de mouvement latéral
- Documentation des risques

## 🔐 Modèles de Sécurité Cloud

### Responsabilité Partagée
```
┌─────────────────────────────────────┐
│ FOURNISSEUR CLOUD                   │
├─────────────────────────────────────┤
│ • Infrastructure physique           │
│ • Hyperviseur                       │
│ • Services gérés                    │
└─────────────────────────────────────┘
┌─────────────────────────────────────┐
│ CLIENT                              │
├─────────────────────────────────────┤
│ • Configuration des services        │
│ • Gestion des identités            │
│ • Chiffrement des données          │
│ • Contrôles d'accès                │
└─────────────────────────────────────┘
```

### Principaux Risques
1. **Mauvaises configurations** - 95% des incidents
2. **Gestion des identités** - Permissions excessives
3. **Exposition de données** - Buckets/bases publiques
4. **Interfaces non sécurisées** - APIs exposées
5. **Insider threats** - Accès privilégiés

## ☁️ AWS Security

### Services Clés à Tester
- **IAM** - Identity and Access Management
- **S3** - Simple Storage Service
- **EC2** - Elastic Compute Cloud
- **RDS** - Relational Database Service
- **Lambda** - Serverless computing
- **CloudTrail** - Logging et audit

### Outils AWS
```bash
# ScoutSuite pour audit complet
scout aws

# Prowler pour tests de sécurité
prowler -g cislevel2

# Pacu pour exploitation
pacu
```

## 🔷 Azure Security

### Services Clés
- **Azure AD** - Active Directory
- **Storage Accounts** - Stockage Azure
- **Virtual Machines** - Machines virtuelles
- **Key Vault** - Gestion des secrets
- **Security Center** - Centre de sécurité

### Commandes Azure
```bash
# Énumération des ressources
az resource list --output table

# Test des permissions
az role assignment list --assignee user@domain.com

# Audit des configurations
az security assessment list
```

## 🌐 GCP Security

### Services Clés
- **Cloud IAM** - Gestion des identités
- **Cloud Storage** - Stockage cloud
- **Compute Engine** - Instances de calcul
- **Cloud SQL** - Base de données gérée
- **Security Command Center** - Centre de sécurité

## 🐳 Container Security

### Vulnérabilités Communes
- **Escape containers** - Évasion vers l'hôte
- **Privilèges excessifs** - Conteneurs privilégiés
- **Secrets exposés** - Variables d'environnement
- **Images vulnérables** - CVE dans les images

### Tests Docker
```bash
# Vérification des configurations
docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock \
  aquasec/docker-bench-security

# Analyse d'images
trivy image nginx:latest

# Tests de sécurité runtime
falco
```

## ⚙️ Kubernetes Security

### Vecteurs d'Attaque
- **RBAC faible** - Permissions trop larges
- **Network policies** - Segmentation insuffisante
- **Secrets management** - Secrets mal protégés
- **Admission controllers** - Contrôles manquants

### Tests K8s
```bash
# Énumération des permissions
kubectl auth can-i --list

# Tests de sécurité
kube-bench run

# Audit des configurations
kube-score score pod.yaml
```

## 🔧 Outils Recommandés

### Multi-Cloud
- **ScoutSuite** - Audit multi-plateformes
- **CloudSploit** - Scanner de sécurité
- **Prowler** - Tests de conformité
- **CloudMapper** - Visualisation d'infrastructure

### Spécialisés
- **Pacu** - Framework AWS
- **ROADtools** - Tests Azure AD
- **GCP Scanner** - Audit Google Cloud
- **kube-hunter** - Tests Kubernetes

## 📚 Certifications Cloud Security

### AWS
- **AWS Certified Security - Specialty**
- **AWS Certified Solutions Architect**

### Azure
- **Azure Security Engineer Associate**
- **Azure Solutions Architect Expert**

### GCP
- **Google Professional Cloud Security Engineer**

### Généralistes
- **CCSP** - Certified Cloud Security Professional
- **GCLD** - GIAC Cloud Security Automation

---
*Section maintenue par : [Votre nom]*  
*Dernière mise à jour : [Date]* 