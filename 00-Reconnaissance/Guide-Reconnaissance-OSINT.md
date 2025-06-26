# 🔍 00-Reconnaissance - Guide Complet

## 📋 Vue d'ensemble
La reconnaissance est la phase fondamentale de toute opération de sécurité offensive. Cette section couvre toutes les techniques de collecte d'informations, des méthodes passives les plus discrètes aux techniques actives de découverte.

## 🎯 Objectifs de la reconnaissance
- **Cartographier la surface d'attaque** de la cible
- **Identifier les points d'entrée** potentiels
- **Collecter des informations** sur l'infrastructure et l'organisation
- **Préparer les phases suivantes** d'énumération et d'exploitation

## 🗂️ Structure de la section

### 📡 [Passive/](Passive/) - Reconnaissance Passive
Techniques de collecte d'informations sans interaction directe avec la cible.

#### 🕵️ [OSINT/](Passive/OSINT/)
- **[Whois.md](Passive/OSINT/Whois.md)** : Reconnaissance par bases de données d'enregistrement
- **[Google_Dorking.md](Passive/OSINT/Google_Dorking.md)** : Recherche avancée avec moteurs de recherche
- **[Shodan_Censys.md](Passive/OSINT/Shodan_Censys.md)** : Reconnaissance d'infrastructure avec moteurs spécialisés

#### 🌐 Analyse réseau passive
- **[Footprinting_Réseau.md](Passive/Footprinting_Réseau.md)** : Cartographie d'infrastructure sans détection

---

### 🎯 [Active/](Active/) - Reconnaissance Active
Techniques impliquant une interaction directe avec les systèmes cibles.

#### 🔍 [Discovery/](Active/Discovery/)
- **[ICMP_PingSweep.md](Active/Discovery/ICMP_PingSweep.md)** : Découverte d'hôtes via ICMP
- **[ARP_Scanning.md](Active/Discovery/ARP_Scanning.md)** : Découverte sur réseaux locaux

#### 🔌 [Port-Scanning/](Active/Port-Scanning/)
- **[Nmap_Bases.md](Active/Port-Scanning/Nmap_Bases.md)** : Fondamentaux du port scanning
- **[Masscan_Rapide.md](Active/Port-Scanning/Masscan_Rapide.md)** : Scanning haute vitesse
- **[Analyse_Résultats.md](Active/Port-Scanning/Analyse_Résultats.md)** : Interprétation et traitement des données

---

### 🛠️ [_Tools/](_Tools/) - Outils Spécialisés
Guides pratiques des outils essentiels pour la reconnaissance terrain.

- **[Nmap.md](_Tools/Nmap.md)** : Guide terrain optimisé ↔️ [Référence complète](../08-Cours/04-Outils_Généraux/Nmap.md)
- **[Masscan.md](_Tools/Masscan.md)** : Utilisation pratique pour reconnaissance rapide
- **[Amass.md](_Tools/Amass.md)** : Découverte de subdomains et OSINT

## 🚀 Progression recommandée

### 🟢 Niveau Débutant
1. **Commencer par la théorie** : [📚 08-Cours/02-Réseaux/](../08-Cours/02-Réseaux/)
2. **Reconnaissance passive** : [OSINT/Whois.md](Passive/OSINT/Whois.md)
3. **Premier scan** : [Active/Discovery/ICMP_PingSweep.md](Active/Discovery/ICMP_PingSweep.md)
4. **Outils de base** : [_Tools/Nmap.md](_Tools/Nmap.md)

### 🟡 Niveau Intermédiaire
1. **Techniques OSINT avancées** : [Google_Dorking.md](Passive/OSINT/Google_Dorking.md) + [Shodan_Censys.md](Passive/OSINT/Shodan_Censys.md)
2. **Scanning méthodique** : [Port-Scanning/Nmap_Bases.md](Active/Port-Scanning/Nmap_Bases.md)
3. **Analyse experte** : [Analyse_Résultats.md](Active/Port-Scanning/Analyse_Résultats.md)
4. **Automation** : [_Tools/Amass.md](_Tools/Amass.md)

### 🔴 Niveau Avancé
1. **Scanning à grande échelle** : [Masscan_Rapide.md](Active/Port-Scanning/Masscan_Rapide.md)
2. **Footprinting complet** : [Footprinting_Réseau.md](Passive/Footprinting_Réseau.md)
3. **Techniques d'évasion** : Sections avancées des guides outils
4. **Workflows personnalisés** : Développement de pipelines automatisés

## 📊 Méthodologie générale

### Phase 1: Reconnaissance Passive (OPSEC Maximum)
```bash
# 1. Collecte OSINT
whois target.com
amass enum -passive -d target.com

# 2. Recherche sur moteurs spécialisés
# Shodan, Censys, Google Dorking

# 3. Footprinting réseau
# ASN, BGP, Certificate Transparency
```

