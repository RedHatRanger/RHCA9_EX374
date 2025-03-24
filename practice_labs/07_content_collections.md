# Lab: Content Collection Management

This lab covers the "Create and manage Ansible content collections" objective of the EX374 exam. You'll learn how to create, install, and use Ansible content collections.

## Prerequisites

- Ansible Automation Platform 2.4 installed according to the [AAP Installation Guide](../guides/aap_installation_guide.md)
- Completion of the previous labs

## Lab Environment Setup

1. **Create a working directory**
   ```bash
   mkdir -p ~/ansible-labs/collections-lab
   cd ~/ansible-labs/collections-lab
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
   
   [all:vars]
   ansible_connection=local
   EOF
   ```

## Exercise 1: Understanding Ansible Collections

In this exercise, you'll learn about Ansible collections and how to use them.

1. **Create a playbook to explore installed collections**
   ```bash
   cat > explore_collections.yml << 'EOF'
   ---
   - name: Explore Ansible Collections
     hosts: localhost
     gather_facts: false
     
     tasks:
       - name: Get Ansible collection paths
         command: ansible-config dump | grep COLLECTIONS_PATHS
         register: collections_paths
         changed_when: false
       
       - name: Display collection paths
         debug:
           var: collections_paths.stdout
       
       - name: List installed collections
         command: ansible-galaxy collection list
         register: installed_collections
         changed_when: false
       
       - name: Display installed collections
         debug:
           var: installed_collections.stdout_lines
   EOF
   ```

2. **Run the playbook**
   ```bash
   ansible-playbook explore_collections.yml
   ```

3. **Create a file to document collection concepts**
   ```bash
   cat > collection_concepts.md << 'EOF'
   # Ansible Collections Concepts

   ## What are Ansible Collections?

   Ansible Collections are a distribution format for Ansible content that can include playbooks, roles, modules, and plugins. Collections are designed to be a more structured way to organize, distribute, and consume Ansible content compared to traditional roles.

   ## Collection Structure

   A typical collection follows this structure:

   ```
   collection/
   ├── docs/
   ├── galaxy.yml
   ├── plugins/
   │   ├── modules/
   │   ├── inventory/
   │   └── ...
   ├── README.md
   ├── roles/
   ├── playbooks/
   └── tests/
   ```

   - **galaxy.yml**: Collection metadata
   - **plugins/**: Contains plugins, modules, and other extensions
   - **roles/**: Contains roles
   - **playbooks/**: Contains playbooks
   - **docs/**: Documentation
   - **tests/**: Tests for the collection

   ## Collection Namespaces

   Collections use a namespace format: `namespace.collection`. For example:
   - `ansible.builtin`: Ansible's built-in collection
   - `community.general`: Community-maintained general utilities
   - `redhat.rhel_system_roles`: Red Hat Enterprise Linux system roles

   ## Using Collections

   Collections can be used in several ways:

   1. **Fully-Qualified Collection Names (FQCN)**:
      ```yaml
      - name: Use a module from a collection
        community.general.nmcli:
          conn_name: my_wifi
          type: wifi
          # ...
      ```

   2. **Using `collections` keyword**:
      ```yaml
      - hosts: all
        collections:
          - community.general
        tasks:
          - name: Use a module from the collection
            nmcli:
              conn_name: my_wifi
              # ...
      ```

   ## Installing Collections

   Collections can be installed using `ansible-galaxy`:

   ```bash
   # Install a collection from Galaxy
   ansible-galaxy collection install community.general

   # Install a specific version
   ansible-galaxy collection install community.general:3.0.0

   # Install from a tarball
   ansible-galaxy collection install my_namespace-my_collection-1.0.0.tar.gz

   # Install from a requirements file
   ansible-galaxy collection install -r requirements.yml
   ```

   ## Collection Requirements File

   A requirements file for collections looks like:

   ```yaml
   collections:
     - name: community.general
       version: '>=3.0.0'
     - name: ansible.posix
       version: '>=1.3.0'
     - name: https://github.com/organization/repo_name.git
       type: git
       version: devel
   ```
   EOF
   ```

4. **Commit your changes**
   ```bash
   git add inventory explore_collections.yml collection_concepts.md
   git commit -m "Add collection exploration and concepts"
   ```

## Exercise 2: Installing and Using Collections

In this exercise, you'll install and use existing collections.

