# Lab: Task Delegation and Privilege Escalation

This lab covers the "Delegate tasks" objective of the EX374 exam. You'll learn how to delegate tasks to specific hosts, run tasks locally, and manage privilege escalation.

## Prerequisites

- Ansible Automation Platform 2.4 installed according to the [AAP Installation Guide](../guides/aap_installation_guide.md)
- Completion of the previous labs

## Lab Environment Setup

1. **Create a working directory**
   ```bash
   mkdir -p ~/ansible-labs/delegation-lab
   cd ~/ansible-labs/delegation-lab
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
   
   [loadbalancers]
   lb1 ansible_host=192.168.1.251
   
   [all:vars]
   ansible_connection=local
   EOF
   ```

## Exercise 1: Basic Task Delegation

In this exercise, you'll learn how to delegate tasks to specific hosts.

1. **Create a playbook with basic delegation**
   ```bash
   cat > basic_delegation.yml << 'EOF'
   ---
   - name: Demonstrate Basic Delegation
     hosts: webservers
     gather_facts: false
     
     tasks:
       - name: Task running on webservers
         debug:
           msg: "This task runs on {{ inventory_hostname }}"
       
       - name: Task delegated to localhost
         debug:
           msg: "This task is delegated to localhost but runs for {{ inventory_hostname }}"
         delegate_to: localhost
       
       - name: Task delegated to first database server
         debug:
           msg: "This task is delegated to the first database server but runs for {{ inventory_hostname }}"
         delegate_to: "{{ groups['dbservers'][0] }}"
       
       - name: Task delegated to the load balancer
         debug:
           msg: "This task is delegated to the load balancer but runs for {{ inventory_hostname }}"
         delegate_to: lb1
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook -i inventory basic_delegation.yml
   ```

3. **Commit your changes**
   ```bash
   git add inventory basic_delegation.yml
   git commit -m "Add basic delegation examples"
   ```

## Exercise 2: Delegation with Local Actions

In this exercise, you'll learn how to use local_action to run tasks on the control node.

1. **Create a playbook with local actions**
   ```bash
   cat > local_actions.yml << 'EOF'
   ---
   - name: Demonstrate Local Actions
     hosts: all
     gather_facts: false
     
     tasks:
       - name: Using local_action
         local_action:
           module: debug
           msg: "This task runs on the control node for {{ inventory_hostname }}"
       
       - name: Alternative local_action syntax
         local_action: debug msg="Another way to run a task locally for {{ inventory_hostname }}"
       
       - name: Create a local file for each remote host
         local_action:
           module: copy
           content: "Configuration for {{ inventory_hostname }}"
           dest: "./output/{{ inventory_hostname }}_config.txt"
         args:
           mode: '0644'
       
       - name: Check if local file exists
         local_action:
           module: stat
           path: "./output/{{ inventory_hostname }}_config.txt"
         register: file_stat
       
       - name: Show file status
         debug:
           msg: "File for {{ inventory_hostname }} exists: {{ file_stat.stat.exists }}"
   EOF
   ```

2. **Create output directory**
   ```bash
   mkdir -p output
   ```

3. **Run the playbook**
   ```bash
   ansible-playbook -i inventory local_actions.yml
   ```

4. **Commit your changes**
   ```bash
   git add local_actions.yml
   git commit -m "Add local_action examples"
   ```

## Exercise 3: Run Once with Delegation

In this exercise, you'll learn how to run a task once and delegate it to a specific host.

1. **Create a playbook with run_once and delegation**
   ```bash
   cat > run_once.yml << 'EOF'
   ---
   - name: Demonstrate Run Once with Delegation
     hosts: all
     gather_facts: false
     
     tasks:
       - name: Run once on the first host in play
         debug:
           msg: "This task runs once on {{ inventory_hostname }}"
         run_once: true
       
       - name: Run once and delegate to localhost
         debug:
           msg: "This task runs once on localhost for all hosts"
         run_once: true
         delegate_to: localhost
       
       - name: Run once and delegate to the load balancer
         debug:
           msg: "This task runs once on the load balancer for all hosts"
         run_once: true
         delegate_to: lb1
       
       - name: Create a summary file
         local_action:
           module: copy
           content: |
             Summary for all hosts:
             {% for host in groups['all'] %}
             - {{ host }}
             {% endfor %}
           dest: "./output/summary.txt"
         run_once: true
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook -i inventory run_once.yml
   ```

