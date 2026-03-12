# ansible-kovacs

## INSTALLER ANSIBLE
##  Challenge n° 1 : Installation standard sur Ubuntu

**Objectif :** Déployer une VM Ubuntu et installer Ansible via les dépôts officiels.

###  Étapes de réalisation

1.  **Démarrage de la VM :**
    ```bash
    vagrant up ubuntu
    ```

2.  **Connexion SSH :**
    ```bash
    vagrant ssh ubuntu
    ```

3.  **Mise à jour et recherche du paquet :**
    ```bash
    sudo apt update && apt search --names-only ansible
    ```

4.  **Installation du paquet officiel :**
    ```bash
    sudo apt install -y ansible
    ```

5.  **Vérification de la version :**
    ```bash
    ansible --version
    ```
    *La version installée est la 2.10.8*

6.  **Nettoyage :**
    ```bash
    exit
    vagrant destroy -f ubuntu
    ```

---

## Challenge n° 2 : Installation via PPA (Ubuntu)

**Objectif :** Utiliser un dépôt tiers (PPA) pour obtenir une version plus récente d'Ansible.

###  Étapes de réalisation

1.  **Configuration du dépôt PPA :**
    Une fois connecté à la VM :
    ```bash
    sudo apt update
    sudo add-apt-repository --yes --update ppa:ansible/ansible
    ```

2.  **Installation d'Ansible :**
    ```bash
    sudo apt install -y ansible
    ```

3.  **Comparaison des versions :**
    * **Challenge 1 (Dépôt officiel) :** 2.10.8
    * **Challenge 2 (PPA) :** 2.17.14

---

##  Challenge n° 3 : Rocky Linux, PIP et Virtualenv

**Objectif :** Installation isolée d'Ansible sur une distribution de type Red Hat.

###  Étapes de réalisation

1.  **Lancement de la VM Rocky Linux :**
    ```bash
    vagrant up rocky
    vagrant ssh rocky
    ```

2.  **Installation de Python et PIP :**
    ```bash
    sudo dnf update
    sudo dnf install -y epel-release
    sudo crb enable
    sudo dnf install -y python3-pip python3-devel
    ```

3.  **Création et activation de l'environnement virtuel :**
    ```bash
    python3 -m venv ansible-env
    source ~/ansible-venv/bin/activate
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
   *La version installée est la 2.15.13*


---