1. **Create a requirements file for collections**
   ```bash
   cat > collections_requirements.yml << 'EOF'
   ---
   collections:
     - name: ansible.posix
       version: '>=1.3.0'
     - name: community.general
       version: '>=5.0.0'
   EOF
   ```

2. **Install the collections**
   ```bash
   ansible-galaxy collection install -r collections_requirements.yml
   ```

3. **Create a playbook that uses the installed collections**
   ```bash
   cat > using_collections.yml << 'EOF'
   ---
   - name: Using Installed Collections
     hosts: localhost
     gather_facts: true
     collections:
       - ansible.posix
       - community.general
     
     tasks:
       - name: Get system information using ansible.posix
         ansible.posix.sysctl:
           name: kernel.hostname
         register: hostname_info
         ignore_errors: true
       
       - name: Display hostname information
         debug:
           var: hostname_info
       
       - name: Get system uptime
         community.general.system.uptime:
         register: uptime_info
         ignore_errors: true
       
       - name: Display uptime information
         debug:
           var: uptime_info
       
       - name: Use collection module without FQCN
         sysctl:
           name: vm.swappiness
         register: swap_info
         ignore_errors: true
       
       - name: Display swap information
         debug:
           var: swap_info
   EOF
   ```

4. **Run the playbook**
   ```bash
   ansible-playbook using_collections.yml
   ```

5. **Create a playbook that uses different ways to reference collections**
   ```bash
   cat > collection_references.yml << 'EOF'
   ---
   - name: Different Ways to Reference Collections
     hosts: localhost
     gather_facts: false
     
     tasks:
       # Method 1: Using Fully-Qualified Collection Names (FQCN)
       - name: Using FQCN
         debug:
           msg: "This task uses a Fully-Qualified Collection Name"
       
       - name: Get file info using FQCN
         ansible.builtin.stat:
           path: /etc/hosts
         register: hosts_file_info
       
       - name: Display file info
         ansible.builtin.debug:
           var: hosts_file_info
     
   - name: Using Collections Keyword
     hosts: localhost
     gather_facts: false
     collections:
       - ansible.builtin
       - community.general
     
     tasks:
       # Method 2: Using collections keyword
       - name: Using collections keyword
         debug:
           msg: "This playbook uses the collections keyword"
       
       - name: Get file info without FQCN
         stat:
           path: /etc/passwd
         register: passwd_file_info
       
       - name: Display file info
         debug:
           var: passwd_file_info
   EOF
   ```

6. **Run the playbook**
   ```bash
   ansible-playbook collection_references.yml
   ```

7. **Commit your changes**
   ```bash
   git add collections_requirements.yml using_collections.yml collection_references.yml
   git commit -m "Add collection installation and usage examples"
   ```

## Exercise 3: Creating a Custom Collection

In this exercise, you'll create your own custom Ansible collection.

1. **Create a collection skeleton**
   ```bash
   mkdir -p collections
   cd collections
   ansible-galaxy collection init my_namespace.my_collection
   cd ..
   ```

2. **Explore the collection structure**
   ```bash
   find collections/my_namespace/my_collection -type f | sort
   ```

3. **Create a custom module for the collection**
   ```bash
   mkdir -p collections/my_namespace/my_collection/plugins/modules
   
   cat > collections/my_namespace/my_collection/plugins/modules/hello.py << 'EOF'
   #!/usr/bin/python
   
   DOCUMENTATION = '''
   ---
   module: hello
   short_description: A simple module that says hello
   version_added: "1.0.0"
   description:
       - "A simple module that says hello"
   options:
       name:
           description:
               - Name to say hello to
           required: true
           type: str
   author:
       - "Your Name (@yourgithub)"
   '''
   
   EXAMPLES = '''
   # Say hello to John
   - name: Say hello
     my_namespace.my_collection.hello:
       name: John
   '''
   
   RETURN = '''
   message:
       description: The hello message
       type: str
       returned: always
       sample: "Hello, John!"
   '''
   
   from ansible.module_utils.basic import AnsibleModule
   
   def main():
       module = AnsibleModule(
           argument_spec=dict(
               name=dict(type='str', required=True),
           ),
           supports_check_mode=True
       )
   
       name = module.params['name']
       result = dict(
           changed=False,
           message=f"Hello, {name}!"
       )
   
       module.exit_json(**result)
   
   if __name__ == '__main__':
       main()
   EOF
   ```

