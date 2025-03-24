# Lab: Execution Environment Management

This lab covers the "Create and use execution environments" objective of the EX374 exam. You'll learn how to create, manage, and use execution environments in Ansible Automation Platform.

## Prerequisites

- Ansible Automation Platform 2.4 installed according to the [AAP Installation Guide](../guides/aap_installation_guide.md)
- Completion of the previous labs
- Podman or Docker installed for building container images

## Lab Environment Setup

1. **Create a working directory**
   ```bash
   mkdir -p ~/ansible-labs/execution-environments-lab
   cd ~/ansible-labs/execution-environments-lab
   ```

2. **Initialize a Git repository**
   ```bash
   git init
   ```

3. **Install ansible-builder if not already installed**
   ```bash
   pip install ansible-builder
   ```

## Exercise 1: Understanding Execution Environments

In this exercise, you'll learn about execution environments and their components.

1. **Create a document explaining execution environments**
   ```bash
   cat > execution_environment_concepts.md << 'EOF'
   # Ansible Execution Environments

   ## What are Execution Environments?

   Execution Environments (EEs) are containerized environments that include Ansible Core, collections, dependencies, and tooling needed to run Ansible automation. They provide a consistent and portable way to execute Ansible content.

   ## Benefits of Execution Environments

   - **Consistency**: Same environment across development, testing, and production
   - **Portability**: Run the same automation anywhere containers can run
   - **Dependency Management**: Package all dependencies together
   - **Isolation**: Separate dependencies for different projects
   - **Versioning**: Version control for the entire automation environment

   ## Components of an Execution Environment

   An execution environment typically includes:

   1. **Base Image**: Usually based on Red Hat Universal Base Image (UBI)
   2. **Ansible Core**: The Ansible command-line tools
   3. **Python Dependencies**: Required Python packages
   4. **System Dependencies**: Required system packages
   5. **Ansible Collections**: Collections with roles, modules, and plugins
   6. **Custom Content**: Any additional files or tools

   ## Execution Environment Definition File

   The execution environment is defined in a YAML file, typically named `execution-environment.yml`:

   ```yaml
   ---
   version: 1
   
   build_arg_defaults:
     EE_BASE_IMAGE: 'registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest'
   
   dependencies:
     python: requirements.txt
     system: bindep.txt
     galaxy: requirements.yml
   
   additional_build_steps:
     prepend: |
       RUN pip3 install --upgrade pip setuptools
     append: |
       RUN echo "Additional build steps completed"
   ```

   ## Key Files

   - **execution-environment.yml**: Main definition file
   - **requirements.txt**: Python package dependencies
   - **bindep.txt**: System package dependencies
   - **requirements.yml**: Ansible collection dependencies

   ## Tools for Working with Execution Environments

   - **ansible-builder**: CLI tool to build execution environments
   - **ansible-navigator**: CLI tool to use execution environments
   - **podman/docker**: Container engines to run execution environments
   EOF
   ```

2. **Create a document explaining the ansible-builder tool**
   ```bash
   cat > ansible_builder_guide.md << 'EOF'
   # Ansible Builder Guide

   ## Overview

   Ansible Builder is a tool for building Execution Environments. It creates container images that include Ansible Core, collections, and their dependencies.

   ## Installation

   ```bash
   pip install ansible-builder
   ```

   ## Basic Usage

   ```bash
   # Create a new execution environment
   ansible-builder build -t my-execution-environment:1.0 -f execution-environment.yml
   ```

   ## Key Commands

   - **build**: Build an execution environment image
   - **create**: Create an execution environment definition
   - **introspect**: Introspect collections for dependencies

   ## Command Options

   - **-t, --tag**: Tag for the container image
   - **-f, --file**: Path to the execution environment definition file
   - **--container-runtime**: Container runtime to use (docker or podman)
   - **--build-arg**: Additional build arguments
   - **--verbosity**: Increase verbosity of output

   ## Example Workflow

   1. Create an execution environment definition:
      ```bash
      ansible-builder create
      ```

   2. Edit the definition files:
      - execution-environment.yml
      - requirements.txt
      - bindep.txt
      - requirements.yml

   3. Build the execution environment:
      ```bash
      ansible-builder build -t my-ee:1.0
      ```

   4. Use the execution environment:
      ```bash
      ansible-navigator run playbook.yml -i inventory --eei my-ee:1.0
      ```

   ## Best Practices

   - Version your execution environments
   - Include only necessary dependencies
   - Document the contents of your execution environment
   - Test execution environments before deployment
   - Store execution environments in a container registry
   EOF
   ```

