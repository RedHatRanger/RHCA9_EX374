# Lab: Inventory and Credential Management

This lab covers the "Manage inventories and credentials" objective of the EX374 exam. You'll learn how to create and manage inventories and credentials in Ansible Automation Platform.

## Prerequisites

- Ansible Automation Platform 2.4 installed according to the [AAP Installation Guide](../guides/aap_installation_guide.md)
- Completion of the previous labs

## Lab Environment Setup

1. **Create a working directory**
   ```bash
   mkdir -p ~/ansible-labs/inventory-credential-lab
   cd ~/ansible-labs/inventory-credential-lab
   ```

2. **Initialize a Git repository**
   ```bash
   git init
   ```

## Exercise 1: Understanding Inventories in Automation Platform

In this exercise, you'll learn about inventory concepts in Ansible Automation Platform.

1. **Create a document explaining inventory concepts**
   ```bash
   cat > inventory_concepts.md << 'EOF'
   # Inventory Concepts in Ansible Automation Platform

   ## What is an Inventory?

   An inventory is a collection of hosts against which jobs can be launched. Inventories are divided into groups and can be sourced from a variety of providers.

   ## Inventory Types

   Ansible Automation Platform supports several inventory types:

   1. **Static Inventory**: Manually defined hosts and groups
   2. **Dynamic Inventory**: Hosts and groups generated from external sources
   3. **Smart Inventory**: Filtered subset of hosts from other inventories
   4. **Constructed Inventory**: Inventory with groups created based on host facts or variables

   ## Inventory Components

   - **Hosts**: Individual machines to be managed
   - **Groups**: Collections of hosts
   - **Variables**: Data associated with hosts or groups
   - **Inventory Sources**: External sources for dynamic inventories
   - **Inventory Scripts**: Custom scripts for generating dynamic inventories

   ## Inventory Structure

   A basic inventory structure looks like:

   ```
   inventory/
   ├── hosts
   ├── group_vars/
   │   ├── all.yml
   │   ├── webservers.yml
   │   └── dbservers.yml
   └── host_vars/
       ├── web1.yml
       └── db1.yml
   ```

   ## Inventory Variables

   Variables can be defined at multiple levels:

   1. **Host Variables**: Apply to specific hosts
   2. **Group Variables**: Apply to all hosts in a group
   3. **Group of Groups Variables**: Apply to all hosts in multiple groups
   4. **All Group Variables**: Apply to all hosts in the inventory

   ## Inventory Plugins

   Ansible uses inventory plugins to parse inventory sources:

   - **INI**: Traditional Ansible inventory format
   - **YAML**: YAML-based inventory format
   - **Dynamic**: Various dynamic inventory plugins (AWS, Azure, VMware, etc.)
   - **Constructed**: Creates groups based on patterns

   ## Inventory in Automation Controller

   In Automation Controller, inventories have additional features:

   - **Inventory Sources**: External sources for dynamic inventories
   - **Inventory Sync**: Scheduled synchronization with external sources
   - **Inventory Export**: Export inventory data
   - **Host Metrics**: Track host usage and status
   - **Host Insights**: Integration with Red Hat Insights
   EOF
   ```

2. **Commit your changes**
   ```bash
   git add inventory_concepts.md
   git commit -m "Add inventory concepts document"
   ```

## Exercise 2: Creating Static Inventories

In this exercise, you'll create and work with static inventories.

1. **Create a basic static inventory**
   ```bash
   mkdir -p inventories/static
   
   cat > inventories/static/hosts << 'EOF'
   [webservers]
   web1.example.com ansible_host=192.168.1.101
   web2.example.com ansible_host=192.168.1.102
   
   [dbservers]
   db1.example.com ansible_host=192.168.1.201
   db2.example.com ansible_host=192.168.1.202
   
   [loadbalancers]
   lb1.example.com ansible_host=192.168.1.251
   
   [webservers:vars]
   http_port=80
   https_port=443
   
   [dbservers:vars]
   db_port=5432
   
   [all:children]
   webservers
   dbservers
   loadbalancers
   
   [all:vars]
   ansible_user=ansible
   ansible_ssh_private_key_file=/home/rhel/.ssh/id_rsa
   EOF
   ```

