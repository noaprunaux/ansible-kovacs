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
