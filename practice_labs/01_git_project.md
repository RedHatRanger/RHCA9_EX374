**Lab 1: Git Workflow for Ansible Projects**

**Objectives**

- Understand and use Git for Ansible project management:
  - Clone a Git repository
  - Create and switch to a new branch
  - Stage and commit changes
  - Push commits to a remote repository

---

### Environment

- **Control node:** controller.lab.example.com (AAP 2.4)  
- **User:** `rhel`  
- **SSH password:** `redhat`  
- **Git repository URL:** `https://github.com/<YourUserName>/lab1.git`

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
3.1 On any browser, navigate to https://github.com and log in or sign up for a free account.
3.2 Click **+** (top right) then **New repository**.
    - **Repository name:** lab1
    - **Visibility:** Public (or Private, if you prefer)
    - Leave other options at defaults, then click **Create repository**.
3.3 On the new repo page, under **Quick setup**, copy the HTTPS URL (e.g. `https://github.com/<YourUsername>/lab1.git`).

3.4 Back in your SSH session on `controller.lab.example.com`:
```bash
cd ~
# Clone your GitHub repo
git clone https://github.com/<YourUsername>/lab1.git
cd lab1
```

4. **Create and switch to a new branch**
 **Create and switch to a new branch**
   ```bash
   git checkout -b exercise
   ```

5. **Inspect the initial project files**
   ```bash
   tree
   # ansible.cfg  site.yml  inventory
   ```

6. **Modify the playbook**
   - Open `site.yml` and add a descriptive play name:
     ```yaml
     ---
     - name: Ensure HTTP service on node1
       hosts: all
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
     ```

7. **Stage and commit your changes**
   ```bash
   git add site.yml
   git commit -m "Add descriptive play name and HTTPD tasks"
   ```

8. **Push the branch to the remote repository**
   ```bash
   git push -u origin exercise
   ```

---

**Success criteria**

- `git log` shows your new commit on branch `exercise`  
- `git branch -r` lists `origin/exercise`  

*End of Lab 1*