3. **Commit your changes**
   ```bash
   git add execution_environment_concepts.md ansible_builder_guide.md
   git commit -m "Add execution environment concepts and ansible-builder guide"
   ```

## Exercise 2: Creating a Basic Execution Environment

In this exercise, you'll create a basic execution environment with ansible-builder.

1. **Create a basic execution environment definition**
   ```bash
   cat > basic-ee.yml << 'EOF'
   ---
   version: 1
   
   build_arg_defaults:
     EE_BASE_IMAGE: 'registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest'
   
   dependencies:
     python: requirements.txt
     galaxy: requirements.yml
   EOF
   ```

2. **Create Python requirements file**
   ```bash
   cat > requirements.txt << 'EOF'
   # Python package requirements
   PyYAML>=6.0
   jmespath>=1.0.1
   netaddr>=0.8.0
   EOF
   ```

3. **Create Ansible collection requirements file**
   ```bash
   cat > requirements.yml << 'EOF'
   ---
   collections:
     - name: ansible.posix
       version: '>=1.3.0'
     - name: community.general
       version: '>=5.0.0'
   EOF
   ```

4. **Build the execution environment**
   ```bash
   ansible-builder build -t basic-ee:1.0 -f basic-ee.yml --container-runtime podman
   ```

5. **Create a script to test the execution environment**
   ```bash
   cat > test-basic-ee.sh << 'EOF'
   #!/bin/bash
   
   # Create a test playbook
   cat > test-playbook.yml << 'EOL'
   ---
   - name: Test Execution Environment
     hosts: localhost
     gather_facts: true
     
     tasks:
       - name: Display Python version
         command: python3 --version
         register: python_version
         changed_when: false
       
       - name: Display Python version
         debug:
           var: python_version.stdout
       
       - name: Test jmespath
         debug:
           msg: "{{ ['a', 'b', 'c'] | json_query('[1]') }}"
       
       - name: Test netaddr
         debug:
           msg: "{{ '192.168.1.1' | ipaddr('address') }}"
   EOL
   
   # Run the playbook using the execution environment
   podman run --rm -v $(pwd):/runner -w /runner basic-ee:1.0 ansible-playbook test-playbook.yml
   EOF
   
   chmod +x test-basic-ee.sh
   ```

6. **Run the test script**
   ```bash
   ./test-basic-ee.sh
   ```

7. **Commit your changes**
   ```bash
   git add basic-ee.yml requirements.txt requirements.yml test-basic-ee.sh
   git commit -m "Add basic execution environment example"
   ```

## Exercise 3: Creating an Advanced Execution Environment

In this exercise, you'll create a more advanced execution environment with system dependencies and custom build steps.

1. **Create an advanced execution environment definition**
   ```bash
   cat > advanced-ee.yml << 'EOF'
   ---
   version: 1
   
   build_arg_defaults:
     EE_BASE_IMAGE: 'registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest'
   
   dependencies:
     python: advanced-requirements.txt
     system: bindep.txt
     galaxy: advanced-requirements.yml
   
   additional_build_steps:
     prepend: |
       RUN pip3 install --upgrade pip setuptools wheel
       RUN mkdir -p /etc/ansible/roles /etc/ansible/collections
     append: |
       COPY scripts/custom-setup.sh /usr/local/bin/
       RUN chmod +x /usr/local/bin/custom-setup.sh
       RUN /usr/local/bin/custom-setup.sh
       RUN echo "Advanced Execution Environment built on $(date)" > /etc/ee-info.txt
   EOF
   ```

2. **Create advanced Python requirements file**
   ```bash
   cat > advanced-requirements.txt << 'EOF'
   # Python package requirements
   PyYAML>=6.0
   jmespath>=1.0.1
   netaddr>=0.8.0
   requests>=2.25.0
   cryptography>=3.4.0
   boto3>=1.18.0
   botocore>=1.21.0
   pywinrm>=0.4.1
   EOF
   ```