4. **Create a custom filter plugin for the collection**
   ```bash
   mkdir -p collections/my_namespace/my_collection/plugins/filter
   
   cat > collections/my_namespace/my_collection/plugins/filter/filters.py << 'EOF'
   #!/usr/bin/python
   
   def reverse_string(string):
       """Reverse a string."""
       return string[::-1]
   
   def to_uppercase(string):
       """Convert string to uppercase."""
       return string.upper()
   
   def to_lowercase(string):
       """Convert string to lowercase."""
       return string.lower()
   
   def add_prefix(string, prefix):
       """Add a prefix to a string."""
       return f"{prefix}{string}"
   
   class FilterModule(object):
       """Custom filters for string manipulation."""
   
       def filters(self):
           return {
               'reverse': reverse_string,
               'to_uppercase': to_uppercase,
               'to_lowercase': to_lowercase,
               'add_prefix': add_prefix,
           }
   EOF
   ```

5. **Create a role for the collection**
   ```bash
   cd collections/my_namespace/my_collection
   ansible-galaxy role init roles/example_role
   cd ../../..
   
   cat > collections/my_namespace/my_collection/roles/example_role/tasks/main.yml << 'EOF'
   ---
   - name: Display a message
     debug:
       msg: "This is a task from the example_role in my_namespace.my_collection"
   
   - name: Create a file
     copy:
       content: "This file was created by the example_role in my_namespace.my_collection"
       dest: "/tmp/example_role.txt"
       mode: '0644'
   EOF
   ```

6. **Create a playbook for the collection**
   ```bash
   mkdir -p collections/my_namespace/my_collection/playbooks
   
   cat > collections/my_namespace/my_collection/playbooks/hello_playbook.yml << 'EOF'
   ---
   - name: Hello Playbook
     hosts: all
     gather_facts: false
     
     tasks:
       - name: Say hello using the hello module
         my_namespace.my_collection.hello:
           name: "{{ inventory_hostname }}"
         register: hello_result
       
       - name: Display the hello message
         debug:
           var: hello_result.message
       
       - name: Use the custom filter
         debug:
           msg: "{{ 'Hello, World!' | my_namespace.my_collection.reverse }}"
       
       - name: Include the example role
         include_role:
           name: my_namespace.my_collection.example_role
   EOF
   ```

7. **Update the collection metadata**
   ```bash
   cat > collections/my_namespace/my_collection/galaxy.yml << 'EOF'
   namespace: my_namespace
   name: my_collection
   version: 1.0.0
   readme: README.md
   authors:
     - Your Name <your.email@example.com>
   description: A collection for the EX374 exam preparation
   license:
     - GPL-3.0-or-later
   tags:
     - demo
     - collection
   repository: https://github.com/yourusername/my_collection
   documentation: https://github.com/yourusername/my_collection
   homepage: https://github.com/yourusername/my_collection
   issues: https://github.com/yourusername/my_collection/issues
   build_ignore:
     - '*.tar.gz'
   EOF
   ```

8. **Update the collection README**
   ```bash
   cat > collections/my_namespace/my_collection/README.md << 'EOF'
   # My Collection
   
   This is a custom Ansible collection created for EX374 exam preparation.
   
   ## Included Content
   
   ### Modules
   - `hello`: A simple module that says hello
   
   ### Filter Plugins
   - `reverse`: Reverses a string
   - `to_uppercase`: Converts a string to uppercase
   - `to_lowercase`: Converts a string to lowercase
   - `add_prefix`: Adds a prefix to a string
   
   ### Roles
   - `example_role`: A simple example role
   
   ### Playbooks
   - `hello_playbook.yml`: A playbook that demonstrates the collection's features
   
   ## Using This Collection
   
   ```yaml
   - hosts: all
     collections:
       - my_namespace.my_collection
     tasks:
       - name: Say hello
         hello:
           name: World
   ```
   
   ## License
   
   GPL-3.0-or-later
   EOF
   ```

9. **Build the collection**
   ```bash
   cd collections
   ansible-galaxy collection build my_namespace/my_collection
   cd ..
   ```

10. **Install the collection**
    ```bash
    ansible-galaxy collection install collections/my_namespace-my_collection-1.0.0.tar.gz -p ./collections
    ```

