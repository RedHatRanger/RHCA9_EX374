**Lesson 4 Summary: Managing Automation Controller Configuration (Simple Version)**

---

### ✅ Objectives Checklist (EX374):
- [ ] Change settings in the automation controller UI
- [ ] Control user access using teams, users, and permissions
- [ ] Use organizations to group resources
- [ ] Create and manage custom credential types
- [ ] Understand RBAC (role-based access control)

---

### 🧠 What You Learn in This Chapter:
In this chapter, you learn how to **set up and customize automation controller settings**. This includes how to control who can do what, how to organize your users, and how to create new types of credentials.

You also learn about the **RBAC (Role-Based Access Control)** system, which helps you keep automation safe and secure.

---

### 🎯 Why It Matters for the EX374 Exam:
- You must know how to adjust controller settings
- You’ll need to manage access using users, teams, and roles
- You'll create custom credentials and assign them safely

---

### ✅ Easy Terms:
- **Organization**: A way to group users, inventories, projects, etc.
- **Team**: A group of users that share the same permissions
- **User**: A person who logs into the controller
- **RBAC**: A method to control who can do what (like view, edit, or run)
- **Credential**: Secure information used to connect to machines or services, like SSH keys, usernames/passwords, or tokens.
- **Credential Type**: A blueprint that defines what kind of credentials can be stored and how to use them. This allows for creating custom connection methods.

---

### 🛠️ Common UI Steps (Including Credential Management):

#### 1. Adjust Controller Settings
- Go to **Settings** (gear icon)
- Change controller-wide options like timeouts, logging, and authentication

#### 2. Create a New Organization
- Go to **Access > Organizations**
- Click **Add** > **Organization**
- Name it something like `devops` or `security`

#### 3. Add a User and Assign to an Organization
- Go to **Access > Users**
- Click **Add**
- Enter username, password, email
- Assign the user to the organization

#### 4. Create a Team
- Go to **Access > Teams**
- Click **Add** > **Team**
- Choose the organization and name the team
- Add users to the team

#### 5. Grant Permissions (RBAC)
- Go to the object (Inventory, Project, Template, etc.)
- Click the **Access** tab
- Click **Add** to assign users or teams
- Choose a role like **Admin**, **Execute**, or **Read Only**

#### 6. Create a Custom Credential Type
- Go to **Resources > Credential Types**
- Click **Add**
- Give it a **Name**, like `Vault Token`
- In the **Input Configuration**, define what info you need (e.g., `vault_token`, `vault_url`)
- In the **Injector Configuration**, define how Ansible should use this info when running jobs (e.g., as environment variables)
- Save and test it by creating a credential that uses this new type

#### 7. Add a New Credential
- Go to **Resources > Credentials**
- Click **Add**
- Choose the credential **Type** (like Machine, Vault, SCM, or your custom one)
- Fill in required fields (username, password, SSH key, etc.)
- Assign the credential to the appropriate **organization**
- Click **Save**

---

### 📦 What You Should Be Able to Do:
- Change controller settings
- Use RBAC to give users the right access
- Organize users with teams and orgs
- Create new credential types for different use cases

---

This chapter is about **locking down your automation system** and organizing your team. Think of it like giving each person the right key to the right tool!

<br><br><br><br>
---

**Lesson 4 Quiz: Working with Ansible Configuration Settings**

---

**1. What is the purpose of the ansible.cfg file in an Ansible project?**

a) It contains playbook YAML syntax rules  
b) It defines configuration options for Ansible behavior  
c) It stores credentials and vault passwords  
d) It holds logs for playbook runs  
**Answer: b**

---

**2. What command can you use in automation content navigator to view the current Ansible configuration?**

a) ansible-navigator settings  
b) ansible-navigator show-config  
c) ansible-navigator config  
d) ansible-navigator config-show  
**Answer: c**

---

**3. What is the order of precedence for Ansible configuration files (from highest to lowest)?**

a) Environment variables > ansible.cfg (in project dir) > global config  
b) ansible.cfg (in project dir) > Environment variables > global config  
c) Global config > Environment variables > ansible.cfg  
d) Environment variables > global config > ansible.cfg  
**Answer: a**

---

**4. Which section of the ansible.cfg file is used to specify the default inventory location?**

a) [inventory]  
b) [defaults]  
c) [inventory_path]  
d) [files]  
**Answer: b**

---

**5. What happens when conflicting Ansible configuration settings are defined in multiple places?**

a) All settings are merged automatically  
b) The system throws an error  
c) The highest precedence setting takes effect  
d) Only the default global configuration is used  
**Answer: c**

---

**6. What is a best practice when working with multiple Ansible projects regarding configuration?**

a) Use a single global ansible.cfg file for all projects  
b) Always use environment variables only  
c) Create a project-specific ansible.cfg file to isolate settings  
d) Avoid using any ansible.cfg files to prevent conflict  
**Answer: c**

---

**7. What is the function of the ansible-navigator settings view?**

a) To update Ansible collections  
b) To configure Navigator plugins  
c) To display automation content navigator-specific configuration  
d) To show remote system facts  
**Answer: c**

---

**8. How can you temporarily override a configuration setting while running a playbook?**

a) By editing ~/.ansible.cfg  
b) By updating the project ansible.cfg file before running  
c) By setting an environment variable before the command  
d) You cannot override it at runtime  
**Answer: c**

---

**9. What does ansible-navigator use by default to execute playbooks?**

a) The global Python interpreter  
b) Virtual environments from the control node  
c) Execution environments (container images)  
d) System-wide installed modules  
**Answer: c**

---

**10. When examining the Ansible configuration with automation content navigator, which command option ensures output is displayed in plain text format?**

a) -o  
b) -p  
c) --mode stdout  
d) --raw  
**Answer: c**

