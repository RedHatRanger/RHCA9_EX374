# Lab: Git Repository Management

This lab covers the "Understand and use Git" objective of the EX374 exam. You'll learn how to clone repositories, create and push files, and manage inventory files using Git.

## Prerequisites

- Basic understanding of Git concepts
- Git installed on your system

## Lab Environment Setup

1. **Install Git if not already installed**
   ```bash
   sudo dnf install -y git
   ```

2. **Configure Git identity**
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "your.email@example.com"
   git config --global push.default simple
   ```

3. **Create a working directory**
   ```bash
   mkdir -p ~/git-repos
   cd ~/git-repos
   ```

## Cloning a Git Repository

In this exercise, you'll clone a sample Ansible repository.

1. **Clone the repository**
   ```bash
   git clone https://github.com/RedHatRanger/devops-review.git
   cd 
   ```

2. **Explore the repository structure**
   ```bash
   ls -la
   git log --oneline -n 5
   git branch -a
   ```

3. **Create a new branch for your work**
   ```bash
   git checkout -b exercise
   ```
# Lab Change Instructions: Detailed

Below is a step-by-step breakdown of **what** exact changes to make to go from the **pre-lab** state to the **finished** state, mirroring the book’s lab instructions.

---
## 1. Create a new branch named `exercise`

From within the cloned **`devops-review`** directory:

```bash
git checkout -b exercise
```

---
## 2. Add a name to the play in `site.yml`

### Before
```yaml
---
- hosts: all
  become: true
  roles:
    - test
```

### After
```yaml
---
- name: Sets the GRUB timeout  # <--- newly added line
  hosts: all
  become: true
  roles:
    - test
```

In other words, insert:

```yaml
name: Sets the GRUB timeout
```

right below the `---` line.

---
## 3. Add names to the tasks in `roles/test/tasks/main.yml`

### Before
```yaml
---
- ansible.builtin.lineinfile:
    path: /etc/default/grub
    regexp: "^GRUB_TIMEOUT="
    line: "GRUB_TIMEOUT={{ timeout | int }}"
    when: persistent | bool == true

- ansible.builtin.lineinfile:
    path: /boot/grub2/grub.cfg
    regexp: "^set timeout="
    line: "set timeout={{ timeout | int }}"
```

### After
```yaml
---
- name: Sets persistent GRUB timeout  # <--- added
  ansible.builtin.lineinfile:
    path: /etc/default/grub
    regexp: "^GRUB_TIMEOUT="
    line: "GRUB_TIMEOUT={{ timeout | int }}"
    when: persistent | bool == true

- name: Sets temporary GRUB timeout   # <--- added
  ansible.builtin.lineinfile:
    path: /boot/grub2/grub.cfg
    regexp: "^set timeout="
    line: "set timeout={{ timeout | int }}"
```

So each task has a `name:`.

---
## 4. Rename the `test` role to `grub` and update references

1. **Rename the folder**:
   ```bash
   git mv roles/test roles/grub
   ```

2. **Update** `site.yml` to reference `- grub` instead of `- test`.

### `site.yml` Before
```yaml
---
- name: Sets the GRUB timeout
  hosts: all
  become: true
  roles:
    - test
```

### `site.yml` After
```yaml
---
- name: Sets the GRUB timeout
  hosts: all
  become: true
  roles:
    - grub
```

---
## 5. Change `timeout` and `persistent` to `grub_timeout` and `grub_persistent`

### Files to change:
1. **`roles/grub/defaults/main.yml`**
2. **`roles/grub/tasks/main.yml`**
3. **`group_vars/lb_servers.yml`**

In each place, replace **`timeout`** with **`grub_timeout`** and **`persistent`** with **`grub_persistent`**.


### Examples

#### `roles/grub/defaults/main.yml`

**Before**:
```yaml
---
timeout: 0
persistent: true
```

**After**:
```yaml
---
grub_timeout: 0
grub_persistent: true
```

---

#### `roles/grub/tasks/main.yml`

**Before** (with names already added):
```yaml
---
- name: Sets persistent GRUB timeout
  ansible.builtin.lineinfile:
    path: /etc/default/grub
    regexp: "^GRUB_TIMEOUT="
    line: "GRUB_TIMEOUT={{ timeout | int }}"
    when: persistent | bool == true

- name: Sets temporary GRUB timeout
  ansible.builtin.lineinfile:
    path: /boot/grub2/grub.cfg
    regexp: "^set timeout="
    line: "set timeout={{ timeout | int }}"
```

**After**:
```yaml
---
- name: Sets persistent GRUB timeout
  ansible.builtin.lineinfile:
    path: /etc/default/grub
    regexp: "^GRUB_TIMEOUT="
    line: "GRUB_TIMEOUT={{ grub_timeout | int }}"
    when: grub_persistent | bool == true

- name: Sets temporary GRUB timeout
  ansible.builtin.lineinfile:
    path: /boot/grub2/grub.cfg
    regexp: "^set timeout="
    line: "set timeout={{ grub_timeout | int }}"
```

---

#### `group_vars/lb_servers.yml`

**Before**:
```yaml
---
timeout: 30
persistent: true
```

**After**:
```yaml
---
grub_timeout: 30
grub_persistent: true
```

---
## 6. Commit and Push

When all changes are done:

```bash
git add .
git commit -m "Lab changes: added names, renamed role, changed variable names"
git push -u origin exercise
```

Now you have an `exercise` branch on GitHub with all the modifications. The lab is complete.

---

## 7. Verification

- **Check** that your `exercise` branch on GitHub has the updated files:
  - `roles/grub/`
  - Variables renamed
  - Play name added
  - Task names added
    
- **OPTIONAL: Run** the playbook to ensure everything works:
  ```bash
  ansible-navigator run site.yml -eei ee-supported-rhel8 --pp missing -m stdout -C
  ```

---

## 8. Repeat If Needed

To restart:
1. Remove the local directory:
   ```bash
   cd ~
   rm -rf ~/git-repos/devops-review
   ```
2. Clone again:
   ```bash
   git clone https://github.com/YOUR_GITHUB_USERNAME/devops-review.git
   cd devops-review
   ```
3. Checkout a fresh branch (`exercise2`, for example) and do the same steps.

You can replicate the lab’s final state any number of times.


## Conclusion

In this lab, you've learned how to:
- Clone a Git repository
- Create and modify files in a Git repository
- Manage inventory files using Git
- Use multiple files per host or group
- Configure remote host settings
- Set up directories with multiple host variable files
- Override names in inventory files
- Push changes to a remote repository

These skills are essential for the "Understand and use Git" objective of the EX374 exam.

## Additional Resources

- [EX374 Objectives](https://github.com/RedHatRanger/RHCA9_EX374/blob/main/objectives.md)
- [Git Documentation](https://git-scm.com/doc)
- [Ansible Inventory Documentation](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)
- [Ansible Variables Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html)
