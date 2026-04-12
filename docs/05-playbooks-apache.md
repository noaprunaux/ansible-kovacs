# Playbooks Apache

> Installer Apache sur trois distributions avec une page d'accueil personnalisée.

---

## Points clés par distribution

| Distribution | Gestionnaire | Service | Répertoire web |
|---|---|---|---|
| Debian | `apt` | `apache2` | `/var/www/html/` |
| Rocky Linux | `dnf` | `httpd` | `/var/www/html/` |
| SUSE | `zypper` | `apache2` | `/srv/www/htdocs/` |

---

## `apache-debian.yml`

```yaml
---  # apache-debian.yml
- hosts: debian
  tasks:
    - name: Update package information
      apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Install Apache
      apt:
        name: apache2

    - name: Start and enable Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Deploy custom web page
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head><meta charset="utf-8"><title>Test</title></head>
            <body><h1>Apache web server running on Debian Linux</h1></body>
          </html>
...
```

---

## `apache-rocky.yml`

```yaml
---  # apache-rocky.yml
- hosts: rocky
  tasks:
    - name: Install Apache
      dnf:
        name: httpd

    - name: Start and enable Apache
      service:
        name: httpd
        state: started
        enabled: true

    - name: Deploy custom web page
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head><meta charset="utf-8"><title>Test</title></head>
            <body><h1>Apache web server running on Rocky Linux</h1></body>
          </html>
...
```

---

## `apache-suse.yml`

```yaml
---  # apache-suse.yml
- hosts: suse
  tasks:
    - name: Install Apache
      zypper:
        name: apache2

    - name: Start and enable Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Deploy custom web page
      copy:
        dest: /srv/www/htdocs/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head><meta charset="utf-8"><title>Test</title></head>
            <body><h1>Apache web server running on SUSE Linux</h1></body>
          </html>
...
```

---

<div align="center">

[Chapitre précédent](./04-idempotence.md) · [Accueil](../README.md) · [Chapitre suivant](./06-handlers.md)

</div>
