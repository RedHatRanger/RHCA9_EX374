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

