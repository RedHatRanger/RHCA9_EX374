# Lab: Data Transformation with Filters and Plugins

This lab covers the "Transform data with filters and plugins" objective of the EX374 exam. You'll learn how to use Ansible filters and plugins to manipulate and transform data in your playbooks.

## Prerequisites

- Ansible Automation Platform 2.4 installed according to the [AAP Installation Guide](../guides/aap_installation_guide.md)
- Completion of the previous labs

## Lab Environment Setup

1. **Create a working directory**
   ```bash
   mkdir -p ~/ansible-labs/data-transformation-lab
   cd ~/ansible-labs/data-transformation-lab
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
   
   [all:vars]
   ansible_connection=local
   EOF
   ```

## Exercise 1: Basic Data Transformation with Filters

In this exercise, you'll learn how to use basic filters to transform data.

1. **Create a playbook with basic filters**
   ```bash
   cat > basic_filters.yml << 'EOF'
   ---
   - name: Demonstrate Basic Filters
     hosts: localhost
     gather_facts: false
     vars:
       my_string: "Hello, World!"
       my_list: [3, 1, 4, 1, 5, 9, 2, 6]
       my_dict: 
         name: John
         age: 30
         city: New York
       my_boolean: true
       my_number: 42
       my_float: 3.14159
       my_empty: ""
       my_undefined: null
     
     tasks:
       # String filters
       - name: String filters
         debug:
           msg:
             - "Original: {{ my_string }}"
             - "Uppercase: {{ my_string | upper }}"
             - "Lowercase: {{ my_string | lower }}"
             - "Title case: {{ my_string | title }}"
             - "Replace: {{ my_string | replace('World', 'Ansible') }}"
             - "Default: {{ my_empty | default('Empty string') }}"
             - "Default for undefined: {{ my_undefined | default('Undefined variable') }}"
             - "Length: {{ my_string | length }}"
       
       # List filters
       - name: List filters
         debug:
           msg:
             - "Original list: {{ my_list }}"
             - "Sorted: {{ my_list | sort }}"
             - "Reversed: {{ my_list | reverse }}"
             - "First item: {{ my_list | first }}"
             - "Last item: {{ my_list | last }}"
             - "Min value: {{ my_list | min }}"
             - "Max value: {{ my_list | max }}"
             - "Sum: {{ my_list | sum }}"
             - "Unique items: {{ [1, 2, 3, 1, 2, 3] | unique }}"
             - "Joined with comma: {{ my_list | join(',') }}"
       
       # Dictionary filters
       - name: Dictionary filters
         debug:
           msg:
             - "Original dict: {{ my_dict }}"
             - "Dict keys: {{ my_dict.keys() | list }}"
             - "Dict values: {{ my_dict.values() | list }}"
             - "Dict items: {{ my_dict.items() | list }}"
             - "Dict to JSON: {{ my_dict | to_json }}"
             - "Dict to YAML: {{ my_dict | to_yaml }}"
       
       # Type conversion filters
       - name: Type conversion filters
         debug:
           msg:
             - "String to int: {{ '42' | int }}"
             - "Float to int: {{ 3.7 | int }}"
             - "Bool to int: {{ true | int }}"
             - "Int to string: {{ 42 | string }}"
             - "Bool to string: {{ true | string }}"
             - "String to bool: {{ 'yes' | bool }}"
             - "String to float: {{ '3.14' | float }}"
       
       # Boolean filters
       - name: Boolean filters
         debug:
           msg:
             - "Boolean true: {{ my_boolean }}"
             - "Boolean false: {{ not my_boolean }}"
             - "Ternary operator: {{ (my_number > 50) | ternary('Greater than 50', 'Less than or equal to 50') }}"
             - "Truthy check: {{ my_empty | bool }}"
             - "Truthy check with string: {{ 'yes' | bool }}"
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook basic_filters.yml
   ```

3. **Commit your changes**
   ```bash
   git add inventory basic_filters.yml
   git commit -m "Add basic filters examples"
   ```