3. **Create system dependencies file**
   ```bash
   cat > bindep.txt << 'EOF'
   # System package dependencies
   gcc [platform:rpm]
   python3-devel [platform:rpm]
   krb5-devel [platform:rpm]
   EOF
   ```

4. **Create advanced Ansible collection requirements file**
   ```bash
   cat > advanced-requirements.yml << 'EOF'
   ---
   collections:
     - name: ansible.posix
       version: '>=1.3.0'
     - name: community.general
       version: '>=5.0.0'
     - name: amazon.aws
       version: '>=2.0.0'
     - name: ansible.windows
       version: '>=1.7.0'
     - name: community.crypto
       version: '>=1.9.0'
   EOF
   ```

5. **Create a custom setup script**
   ```bash
   mkdir -p scripts
   
   cat > scripts/custom-setup.sh << 'EOF'
   #!/bin/bash
   
   # Custom setup script for advanced execution environment
   
   # Create a custom ansible.cfg
   mkdir -p /etc/ansible
   cat > /etc/ansible/ansible.cfg << 'EOL'
   [defaults]
   host_key_checking = False
   retry_files_enabled = False
   stdout_callback = yaml
   bin_ansible_callbacks = True
   
   [ssh_connection]
   pipelining = True
   EOL
   
   # Create a custom facts directory
   mkdir -p /etc/ansible/facts.d
   
   # Create a custom fact
   cat > /etc/ansible/facts.d/ee_info.fact << 'EOL'
   #!/bin/bash
   
   echo "{\"execution_environment\": {\"name\": \"advanced-ee\", \"version\": \"1.0\", \"build_date\": \"$(date -u +%Y-%m-%d)\"}}"
   EOL
   
   chmod +x /etc/ansible/facts.d/ee_info.fact
   
   echo "Custom setup completed"
   EOF
   
   chmod +x scripts/custom-setup.sh
   ```

6. **Build the advanced execution environment**
   ```bash
   ansible-builder build -t advanced-ee:1.0 -f advanced-ee.yml --container-runtime podman
   ```

7. **Create a script to test the advanced execution environment**
   ```bash
   cat > test-advanced-ee.sh << 'EOF'
   #!/bin/bash
   
   # Create a test playbook
   cat > advanced-test-playbook.yml << 'EOL'
   ---
   - name: Test Advanced Execution Environment
     hosts: localhost
     gather_facts: true
     
     tasks:
       - name: Display Python version
         command: python3 --version
         register: python_version
         changed_when: false
       
       - name: Display Python version
         debug:
           var: python_version.stdout
       
       - name: Display installed collections
         command: ansible-galaxy collection list
         register: collections
         changed_when: false
       
       - name: Show collections
         debug:
           var: collections.stdout_lines
       
       - name: Check for custom ansible.cfg
         command: cat /etc/ansible/ansible.cfg
         register: ansible_cfg
         changed_when: false
       
       - name: Show ansible.cfg
         debug:
           var: ansible_cfg.stdout_lines
       
       - name: Check for custom fact
         command: cat /etc/ansible/facts.d/ee_info.fact
         register: custom_fact
         changed_when: false
       
       - name: Show custom fact
         debug:
           var: custom_fact.stdout_lines
       
       - name: Check for ee-info.txt
         command: cat /etc/ee-info.txt
         register: ee_info
         changed_when: false
       
       - name: Show ee-info.txt
         debug:
           var: ee_info.stdout
   EOL
   
   # Run the playbook using the execution environment
   podman run --rm -v $(pwd):/runner -w /runner advanced-ee:1.0 ansible-playbook advanced-test-playbook.yml
   EOF
   
   chmod +x test-advanced-ee.sh
   ```

8. **Run the test script**
   ```bash
   ./test-advanced-ee.sh
   ```

9. **Commit your changes**
   ```bash
   git add advanced-ee.yml advanced-requirements.txt bindep.txt advanced-requirements.yml scripts test-advanced-ee.sh
   git commit -m "Add advanced execution environment example"
   ```

## Exercise 4: Using Execution Environments with ansible-navigator

In this exercise, you'll learn how to use execution environments with ansible-navigator.

1. **Install ansible-navigator if not already installed**
   ```bash
   pip install ansible-navigator
   ```

