**Lesson 5 Summary: Managing Inventories and Credentials (Simple Version)**

---

### ✅ Objectives Checklist (EX374):
- [ ] Create and organize static and dynamic inventories
- [ ] Use inventory groups and host variables
- [ ] Manage credentials and match them to inventory and playbook needs
- [ ] Use inventory sources like SCM (Git)
- [ ] Test and validate inventory access

---

### 🧠 What You Learn in This Chapter:
This chapter focuses on **inventories**—the list of machines Ansible controls—and **credentials**, which are the secure keys to log into those machines.

You’ll learn how to:
- Create inventories and add hosts
- Organize those hosts into groups
- Add variables for specific hosts or groups
- Use credentials like SSH keys and vault passwords
- Use source control (like Git) to load inventories automatically

---

### 🎯 Why It Matters for the EX374 Exam:
- You’ll need to manage machines (hosts) and access them securely
- You’ll have to use inventory groups and variables correctly
- You should be able to bring in inventories from version control systems

---

### ✅ Easy Terms:
- **Inventory**: A list of systems that Ansible manages
- **Host**: A single computer in the inventory
- **Group**: A way to organize hosts (like “webservers” or “databases”)
- **Variables**: Custom values assigned to hosts or groups (like usernames, IPs)
- **Credential**: A secure way to log into a host (like SSH key or password)
- **SCM Source**: A Git repo or similar location where inventory data is stored

---

### 🛠️ UI Steps for Inventory and Credential Management:

#### 1. Create an Inventory
- Go to **Resources > Inventories**
- Click **Add** > **Inventory**
- Enter a name (like `lab-inventory`)
- Choose the organization and click **Save**

#### 2. Add Hosts to Inventory
- Open the inventory you just created
- Go to the **Hosts** tab and click **Add**
- Enter the host name (like `node1.lab.example.com`)
- Click **Save**

#### 3. Create Groups and Assign Hosts
- Go to the **Groups** tab inside your inventory
- Click **Add** to make a group (like `webservers`)
- After creating the group, click it, go to **Hosts**, and **Add** existing hosts

#### 4. Add Variables
- Go to a host or group’s **Details** tab
- Add key-value pairs like:
```yaml
ansible_user: devops
ansible_port: 22
```
- Click **Save**

#### 5. Use a Source Control Inventory
- Inside the inventory, go to the **Sources** tab
- Click **Add Source**
- Choose **Sourced from a project**
- Pick your Git project and enter the path to the inventory file (like `inventory/hosts.yml`)
- Click **Save**, then **Sync** the inventory source

#### 6. Create or Assign Credentials
- Go to **Resources > Credentials**
- Click **Add** and choose the type (e.g., Machine)
- Enter required fields like username, SSH key, etc.
- Save and assign to inventory or job template

---

### 💻 Helpful Commands (CLI):
Here’s a full list of command-line tools you can use to manage and test your inventories, both static and dynamic:

```bash
# List the inventory hosts (for testing connection)
ansible-inventory -i inventory/hosts.yml --list

# Ping all hosts
ansible all -i inventory/hosts.yml -m ping

# Run a playbook with a specific inventory file
ansible-navigator run site.yml -i inventory/hosts.yml --eei my-ee

# Visualize inventory group relationships
ansible-navigator inventory --mode stdout -i inventory/hosts.yml --graph

# Show available inventory plug-ins
ansible-navigator doc --mode stdout --type inventory --list

# View help for a specific inventory plug-in (example: Foreman)
ansible-navigator doc --mode stdout --type inventory redhat.satellite.foreman

# Run a playbook using a dynamic inventory config file
ansible-navigator run --inventory ./satellite.yml my_playbook.yml

# Make a dynamic inventory script executable
chmod 755 inventory/inventorya.py

# Run the dynamic script to see host data
./inventory/inventorya.py --list
./inventory/inventorya.py --host node1.lab.example.com
```

---

### 📦 What You Should Be Able to Do:
- Build and organize inventories in the UI
- Assign hosts, groups, and variables
- Use source-controlled inventory
- Create credentials and match them to jobs
- Test inventory connection using CLI

---

This chapter is about knowing **what systems you control** and **how to connect to them safely**. Inventories are your map, and credentials are your keys!

<br><br><br><br>
---

**Lesson 5 Quiz: Managing Inventories**

---

**1. What is an inventory in Ansible?**

a) A YAML file that defines variable precedence  
b) A file or source that lists managed hosts and groups  
c) A role dependency file  
d) A script that generates logs  
**Answer: b**

---

**2. Which of the following are valid inventory file formats in Ansible? (Select two)**

a) INI  
b) XML  
c) YAML  
d) JSON  
**Answer: a, c**

---

**3. Which command is used to preview and explore an inventory in automation content navigator?**

a) ansible-navigator show-inventory  
b) ansible-navigator list  
c) ansible-navigator inventory -i inventory.yml  
d) ansible-navigator parse-inventory  
**Answer: c**

---

**4. How can you dynamically generate an inventory at runtime?**

a) Use the ansible-doc tool  
b) Write a role in the inventory directory  
c) Use a dynamic inventory script or plugin  
d) It’s not supported  
**Answer: c**

---

**5. What are inventory variables used for?**

a) Define the playbook syntax  
b) Store Git credentials  
c) Apply configuration data to hosts and groups  
d) Format the output of playbook runs  
**Answer: c**

---

**6. What is the correct section in a YAML inventory for defining host variables?**

a) vars: under the hosts list  
b) vars: under the all group  
c) host_vars: under each host  
d) It must be placed in a separate .ini file  
**Answer: a**

---

**7. What command allows you to validate a YAML inventory file manually?**

a) ansible-navigator validate-inventory  
b) ansible-inventory --list -i inventory.yml  
c) ansible --check inventory.yml  
d) yaml-check inventory.yml  
**Answer: b**

---

**8. In a dynamic inventory plugin configuration, which file defines plugin behavior and inventory structure?**

a) dynamic_vars.yml  
b) plugin.json  
c) inventory.config  
d) inventory.yml  
**Answer: d**

---

**9. What is the effect of assigning a variable at the group level in inventory?**

a) It overrides all host variables  
b) It applies to all hosts in that group unless overridden  
c) It is ignored during playbook execution  
d) It is only used in tags  
**Answer: b**

---

**10. What tool in automation controller supports both static and dynamic inventories?**

a) Inventory Syncer  
b) Smart Inventory Plugin  
c) Inventory Management UI  
d) All of the above  
**Answer: d**

---

**11. What special variable is used to define the remote address of a host in an inventory?**

a) host_name  
b) remote_address  
c) ansible_host  
d) inventory_target  
**Answer: c**

---

**12. What inventory directory structure allows you to separate variables per group and host?**

a) vars/all.yml and vars/host.yml  
b) inventory.ini  
c) group_vars/ and host_vars/ directories  
d) Only one global vars file is supported  
**Answer: c**

---

**13. Which variable determines the user that Ansible connects as when executing tasks?**

a) ssh_user  
b) remote_user  
c) ansible_user  
d) login_user  
**Answer: c**

---

**14. What output format must a dynamic inventory script provide when run with `--list`?**

a) XML  
b) YAML  
c) INI  
d) JSON  
**Answer: d**

---

**15. What is the purpose of assigning variables in `group_vars/` or `host_vars/` folders?**

a) To override default task values globally  
b) To structure reusable configuration and improve clarity  
c) To replace ansible.cfg  
d) To define execution environments  
**Answer: b**

