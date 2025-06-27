# Linux Enumeration

## 🗂️ Workflow d'énumération Linux
1. Identification du système (uname, lsb_release, /etc/*-release)
   ↓
2. Enumération des utilisateurs et groupes (/etc/passwd, /etc/group)
   ↓
3. Enumération des services et processus (ps, systemctl, netstat, ss)
   ↓
4. Enumération des ports ouverts (netstat, ss, lsof)
   ↓
5. Enumération des tâches planifiées (crontab, /etc/cron*)
   ↓
6. Enumération des fichiers sensibles (/etc/shadow, clés SSH, sudoers)
   ↓
7. Enumération des failles connues (searchsploit, linpeas, linux-exploit-suggester)

---

## 🛡️ Conseils OPSEC
- Ne jamais modifier les fichiers système sans autorisation.
- Privilégier les commandes en lecture seule.
- Éviter les scans trop bruyants (find /, nmap local, etc.).
- Utiliser des scripts d'énumération discrètement (LinPEAS, lse).

## ⚠️ Erreurs fréquentes
- Oublier de vérifier les permissions sur les fichiers critiques.
- Négliger les tâches planifiées et les services cachés.
- Lancer des scripts d'énumération trop verbeux qui laissent des traces.
- Ne pas sauvegarder les résultats d'énumération pour l'exploitation.

## 💡 Astuces
- Utiliser LinPEAS pour une énumération automatisée et complète.
- Croiser les résultats de plusieurs outils (manual + script).
- Scripter l'extraction des informations clés pour gagner du temps.
- Vérifier les fichiers de configuration réseau (/etc/network/interfaces, resolv.conf).

## 🔗 Pour aller plus loin
- [PayloadsAllTheThings - Linux Enumeration](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources/Linux%20Privilege%20Escalation)
- [HackTricks - Linux](https://book.hacktricks.xyz/linux-hardening)
- [LinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS)
- [Linux Exploit Suggester](https://github.com/mzet-/linux-exploit-suggester)

## 🧭 Navigation
- [Énumération Windows](./Windows_Enumeration.md)
- [Énumération Active Directory](./Active_Directory.md)
- [Outils d'énumération](../Tools-Enumeration/)
- [Retour à la méthodologie générale](../Guide-Enumeration-Pentest.md) 