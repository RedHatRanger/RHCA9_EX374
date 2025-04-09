**Chapter 1 Summary: Developing Playbooks with Ansible Automation Platform 2 (Simple Version)**

---

### ✅ Objectives Checklist (EX374):
- [ ] Use Git to clone, edit, and push changes to a repository
- [ ] Identify and run playbooks stored in a Git repository
- [ ] Use `ansible-navigator` to run automation tasks and playbooks
- [ ] Use `become` to run tasks with admin privileges
- [ ] Understand what execution environments are and how to use them
- [ ] Use `ansible-navigator` to explore Ansible content collections
- [ ] Find help for Ansible modules and configuration settings using navigator

---

### 🧠 What You Learn in This Chapter:
This chapter teaches you how to use **Ansible Navigator** instead of old Ansible tools. It also shows you how to use something called an **Execution Environment**, which is like a box that holds all your Ansible tools.

You’ll also learn what **collections** are (they are bundles of Ansible stuff like tasks and roles), and how to look at your Ansible setup using commands.

---

### 🎯 Why It Matters for the EX374 Exam:
This chapter helps with these test skills:
- **Using Git** (cloning, editing files, pushing changes)
- **Running tasks with Ansible** (doing tasks with or without admin power)

---

### ✅ Easy Terms:
- **Ansible Navigator**: A new tool that helps you run Ansible in a nice and simple way
- **Execution Environment**: A container (like a toolkit in a backpack) that has everything Ansible needs
- **Collection**: A bundle of tools that work with Ansible

---

### 💻 Commands You Practice:
```bash
# Make a new folder and go there
mkdir -p ~/ansible-aap/ch1 && cd ~/ansible-aap/ch1

# Make a simple playbook file
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

# Run the playbook in plain text mode
ansible-navigator run web_setup.yml -m stdout --eei ee-supported-rhel8

# Run in interactive mode
ansible-navigator --eei ee-supported-rhel8

# Then in the menu, type this to run the playbook
:run web_setup.yml

# See help for a module
ansible-navigator doc yum

# Look at Ansible’s settings
ansible-navigator config

# Look at Navigator’s own settings
ansible-navigator settings

# See which collections are installed
ansible-navigator collections
```

---

### 📦 What You Should Be Able to Do:
- Make and run a simple playbook with Navigator
- Use the built-in tools in the Execution Environment
- Look up help and settings using Navigator
- Know where your Ansible stuff (collections) lives

---

This chapter is like your **first lesson in using a new toolbox**. You’ll use these tools in every other chapter!

<br><br><br>

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