2. **Create group variables**
   ```bash
   mkdir -p inventories/static/group_vars/{all,webservers,dbservers,loadbalancers}
   
   cat > inventories/static/group_vars/all/main.yml << 'EOF'
   ---
   # Variables for all hosts
   ansible_user: rhel
   ansible_ssh_private_key_file: /home/rhel/.ssh/id_rsa
   timezone: UTC
   ntp_servers:
     - 0.pool.ntp.org
     - 1.pool.ntp.org
   dns_servers:
     - 8.8.8.8
     - 8.8.4.4
   EOF
   
   cat > inventories/static/group_vars/webservers/main.yml << 'EOF'
   ---
   # Variables for webservers
   http_port: 80
   https_port: 443
   web_server: nginx
   document_root: /var/www/html
   max_clients: 200
   EOF
   
   cat > inventories/static/group_vars/dbservers/main.yml << 'EOF'
   ---
   # Variables for dbservers
   db_port: 5432
   db_name: app_db
   db_user: app_user
   db_password: "{{ vault_db_password }}"
   max_connections: 100
   EOF
   
   cat > inventories/static/group_vars/dbservers/vault.yml << 'EOF'
   ---
   # Encrypted variables for dbservers
   vault_db_password: redhat
   EOF
   
   cat > inventories/static/group_vars/loadbalancers/main.yml << 'EOF'
   ---
   # Variables for loadbalancers
   lb_method: round_robin
   lb_port: 80
   lb_ssl_port: 443
   health_check_interval: 30
   EOF
   ```

3. **Create host variables**
   ```bash
   mkdir -p inventories/static/host_vars/{web1.example.com,web2.example.com,db1.example.com,db2.example.com,lb1.example.com}
   
   cat > inventories/static/host_vars/web1.example.com/main.yml << 'EOF'
   ---
   # Variables for web1.example.com
   ansible_host: 192.168.1.101
   server_id: 1
   server_role: primary
   http_port: 8080  # Override group variable
   EOF
   
   cat > inventories/static/host_vars/web2.example.com/main.yml << 'EOF'
   ---
   # Variables for web2.example.com
   ansible_host: 192.168.1.102
   server_id: 2
   server_role: secondary
   EOF
   
   cat > inventories/static/host_vars/db1.example.com/main.yml << 'EOF'
   ---
   # Variables for db1.example.com
   ansible_host: 192.168.1.201
   server_id: 3
   server_role: primary
   backup_schedule: "0 2 * * *"
   EOF
   
   cat > inventories/static/host_vars/db2.example.com/main.yml << 'EOF'
   ---
   # Variables for db2.example.com
   ansible_host: 192.168.1.202
   server_id: 4
   server_role: replica
   replication_source: db1.example.com
   EOF
   
   cat > inventories/static/host_vars/lb1.example.com/main.yml << 'EOF'
   ---
   # Variables for lb1.example.com
   ansible_host: 192.168.1.251
   server_id: 5
   server_role: loadbalancer
   backend_servers:
     - web1.example.com
     - web2.example.com
   EOF
   ```

4. **Create a playbook to test the static inventory**
   ```bash
   cat > test_static_inventory.yml << 'EOF'
   ---
   - name: Test Static Inventory
     hosts: all
     gather_facts: false
     
     tasks:
       - name: Display host information
         debug:
           msg: >
             Host: {{ inventory_hostname }}
             IP: {{ ansible_host }}
             Server ID: {{ server_id }}
             Server Role: {{ server_role }}
       
       - name: Display group-specific information for webservers
         debug:
           msg: >
             Web Server: {{ web_server }}
             HTTP Port: {{ http_port }}
             HTTPS Port: {{ https_port }}
             Document Root: {{ document_root }}
         when: inventory_hostname in groups['webservers']
       
       - name: Display group-specific information for dbservers
         debug:
           msg: >
             DB Port: {{ db_port }}
             DB Name: {{ db_name }}
             DB User: {{ db_user }}
             Max Connections: {{ max_connections }}
         when: inventory_hostname in groups['dbservers']
       
       - name: Display group-specific information for loadbalancers
         debug:
           msg: >
             LB Method: {{ lb_method }}
             LB Port: {{ lb_port }}
             LB SSL Port: {{ lb_ssl_port }}
             Backend Servers: {{ backend_servers | join(', ') }}
         when: inventory_hostname in groups['loadbalancers']
   EOF
   ```

5. **Run the playbook with the static inventory**
   ```bash
   ansible-playbook -i inventories/static/hosts test_static_inventory.yml
   ```

6. **Commit your changes**
   ```bash
   git add inventories/static test_static_inventory.yml
   git commit -m "Add static inventory example"
   ```

## Exercise 3: Creating Dynamic Inventories

In this exercise, you'll create and work with dynamic inventories.

1. **Create a directory for dynamic inventories**
   ```bash
   mkdir -p inventories/dynamic
   ```