## Exercise 2: Advanced Data Transformation with Filters

In this exercise, you'll learn how to use more advanced filters for data transformation.

1. **Create a playbook with advanced filters**
   ```bash
   cat > advanced_filters.yml << 'EOF'
   ---
   - name: Demonstrate Advanced Filters
     hosts: localhost
     gather_facts: false
     vars:
       users:
         - name: alice
           groups: [admin, developers]
           uid: 1001
         - name: bob
           groups: [developers]
           uid: 1002
         - name: charlie
           groups: [testers]
           uid: 1003
       
       servers:
         web01:
           ip: 192.168.1.101
           role: web
           environment: production
         web02:
           ip: 192.168.1.102
           role: web
           environment: staging
         db01:
           ip: 192.168.1.201
           role: database
           environment: production
       
       network_config:
         subnet: 192.168.1.0/24
         gateway: 192.168.1.1
         dns:
           - 8.8.8.8
           - 8.8.4.4
       
       complex_data:
         - name: Project A
           status: active
           members:
             - alice
             - bob
           resources:
             cpu: 2
             memory: 4GB
         - name: Project B
           status: inactive
           members:
             - charlie
           resources:
             cpu: 1
             memory: 2GB
     
     tasks:
       # Map, select, and reject filters
       - name: Map, select, and reject filters
         debug:
           msg:
             - "User names: {{ users | map(attribute='name') | list }}"
             - "User UIDs: {{ users | map(attribute='uid') | list }}"
             - "Admin users: {{ users | selectattr('groups', 'contains', 'admin') | list }}"
             - "Non-admin users: {{ users | rejectattr('groups', 'contains', 'admin') | list }}"
             - "Production servers: {{ servers | dict2items | selectattr('value.environment', 'equalto', 'production') | list }}"
             - "Web servers: {{ servers | dict2items | selectattr('value.role', 'equalto', 'web') | map(attribute='key') | list }}"
       
       # Combine and zip filters
       - name: Combine and zip filters
         debug:
           msg:
             - "Combine dictionaries: {{ {'a': 1, 'b': 2} | combine({'b': 3, 'c': 4}) }}"
             - "Combine with recursive: {{ {'a': {'x': 1, 'y': 2}} | combine({'a': {'y': 3, 'z': 4}}, recursive=True) }}"
             - "Zip lists: {{ ['a', 'b', 'c'] | zip(['1', '2', '3']) | list }}"
             - "Zip with longest: {{ ['a', 'b'] | zip_longest(['1', '2', '3'], fillvalue='x') | list }}"
       
       # Dict transformation
       - name: Dict transformation
         debug:
           msg:
             - "Dict to items: {{ servers | dict2items }}"
             - "Items to dict (by key): {{ servers | dict2items | items2dict }}"
             - "Items to dict (by field): {{ users | items2dict(key_name='name', value_name='uid') }}"
       
       # JSON and YAML filters
       - name: JSON and YAML filters
         debug:
           msg:
             - "To JSON: {{ complex_data | to_json }}"
             - "To nice JSON: {{ complex_data | to_nice_json }}"
             - "To YAML: {{ complex_data | to_yaml }}"
             - "From JSON: {{ '{\"name\":\"test\",\"value\":123}' | from_json }}"
       
       # Network filters
       - name: Network filters
         debug:
           msg:
             - "IP address in network: {{ '192.168.1.100' | ipaddr('192.168.1.0/24') }}"
             - "IP version: {{ '192.168.1.100' | ipv4 }}"
             - "Next IP: {{ '192.168.1.100' | ipmath(1) }}"
             - "Network address: {{ '192.168.1.100/24' | ipaddr('network') }}"
             - "Broadcast address: {{ '192.168.1.100/24' | ipaddr('broadcast') }}"
             - "Netmask: {{ '192.168.1.100/24' | ipaddr('netmask') }}"
             - "Prefix length: {{ '192.168.1.100/24' | ipaddr('prefix') }}"
       
       # Path filters
       - name: Path filters
         debug:
           msg:
             - "Basename: {{ '/path/to/file.txt' | basename }}"
             - "Dirname: {{ '/path/to/file.txt' | dirname }}"
             - "Expanduser: {{ '~/file.txt' | expanduser }}"
             - "Realpath: {{ '/etc/../etc/hosts' | realpath }}"
             - "Splitext: {{ 'file.txt' | splitext }}"
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook advanced_filters.yml
   ```

