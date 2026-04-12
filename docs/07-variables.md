# Variables

> Maîtriser les différentes façons de définir des variables dans Ansible.

---

## 1. Play vars — `myvars1.yml`

```yaml
---  # myvars1.yml
- hosts: localhost
  gather_facts: false

  vars:
    mycar: Aston Martin DBS
    mybike: Suzuki GSXR 1000

  tasks:
    - debug:
        msg: "My car is: {{ mycar }} and my bike is: {{ mybike }}"
...
```

**Remplacement via extra vars :**

```bash
ansible-playbook myvars1.yml -e mycar="Peugeot_406"
ansible-playbook myvars1.yml -e mybike="Peugeot_103"
ansible-playbook myvars1.yml -e "mycar=Ferrari mybike=Ducati"
```

---

## 2. set_fact — `myvars2.yml`

```yaml
---  # myvars2.yml
- hosts: localhost
  gather_facts: false

  tasks:
    - name: Define variables
      set_fact:
        mycar: Aston Martin DBS
        mybike: Suzuki GSXR 1000

    - debug:
        msg: "My car is {{ mycar }} and my bike is {{ mybike }}"
...
```

> Les extra vars ont toujours la priorité sur `set_fact`.

---

## 3. Group vars & Host vars — `myvars3.yml`

**Playbook :**

```yaml
---  # myvars3.yml
- hosts: all
  gather_facts: false

  tasks:
    - debug:
        msg: "My car is {{ mycar }} and my bike is {{ mybike }}"
...
```

**`group_vars/all.yml` :**

```yaml
***
mycar: VW
mybike: BMW
...
```

**`host_vars/target02.yml` :**

```yaml
***
mycar: Mercedes
mybike: Honda
...
```

**Résultat :**
ok: [target01] => { "msg": "My car is VW and my bike is BMW" }
ok: [target02] => { "msg": "My car is Mercedes and my bike is Honda" }
ok: [target03] => { "msg": "My car is VW and my bike is BMW" }

> `host_vars` a priorité sur `group_vars`.

---

## 4. Vars prompt — `display_user.yml`

```yaml
---  # display_user.yml
- hosts: localhost
  gather_facts: false

  vars_prompt:
    - name: user
      prompt: Please write your name
      default: microlinux
      private: false

    - name: password
      prompt: Please write your password (secret)
      default: yatahongaga
      private: true

  tasks:
    - debug:
        msg: "Your name is {{ user }} and your password is {{ password }}"
...
```

> `private: true` masque la saisie du mot de passe dans le terminal.

---

<div align="center">

[Chapitre précédent](./06-handlers.md) · [Accueil](../README.md) · [Chapitre suivant](./08-variables-enregistrees.md)

</div>
