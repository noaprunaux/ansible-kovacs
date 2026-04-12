# Ansible — Ateliers pratiques

---

## Sommaire

- [INSTALLER ANSIBLE](#installer-ansible)
- [AUTHENTIFICATION](#authentification)
- [CONFIGURATION DE BASE](#configuration-de-base)
- [IDEMPOTENCE](#idempotence)
- [PLAYBOOKS_APACHE](#playbooks_apache)
- [HANDLERS](#handlers)
- [VARIABLES](#variables)
- [VARIABLES_ENREGISTRÉES](#variables_enregistrées)
- [FACTS](#facts)
- [CHRONY](#chrony)

---

## INSTALLER ANSIBLE

### Challenge n°1 — Installation standard sur Ubuntu

**Objectif :** Déployer une VM Ubuntu et installer Ansible via les dépôts officiels.

**1. Démarrage de la VM :**

```bash
vagrant up ubuntu
```

**2. Connexion SSH :**

```bash
vagrant ssh ubuntu
```

**3. Mise à jour et recherche du paquet :**

```bash
sudo apt update && apt search --names-only ansible
```

**4. Installation du paquet officiel :**

```bash
sudo apt install -y ansible
```

**5. Vérification de la version :**

```bash
ansible --version
```

> Version installée : **2.10.8**

**6. Nettoyage :**

```bash
exit
vagrant destroy -f ubuntu
```

---

### Challenge n°2 — Installation via PPA (Ubuntu)

**Objectif :** Utiliser un dépôt tiers (PPA) pour obtenir une version plus récente d'Ansible.

**1. Configuration du dépôt PPA :**

```bash
sudo apt update
sudo add-apt-repository --yes --update ppa:ansible/ansible
```

**2. Installation d'Ansible :**

```bash
sudo apt install -y ansible
```

**3. Vérification de la version :**

```bash
ansible --version
```

### Comparaison des versions

| Méthode | Version obtenue |
|---|---|
| Dépôt officiel Ubuntu (Challenge 1) | 2.10.8 |
| PPA `ansible/ansible` (Challenge 2) | 2.17.14 |

---

### Challenge n°3 — Rocky Linux, PIP et Virtualenv

**Objectif :** Installation isolée d'Ansible sur une distribution de type Red Hat.

**1. Lancement de la VM Rocky Linux :**

```bash
vagrant up rocky
vagrant ssh rocky
```

**2. Installation de Python et PIP :**

```bash
sudo dnf update
sudo dnf install -y epel-release
sudo crb enable
sudo dnf install -y python3-pip python3-devel
```

**3. Création et activation de l'environnement virtuel :**

```bash
python3 -m venv ansible-env
source ~/ansible-venv/bin/activate
```

**4. Installation d'Ansible via PIP :**

```bash
pip install --upgrade pip
pip install ansible
```

**5. Vérification de la version :**

```bash
ansible --version
```

> Version installée : **2.15.13**

---

## AUTHENTIFICATION

**Objectif :** Configurer un nœud de contrôle (`control`) pour piloter trois machines cibles (`target01`, `target02`, `target03`) via des clés SSH.

### Environnement

| Machine | Rôle | Adresse IP |
|---|---|---|
| `control` | Nœud de contrôle Ansible | 192.168.56.10 |
| `target01` | Serveur cible 1 | 192.168.56.20 |
| `target02` | Serveur cible 2 | 192.168.56.30 |
| `target03` | Serveur cible 3 | 192.168.56.40 |

### Étapes de réalisation

**1. Démarrage de l'infrastructure :**

```bash
cd ~/formation-ansible/atelier-03
vagrant up
vagrant ssh control
```

**2. Configuration de la résolution de noms :**

```bash
sudo nano /etc/hosts
```

Contenu à ajouter :