3. **Commit your changes**
   ```bash
   git add advanced_filters.yml
   git commit -m "Add advanced filters examples"
   ```

## Exercise 3: Custom Filters and Plugins

In this exercise, you'll learn how to create and use custom filters and plugins.

1. **Create a filter plugins directory**
   ```bash
   mkdir -p filter_plugins
   ```

2. **Create a custom filter plugin**
   ```bash
   cat > filter_plugins/custom_filters.py << 'EOF'
   #!/usr/bin/python
   
   class FilterModule(object):
       """Custom filters for string manipulation."""
   
       def filters(self):
           return {
               'reverse_string': self.reverse_string,
               'count_vowels': self.count_vowels,
               'caesar_cipher': self.caesar_cipher,
               'format_size': self.format_size,
           }
   
       def reverse_string(self, string):
           """Reverse a string."""
           return string[::-1]
   
       def count_vowels(self, string):
           """Count vowels in a string."""
           return sum(1 for char in string.lower() if char in 'aeiou')
   
       def caesar_cipher(self, string, shift=1):
           """Apply Caesar cipher to a string."""
           result = ""
           for char in string:
               if char.isalpha():
                   ascii_offset = ord('a') if char.islower() else ord('A')
                   result += chr((ord(char) - ascii_offset + shift) % 26 + ascii_offset)
               else:
                   result += char
           return result
   
       def format_size(self, size_in_bytes):
           """Format bytes to human-readable size."""
           for unit in ['B', 'KB', 'MB', 'GB', 'TB']:
               if size_in_bytes < 1024 or unit == 'TB':
                   return f"{size_in_bytes:.2f} {unit}"
               size_in_bytes /= 1024
   EOF
   ```

3. **Create a playbook to test custom filters**
   ```bash
   cat > custom_filters.yml << 'EOF'
   ---
   - name: Demonstrate Custom Filters
     hosts: localhost
     gather_facts: false
     vars:
       test_string: "Hello, Ansible!"
       file_sizes:
         - 123
         - 1234
         - 12345
         - 123456
         - 1234567
         - 12345678
         - 123456789
     
     tasks:
       - name: Use custom string filters
         debug:
           msg:
             - "Original: {{ test_string }}"
             - "Reversed: {{ test_string | reverse_string }}"
             - "Vowel count: {{ test_string | count_vowels }}"
             - "Caesar cipher (shift 1): {{ test_string | caesar_cipher }}"
             - "Caesar cipher (shift 3): {{ test_string | caesar_cipher(3) }}"
       
       - name: Format file sizes
         debug:
           msg: "{{ item }} bytes = {{ item | format_size }}"
         loop: "{{ file_sizes }}"
   EOF
   ```

4. **Run the playbook**
   ```bash
   ansible-playbook custom_filters.yml
   ```

5. **Create a lookup plugin directory**
   ```bash
   mkdir -p lookup_plugins
   ```

6. **Create a custom lookup plugin**
   ```bash
   cat > lookup_plugins/random_quote.py << 'EOF'
   #!/usr/bin/python
   
   from ansible.plugins.lookup import LookupBase
   import random
   
   class LookupModule(LookupBase):
       quotes = [
           "The only way to do great work is to love what you do. - Steve Jobs",
           "Life is what happens when you're busy making other plans. - John Lennon",
           "The future belongs to those who believe in the beauty of their dreams. - Eleanor Roosevelt",
           "Success is not final, failure is not fatal: It is the courage to continue that counts. - Winston Churchill",
           "The best way to predict the future is to create it. - Peter Drucker",
           "Automation isn't about replacing humans, it's about enabling them. - Unknown",
           "The most powerful tool we have as developers is automation. - Scott Hanselman",
           "The first rule of any technology used in a business is that automation applied to an efficient operation will magnify the efficiency. - Bill Gates",
           "The greatest glory in living lies not in never falling, but in rising every time we fall. - Nelson Mandela",
           "The way to get started is to quit talking and begin doing. - Walt Disney"
       ]
       
       def run(self, terms, variables=None, **kwargs):
           result = []
           count = int(terms[0]) if terms and terms[0].isdigit() else 1
           
           for _ in range(count):
               result.append(random.choice(self.quotes))
               
           return result
   EOF
   ```

