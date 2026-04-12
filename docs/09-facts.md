# Facts

> Exploiter les variables système collectées automatiquement par Gathering Facts.

---

## `pkg-info.yml` — Gestionnaire de paquets

```yaml
---  # pkg-info.yml
- hosts: all

  tasks:
    - name: Package manager per distribution
      debug:
        msg: "{{ ansible_host }} package is {{ ansible_pkg_mgr }}"
...
```

**Résultat :**
ok: [rocky] => { "msg": "rocky package is dnf" }
ok: [debian] => { "msg": "debian package is apt" }
ok: [suse] => { "msg": "suse package is zypper" }

---

## `python-info.yml` — Version de Python

```yaml
---  # python-info.yml
- hosts: all

  tasks:
    - name: Python version per distribution
      debug:
        msg: "{{ ansible_host }} python version is {{ ansible_python_version }}"
...
```

**Résultat :**
ok: [rocky] => { "msg": "rocky python version is 3.9.21" }
ok: [debian] => { "msg": "debian python version is 3.11.2" }
ok: [suse] => { "msg": "suse python version is 3.6.15" }

---

## `dns-info.yml` — Serveurs DNS

```yaml
---  # dns-info.yml
- hosts: all

  tasks:
    - name: DNS per distribution
      debug:
        msg: "{{ ansible_host }} DNS is {{ ansible_dns }}"
...
```

**Résultat :**
ok: [rocky] => { "msg": "rocky DNS is {'search': ['mines-ales.fr'], 'nameservers': ['10.0.2.3']}" }
ok: [debian] => { "msg": "debian DNS is {'domain': 'mines-ales.fr', 'search': ['mines-ales.fr.'], 'nameservers': ['10.0.2.3']}" }
ok: [suse] => { "msg": "suse DNS is {'search': ['mines-ales.fr'], 'nameservers': ['10.0.2.3']}" }


> Debian inclut une clé `domain` supplémentaire absente sur Rocky et SUSE. Les facts sont collectés automatiquement via `Gathering Facts` — aucune définition manuelle n'est nécessaire.

---

<div align="center">

[Chapitre précédent](./08-variables-enregistrees.md) · [Accueil](../README.md) · [Chapitre suivant](./10-chrony.md)

</div>
