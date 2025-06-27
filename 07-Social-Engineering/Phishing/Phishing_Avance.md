# Phishing Avancé

> ⚠️ **Avertissement légal et éthique** :
> Les techniques décrites ici sont à but pédagogique et doivent être utilisées uniquement dans un cadre légal (lab, test autorisé). Le phishing est illégal sans consentement explicite.

## Qu’est-ce que le phishing avancé ?
C’est l’art de tromper une cible en utilisant des scénarios élaborés, des techniques d’ingénierie sociale et parfois des outils techniques pour contourner les protections classiques (antispam, MFA, etc.).

## Exemples de scénarios
- **Spear phishing** : Email ciblé, personnalisé (ex : faux mail du service RH à un employé précis)
- **Phishing multi-étapes** : Email + faux site + relance téléphonique
- **Payloads dynamiques** : Lien qui ne s’active que si la cible utilise un certain navigateur/IP
- **Phishing via QR code** : Inciter à scanner un QR menant à un site piégé

## Techniques et outils
- Création de faux sites (clones) : `setoolkit`, `evilginx2`, `gophish`
- Bypass d’antispam : utilisation de services cloud, d’URL courtes, d’images cliquables
- Contournement de MFA : `evilginx2` (proxy reverse), attaques de consentement OAuth
- Utilisation de documents piégés (macro, OneNote, PDF interactif)

## Conseils pour la défense
- Sensibilisation des utilisateurs (formations, simulations)
- Vérification des URL, des expéditeurs, des pièces jointes
- Activation de l’authentification forte (MFA)

## Pour aller plus loin
- [Phishing Frenzy](https://github.com/pentestgeek/phishing-frenzy)
- [Evilginx2](https://github.com/kgretzky/evilginx2)
- [Gophish](https://getgophish.com/)

*Cette fiche sera enrichie avec de nouveaux scénarios et techniques. Propose tes idées !* 