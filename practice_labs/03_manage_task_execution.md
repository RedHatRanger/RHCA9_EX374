**Lab 3: Managing Task Execution**

**Objectives**

- Control privilege escalation (`become`, `become_user`, `become_method`)
- Use the `--tags` option to run selected tasks from a playbook

---

### Environment & Prerequisites

- **Control node:** controller.lab.example.com (AAP 2.4)
- **Managed hosts:** node1.lab.example.com, node2.lab.example.com
- **User:** `rhel` (password: `redhat`)
- **GitHub repo:** `lab3-tasks` (URL: `https://github.com/<YourUsername>/lab3-tasks.git`)

---

### Project Setup

```bash
ssh rhel@controller.lab.example.com  # password: redhat
sudo yum install -y ansible git
mkdir ~/lab3-tasks && cd ~/lab3-tasks
git init
```  

Create project files:

```bash
mkdir playbooks
```

---

### Steps

1. **Clone or create your GitHub repo**  
   - In a browser, create `lab3-tasks` on GitHub and copy its HTTPS URL.
   - Back on the control node:
     ```bash
     git remote add origin https://github.com/<YourUsername>/lab3-tasks.git
     ```

2. **Write a playbook with privileged tasks and tags**  
   **`playbooks/manage_services.yml`**
   ```yaml
   ---
   - name: Manage critical services
     hosts: all
     become: true        # enable privilege escalation
     become_user: root   # escalate to root

     tasks:
       - name: Install HTTPD
         ansible.builtin.yum:
           name: httpd
           state: present
         tags:
           - install

       - name: Start HTTPD
         ansible.builtin.service:
           name: httpd
           state: started
           enabled: true
         tags:
           - configure

       - name: Install MariaDB
         ansible.builtin.yum:
           name: mariadb-server
           state: present
         tags:
           - install

       - name: Start MariaDB
         ansible.builtin.service:
           name: mariadb
           state: started
           enabled: true
         tags:
           - configure
   ```

3. **Define inventory**  
   **`inventory`**
   ```ini
   [all]
   node1.lab.example.com ansible_user=rhel ansible_password=redhat
   node2.lab.example.com ansible_user=rhel ansible_password=redhat
   ```

4. **Verify privilege escalation**  
   Run only the install tasks:
   ```bash
   ansible-playbook playbooks/manage_services.yml \
     -i inventory \
     --tags install
   ```
   - Confirm HTTPD and MariaDB packages are installed as root.

5. **Run configuration tasks only**  
   ```bash
   ansible-playbook playbooks/manage_services.yml \
     -i inventory \
     --tags configure
   ```
   - Confirm services are started and enabled.

6. **Run the full playbook**  
   ```bash
   ansible-playbook playbooks/manage_services.yml \
     -i inventory
   ```

7. **Commit and push your changes**  
   ```bash
   git add playbooks manage_services.yml inventory
   git commit -m "Add task management playbook with become and tags"
   git push -u origin main
   ```

---

**Success criteria**

- `ansible-playbook --tags install` installs packages only
- `ansible-playbook --tags configure` starts services only
- Full playbook run without errors under root privileges

*End of Lab 3: Managing Task Execution*
