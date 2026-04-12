# Jinja & Templates

> Générer des fichiers de configuration personnalisés et dynamiques avec Jinja2.

---

## Syntaxe Jinja2 essentielle

| Balise | Role | Exemple |
|---|---|---|
| `{{ variable }}` | Interpolation de variable | `{{ inventory_hostname }}` |
| `{% for x in list %}` | Boucle | Générer une liste HTML |
| `{% if condition %}` | Condition | Affichage conditionnel |
| `{# commentaire #}` | Commentaire | N'apparaît pas dans le fichier généré |

> Le module `template` cherche ses sources dans le répertoire `templates/` à côté du playbook. L'extension `.j2` est une convention signifiant Jinja2.

---

## Démonstration — Pages web dynamiques

### `apache-03.yml` — Titre personnalisé par hôte

```yaml
    - name: Install custom web page
      template:
        dest: "{{ apache_document_root }}/index.html"
        mode: 0644
        src: index-1.html.j2
```

**`templates/index-1.html.j2` :**

```jinja2
{# templates/index-1.html.j2 #}
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>{{ inventory_hostname }}</title>
  </head>
  <body>
    <h1>Welcome to {{ inventory_hostname }}!</h1>
  </body>
</html>
```

**Résultat sur `suse` :**

```html
<title>suse</title>
<h1>Welcome to suse!</h1>
```

---

### `apache-04.yml` — Liste de tous les hôtes avec une boucle

**`templates/index-2.html.j2` :**

```jinja2
{# templates/index-2.html.j2 #}
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>{{ inventory_hostname }}</title>
  </head>
  <body>
    <h1>Welcome to {{ inventory_hostname }}!</h1>
    <h2>Here's our team:</h2>
    <ul>
    {% for host in groups['all'] %}
      <li>{{ host }}</li>
    {% endfor %}
    </ul>
  </body>
</html>
```

> La boucle `{% for %}` itère sur tous les hôtes de l'inventaire. Chaque machine reçoit une page unique, mais avec la liste complète de tous ses pairs — générée dynamiquement.

---

## Challenge — Configuration Chrony via template : `chrony.yml`

> La première ligne du fichier généré doit être un commentaire indiquant le chemin complet : `/etc/chrony/chrony.conf` sur Debian/Ubuntu, `/etc/chrony.conf` sur RedHat/SUSE.

### Solution

La variable `chrony_confdir` (définie dans les `group_vars`) est utilisée directement dans le template pour générer le commentaire dynamiquement — sans aucune condition `when`.

**`templates/chrony.conf.j2` :**

```jinja2
{# templates/chrony.conf.j2 #}
# {{ chrony_confdir }}/chrony.conf
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

**`chrony.yml` :**

```yaml
---  # chrony.yml
- hosts: all

  tasks:
    - name: Update package information on Debian/Ubuntu
      apt:
        update_cache: true
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"

    - name: Install Chrony
      package:
        name: "{{ chrony_package }}"

    - name: Install configuration from template
      template:
        dest: "{{ chrony_confdir }}/chrony.conf"
        src: chrony.conf.j2
        mode: 0644
      notify: Reload Chrony

  handlers:
    - name: Reload Chrony
      service:
        name: "{{ chrony_service }}"
        state: restarted
...
```

### Vérification des fichiers générés

**Sur Debian/Ubuntu** (`chrony_confdir: /etc/chrony`) :
/etc/chrony/chrony.conf
server 0.fr.pool.ntp.org iburst
...

**Sur Rocky/SUSE** (`chrony_confdir: /etc`) :
/etc/chrony.conf
server 0.fr.pool.ntp.org iburst
...


### `copy` vs `template`

| Critère | `copy` | `template` |
|---|---|---|
| Répertoire source | `files/` | `templates/` |
| Jinja2 interprété | Non | Oui |
| Extension conventionnelle | Libre | `.j2` |
| Cas d'usage | Fichier identique sur toutes les cibles | Fichier personnalisé par hôte ou groupe |

### Structure du projet
playbooks/
├── chrony.yml
├── group_vars/
│ ├── debian.yml # chrony_confdir: /etc/chrony | chrony_service: chrony
│ ├── ubuntu.yml # chrony_confdir: /etc/chrony | chrony_service: chrony
│ ├── rocky.yml # chrony_confdir: /etc | chrony_service: chronyd
│ └── suse.yml # chrony_confdir: /etc | chrony_service: chronyd
└── templates/
└── chrony.conf.j2


---

<div align="center">

[Chapitre précédent](./10-chrony.md) · [Accueil](../README.md)

</div>
