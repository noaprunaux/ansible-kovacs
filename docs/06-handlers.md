# Handlers

> Déclencher des tâches uniquement en cas de changement de configuration.

---

## `chrony.yml`

```yaml
---  # chrony.yml
- hosts: redhat
  tasks:
    - name: Install package chrony
      dnf:
        name: chrony

    - name: Install configuration
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
      notify: Reload Chrony

  handlers:
    - name: Reload Chrony
      service:
        name: chronyd
        state: restarted
...
```

---

## Résultat — 1ère exécution (handler déclenché)
TASK [Install configuration] **
changed: [target01]
changed: [target02]
changed: [target03]

RUNNING HANDLER [Reload Chrony] **
changed: [target01]
changed: [target02]
changed: [target03]

PLAY RECAP **
target01 : ok=4 changed=2 unreachable=0 failed=0


## Résultat — 2ème exécution (idempotence)
TASK [Install configuration] **
ok: [target01]
ok: [target02]
ok: [target03]

PLAY RECAP **
target01 : ok=3 changed=0 unreachable=0 failed=0


> Le handler `Reload Chrony` n'est exécuté que si le fichier de configuration a réellement été modifié. Lors de la 2ème exécution, aucun changement n'est détecté et le service n'est pas redémarré inutilement.

---

<div align="center">

[Chapitre précédent](./05-playbooks-apache.md) · [Accueil](../README.md) · [Chapitre suivant](./07-variables.md)

</div>