### Phase 2: Reconnaissance Active (Détectable)
```bash
# 1. Découverte d'hôtes
nmap -sn target_range

# 2. Port scanning
nmap -T4 -F discovered_hosts
masscan target_range -p top_ports --rate moderate

# 3. Analyse et priorisation
# Traitement des résultats pour phase d'énumération
```

## 🎯 Objectifs par sous-section

### Reconnaissance Passive
- ✅ **Zéro détection** : Aucune trace sur les systèmes cibles
- ✅ **Collecte maximale** : Exploitation de toutes les sources publiques
- ✅ **Intelligence** : Compréhension de l'organisation et de l'infrastructure

### Reconnaissance Active
- ✅ **Cartographie précise** : Identification exhaustive des assets
- ✅ **Optimisation** : Équilibre entre vitesse et discrétion
- ✅ **Préparation** : Données structurées pour les phases suivantes

## 🛡️ Considérations OPSEC

### Reconnaissance Passive ✅
- Aucun trafic vers la cible
- Utilisation de sources tierces uniquement
- Historique consultable mais non attribuable

### Reconnaissance Active ⚠️
- **Détectable** dans les logs réseau/sécurité
- **Timing crucial** : Éviter les patterns suspects
- **Attribution** : Masquer/distribuer les sources

## 🔄 Intégration avec autres phases

### ➡️ Vers [01-Enumeration](../01-Enumeration/)
- Listes d'hôtes et services découverts
- Priorisation des cibles selon criticité
- Vectors d'attaque identifiés

### ➡️ Vers [02-Exploitation](../02-Exploitation/)
- Services vulnérables détectés
- Versions de logiciels obsolètes
- Points d'entrée potentiels

### ➡️ Vers [05-Red-Team](../05-Red-Team/)
- Intelligence pour social engineering
- Infrastructure pour C2
- OPSEC pour opérations long-terme

## 📈 Métriques de succès

### Métriques quantitatives
- **Nombre d'assets découverts** vs surface réelle
- **Services identifiés** avec versions
- **Subdomains/IPs** collectés par rapport au périmètre

### Métriques qualitatives
- **Précision** des informations collectées
- **Pertinence** pour les phases suivantes
- **Discrétion** (absence de détection)

## 🔧 Outils complémentaires

### Reconnaissance Passive
- `theHarvester` : Collecte d'emails et subdomains
- `recon-ng` : Framework de reconnaissance modulaire
- `maltego` : Analyse de liens et corrélations

### Reconnaissance Active
- `zmap` : Scanning Internet à grande échelle
- `unicornscan` : Scanner asynchrone alternatif
- `hping3` : Génération de paquets personnalisés

## 📚 Ressources d'apprentissage

### Documentation technique
- [NIST SP 800-115](https://csrc.nist.gov/publications/detail/sp/800-115/final) : Guide technique des tests de sécurité
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/) : Méthodologies d'audit web
- [PTES](http://www.pentest-standard.org/) : Standard de tests de pénétration

### Ressources pratiques
- [SecLists](https://github.com/danielmiessler/SecLists) : Listes pour reconnaissance
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) : Techniques et payloads
- [HackerOne Hacktivity](https://hackerone.com/hacktivity) : Cas pratiques de reconnaissance

## ⚖️ Aspects légaux

### 🚨 RAPPEL IMPORTANT
```
⚠️  La reconnaissance active peut être considérée comme intrusive
    selon les juridictions et contextes.

✅  TOUJOURS obtenir une autorisation écrite avant toute activité
    de reconnaissance active sur des systèmes tiers.

📋  Documenter toutes les activités pour justification légale.
```

### Bonnes pratiques
- **Périmètre clairement défini** dans l'autorisation
- **Exclusions explicites** (systèmes critiques, tiers)
- **Fenêtres temporelles** respectées
- **Points de contact** pour incidents

---

## 🎉 Commencer maintenant

### Installation rapide des outils
```bash
# Outils essentiels
sudo apt update && sudo apt install -y nmap masscan amass whois dnsutils

# Vérification
nmap --version
masscan --version
amass version
```

### Premier workflow
```bash
# 1. Reconnaissance passive
amass enum -passive -d target.com -o subdomains.txt

# 2. Résolution DNS
cat subdomains.txt | while read domain; do dig +short $domain; done | sort -u > ips.txt

# 3. Scan initial
nmap -sn -iL ips.txt -oA host_discovery

# 4. Port scan rapide
nmap -T4 -F -iL ips.txt -oA port_scan_initial
```

---

**🎯 La reconnaissance de qualité détermine le succès de toute l'opération !**

*Progression recommandée : Passive → Active → Analyse → [Énumération](../01-Enumeration/)* 