2. **Create a simple dynamic inventory script**
   ```bash
   cat > inventories/dynamic/inventory.py << 'EOF'
   #!/usr/bin/env python3
   
   import json
   import argparse
   
   def get_inventory():
       inventory = {
           "_meta": {
               "hostvars": {
                   "web1.example.com": {
                       "ansible_host": "192.168.1.101",
                       "server_id": 1,
                       "server_role": "primary",
                       "http_port": 8080
                   },
                   "web2.example.com": {
                       "ansible_host": "192.168.1.102",
                       "server_id": 2,
                       "server_role": "secondary",
                       "http_port": 80
                   },
                   "db1.example.com": {
                       "ansible_host": "192.168.1.201",
                       "server_id": 3,
                       "server_role": "primary",
                       "db_port": 5432
                   },
                   "db2.example.com": {
                       "ansible_host": "192.168.1.202",
                       "server_id": 4,
                       "server_role": "replica",
                       "db_port": 5432
                   },
                   "lb1.example.com": {
                       "ansible_host": "192.168.1.251",
                       "server_id": 5,
                       "server_role": "loadbalancer",
                       "lb_port": 80
                   }
               }
           },
           "all": {
               "children": ["webservers", "dbservers", "loadbalancers"]
           },
           "webservers": {
               "hosts": ["web1.example.com", "web2.example.com"],
               "vars": {
                   "web_server": "nginx",
                   "document_root": "/var/www/html"
               }
           },
           "dbservers": {
               "hosts": ["db1.example.com", "db2.example.com"],
               "vars": {
                   "db_name": "app_db",
                   "db_user": "app_user"
               }
           },
           "loadbalancers": {
               "hosts": ["lb1.example.com"],
               "vars": {
                   "lb_method": "round_robin"
               }
           },
           "primary_servers": {
               "hosts": ["web1.example.com", "db1.example.com"]
           }
       }
       return inventory
   
   def get_host(host):
       inventory = get_inventory()
       return inventory["_meta"]["hostvars"].get(host, {})
   
   def main():
       parser = argparse.ArgumentParser(description="Dynamic Inventory Script")
       parser.add_argument("--list", action="store_true", help="List all hosts")
       parser.add_argument("--host", help="Get variables for a specific host")
       args = parser.parse_args()
   
       if args.host:
           print(json.dumps(get_host(args.host)))
       else:
           print(json.dumps(get_inventory()))
   
   if __name__ == "__main__":
       main()
   EOF
   
   chmod +x inventories/dynamic/inventory.py
   ```

3. **Create a playbook to test the dynamic inventory**
   ```bash
   cat > test_dynamic_inventory.yml << 'EOF'
   ---
   - name: Test Dynamic Inventory
     hosts: all
     gather_facts: false
     
     tasks:
       - name: Display host information
         debug:
           msg: >
             Host: {{ inventory_hostname }}
             IP: {{ ansible_host }}
             Server ID: {{ server_id }}
             Server Role: {{ server_role }}
       
       - name: Display primary servers
         debug:
           msg: "Primary servers: {{ groups['primary_servers'] | join(', ') }}"
         run_once: true
   EOF
   ```

4. **Run the playbook with the dynamic inventory**
   ```bash
   ansible-playbook -i inventories/dynamic/inventory.py test_dynamic_inventory.yml
   ```

5. **Create an inventory plugin configuration**
   ```bash
   mkdir -p inventories/plugin
   
   cat > inventories/plugin/aws_ec2.yml << 'EOF'
   # Example AWS EC2 inventory plugin configuration
   plugin: aws_ec2
   
   # AWS credentials
   aws_access_key: AKIAIOSFODNN7EXAMPLE
   aws_secret_key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   
   # Regions to include
   regions:
     - us-east-1
     - us-west-1
   
   # Filters
   filters:
     tag:Environment: production
   
   # Group by tags and other properties
   keyed_groups:
     - key: tags.Role
       prefix: role
     - key: placement.region
       prefix: aws_region
     - key: instance_type
       prefix: instance_type
   
   # Host variables from instance properties
   hostnames:
     - tag:Name
     - dns-name
     - private-dns-name
   
   # Compose variables
   compose:
     ansible_host: public_ip_address
   EOF
   
   cat > inventories/plugin/azure_rm.yml << 'EOF'
   # Example Azure RM inventory plugin configuration
   plugin: azure_rm
   
   # Azure credentials
   auth_source: auto
   
   # Include specific resource groups
   include_vm_resource_groups:
     - production-rg
     - staging-rg
   
   # Group by tags and other properties
   keyed_groups:
     - prefix: tag
       key: tags
     - prefix: azure_loc
       key: location
   
   # Host variables from VM properties
   hostnames:
     - name
     - private_ip_address
     - public_ip_address
   
   # Compose variables
   compose:
     ansible_host: private_ip_address
   EOF
   ```

