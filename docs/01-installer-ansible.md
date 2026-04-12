# Installer Ansible

> Découvrir les différentes méthodes d'installation d'Ansible selon les distributions et les besoins.

---

## Challenge 1 — Installation standard sur Ubuntu

**Objectif :** Déployer une VM Ubuntu et installer Ansible via les dépôts officiels.

```bash
vagrant up ubuntu
vagrant ssh ubuntu

sudo apt update && apt search --names-only ansible
sudo apt install -y ansible
ansible --version

exit
vagrant destroy -f ubuntu
```

> Version installée : **2.10.8**

---

## Challenge 2 — Installation via PPA (Ubuntu)

**Objectif :** Utiliser un dépôt tiers pour obtenir une version plus récente d'Ansible.

```bash
sudo apt update
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install -y ansible
ansible --version
```

### Comparaison des versions

| Méthode | Version |
|---|---|
| Dépôt officiel Ubuntu | 2.10.8 |
| PPA `ansible/ansible` | 2.17.14 |

---

## Challenge 3 — Rocky Linux, PIP et Virtualenv

**Objectif :** Installation isolée d'Ansible sur une distribution de type Red Hat.

```bash
vagrant up rocky
vagrant ssh rocky

sudo dnf update
sudo dnf install -y epel-release
sudo crb enable
sudo dnf install -y python3-pip python3-devel

python3 -m venv ansible-env
source ~/ansible-venv/bin/activate

pip install --upgrade pip
pip install ansible
ansible --version
```

> Version installée : **2.15.13**

---

<div align="center">

[Accueil](../README.md) · [Chapitre suivant](./02-authentification.md)

</div>
