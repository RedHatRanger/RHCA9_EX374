# Lab: Working with Ansible Configuration Settings (Git-Clone Edition)

## Overview
In this lab, you will:
1. **Clone** a Git repository that includes all Ansible configuration files and a sample playbook.
2. Configure **ansible-navigator** to use a specific container image, set a pull policy, and disable artifact creation (via `ansible-navigator.yml`).
3. Update **ansible.cfg** to specify `forks=15`.
4. **Run** the `install-web.yml` playbook to verify everything is configured correctly.

### Final Outcome
When you run the lab’s playbook against `node1.lab.example.com`, Apache (HTTPD) is installed successfully, confirming your Ansible configuration is correct.

---

## 1. Clone the Lab Repository
1. Choose a local directory for your labs, for example:
   ```bash
   mkdir -p ~/config-review
   cd ~/config-review
   ```
2. **Clone** the repository (adjust the URL if necessary):
   ```bash
   git clone https://github.com/RedHatRanger/config-review.git
   cd config-review
   ```
3. Your local structure should look like:
   ```
   config-review/
   ├── ansible-navigator.yml
   ├── ansible.cfg
   ├── inventory
   └── install-web.yml
   ```

**Note**: The repository includes:
- `ansible-navigator.yml`: Configures Navigator’s EE image, pull policy, and artifact settings.
- `ansible.cfg`: Adjusts default Ansible settings like `forks`.
- `inventory`: Points to `node1.lab.example.com` with an appropriate user/credentials.
- `install-web.yml`: A simple playbook installing Apache.

---

## 2. Review `ansible-navigator.yml`

Open **`ansible-navigator.yml`**:

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
- **`image`:** The default container image for execution environments.
- **`policy: missing`:** Only pull if the image is missing locally.
- **`playbook-artifact.enable: False`:** Disables artifact creation (helps with `--ask-become-pass`).

If you need a different image or settings, edit them here. Commit and push your changes if you have permissions:
```bash
git add ansible-navigator.yml
git commit -m "Update EE image"
git push
```

---

## 3. Review `ansible.cfg`

Open **`ansible.cfg`**:

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
- **`forks = 15`:** Increase parallel tasks.
- **`inventory = inventory`:** Use the local `inventory` file by default.
- `[privilege_escalation]`: Ensures prompts for `--ask-become-pass` if needed.

Again, commit any changes if needed.

---

## 4. Validate Configuration

From the **config-review** directory, run:

```bash
ansible-navigator config
```

1. In the TUI, type `:f forks` and press Enter.
2. Confirm `forks = 15` from your `ansible.cfg`.
3. Press Esc twice to exit.

**Result**: You verified the local config is recognized by `ansible-navigator`.

---

## 5. Run the `install-web.yml` Playbook

1. Execute:
   ```bash
   ansible-navigator run install-web.yml --ask-become-pass -m stdout
   ```
   - **`--ask-become-pass`**: Prompts for the sudo password.
   - **`-m stdout`**: Outputs logs to your terminal.
2. Enter your become password (for example, `redhat`) if prompted.
3. The playbook installs Apache on **`node1.lab.example.com`**:
   ```
   PLAY RECAP
   node1.lab.example.com : ok=2 changed=1 unreachable=0 failed=0
   ```

**Result**: The lab confirms your EE and config settings function as intended.

---

## 6. Repeatability

Whenever you want a fresh lab:
1. **Remove** or rename the `config-review` folder:
   ```bash
   rm -rf ~/git-labs/config-review
   ```
2. **Re-clone**:
   ```bash
   git clone https://github.com/RedHatRanger/config-review.git
   cd config-review
   ```
3. Re-run `ansible-navigator run install-web.yml --ask-become-pass`.

**That’s it!** You can re-do the same lab steps.

---

## Which EX374 Objectives?

1. **Manage Ansible configuration**: Setting `forks`, referencing the local inventory, controlling artifact creation.
2. **Manage execution environments** (Partial): Specifying the container image and pull policy in `ansible-navigator.yml`.
3. **Manage task execution**: Using `--ask-become-pass`, verifying the playbook runs with privilege escalation.

No advanced roles or collections usage here, but you demonstrate essential config usage and verifying it by running a simple playbook.


**Lab Complete!**