7. **Create a playbook to test custom lookup plugin**
   ```bash
   cat > custom_lookup.yml << 'EOF'
   ---
   - name: Demonstrate Custom Lookup Plugin
     hosts: localhost
     gather_facts: false
     
     tasks:
       - name: Get a random quote
         debug:
           msg: "Quote of the day: {{ lookup('random_quote') }}"
       
       - name: Get multiple random quotes
         debug:
           msg: "{{ item }}"
         loop: "{{ query('random_quote', '3') }}"
   EOF
   ```

8. **Run the playbook**
   ```bash
   ansible-playbook custom_lookup.yml
   ```

9. **Commit your changes**
   ```bash
   git add filter_plugins/ lookup_plugins/ custom_filters.yml custom_lookup.yml
   git commit -m "Add custom filters and plugins examples"
   ```

## Exercise 4: Practical Data Transformation Scenarios

In this exercise, you'll apply filters and plugins to solve practical scenarios.

1. **Create a playbook for user management**
   ```bash
   cat > user_management.yml << 'EOF'
   ---
   - name: User Management with Data Transformation
     hosts: localhost
     gather_facts: false
     vars:
       user_data:
         - name: alice
           full_name: Alice Smith
           groups: [admin, developers]
           password: password123
           shell: /bin/bash
           ssh_key: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQ...
         - name: bob
           full_name: Bob Johnson
           groups: [developers]
           password: password456
           shell: /bin/zsh
           ssh_key: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQ...
         - name: charlie
           full_name: Charlie Brown
           groups: [testers]
           password: password789
           shell: /bin/bash
           ssh_key: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQ...
       
       admin_group: admin
       default_shell: /bin/bash
       password_salt: randomsalt
     
     tasks:
       - name: Generate user accounts configuration
         debug:
           msg: |
             # User Accounts Configuration
             # Generated by Ansible on {{ ansible_date_time.date }}
             
             {% for user in user_data %}
             # User: {{ user.full_name }}
             user_{{ user.name }}:
               username: {{ user.name }}
               full_name: {{ user.full_name }}
               uid: {{ 1000 + loop.index }}
               groups: {{ user.groups | join(',') }}
               is_admin: {{ user.groups is defined and admin_group in user.groups }}
               password_hash: {{ user.password | password_hash('sha512', password_salt) }}
               shell: {{ user.shell | default(default_shell) }}
               home_directory: {{ '/home/' + user.name }}
             {% endfor %}
             
             # Group Summary
             groups:
             {% for group in user_data | map(attribute='groups') | flatten | unique | sort %}
               - {{ group }}
             {% endfor %}
             
             # Admin Users
             admin_users:
             {% for user in user_data | selectattr('groups', 'contains', admin_group) %}
               - {{ user.name }}
             {% endfor %}
       
       - name: Generate SSH authorized keys files
         debug:
           msg: |
             # SSH Authorized Keys Configuration
             # Generated by Ansible on {{ ansible_date_time.date }}
             
             {% for user in user_data %}
             {{ user.name }}_authorized_keys:
               path: {{ '/home/' + user.name + '/.ssh/authorized_keys' }}
               content: |
                 {{ user.ssh_key }}
             {% endfor %}
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook user_management.yml
   ```