3. **Commit your changes**
   ```bash
   git add run_once.yml
   git commit -m "Add run_once examples"
   ```

## Exercise 4: Delegation with Changed Status

In this exercise, you'll learn how to handle the changed status when delegating tasks.

1. **Create a playbook to demonstrate delegation with changed status**
   ```bash
   cat > delegation_changed.yml << 'EOF'
   ---
   - name: Demonstrate Delegation with Changed Status
     hosts: webservers
     gather_facts: false
     
     tasks:
       - name: Task with default delegate_facts (false)
         debug:
           msg: "This task is delegated to localhost"
         delegate_to: localhost
         changed_when: true
       
       - name: Task with delegate_facts set to true
         debug:
           msg: "This task is delegated to localhost with delegate_facts"
         delegate_to: localhost
         delegate_facts: true
         changed_when: true
       
       - name: Task with changed_when based on a condition
         command: echo "Hello"
         delegate_to: localhost
         register: command_result
         changed_when: "'Hello' in command_result.stdout"
       
       - name: Show registered result
         debug:
           var: command_result
       
       - name: Task with changed_when false
         command: echo "No change"
         delegate_to: localhost
         changed_when: false
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook -i inventory delegation_changed.yml
   ```

3. **Commit your changes**
   ```bash
   git add delegation_changed.yml
   git commit -m "Add delegation with changed status examples"
   ```

## Exercise 5: Privilege Escalation

In this exercise, you'll learn how to use privilege escalation with delegation.

1. **Create a playbook to demonstrate privilege escalation**
   ```bash
   cat > privilege_escalation.yml << 'EOF'
   ---
   - name: Demonstrate Privilege Escalation
     hosts: all
     gather_facts: false
     
     tasks:
       - name: Run as root
         debug:
           msg: "This task runs as root on {{ inventory_hostname }}"
         become: true
       
       - name: Run as a specific user
         debug:
           msg: "This task runs as the specified user on {{ inventory_hostname }}"
         become: true
         become_user: nobody
       
       - name: Run with a specific method
         debug:
           msg: "This task uses a specific become method on {{ inventory_hostname }}"
         become: true
         become_method: sudo
       
       - name: Delegated task with privilege escalation
         debug:
           msg: "This task is delegated to localhost and runs as root"
         delegate_to: localhost
         become: true
       
       - name: Delegated task with different privilege escalation
         debug:
           msg: "This task is delegated to localhost and runs as a different user"
         delegate_to: localhost
         become: true
         become_user: nobody
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook -i inventory privilege_escalation.yml
   ```

3. **Commit your changes**
   ```bash
   git add privilege_escalation.yml
   git commit -m "Add privilege escalation examples"
   ```

## Exercise 6: Practical Delegation Scenarios

In this exercise, you'll apply delegation to practical scenarios.

1. **Create a playbook for load balancer configuration**
   ```bash
   cat > load_balancer_config.yml << 'EOF'
   ---
   - name: Configure Load Balancer for Web Servers
     hosts: webservers
     gather_facts: true
     
     tasks:
       - name: Gather web server information
         debug:
           msg: "Gathering information from {{ inventory_hostname }}"
         register: webserver_info
       
       - name: Update load balancer configuration
         debug:
           msg: "Adding {{ inventory_hostname }} ({{ ansible_host }}) to load balancer pool"
         delegate_to: lb1
       
       - name: Verify web server is responding
         command: echo "Checking {{ inventory_hostname }} health"
         register: health_check
         delegate_to: localhost
       
       - name: Enable web server in load balancer
         debug:
           msg: "Enabling {{ inventory_hostname }} in load balancer"
         delegate_to: lb1
         when: health_check is succeeded
   
   - name: Reload Load Balancer Configuration
     hosts: loadbalancers
     gather_facts: false
     
     tasks:
       - name: Validate configuration
         debug:
           msg: "Validating load balancer configuration on {{ inventory_hostname }}"
       
       - name: Reload configuration
         debug:
           msg: "Reloading load balancer configuration on {{ inventory_hostname }}"
       
       - name: Notify team about load balancer update
         debug:
           msg: "Load balancer {{ inventory_hostname }} has been updated with new web server configuration"
         delegate_to: localhost
         run_once: true
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook -i inventory load_balancer_config.yml
   ```