11. **Create a playbook to test the collection**
    ```bash
    cat > test_custom_collection.yml << 'EOF'
    ---
    - name: Test Custom Collection
      hosts: all
      gather_facts: false
      collections:
        - my_namespace.my_collection
      
      tasks:
        - name: Use the hello module
          hello:
            name: "{{ inventory_hostname }}"
          register: hello_result
        
        - name: Display the hello message
          debug:
            var: hello_result.message
        
        - name: Test the custom filters
          debug:
            msg:
              - "Original: Hello, World!"
              - "Reversed: {{ 'Hello, World!' | reverse }}"
              - "Uppercase: {{ 'Hello, World!' | to_uppercase }}"
              - "Lowercase: {{ 'Hello, World!' | to_lowercase }}"
              - "With prefix: {{ 'World!' | add_prefix('Hello, ') }}"
        
        - name: Include the example role
          include_role:
            name: example_role
    EOF
    ```

12. **Run the playbook**
    ```bash
    ANSIBLE_COLLECTIONS_PATH=./collections ansible-playbook -i inventory test_custom_collection.yml
    ```

13. **Commit your changes**
    ```bash
    git add collections test_custom_collection.yml
    git commit -m "Add custom collection creation example"
    ```

## Exercise 4: Collection Dependencies and Requirements

In this exercise, you'll learn how to manage collection dependencies and requirements.

1. **Create a collection with dependencies**
   ```bash
   mkdir -p collections/dependent_namespace/dependent_collection/{plugins/modules,roles,playbooks}
   
   cat > collections/dependent_namespace/dependent_collection/galaxy.yml << 'EOF'
   namespace: dependent_namespace
   name: dependent_collection
   version: 1.0.0
   readme: README.md
   authors:
     - Your Name <your.email@example.com>
   description: A collection that depends on my_namespace.my_collection
   license:
     - GPL-3.0-or-later
   tags:
     - demo
     - collection
   repository: https://github.com/yourusername/dependent_collection
   documentation: https://github.com/yourusername/dependent_collection
   homepage: https://github.com/yourusername/dependent_collection
   issues: https://github.com/yourusername/dependent_collection/issues
   dependencies:
     my_namespace.my_collection: '>=1.0.0'
   build_ignore:
     - '*.tar.gz'
   EOF
   
   cat > collections/dependent_namespace/dependent_collection/README.md << 'EOF'
   # Dependent Collection
   
   This collection depends on my_namespace.my_collection.
   
   ## Dependencies
   
   - my_namespace.my_collection: '>=1.0.0'
   EOF
   
   cat > collections/dependent_namespace/dependent_collection/plugins/modules/greet.py << 'EOF'
   #!/usr/bin/python
   
   DOCUMENTATION = '''
   ---
   module: greet
   short_description: A module that greets with a custom greeting
   version_added: "1.0.0"
   description:
       - "A module that greets with a custom greeting"
   options:
       name:
           description:
               - Name to greet
           required: true
           type: str
       greeting:
           description:
               - Custom greeting to use
           required: false
           default: "Hello"
           type: str
   author:
       - "Your Name (@yourgithub)"
   '''
   
   EXAMPLES = '''
   # Greet John with Hello
   - name: Greet with default greeting
     dependent_namespace.dependent_collection.greet:
       name: John
   
   # Greet John with Hi
   - name: Greet with custom greeting
     dependent_namespace.dependent_collection.greet:
       name: John
       greeting: Hi
   '''
   
   RETURN = '''
   message:
       description: The greeting message
       type: str
       returned: always
       sample: "Hello, John!"
   '''
   
   from ansible.module_utils.basic import AnsibleModule
   
   def main():
       module = AnsibleModule(
           argument_spec=dict(
               name=dict(type='str', required=True),
               greeting=dict(type='str', required=False, default='Hello'),
           ),
           supports_check_mode=True
       )
   
       name = module.params['name']
       greeting = module.params['greeting']
       result = dict(
           changed=False,
           message=f"{greeting}, {name}!"
       )
   
       module.exit_json(**result)
   
   if __name__ == '__main__':
       main()
   EOF
   
   cat > collections/dependent_namespace/dependent_collection/playbooks/greet_playbook.yml << 'EOF'
   ---
   - name: Greet Playbook
     hosts: all
     gather_facts: false
     collections:
       - dependent_namespace.dependent_collection
       - my_namespace.my_collection
     
     tasks:
       - name: Greet using the greet module
         greet:
           name: "{{ inventory_hostname }}"
           greeting: "Greetings"
         register: greet_result
       
       - name: Display the greeting message
         debug:
           var: greet_result.message
       
       - name: Say hello using the hello module from the dependency
         hello:
           name: "{{ inventory_hostname }}"
         register: hello_result
       
       - name: Display the hello message
         debug:
           var: hello_result.message
       
       - name: Use filters from the dependency
         debug:
           msg: "{{ greet_result.message | reverse }}"
   EOF
   ```