6. **Create a document explaining dynamic inventory sources**
   ```bash
   cat > dynamic_inventory_sources.md << 'EOF'
   # Dynamic Inventory Sources

   ## Overview

   Dynamic inventories allow Ansible to pull inventory data from external sources. This is useful for cloud environments, virtualization platforms, and other dynamic infrastructure.

   ## Common Dynamic Inventory Sources

   ### Cloud Providers

   - **AWS EC2**: Amazon Web Services EC2 instances
   - **Azure RM**: Microsoft Azure Resource Manager
   - **GCP**: Google Cloud Platform
   - **OpenStack**: OpenStack instances
   - **DigitalOcean**: DigitalOcean droplets

   ### Virtualization Platforms

   - **VMware**: VMware vSphere/vCenter
   - **oVirt/RHV**: Red Hat Virtualization
   - **Proxmox**: Proxmox Virtual Environment
   - **Libvirt**: Libvirt-based virtualization

   ### Infrastructure Management

   - **Foreman**: Foreman/Katello
   - **Zabbix**: Zabbix monitoring
   - **ServiceNow**: ServiceNow CMDB
   - **NetBox**: NetBox IPAM and DCIM

   ### Containers and Orchestration

   - **Kubernetes**: Kubernetes clusters
   - **Docker**: Docker containers
   - **Podman**: Podman containers
   - **OpenShift**: OpenShift clusters

   ## Inventory Plugins vs. Scripts

   Ansible supports two methods for dynamic inventories:

   1. **Inventory Plugins**: Native Ansible plugins (preferred method)
   2. **Inventory Scripts**: External scripts that output JSON

   Inventory plugins are preferred because they:
   - Integrate better with Ansible
   - Support caching
   - Can use Ansible variables and configuration
   - Are easier to maintain

   ## Using Inventory Plugins

   To use an inventory plugin:

   1. Create a YAML file with the plugin configuration
   2. Use the file as the inventory source

   Example:
   ```bash
   ansible-playbook -i inventory_plugin.yml playbook.yml
   ```

   ## Using Inventory Scripts

   To use an inventory script:

   1. Create a script that outputs JSON in the expected format
   2. Make the script executable
   3. Use the script as the inventory source

   Example:
   ```bash
   ansible-playbook -i inventory_script.py playbook.yml
   ```

   ## Inventory Caching

   For performance, Ansible can cache inventory data:

   ```ini
   # ansible.cfg
   [inventory]
   cache = yes
   cache_plugin = jsonfile
   cache_timeout = 3600
   cache_connection = /tmp/ansible_inventory_cache
   ```

   ## Best Practices

   - Use inventory plugins instead of scripts when possible
   - Enable caching for better performance
   - Test inventory sources regularly
   - Document inventory source configurations
   - Use version control for inventory configurations
   - Implement proper error handling in custom inventory scripts
   EOF
   ```

7. **Commit your changes**
   ```bash
   git add inventories/dynamic inventories/plugin test_dynamic_inventory.yml dynamic_inventory_sources.md
   git commit -m "Add dynamic inventory examples"
   ```

## Exercise 4: Understanding Credentials in Automation Platform

In this exercise, you'll learn about credential concepts in Ansible Automation Platform.

1. **Create a document explaining credential concepts**
   ```bash
   cat > credential_concepts.md << 'EOF'
   # Credential Concepts in Ansible Automation Platform

   ## What are Credentials?

   Credentials are a way to store and manage authentication information in Ansible Automation Platform. They allow Ansible to securely access remote systems, cloud providers, and other services.

   ## Credential Types

   Ansible Automation Platform supports several credential types:

   1. **Machine**: SSH/WinRM credentials for connecting to hosts
   2. **Source Control**: Credentials for accessing source control repositories
   3. **Cloud**: Credentials for cloud providers (AWS, Azure, GCP, etc.)
   4. **Network**: Credentials for network devices
   5. **Vault**: Credentials for Ansible Vault
   6. **Registry**: Credentials for container registries
   7. **Custom**: User-defined credential types

   ## Credential Components

   Depending on the credential type, credentials may include:

   - **Username**: User account name
   - **Password**: User account password
   - **SSH Private Key**: Private key for SSH authentication
   - **API Tokens**: Authentication tokens for APIs
   - **Certificates**: SSL/TLS certificates
   - **Secret Keys**: Secret keys for cloud providers
   - **Custom Fields**: User-defined fields for custom credential types

   ## Credential Security

   Credentials in Automation Platform are secured through:

   - **Encryption**: Sensitive fields are encrypted at rest
   - **Access Control**: Role-based access control for credentials
   - **Isolation**: Credentials are only exposed to the jobs that need them
   - **Auditing**: Credential usage is logged and auditable

   ## Credential Usage

   Credentials can be used in:

   - **Job Templates**: For connecting to inventory hosts
   - **Projects**: For accessing source control repositories
   - **Inventory Sources**: For accessing dynamic inventory sources
   - **Workflow Templates**: For complex automation workflows
   - **Execution Environments**: For accessing container registries

   ## Credential Precedence

   When multiple credentials are available, the precedence is:

   1. **Job Template Credentials**: Highest precedence
   2. **Inventory Credentials**: Applied to specific inventories
   3. **Organization Credentials**: Applied to all resources in an organization
   4. **Global Credentials**: Lowest precedence

   ## Credential Types vs. Credential Instances

   - **Credential Types**: Define the structure and fields for credentials
   - **Credential Instances**: Actual credentials created from credential types

   ## Custom Credential Types

   Custom credential types allow you to define:

   - **Input Fields**: Fields for entering credential data
   - **Injectors**: How credential data is injected into jobs
   EOF
   ```

