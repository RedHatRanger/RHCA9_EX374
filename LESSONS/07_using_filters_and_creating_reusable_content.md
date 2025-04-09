**Lesson 7 Quiz: Creating Reusable Content with Roles**

---

**1. What is the purpose of using roles in Ansible?**

a) To store only variables for a playbook  
b) To automate the creation of inventory files  
c) To organize playbook content into reusable, structured components  
d) To track execution time  
**Answer: c**

---

**2. Which command initializes the file structure of a new role?**

a) ansible-role create  
b) ansible-playbook init-role  
c) ansible-galaxy init <role_name>  
d) ansible-navigator role new  
**Answer: c**

---

**3. What is the correct directory structure for a role’s default variables?**

a) roles/rolename/vars/defaults.yml  
b) roles/rolename/defaults/main.yml  
c) roles/rolename/group_vars/main.yml  
d) roles/rolename/vars.yml  
**Answer: b**

---

**4. Where should task definitions be placed within a role directory?**

a) tasks.yml  
b) tasks/init.yml  
c) tasks/main.yml  
d) role_tasks.yml  
**Answer: c**

---

**5. What is the purpose of the `meta/main.yml` file in a role?**

a) Defines host-specific parameters  
b) Declares role dependencies and metadata  
c) Lists playbook pre-tasks  
d) Controls file permissions  
**Answer: b**

---

**6. Which of the following are valid subdirectories inside an Ansible role? (Select two)**

a) plugins/  
b) handlers/  
c) files/  
d) defaults.d/  
**Answer: b, c**

---

**7. How do you include a role in a playbook?**

a) - import_role: name=rolename  
b) - roles: rolename  
c) - role: rolename  
d) - include_role: name=rolename  
**Answer: c**

---

**8. What happens if a required file like `tasks/main.yml` is missing in a role?**

a) The role silently skips execution  
b) The playbook fails to run with an error  
c) Ansible uses a default task  
d) Only a warning is shown  
**Answer: b**

---

**9. What is a common use for the `files/` directory in a role?**

a) To store playbooks  
b) To hold static files for copy module  
c) To log host execution results  
d) To cache inventory sources  
**Answer: b**

---

**10. When should you define role dependencies in `meta/main.yml`?**

a) When you need to import variables from the controller  
b) When your role relies on other roles being executed first  
c) When your playbook uses dynamic inventory  
d) When overriding role defaults  
**Answer: b**

---

**11. What is the difference between `defaults/main.yml` and `vars/main.yml` in a role?**

a) Defaults are encrypted, vars are not  
b) Defaults have lower precedence than vars  
c) Vars are ignored during runtime  
d) Vars must match tags, defaults do not  
**Answer: b**

---

**12. What is the purpose of the `handlers/main.yml` file in a role?**

a) To define tags for the playbook  
b) To collect logs for reporting  
c) To define tasks triggered by notify statements  
d) To store configuration for looped tasks  
**Answer: c**

---

**13. What is a recommended best practice when naming role variables?**

a) Use short, generic names  
b) Use camelCase for readability  
c) Prefix variables with the role name  
d) Avoid underscores  
**Answer: c**

---

**14. Where should roles be placed in a typical Git project layout?**

a) In /etc/ansible/roles only  
b) Inside collections/roles/ directory  
c) In a `roles/` directory at the root of the project  
d) In the inventory directory  
**Answer: c**

---

**15. How do you download roles defined in a project’s role requirements file?**

a) ansible-pull roles.yml  
b) ansible-navigator sync-roles  
c) ansible-galaxy install -r roles/requirements.yml  
d) galaxy-download requirements.yml  
**Answer: c**

---

**16. When would you use `include_role` instead of `roles:` in a playbook?**

a) To load the role conditionally or dynamically  
b) When calling the role from a dynamic inventory  
c) To execute roles on localhost only  
d) For initializing roles with Galaxy  
**Answer: a**
