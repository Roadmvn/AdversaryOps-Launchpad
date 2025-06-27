# Tools-Reconnaissance - Outils de Reconnaissance

## 📋 Vue d'ensemble

Ce dossier contient les guides pratiques des outils spécialisés pour la reconnaissance passive et active, OSINT, et découverte d'infrastructures cibles.

## 🛠️ Outils Disponibles

### Scanning de Ports et Services
- **[Nmap.md](Nmap.md)** - Scanner de ports et services de référence
- **[Masscan.md](Masscan.md)** - Scanner haute vitesse pour grandes infrastructures

### OSINT et Reconnaissance Passive
- **[Amass.md](Amass.md)** - Découverte de sous-domaines et cartographie d'infrastructure
- **theHarvester** - Collecte d'emails, sous-domaines, IPs (à venir)
- **Recon-ng** - Framework de reconnaissance modulaire (à venir)

### Reconnaissance DNS
- **DNSrecon** - Énumération DNS avancée (à venir)
- **Fierce** - Scanner de domaines et sous-domaines (à venir)
- **DNSenum** - Énumération DNS complète (à venir)

## 🔄 Workflow Standard

1. **Reconnaissance passive** → [OSINT et collecte publique](../Passive/OSINT/)
2. **Découverte d'infrastructure** → [Amass](Amass.md) + [theHarvester]
3. **Scanning initial** → [Masscan](Masscan.md) pour découverte rapide
4. **Énumération détaillée** → [Nmap](Nmap.md) pour services et versions
5. **Validation et documentation** → Transition vers [énumération](../../01-Enumeration/)

## 🎯 Utilisation par Phase

### Phase 1 : Collecte Passive
- [Amass](Amass.md) pour cartographier l'infrastructure
- OSINT via sources publiques
- Éviter tout contact direct avec la cible

### Phase 2 : Reconnaissance Active
- [Masscan](Masscan.md) pour discovery rapide sur grandes plages
- [Nmap](Nmap.md) pour énumération précise des services
- Validation des découvertes passives

### Phase 3 : Préparation Énumération
- Consolidation des résultats
- Priorisation des cibles identifiées
- Transition vers phase d'énumération détaillée

## 📚 Guides Associés

### Méthodologies
- [Guide reconnaissance générale](../Guide-Reconnaissance-OSINT.md)
- [Reconnaissance passive](../Passive/)
- [Reconnaissance active](../Active/)

### Phases suivantes
- [Énumération des services](../../01-Enumeration/Tools-Enumeration/)
- [Exploitation](../../02-Exploitation/Tools-Exploitation/)

## 🛡️ Conseils OPSEC

### Reconnaissance Passive
- Privilégier toujours les sources OSINT en premier
- Utiliser des VPNs et proxies pour masquer l'origine
- Respecter les robots.txt et politiques des sites
- Éviter les requêtes répétitives suspectes

### Reconnaissance Active
- Utiliser des techniques de scan distribuées
- Varier les sources IP et timing des scans
- Respecter les bandes passantes des cibles
- Documenter toutes les activités pour reporting

## ⚠️ Bonnes Pratiques

1. **Toujours** commencer par la reconnaissance passive
2. **Documenter** toutes les découvertes méthodiquement
3. **Respecter** les limitations légales et éthiques
4. **Valider** les informations par recoupement
5. **Protéger** les données collectées sensibles

## 🔗 Navigation

- [⬅️ Retour à la reconnaissance](../Guide-Reconnaissance-OSINT.md)
- [🕵️ Énumération](../../01-Enumeration/Tools-Enumeration/)
- [🏠 Accueil du projet](../../README.md) 