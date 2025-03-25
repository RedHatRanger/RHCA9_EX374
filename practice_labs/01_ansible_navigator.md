# Lab 1: Ansible Navigator and Execution Environments

This lab covers the use of ansible-navigator, a text-based user interface (TUI) that helps you create, manage, and use Ansible execution environments. Ansible Navigator is a crucial tool for modern Ansible development and is an essential component of the EX374 exam.

## Prerequisites

- Ansible Automation Platform 2.4 installed according to the [AAP Installation Guide](../guides/aap_installation_guide.md)
- Basic understanding of Ansible concepts
- Access to a terminal or command line interface

## Lab Objectives

By the end of this lab, you will be able to:

1. Understand execution environments and their real-world applications
2. Install and configure ansible-navigator
3. Navigate the ansible-navigator interface
4. Run playbooks using ansible-navigator
5. Work with execution environments
6. Customize ansible-navigator settings
7. Integrate ansible-navigator with Ansible Automation Platform

## Part 1: Understanding Execution Environments

### What are Execution Environments?

Execution Environments (EEs) are containerized environments that include Ansible Core, collections, dependencies, and the necessary system-level packages to run Ansible. They provide a consistent and isolated environment for Ansible automation.

### Why Execution Environments Matter: Real-World Examples

#### Example 1: The Dependency Nightmare

**Traditional Ansible Setup:**
Imagine a large enterprise with multiple teams working on different Ansible projects:

- Team A needs `ansible-core 2.12` with `pywinrm 0.4.1` for Windows automation
- Team B needs `ansible-core 2.13` with `boto3 1.24.0` for AWS automation
- Team C needs `ansible-core 2.11` with `openshift 0.13.1` for Kubernetes automation

Without execution environments, these teams would face conflicts when trying to use the same control node, leading to:
- Python dependency conflicts
- Incompatible Ansible versions
- Inconsistent behavior across environments
- "Works on my machine" syndrome

**Solution with Execution Environments:**
Each team creates their own execution environment with their specific dependencies:
```yaml
# Team A's execution environment definition
version: 1
build_arg_defaults:
  EE_BASE_IMAGE: registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest
dependencies:
  python:
    - pywinrm==0.4.1
  galaxy:
    - community.windows:==1.12.0
```

Now teams can work independently without conflicts, and automation runs consistently regardless of the control node's configuration.

#### Example 2: Production vs. Development Consistency

**Problem:**
A company has a complex CI/CD pipeline where Ansible playbooks:
- Are developed on developers' laptops
- Are tested in a CI environment
- Run in production on Ansible Automation Platform

Without execution environments, the same playbook might:
- Work on a developer's laptop with the latest collections
- Fail in CI due to different Python dependencies
- Behave differently in production due to different system packages

**Solution with Execution Environments:**
By defining an execution environment that contains all required dependencies:
```yaml
version: 1
build_arg_defaults:
  EE_BASE_IMAGE: registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest
dependencies:
  galaxy:
    - community.general:==6.2.0
    - ansible.posix:==1.5.1
  python:
    - boto3==1.24.0
    - requests==2.28.1
  system:
    - gcc [platform:rpm]
    - python3-devel [platform:rpm]
```

The playbook runs in the exact same environment at every stage, ensuring consistent behavior.

#### Example 3: Specialized Requirements for Network Automation

**Problem:**
Network automation often requires specialized libraries and tools that might conflict with other automation tasks:
- Network automation needs `napalm`, `netmiko`, and other specialized libraries
- These libraries have specific version requirements that might conflict with other Python packages
- Network modules might need specific system-level packages

**Solution with Execution Environments:**
Create a specialized network automation execution environment:
```yaml
version: 1
build_arg_defaults:
  EE_BASE_IMAGE: registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest
dependencies:
  galaxy:
    - ansible.netcommon:==4.1.0
    - cisco.ios:==4.3.0
  python:
    - napalm==4.0.0
    - netmiko==4.1.2
```

