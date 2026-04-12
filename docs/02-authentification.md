# Authentification

> Configurer un nœud de contrôle pour piloter des machines cibles via des clés SSH.

---

## Environnement

| Machine | Rôle | Adresse IP |
|---|---|---|
| `control` | Nœud de contrôle Ansible | 192.168.56.10 |
| `target01` | Serveur cible 1 | 192.168.56.20 |
| `target02` | Serveur cible 2 | 192.168.56.30 |
| `target03` | Serveur cible 3 | 192.168.56.40 |

---

## Étapes de réalisation

**1. Démarrage de l'infrastructure :**

```bash
cd ~/formation-ansible/atelier-03
vagrant up
vagrant ssh control
```

**2. Configuration de `/etc/hosts` :**
127.0.0.1 localhost.localdomain localhost
192.168.56.10 control.sandbox.lan control
192.168.56.20 target01.sandbox.lan target01
192.168.56.30 target02.sandbox.lan target02
192.168.56.40 target03.sandbox.lan target03

text

**3. Génération et déploiement des clés SSH :**

```bash
ssh-keyscan -t rsa target01 target02 target03 >> ~/.ssh/known_hosts
ssh-keygen
ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03
```

**4. Test de connectivité Ansible :**

```bash
ansible all -i target01,target02,target03 -m ping
```

**Résultat :**
target01 | SUCCESS => { "changed": false, "ping": "pong" }
target02 | SUCCESS => { "changed": false, "ping": "pong" }
target03 | SUCCESS => { "changed": false, "ping": "pong" }
---

<div align="center">

[Chapitre précédent](./01-installer-ansible.md) · [Accueil](../README.md) · [Chapitre suivant](./03-configuration-de-base.md)

</div>