3. **Create a playbook for network configuration**
   ```bash
   cat > network_config.yml << 'EOF'
   ---
   - name: Network Configuration with Data Transformation
     hosts: all
     gather_facts: false
     vars:
       network_data:
         domain: example.com
         dns_servers:
           - 8.8.8.8
           - 8.8.4.4
         subnets:
           management:
             cidr: 10.0.0.0/24
             vlan: 10
             gateway: 10.0.0.1
           production:
             cidr: 10.0.1.0/24
             vlan: 20
             gateway: 10.0.1.1
           development:
             cidr: 10.0.2.0/24
             vlan: 30
             gateway: 10.0.2.1
         
         hosts:
           web1:
             subnet: production
             ip: 10.0.1.101
             interfaces:
               - name: eth0
                 type: physical
               - name: eth1
                 type: physical
           web2:
             subnet: production
             ip: 10.0.1.102
             interfaces:
               - name: eth0
                 type: physical
           db1:
             subnet: production
             ip: 10.0.1.201
             interfaces:
               - name: eth0
                 type: physical
               - name: eth1
                 type: physical
     
     tasks:
       - name: Set host-specific network configuration
         set_fact:
           host_config: "{{ network_data.hosts[inventory_hostname] | default({}) }}"
         when: inventory_hostname in network_data.hosts
       
       - name: Skip hosts not in network data
         meta: end_play
         when: host_config is not defined or host_config | length == 0
       
       - name: Set subnet configuration
         set_fact:
           subnet_config: "{{ network_data.subnets[host_config.subnet] }}"
         when: host_config.subnet is defined and host_config.subnet in network_data.subnets
       
       - name: Generate network configuration
         debug:
           msg: |
             # Network Configuration for {{ inventory_hostname }}.{{ network_data.domain }}
             # Generated by Ansible on {{ ansible_date_time.date }}
             
             hostname: {{ inventory_hostname }}
             domain: {{ network_data.domain }}
             fqdn: {{ inventory_hostname }}.{{ network_data.domain }}
             
             # Primary IP Configuration
             ip_address: {{ host_config.ip }}
             subnet_mask: {{ subnet_config.cidr | ipaddr('netmask') }}
             network: {{ subnet_config.cidr | ipaddr('network') }}
             broadcast: {{ subnet_config.cidr | ipaddr('broadcast') }}
             gateway: {{ subnet_config.gateway }}
             vlan_id: {{ subnet_config.vlan }}
             
             # DNS Configuration
             nameservers:
             {% for dns in network_data.dns_servers %}
               - {{ dns }}
             {% endfor %}
             
             # Interface Configuration
             interfaces:
             {% for interface in host_config.interfaces %}
               - name: {{ interface.name }}
                 type: {{ interface.type }}
                 {% if loop.index == 1 %}
                 ip_address: {{ host_config.ip }}
                 subnet_mask: {{ subnet_config.cidr | ipaddr('netmask') }}
                 gateway: {{ subnet_config.gateway }}
                 {% else %}
                 # Secondary interface - no IP configuration
                 {% endif %}
             {% endfor %}
             
             # Routing Table
             routes:
             {% for subnet_name, subnet in network_data.subnets.items() %}
               {% if subnet_name != host_config.subnet %}
               - network: {{ subnet.cidr | ipaddr('network') }}
                 netmask: {{ subnet.cidr | ipaddr('netmask') }}
                 gateway: {{ subnet_config.gateway }}
               {% endif %}
             {% endfor %}
         when: subnet_config is defined
   EOF
   ```

4. **Run the playbook**
   ```bash
   ansible-playbook -i inventory network_config.yml
   ```