This allows network engineers to use specialized tools without affecting other automation tasks.

### How Execution Environments Work

Execution environments are built using `ansible-builder`, which creates a container image with:

1. A base image (usually minimal RHEL)
2. Ansible Core
3. Python dependencies
4. System-level packages
5. Ansible collections

When you run a playbook with ansible-navigator, it:
1. Launches the execution environment container
2. Mounts your playbook and inventory inside the container
3. Runs Ansible within the container
4. Returns the results to your terminal

This ensures that your automation runs in a consistent environment regardless of the host system's configuration.

## Part 2: Installing ansible-navigator

1. **Install ansible-navigator using pip**

   ```bash
   sudo pip3 install ansible-navigator
   ```

2. **Verify the installation**

   ```bash
   ansible-navigator --version
   ```

   You should see output showing the installed version of ansible-navigator.

3. **Create a basic configuration file**

   Create a file named `ansible-navigator.yml` in your home directory:

   ```bash
   cat > ~/ansible-navigator.yml << 'EOF'
   ---
   ansible-navigator:
     execution-environment:
       enabled: true
       image: registry.redhat.io/ansible-automation-platform-24/ee-supported-rhel8:latest
     logging:
       level: debug
     playbook-artifact:
       enable: true
       save-as: "{playbook_dir}/{playbook_name}-artifact-{time_stamp}.json"
   EOF
   ```

## Part 3: Exploring the ansible-navigator Interface

1. **Launch ansible-navigator in interactive mode**

   ```bash
   ansible-navigator
   ```

   This opens the ansible-navigator TUI (Text User Interface).

2. **Explore available subcommands**

   Press `:` (colon) to access the command palette, then type `help` and press Enter.
   
   This displays a list of available subcommands:
   
   - `collections` - Explore available collections
   - `config` - Explore the current Ansible configuration
   - `doc` - View documentation for modules, plugins, etc.
   - `inventory` - Explore inventory
   - `images` - Explore execution environment images
   - `replay` - Replay a playbook artifact
   - `run` - Run a playbook
   - `welcome` - Display the welcome page

3. **Exit ansible-navigator**

   Press `:q` or `:quit` to exit ansible-navigator.

## Part 4: Running Playbooks with ansible-navigator

1. **Create a simple playbook**

   ```bash
   mkdir -p ~/navigator-lab/playbooks
   cat > ~/navigator-lab/playbooks/hello.yml << 'EOF'
   ---
   - name: Hello World Playbook
     hosts: localhost
     gather_facts: false
     tasks:
       - name: Print hello message
         debug:
           msg: "Hello from ansible-navigator!"
   EOF
   ```

2. **Run the playbook with ansible-navigator**

   ```bash
   cd ~/navigator-lab
   ansible-navigator run playbooks/hello.yml
   ```

3. **Explore the playbook output**

   - Use arrow keys to navigate through the output
   - Press `0` to see the standard output view
   - Press `1` to see the JSON output view
   - Press `2` to see the YAML output view
   - Press `f` to toggle full-screen mode
   - Press `Esc` to go back to the previous menu

4. **Run the playbook with different settings**

   ```bash
   ansible-navigator run playbooks/hello.yml --mode stdout
   ```

   This runs the playbook with standard output mode instead of the TUI.

## Part 5: Working with Execution Environments

1. **List available execution environment images**

   ```bash
   ansible-navigator images
   ```

2. **Pull a different execution environment**

   ```bash
   ansible-navigator images pull registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest
   ```

3. **Run a playbook with a specific execution environment**

   ```bash
   ansible-navigator run playbooks/hello.yml --eei registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest
   ```

