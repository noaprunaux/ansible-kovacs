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


## AUTHENTIFICATION


**Objectif :** Configurer un nœud de contrôle (`control`) pour piloter trois machines cibles (`target01`, `target02`, `target03`) via des clés SSH.

### Inventaire de l'atelier
| Machine | Rôle | Adresse IP |
| :--- | :--- | :--- |
| **control** | Nœud de contrôle Ansible | 192.168.56.10 |
| **target01** | Serveur cible 1 | 192.168.56.20 |
| **target02** | Serveur cible 2 | 192.168.56.30 |
| **target03** | Serveur cible 3 | 192.168.56.40 |

### Étapes de réalisation

1.  **Démarrage de l'infrastructure :**
    ```bash
    cd ~/formation-ansible/atelier-03
    vagrant up
    vagrant ssh control
    ```

2.  **Configuration de la résolution de noms :**
    Modification du fichier `/etc/hosts` pour mapper les adresses IP aux noms de machines :
    ```bash
    sudo nano /etc/hosts
    # Ajout des lignes :
    127.0.0.1      localhost.localdomain  localhost
    192.168.56.10  control.sandbox.lan    control
    192.168.56.20  target01.sandbox.lan   target01
    192.168.56.30  target02.sandbox.lan   target02
    192.168.56.40  target03.sandbox.lan   target03

    ```

3.  **Génération et déploiement des clés SSH :**
    Pour permettre à Ansible de se connecter sans mot de passe :
    ```bash

    # Récupération des empreintes pour éviter les prompts de confirmation
    ssh-keyscan -t rsa target01 target02 target03 >> .ssh/know_hosts

    # Génération de la clé RSA (sans mot de passe)
    ssh-keygen

    # Copie de la clé publique vers les cibles (password: vagrant)
    ssh-copy-id vagrant@target01
    ssh-copy-id vagrant@target02
    ssh-copy-id vagrant@target03
    ```

4.  **Test de connectivité avec Ansible :**
    Exécution d'un "ping" Ansible sur l'ensemble des cibles :
    ```bash
    ansible all -i target01,target02,target03 -m ping
    ```

###  Résultat attendu
Le module `ping` doit retourner un succès (`pong`) pour les trois machines :
```json
target01 | SUCCESS => {"changed": false, "ping": "pong"}
target02 | SUCCESS => {"changed": false, "ping": "pong"}
target03 | SUCCESS => {"changed": false, "ping": "pong"}
