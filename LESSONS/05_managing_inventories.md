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

