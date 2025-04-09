**Chapter 4 Quiz: Working with Ansible Configuration Settings**

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