4. **Create a custom execution environment definition**

   ```bash
   mkdir -p ~/navigator-lab/execution-environments
   cat > ~/navigator-lab/execution-environments/custom-ee.yml << 'EOF'
   ---
   version: 1
   build_arg_defaults:
     EE_BASE_IMAGE: registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest
   
   ansible_config: ansible.cfg
   
   dependencies:
     galaxy: requirements.yml
     python: requirements.txt
     system: bindep.txt
   
   additional_build_steps:
     prepend: |
       RUN pip3 install --upgrade pip setuptools
     append: |
       RUN echo "Custom execution environment is ready"
   EOF
   ```

5. **Create dependency files**

   ```bash
   cat > ~/navigator-lab/execution-environments/requirements.yml << 'EOF'
   ---
   collections:
     - name: ansible.posix
     - name: community.general
   EOF
   
   cat > ~/navigator-lab/execution-environments/requirements.txt << 'EOF'
   PyYAML>=5.1
   jmespath
   EOF
   
   cat > ~/navigator-lab/execution-environments/bindep.txt << 'EOF'
   gcc [platform:rpm]
   python3-devel [platform:rpm]
   EOF
   
   touch ~/navigator-lab/execution-environments/ansible.cfg
   ```

6. **Build a custom execution environment**

   First, install ansible-builder:

   ```bash
   sudo pip3 install ansible-builder
   ```

   Then build the execution environment:

   ```bash
   cd ~/navigator-lab/execution-environments
   ansible-builder build -t custom-ee:latest -f custom-ee.yml
   ```

7. **Use your custom execution environment**

   ```bash
   ansible-navigator run ../playbooks/hello.yml --eei custom-ee:latest
   ```

## Part 6: Real-World Execution Environment Scenarios

### Scenario 1: Multi-Cloud Automation

Imagine you need to automate resources across AWS, Azure, and Google Cloud:

1. **Create a multi-cloud execution environment definition**

   ```bash
   mkdir -p ~/navigator-lab/multi-cloud
   cat > ~/navigator-lab/multi-cloud/ee-multi-cloud.yml << 'EOF'
   ---
   version: 1
   build_arg_defaults:
     EE_BASE_IMAGE: registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest
   
   dependencies:
     galaxy: requirements.yml
     python: requirements.txt
   EOF
   
   cat > ~/navigator-lab/multi-cloud/requirements.yml << 'EOF'
   ---
   collections:
     - name: amazon.aws
     - name: azure.azcollection
     - name: google.cloud
   EOF
   
   cat > ~/navigator-lab/multi-cloud/requirements.txt << 'EOF'
   boto3>=1.24.0
   azure-cli-core>=2.40.0
   google-auth>=2.11.0
   EOF
   ```

2. **Build the multi-cloud execution environment**

   ```bash
   cd ~/navigator-lab/multi-cloud
   ansible-builder build -t multi-cloud-ee:latest -f ee-multi-cloud.yml
   ```

3. **Create a multi-cloud playbook**

   ```bash
   cat > ~/navigator-lab/playbooks/multi-cloud.yml << 'EOF'
   ---
   - name: Multi-Cloud Example
     hosts: localhost
     gather_facts: false
     tasks:
       - name: Get AWS regions
         amazon.aws.aws_region_info:
         register: aws_regions
         ignore_errors: true
       
       - name: Display AWS regions
         debug:
           msg: "Found {{ aws_regions.regions | default([]) | length }} AWS regions"
         when: aws_regions is defined
   EOF
   ```

4. **Run the multi-cloud playbook**

   ```bash
   cd ~/navigator-lab
   ansible-navigator run playbooks/multi-cloud.yml --eei multi-cloud-ee:latest
   ```

### Scenario 2: Database Administration Across Different Database Types