3. **Create a playbook for database backup**
   ```bash
   cat > database_backup.yml << 'EOF'
   ---
   - name: Database Backup
     hosts: dbservers
     gather_facts: true
     
     tasks:
       - name: Create backup directory locally
         file:
           path: "./backups"
           state: directory
         delegate_to: localhost
         run_once: true
       
       - name: Get current date
         command: date +%Y%m%d
         register: date_output
         delegate_to: localhost
         run_once: true
         changed_when: false
       
       - name: Set backup filename
         set_fact:
           backup_file: "db_backup_{{ inventory_hostname }}_{{ date_output.stdout }}.sql"
       
       - name: Simulate database backup
         debug:
           msg: "Creating database backup on {{ inventory_hostname }}"
         register: backup_task
       
       - name: Simulate copying backup to control node
         debug:
           msg: "Copying {{ backup_file }} from {{ inventory_hostname }} to control node"
         delegate_to: localhost
       
       - name: Verify backup file (simulated)
         debug:
           msg: "Verifying backup file {{ backup_file }}"
         delegate_to: localhost
       
       - name: Update backup log
         copy:
           content: |
             Backup completed for {{ inventory_hostname }} at {{ ansible_date_time.iso8601 }}
             Backup file: {{ backup_file }}
             Status: {{ backup_task is succeeded | ternary('Success', 'Failed') }}
           dest: "./backups/backup_log.txt"
           mode: '0644'
         delegate_to: localhost
         run_once: true
   EOF
   ```

4. **Run the playbook**
   ```bash
   ansible-playbook -i inventory database_backup.yml
   ```

5. **Create a playbook for rolling updates**
   ```bash
   cat > rolling_updates.yml << 'EOF'
   ---
   - name: Perform Rolling Updates
     hosts: webservers
     gather_facts: true
     serial: 1
     
     tasks:
       - name: Notify start of update for current host
         debug:
           msg: "Starting update for {{ inventory_hostname }}"
         delegate_to: localhost
       
       - name: Remove server from load balancer
         debug:
           msg: "Removing {{ inventory_hostname }} from load balancer pool"
         delegate_to: lb1
       
       - name: Wait for connections to drain
         debug:
           msg: "Waiting for connections to drain on {{ inventory_hostname }}"
       
       - name: Perform update
         debug:
           msg: "Updating {{ inventory_hostname }}"
       
       - name: Verify service is running
         debug:
           msg: "Verifying service is running on {{ inventory_hostname }}"
         register: service_status
       
       - name: Add server back to load balancer
         debug:
           msg: "Adding {{ inventory_hostname }} back to load balancer pool"
         delegate_to: lb1
         when: service_status is succeeded
       
       - name: Verify server is serving traffic
         debug:
           msg: "Verifying {{ inventory_hostname }} is serving traffic"
         delegate_to: localhost
       
       - name: Notify completion of update for current host
         debug:
           msg: "Completed update for {{ inventory_hostname }}"
         delegate_to: localhost
   
   - name: Post-Update Tasks
     hosts: loadbalancers
     gather_facts: false
     
     tasks:
       - name: Verify all servers are healthy
         debug:
           msg: "Verifying all servers are healthy in {{ inventory_hostname }}"
       
       - name: Send update completion notification
         debug:
           msg: "Rolling update completed successfully for all web servers"
         delegate_to: localhost
         run_once: true
   EOF
   ```

6. **Run the playbook**
   ```bash
   ansible-playbook -i inventory rolling_updates.yml
   ```

7. **Commit your changes**
   ```bash
   git add load_balancer_config.yml database_backup.yml rolling_updates.yml
   git commit -m "Add practical delegation scenarios"
   ```

## Exercise 7: Advanced Delegation Techniques

In this exercise, you'll learn advanced delegation techniques.

