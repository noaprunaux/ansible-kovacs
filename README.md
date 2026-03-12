# ansible-kovacs


## 🟢 Challenge n° 1 : Installation standard sur Ubuntu

**Objectif :** Déployer une VM Ubuntu et installer Ansible via les dépôts officiels.

### 📝 Étapes de réalisation

1.  **Démarrage de la VM :**
    ```bash
    mkdir atelier-01 && cd atelier-01
    vagrant init generic/ubuntu2204
    vagrant up
    ```

2.  **Connexion SSH :**
    ```bash
    vagrant ssh
    ```

3.  **Mise à jour et recherche du paquet :**
    ```bash
    sudo apt update
    apt-cache policy ansible
    ```

4.  **Installation du paquet officiel :**
    ```bash
    sudo apt install -y ansible
    ```

5.  **Vérification de la version :**
    ```bash
    ansible --version
    ```
    *Note : La version installée est généralement la version stable par défaut de la distribution (ex: 2.10.x).*

6.  **Nettoyage :**
    ```bash
    exit
    vagrant destroy -f
    ```

---

## 🔵 Challenge n° 2 : Installation via PPA (Ubuntu)

**Objectif :** Utiliser un dépôt tiers (PPA) pour obtenir une version plus récente d'Ansible.

### 📝 Étapes de réalisation

1.  **Configuration du dépôt PPA :**
    Une fois connecté à la VM :
    ```bash
    sudo apt update
    sudo apt install -y software-properties-common
    sudo add-apt-repository --yes --update ppa:ansible/ansible
    ```

2.  **Installation d'Ansible :**
    ```bash
    sudo apt install -y ansible
    ```

3.  **Comparaison des versions :**
    * **Challenge 1 (Dépôt officiel) :** Version plus ancienne, privilégiant la stabilité de la distribution.
    * **Challenge 2 (PPA) :** Version beaucoup plus récente (ex: 2.16+), permettant d'utiliser les dernières fonctionnalités d'Ansible.

---

## 🔴 Challenge n° 3 : Rocky Linux, PIP et Virtualenv

**Objectif :** Installation isolée d'Ansible sur une distribution de type Red Hat.

### 📝 Étapes de réalisation

1.  **Lancement de la VM Rocky Linux :**
    ```bash
    mkdir atelier-03 && cd atelier-03
    vagrant init generic/rocky9
    vagrant up
    vagrant ssh
    ```

2.  **Installation de Python et PIP :**
    ```bash
    sudo dnf install -y python3-pip
    ```

3.  **Création et activation de l'environnement virtuel :**
    ```bash
    python3 -m venv ansible-env
    source ansible-env/bin/activate
    ```

4.  **Installation d'Ansible via PIP :**
    ```bash
    pip install --upgrade pip
    pip install ansible
    ```

5.  **Vérification :**
    ```bash
    ansible --version
    ```
    *L'exécutable est situé dans : `/home/vagrant/ansible-env/bin/ansible`.*

---
