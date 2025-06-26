# Glossaire Linux - Terminologie Technique

## 📚 Vue d'ensemble

Ce glossaire définit **TOUS les termes techniques Linux** que vous rencontrerez dans les guides. Chaque terme est expliqué **simplement** avec des analogies du quotidien.

---

## 🐧 Système de Base

### **Kernel (Noyau)**
> **💭 Définition :** Le cœur du système Linux qui gère le matériel et les processus.
> **🧠 Analogie :** Le cerveau de l'ordinateur qui coordonne tout (CPU, mémoire, disques).

### **Distribution (Distro)**
> **💭 Définition :** Version complète de Linux avec noyau + programmes + gestionnaire de paquets.
> **📦 Analogie :** Comme différentes marques de voitures - même moteur (kernel) mais carrosserie et options différentes.

**Exemples :** Debian/Ubuntu, Red Hat/CentOS, Kali Linux, Arch Linux

### **Shell**
> **💭 Définition :** Interface en ligne de commande pour interagir avec le système.
> **🐚 Analogie :** La "coquille" qui vous permet de parler au système en tapant des commandes.

**Types :** bash, zsh, fish, sh

### **Root**
> **💭 Définition :** Super-utilisateur avec tous les pouvoirs sur le système.
> **👑 Analogie :** Le propriétaire de la maison qui peut tout faire, même casser les murs.

### **sudo**
> **💭 Définition :** Commande pour exécuter des actions en tant qu'autre utilisateur (souvent root).
> **🔑 Analogie :** Emprunter temporairement les clés du patron pour ouvrir son bureau.

---

## 📁 Système de Fichiers

### **Root Directory (/)**
> **💭 Définition :** Le dossier racine, parent de tous les autres.
> **🌳 Analogie :** Le tronc de l'arbre d'où partent toutes les branches.

### **Home Directory (~)**
> **💭 Définition :** Dossier personnel de chaque utilisateur.
> **🏠 Analogie :** Votre chambre personnelle dans la maison familiale.

### **Mount Point**
> **💭 Définition :** Dossier où un système de fichiers est "attaché" à l'arborescence.
> **🔗 Analogie :** Point d'attache pour connecter une nouvelle armoire à votre maison.

### **Symlink (Symbolic Link)**
> **💭 Définition :** Raccourci vers un autre fichier ou dossier.
> **🔗 Analogie :** Post-it avec l'adresse qui pointe vers le vrai document.

### **Permissions Unix**
> **�� Définition :** Système rwx (read, write, execute) pour contrôler l'accès aux fichiers.
> **🚪 Analogie :** Serrures à trois niveaux : qui peut regarder, modifier, utiliser.

---

## ⚙️ Processus et Services

### **Process (Processus)**
> **💭 Définition :** Programme en cours d'exécution.
> **🏃 Analogie :** Une personne en train de faire une tâche spécifique.

### **PID (Process ID)**
> **💭 Définition :** Numéro unique de chaque processus.
> **🏷️ Analogie :** Badge d'identification unique pour chaque employé au travail.

### **Daemon**
> **💭 Définition :** Programme qui tourne en arrière-plan sans interface utilisateur.
> **👻 Analogie :** Service invisible qui travaille en permanence (comme le chauffage automatique).

### **systemd**
> **💭 Définition :** Système d'init moderne qui gère les services et le démarrage.
> **🎛️ Analogie :** Chef d'orchestre qui coordonne tous les musiciens (services) du système.

### **Signal**
> **�� Définition :** Message envoyé à un processus pour lui dire quoi faire.
> **📡 Analogie :** Signal radio envoyé à un pilote d'avion.

**Signaux courants :**
- `SIGTERM (15)` - Demande polie d'arrêt
- `SIGKILL (9)` - Arrêt forcé immédiat
- `SIGHUP (1)` - Rechargement de configuration

---

## 🌐 Réseau

### **Interface réseau**
> **💭 Définition :** Carte réseau virtuelle ou physique.
> **🚪 Analogie :** Porte d'entrée de la maison vers l'extérieur.

**Exemples :** eth0, wlan0, lo

### **iptables**
> **💭 Définition :** Firewall Linux qui filtre les paquets réseau.
> **🚧 Analogie :** Vigile à l'entrée qui décide qui peut entrer ou sortir.

### **Port**
> **💭 Définition :** Numéro qui identifie un service réseau.
> **🚪 Analogie :** Numéro d'appartement dans un immeuble - chaque service a son adresse.

---

## 📦 Gestionnaires de Paquets

### **APT (Advanced Package Tool)**
> **💭 Définition :** Gestionnaire de paquets Debian/Ubuntu.
> **📱 Analogie :** Play Store pour Android.

### **YUM/DNF**
> **💭 Définition :** Gestionnaire de paquets Red Hat/CentOS/Fedora.
> **🍎 Analogie :** App Store pour iPhone.

### **Repository (Repo)**
> **💭 Définition :** Serveur contenant des paquets logiciels.
> **🏬 Analogie :** Entrepôt Amazon d'où viennent les colis.

---

## 🔐 Sécurité

### **SSH (Secure Shell)**
> **💭 Définition :** Protocole pour se connecter de manière sécurisée à distance.
> **📞 Analogie :** Téléphone crypté pour parler en privé avec un ordinateur distant.

### **SELinux**
> **💭 Définition :** Système de sécurité qui contrôle l'accès aux ressources.
> **🛡️ Analogie :** Système de sécurité renforcé avec contrôles d'accès très stricts.

### **Chroot**
> **💭 Définition :** Environnement isolé avec une racine différente.
> **🏠 Analogie :** Maison dans la maison - le programme ne voit qu'une partie du système.

---

## 📊 Surveillance et Tools

### **Log Files**
> **💭 Définition :** Fichiers qui enregistrent les événements du système.
> **📓 Analogie :** Journal de bord qui note tout ce qui se passe.

### **cron/crontab**
> **💭 Définition :** Planificateur de tâches automatiques.
> **⏰ Analogie :** Réveil programmable qui exécute des tâches à heures fixes.

### **grep**
> **💭 Définition :** Recherche de texte dans des fichiers.
> **🔍 Analogie :** Ctrl+F géant qui cherche dans tous les documents.

### **tar**
> **💭 Définition :** Outil d'archivage de fichiers.
> **📦 Analogie :** Machine à emballer qui met plusieurs objets dans un carton.

---

*Ce glossaire couvre l'essentiel des termes Linux pour les pentesters.*