2. **Commit your changes**
   ```bash
   git add credential_concepts.md
   git commit -m "Add credential concepts document"
   ```

## Exercise 5: Managing Credentials

In this exercise, you'll learn how to create and manage different types of credentials.

1. **Create examples of different credential types**
   ```bash
   mkdir -p credentials/{machine,scm,cloud,network,vault,registry,custom}
   
   cat > credentials/machine/ssh_key.yml << 'EOF'
   ---
   # Example SSH key credential
   name: Production SSH Key
   description: SSH key for production servers
   credential_type: Machine
   inputs:
     username: ansible
     ssh_key_data: |
       -----BEGIN OPENSSH PRIVATE KEY-----
       b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
       NhAAAAAwEAAQAAAYEAtlOPUiLQGBTgXK8fQKvq6vWUFXTzj3NOKZCj3LRnEt6iiXqIy7Vy
       ... (truncated for brevity) ...
       YZJdUUhx2ONwJZ6HkQQeyWyQQwAAABFhbnNpYmxlQGV4YW1wbGUuY29tAQID
       -----END OPENSSH PRIVATE KEY-----
     ssh_key_unlock: redhat
     become_method: sudo
     become_username: root
     become_password: redhat
   EOF
   
   cat > credentials/machine/username_password.yml << 'EOF'
   ---
   # Example username/password credential
   name: Windows Admin
   description: Windows administrator credentials
   credential_type: Machine
   inputs:
     username: administrator
     password: redhat
     become_method: runas
     become_username: administrator
     become_password: redhat
   EOF
   
   cat > credentials/scm/git.yml << 'EOF'
   ---
   # Example SCM credential
   name: GitHub Access
   description: GitHub access for Ansible projects
   credential_type: Source Control
   inputs:
     username: git_user
     password: redhat
     ssh_key_data: |
       -----BEGIN OPENSSH PRIVATE KEY-----
       b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
       NhAAAAAwEAAQAAAYEAtlOPUiLQGBTgXK8fQKvq6vWUFXTzj3NOKZCj3LRnEt6iiXqIy7Vy
       ... (truncated for brevity) ...
       YZJdUUhx2ONwJZ6HkQQeyWyQQwAAABFhbnNpYmxlQGV4YW1wbGUuY29tAQID
       -----END OPENSSH PRIVATE KEY-----
   EOF
   
   cat > credentials/cloud/aws.yml << 'EOF'
   ---
   # Example AWS credential
   name: AWS Access
   description: AWS access for cloud automation
   credential_type: Amazon Web Services
   inputs:
     username: AKIAIOSFODNN7EXAMPLE
     password: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
     security_token: AQoEXAMPLEH4aoAH0gNCAPyJxz4BlCFFxWNE1OPTgk5TthT+FvwqnKwRcOIfrRh3c/LTo6UDdyJwOOvEVPvLXCrrrUtdnniCEXAMPLE/IvU1dYUg2RVAJBanLiHb4IgRmpRV3zrkuWJOgQs8IZZaIv2BXIa2R4OlgkBN9bkUDNCJiBeb/AXlzBBko7b15fjrBs2+cTQtpZ3CYWFXG8C5zqx37wnOE49mRl/+OtkIKGO7fAE
   EOF
   
   cat > credentials/cloud/azure.yml << 'EOF'
   ---
   # Example Azure credential
   name: Azure Access
   description: Azure access for cloud automation
   credential_type: Microsoft Azure Resource Manager
   inputs:
     subscription: 12345678-1234-1234-1234-123456789012
     client: abcdef01-abcd-abcd-abcd-abcdef0123456
     secret: P@ssw0rd123!
     tenant: 98765432-9876-9876-9876-987654321098
   EOF
   
   cat > credentials/network/network_device.yml << 'EOF'
   ---
   # Example network device credential
   name: Network Admin
   description: Network device administrator credentials
   credential_type: Network
   inputs:
     username: netadmin
     password: redhat
     authorize: true
     authorize_password: redhat
   EOF
   
   cat > credentials/vault/ansible_vault.yml << 'EOF'
   ---
   # Example Ansible Vault credential
   name: Vault Password
   description: Ansible Vault password for encrypted files
   credential_type: Vault
   inputs:
     vault_password: redhat
     vault_id: production
   EOF
   
   cat > credentials/registry/container_registry.yml << 'EOF'
   ---
   # Example container registry credential
   name: Docker Hub
   description: Docker Hub access for container images
   credential_type: Container Registry
   inputs:
     username: docker_user
     password: redhat
     host: https://index.docker.io/v1/
   EOF
   ```

