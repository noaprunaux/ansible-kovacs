# Configuration de base

> Mettre en place une structure de projet Ansible professionnelle avec gestion des logs, inventaire structuré et élévation de privilèges.

---

## Environnement

| Machine | IP | Rôle |
|---|---|---|
| `control` | 192.168.56.10 | Control Host |
| `target01` | 192.168.56.20 | Target Host |
| `target02` | 192.168.56.30 | Target Host |
| `target03` | 192.168.56.40 | Target Host |

---

## Mise en place

```bash
mkdir ~/monprojet && cd ~/monprojet
touch ansible.cfg
ansible --version | head -n 2
```
ansible [core 2.17.14]
config file = /home/vagrant/monprojet/ansible.cfg

---

## Fichiers de configuration finaux

### `ansible.cfg`

```ini
[defaults]
inventory = ./hosts
log_path = ~/journal/ansible.log
```

### `hosts`

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

## Vérification de l'élévation des droits

```bash
ansible all -a "head -n 1 /etc/shadow"
```

**Résultat :**
target01 | CHANGED | rc=0 >>
root:*:19977:0:99999:7:::

> L'accès à `/etc/shadow` (restreint à root) confirme que l'élévation de privilèges fonctionne correctement.

---

<div align="center">

[Chapitre précédent](./02-authentification.md) · [Accueil](../README.md) · [Chapitre suivant](./04-idempotence.md)

</div>
