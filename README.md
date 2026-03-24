# Ansible — Ateliers pratiques

---

## Sommaire

- [INSTALLER ANSIBLE](#installer-ansible)
- [AUTHENTIFICATION](#authentification)
- [CONFIGURATION DE BASE](#configuration-de-base)
- [IDEMPOTENCE](#idempotence)
- [PLAYBOOKS_APACHE](#playbooks_apache)

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

```
127.0.0.1      localhost.localdomain  localhost
192.168.56.10  control.sandbox.lan    control
192.168.56.20  target01.sandbox.lan   target01
192.168.56.30  target02.sandbox.lan   target02
192.168.56.40  target03.sandbox.lan   target03
```

**3. Génération et déploiement des clés SSH :**

```bash
# Récupération des empreintes pour éviter les prompts de confirmation
ssh-keyscan -t rsa target01 target02 target03 >> ~/.ssh/known_hosts

# Génération de la paire de clés RSA (sans mot de passe)
ssh-keygen

# Copie de la clé publique vers les cibles (mot de passe : vagrant)
ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03
```

**4. Test de connectivité Ansible :**

```bash
ansible all -i target01,target02,target03 -m ping
```

**Résultat :**

```
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

---

## CONFIGURATION DE BASE

**Objectif :** Mettre en place une structure de projet Ansible professionnelle, incluant la gestion des logs, un inventaire structuré et l'élévation de privilèges.

### Environnement

| Machine | IP | Rôle |
|---|---|---|
| `control` | 192.168.56.10 | Control Host |
| `target01` | 192.168.56.20 | Target Host |
| `target02` | 192.168.56.30 | Target Host |
| `target03` | 192.168.56.40 | Target Host |

---

### 1. Résolution de noms — Éditer `/etc/hosts`

> **Consigne :** Éditez `/etc/hosts` de manière à ce que les Target Hosts soient joignables par leur nom d'hôte simple.

```bash
sudo nano /etc/hosts
```

Contenu à ajouter :

```
127.0.0.1      localhost.localdomain  localhost
192.168.56.10  control.sandbox.lan    control
192.168.56.20  target01.sandbox.lan   target01
192.168.56.30  target02.sandbox.lan   target02
192.168.56.40  target03.sandbox.lan   target03
```

---

### 2. Authentification par clé SSH

> **Consigne :** Configurez l'authentification par clé SSH avec les trois Target Hosts.

```bash
# Récupération des clés hôtes
ssh-keyscan -t rsa target01 target02 target03 >> ~/.ssh/known_hosts

# Génération de la paire de clés
ssh-keygen

# Copie de la clé publique vers chaque cible
ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03
```

---

### 3. Installation d'Ansible

> **Consigne :** Installez Ansible.

```bash
sudo apt update
sudo apt install -y ansible
```

---

### 4. Premier ping Ansible sans configuration

> **Consigne :** Envoyez un premier `ping` Ansible sans configuration.

```bash
ansible all -i target01,target02,target03 -m ping
```

**Résultat :**

```
target01 | SUCCESS => { "changed": false, "ping": "pong" }
target02 | SUCCESS => { "changed": false, "ping": "pong" }
target03 | SUCCESS => { "changed": false, "ping": "pong" }
```

---

### 5. Création du répertoire de projet

> **Consigne :** Créez un répertoire de projet `~/monprojet`.

```bash
mkdir ~/monprojet
```

---

### 6. Création du fichier `ansible.cfg`

> **Consigne :** Créez un fichier vide `ansible.cfg` dans ce répertoire.

```bash
touch ~/monprojet/ansible.cfg
```

---

### 7. Vérification de la prise en compte du fichier

> **Consigne :** Vérifiez si ce fichier est bien pris en compte par Ansible.

```bash
cd ~/monprojet
ansible --version | head -n 2
```

**Résultat :**

```
ansible [core 2.17.14]
  config file = /home/vagrant/monprojet/ansible.cfg
```

> Ansible détecte bien le fichier `ansible.cfg` local au projet.

---

### 8. Spécification de l'inventaire `hosts`

> **Consigne :** Spécifiez un inventaire nommé `hosts`.

```ini
# ~/monprojet/ansible.cfg
[defaults]
inventory = ./hosts
```

---

### 9. Activation de la journalisation

> **Consigne :** Activez la journalisation dans `~/journal/ansible.log`.

```ini
# ~/monprojet/ansible.cfg
[defaults]
inventory = ./hosts
log_path = ~/journal/ansible.log
```

---

### 10. Test de la journalisation

> **Consigne :** Testez la journalisation.

```bash
ansible all -i target01,target02,target03 -m ping
cat ~/journal/ansible.log
```

**Extrait du journal :**

```
2026-03-16 10:12:10,977 p=3652 u=vagrant n=ansible | target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.10"
    },
    "changed": false,
    "ping": "pong"
}
2026-03-16 10:12:11,014 p=3652 u=vagrant n=ansible | target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.10"
    },
    "changed": false,
    "ping": "pong"
}
```

> Les logs sont bien écrits dans `~/journal/ansible.log`.

---

### 11. Création du groupe `[testlab]`

> **Consigne :** Créez un groupe `[testlab]` avec vos trois Target Hosts.

```ini
# ~/monprojet/hosts
[testlab]
target01
target02
target03
```

---

### 12. Définition de l'utilisateur de connexion

> **Consigne :** Définissez explicitement l'utilisateur `vagrant` pour la connexion à vos cibles.

```ini
# ~/monprojet/hosts
[testlab:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=vagrant
```

---

### 13. Ping vers le groupe `[all]`

> **Consigne :** Envoyez un `ping` Ansible vers le groupe de machines `[all]`.

```bash
ansible all -m ping
```

**Résultat :** Les trois hôtes répondent `"ping": "pong"` — SUCCESS

---

### 14. Élévation des droits (`become`)

> **Consigne :** Définissez l'élévation des droits pour l'utilisateur `vagrant` sur les Target Hosts.

```ini
# ~/monprojet/hosts
[testlab:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=vagrant
ansible_become=yes
```

---

### 15. Affichage de la première ligne de `/etc/shadow`

> **Consigne :** Affichez la première ligne du fichier `/etc/shadow` sur tous les Target Hosts.

```bash
ansible all -a "head -n 1 /etc/shadow"
```

**Résultat :**

```
target01 | CHANGED | rc=0 >>
root:*:19977:0:99999:7:::
target02 | CHANGED | rc=0 >>
root:*:19977:0:99999:7:::
target03 | CHANGED | rc=0 >>
root:*:19977:0:99999:7:::
```

> L'accès à `/etc/shadow` (restreint à root) confirme que l'élévation de privilèges fonctionne correctement.

---

### 16. Nettoyage — Suppression des VM

> **Consigne :** Quittez le Control Host et supprimez toutes les VM de l'atelier.

```bash
exit
vagrant destroy -f
```

---

## Fichiers de configuration finaux

### `~/monprojet/ansible.cfg`

```ini
[defaults]
inventory = ./hosts
log_path = ~/journal/ansible.log
```

### `~/monprojet/hosts`

```ini
[testlab]
target01
target02
target03

[testlab:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=vagrant
ansible_become=yes
```

---

## IDEMPOTENCE

### 1. Installation de paquets

> **Consigne :** Installez successivement les paquets `tree`, `git` et `nmap` sur toutes les cibles.

```bash
ansible all -m package -a "name=tree,git,nmap"
```

---

### 2. Désinstallation de paquets

> **Consigne :** Désinstallez ces trois paquets en utilisant le paramètre supplémentaire `state=absent`.

```bash
ansible all -m package -a "name=tree,git,nmap state=absent"
```

---

### 3. Copie d'un fichier vers les cibles

> **Consigne :** Copiez le fichier `/etc/fstab` du Control Host vers tous les Target Hosts sous forme d'un fichier `/tmp/test3.txt`.

```bash
ansible all -m file -a "dest=/tmp/test state=directory"
ansible all -m copy -a "src=/etc/fstab dest=/tmp/test3.txt"
```

---

### 4. Suppression du fichier copié

> **Consigne :** Supprimez le fichier `/tmp/test3.txt` sur les Target Hosts en utilisant le module `file` avec le paramètre `state=absent`.

```bash
ansible all -m file -a "path=/tmp/test3.txt state=absent"
```

---

### 5. Affichage de l'espace disque

> **Consigne :** Affichez l'espace utilisé par la partition principale sur tous les Target Hosts. Que remarquez-vous ?

```bash
ansible all -m command -a "df -h /"
```

> On remarque que les tâches sont retournées avec le statut `CHANGED`, même lorsqu'aucune modification réelle n'a eu lieu. Cela illustre la limite du module `command` qui n'est pas idempotent par nature : Ansible ne peut pas déterminer si la commande a réellement changé l'état du système.

## PLAYBOOKS_APACHE

**Objectif :** Écrire trois playbooks pour installer Apache sur trois distributions Linux différentes, chacune avec une page d'accueil personnalisée.

---

### 1. Playbook Debian — `apache-debian.yml`

> **Consigne :** Installez Apache sur l'hôte `debian` avec une page personnalisée `Apache web server running on Debian Linux`.

```yaml
--- # apache-debian.yml
- hosts: debian
  tasks:
    - name: Update package information
      apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Install Apache
      apt:
        name: apache2

    - name: Start & enable Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Apache web server on Debian
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>Apache web server running on Debian Linux</h1>
            </body>
          </html>
...
```

---

### 2. Playbook Rocky Linux — `apache-rocky.yml`

> **Consigne :** Installez Apache sur l'hôte `rocky` avec une page personnalisée `Apache web server running on Rocky Linux`.

```yaml
--- # apache-rocky.yml
- hosts: rocky
  tasks:
    - name: Install Apache
      dnf:
        name: httpd

    - name: Start service
      service:
        name: httpd
        state: started
        enabled: true

    - name: Apache web server on Rocky
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>Apache web server running on Rocky Linux</h1>
            </body>
          </html>
...
```

---

### 3. Playbook SUSE — `apache-suse.yml`

> **Consigne :** Installez Apache sur l'hôte `suse` avec une page personnalisée `Apache web server running on SUSE Linux`.

```yaml
--- # apache-suse.yml
- hosts: suse
  tasks:
    - name: Install Apache
      zypper:
        name: apache2

    - name: Start service
      service:
        name: apache2
        state: started
        enabled: true

    - name: Apache web server on Suse
      copy:
        dest: /srv/www/htdocs/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Test</title>
            </head>
            <body>
              <h1>Apache web server running on Suse Linux</h1>
            </body>
          </html>
...
```

---

### Points clés par distribution

| Distribution | Gestionnaire de paquets | Nom du service | Répertoire web |
|---|---|---|---|
| Debian | `apt` | `apache2` | `/var/www/html/` |
| Rocky Linux | `dnf` | `httpd` | `/var/www/html/` |
| SUSE | `zypper` | `apache2` | `/srv/www/htdocs/` |

