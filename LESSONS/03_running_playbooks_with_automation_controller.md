**Lesson 3 Quiz: Running Playbooks with Automation Controller**

---

**1. What is the role of automation controller in Red Hat Ansible Automation Platform?**

a) Manages source control systems  
b) Provides a centralized interface to configure and run automation jobs  
c) Builds and compiles container images  
d) Acts as an IDE for Ansible playbooks  
**Answer: b**

---

**2. What is the new name for the previously known Red Hat Ansible Tower?**

a) Automation Orchestrator  
b) Control Hub  
c) Automation controller  
d) Ansible Central  
**Answer: c**

---

**3. What interface does automation controller provide for interacting with automation jobs?**

a) CLI only  
b) REST API and Web UI  
c) Python GUI  
d) JSON-RPC  
**Answer: b**

---

**4. What major benefit does automation controller provide in a large enterprise environment?**

a) Faster YAML rendering  
b) Individual user shell access  
c) Centralized automation management with access control and scheduling  
d) Auto-conversion of shell scripts to Ansible  
**Answer: c**

---

**5. What feature in automation controller allows it to run EEs on remote nodes?**

a) Distributed Nodes  
b) Ansible Forking  
c) Automation Mesh  
d) Execution Balancer  
**Answer: c**

---

**6. What is a Job Template in automation controller?**

a) A copy of a playbook stored locally  
b) A runtime option that overrides config files  
c) A definition of what playbook to run, with what inventory and parameters  
d) A log of previous job runs  
**Answer: c**

---

**7. In automation controller, what is a Project?**

a) A group of automation users  
b) A folder in the control node's filesystem  
c) A link to a source control repository containing Ansible content  
d) A dashboard for monitoring execution environments  
**Answer: c**

---

**8. Which of the following is a requirement for launching a job in automation controller?**

a) Inventory and credential only  
b) Playbook, credential, and execution environment  
c) Inventory, project, playbook, and credential  
d) Just the playbook file  
**Answer: c**

---

**9. What is the purpose of a Credential in automation controller?**

a) It stores passwords for GitLab login only  
b) It defines what container runtime to use  
c) It provides secure access to systems, source control, or vaults  
d) It identifies YAML syntax errors  
**Answer: c**

---

**10. What kind of inventory can automation controller manage?**

a) Only static INI-style inventory files  
b) Dynamic inventories from external sources and static inventories  
c) Only inventories pulled from GitHub  
d) Manually entered IP addresses only  
**Answer: b**

---

**11. What type of logging and reporting is built into automation controller?**

a) Minimal logs in /var/log/messages only  
b) Local system log only  
c) Real-time job output, system audit logs, and integrated logging with Insights  
d) Git log integrations only  
**Answer: c**

---

**12. Which automation controller feature allows for delegating permissions based on roles?**

a) RBAC (Role-Based Access Control)  
b) SELinux  
c) Project Scoping  
d) Access Delegation API  
**Answer: a**

---

**13. What visual element in the web UI helps define job workflows with dependencies?**

a) Dashboard reports  
b) Workflow visualizer  
c) Inventory tree view  
d) Playbook trace  
**Answer: b**

---

**14. During the guided exercise, what are the essential steps to running a job in automation controller?**

a) Create project, create inventory, add user, configure podman  
b) Create project, set job timeout, pull from registry, add user roles  
c) Create project, define inventory, add credentials, create job template  
d) Only define playbook and run job  
**Answer: c**

---

**15. What benefit does a Source Control-managed Project provide in automation controller?**

a) Easier installation of roles  
b) Automatic syncing and version control of automation content  
c) Isolates job runs from each other  
d) Improves YAML parsing speed  
**Answer: b**

