**lESSON 7 Summary: Building Reusable Playbooks with Roles (Simple Version)**

---

### ✅ Objectives Checklist (EX374):
- [ ] Understand the structure of a role and its purpose
- [ ] Use the `ansible-galaxy` command to create and manage roles
- [ ] Reuse role content in playbooks and templates
- [ ] Apply variables, handlers, and templates inside a role
- [ ] Set role dependencies and defaults

---

### 🧠 What You Learn in This Chapter:
This chapter shows you how to **organize your playbooks into roles** so that they are cleaner, easier to reuse, and more manageable. Roles help break big projects into smaller, reusable chunks.

You learn how to build your own roles, use roles from collections, and make your roles flexible by adding variables, templates, and handlers. You also learn about role dependencies and default values.

---

### 🎯 Why It Matters for the EX374 Exam:
- Roles are one of the most important ways to reuse automation code
- The exam may ask you to build, apply, or troubleshoot roles
- Understanding role structure helps you read and write organized playbooks

---

### ✅ Easy Terms:
- **Filter**: A tool used in Jinja2 to change or format data in a variable (like turning 'yes' into 'YES', or filtering a list)
- **Role**: A folder structure that holds reusable tasks, variables, templates, and more
- **Handler**: A task that runs only when notified (often used to restart a service)
- **Template**: A dynamic configuration file that uses variables
- **Defaults**: Variables that can be easily overridden by other variables
- **Dependencies**: Other roles that this role needs to work

---

### 📘 Example Use of a Filter:
Here’s how a filter might be used in a template:

```jinja
# inside templates/index.html.j2
Welcome, {{ username | upper }}!
```

This turns the value of `username` into all uppercase letters.

---

### 🛠️ Role Creation Steps:

#### 1. Create a Role
```bash
ansible-galaxy init webrole
```

#### 2. Add Tasks to the Role
- Open `webrole/tasks/main.yml`
- Add tasks like installing packages, configuring files, or starting services

#### 3. Add Variables
- Use `defaults/main.yml` for variables users can override
- Use `vars/main.yml` for fixed variables that are harder to override

#### 4. Add Templates or Handlers
- Add Jinja2 templates to `templates/`
- Add restart/reload handlers to `handlers/main.yml`

#### 5. Use the Role in a Playbook
```yaml
- name: Apply webrole
  hosts: node1.lab.example.com
  roles:
    - webrole
```

#### 6. Define Role Dependencies (Optional)
- In `meta/main.yml`, add:
```yaml
dependencies:
  - role: firewall
  - role: monitoring
```

---

### 💻 Helpful Commands:
```bash
# Create a new role scaffold
ansible-galaxy init <role-name>

# Install a role from Ansible Galaxy
ansible-galaxy install <namespace.role>

# List installed roles
ansible-galaxy list
```

---

### 📦 What You Should Be Able to Do:
- Organize tasks into a clean role structure
- Use roles inside playbooks
- Create templates and handlers for flexibility
- Share and reuse roles across projects

---

This chapter is about writing **modular Ansible code**. Roles make your automation easier to build, share, and manage!

<br><br><br><br>
---

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