1. **Create a database administration execution environment**

   ```bash
   mkdir -p ~/navigator-lab/db-admin
   cat > ~/navigator-lab/db-admin/ee-db-admin.yml << 'EOF'
   ---
   version: 1
   build_arg_defaults:
     EE_BASE_IMAGE: registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest
   
   dependencies:
     galaxy: requirements.yml
     python: requirements.txt
     system: bindep.txt
   EOF
   
   cat > ~/navigator-lab/db-admin/requirements.yml << 'EOF'
   ---
   collections:
     - name: community.mysql
     - name: community.postgresql
     - name: community.mongodb
   EOF
   
   cat > ~/navigator-lab/db-admin/requirements.txt << 'EOF'
   pymysql>=1.0.2
   psycopg2-binary>=2.9.3
   pymongo>=4.2.0
   EOF
   
   cat > ~/navigator-lab/db-admin/bindep.txt << 'EOF'
   gcc [platform:rpm]
   python3-devel [platform:rpm]
   EOF
   ```

2. **Build the database administration execution environment**

   ```bash
   cd ~/navigator-lab/db-admin
   ansible-builder build -t db-admin-ee:latest -f ee-db-admin.yml
   ```

3. **Create a database administration playbook**

   ```bash
   cat > ~/navigator-lab/playbooks/db-admin.yml << 'EOF'
   ---
   - name: Database Administration Example
     hosts: localhost
     gather_facts: false
     tasks:
       - name: Show MySQL module capabilities
         debug:
           msg: "MySQL module is available through community.mysql collection"
       
       - name: Show PostgreSQL module capabilities
         debug:
           msg: "PostgreSQL module is available through community.postgresql collection"
       
       - name: Show MongoDB module capabilities
         debug:
           msg: "MongoDB module is available through community.mongodb collection"
   EOF
   ```

4. **Run the database administration playbook**

   ```bash
   cd ~/navigator-lab
   ansible-navigator run playbooks/db-admin.yml --eei db-admin-ee:latest
   ```

### Scenario 3: Security Compliance Automation

1. **Create a security compliance execution environment**

   ```bash
   mkdir -p ~/navigator-lab/security
   cat > ~/navigator-lab/security/ee-security.yml << 'EOF'
   ---
   version: 1
   build_arg_defaults:
     EE_BASE_IMAGE: registry.redhat.io/ansible-automation-platform-24/ee-supported-rhel8:latest
   
   dependencies:
     galaxy: requirements.yml
     python: requirements.txt
   EOF
   
   cat > ~/navigator-lab/security/requirements.yml << 'EOF'
   ---
   collections:
     - name: ansible.posix
     - name: community.crypto
     - name: community.general
   EOF
   
   cat > ~/navigator-lab/security/requirements.txt << 'EOF'
   cryptography>=37.0.0
   pyOpenSSL>=22.0.0
   EOF
   ```

2. **Build the security compliance execution environment**

   ```bash
   cd ~/navigator-lab/security
   ansible-builder build -t security-ee:latest -f ee-security.yml
   ```

3. **Create a security compliance playbook**

   ```bash
   cat > ~/navigator-lab/playbooks/security.yml << 'EOF'
   ---
   - name: Security Compliance Example
     hosts: localhost
     gather_facts: false
     tasks:
       - name: Generate a self-signed OpenSSL certificate
         community.crypto.openssl_certificate:
           path: /tmp/ansible-test-cert.pem
           privatekey_path: /tmp/ansible-test-key.pem
           provider: selfsigned
           selfsigned_not_after: "+3650d"
           state: present
           force: true
         ignore_errors: true
       
       - name: Show certificate generation result
         debug:
           msg: "Certificate generation attempted"
   EOF
   ```

4. **Run the security compliance playbook**

   ```bash
   cd ~/navigator-lab
   ansible-navigator run playbooks/security.yml --eei security-ee:latest
   ```

## Part 7: Customizing ansible-navigator

