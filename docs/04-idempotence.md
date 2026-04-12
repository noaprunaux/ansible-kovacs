# Idempotence

> Comprendre et maîtriser l'idempotence des modules Ansible.

---

## Exemples pratiques

### Installation et désinstallation de paquets

```bash
# Installation
ansible all -m package -a "name=tree,git,nmap"

# Désinstallation
ansible all -m package -a "name=tree,git,nmap state=absent"
```

### Copie et suppression de fichier

```bash
ansible all -m copy -a "src=/etc/fstab dest=/tmp/test3.txt"
ansible all -m file -a "path=/tmp/test3.txt state=absent"
```

### Affichage de l'espace disque

```bash
ansible all -m command -a "df -h /"
```

> **Remarque :** Le module `command` retourne toujours le statut `CHANGED`, même sans modification réelle du système. Ansible ne peut pas évaluer si la commande a changé l'état de la machine, ce qui illustre la limite des modules non idempotents.

---

<div align="center">

[Chapitre précédent](./03-configuration-de-base.md) · [Accueil](../README.md) · [Chapitre suivant](./05-playbooks-apache.md)

</div>
