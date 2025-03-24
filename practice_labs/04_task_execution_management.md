# Lab: Task Execution Management

This lab covers the "Use conditionals to control play execution" and "Configure error handling" objectives of the EX374 exam. You'll learn how to use conditionals, loops, and error handling techniques in Ansible playbooks.

## Prerequisites

- Ansible Automation Platform 2.4 installed according to the [AAP Installation Guide](../guides/aap_installation_guide.md)
- Completion of the [Git Repository Management Lab](01_git_repository_management.md) and [Ansible Playbook Development Lab](02_ansible_playbook_development.md)

## Lab Environment Setup

1. **Create a working directory**
   ```bash
   mkdir -p ~/ansible-labs/task-execution-lab
   cd ~/ansible-labs/task-execution-lab
   ```

2. **Initialize a Git repository**
   ```bash
   git init
   ```

3. **Create a basic inventory file**
   ```bash
   cat > inventory << 'EOF'
   [webservers]
   web1 ansible_host=192.168.1.101
   web2 ansible_host=192.168.1.102
   
   [dbservers]
   db1 ansible_host=192.168.1.201
   db2 ansible_host=192.168.1.202
   
   [all:vars]
   ansible_connection=local
   EOF
   ```

## Exercise 1: Using Conditionals in Playbooks

In this exercise, you'll learn how to use conditionals to control task execution.

1. **Create a playbook with basic conditionals**
   ```bash
   cat > conditional_basics.yml << 'EOF'
   ---
   - name: Demonstrate Basic Conditionals
     hosts: all
     gather_facts: true
     vars:
       install_nginx: true
       install_apache: false
       environment: production
       min_memory_mb: 2048
     
     tasks:
       - name: Print operating system
         debug:
           msg: "Running on {{ ansible_distribution }} {{ ansible_distribution_version }}"
       
       - name: Install NGINX
         debug:
           msg: "Installing NGINX on {{ inventory_hostname }}"
         when: install_nginx | bool
       
       - name: Install Apache
         debug:
           msg: "Installing Apache on {{ inventory_hostname }}"
         when: install_apache | bool
       
       - name: Check if system has enough memory
         debug:
           msg: "System has enough memory ({{ ansible_memtotal_mb }}MB)"
         when: ansible_memtotal_mb >= min_memory_mb
       
       - name: Warning for low memory
         debug:
           msg: "WARNING: System has low memory ({{ ansible_memtotal_mb }}MB)"
         when: ansible_memtotal_mb < min_memory_mb
       
       - name: Configure for production
         debug:
           msg: "Applying production configuration to {{ inventory_hostname }}"
         when: environment == "production"
       
       - name: Configure for development
         debug:
           msg: "Applying development configuration to {{ inventory_hostname }}"
         when: environment == "development"
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook -i inventory conditional_basics.yml
   ```

3. **Create a playbook with multiple conditions**
   ```bash
   cat > multiple_conditions.yml << 'EOF'
   ---
   - name: Demonstrate Multiple Conditions
     hosts: all
     gather_facts: true
     vars:
       enable_feature_x: true
       enable_feature_y: false
       min_disk_gb: 50
       required_packages:
         - python3
         - curl
         - wget
     
     tasks:
       - name: Check OS and version
         debug:
           msg: "This is a supported OS: {{ ansible_distribution }} {{ ansible_distribution_version }}"
         when: >
           (ansible_distribution == "RedHat" and ansible_distribution_major_version | int >= 8) or
           (ansible_distribution == "Debian" and ansible_distribution_major_version | int >= 10) or
           (ansible_distribution == "Ubuntu" and ansible_distribution_major_version | int >= 20)
       
       - name: Check features and disk space
         debug:
           msg: "Features X and Y are enabled and disk space is sufficient"
         when:
           - enable_feature_x | bool
           - enable_feature_y | bool
           - ansible_facts['mounts'][0]['size_total'] | int > min_disk_gb * 1024 * 1024 * 1024
       
       - name: Check if all required packages are installed
         debug:
           msg: "All required packages are available"
         when: required_packages | intersect(ansible_facts.packages.keys()) | length == required_packages | length
   EOF
   ```

4. **Run the playbook**
   ```bash
   ansible-playbook -i inventory multiple_conditions.yml
   ```

