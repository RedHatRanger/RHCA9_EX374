**Lesson 1 Summary: Developing Playbooks with Ansible Automation Platform 2**

---

### 📘 Chapter Overview:
Chapter 1 introduces the foundational changes in Ansible Automation Platform (AAP) 2 and the tooling used to manage, develop, and execute automation content. The primary focus is on the shift from traditional Ansible CLI tools to the use of **Automation Content Navigator** and **Execution Environments (EEs)**. The chapter helps learners get familiar with:

- How playbooks are developed and run using `ansible-navigator`
- The purpose and structure of Execution Environments
- The concept of collections in Ansible
- Viewing documentation and configuration using the new tools

This chapter is about **getting hands-on quickly** with AAP 2’s CLI and containerized toolchain.

---

### 🧠 Key Concepts:
- `ansible-navigator` replaces multiple tools like `ansible-playbook`, `ansible-doc`, and `ansible-config`
- Execution Environments (EEs) are container images that package Ansible, Python dependencies, and collections
- Content Collections are modular bundles of roles, modules, and plugins
- Automation Hub and Private Automation Hub are used to host and curate collections
- `ansible-navigator` can be run in interactive (TUI) mode or stdout mode

---

### 💻 Commands Used in Chapter 1:

```bash
# Create a working directory
mkdir -p ~/ansible-aap/ch1 && cd ~/ansible-aap/ch1

# Sample playbook
cat <<EOF > web_setup.yml
---
- name: Install and start Apache
  hosts: localhost
  become: true
  tasks:
    - name: Install httpd
      ansible.builtin.yum:
        name: httpd
        state: present
    - name: Start and enable httpd
      ansible.builtin.service:
        name: httpd
        state: started
        enabled: true
EOF

# Run playbook using ansible-navigator in stdout mode
ansible-navigator run web_setup.yml -m stdout --eei ee-supported-rhel8

# Run ansible-navigator in TUI mode (interactive)
ansible-navigator --eei ee-supported-rhel8

# Inside TUI: Run the playbook interactively
:run web_setup.yml

# View documentation for a module (e.g., yum)
ansible-navigator doc yum

# View current Ansible config
ansible-navigator config

# View settings specific to automation content navigator
ansible-navigator settings

# List installed collections
ansible-navigator collections
```

---

### ✅ Outcomes by the End of Chapter:
- You should be able to create and run a playbook using `ansible-navigator`
- You understand how execution environments isolate your automation logic
- You know how to explore configuration, documentation, and collections with navigator
- You’re familiar with Red Hat’s ecosystem for managing and curating Ansible content

---

This chapter lays the groundwork for using the platform’s new tooling and helps you shift your mindset toward **containerized and modular automation workflows.**

---

**Lesson 1 Quiz: Red Hat Ansible Automation Platform 2**

---

**1. Which component replaces commands such as ansible-playbook and ansible-doc?**

a) Automation hub  
b) Ansible Core  
c) Automation content navigator  
d) Ansible Playbook editor  
**Answer: c**

---

**2. Which service provides automation content officially supported by Red Hat?**

a) Automation hub  
b) Red Hat Enterprise Ansible Cloud  
c) Ansible Galaxy  
d) Ansible content platform  
**Answer: a**

---

**3. Automation content navigator separates which two functions to make it a complete working environment?**

a) Network management and host management  
b) Content plane and configuration plane  
c) Management network and production network  
d) Control node and automation execution environment  
**Answer: d**

---

**4. Automation content can be hosted on-premise using which component?**

a) Private automation hub  
b) Red Hat Insights for Red Hat Ansible Automation Platform  
c) Automation Hub Repository  
d) Red Hat Ansible cache  
**Answer: a**

---

**5. Which Ansible Content Collection is always included with ansible-core?**

a) community.general  
b) ansible.builtin  
c) ansible.utils  
d) ansible.posix  
e) kubernetes.core  
f) ansible.netcommon  
**Answer: b**

---

**6. What command would you use to view the Ansible configuration using automation content navigator?**

a) ansible-config show  
b) ansible-navigator config  
c) ansible-navigator settings  
d) ansible settings view  
**Answer: b**

---

**7. What does the command `ansible-navigator run playbook.yml -m stdout` do?**

a) Runs the playbook and opens a browser GUI  
b) Runs the playbook and saves logs to disk  
c) Runs the playbook in text output mode similar to ansible-playbook  
d) Runs the playbook but skips inventory processing  
**Answer: c**

---

**8. Which of the following is NOT a subcommand provided by automation content navigator?**

a) run  
b) doc  
c) lint  
d) init  
**Answer: d**

---

**9. What file format is typically used for Ansible playbooks?**

a) JSON  
b) XML  
c) YAML  
d) INI  
**Answer: c**

---

**10. What is the purpose of an execution environment in AAP 2.2?**

a) To create automation job templates  
b) To package and distribute Ansible, collections, and dependencies  
c) To log job execution results  
d) To replace Ansible Vault  
**Answer: b**

---

**11. What benefit does automation content navigator provide over ansible-playbook?**

a) Graphical interface for automation  
b) Ability to build roles automatically  
c) Interactive TUI for exploring results and replaying jobs  
d) Allows direct editing of YAML files  
**Answer: c**

---

**12. How do you view the results of a previous Ansible run in navigator?**

a) :history  
b) :log  
c) :doc previous  
d) :replay  
**Answer: d**

---

**13. If you’re prompted to log into registry.redhat.io, what is likely happening?**

a) Pulling a container image for an execution environment  
b) Viewing public Red Hat documentation  
c) Logging into Ansible Controller  
d) Accessing source code from Git  
**Answer: a**

---

**14. What is the default execution environment image used in AAP 2.2 if none is specified?**

a) ansible-runner  
b) rhel-ansible-core  
c) ee-supported-rhel8:latest  
d) aap2-base  
**Answer: c**

---

**15. What role does ssh-agent play when using ansible-navigator?**

a) It encrypts Ansible playbooks  
b) It stores private keys for authentication into managed nodes  
c) It syncs playbooks between Git and controller  
d) It logs user access  
**Answer: b**