2. **Create an ansible-navigator configuration file**
   ```bash
   cat > navigator.yml << 'EOF'
   ---
   ansible-navigator:
     execution-environment:
       container-engine: podman
       image: advanced-ee:1.0
       pull:
         policy: missing
     playbook-artifact:
       enable: true
       save-as: '{playbook_dir}/{playbook_name}-artifact-{time_stamp}.json'
   EOF
   ```

3. **Create a playbook to run with ansible-navigator**
   ```bash
   cat > navigator-playbook.yml << 'EOF'
   ---
   - name: Test Ansible Navigator with Execution Environment
     hosts: localhost
     gather_facts: true
     
     tasks:
       - name: Display Ansible version
         debug:
           msg: "Ansible version: {{ ansible_version.full }}"
       
       - name: Display Python version
         command: python3 --version
         register: python_version
         changed_when: false
       
       - name: Show Python version
         debug:
           var: python_version.stdout
       
       - name: Display distribution
         debug:
           msg: "Distribution: {{ ansible_distribution }} {{ ansible_distribution_version }}"
       
       - name: Display execution environment info
         debug:
           msg: "Running in execution environment: advanced-ee:1.0"
   EOF
   ```

4. **Create a script to run the playbook with ansible-navigator**
   ```bash
   cat > run-navigator.sh << 'EOF'
   #!/bin/bash
   
   # Run the playbook using ansible-navigator
   ansible-navigator run navigator-playbook.yml \
     --eei advanced-ee:1.0 \
     --mode stdout \
     --playbook-artifact-enable true
   
   # Show the playbook artifact
   echo "Playbook artifact created:"
   ls -la *-artifact-*.json
   EOF
   
   chmod +x run-navigator.sh
   ```

5. **Run the script**
   ```bash
   ./run-navigator.sh
   ```

6. **Create a document explaining ansible-navigator**
   ```bash
   cat > ansible_navigator_guide.md << 'EOF'
   # Ansible Navigator Guide

   ## Overview

   Ansible Navigator is a command-line tool and text-based user interface (TUI) for Ansible. It's designed to help you use, troubleshoot, and understand Ansible content, especially when working with execution environments.

   ## Installation

   ```bash
   pip install ansible-navigator
   ```

   ## Key Commands

   - **run**: Run a playbook
   - **inventory**: Explore inventory
   - **config**: View Ansible configuration
   - **collections**: Explore collections
   - **doc**: View documentation
   - **images**: Manage execution environment images

   ## Command Options

   - **--eei, --execution-environment-image**: Specify the execution environment image
   - **--mode**: Set the display mode (interactive, stdout)
   - **--help-config**: Show configuration help
   - **--playbook-artifact-enable**: Enable playbook artifacts

   ## Configuration File

   Ansible Navigator can be configured using a YAML file, typically named `navigator.yml`:

   ```yaml
   ---
   ansible-navigator:
     execution-environment:
       container-engine: podman
       image: my-ee:1.0
       pull:
         policy: missing
     playbook-artifact:
       enable: true
       save-as: '{playbook_dir}/{playbook_name}-artifact-{time_stamp}.json'
   ```

   ## Interactive Mode

   In interactive mode, Ansible Navigator provides a TUI with various screens:

   - **Welcome**: Initial screen with information
   - **Playbook**: Shows playbook execution
   - **Task**: Shows task details
   - **Result**: Shows task results
   - **Collections**: Shows installed collections
   - **Documentation**: Shows documentation

   ## Keyboard Shortcuts

   - **:help**: Show help
   - **:quit**: Quit
   - **:doc**: Show documentation
   - **:collections**: Show collections
   - **:inventory**: Show inventory
   - **:config**: Show configuration

   ## Best Practices

   - Use a configuration file for consistent settings
   - Enable playbook artifacts for troubleshooting
   - Use execution environments for consistent execution
   - Explore the TUI to learn more about Ansible content
   EOF
   ```

7. **Commit your changes**
   ```bash
   git add navigator.yml navigator-playbook.yml run-navigator.sh ansible_navigator_guide.md
   git commit -m "Add ansible-navigator examples"
   ```

## Exercise 5: Managing Execution Environments in Automation Controller

In this exercise, you'll learn how to manage execution environments in Automation Controller.