5. **Create a playbook with conditional imports and includes**
   ```bash
   mkdir -p tasks
   
   cat > tasks/webserver_tasks.yml << 'EOF'
   ---
   - name: Configure web server
     debug:
       msg: "Configuring web server on {{ inventory_hostname }}"
   
   - name: Set up virtual hosts
     debug:
       msg: "Setting up virtual hosts on {{ inventory_hostname }}"
   EOF
   
   cat > tasks/dbserver_tasks.yml << 'EOF'
   ---
   - name: Configure database server
     debug:
       msg: "Configuring database server on {{ inventory_hostname }}"
   
   - name: Set up database users
     debug:
       msg: "Setting up database users on {{ inventory_hostname }}"
   EOF
   
   cat > conditional_imports.yml << 'EOF'
   ---
   - name: Demonstrate Conditional Imports
     hosts: all
     gather_facts: true
     
     tasks:
       - name: Include web server tasks
         include_tasks: tasks/webserver_tasks.yml
         when: inventory_hostname in groups['webservers']
       
       - name: Include database server tasks
         include_tasks: tasks/dbserver_tasks.yml
         when: inventory_hostname in groups['dbservers']
       
       - name: Import tasks based on OS family
         import_tasks: "tasks/{{ ansible_os_family | lower }}_tasks.yml"
         when: ansible_os_family in ['RedHat', 'Debian']
         ignore_errors: yes
   EOF
   
   # Create OS-specific task files
   cat > tasks/redhat_tasks.yml << 'EOF'
   ---
   - name: RedHat specific tasks
     debug:
       msg: "Performing RedHat specific tasks on {{ inventory_hostname }}"
   EOF
   
   cat > tasks/debian_tasks.yml << 'EOF'
   ---
   - name: Debian specific tasks
     debug:
       msg: "Performing Debian specific tasks on {{ inventory_hostname }}"
   EOF
   ```

6. **Run the playbook**
   ```bash
   ansible-playbook -i inventory conditional_imports.yml
   ```

7. **Commit your changes**
   ```bash
   git add inventory conditional_basics.yml multiple_conditions.yml conditional_imports.yml tasks/
   git commit -m "Add conditionals examples"
   ```

## Exercise 2: Using Loops in Playbooks

In this exercise, you'll learn how to use loops to iterate over data.

1. **Create a playbook with basic loops**
   ```bash
   cat > basic_loops.yml << 'EOF'
   ---
   - name: Demonstrate Basic Loops
     hosts: all
     gather_facts: false
     vars:
       users:
         - name: alice
           groups: [admin, developers]
           state: present
         - name: bob
           groups: [developers]
           state: present
         - name: charlie
           groups: [testers]
           state: absent
       packages:
         - name: nginx
           state: present
         - name: httpd
           state: absent
         - name: postgresql
           state: present
     
     tasks:
       - name: Create users
         debug:
           msg: "Creating user {{ item.name }} with groups {{ item.groups | join(',') }}"
         loop: "{{ users }}"
         when: item.state == "present"
       
       - name: Remove users
         debug:
           msg: "Removing user {{ item.name }}"
         loop: "{{ users }}"
         when: item.state == "absent"
       
       - name: Manage packages
         debug:
           msg: "{{ 'Installing' if item.state == 'present' else 'Removing' }} package {{ item.name }}"
         loop: "{{ packages }}"
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook -i inventory basic_loops.yml
   ```

3. **Create a playbook with advanced loops**
   ```bash
   cat > advanced_loops.yml << 'EOF'
   ---
   - name: Demonstrate Advanced Loops
     hosts: all
     gather_facts: false
     vars:
       web_services:
         nginx:
           port: 80
           config_file: /etc/nginx/nginx.conf
           state: started
         apache:
           port: 8080
           config_file: /etc/httpd/conf/httpd.conf
           state: stopped
       
       database_servers:
         - name: primary
           type: postgresql
           port: 5432
           databases:
             - name: users
               owner: admin
             - name: products
               owner: app
         - name: secondary
           type: mysql
           port: 3306
           databases:
             - name: users_backup
               owner: admin
             - name: logs
               owner: app
     
     tasks:
       - name: Configure web services
         debug:
           msg: "Configuring {{ item.key }} on port {{ item.value.port }} with config {{ item.value.config_file }}"
         loop: "{{ web_services | dict2items }}"
       
       - name: Manage service state
         debug:
           msg: "Setting {{ item.key }} to {{ item.value.state }} state"
         loop: "{{ web_services | dict2items }}"
       
       - name: Configure database servers
         debug:
           msg: "Configuring {{ item.0.name }} {{ item.0.type }} server with database {{ item.1.name }} owned by {{ item.1.owner }}"
         loop: "{{ database_servers | subelements('databases') }}"
       
       - name: Demonstrate with_* loops
         debug:
           msg: "Item: {{ item }}"
         with_items:
           - apple
           - banana
           - cherry
       
       - name: Loop with index
         debug:
           msg: "{{ index }} - {{ item }}"
         loop:
           - apple
           - banana
           - cherry
         loop_control:
           index_var: index
       
       - name: Loop with label
         debug:
           msg: "Configuring {{ item.name }} ({{ item.type }})"
         loop: "{{ database_servers }}"
         loop_control:
           label: "{{ item.name }}"
   EOF
   ```

