**Chapter 2 Summary: Managing Content Collections and Execution Environments (Simple Version)**

---

### ✅ Objectives Checklist (EX374):
- [ ] Use automation content navigator to explore Ansible content collections
- [ ] Install and manage Ansible collections from different sources (Galaxy, Automation Hub)
- [ ] Understand and manage execution environments (EEs)
- [ ] Use `ansible-builder` to build a custom EE
- [ ] Configure and run playbooks using an execution environment

---

### 🧠 What You Learn in This Chapter:
This chapter helps you understand **collections** and **execution environments** in Ansible Automation Platform 2.

Collections are like bundles of tools (modules, roles, plugins) you can install and reuse. Execution environments (EEs) are containers that hold everything Ansible needs to run—like Python tools, collections, and settings—all in one place.

You also learn how to use `ansible-navigator` to look at collections and see what’s available.

---

### 🎯 Why It Matters for the EX374 Exam:
- You must be able to find and install collections
- You need to understand what goes into an execution environment
- You should know how to build your own EE using `ansible-builder`

---

### ✅ Easy Terms:
- **Collection**: A bundle of Ansible content you can install and reuse
- **Execution Environment (EE)**: A container that has Ansible, Python, collections, and more
- **ansible-builder**: A tool to help you create your own EE

---

### 💻 Commands You Practice:
```bash
# View installed collections
ansible-navigator collections

# Install a collection from Ansible Galaxy
ansible-galaxy collection install ansible.posix

# Run navigator to see details (interactive mode)
ansible-navigator --eei ee-supported-rhel8

# Inside the TUI, view docs or collection info
:collections
:doc yum

# View current execution environments
ansible-navigator images

# Create or customize an execution environment (using ansible-builder)
ansible-builder create
ansible-builder build -t my-custom-ee

# Login to container registry (if needed to push/pull images)
podman login <registry-url>

# Pull or push an EE image
podman pull <image-name>
podman push my-custom-ee <registry-url>
```

---

### 📦 What You Should Be Able to Do:
- Install and view Ansible collections
- Use Navigator to browse and run playbooks
- Understand what’s inside an EE and how it’s used
- Create a basic custom EE using `ansible-builder`
- Run Ansible tasks using a containerized EE

---

This chapter is about knowing where your tools come from and how to package them neatly. It's like building your own toolbox and knowing exactly what's inside!


<br><br><br><br>
---

**Lesson 2 Quiz: Managing Content Collections and Execution Environments**

---

**1. What is the primary purpose of Ansible Content Collections?**

a) Provide graphical UIs for automation control  
b) Bundle related modules, plugins, and roles for reuse and distribution  
c) Serve as logging repositories for playbook execution  
d) Act as backups for Ansible playbooks  
**Answer: b**

---

**2. Which command allows you to install a collection from a remote source?**

a) ansible-pull install  
b) ansible-collection pull  
c) ansible-galaxy collection install  
d) ansible-navigator collection install  
**Answer: c**

---

**3. What command lists installed collections when using automation content navigator?**

a) ansible-navigator collections  
b) ansible-navigator list-collections  
c) ansible-galaxy list  
d) ansible-navigator inventory --list  
**Answer: a**

---

**4. What is the benefit of using execution environments (EEs)?**

a) They replace Git for version control  
b) They offer remote desktop functionality  
c) They encapsulate all dependencies and Ansible versions into portable containers  
d) They allow for real-time playbook editing  
**Answer: c**

---

**5. Which tool is used to create custom execution environments?**

a) container-builder  
b) ansible-builder  
c) image-factory  
d) ee-creator  
**Answer: b**

---

**6. Where can execution environments be stored and retrieved for use in your automation workflows?**

a) GitHub only  
b) Tower Vault  
c) Automation Hub or Private Container Registry  
d) Satellite Server  
**Answer: c**

---

**7. What command in automation content navigator shows details about execution environments?**

a) ansible-navigator doc --ee  
b) ansible-navigator exec  
c) ansible-navigator images  
d) ansible-navigator --list-eenvs  
**Answer: c**

---

**8. What file defines which collections and Python dependencies are included in an execution environment?**

a) ansible.cfg  
b) pyproject.toml  
c) execution-environment.yml  
d) requirements.yml  
**Answer: d**

---

**9. How do tags help when working with container images for execution environments?**

a) They provide security signatures  
b) They let you schedule cron tasks  
c) They identify specific versions or variants of the image  
d) They enable automated deletion of old images  
**Answer: c**

---

**10. What podman command would authenticate you to a container registry?**

a) podman connect  
b) podman auth login  
c) podman login  
d) podman pull --auth  
**Answer: c**

