# Reconnaissance Whois

## 📋 Vue d'ensemble
Le Whois est une technique de reconnaissance passive essentielle pour collecter des informations sur les domaines, adresses IP et entités associées.

## 🎯 Objectifs
- Identifier les propriétaires de domaines
- Découvrir les serveurs de noms
- Obtenir des informations de contact
- Analyser l'historique des enregistrements

## 🛠️ Outils recommandés
- `whois` (ligne de commande)
- Sites web spécialisés (whois.net, etc.)
- Scripts personnalisés

## 📝 Techniques de base

### Requête whois simple
```bash
whois example.com
whois 8.8.8.8
```

### Analyse des résultats
- **Registrant** : Propriétaire du domaine
- **Admin Contact** : Contact administratif
- **Tech Contact** : Contact technique
- **Name Servers** : Serveurs DNS
- **Creation/Expiration** : Dates importantes

## 🔍 Informations exploitables
- Emails des contacts (pour social engineering)
- Infrastructure réseau
- Fournisseurs de services
- Historique et patterns

## ⚠️ Limitations et considérations
- Informations parfois masquées (privacy protection)
- Données pas toujours à jour
- Certains registrars limitent les requêtes

## 📚 Références
- RFC 3912 (WHOIS Protocol Specification)
- IANA WHOIS Service 