1. **Create a document explaining execution environments in Automation Controller**
   ```bash
   cat > ee_in_controller.md << 'EOF'
   # Execution Environments in Automation Controller

   ## Overview

   Automation Controller (formerly Tower) uses execution environments to run Ansible automation. Execution environments provide a consistent and isolated environment for running playbooks, regardless of where they are executed.

   ## Adding Execution Environments to Controller

   1. **Navigate to Administration > Execution Environments**
   2. **Click "Add"**
   3. **Fill in the form**:
      - **Name**: A descriptive name for the execution environment
      - **Image**: The container image name (e.g., `registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest`)
      - **Pull**: The image pull policy (Always, Never, If Not Present)
      - **Description**: A description of the execution environment
   4. **Click "Save"**

   ## Using Execution Environments in Controller

   Execution environments can be specified at different levels:

   1. **Global Level**: Set in Settings > Jobs > Default Execution Environment
   2. **Organization Level**: Set in Organizations > Edit > Default Execution Environment
   3. **Project Level**: Set in Projects > Edit > Default Execution Environment
   4. **Job Template Level**: Set in Job Templates > Edit > Execution Environment

   The most specific level takes precedence.

   ## Creating Custom Execution Environments for Controller

   1. **Build a custom execution environment**:
      ```bash
      ansible-builder build -t my-custom-ee:1.0 -f execution-environment.yml
      ```

   2. **Push the image to a container registry**:
      ```bash
      podman push my-custom-ee:1.0 registry.example.com/my-custom-ee:1.0
      ```

   3. **Add the execution environment to Controller**:
      - Navigate to Administration > Execution Environments
      - Click "Add"
      - Set the image to `registry.example.com/my-custom-ee:1.0`
      - Configure pull credentials if needed
      - Click "Save"

   ## Managing Execution Environment Credentials

   If your container registry requires authentication:

   1. **Navigate to Credentials**
   2. **Click "Add"**
   3. **Select "Container Registry" credential type**
   4. **Fill in the form**:
      - **Name**: A descriptive name
      - **Username**: Registry username
      - **Password**: Registry password
      - **Host**: Registry hostname
   5. **Click "Save"**

   Then, associate this credential with your execution environment.

   ## Best Practices

   - **Version your execution environments** using tags
   - **Document the contents** of each execution environment
   - **Test execution environments** before using them in production
   - **Use a private container registry** for custom execution environments
   - **Standardize on a small number** of execution environments
   - **Include only necessary dependencies** to keep images small
   EOF
   ```

2. **Create a sample execution environment definition for Controller**
   ```bash
   mkdir -p controller-ee
   
   cat > controller-ee/execution-environment.yml << 'EOF'
   ---
   version: 1
   
   build_arg_defaults:
     EE_BASE_IMAGE: 'registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest'
   
   dependencies:
     python: requirements.txt
     system: bindep.txt
     galaxy: requirements.yml
   
   additional_build_steps:
     prepend: |
       RUN pip3 install --upgrade pip setuptools wheel
     append: |
       RUN echo "Controller Execution Environment" > /etc/ee-info.txt
   EOF
   
   cat > controller-ee/requirements.txt << 'EOF'
   # Python package requirements
   PyYAML>=6.0
   jmespath>=1.0.1
   netaddr>=0.8.0
   requests>=2.25.0
   EOF
   
   cat > controller-ee/bindep.txt << 'EOF'
   # System package dependencies
   gcc [platform:rpm]
   python3-devel [platform:rpm]
   EOF
   
   cat > controller-ee/requirements.yml << 'EOF'
   ---
   collections:
     - name: ansible.posix
       version: '>=1.3.0'
     - name: community.general
       version: '>=5.0.0'
   EOF
   
   cat > controller-ee/README.md << 'EOF'
   # Controller Execution Environment
   
   This is a custom execution environment for Automation Controller.
   
   ## Contents
   
   - Base Image: registry.redhat.io/ansible-automation-platform-24/ee-minimal-rhel8:latest
   - Python Packages: PyYAML, jmespath, netaddr, requests
   - System Packages: gcc, python3-devel
   - Ansible Collections: ansible.posix, community.general
   
   ## Building
   
   ```bash
   ansible-builder build -t controller-ee:1.0 -f execution-environment.yml
   ```
   
   ## Pushing to Registry
   
   ```bash
   podman push controller-ee:1.0 registry.example.com/controller-ee:1.0
   ```
   
   ## Using in Controller
   
   1. Add the execution environment in Controller
   2. Associate it with job templates
   EOF
   
   cat > controller-ee/build.sh << 'EOF'
   #!/bin/bash
   
   # Build the execution environment
   ansible-builder build -t controller-ee:1.0 -f execution-environment.yml --container-runtime podman
   
   # Display the built image
   echo "Built image:"
   podman images controller-ee:1.0
   
   # Instructions for pushing to a registry
   echo "To push to a registry:"
   echo "podman push controller-ee:1.0 registry.example.com/controller-ee:1.0"
   EOF
   
   chmod +x controller-ee/build.sh
   ```

