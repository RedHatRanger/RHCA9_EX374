**Lab 1: Git Project Setup**

**Objectives**

- Initialize a new Git repository for an Ansible project
- Configure Git user identity and repository layout
- Create, stage, and commit Ansible configuration and playbook files
- Create feature branches and merge changes back to `main`
- Push and pull from a remote Git repository hosted on `controller.lab.example.com`

---

### Environment & Prerequisites

- Control node: `controller.lab.example.com` (AAP 2.4)
- Managed hosts: `node1.lab.example.com`–`node4.lab.example.com`
- Ansible user: `rhel` (password `redhat` for SSH and sudo)
- Ensure SSH password auth:
  ```bash
  ssh rhel@controller.lab.example.com  # enter password redhat
  ```

---

### Lab Steps

1. **Install Git** on the control node:
   ```bash
   sudo yum install -y git
   ```

2. **Configure global Git identity** for the `rhel` user:
   ```bash
   git config --global user.name "rhel"
   git config --global user.email "rhel@lab.example.com"
   git config --global push.default simple

   # Verify settings:
   git config --global -l
   ```

3. **Initialize a new Ansible project**:
   ```bash
   mkdir -p ~/ansible-project
   cd ~/ansible-project
   git init
   ```

4. **Create basic Ansible files**:
   - `ansible.cfg`:
     ```ini
     [defaults]
     inventory = ./inventories/prod/inventory
     remote_user = rhel
     host_key_checking = False
     ```

   - `site.yml`:
     ```yaml
     ---
     - name: Ping all hosts
       hosts: all
       tasks:
         - name: Verify connectivity
           ansible.builtin.ping:
     ```

5. **Stage and commit** initial files:
   ```bash
   git add ansible.cfg site.yml
   git commit -m "Initial scaffold: ansible.cfg and site.yml"
   ```

6. **Create a feature branch** `dev-setup` and add an NGINX play:
   ```bash
   git checkout -b dev-setup
   cat << 'EOF' >> site.yml
   - name: Install NGINX on web servers
     hosts: webservers
     become: true
     tasks:
       - name: Ensure nginx is installed
         ansible.builtin.yum:
           name: nginx
           state: latest
   EOF
   git add site.yml
   git commit -m "Add nginx installation play for webservers"
   ```

7. **Merge** `dev-setup` into `main`:
   ```bash
   git checkout main
   git merge dev-setup
   ```

8. **Create a bare repo** on the control node and push:
   ```bash
   mkdir -p ~/git-repos/ansible-project.git
   cd ~/git-repos/ansible-project.git
   git init --bare

   cd ~/ansible-project
   git remote add origin rhel@controller.lab.example.com:~/git-repos/ansible-project.git
   git push -u origin main
   ```

9. **Validate** by cloning and listing inventory:
   ```bash
   cd ~
   git clone rhel@controller.lab.example.com:~/git-repos/ansible-project.git test-clone
   cd test-clone
   ansible-inventory --list -i inventories/prod/inventory
   ```

---

**Success Criteria**

- Git identity correctly set for `rhel` user
- Project scaffold committed and pushed to remote
- Feature branch merged into `main`
- Fresh clone works and `ansible-inventory` lists hosts

*End of Lab 1*