2. **Build the dependent collection**
   ```bash
   cd collections
   ansible-galaxy collection build dependent_namespace/dependent_collection
   cd ..
   ```

3. **Install the dependent collection**
   ```bash
   ansible-galaxy collection install collections/dependent_namespace-dependent_collection-1.0.0.tar.gz -p ./collections
   ```

4. **Create a requirements file for both collections**
   ```bash
   cat > collection_requirements_all.yml << 'EOF'
   ---
   collections:
     - name: my_namespace.my_collection
       version: '>=1.0.0'
       source: ./collections
     - name: dependent_namespace.dependent_collection
       version: '>=1.0.0'
       source: ./collections
   EOF
   ```

5. **Create a playbook to test the dependent collection**
   ```bash
   cat > test_dependent_collection.yml << 'EOF'
   ---
   - name: Test Dependent Collection
     hosts: all
     gather_facts: false
     collections:
       - dependent_namespace.dependent_collection
       - my_namespace.my_collection
     
     tasks:
       - name: Use the greet module
         greet:
           name: "{{ inventory_hostname }}"
           greeting: "Greetings"
         register: greet_result
       
       - name: Display the greeting message
         debug:
           var: greet_result.message
       
       - name: Use the hello module from the dependency
         hello:
           name: "{{ inventory_hostname }}"
         register: hello_result
       
       - name: Display the hello message
         debug:
           var: hello_result.message
       
       - name: Use filters from the dependency
         debug:
           msg:
             - "Original: {{ greet_result.message }}"
             - "Reversed: {{ greet_result.message | reverse }}"
             - "Uppercase: {{ greet_result.message | to_uppercase }}"
   EOF
   ```

6. **Run the playbook**
   ```bash
   ANSIBLE_COLLECTIONS_PATH=./collections ansible-playbook -i inventory test_dependent_collection.yml
   ```

7. **Commit your changes**
   ```bash
   git add collections collection_requirements_all.yml test_dependent_collection.yml
   git commit -m "Add collection dependencies example"
   ```

## Exercise 5: Publishing and Sharing Collections

In this exercise, you'll learn how to publish and share collections.

1. **Create documentation for publishing collections**
   ```bash
   cat > publishing_collections.md << 'EOF'
   # Publishing and Sharing Ansible Collections

   ## Methods for Sharing Collections

   There are several ways to share Ansible collections:

   1. **Ansible Galaxy**: The official repository for Ansible collections
   2. **Private Automation Hub**: Red Hat's private repository for organizations
   3. **Git Repositories**: Hosting collections in Git repositories
   4. **Tarball Distribution**: Sharing collection tarballs directly

   ## Publishing to Ansible Galaxy

   To publish a collection to Ansible Galaxy:

   1. **Create an account** on [galaxy.ansible.com](https://galaxy.ansible.com/)
   2. **Generate an API token** in your Galaxy profile
   3. **Build your collection**:
      ```bash
      ansible-galaxy collection build my_namespace/my_collection
      ```
   4. **Publish your collection**:
      ```bash
      ansible-galaxy collection publish my_namespace-my_collection-1.0.0.tar.gz --api-key=<your-api-key>
      ```

   ## Publishing to Private Automation Hub

   To publish a collection to Private Automation Hub:

   1. **Configure your Ansible config** to use your Private Automation Hub:
      ```ini
      # ansible.cfg
      [galaxy]
      server_list = automation_hub

      [galaxy_server.automation_hub]
      url=https://automation-hub.example.com/api/galaxy/
      auth_url=https://automation-hub.example.com/api/galaxy/v3/auth/token/
      token=<your-token>
      ```
   2. **Build your collection**:
      ```bash
      ansible-galaxy collection build my_namespace/my_collection
      ```
   3. **Publish your collection**:
      ```bash
      ansible-galaxy collection publish my_namespace-my_collection-1.0.0.tar.gz --server automation_hub
      ```

   ## Sharing via Git Repository

   To share a collection via a Git repository:

   1. **Push your collection to a Git repository**
   2. **Reference the repository in your requirements file**:
      ```yaml
      collections:
        - name: https://github.com/username/my_collection.git
          type: git
          version: main
      ```
   3. **Install from the requirements file**:
      ```bash
      ansible-galaxy collection install -r requirements.yml
      ```

   ## Sharing via Tarball

   To share a collection via tarball:

   1. **Build your collection**:
      ```bash
      ansible-galaxy collection build my_namespace/my_collection
      ```
   2. **Share the tarball** with your team
   3. **Install from the tarball**:
      ```bash
      ansible-galaxy collection install my_namespace-my_collection-1.0.0.tar.gz
      ```

   ## Collection Versioning

   Collections use semantic versioning (MAJOR.MINOR.PATCH):

   - **MAJOR**: Incompatible API changes
   - **MINOR**: Add functionality in a backward-compatible manner
   - **PATCH**: Backward-compatible bug fixes

   When publishing new versions, update the `version` field in `galaxy.yml`.
   EOF
   ```