1. **Create a playbook with advanced delegation techniques**
   ```bash
   cat > advanced_delegation.yml << 'EOF'
   ---
   - name: Demonstrate Advanced Delegation Techniques
     hosts: all
     gather_facts: false
     
     tasks:
       - name: Delegate to each host in a group
         debug:
           msg: "Task for {{ inventory_hostname }} delegated to {{ item }}"
         loop: "{{ groups['dbservers'] }}"
         delegate_to: "{{ item }}"
       
       - name: Delegate with loop and run_once
         debug:
           msg: "Processing {{ item }} from control node"
         loop: "{{ groups['webservers'] }}"
         delegate_to: localhost
         run_once: true
       
       - name: Delegate based on a condition
         debug:
           msg: "Conditional delegation for {{ inventory_hostname }}"
         delegate_to: "{{ 'lb1' if inventory_hostname in groups['webservers'] else 'localhost' }}"
       
       - name: Delegate with complex logic
         debug:
           msg: "Complex delegation for {{ inventory_hostname }} to {{ delegation_target }}"
         vars:
           delegation_target: >-
             {% if inventory_hostname in groups['webservers'] %}
               lb1
             {% elif inventory_hostname in groups['dbservers'] %}
               {{ groups['dbservers'][0] }}
             {% else %}
               localhost
             {% endif %}
         delegate_to: "{{ delegation_target }}"
       
       - name: Delegate with facts
         debug:
           msg: "Delegated task with facts for {{ inventory_hostname }}"
         delegate_to: localhost
         delegate_facts: true
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook -i inventory advanced_delegation.yml
   ```

3. **Create a playbook for delegation with block structures**
   ```bash
   cat > delegation_blocks.yml << 'EOF'
   ---
   - name: Demonstrate Delegation with Block Structures
     hosts: webservers
     gather_facts: false
     
     tasks:
       - name: Block with delegation
         block:
           - name: Task 1 in block
             debug:
               msg: "Task 1 in block for {{ inventory_hostname }}"
           
           - name: Task 2 in block
             debug:
               msg: "Task 2 in block for {{ inventory_hostname }}"
           
           - name: Task 3 in block
             debug:
               msg: "Task 3 in block for {{ inventory_hostname }}"
         delegate_to: localhost
       
       - name: Block with delegation and privilege escalation
         block:
           - name: Task 1 with privilege
             debug:
               msg: "Task 1 with privilege for {{ inventory_hostname }}"
           
           - name: Task 2 with privilege
             debug:
               msg: "Task 2 with privilege for {{ inventory_hostname }}"
         delegate_to: localhost
         become: true
       
       - name: Block with error handling and delegation
         block:
           - name: Task that might fail
             command: /bin/false
             ignore_errors: true
           
           - name: Task after potential failure
             debug:
               msg: "This may or may not run"
         rescue:
           - name: Recovery task
             debug:
               msg: "Recovering from failure for {{ inventory_hostname }}"
         always:
           - name: Cleanup task
             debug:
               msg: "Cleaning up for {{ inventory_hostname }}"
         delegate_to: localhost
   EOF
   ```

4. **Run the playbook**
   ```bash
   ansible-playbook -i inventory delegation_blocks.yml
   ```

5. **Commit your changes**
   ```bash
   git add advanced_delegation.yml delegation_blocks.yml
   git commit -m "Add advanced delegation techniques"
   ```

## Exercise 8: Comprehensive Delegation Example

In this exercise, you'll create a comprehensive playbook that uses various delegation techniques.

