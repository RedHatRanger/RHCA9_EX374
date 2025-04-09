**Lesson 9 Quiz: Using Variables and Facts Effectively**

---

**1. What is the primary purpose of variables in Ansible?**

a) To create dynamic inventories  
b) To manage playbook output formatting  
c) To define flexible and reusable automation configurations  
d) To store log files for remote execution  
**Answer: c**

---

**2. What is the difference between a variable and a fact in Ansible?**

a) Variables are user-defined; facts are discovered automatically  
b) Variables are stored in YAML; facts are in JSON only  
c) Variables require an API call; facts require a role  
d) There is no difference  
**Answer: a**

---

**3. Which module is commonly used to collect system facts?**

a) setup  
b) fact_gather  
c) gather_facts  
d) ansible_facts  
**Answer: a**

---

**4. How do you disable automatic fact gathering in a playbook?**

a) gather_facts: no  
b) setup: disabled  
c) gather_facts: false  
d) skip_facts: true  
**Answer: a**

---

**5. What is the default location where facts are stored in a playbook’s variable space?**

a) hostvars  
b) vars  
c) facts:  
d) ansible_facts  
**Answer: d**

---

**6. Which directory structure is used to define host-specific variables?**

a) inventory/vars/host.yml  
b) host_vars/<hostname>/main.yml  
c) hosts.d/hostname.yml  
d) facts/hostname.yml  
**Answer: b**

---

**7. Which of the following methods allows the use of variables from a file?**

a) include_vars  
b) set_fact  
c) import_vars  
d) load_yaml  
**Answer: a**

---

**8. What is the primary use of `set_fact`?**

a) To load variables from host_vars  
b) To gather facts from the control node  
c) To define variables during playbook runtime  
d) To override environment variables  
**Answer: c**

---

**9. How are registered variables created in Ansible?**

a) By using the var module  
b) By assigning values in `set_fact` only  
c) By using the register keyword to capture module output  
d) By importing environment files  
**Answer: c**

---

**10. Which Jinja2 expression is used to access the `ansible_facts` dictionary for the system architecture?**

a) {{ facts.arch }}  
b) {{ ansible_facts['architecture'] }}  
c) {{ hostvars.arch }}  
d) {{ setup.arch }}  
**Answer: b**

---

**11. What does setting `gathering = smart` in ansible.cfg enable?**

a) Disables all fact collection  
b) Forces every play to re-gather facts  
c) Enables caching and avoids duplicate fact gathering  
d) Prevents facts from being stored in memory  
**Answer: c**

---

**12. How do you collect only a specific subset of system facts with the setup module?**

a) setup: subset=basic  
b) setup: gather_subset=all  
c) setup: gather_subset=network  
d) gather_facts: filter=default  
**Answer: c**

---

**13. Which type of variable has the highest precedence in Ansible?**

a) Inventory variable  
b) Role default variable  
c) Variable set with `set_fact`  
d) Extra variable passed with `--extra-vars`  
**Answer: d**

---

**14. What is the best practice for organizing variables by role or category in a project?**

a) Use vars.yml only  
b) Use a large host_vars file for all hosts  
c) Use structured group_vars/ and host_vars/ directories  
d) Define everything inside the playbook  
**Answer: c**

