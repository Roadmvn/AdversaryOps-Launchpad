# Tools-PrivEsc - Outils d'Escalade de Privilèges

## 📋 Vue d'ensemble

Ce dossier contient les guides pratiques des outils spécialisés pour l'escalade de privilèges sur systèmes Linux et Windows, l'énumération automatisée et l'exploitation de vulnérabilités locales.

## 🛠️ Outils Disponibles

### Scripts d'Énumération Linux
- **[LinPEAS.md](LinPEAS.md)** - Script d'énumération Linux automatisé
- **Linux Exploit Suggester** - Suggestion d'exploits kernel (à venir)
- **pspy** - Monitoring de processus sans privilèges (à venir)

### Scripts d'Énumération Windows
- **[WinPEAS.md](WinPEAS.md)** - Script d'énumération Windows automatisé
- **PowerUp** - Module PowerShell pour escalade Windows (à venir)
- **Sherlock** - Détection de patches manquants (à venir)

### Outils Multi-Plateformes
- **JAWS** - Just Another Windows Script (à venir)
- **PrivescCheck** - Script PowerShell d'énumération (à venir)
- **PEASS-ng** - Suite PEASS complète (à venir)

### Exploits et Techniques
- **GTFOBins** - Binaires Unix exploitables (référence)
- **LOLBAS** - Living Off The Land Binaries Windows (référence)
- **Kernel Exploits** - Collection d'exploits locaux (à venir)

## 🔄 Workflow Standard

1. **Énumération système** → [LinPEAS](LinPEAS.md) ou [WinPEAS](WinPEAS.md)
2. **Analyse des résultats** → Priorisation par couleur/criticité
3. **Recherche d'exploits** → CVE et techniques spécifiques
4. **Test d'exploitation** → Validation en environnement sûr
5. **Exploitation ciblée** → Obtention privilèges élevés
6. **Validation escalade** → Confirmation nouveaux privilèges

## 🎯 Utilisation par Système

### Linux Systems
- [LinPEAS](LinPEAS.md) pour énumération complète
- pspy pour monitoring processus
- Exploits kernel selon version détectée
- Techniques SUID/Capabilities/Sudo

### Windows Systems
- [WinPEAS](WinPEAS.md) pour découverte vulnérabilités
- PowerUp pour tests PowerShell
- Exploits locaux selon version Windows
- Techniques UAC bypass et token manipulation

### Active Directory
- BloodHound pour cartographie
- PowerView pour énumération AD
- Kerberoasting et ASREPRoasting
- DCSync et Golden Ticket attacks

## 📚 Guides Associés

### Méthodologies spécialisées
- [Escalade Linux](../Linux/)
- [Escalade Windows](../Windows/)
- [Techniques génériques](../../02-Exploitation/Systèmes/)

### Phases connexes
- [Énumération système](../../01-Enumeration/Systèmes/)
- [Post-exploitation](../../03-Post-Exploitation/Tools-PostExploitation/)
- [Red Team operations](../../05-Red-Team/)

## 🛡️ Conseils OPSEC

### Énumération discrète
- Exécuter les scripts durant les heures de maintenance
- Utiliser des versions obfusquées pour éviter la détection
- Limiter les scans agressifs sur systèmes de production
- Nettoyer immédiatement les artefacts

### Exploitation prudente
- Tester d'abord en environnement isolé
- Sauvegarder avant modifications système critiques
- Éviter les exploits instables pouvant crasher le système
- Documenter toutes les modifications pour rollback

## ⚠️ Précautions Critiques

### Stabilité système
1. **Toujours** tester les exploits en lab d'abord
2. **Éviter** les modifications irréversibles
3. **Sauvegarder** les configurations avant changements
4. **Prévoir** des mécanismes de rollback
5. **Monitorer** la stabilité système post-exploitation

### Légalité et éthique
- Respecter strictement le périmètre autorisé
- Documenter toutes les actions pour audit
- Éviter l'accès aux données sensibles non nécessaires
- Nettoyer complètement après tests

## 💡 Astuces d'Optimisation

### Automation
- Scripter l'exécution séquentielle des outils
- Parser automatiquement les résultats par priorité
- Intégrer dans frameworks de pentest (Metasploit, Empire)
- Créer des rapports automatisés

### Techniques avancées
- Combiner plusieurs outils pour validation croisée
- Utiliser des exploits 0-day en dernier recours
- Implémenter des techniques de bypass EDR/AV
- Développer des exploits personnalisés si nécessaire

## 🔗 Navigation

- [⬅️ Retour à l'escalade de privilèges](../README.md)
- [🕵️ Énumération système](../../01-Enumeration/Systèmes/)
- [🎭 Post-exploitation](../../03-Post-Exploitation/Tools-PostExploitation/)
- [🔴 Red Team](../../05-Red-Team/Tools-RedTeam/)
- [🏠 Accueil du projet](../../README.md) 