4. **Run the playbook**
   ```bash
   ansible-playbook -i inventory advanced_loops.yml
   ```

5. **Create a playbook with nested loops**
   ```bash
   cat > nested_loops.yml << 'EOF'
   ---
   - name: Demonstrate Nested Loops
     hosts: all
     gather_facts: false
     vars:
       servers:
         - name: web1
           services:
             - name: nginx
               ports: [80, 443]
             - name: php-fpm
               ports: [9000]
         - name: web2
           services:
             - name: apache
               ports: [8080, 8443]
             - name: tomcat
               ports: [8005, 8080, 8009]
         - name: db1
           services:
             - name: postgresql
               ports: [5432]
             - name: redis
               ports: [6379]
     
     tasks:
       - name: Configure services on servers
         debug:
           msg: "Configuring {{ item.0.name }} with service {{ item.1.name }} on ports {{ item.1.ports | join(',') }}"
         loop: "{{ servers | subelements('services') }}"
       
       - name: Configure individual ports
         debug:
           msg: "Configuring {{ outer_item.name }} with service {{ inner_item.name }} on port {{ port_item }}"
         loop: "{{ servers }}"
         loop_control:
           loop_var: outer_item
         loop_control:
           label: "{{ outer_item.name }}"
       
       - name: Configure individual ports (nested loops)
         debug:
           msg: "Configuring {{ outer_item.name }} with service {{ inner_item.name }} on port {{ port_item }}"
         loop: "{{ servers }}"
         loop_control:
           loop_var: outer_item
           label: "{{ outer_item.name }}"
         tasks:
           - debug:
               msg: "Configuring {{ outer_item.name }} with service {{ inner_item.name }} on port {{ port_item }}"
             loop: "{{ outer_item.services }}"
             loop_control:
               loop_var: inner_item
               label: "{{ inner_item.name }}"
             tasks:
               - debug:
                   msg: "Configuring {{ outer_item.name }} with service {{ inner_item.name }} on port {{ port_item }}"
                 loop: "{{ inner_item.ports }}"
                 loop_control:
                   loop_var: port_item
                   label: "{{ port_item }}"
   EOF
   ```

6. **Run the playbook**
   ```bash
   ansible-playbook -i inventory nested_loops.yml
   ```

7. **Commit your changes**
   ```bash
   git add basic_loops.yml advanced_loops.yml nested_loops.yml
   git commit -m "Add loops examples"
   ```

## Exercise 3: Error Handling in Playbooks

In this exercise, you'll learn how to handle errors in Ansible playbooks.

1. **Create a playbook with basic error handling**
   ```bash
   cat > error_handling_basics.yml << 'EOF'
   ---
   - name: Demonstrate Basic Error Handling
     hosts: all
     gather_facts: false
     
     tasks:
       - name: Task that will succeed
         debug:
           msg: "This task will succeed"
       
       - name: Task that will fail
         command: /bin/false
         ignore_errors: true
         register: failure_result
       
       - name: Show failure result
         debug:
           var: failure_result
       
       - name: Task that depends on previous task
         debug:
           msg: "This task runs despite the previous failure"
       
       - name: Task with custom failure condition
         command: echo "Memory usage is 85%"
         register: memory_check
         failed_when: "'100%' in memory_check.stdout"
       
       - name: Task with custom changed condition
         command: echo "No changes needed"
         register: change_check
         changed_when: "'changes' in change_check.stdout"
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook -i inventory error_handling_basics.yml
   ```