2. **Create a script to simulate publishing a collection**
   ```bash
   cat > publish_collection.sh << 'EOF'
   #!/bin/bash
   
   # This script simulates the process of publishing a collection
   
   echo "Building collection my_namespace.my_collection..."
   cd collections
   ansible-galaxy collection build my_namespace/my_collection
   
   echo "Simulating publication to Ansible Galaxy..."
   echo "ansible-galaxy collection publish my_namespace-my_collection-1.0.0.tar.gz --api-key=<your-api-key>"
   
   echo "Simulating publication to Private Automation Hub..."
   echo "ansible-galaxy collection publish my_namespace-my_collection-1.0.0.tar.gz --server automation_hub"
   
   echo "Creating a Git repository for the collection..."
   mkdir -p git_repo
   cp my_namespace-my_collection-1.0.0.tar.gz git_repo/
   cd git_repo
   git init
   echo "# My Collection" > README.md
   git add README.md my_namespace-my_collection-1.0.0.tar.gz
   git config --local user.email "you@example.com"
   git config --local user.name "Your Name"
   git commit -m "Initial commit with collection tarball"
   
   echo "Collection is ready for distribution"
   cd ../..
   EOF
   
   chmod +x publish_collection.sh
   ```

3. **Run the script**
   ```bash
   ./publish_collection.sh
   ```

4. **Create a requirements file for a Git-hosted collection**
   ```bash
   cat > git_requirements.yml << 'EOF'
   ---
   collections:
     # This is a simulated Git repository reference
     - name: https://github.com/username/my_collection.git
       type: git
       version: main
   EOF
   ```

5. **Commit your changes**
   ```bash
   git add publishing_collections.md publish_collection.sh git_requirements.yml
   git commit -m "Add collection publishing documentation"
   ```

## Exercise 6: Using Collections in Automation Controller

In this exercise, you'll learn how to use collections in Automation Controller.

