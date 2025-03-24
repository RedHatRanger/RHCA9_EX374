# Lab: Git Repository Management

This lab covers the "Understand and use Git" objective of the EX374 exam. You'll learn how to clone repositories, create and push files, and manage inventory files using Git.

## Prerequisites

- Ansible Automation Platform 2.4 installed according to the [AAP Installation Guide](../guides/aap_installation_guide.md)
- Basic understanding of Git concepts
- Git installed on your system

## Lab Environment Setup

1. **Install Git if not already installed**
   ```bash
   sudo dnf install -y git
   ```

2. **Configure Git identity**
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "your.email@example.com"
   ```

3. **Create a working directory**
   ```bash
   mkdir -p ~/ansible-labs/git-lab
   cd ~/ansible-labs/git-lab
   ```

## Exercise 1: Cloning a Git Repository

In this exercise, you'll clone a sample Ansible repository.

1. **Clone the repository**
   ```bash
   git clone https://github.com/ansible/ansible-examples.git
   cd ansible-examples
   ```

2. **Explore the repository structure**
   ```bash
   ls -la
   git log --oneline -n 5
   git branch -a
   ```

3. **Create a new branch for your work**
   ```bash
   git checkout -b my-ansible-work
   ```

## Exercise 2: Creating and Modifying Files in a Git Repository

1. **Create a simple playbook**
   ```bash
   cat > simple-playbook.yml << 'EOF'
   ---
   - name: Simple Playbook
     hosts: localhost
     gather_facts: false
     tasks:
       - name: Print a message
         debug:
           msg: "This is a simple playbook created in the Git lab"
   EOF
   ```

2. **Check the status of your changes**
   ```bash
   git status
   ```

3. **Stage the new file**
   ```bash
   git add simple-playbook.yml
   ```

4. **Commit the changes**
   ```bash
   git commit -m "Add simple playbook"
   ```

5. **Modify the playbook**
   ```bash
   cat >> simple-playbook.yml << 'EOF'
   
       - name: Get date and time
         command: date
         register: date_output
         
       - name: Display date and time
         debug:
           var: date_output.stdout
   EOF
   ```

6. **Stage and commit the changes**
   ```bash
   git add simple-playbook.yml
   git commit -m "Add date and time tasks to playbook"
   ```

## Exercise 3: Managing Inventory Files with Git

In this exercise, you'll create and manage inventory files using Git.

1. **Create an inventory directory structure**
   ```bash
   mkdir -p inventory/group_vars/webservers
   mkdir -p inventory/host_vars/web1
   ```

2. **Create a main inventory file**
   ```bash
   cat > inventory/hosts << 'EOF'
   [webservers]
   web1 ansible_host=192.168.1.101
   web2 ansible_host=192.168.1.102
   
   [dbservers]
   db1 ansible_host=192.168.1.201
   db2 ansible_host=192.168.1.202
   
   [all:children]
   webservers
   dbservers
   EOF
   ```

3. **Create group variables file**
   ```bash
   cat > inventory/group_vars/webservers/vars.yml << 'EOF'
   ---
   http_port: 80
   https_port: 443
   web_server: nginx
   EOF
   ```

4. **Create host variables file**
   ```bash
   cat > inventory/host_vars/web1/vars.yml << 'EOF'
   ---
   ansible_user: rhel
   ansible_ssh_private_key_file: /home/admin/.ssh/id_rsa
   http_port: 8080  # Override group variable
   EOF
   ```

5. **Stage and commit the inventory files**
   ```bash
   git add inventory/
   git commit -m "Add inventory structure with group and host vars"
   ```

## Exercise 4: Using Multiple Files per Host or Group

1. **Add additional variable files for the webservers group**
   ```bash
   cat > inventory/group_vars/webservers/nginx.yml << 'EOF'
   ---
   nginx_version: 1.18.0
   nginx_worker_processes: 4
   nginx_worker_connections: 1024
   EOF
   
   cat > inventory/group_vars/webservers/ssl.yml << 'EOF'
   ---
   ssl_enabled: true
   ssl_cert_path: /etc/nginx/ssl/cert.pem
   ssl_key_path: /etc/nginx/ssl/key.pem
   EOF
   ```

2. **Add additional variable files for the web1 host**
   ```bash
   cat > inventory/host_vars/web1/nginx.yml << 'EOF'
   ---
   nginx_worker_processes: 2  # Override group setting
   nginx_custom_config: true
   EOF
   
   cat > inventory/host_vars/web1/monitoring.yml << 'EOF'
   ---
   enable_monitoring: true
   monitoring_interval: 60
   monitoring_service: prometheus
   EOF
   ```

3. **Stage and commit the changes**
   ```bash
   git add inventory/
   git commit -m "Add multiple variable files for hosts and groups"
   ```

## Exercise 5: Configuring Remote Host Settings

1. **Create a playbook that uses the inventory**
   ```bash
   cat > configure-webservers.yml << 'EOF'
   ---
   - name: Configure Web Servers
     hosts: webservers
     gather_facts: true
     tasks:
       - name: Display host configuration
         debug:
           msg: >
             Host: {{ inventory_hostname }}
             IP: {{ ansible_host }}
             HTTP Port: {{ http_port }}
             HTTPS Port: {{ https_port }}
             Web Server: {{ web_server }}
             {% if nginx_version is defined %}
             Nginx Version: {{ nginx_version }}
             {% endif %}
   EOF
   ```

2. **Create an inventory file with custom connection settings**
   ```bash
   cat > inventory/custom_connection.ini << 'EOF'
   [webservers]
   web1 ansible_host=192.168.1.101 ansible_port=2222 ansible_user=custom_user
   web2 ansible_host=192.168.1.102
   
   [dbservers]
   db1 ansible_host=192.168.1.201
   EOF
   ```

3. **Stage and commit the changes**
   ```bash
   git add configure-webservers.yml inventory/custom_connection.ini
   git commit -m "Add webserver configuration playbook and custom connection settings"
   ```

## Exercise 6: Setting Up Directories with Multiple Host Variable Files

1. **Create a more complex directory structure for a production environment**
   ```bash
   mkdir -p inventory/production/{group_vars,host_vars}
   mkdir -p inventory/production/group_vars/{webservers,dbservers,loadbalancers}
   mkdir -p inventory/production/host_vars/{web1,web2,db1,lb1}
   ```

2. **Create the main inventory file**
   ```bash
   cat > inventory/production/hosts << 'EOF'
   [webservers]
   web1.example.com ansible_host=192.168.10.101
   web2.example.com ansible_host=192.168.10.102
   
   [dbservers]
   db1.example.com ansible_host=192.168.10.201
   
   [loadbalancers]
   lb1.example.com ansible_host=192.168.10.251
   
   [all:children]
   webservers
   dbservers
   loadbalancers
   EOF
   ```

3. **Create variable files for each group and host**
   ```bash
   # Group variables
   for group in webservers dbservers loadbalancers; do
     cat > inventory/production/group_vars/$group/main.yml << EOF
   ---
   # Main variables for $group
   group_name: $group
   EOF
   
     cat > inventory/production/group_vars/$group/users.yml << EOF
   ---
   # User variables for $group
   ansible_user: rhel
   ansible_become: true
   EOF
   done
   
   # Host variables
   for host in web1 web2 db1 lb1; do
     cat > inventory/production/host_vars/$host/main.yml << EOF
   ---
   # Main variables for $host
   host_name: $host.example.com
   EOF
   
     cat > inventory/production/host_vars/$host/network.yml << EOF
   ---
   # Network variables for $host
   ansible_host: 192.168.10.$([ "$host" == "web1" ] && echo "101" || [ "$host" == "web2" ] && echo "102" || [ "$host" == "db1" ] && echo "201" || echo "251")
   EOF
   done
   ```

4. **Stage and commit the changes**
   ```bash
   git add inventory/production/
   git commit -m "Add complex production inventory structure with multiple variable files"
   ```

## Exercise 7: Overriding Names in Inventory Files

1. **Create an inventory file with name overrides**
   ```bash
   cat > inventory/name_override.ini << 'EOF'
   [webservers]
   actual_hostname1 ansible_host=192.168.1.101 ansible_ssh_host=web1.example.com
   actual_hostname2 ansible_host=192.168.1.102 ansible_ssh_host=web2.example.com
   
   [dbservers]
   actual_db1 ansible_host=192.168.1.201 ansible_ssh_host=db1.example.com
   EOF
   ```

2. **Create a playbook that uses the inventory with name overrides**
   ```bash
   cat > name_override_demo.yml << 'EOF'
   ---
   - name: Demonstrate Name Override
     hosts: webservers
     gather_facts: false
     tasks:
       - name: Show hostname vs. inventory_hostname
         debug:
           msg: >
             Inventory Hostname: {{ inventory_hostname }}
             Ansible Host: {{ ansible_host }}
             SSH Host: {{ ansible_ssh_host }}
   EOF
   ```

3. **Stage and commit the changes**
   ```bash
   git add inventory/name_override.ini name_override_demo.yml
   git commit -m "Add inventory with name overrides and demo playbook"
   ```

## Exercise 8: Pushing Changes to a Remote Repository

In a real-world scenario, you would push your changes to a remote repository. For this lab, we'll simulate this process.

1. **Create a bare repository to simulate a remote repository**
   ```bash
   cd ~/ansible-labs/git-lab
   mkdir remote-repo.git
   cd remote-repo.git
   git init --bare
   cd ../ansible-examples
   ```

2. **Add the remote repository**
   ```bash
   git remote add origin ~/ansible-labs/git-lab/remote-repo.git
   ```

3. **Push your branch to the remote repository**
   ```bash
   git push -u origin my-ansible-work
   ```

4. **Verify the push was successful**
   ```bash
   cd ~/ansible-labs/git-lab/remote-repo.git
   git branch -a
   ```

## Conclusion

In this lab, you've learned how to:
- Clone a Git repository
- Create and modify files in a Git repository
- Manage inventory files using Git
- Use multiple files per host or group
- Configure remote host settings
- Set up directories with multiple host variable files
- Override names in inventory files
- Push changes to a remote repository

These skills are essential for the "Understand and use Git" objective of the EX374 exam.

## Additional Resources

- [Git Documentation](https://git-scm.com/doc)
- [Ansible Inventory Documentation](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)
- [Ansible Variables Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html)