5. **Create a playbook for data analysis**
   ```bash
   cat > data_analysis.yml << 'EOF'
   ---
   - name: Data Analysis with Filters
     hosts: localhost
     gather_facts: false
     vars:
       server_metrics:
         - server: web1
           cpu_usage: 45.2
           memory_usage: 62.8
           disk_usage: 78.3
           response_time: 0.245
           timestamp: "2023-06-15T10:30:00"
         - server: web2
           cpu_usage: 32.7
           memory_usage: 48.5
           disk_usage: 65.1
           response_time: 0.189
           timestamp: "2023-06-15T10:30:00"
         - server: db1
           cpu_usage: 78.9
           memory_usage: 85.2
           disk_usage: 92.7
           response_time: 0.312
           timestamp: "2023-06-15T10:30:00"
         - server: web1
           cpu_usage: 51.3
           memory_usage: 67.4
           disk_usage: 78.5
           response_time: 0.278
           timestamp: "2023-06-15T11:30:00"
         - server: web2
           cpu_usage: 38.1
           memory_usage: 52.3
           disk_usage: 65.8
           response_time: 0.201
           timestamp: "2023-06-15T11:30:00"
         - server: db1
           cpu_usage: 82.4
           memory_usage: 88.7
           disk_usage: 93.1
           response_time: 0.345
           timestamp: "2023-06-15T11:30:00"
     
     tasks:
       - name: Calculate average metrics by server
         debug:
           msg: |
             # Server Performance Analysis
             # Generated by Ansible on {{ ansible_date_time.date }}
             
             ## Average Metrics by Server
             
             {% for server in server_metrics | map(attribute='server') | unique %}
             ### {{ server }}
             {% set server_data = server_metrics | selectattr('server', 'equalto', server) | list %}
             - Average CPU Usage: {{ (server_data | map(attribute='cpu_usage') | sum) / server_data | length | round(2) }}%
             - Average Memory Usage: {{ (server_data | map(attribute='memory_usage') | sum) / server_data | length | round(2) }}%
             - Average Disk Usage: {{ (server_data | map(attribute='disk_usage') | sum) / server_data | length | round(2) }}%
             - Average Response Time: {{ (server_data | map(attribute='response_time') | sum) / server_data | length | round(3) }} seconds
             {% endfor %}
             
             ## Performance Rankings
             
             ### CPU Usage (Highest to Lowest)
             {% for item in server_metrics | map(attribute='server') | unique | sort(attribute=server_metrics | selectattr('server', 'equalto', item) | map(attribute='cpu_usage') | sum) | reverse %}
             {% set server_data = server_metrics | selectattr('server', 'equalto', item) | list %}
             {% set avg_cpu = (server_data | map(attribute='cpu_usage') | sum) / server_data | length | round(2) %}
             - {{ item }}: {{ avg_cpu }}%
             {% endfor %}
             
             ### Memory Usage (Highest to Lowest)
             {% for item in server_metrics | map(attribute='server') | unique | sort(attribute=server_metrics | selectattr('server', 'equalto', item) | map(attribute='memory_usage') | sum) | reverse %}
             {% set server_data = server_metrics | selectattr('server', 'equalto', item) | list %}
             {% set avg_memory = (server_data | map(attribute='memory_usage') | sum) / server_data | length | round(2) %}
             - {{ item }}: {{ avg_memory }}%
             {% endfor %}
             
             ### Response Time (Fastest to Slowest)
             {% for item in server_metrics | map(attribute='server') | unique | sort(attribute=server_metrics | selectattr('server', 'equalto', item) | map(attribute='response_time') | sum) %}
             {% set server_data = server_metrics | selectattr('server', 'equalto', item) | list %}
             {% set avg_response = (server_data | map(attribute='response_time') | sum) / server_data | length | round(3) %}
             - {{ item }}: {{ avg_response }} seconds
             {% endfor %}
             
             ## Alert Summary
             
             {% for metric in server_metrics %}
             {% if metric.cpu_usage > 75 or metric.memory_usage > 80 or metric.disk_usage > 90 %}
             - {{ metric.server }} at {{ metric.timestamp }}:
               {% if metric.cpu_usage > 75 %}CPU: {{ metric.cpu_usage }}% (HIGH){% endif %}
               {% if metric.memory_usage > 80 %}Memory: {{ metric.memory_usage }}% (HIGH){% endif %}
               {% if metric.disk_usage > 90 %}Disk: {{ metric.disk_usage }}% (CRITICAL){% endif %}
             {% endif %}
             {% endfor %}
   EOF
   ```