1. **Create a comprehensive delegation playbook**
   ```bash
   cat > comprehensive_delegation.yml << 'EOF'
   ---
   - name: Comprehensive Delegation Example
     hosts: all
     gather_facts: true
     
     tasks:
       - name: Create host groups based on inventory
         group_by:
           key: "{{ ansible_os_family | lower }}_servers"
       
       - name: Create directory for reports
         file:
           path: "./reports"
           state: directory
         delegate_to: localhost
         run_once: true
     
   - name: Web Server Configuration
     hosts: webservers
     gather_facts: true
     
     tasks:
       - name: Check web server status
         debug:
           msg: "Checking status of {{ inventory_hostname }}"
         register: webserver_status
       
       - name: Update load balancer for each web server
         block:
           - name: Remove from load balancer temporarily
             debug:
               msg: "Removing {{ inventory_hostname }} from load balancer"
           
           - name: Apply configuration changes
             debug:
               msg: "Applying configuration changes to {{ inventory_hostname }}"
           
           - name: Add back to load balancer
             debug:
               msg: "Adding {{ inventory_hostname }} back to load balancer"
         delegate_to: lb1
         when: webserver_status is succeeded
       
       - name: Generate web server report
         copy:
           content: |
             Web Server Report for {{ inventory_hostname }}
             ===============================
             Hostname: {{ inventory_hostname }}
             IP Address: {{ ansible_host }}
             OS: {{ ansible_distribution }} {{ ansible_distribution_version }}
             Status: {{ webserver_status is succeeded | ternary('Online', 'Offline') }}
             Date: {{ ansible_date_time.iso8601 }}
           dest: "./reports/{{ inventory_hostname }}_report.txt"
         delegate_to: localhost
     
   - name: Database Server Configuration
     hosts: dbservers
     gather_facts: true
     
     tasks:
       - name: Check database status
         debug:
           msg: "Checking status of {{ inventory_hostname }}"
         register: db_status
       
       - name: Simulate database backup
         debug:
           msg: "Creating backup of {{ inventory_hostname }}"
         register: backup_result
       
       - name: Copy backup to control node
         debug:
           msg: "Copying backup from {{ inventory_hostname }} to control node"
         delegate_to: localhost
         when: backup_result is succeeded
       
       - name: Generate database server report
         copy:
           content: |
             Database Server Report for {{ inventory_hostname }}
             ===============================
             Hostname: {{ inventory_hostname }}
             IP Address: {{ ansible_host }}
             OS: {{ ansible_distribution }} {{ ansible_distribution_version }}
             Status: {{ db_status is succeeded | ternary('Online', 'Offline') }}
             Backup Status: {{ backup_result is succeeded | ternary('Success', 'Failed') }}
             Date: {{ ansible_date_time.iso8601 }}
           dest: "./reports/{{ inventory_hostname }}_report.txt"
         delegate_to: localhost
     
   - name: Load Balancer Configuration
     hosts: loadbalancers
     gather_facts: true
     
     tasks:
       - name: Check load balancer status
         debug:
           msg: "Checking status of {{ inventory_hostname }}"
         register: lb_status
       
       - name: Verify web server connectivity
         debug:
           msg: "Verifying connectivity to {{ item }}"
         loop: "{{ groups['webservers'] }}"
         register: connectivity_results
       
       - name: Generate load balancer report
         copy:
           content: |
             Load Balancer Report for {{ inventory_hostname }}
             ===============================
             Hostname: {{ inventory_hostname }}
             IP Address: {{ ansible_host }}
             OS: {{ ansible_distribution }} {{ ansible_distribution_version }}
             Status: {{ lb_status is succeeded | ternary('Online', 'Offline') }}
             Web Servers:
             {% for result in connectivity_results.results %}
             - {{ result.item }}: {{ result is succeeded | ternary('Connected', 'Disconnected') }}
             {% endfor %}
             Date: {{ ansible_date_time.iso8601 }}
           dest: "./reports/{{ inventory_hostname }}_report.txt"
         delegate_to: localhost
     
   - name: Generate Summary Report
     hosts: localhost
     gather_facts: false
     
     tasks:
       - name: Collect all reports
         find:
           paths: "./reports"
           patterns: "*_report.txt"
         register: report_files
       
       - name: Generate summary report
         copy:
           content: |
             Infrastructure Summary Report
             ============================
             Generated: {{ ansible_date_time.iso8601 }}
             
             Server Inventory:
             - Web Servers: {{ groups['webservers'] | length }}
             - Database Servers: {{ groups['dbservers'] | length }}
             - Load Balancers: {{ groups['loadbalancers'] | length }}
             
             Individual Reports:
             {% for file in report_files.files %}
             - {{ file.path | basename }}
             {% endfor %}
           dest: "./reports/summary_report.txt"
       
       - name: Display summary
         debug:
           msg: "Summary report generated at ./reports/summary_report.txt"
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook -i inventory comprehensive_delegation.yml
   ```

3. **Commit your changes**
   ```bash
   git add comprehensive_delegation.yml
   git commit -m "Add comprehensive delegation example"
   ```

## Conclusion

In this lab, you've learned how to:
- Delegate tasks to specific hosts
- Use local_action to run tasks on the control node
- Combine run_once with delegation
- Handle changed status with delegation
- Use privilege escalation with delegation
- Apply delegation to practical scenarios
- Use advanced delegation techniques
- Create comprehensive playbooks with delegation

These skills are essential for the "Delegate tasks" objective of the EX374 exam.

## Additional Resources

- [Ansible Delegation Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_delegation.html)
- [Ansible Privilege Escalation Documentation](https://docs.ansible.com/ansible/latest/user_guide/become.html)
- [Ansible Local Actions Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_delegation.html#local-actions)
