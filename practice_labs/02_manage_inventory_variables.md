**Lab 2: Managing Inventory Variables**

**Objectives**

- Structure host and group variables using multiple files per host or group
- Use special variables (`ansible_host`, `ansible_port`, `ansible_user`) to override connection details
- Organize inventory directory with multiple host variable files
- Override inventory hostnames with a custom name or IP address

---

### Environment & Prerequisites

- **Control node:** controller.lab.example.com (AAP 2.4)
- **Managed hosts:** node1.lab.example.com, node2.lab.example.com
- **User:** `rhel` (password: `redhat`)
- **GitHub repo:** `lab2-inventory` (URL: `https://github.com/<YourUsername>/lab2-inventory.git`)

---

### Project Setup

```bash
ssh rhel@controller.lab.example.com  # password: redhat
sudo yum install -y ansible git
mkdir ~/lab2-inventory && cd ~/lab2-inventory
git init
```  

Create the inventory directory structure:

```bash
mkdir -p inventories/prod/group_vars
mkdir -p inventories/prod/host_vars
```  

---

### Steps

1. **Define hosts in `inventories/prod/inventory`**
   ```ini
   [webservers]
   web1 ansible_host=node1.lab.example.com
   web2 ansible_host=node2.lab.example.com ansible_port=2222
   ```

2. **Create group variables**
   
   **`inventories/prod/group_vars/webservers.yml`**
   ```yaml
   ---
   http_port: 8080
   max_clients: 200
   ```

3. **Create host-specific variables**
   
   **`inventories/prod/host_vars/web1.yml`**
   ```yaml
   ---
   http_port: 9090        # override group default
   ```

   **`inventories/prod/host_vars/web2.yml`**
   ```yaml
   ---
   ansible_user: rhel_admin  # override default remote_user
   ```

4. **Write a simple playbook**
   **`site.yml`**:
   ```yaml
   ---
   - name: Test inventory vars on webservers
     hosts: webservers
     tasks:
       - name: Show host and port
         ansible.builtin.debug:
           msg: "{{ inventory_hostname }} -> {{ ansible_host }}:{{ ansible_port }}"
       - name: Show HTTP port and max clients
         ansible.builtin.debug:
           msg: "HTTP on {{ inventory_hostname }} runs on port {{ http_port }} with max {{ max_clients }} clients"
   ```

5. **Run the playbook**
   ```bash
   ansible-playbook site.yml -i inventories/prod/inventory
   ```

   - Confirm that `web1` uses `http_port: 9090` and default `ansible_port`
   - Confirm that `web2` uses `ansible_port: 2222` and default `http_port: 8080`

6. **Override inventory host names**

   6.1. Edit `inventories/prod/inventory` to use real IPs but alias them:
   ```ini
   [dbservers]
   db1 ansible_host=192.168.1.10
   db2 ansible_host=192.168.1.11
   ```

   6.2. Run a test play:
   ```yaml
   ---
   - name: Ping DB servers
     hosts: dbservers
     tasks:
       - ansible.builtin.ping:
   ```
   ```bash
   ansible-playbook ping_db.yml -i inventories/prod/inventory
   ```

7. **Version control your work**
   ```bash
   git add .
   git commit -m "Lab2: inventory variable organization and overrides"
   git remote add origin https://github.com/<YourUsername>/lab2-inventory.git
   git push -u origin main
   ```

---

**Success criteria**

- `ansible-playbook site.yml` outputs correct HTTP port and user overrides
- `ansible-playbook ping_db.yml` successfully pings IP-based aliases
- Inventory directory has separate `group_vars` and `host_vars` files

*End of Lab 2: Managing Inventory Variables*