3. **Commit your changes**
   ```bash
   git add ee_in_controller.md controller-ee
   git commit -m "Add execution environments in Controller documentation"
   ```

## Exercise 6: Execution Environment Best Practices

In this exercise, you'll learn best practices for working with execution environments.

1. **Create a document with execution environment best practices**
   ```bash
   cat > ee_best_practices.md << 'EOF'
   # Execution Environment Best Practices

   ## Design Principles

   1. **Minimalism**: Include only what's necessary
   2. **Specificity**: Create purpose-specific execution environments
   3. **Versioning**: Version execution environments like any other code
   4. **Documentation**: Document the contents and purpose
   5. **Testing**: Test execution environments before deployment

   ## Naming Conventions

   - Use descriptive names: `purpose-platform-version`
   - Examples:
     - `network-rhel8-1.0`
     - `aws-rhel8-2.1`
     - `windows-ubi8-3.0`

   ## Versioning Strategy

   - Use semantic versioning (MAJOR.MINOR.PATCH)
   - Increment MAJOR for incompatible changes
   - Increment MINOR for new functionality
   - Increment PATCH for bug fixes
   - Tag images with version: `my-ee:1.0.0`

   ## Dependency Management

   - Pin dependencies to specific versions
   - Use requirements files for all dependencies
   - Regularly update dependencies for security
   - Test dependency changes before deployment

   ## Security Considerations

   - Use trusted base images
   - Scan images for vulnerabilities
   - Minimize the number of installed packages
   - Remove development tools in production images
   - Use non-root users when possible

   ## Performance Optimization

   - Keep images small
   - Use multi-stage builds
   - Remove unnecessary files
   - Combine RUN commands to reduce layers

   ## Documentation

   Document the following for each execution environment:

   - Purpose and use cases
   - Base image and version
   - Included collections and versions
   - Python dependencies
   - System dependencies
   - Custom configurations
   - Build and usage instructions

   ## Testing

   Test execution environments for:

   - Build success
   - Basic functionality
   - Specific use cases
   - Compatibility with Automation Controller
   - Performance and size

   ## CI/CD Integration

   - Automate builds with CI/CD pipelines
   - Test execution environments in CI/CD
   - Publish to a container registry
   - Tag with build information
   - Implement promotion between environments

   ## Maintenance

   - Regularly rebuild with updated dependencies
   - Monitor for security vulnerabilities
   - Deprecate and remove old versions
   - Document changes between versions
   - Test upgrades between versions
   EOF
   ```