2. **Create an example of a custom credential type**
   ```bash
   cat > credentials/custom/custom_credential_type.yml << 'EOF'
   ---
   # Example custom credential type
   name: API Token
   description: API token for external service
   input_schema:
     fields:
       - id: api_endpoint
         type: string
         label: API Endpoint
         required: true
       - id: api_token
         type: string
         label: API Token
         required: true
         secret: true
       - id: api_version
         type: string
         label: API Version
         required: false
         default: "v1"
   injector_schema:
     env:
       API_ENDPOINT: "{{ api_endpoint }}"
       API_TOKEN: "{{ api_token }}"
       API_VERSION: "{{ api_version }}"
     extra_vars:
       api_endpoint: "{{ api_endpoint }}"
       api_token: "{{ api_token }}"
       api_version: "{{ api_version }}"
   EOF
   
   cat > credentials/custom/api_token.yml << 'EOF'
   ---
   # Example custom credential instance
   name: Service API Token
   description: API token for external service
   credential_type: API Token
   inputs:
     api_endpoint: https://api.example.com
     api_token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
     api_version: v2
   EOF
   ```

3. **Create a document explaining credential management in Automation Controller**
   ```bash
   cat > credential_management.md << 'EOF'
   # Credential Management in Automation Controller

   ## Creating Credentials

   To create a credential in Automation Controller:

   1. **Navigate to Resources > Credentials**
   2. **Click "Add"**
   3. **Select the credential type**
   4. **Fill in the required fields**
   5. **Click "Save"**

   ## Credential Organization

   Credentials can be organized by:

   - **Organization**: Assign credentials to specific organizations
   - **Teams**: Grant teams access to credentials
   - **Users**: Grant users access to credentials

   ## Credential Permissions

   Permissions that can be granted for credentials:

   - **Use**: Ability to use the credential in jobs
   - **Read**: Ability to view the credential (but not see sensitive fields)
   - **Write**: Ability to modify the credential
   - **Admin**: Full control over the credential

   ## Using Credentials in Job Templates

   To use credentials in a job template:

   1. **Navigate to Resources > Templates**
   2. **Create or edit a job template**
   3. **In the "Credentials" section, select the appropriate credentials**
   4. **Click "Save"**

   ## Credential Lookup

   When a job runs, Automation Controller looks for credentials in this order:

   1. **Job Template Credentials**: Credentials explicitly assigned to the job template
   2. **Inventory Credentials**: Credentials assigned to the inventory
   3. **Project Credentials**: Credentials assigned to the project

   ## Credential Types

   To create a custom credential type:

   1. **Navigate to Administration > Credential Types**
   2. **Click "Add"**
   3. **Define the input schema (fields for the credential)**
   4. **Define the injector schema (how the credential is injected into jobs)**
   5. **Click "Save"**

   ## Credential Encryption

   Sensitive credential fields are encrypted using:

   - **AES-256**: For encryption at rest
   - **HTTPS/TLS**: For encryption in transit

   ## Credential Rotation

   Best practices for credential rotation:

   - **Regular Rotation**: Rotate credentials on a regular schedule
   - **Automation**: Automate credential rotation when possible
   - **Monitoring**: Monitor credential usage and detect anomalies
   - **Documentation**: Document credential rotation procedures
   EOF
   ```