1. **Create documentation for using collections in Automation Controller**
   ```bash
   cat > collections_in_controller.md << 'EOF'
   # Using Collections in Automation Controller

   ## Overview

   Ansible Automation Platform's Automation Controller (formerly Tower) can use collections in several ways:

   1. **Project-based collections**: Collections included in a project repository
   2. **Execution Environment-based collections**: Collections included in custom execution environments
   3. **Controller-managed collections**: Collections installed directly on the Controller

   ## Project-Based Collections

   When using collections in a project:

   1. **Include a `collections/requirements.yml` file in your project repository**:
      ```yaml
      ---
      collections:
        - name: ansible.posix
          version: '>=1.3.0'
        - name: community.general
          version: '>=5.0.0'
      ```

   2. **Reference collections in your playbooks**:
      ```yaml
      ---
      - name: My Playbook
        hosts: all
        collections:
          - ansible.posix
          - community.general
        tasks:
          - name: Use a module from a collection
            sysctl:
              name: vm.swappiness
              value: '10'
      ```

   3. **Configure the project in Automation Controller**:
      - Set "Update Revision on Launch" to ensure collections are updated
      - Set appropriate SCM update options

   ## Execution Environment-Based Collections

   For collections in execution environments:

   1. **Create a custom execution environment definition**:
      ```yaml
      # execution-environment.yml
      ---
      version: 1
      build_arg_defaults:
        EE_BASE_IMAGE: 'registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest'
      dependencies:
        galaxy: requirements.yml
      ```

   2. **Create a requirements file**:
      ```yaml
      # requirements.yml
      ---
      collections:
        - name: ansible.posix
          version: '>=1.3.0'
        - name: community.general
          version: '>=5.0.0'
      ```

   3. **Build the execution environment**:
      ```bash
      ansible-builder build -t my-custom-ee -f execution-environment.yml
      ```

   4. **Push to a container registry**:
      ```bash
      podman push my-custom-ee registry.example.com/my-custom-ee
      ```

   5. **Configure the execution environment in Automation Controller**:
      - Add the execution environment in the Controller UI
      - Associate it with job templates

   ## Controller-Managed Collections

   For collections managed directly by Controller:

   1. **Install collections on the Controller nodes**:
      ```bash
      ansible-galaxy collection install ansible.posix community.general
      ```

   2. **Ensure collections are in the Ansible collection path**:
      ```bash
      ansible-config dump | grep COLLECTIONS_PATHS
      ```

   ## Best Practices

   - **Use execution environments** for the most reliable and reproducible automation
   - **Version pin your collections** to ensure consistency
   - **Test collection compatibility** before deploying to production
   - **Document collection dependencies** in your project README
   - **Use collection requirements files** rather than installing collections manually
   EOF
   ```

2. **Create a sample project structure for Automation Controller**
   ```bash
   mkdir -p controller_project/{playbooks,collections,roles}
   
   cat > controller_project/collections/requirements.yml << 'EOF'
   ---
   collections:
     - name: ansible.posix
       version: '>=1.3.0'
     - name: community.general
       version: '>=5.0.0'
     # Reference to our custom collection (in a real scenario, this would be a Git URL or Galaxy reference)
     - name: my_namespace.my_collection
       version: '>=1.0.0'
   EOF
   
   cat > controller_project/playbooks/main.yml << 'EOF'
   ---
   - name: Main Playbook for Automation Controller
     hosts: all
     gather_facts: true
     collections:
       - ansible.posix
       - community.general
       - my_namespace.my_collection
     
     tasks:
       - name: Get system information
         ansible.posix.sysctl:
           name: kernel.hostname
         register: hostname_info
         ignore_errors: true
       
       - name: Display hostname information
         debug:
           var: hostname_info
       
       - name: Use custom collection module
         my_namespace.my_collection.hello:
           name: "{{ inventory_hostname }}"
         register: hello_result
       
       - name: Display hello message
         debug:
           var: hello_result.message
   EOF
   
   cat > controller_project/execution-environment.yml << 'EOF'
   ---
   version: 1
   build_arg_defaults:
     EE_BASE_IMAGE: 'registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest'
   dependencies:
     galaxy: collections/requirements.yml
   EOF
   
   cat > controller_project/README.md << 'EOF'
   # Controller Project
   
   This project demonstrates how to use collections in Automation Controller.
   
   ## Collections Used
   
   - ansible.posix
   - community.general
   - my_namespace.my_collection
   
   ## Execution Environment
   
   This project includes an execution environment definition that includes all required collections.
   
   ## Playbooks
   
   - `playbooks/main.yml`: Main playbook that uses modules from various collections
   EOF
   ```

3. **Commit your changes**
   ```bash
   git add collections_in_controller.md controller_project
   git commit -m "Add documentation for using collections in Automation Controller"
   ```

## Conclusion

In this lab, you've learned how to:
- Understand Ansible collections and their structure
- Install and use existing collections
- Create custom collections with modules, filters, roles, and playbooks
- Manage collection dependencies and requirements
- Publish and share collections
- Use collections in Automation Controller

These skills are essential for the "Create and manage Ansible content collections" objective of the EX374 exam.

## Additional Resources

- [Ansible Collections Documentation](https://docs.ansible.com/ansible/latest/user_guide/collections_using.html)
- [Developing Ansible Collections](https://docs.ansible.com/ansible/latest/dev_guide/developing_collections.html)
- [Ansible Galaxy Documentation](https://galaxy.ansible.com/docs/)
- [Ansible Builder Documentation](https://ansible-builder.readthedocs.io/en/latest/)