2. **Create a sample CI/CD pipeline for execution environments**
   ```bash
   mkdir -p ci-cd
   
   cat > ci-cd/gitlab-ci.yml << 'EOF'
   ---
   stages:
     - build
     - test
     - publish
     - deploy
   
   variables:
     EE_NAME: my-execution-environment
     EE_VERSION: 1.0.0
     EE_REGISTRY: registry.example.com
   
   build:
     stage: build
     image: registry.redhat.io/ansible-automation-platform-24/ansible-builder-rhel8:latest
     script:
       - ansible-builder build -t ${EE_NAME}:${EE_VERSION} -f execution-environment.yml
       - podman save -o ${EE_NAME}-${EE_VERSION}.tar ${EE_NAME}:${EE_VERSION}
     artifacts:
       paths:
         - ${EE_NAME}-${EE_VERSION}.tar
   
   test:
     stage: test
     image: registry.redhat.io/ansible-automation-platform-24/ansible-runner-rhel8:latest
     script:
       - podman load -i ${EE_NAME}-${EE_VERSION}.tar
       - ansible-navigator run test-playbook.yml --eei ${EE_NAME}:${EE_VERSION} --mode stdout
     dependencies:
       - build
   
   publish:
     stage: publish
     image: registry.redhat.io/ansible-automation-platform-24/ansible-builder-rhel8:latest
     script:
       - podman load -i ${EE_NAME}-${EE_VERSION}.tar
       - podman tag ${EE_NAME}:${EE_VERSION} ${EE_REGISTRY}/${EE_NAME}:${EE_VERSION}
       - podman tag ${EE_NAME}:${EE_VERSION} ${EE_REGISTRY}/${EE_NAME}:latest
       - podman login ${EE_REGISTRY} -u ${REGISTRY_USER} -p ${REGISTRY_PASSWORD}
       - podman push ${EE_REGISTRY}/${EE_NAME}:${EE_VERSION}
       - podman push ${EE_REGISTRY}/${EE_NAME}:latest
     dependencies:
       - build
     only:
       - main
   
   deploy:
     stage: deploy
     image: registry.redhat.io/ansible-automation-platform-24/ansible-runner-rhel8:latest
     script:
       - ansible-playbook update-controller-ee.yml -e "ee_name=${EE_NAME} ee_version=${EE_VERSION} ee_registry=${EE_REGISTRY}"
     dependencies: []
     only:
       - main
   EOF
   
   cat > ci-cd/update-controller-ee.yml << 'EOF'
   ---
   - name: Update Execution Environment in Controller
     hosts: localhost
     gather_facts: false
     vars:
       controller_host: https://controller.example.com
       controller_user: admin
       controller_password: "{{ lookup('env', 'CONTROLLER_PASSWORD') }}"
       ee_name: my-execution-environment
       ee_version: 1.0.0
       ee_registry: registry.example.com
     
     tasks:
       - name: Update execution environment in Controller
         uri:
           url: "{{ controller_host }}/api/v2/execution_environments/"
           method: POST
           user: "{{ controller_user }}"
           password: "{{ controller_password }}"
           force_basic_auth: true
           validate_certs: false
           status_code: [200, 201]
           body_format: json
           body:
             name: "{{ ee_name }}"
             image: "{{ ee_registry }}/{{ ee_name }}:{{ ee_version }}"
             pull: "always"
             description: "Updated by CI/CD pipeline on {{ ansible_date_time.iso8601 }}"
         register: ee_result
         ignore_errors: true
       
       - name: Show result
         debug:
           var: ee_result
   EOF
   
   cat > ci-cd/README.md << 'EOF'
   # CI/CD Pipeline for Execution Environments

   This directory contains sample CI/CD pipeline configurations for building, testing, publishing, and deploying execution environments.

   ## GitLab CI/CD

   The `gitlab-ci.yml` file defines a pipeline with the following stages:

   1. **Build**: Build the execution environment
   2. **Test**: Test the execution environment
   3. **Publish**: Publish the execution environment to a container registry
   4. **Deploy**: Update the execution environment in Automation Controller

   ## Requirements

   - GitLab CI/CD
   - Container registry
   - Automation Controller
   - CI/CD variables:
     - `REGISTRY_USER`: Container registry username
     - `REGISTRY_PASSWORD`: Container registry password
     - `CONTROLLER_PASSWORD`: Automation Controller password

   ## Usage

   1. Place the `gitlab-ci.yml` file in your repository as `.gitlab-ci.yml`
   2. Configure the required CI/CD variables
   3. Push to GitLab
   EOF
   ```

3. **Commit your changes**
   ```bash
   git add ee_best_practices.md ci-cd
   git commit -m "Add execution environment best practices and CI/CD examples"
   ```

## Conclusion

In this lab, you've learned how to:
- Understand execution environments and their components
- Use ansible-builder to create basic and advanced execution environments
- Use ansible-navigator to run playbooks with execution environments
- Manage execution environments in Automation Controller
- Apply best practices for working with execution environments
- Implement CI/CD pipelines for execution environments

These skills are essential for the "Create and use execution environments" objective of the EX374 exam.

## Additional Resources

- [Ansible Builder Documentation](https://ansible-builder.readthedocs.io/en/latest/)
- [Ansible Navigator Documentation](https://ansible-navigator.readthedocs.io/en/latest/)
- [Execution Environments in Automation Controller Documentation](https://docs.ansible.com/automation-controller/latest/html/userguide/execution_environments.html)
- [Container Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [Ansible Automation Platform Documentation](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/)
