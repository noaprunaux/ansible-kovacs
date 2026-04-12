# Chrony

> Déployer la synchronisation NTP avec Chrony sur quatre distributions Linux.

---

## Référence par distribution

| Distribution | `ansible_os_family` | Module | Service | Fichier de configuration |
|---|---|---|---|---|
| Debian / Ubuntu | `Debian` | `apt` | `chrony` | `/etc/chrony/chrony.conf` |
| Rocky Linux | `RedHat` | `dnf` | `chronyd` | `/etc/chrony.conf` |
| SUSE Linux | `Suse` | `zypper` | `chronyd` | `/etc/chrony.conf` |

> Le nom du service systemd diffère selon la famille : `chrony` sur Debian/Ubuntu, `chronyd` sur RedHat/SUSE.

---

## Exercice 1 — Approche native : `chrony-01.yml`

> Utilisation des modules `apt`, `dnf` et `zypper` avec des conditions `when`.

```yaml
---  # chrony-01.yml
- hosts: all

  tasks:
    - name: Update package information on Debian/Ubuntu
      apt:
        update_cache: true
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"

    - name: Install Chrony on Debian/Ubuntu
      apt:
        name: chrony
      when: ansible_os_family == "Debian"

    - name: Install Chrony on RedHat
      dnf:
        name: chrony
      when: ansible_os_family == "RedHat"

    - name: Install Chrony on SUSE
      zypper:
        name: chrony
      when: ansible_os_family == "Suse"

    - name: Install configuration on RedHat and SUSE
      copy:
        dest: /etc/chrony.conf
        content: |
          # /etc/chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      when: ansible_os_family in ["RedHat", "Suse"]
      notify: Reload Chrony RedHat Suse

    - name: Install configuration on Debian/Ubuntu
      copy:
        dest: /etc/chrony/chrony.conf
        content: |
          # /etc/chrony/chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      when: ansible_os_family == "Debian"
      notify: Reload Chrony Debian

  handlers:
    - name: Reload Chrony RedHat Suse
      service:
        name: chronyd
        state: restarted

    - name: Reload Chrony Debian
      service:
        name: chrony
        state: restarted
...
```

---

## Exercice 2 — Approche par variables : `chrony-02.yml`

> Module générique `package` et variables définies dans `group_vars`.

### Structure des `group_vars`

**`group_vars/debian.yml`** et **`group_vars/ubuntu.yml`** :

```yaml
***
chrony_package: chrony
chrony_service: chrony
chrony_confdir: /etc/chrony
...
```

**`group_vars/rocky.yml`** et **`group_vars/suse.yml`** :

```yaml
***
chrony_package: chrony
chrony_service: chronyd
chrony_confdir: /etc
...
```

### Playbook

```yaml
---  # chrony-02.yml
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

    - name: Install configuration
      copy:
        dest: "{{ chrony_confdir }}/chrony.conf"
        content: |
          # {{ chrony_confdir }}/chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      notify: Reload Chrony

  handlers:
    - name: Reload Chrony
      service:
        name: "{{ chrony_service }}"
        state: restarted
...
```

> Un seul handler suffit : `{{ chrony_service }}` résout automatiquement `chrony` ou `chronyd` selon la distribution.

---

<div align="center">

[Chapitre précédent](./09-facts.md) · [Accueil](../README.md) · [Chapitre suivant](./11-jinja-templates.md)

</div>
