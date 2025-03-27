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

**Result**: The lab confirms your EE and config settings function as intended (No --eei --pp parameters needed!).

---

## Which EX374 Objectives?

1. **Manage Ansible configuration**: Setting `forks`, referencing the local inventory, controlling artifact creation.
2. **Manage execution environments** (Partial): Specifying the container image and pull policy in `ansible-navigator.yml`.
3. **Manage task execution**: Using `--ask-become-pass`, verifying the playbook runs with privilege escalation.

No advanced roles or collections usage here, but you demonstrate essential config usage and verifying it by running a simple playbook.


**Lab Complete!**


<br><br><br><br>
## Ansible Navigator Configuration Summary
- `ansible-navigator config` shows you the current config used by ansible-navigator run.
- Only config files visible inside the execution environment (like /etc/ansible/ansible.cfg or one in your project directory) are used.
- You can configure Navigator with:
> ANSIBLE_NAVIGATOR_CONFIG environment variable
> ansible-navigator.yml in your current directory
> ~/.ansible-navigator.yml in your home directory

- Use `ansible-navigator settings --sample` to generate a sample config file.
- Use `ansible-navigator settings --effective` to display your final config (merged from CLI, env vars, and config files).

**Important**
If you redirect the output of the ansible-navigator settings command into a file named ansible-navigator.yml in the current directory, then the `ansible-navigator`
command attempts to use the configuration file and ultimately fails. Instead, redirect the command output to a file in a different directory, such as to the 
`/tmp/ansible-navigator.yml` file, or to a file with a different name in the current directory, such as sample.yml. After the command completes, you can 
copy or move the generated file to the desired directory with the correct name.
```bash
ansible-navigator settings --effective --pp missing --eei ee-supported-rhel8 > /tmp/ansible-navigator.yml
```
