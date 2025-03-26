# Lab: Working with Ansible Configuration Settings

## Overview
In this lab, you will:
1. Configure **ansible-navigator** to use a specific container image, set a pull policy, and disable artifact creation.
2. Update **ansible.cfg** to specify `forks=15`.
3. Run a simple playbook (`install-web.yml`) to validate your changes.

### Final Outcome
When you run the lab’s playbook against `servera.lab.example.com`, Apache (HTTPD) is installed successfully, and you confirm your Ansible configuration is correct.

---

## 1. Prepare Your Project Folder

Suppose you have a folder named `~/config-review`:

```
config-review/
├── ansible-navigator.yml
├── ansible.cfg
├── inventory
└── install-web.yml
```

- `ansible-navigator.yml`: Navigator-level settings.
- `ansible.cfg`: Traditional Ansible config.
- `inventory`: Has `servera.lab.example.com` with `ansible_user=student`, `ansible_become=true`, etc.
- `install-web.yml`: A minimal playbook installing an HTTP server.

---

## 2. Configure `ansible-navigator.yml`

1. **Edit** `~/config-review/ansible-navigator.yml`.
2. Ensure it includes:

```yaml
---
ansible-navigator:
  execution-environment:
    enabled: True
    image: hub.lab.example.com/ee-supported-rhel8:latest
    pull:
      policy: missing
  logging:
    level: critical
  playbook-artifact:
    enable: False
```

### Explanation
- **`image: hub.lab.example.com/ee-supported-rhel8:latest`**: The default container image.
- **`policy: missing`**: Only pull the image if it’s not present locally.
- **`playbook-artifact: enable: False`**: Disables artifact creation, which can otherwise conflict with `--ask-become-pass`.

---

## 3. Configure `ansible.cfg`

1. In `~/config-review/ansible.cfg`, add:

```ini
[defaults]
forks = 15
inventory = inventory

[privilege_escalation]
prompt = True
become = True
become_method = sudo
```

### Explanation
- `forks = 15` increases parallelism.
- `inventory = inventory` points to your local file.
- The `[privilege_escalation]` block ensures we can prompt for a become password.

---

## 4. Check Your Configuration

Run:

```bash
ansible-navigator config
```

- In the TUI, type `:f forks`.
- You should see `forks: 15` from `/home/student/config-review/ansible.cfg`.
- Press Esc twice to exit.

---

## 5. Run the `install-web.yml` Playbook

1. **Command**:
   ```bash
   ansible-navigator run install-web.yml --ask-become-pass -m stdout
   ```
2. **Enter** the become password (`student`) if prompted.
3. The play runs in the specified EE, and you should see:
   ```
   PLAY RECAP
   servera.lab.example.com : ok=2 changed=1 unreachable=0 failed=0
   ```
4. Apache (HTTPD) should be installed on `servera.lab.example.com`.

---

## 6. Repeatability

To **redo** the lab:
1. Remove or rename `ansible-navigator.yml` and `ansible.cfg`.
2. Recreate them with the same content.
3. Rerun `ansible-navigator run install-web.yml --ask-become-pass`.

You can verify your config each time via `ansible-navigator config`.

---

## Which EX374 Objectives?
1. **Manage execution environments** (partial): Specifying the container image and pull policy.
2. **Manage Ansible configuration**: Adjusting `ansible.cfg` (`forks`) and disabling playbook artifacts.
3. **Manage task execution**: Using `--ask-become-pass`, ensuring the playbook runs with elevated privileges.

No advanced roles or collections here, but this lab **demonstrates** essential Ansible config usage, setting up an EE in `ansible-navigator`, and verifying changes.

**Lab Complete!**

