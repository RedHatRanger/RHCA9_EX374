**Lesson 3 Summary: Running Playbooks with Automation Controller (Simple Version)**

---

### ✅ Objectives Checklist (EX374):
- [ ] Understand how automation controller (formerly Ansible Tower) works
- [ ] Create and use projects, inventories, credentials, and job templates
- [ ] Launch and monitor jobs in the web UI
- [ ] Use automation controller with execution environments (EEs)
- [ ] Understand automation mesh and how it connects different nodes

---

### 🧠 What You Learn in This Chapter:
This chapter teaches you how to use the **automation controller**, which is the web-based version of Ansible. It lets you run playbooks, store your inventory, and schedule jobs—all in one place.

You learn about the controller’s building blocks:
- **Projects** (where your playbooks come from)
- **Inventories** (what systems you manage)
- **Credentials** (secure login info)
- **Job Templates** (how everything connects to run a playbook)

You also see how it works with execution environments and automation mesh (a system for spreading out the work).

---

### 🎯 Why It Matters for the EX374 Exam:
- You’ll need to create and manage controller resources
- You must know how to run and monitor playbook jobs in the UI
- You should understand how controller connects to your EEs and systems

---

### ✅ Easy Terms:
- **Automation Controller**: A web app that helps run and manage playbooks
- **Project**: A source for your playbooks (like a Git repo)
- **Inventory**: The list of systems you manage
- **Job Template**: The settings used to run a playbook
- **Automation Mesh**: A way to run jobs across multiple machines

---

### 🛠️ Setup Instructions for AAP 2.4 (Web UI Style):

Below are the step-by-step instructions to complete your setup using the **Automation Controller web interface**:

#### 1. Log in to the Automation Controller
- Open your web browser and go to `https://<controller-hostname>`
- Log in with your `admin` credentials

#### 2. Create a Source Control Credential
- Go to **Resources > Credentials**
- Click **Add**
- **Name**: reviewgitcred
- **Credential Type**: Source Control
- **Username**: student
- **SSH Private Key**: paste `/home/student/.ssh/gitlab_rsa`
- Click **Save**

#### 3. Create a Machine Credential
- Go to **Resources > Credentials**
- Click **Add**
- **Name**: reviewmachinecred
- **Credential Type**: Machine
- **Username**: devops
- **SSH Private Key**: paste `/home/student/.ssh/lab_rsa`
- **Privilege Escalation Method**: sudo
- Click **Save**

#### 4. Create an Inventory and Add a Host
- Go to **Resources > Inventories**
- Click **Add** > **Inventory**
- **Name**: reviewinventory
- Click **Save**
- Click the new inventory and go to **Hosts**
- Click **Add**
- **Host Name**: node1.lab.example.com
- Click **Save**

#### 5. Create a Project
- Go to **Resources > Projects**
- Click **Add**
- **Name**: reviewproject
- **SCM Type**: Git
- **SCM URL**: `git@git.lab.example.com:student/controller-review.git`
- **Credential**: reviewgitcred
- Click **Save**

#### 6. Create a Job Template
- Go to **Resources > Templates**
- Click **Add** > **Job Template**
- **Name**: reviewtemplate
- **Inventory**: reviewinventory
- **Project**: reviewproject
- **Playbook**: webserver.yml
- **Credential**: reviewmachinecred
- **Execution Environment**: Default EE
- Click **Save**

#### 7. Launch the Job
- Go to **Templates**
- Click the **rocket/launch icon** next to `reviewtemplate`

#### 8. Verify the Result
- Run this command from any terminal:
```bash
curl http://node1.lab.example.com
```

---

### 📦 What You Should Be Able to Do:
- Set up the core parts of automation controller
- Run a playbook job through the UI
- Use controller with execution environments
- Understand what automation mesh is and when it’s used

---

This chapter is about using the **control center for Ansible**—the automation controller. You’re not just running playbooks anymore—you’re managing them like a pro!


<br><br><br><br>
---

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