3. **Create a playbook with block error handling**
   ```bash
   cat > block_error_handling.yml << 'EOF'
   ---
   - name: Demonstrate Block Error Handling
     hosts: all
     gather_facts: false
     
     tasks:
       - name: Handle errors with blocks
         block:
           - name: Task that might fail
             command: /bin/false
           
           - name: This task will be skipped on failure
             debug:
               msg: "This will not run if the previous task fails"
         
         rescue:
           - name: Recovery task
             debug:
               msg: "Recovering from failure"
           
           - name: Another recovery task
             debug:
               msg: "Performing additional recovery steps"
         
         always:
           - name: Cleanup task
             debug:
               msg: "This will always run, regardless of success or failure"
       
       - name: Nested blocks
         block:
           - name: Outer block task
             debug:
               msg: "Outer block task"
           
           - name: Inner block
             block:
               - name: Task that will fail
                 command: /bin/false
               
               - name: This task will be skipped
                 debug:
                   msg: "This will not run"
             
             rescue:
               - name: Inner recovery
                 debug:
                   msg: "Inner block recovery"
             
             always:
               - name: Inner always
                 debug:
                   msg: "Inner always section"
         
         rescue:
           - name: Outer recovery
             debug:
               msg: "Outer block recovery (should not run)"
         
         always:
           - name: Outer always
             debug:
               msg: "Outer always section"
   EOF
   ```

4. **Run the playbook**
   ```bash
   ansible-playbook -i inventory block_error_handling.yml
   ```

5. **Create a playbook with advanced error handling**
   ```bash
   cat > advanced_error_handling.yml << 'EOF'
   ---
   - name: Demonstrate Advanced Error Handling
     hosts: all
     gather_facts: false
     vars:
       max_retries: 3
       retry_delay: 1
     
     tasks:
       - name: Retry a task
         command: /bin/false
         register: result
         retries: "{{ max_retries }}"
         delay: "{{ retry_delay }}"
         until: result.rc == 0
         ignore_errors: true
       
       - name: Show retry results
         debug:
           var: result
       
       - name: Fail with custom message
         fail:
           msg: "This is a custom failure message"
         when: result.rc != 0
         ignore_errors: true
       
       - name: Fail when condition is met
         fail:
           msg: "Disk space is too low"
         when: ansible_facts.get('mounts', [{}])[0].get('size_available', 0) < 1024 * 1024 * 1024
         ignore_errors: true
       
       - name: Task with any_errors_fatal
         debug:
           msg: "This task has any_errors_fatal set to true"
         any_errors_fatal: true
         ignore_errors: true
       
       - name: Task with no_log
         debug:
           msg: "This task has sensitive data: password123"
         no_log: true
   EOF
   ```

6. **Run the playbook**
   ```bash
   ansible-playbook -i inventory advanced_error_handling.yml
   ```

7. **Create a playbook with error handling strategies**
   ```bash
   cat > error_strategies.yml << 'EOF'
   ---
   - name: Demonstrate Linear Strategy (default)
     hosts: all
     gather_facts: false
     strategy: linear
     
     tasks:
       - name: Task 1
         debug:
           msg: "Task 1 on {{ inventory_hostname }}"
       
       - name: Task that fails on web1
         command: /bin/false
         when: inventory_hostname == "web1"
         ignore_errors: true
       
       - name: Task 3
         debug:
           msg: "Task 3 on {{ inventory_hostname }}"
   
   - name: Demonstrate Free Strategy
     hosts: all
     gather_facts: false
     strategy: free
     
     tasks:
       - name: Task 1
         debug:
           msg: "Task 1 on {{ inventory_hostname }}"
       
       - name: Task that fails on web1
         command: /bin/false
         when: inventory_hostname == "web1"
         ignore_errors: true
       
       - name: Task 3
         debug:
           msg: "Task 3 on {{ inventory_hostname }}"
   
   - name: Demonstrate Host Pinning
     hosts: all
     gather_facts: false
     
     tasks:
       - name: Task with throttle
         debug:
           msg: "Throttled task on {{ inventory_hostname }}"
         throttle: 1
   EOF
   ```

8. **Run the playbook**
   ```bash
   ansible-playbook -i inventory error_strategies.yml
   ```

9. **Commit your changes**
   ```bash
   git add error_handling_basics.yml block_error_handling.yml advanced_error_handling.yml error_strategies.yml
   git commit -m "Add error handling examples"
   ```

## Exercise 4: Combining Conditionals, Loops, and Error Handling

In this exercise, you'll create a comprehensive playbook that combines conditionals, loops, and error handling.

