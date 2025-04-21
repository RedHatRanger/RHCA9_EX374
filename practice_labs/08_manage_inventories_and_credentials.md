**Lab 8: Managing Inventories & Credentials**

**Objectives**

1. **Manage advanced inventories** (static & grouped variables)
2. **Create a dynamic inventory** from an identity management server or a database server
3. **Create machine credentials** to access inventory hosts in Automation Controller
4. **Create a source control credential** in Automation Controller

---

### Introduction
In this lab you will explore both static and dynamic inventory management with Ansible Automation Platform 2.4. You’ll build a multi‑file inventory hierarchy with group_vars/ and host_vars/, then configure two types of dynamic inventories (LDAP and database). Finally, you’ll switch to the Automation Controller UI to define machine and source control credentials, enabling secure access to managed hosts and repositories.

### Environment & Setup
- **Control node**: `workstation.lab.example.com` (user `rhel`, password `redhat`)
- **Automation Controller**: `controller.lab.example.com` (user `rhel`, password `redhat`)
- **Static inventory root**: `~/lab8-inventories/`
- **Dynamic inventory plugins**: `ldap` (python-ldap) and `community.mysql.mysql`

```bash
ssh rhel@workstation.lab.example.com      # password: redhat
sudo yum install -y ansible python3-ldap python3-PyMySQL
mkdir -p ~/lab8-inventories/{inventories/{prod,dev}/{group_vars,host_vars},plugins/inventory}
cd ~/lab8-inventories
```  

---

## 1. Manage advanced (static) inventories ← Objective 1

1. **Create a production inventory** in `inventories/prod/inventory`:

   ```ini
   [webservers]
   node1.lab.example.com
   node2.lab.example.com

   [databases]
   db1.lab.example.com
   db2.lab.example.com
   ```

2. **Define group variables** for `webservers` in `inventories/prod/group_vars/webservers.yml`:

   ```yaml
   http_port: 8080    # example of grouped var overriding defaults
   ```

3. **Define host-specific variables** in `inventories/prod/host_vars/node1.lab.example.com.yml`:

   ```yaml
   ansible_port: 2222       # override SSH port for a single host
   ansible_user: deploy     # override SSH user
   ```

4. **Validate** by listing inventory and showing vars:

   ```bash
   ansible-inventory -i inventories/prod/inventory --list
   ansible-inventory -i inventories/prod/inventory --host node1.lab.example.com
   ```

---

## 2. Create a dynamic inventory ← Objective 2

### A. LDAP-based dynamic inventory (Identity Management)

1. **Place the LDAP plugin config** in `plugins/inventory/ldap_inventory.yml`:
   ```yaml
   plugin: ldap
   # demo values; replace with your IDM server details
   uri: ldap://idm.example.com
   bind_dn: 'cn=admin,dc=example,dc=com'
   bind_pw: 'redhat'
   base_dn: 'dc=example,dc=com'
   group_search:
     filter: '(objectClass=groupOfNames)'
     attribute: cn
   host_search:
     filter: '(objectClass=person)'
     attribute: cn
   ```

2. **Test the LDAP inventory**:
   ```bash
   ansible-inventory -i plugins/inventory/ldap_inventory.yml --list
   ```

### B. Database-based dynamic inventory

1. **Place the MySQL plugin config** in `plugins/inventory/db_inventory.yml`:
   ```yaml
   plugin: community.mysql.mysql
   db_host: db.example.com
   db_user: ansible
   db_pass: redhat
   query: |
     SELECT hostname, ip_address, role FROM servers;
   keyed_groups:
     - key: role
       prefix: role_
   ```

2. **Test the MySQL inventory**:
   ```bash
   ansible-inventory -i plugins/inventory/db_inventory.yml --list
   ```

---

## 3. Create machine credentials in Controller ← Objective 3

1. **Log in** to the Automation Controller UI at `https://controller.lab.example.com` with `rhel/redhat`.
2. Navigate to **Credentials » Add Credential » Machine**.
3. **Name**: `lab8-machine`, **Description**: `SSH to managed hosts`
4. **Credential Type**: Machine
   - **Username**: `rhel`
   - **Password**: `redhat`
   - Or **Private Key**: Upload or paste key
5. **Save** and **Test** against one host (e.g., `node1.lab.example.com`).

---

## 4. Create a source control credential ← Objective 4

1. In **Controller UI**, go to **Credentials » Add Credential » Source Control**.
2. **Name**: `lab8-git`, **Description**: `GitHub token for playbook repo`
3. **Credential Type**: Source Control
   - **SCM Type**: Git
   - **Username**: your GitHub username
   - **Password/Token**: GitHub personal access token
4. **Save** and optionally **Test** by linking a project.

---

**Outcome**: You have structured a multi‑file static inventory, created two types of dynamic inventories, and defined both machine and source‑control credentials in Automation Controller, covering the RHCA objectives for advanced inventory & credentials.