4. **Create a playbook that uses credentials**
   ```bash
   cat > use_credentials.yml << 'EOF'
   ---
   - name: Demonstrate Credential Usage
     hosts: all
     gather_facts: false
     
     vars:
       # These would normally be provided by credentials
       aws_access_key: "{{ lookup('env', 'AWS_ACCESS_KEY') | default('AKIAIOSFODNN7EXAMPLE') }}"
       aws_secret_key: "{{ lookup('env', 'AWS_SECRET_KEY') | default('wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY') }}"
       api_token: "{{ lookup('env', 'API_TOKEN') | default('eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9') }}"
     
     tasks:
       - name: Display AWS credential usage (simulated)
         debug:
           msg: >
             Using AWS credentials:
             Access Key: {{ aws_access_key[:5] }}...
             Secret Key: {{ aws_secret_key[:5] }}...
       
       - name: Display API token usage (simulated)
         debug:
           msg: "Using API token: {{ api_token[:10] }}..."
       
       - name: Simulate using SSH key
         debug:
           msg: "Connecting to {{ inventory_hostname }} using SSH key authentication"
       
       - name: Simulate using Vault password
         debug:
           msg: "Decrypting Vault-encrypted files using Vault password"
   EOF
   ```

5. **Commit your changes**
   ```bash
   git add credentials credential_management.md use_credentials.yml
   git commit -m "Add credential management examples"
   ```

## Exercise 6: Integrating Inventories and Credentials in Automation Controller

In this exercise, you'll learn how to integrate inventories and credentials in Automation Controller.

1. **Create a document explaining inventory and credential integration**
   ```bash
   cat > inventory_credential_integration.md << 'EOF'
   # Integrating Inventories and Credentials in Automation Controller

   ## Overview

   Inventories and credentials work together in Automation Controller to provide a complete automation solution. This document explains how to integrate them effectively.

   ## Associating Credentials with Inventories

   ### Machine Credentials

   Machine credentials can be associated with inventories to provide default connection credentials:

   1. **Navigate to Resources > Inventories**
   2. **Select an inventory**
   3. **Click "Access" tab**
   4. **Click "Add"**
   5. **Select a machine credential**
   6. **Set the permission to "Use"**

   ### Source Credentials

   For dynamic inventories, source credentials are used to authenticate with the inventory source:

   1. **Navigate to Resources > Inventories**
   2. **Select an inventory**
   3. **Click "Sources" tab**
   4. **Add or edit an inventory source**
   5. **Select the appropriate credential in the "Credential" field**

   ## Credential Precedence with Inventories

   When multiple credentials are available, the precedence is:

   1. **Job Template Credentials**: Highest precedence
   2. **Inventory Credentials**: Applied to specific inventories
   3. **Organization Credentials**: Applied to all resources in an organization

   ## Using Inventories and Credentials in Job Templates

   To use inventories and credentials in a job template:

   1. **Navigate to Resources > Templates**
   2. **Create or edit a job template**
   3. **Select the inventory in the "Inventory" field**
   4. **Select credentials in the "Credentials" section**
   5. **Click "Save"**

   ## Smart Inventories with Credential Filtering

   Smart inventories can be created based on credential usage:

   1. **Navigate to Resources > Inventories**
   2. **Click "Add" > "Smart Inventory"**
   3. **Set the filter to include hosts with specific credential requirements**
   4. **Click "Save"**

   ## Inventory and Credential Synchronization

   For dynamic inventories, synchronization is important:

   1. **Navigate to Resources > Inventories**
   2. **Select an inventory**
   3. **Click "Sources" tab**
   4. **Select an inventory source**
   5. **Click "Sync"**

   Synchronization can be scheduled:

   1. **Edit the inventory source**
   2. **Set the "Update Options"**
   3. **Configure the schedule**
   4. **Click "Save"**

   ## Inventory and Credential Exports

   Inventories and credentials can be exported for backup or migration:

   1. **Use the Automation Controller API to export inventories and credentials**
   2. **Use the `awx` command-line tool to export resources**
   3. **Use the Automation Controller UI to export job templates that reference inventories and credentials**

   ## Best Practices

   - **Use credential types appropriate for inventory sources**
   - **Organize inventories and credentials by environment or purpose**
   - **Use descriptive names for inventories and credentials**
   - **Document inventory and credential relationships**
   - **Regularly test inventory and credential access**
   - **Implement proper access controls for inventories and credentials**
   - **Use credential rotation for sensitive credentials**
   EOF
   ```