1. **Create a comprehensive playbook**
   ```bash
   cat > comprehensive_example.yml << 'EOF'
   ---
   - name: Comprehensive Example
     hosts: all
     gather_facts: true
     vars:
       services:
         - name: nginx
           port: 80
           required_on: webservers
           state: started
         - name: postgresql
           port: 5432
           required_on: dbservers
           state: started
         - name: redis
           port: 6379
           required_on: all
           state: stopped
       
       min_disk_space_gb: 10
       min_memory_mb: 1024
       max_retries: 3
       retry_delay: 2
     
     tasks:
       - name: System requirements check
         block:
           - name: Check disk space
             debug:
               msg: "Checking disk space on {{ inventory_hostname }}"
             register: disk_check
             failed_when: >
               ansible_facts.get('mounts', [{}])[0].get('size_available', 0) < 
               (min_disk_space_gb * 1024 * 1024 * 1024)
           
           - name: Check memory
             debug:
               msg: "Checking memory on {{ inventory_hostname }}"
             failed_when: ansible_memtotal_mb < min_memory_mb
         
         rescue:
           - name: System requirements not met
             debug:
               msg: >
                 System requirements not met on {{ inventory_hostname }}.
                 Required: {{ min_disk_space_gb }}GB disk space and {{ min_memory_mb }}MB memory.
                 Available: 
                 {{ (ansible_facts.get('mounts', [{}])[0].get('size_available', 0) / 1024 / 1024 / 1024) | round(2) }}GB disk space and 
                 {{ ansible_memtotal_mb }}MB memory.
           
           - name: Set host as failed
             set_fact:
               host_failed: true
         
         always:
           - name: System check completed
             debug:
               msg: "System check completed on {{ inventory_hostname }}"
       
       - name: Service configuration
         block:
           - name: Configure services
             debug:
               msg: >
                 Configuring {{ item.name }} on port {{ item.port }} 
                 ({{ 'started' if item.state == 'started' else 'stopped' }})
             loop: "{{ services }}"
             when: >
               item.required_on == 'all' or 
               (item.required_on == 'webservers' and inventory_hostname in groups['webservers']) or
               (item.required_on == 'dbservers' and inventory_hostname in groups['dbservers'])
             register: service_config
           
           - name: Start required services
             command: "echo Starting {{ item.item.name }}"
             loop: "{{ service_config.results }}"
             when: >
               item is defined and 
               item.item is defined and 
               item.item.state == 'started' and 
               item.skipped is not defined
             register: service_start
             retries: "{{ max_retries }}"
             delay: "{{ retry_delay }}"
             until: service_start.rc == 0
             ignore_errors: true
         
         when: host_failed is not defined or not host_failed
         
         rescue:
           - name: Service configuration failed
             debug:
               msg: "Service configuration failed on {{ inventory_hostname }}"
           
           - name: Notify administrators
             debug:
               msg: "Sending notification about service configuration failure on {{ inventory_hostname }}"
         
         always:
           - name: Service configuration completed
             debug:
               msg: "Service configuration completed on {{ inventory_hostname }}"
       
       - name: Final status report
         debug:
           msg: >
             Host: {{ inventory_hostname }}
             Status: {{ 'Failed' if (host_failed is defined and host_failed) else 'Success' }}
             Services: 
             {% for service in services %}
             {% if service.required_on == 'all' or 
                (service.required_on == 'webservers' and inventory_hostname in groups['webservers']) or
                (service.required_on == 'dbservers' and inventory_hostname in groups['dbservers']) %}
             - {{ service.name }} ({{ service.state }})
             {% endif %}
             {% endfor %}
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook -i inventory comprehensive_example.yml
   ```

3. **Commit your changes**
   ```bash
   git add comprehensive_example.yml
   git commit -m "Add comprehensive example combining conditionals, loops, and error handling"
   ```

## Conclusion

In this lab, you've learned how to:
- Use conditionals to control task execution based on various criteria
- Use loops to iterate over data structures
- Handle errors using ignore_errors, failed_when, and changed_when
- Use block/rescue/always structures for error handling
- Implement retries and until conditions
- Use different execution strategies
- Combine conditionals, loops, and error handling in comprehensive playbooks

These skills are essential for the "Use conditionals to control play execution" and "Configure error handling" objectives of the EX374 exam.

## Additional Resources

- [Ansible Conditionals Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html)
- [Ansible Loops Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html)
- [Ansible Error Handling Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_error_handling.html)
- [Ansible Strategies Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_strategies.html)
