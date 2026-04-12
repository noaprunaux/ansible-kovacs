# Variables enregistrées

> Capturer la sortie de commandes avec `register` et l'afficher via `debug`.

---

## `kernel.yml` — paramètre `msg`

```yaml
---  # kernel.yml
- hosts: all
  gather_facts: false

  tasks:
    - name: View kernel informations
      command: uname -a
      changed_when: false
      register: kernel_param

    - debug:
        msg: "{{ kernel_param.stdout_lines }}"
...
```

---

## `kernel2.yml` — paramètre `var`

```yaml
---  # kernel2.yml
- hosts: all
  gather_facts: false

  tasks:
    - name: View kernel information
      command: uname -a
      changed_when: false
      register: kernel_params2

    - debug:
        var: kernel_params2.stdout_lines
...
```

### Différence `msg` vs `var`

| Paramètre | Syntaxe | Remarque |
|---|---|---|
| `msg` | `"{{ kernel_param.stdout_lines }}"` | Interpolation dans une chaîne |
| `var` | `kernel_params2.stdout_lines` | Référence directe, sans guillemets ni accolades |

---

## `packages.yml` — Nombre de paquets RPM

```yaml
---  # packages.yml
- hosts: rocky, suse
  gather_facts: false

  tasks:
    - name: How many packages
      shell: rpm -qa | wc -l
      changed_when: false
      register: rpm_count

    - debug:
        var: rpm_count.stdout_lines
...
```

**Résultat :**
ok: [rocky] => { "rpm_count.stdout_lines": ["642"] }
ok: [suse] => { "rpm_count.stdout_lines": ["504"] }

> `shell` est utilisé à la place de `command` car la commande contient un pipe `|`. `changed_when: false` empêche la tâche d'être marquée `CHANGED` à chaque exécution.

---

<div align="center">

[Chapitre précédent](./07-variables.md) · [Accueil](../README.md) · [Chapitre suivant](./09-facts.md)

</div>