1. **Create a project-specific configuration**

   ```bash
   cat > ~/navigator-lab/ansible-navigator.yml << 'EOF'
   ---
   ansible-navigator:
     ansible:
       inventory:
         entries:
           - /home/rhel/navigator-lab/inventory
     execution-environment:
       container-engine: podman
       enabled: true
       image: custom-ee:latest
       pull:
         policy: missing
     playbook-artifact:
       enable: true
       save-as: "{playbook_dir}/{playbook_name}-artifact-{time_stamp}.json"
     logging:
       level: info
     editor:
       command: vim
     mode: stdout
   EOF
   ```

2. **Create a sample inventory file**

   ```bash
   mkdir -p ~/navigator-lab/inventory
   cat > ~/navigator-lab/inventory/hosts << 'EOF'
   [webservers]
   web1.example.com
   web2.example.com
   
   [dbservers]
   db1.example.com
   
   [all:vars]
   ansible_user=rhel
   ansible_password=redhat
   EOF
   ```

3. **Explore inventory with ansible-navigator**

   ```bash
   cd ~/navigator-lab
   ansible-navigator inventory --mode interactive
   ```

   - Press `0` to see the graph view
   - Press `1` to see the host view
   - Press `Esc` to go back

## Part 8: Integrating with Ansible Automation Platform

1. **Create a playbook that uses collections**

   ```bash
   cat > ~/navigator-lab/playbooks/collections-demo.yml << 'EOF'
   ---
   - name: Collections Demo
     hosts: localhost
     gather_facts: false
     collections:
       - ansible.posix
       - community.general
     tasks:
       - name: Get path info
         ansible.posix.stat:
           path: /etc/hosts
         register: path_info
       
       - name: Display path info
         debug:
           var: path_info
   EOF
   ```

2. **Run the playbook with ansible-navigator**

   ```bash
   cd ~/navigator-lab
   ansible-navigator run playbooks/collections-demo.yml
   ```

3. **Create a playbook artifact**

   ```bash
   ansible-navigator run playbooks/collections-demo.yml --pae true --pas collections-demo-artifact.json
   ```

4. **Replay a playbook artifact**

   ```bash
   ansible-navigator replay collections-demo-artifact.json
   ```

5. **Examine module documentation**

   ```bash
   ansible-navigator doc ansible.posix.stat
   ```

## Part 9: Using ansible-navigator with Automation Controller

1. **Create a playbook for Automation Controller**

   ```bash
   cat > ~/navigator-lab/playbooks/controller-demo.yml << 'EOF'
   ---
   - name: Automation Controller Demo
     hosts: localhost
     gather_facts: false
     collections:
       - awx.awx
     tasks:
       - name: Create an organization
         awx.awx.organization:
           name: "Navigator Demo Org"
           description: "Organization created via ansible-navigator"
           state: present
           controller_host: https://localhost
           controller_username: admin
           controller_password: redhat
           validate_certs: false
   EOF
   ```

2. **Install the AWX collection**

   ```bash
   ansible-galaxy collection install awx.awx
   ```

3. **Run the playbook with ansible-navigator**

   ```bash
   cd ~/navigator-lab
   ansible-navigator run playbooks/controller-demo.yml
   ```

## Part 10: Troubleshooting with ansible-navigator

1. **Create a playbook with an error**

   ```bash
   cat > ~/navigator-lab/playbooks/error-demo.yml << 'EOF'
   ---
   - name: Error Demo
     hosts: localhost
     gather_facts: false
     tasks:
       - name: This will fail
         command: /bin/false
         register: result
         failed_when: result.rc != 0
       
       - name: This will not run
         debug:
           msg: "This task should be skipped"
   EOF
   ```

2. **Run the playbook and examine the error**

   ```bash
   cd ~/navigator-lab
   ansible-navigator run playbooks/error-demo.yml -m stdout
   ```

3. **Use --syntax-check to find errors before running**

   ```bash
   ansible-navigator run playbooks/error-demo.yml --syntax-check
   ```

4. **View logs for troubleshooting**

   ```bash
   cat ~/.ansible-navigator/logs/ansible-navigator.log
   ```

