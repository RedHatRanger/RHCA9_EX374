**Lab 1: Understanding and Using Git**

**Objectives**

- Use Git to manage an Ansible project:
  - Configure Git global settings (user.name, user.email, push.default)
  - Create and clone a GitHub repository
  - Create a feature branch, commit changes, and push to GitHub

---

### Environment

- **Control node:** controller.lab.example.com (AAP 2.4)
- **User:** `rhel` (password: `redhat`)
- **GitHub repo name:** `lab1` (URL: `https://github.com/<YourUsername>/lab1.git`)

---

### Steps

1. **SSH into the control node**
   ```bash
   ssh rhel@controller.lab.example.com  # password: redhat
   ```

2. **Configure your Git identity**
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email you@example.com
   git config --global push.default simple

   # Verify your settings:
   git config --global -l
   ```

3. **Create and configure your GitHub repository**

   3.1. In a browser, go to https://github.com and log in (or sign up).

   3.2. Click **+** → **New repository**:
   - **Repository name:** lab1
   - **Visibility:** Public or Private
   - Click **Create repository**.

   3.3. On the new repo page, copy the HTTPS clone URL (e.g. `https://github.com/<YourUsername>/lab1.git`).

4. **Clone your GitHub repo and create a branch**
   ```bash
   cd ~
   git clone https://github.com/<YourUsername>/lab1.git
   cd lab1
   git checkout -b exercise
   ```

5. **Create project files**

   5.1. **ansible.cfg**
   ```ini
   [defaults]
   inventory = ./inventory
   remote_user = rhel
   host_key_checking = False

   [ssh_connection]
   pipelining = True
   ```

   5.2. **site.yml**
   ```yaml
   ---
   - name: Deploy and validate HTTP service on node1
     hosts: node1.lab.example.com
     become: true

     tasks:
       - name: Install and start HTTPD
         ansible.builtin.yum:
           name: httpd
           state: latest

       - name: Ensure HTTPD is running
         ansible.builtin.service:
           name: httpd
           enabled: true
           state: started

       - name: Test HTTP service
         ansible.builtin.uri:
           url: http://node1.lab.example.com
           return_content: false
           status_code: 200
   ```

   5.3. **inventory**
   ```ini
   [node1]
   node1.lab.example.com ansible_user=rhel ansible_password=redhat
   ```

6. **Verify project layout**
   ```bash
   tree
   # ansible.cfg  inventory  site.yml
   ```

7. **Run the playbook using Ansible CLI**

   Execute with `ansible-playbook`:
   ```bash
   ansible-playbook site.yml -i inventory
   ```

8. **Commit and push your changes****
   ```bash
   git add ansible.cfg site.yml inventory
   git commit -m "Initial HTTPD playbook with validation"
   git push -u origin exercise
   ```

---

**Success criteria**

- `ansible-navigator run` completes all tasks successfully
- `git log` shows your commit on branch `exercise`
- `git branch -r` lists `origin/exercise`

*End of Lab 1*