2. **Create a sample Automation Controller configuration**
   ```bash
   mkdir -p controller_config
   
   cat > controller_config/inventory.json << 'EOF'
   {
     "name": "Production Servers",
     "description": "Production environment servers",
     "organization": 1,
     "variables": {
       "ansible_connection": "ssh",
       "ansible_user": "ansible",
       "environment": "production"
     },
     "hosts": [
       {
         "name": "web1.example.com",
         "description": "Production web server 1",
         "variables": {
           "ansible_host": "192.168.1.101",
           "server_role": "web"
         }
       },
       {
         "name": "web2.example.com",
         "description": "Production web server 2",
         "variables": {
           "ansible_host": "192.168.1.102",
           "server_role": "web"
         }
       },
       {
         "name": "db1.example.com",
         "description": "Production database server",
         "variables": {
           "ansible_host": "192.168.1.201",
           "server_role": "db"
         }
       }
     ],
     "groups": [
       {
         "name": "webservers",
         "description": "Web servers group",
         "variables": {
           "http_port": 80,
           "https_port": 443
         },
         "hosts": ["web1.example.com", "web2.example.com"]
       },
       {
         "name": "dbservers",
         "description": "Database servers group",
         "variables": {
           "db_port": 5432
         },
         "hosts": ["db1.example.com"]
       }
     ],
     "inventory_sources": [
       {
         "name": "AWS EC2",
         "description": "AWS EC2 instances",
         "source": "ec2",
         "credential": 3,
         "source_vars": {
           "regions": ["us-east-1", "us-west-1"],
           "filters": {
             "tag:Environment": "production"
           }
         },
         "update_on_launch": true,
         "update_cache_timeout": 1800,
         "overwrite": false,
         "overwrite_vars": false
       }
     ]
   }
   EOF
   
   cat > controller_config/credential.json << 'EOF'
   {
     "name": "Production SSH Key",
     "description": "SSH key for production servers",
     "organization": 1,
     "credential_type": 1,
     "inputs": {
       "username": "ansible",
       "ssh_key_data": "-----BEGIN OPENSSH PRIVATE KEY-----\nb3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlw...\n-----END OPENSSH PRIVATE KEY-----",
       "become_method": "sudo",
       "become_username": "root"
     }
   }
   EOF
   
   cat > controller_config/job_template.json << 'EOF'
   {
     "name": "Deploy Application",
     "description": "Deploy application to production servers",
     "job_type": "run",
     "inventory": 1,
     "project": 1,
     "playbook": "deploy.yml",
     "credentials": [1, 3],
     "extra_vars": {
       "app_version": "1.2.3",
       "deploy_environment": "production"
     },
     "ask_variables_on_launch": true,
     "ask_credentials_on_launch": false,
     "ask_inventory_on_launch": false
   }
   EOF
   
   cat > controller_config/README.md << 'EOF'
   # Automation Controller Configuration

   This directory contains sample Automation Controller configuration files for inventories, credentials, and job templates.

   ## Files

   - `inventory.json`: Sample inventory configuration
   - `credential.json`: Sample credential configuration
   - `job_template.json`: Sample job template configuration

   ## Usage

   These files can be used with the Automation Controller API or the `awx` command-line tool to configure Automation Controller.

   ### Using the API

   ```bash
   # Create an inventory
   curl -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d @inventory.json https://controller.example.com/api/v2/inventories/

   # Create a credential
   curl -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d @credential.json https://controller.example.com/api/v2/credentials/

   # Create a job template
   curl -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d @job_template.json https://controller.example.com/api/v2/job_templates/
   ```

   ### Using the awx Command-Line Tool

   ```bash
   # Create an inventory
   awx inventories create --json @inventory.json

   # Create a credential
   awx credentials create --json @credential.json

   # Create a job template
   awx job_templates create --json @job_template.json
   ```
   EOF
   ```

3. **Commit your changes**
   ```bash
   git add inventory_credential_integration.md controller_config
   git commit -m "Add inventory and credential integration examples"
   ```

## Conclusion

In this lab, you've learned how to:
- Understand inventory concepts in Ansible Automation Platform
- Create and work with static inventories
- Create and work with dynamic inventories
- Understand credential concepts in Ansible Automation Platform
- Create and manage different types of credentials
- Integrate inventories and credentials in Automation Controller

These skills are essential for the "Manage inventories and credentials" objective of the EX374 exam.

## Additional Resources

- [Ansible Inventory Documentation](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)
- [Ansible Dynamic Inventory Documentation](https://docs.ansible.com/ansible/latest/user_guide/intro_dynamic_inventory.html)
- [Ansible Inventory Plugins Documentation](https://docs.ansible.com/ansible/latest/plugins/inventory.html)
- [Ansible Vault Documentation](https://docs.ansible.com/ansible/latest/user_guide/vault.html)
- [Automation Controller User Guide - Inventories](https://docs.ansible.com/automation-controller/latest/html/userguide/inventories.html)
- [Automation Controller User Guide - Credentials](https://docs.ansible.com/automation-controller/latest/html/userguide/credentials.html)