6. **Run the playbook**
   ```bash
   ansible-playbook data_analysis.yml
   ```

7. **Commit your changes**
   ```bash
   git add user_management.yml network_config.yml data_analysis.yml
   git commit -m "Add practical data transformation scenarios"
   ```

## Exercise 5: Working with Complex Data Structures

In this exercise, you'll work with complex data structures and transformations.

1. **Create a playbook for complex data transformation**
   ```bash
   cat > complex_data_transformation.yml << 'EOF'
   ---
   - name: Complex Data Transformation
     hosts: localhost
     gather_facts: false
     vars:
       # Complex nested data structure
       organization:
         name: "Example Corp"
         departments:
           IT:
             manager: "John Smith"
             budget: 500000
             employees:
               - name: "Alice Johnson"
                 position: "System Administrator"
                 skills: ["Linux", "Ansible", "AWS"]
                 projects: ["Infrastructure Upgrade", "Cloud Migration"]
               - name: "Bob Williams"
                 position: "Network Engineer"
                 skills: ["Cisco", "Juniper", "VPN"]
                 projects: ["Network Redesign"]
               - name: "Charlie Davis"
                 position: "DevOps Engineer"
                 skills: ["Docker", "Kubernetes", "CI/CD", "Ansible"]
                 projects: ["CI/CD Pipeline", "Container Migration", "Infrastructure Upgrade"]
           Marketing:
             manager: "Sarah Johnson"
             budget: 350000
             employees:
               - name: "David Miller"
                 position: "Marketing Specialist"
                 skills: ["Social Media", "Content Creation"]
                 projects: ["Brand Refresh", "Social Media Campaign"]
               - name: "Emma Wilson"
                 position: "Graphic Designer"
                 skills: ["Photoshop", "Illustrator", "InDesign"]
                 projects: ["Brand Refresh", "Website Redesign"]
           Finance:
             manager: "Michael Brown"
             budget: 450000
             employees:
               - name: "Olivia Taylor"
                 position: "Financial Analyst"
                 skills: ["Accounting", "Forecasting", "Excel"]
                 projects: ["Budget Planning", "Cost Reduction"]
               - name: "James Anderson"
                 position: "Accountant"
                 skills: ["Accounting", "Tax", "Compliance"]
                 projects: ["Tax Filing", "Audit Preparation"]
     
     tasks:
       - name: Extract all employees
         set_fact:
           all_employees: >-
             {% set employees = [] %}
             {% for dept_name, dept in organization.departments.items() %}
               {% for emp in dept.employees %}
                 {% set _ = employees.append({'name': emp.name, 'position': emp.position, 'department': dept_name, 'manager': dept.manager}) %}
               {% endfor %}
             {% endfor %}
             {{ employees }}
       
       - name: Show all employees
         debug:
           var: all_employees
       
       - name: Extract employees by skill
         set_fact:
           employees_by_skill: >-
             {% set skill_map = {} %}
             {% for dept_name, dept in organization.departments.items() %}
               {% for emp in dept.employees %}
                 {% for skill in emp.skills %}
                   {% if skill not in skill_map %}
                     {% set _ = skill_map.update({skill: []}) %}
                   {% endif %}
                   {% set _ = skill_map[skill].append({'name': emp.name, 'department': dept_name}) %}
                 {% endfor %}
               {% endfor %}
             {% endfor %}
             {{ skill_map }}
       
       - name: Show employees by skill
         debug:
           var: employees_by_skill
       
       - name: Extract project assignments
         set_fact:
           project_assignments: >-
             {% set project_map = {} %}
             {% for dept_name, dept in organization.departments.items() %}
               {% for emp in dept.employees %}
                 {% for project in emp.projects %}
                   {% if project not in project_map %}
                     {% set _ = project_map.update({project: []}) %}
                   {% endif %}
                   {% set _ = project_map[project].append({'name': emp.name, 'department': dept_name, 'position': emp.position}) %}
                 {% endfor %}
               {% endfor %}
             {% endfor %}
             {{ project_map }}
       
       - name: Show project assignments
         debug:
           var: project_assignments
       
       - name: Generate department summary
         debug:
           msg: |
             # Organization Summary for {{ organization.name }}
             # Generated by Ansible on {{ ansible_date_time.date }}
             
             ## Department Overview
             
             {% for dept_name, dept in organization.departments.items() %}
             ### {{ dept_name }} Department
             - Manager: {{ dept.manager }}
             - Budget: ${{ dept.budget | int }}
             - Employees: {{ dept.employees | length }}
             - Total Skills: {{ dept.employees | map(attribute='skills') | flatten | unique | list | length }}
             - Projects: {{ dept.employees | map(attribute='projects') | flatten | unique | list | length }}
             
             #### Employees:
             {% for emp in dept.employees %}
             - {{ emp.name }} ({{ emp.position }})
               - Skills: {{ emp.skills | join(', ') }}
               - Projects: {{ emp.projects | join(', ') }}
             {% endfor %}
             
             {% endfor %}
             
             ## Cross-Department Projects
             
             {% for project, members in project_assignments.items() %}
             {% if members | map(attribute='department') | unique | list | length > 1 %}
             ### {{ project }}
             {% for member in members %}
             - {{ member.name }} ({{ member.position }}, {{ member.department }})
             {% endfor %}
             
             {% endif %}
             {% endfor %}
             
             ## Skill Distribution
             
             {% for skill, employees in employees_by_skill.items() %}
             ### {{ skill }}
             - Count: {{ employees | length }}
             - Departments: {{ employees | map(attribute='department') | unique | join(', ') }}
             {% endfor %}
             
             ## Budget Allocation
             
             Total Budget: ${{ organization.departments.values() | map(attribute='budget') | sum }}
             
             {% for dept_name, dept in organization.departments.items() %}
             - {{ dept_name }}: ${{ dept.budget }} ({{ (dept.budget / (organization.departments.values() | map(attribute='budget') | sum) * 100) | round(1) }}%)
             {% endfor %}
       
       - name: Generate organization chart in JSON format
         set_fact:
           org_chart: >-
             {
               "name": "{{ organization.name }}",
               "departments": [
                 {% for dept_name, dept in organization.departments.items() %}
                 {
                   "name": "{{ dept_name }}",
                   "manager": "{{ dept.manager }}",
                   "budget": {{ dept.budget }},
                   "employees": [
                     {% for emp in dept.employees %}
                     {
                       "name": "{{ emp.name }}",
                       "position": "{{ emp.position }}",
                       "skills": {{ emp.skills | to_json }},
                       "projects": {{ emp.projects | to_json }}
                     }{% if not loop.last %},{% endif %}
                     {% endfor %}
                   ]
                 }{% if not loop.last %},{% endif %}
                 {% endfor %}
               ]
             }
       
       - name: Show organization chart
         debug:
           msg: "{{ org_chart | from_json | to_nice_json }}"
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook complex_data_transformation.yml
   ```

3. **Commit your changes**
   ```bash
   git add complex_data_transformation.yml
   git commit -m "Add complex data transformation examples"
   ```

## Conclusion

In this lab, you've learned how to:
- Use basic filters for string, list, dictionary, and type transformations
- Apply advanced filters like map, select, reject, combine, and zip
- Work with JSON, YAML, network, and path filters
- Create and use custom filters and plugins
- Apply data transformation techniques to practical scenarios
- Transform complex nested data structures

These skills are essential for the "Transform data with filters and plugins" objective of the EX374 exam.

## Additional Resources

- [Ansible Filter Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html)
- [Ansible Lookup Plugins Documentation](https://docs.ansible.com/ansible/latest/plugins/lookup.html)
- [Jinja2 Template Designer Documentation](https://jinja.palletsprojects.com/en/3.0.x/templates/)
- [Ansible Plugin Development Documentation](https://docs.ansible.com/ansible/latest/dev_guide/developing_plugins.html)