## Part 11: Advanced ansible-navigator Features

1. **Using settings from environment variables**

   ```bash
   export ANSIBLE_NAVIGATOR_EXECUTION_ENVIRONMENT_IMAGE=registry.redhat.io/ansible-automation-platform-24/ee-supported-rhel8:latest
   ansible-navigator run playbooks/hello.yml
   ```

2. **Using ansible-navigator with collections**

   ```bash
   ansible-navigator collections
   ```

   This opens the collections explorer where you can browse installed collections.

3. **Exploring Ansible configuration**

   ```bash
   ansible-navigator config
   ```

   This shows the current Ansible configuration settings.

4. **Using ansible-navigator with roles**

   ```bash
   mkdir -p ~/navigator-lab/roles/example/tasks
   cat > ~/navigator-lab/roles/example/tasks/main.yml << 'EOF'
   ---
   - name: Example task
     debug:
       msg: "This is a task from a role"
   EOF
   
   cat > ~/navigator-lab/playbooks/role-demo.yml << 'EOF'
   ---
   - name: Role Demo
     hosts: localhost
     gather_facts: false
     roles:
       - example
   EOF
   ```

   Run the playbook:

   ```bash
   cd ~/navigator-lab
   ansible-navigator run playbooks/role-demo.yml
   ```

## Part 12: Preparing for the EX374 Exam

1. **Practice using ansible-navigator in different modes**

   ```bash
   ansible-navigator --help
   ansible-navigator run --help
   ```

2. **Create a complex playbook and run it with ansible-navigator**

   ```bash
   cat > ~/navigator-lab/playbooks/exam-prep.yml << 'EOF'
   ---
   - name: Exam Preparation
     hosts: localhost
     gather_facts: true
     vars:
       message: "Preparing for EX374"
     tasks:
       - name: Display system information
         debug:
           msg: "Running on {{ ansible_distribution }} {{ ansible_distribution_version }}"
       
       - name: Create a test file
         file:
           path: /tmp/ex374-test.txt
           state: touch
           mode: '0644'
         become: true
       
       - name: Write to the test file
         copy:
           content: "{{ message }} - {{ ansible_date_time.iso8601 }}"
           dest: /tmp/ex374-test.txt
         become: true
       
       - name: Read the test file
         command: cat /tmp/ex374-test.txt
         register: file_content
         changed_when: false
       
       - name: Display file content
         debug:
           var: file_content.stdout
   EOF
   ```

   Run the playbook:

   ```bash
   cd ~/navigator-lab
   ansible-navigator run playbooks/exam-prep.yml
   ```

3. **Practice with different execution environments**

   ```bash
   ansible-navigator images
   ansible-navigator run playbooks/exam-prep.yml --eei registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest
   ansible-navigator run playbooks/exam-prep.yml --eei registry.redhat.io/ansible-automation-platform-24/ee-supported-rhel8:latest
   ```

## Summary

In this lab, you've learned how to:

- Understand execution environments and their real-world applications
- Install and configure ansible-navigator
- Navigate the ansible-navigator interface
- Run playbooks using ansible-navigator
- Work with execution environments
- Customize ansible-navigator settings
- Integrate ansible-navigator with Ansible Automation Platform
- Troubleshoot issues using ansible-navigator
- Use advanced ansible-navigator features

These skills are essential for the EX374 exam and for working effectively with Ansible Automation Platform 2.4.

## Additional Resources

- [EX374 Objectives](https://github.com/RedHatRanger/RHCA9_EX374/blob/main/objectives.md)
- [Ansible Navigator Documentation](https://ansible-navigator.readthedocs.io/)
- [Ansible Builder Documentation](https://ansible-builder.readthedocs.io/)
- [Ansible Execution Environments Documentation](https://docs.ansible.com/ansible/latest/getting_started_ee/index.html)
- [Red Hat Ansible Automation Platform Documentation](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/)
