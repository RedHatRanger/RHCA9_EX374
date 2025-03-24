# Lab: Automation Controller Management

This lab covers the "Manage Automation Controller" objective of the EX374 exam. You'll learn how to configure and manage Ansible Automation Platform's Automation Controller (formerly Tower).

## Prerequisites

- Ansible Automation Platform 2.4 installed according to the [AAP Installation Guide](../guides/aap_installation_guide.md)
- Completion of the previous labs

## Lab Environment Setup

1. **Create a working directory**
   ```bash
   mkdir -p ~/ansible-labs/controller-lab
   cd ~/ansible-labs/controller-lab
   ```

2. **Initialize a Git repository**
   ```bash
   git init
   ```

## Exercise 1: Understanding Automation Controller

In this exercise, you'll learn about Automation Controller concepts and architecture.

1. **Create a document explaining Automation Controller concepts**
   ```bash
   cat > controller_concepts.md << 'EOF'
   # Automation Controller Concepts

   ## What is Automation Controller?

   Automation Controller (formerly Tower) is a web-based solution that makes Ansible more accessible to teams by providing a user interface, role-based access control, job scheduling, and graphical inventory management.

   ## Key Components

   - **Web UI**: Browser-based interface for managing automation
   - **REST API**: Programmatic interface for automation
   - **CLI**: Command-line interface for automation
   - **Database**: PostgreSQL database for storing configuration and results
   - **Task Engine**: Executes Ansible playbooks
   - **Event System**: Tracks and reports on job execution

   ## Core Features

   - **Job Templates**: Reusable job definitions
   - **Workflows**: Multi-step automation sequences
   - **Inventories**: Host and group management
   - **Credentials**: Secure credential management
   - **Projects**: Source control integration
   - **Organizations**: Multi-tenant support
   - **Teams**: User group management
   - **Roles**: Role-based access control
   - **Notifications**: Event-based notifications
   - **Scheduling**: Time-based job execution
   - **Surveys**: Interactive forms for job parameters

   ## Architecture

   Automation Controller follows a distributed architecture:

   - **Control Plane**: Manages the automation platform
     - Web UI
     - API
     - Task Dispatcher
     - Database
   - **Execution Plane**: Executes automation tasks
     - Automation Mesh Nodes
     - Execution Environments

   ## Automation Mesh

   Automation Mesh is a network overlay that connects control and execution nodes:

   - **Control Nodes**: Manage the automation platform
   - **Hybrid Nodes**: Both control and execution capabilities
   - **Execution Nodes**: Execute automation tasks
   - **Hop Nodes**: Route traffic between nodes
   - **Edge Nodes**: Connect to isolated networks

   ## Execution Environments

   Execution Environments are containerized environments for running Ansible:

   - **Consistent**: Same environment across development and production
   - **Isolated**: Separate dependencies for different projects
   - **Portable**: Run the same automation anywhere
   - **Versioned**: Version control for the entire automation environment
   EOF
   ```

2. **Create a document explaining Automation Controller architecture**
   ```bash
   cat > controller_architecture.md << 'EOF'
   # Automation Controller Architecture

   ## High-Level Architecture

   ```
   +----------------------------------+
   |          Web Browser             |
   +----------------------------------+
                   |
                   v
   +----------------------------------+
   |         Load Balancer            |
   +----------------------------------+
                   |
                   v
   +----------------------------------+
   |     Automation Controller        |
   |                                  |
   |  +----------------------------+  |
   |  |         Web UI            |  |
   |  +----------------------------+  |
   |  +----------------------------+  |
   |  |         REST API          |  |
   |  +----------------------------+  |
   |  +----------------------------+  |
   |  |      Task Dispatcher      |  |
   |  +----------------------------+  |
   +----------------------------------+
                   |
         +---------+---------+
         |                   |
         v                   v
   +------------+     +------------+
   | PostgreSQL |     | Execution  |
   | Database   |     | Environment|
   +------------+     +------------+
   ```

   ## Component Details

   ### Web UI

   The Web UI provides a graphical interface for:
   - Managing inventories, projects, and job templates
   - Launching and monitoring jobs
   - Managing users, teams, and permissions
   - Viewing reports and dashboards

   ### REST API

   The REST API provides programmatic access to all Automation Controller functionality:
   - CRUD operations for all resources
   - Job launching and monitoring
   - Authentication and authorization
   - Webhooks for integration with external systems

   ### Task Dispatcher

   The Task Dispatcher:
   - Queues and schedules jobs
   - Assigns jobs to execution nodes
   - Monitors job status
   - Handles job timeouts and failures

   ### PostgreSQL Database

   The PostgreSQL database stores:
   - Configuration data
   - Inventory data
   - Job results
   - User and permission data
   - Credentials (encrypted)

   ### Execution Environment

   Execution Environments are containerized environments that:
   - Run Ansible playbooks
   - Include Ansible Core
   - Include required collections
   - Include required dependencies

   ## Scalability

   Automation Controller can scale in several ways:

   - **Horizontal Scaling**: Add more controller nodes
   - **Vertical Scaling**: Add more resources to existing nodes
   - **Database Scaling**: Scale the PostgreSQL database
   - **Execution Scaling**: Add more execution nodes

   ## High Availability

   For high availability, Automation Controller can be configured with:

   - **Multiple Controller Nodes**: For redundancy
   - **Database Replication**: For database redundancy
   - **Load Balancing**: For distributing traffic
   - **Failover**: For automatic recovery
   EOF
   ```

3. **Commit your changes**
   ```bash
   git add controller_concepts.md controller_architecture.md
   git commit -m "Add Automation Controller concepts and architecture documents"
   ```

## Exercise 2: Configuring Automation Controller

In this exercise, you'll learn how to configure Automation Controller.

1. **Create a document explaining Automation Controller configuration**
   ```bash
   cat > controller_configuration.md << 'EOF'
   # Automation Controller Configuration

   ## Initial Setup

   After installation, Automation Controller requires initial configuration:

   1. **Access the Web UI**: Navigate to `https://<controller-hostname>/`
   2. **Log in**: Use the admin credentials created during installation
   3. **Accept the License**: Review and accept the license agreement
   4. **Create an Organization**: Create your first organization
   5. **Create Users**: Create additional users as needed
   6. **Configure Settings**: Adjust system settings as needed

   ## System Settings

   System settings can be configured in the UI:

   1. **Navigate to Settings > System**
   2. **Adjust settings as needed**:
      - **Base URL**: The URL used to access Automation Controller
      - **Default Execution Environment**: The default execution environment for jobs
      - **Job Concurrency**: The number of jobs that can run concurrently
      - **Job Timeout**: The default timeout for jobs
      - **Idle Instance Timeout**: The timeout for idle instances
      - **Logging Aggregation**: Settings for log aggregation
      - **Proxy Settings**: Settings for HTTP/HTTPS proxy

   ## Authentication Settings

   Authentication settings can be configured in the UI:

   1. **Navigate to Settings > Authentication**
   2. **Configure authentication methods**:
      - **LDAP/Active Directory**: Integration with LDAP or Active Directory
      - **SAML**: Integration with SAML identity providers
      - **OAuth2**: Integration with OAuth2 providers
      - **RADIUS**: Integration with RADIUS servers
      - **TACACS+**: Integration with TACACS+ servers

   ## License Management

   License management can be performed in the UI:

   1. **Navigate to Settings > License**
   2. **View license information**:
      - **License Type**: The type of license
      - **Subscription**: The subscription details
      - **Features**: The licensed features
      - **Hosts**: The number of licensed hosts
      - **Expiration**: The license expiration date
   3. **Import a new license** if needed

   ## Job Settings

   Job settings can be configured in the UI:

   1. **Navigate to Settings > Jobs**
   2. **Adjust job settings**:
      - **Default Execution Environment**: The default execution environment for jobs
      - **Default Job Timeout**: The default timeout for jobs
      - **Default Inventory Update Timeout**: The default timeout for inventory updates
      - **Default Project Update Timeout**: The default timeout for project updates
      - **Default Job Concurrency**: The default number of jobs that can run concurrently
      - **Job Event Processing**: Settings for job event processing

   ## User Interface Settings

   User interface settings can be configured in the UI:

   1. **Navigate to Settings > User Interface**
   2. **Adjust UI settings**:
      - **Logo**: Custom logo for the UI
      - **Login Info**: Custom login information
      - **Custom Login URL**: Custom URL for login
      - **Custom Logout URL**: Custom URL for logout
      - **Idle Timeout**: Timeout for idle UI sessions

   ## Logging Settings

   Logging settings can be configured in the UI:

   1. **Navigate to Settings > Logging**
   2. **Adjust logging settings**:
      - **Log Level**: The level of logging detail
      - **Log Aggregation**: Settings for log aggregation
      - **Log Rotation**: Settings for log rotation
      - **External Logging**: Settings for external logging systems

   ## Configuration via API

   All configuration can also be performed via the REST API:

   ```bash
   # Get system settings
   curl -k -H "Authorization: Bearer TOKEN" https://controller.example.com/api/v2/settings/system/

   # Update system settings
   curl -k -X PATCH -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"TOWER_URL_BASE": "https://controller.example.com"}' \
     https://controller.example.com/api/v2/settings/system/
   ```

   ## Configuration via CLI

   Configuration can also be performed via the `awx` CLI:

   ```bash
   # Get system settings
   awx settings list

   # Update system settings
   awx settings modify TOWER_URL_BASE=https://controller.example.com
   ```

   ## Configuration via Ansible

   Configuration can also be performed via Ansible:

   ```yaml
   - name: Configure Automation Controller
     hosts: localhost
     gather_facts: false
     
     tasks:
       - name: Update system settings
         awx.awx.tower_settings:
           settings:
             TOWER_URL_BASE: https://controller.example.com
           tower_host: https://controller.example.com
           tower_username: admin
           tower_password: password
   ```
   EOF
   ```

2. **Create a sample Automation Controller configuration playbook**
   ```bash
   cat > configure_controller.yml << 'EOF'
   ---
   - name: Configure Automation Controller
     hosts: localhost
     gather_facts: false
     
     vars:
       controller_host: https://controller.example.com
       controller_username: admin
       controller_password: redhat
       controller_validate_certs: false
     
     tasks:
       - name: Configure system settings
         awx.awx.tower_settings:
           settings:
             TOWER_URL_BASE: "{{ controller_host }}"
             DEFAULT_EXECUTION_ENVIRONMENT: "Default execution environment"
             DEFAULT_JOB_TIMEOUT: 3600
             DEFAULT_INVENTORY_UPDATE_TIMEOUT: 1800
             DEFAULT_PROJECT_UPDATE_TIMEOUT: 1800
             AWX_TASK_ENV: {}
             AWX_TASK_EXTRA_VOLUME_MOUNTS: []
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
       
       - name: Configure job settings
         awx.awx.tower_settings:
           settings:
             AWX_ISOLATED_CHECK_INTERVAL: 1
             AWX_ISOLATED_LAUNCH_TIMEOUT: 600
             AWX_ISOLATED_CONNECTION_TIMEOUT: 10
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
       
       - name: Configure UI settings
         awx.awx.tower_settings:
           settings:
             CUSTOM_LOGIN_INFO: "Welcome to Automation Controller"
             CUSTOM_LOGO: ""
             MAX_UI_JOB_EVENTS: 2000
             UI_LIVE_UPDATES_ENABLED: true
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
   EOF
   ```

3. **Commit your changes**
   ```bash
   git add controller_configuration.md configure_controller.yml
   git commit -m "Add Automation Controller configuration documents"
   ```

## Exercise 3: Managing Organizations and Teams

In this exercise, you'll learn how to manage organizations and teams in Automation Controller.

1. **Create a document explaining organization and team management**
   ```bash
   cat > organizations_teams.md << 'EOF'
   # Managing Organizations and Teams

   ## Organizations

   Organizations are logical collections of teams, projects, inventories, and other resources.

   ### Creating an Organization

   To create an organization in the UI:

   1. **Navigate to Access > Organizations**
   2. **Click "Add"**
   3. **Fill in the form**:
      - **Name**: A unique name for the organization
      - **Description**: A description of the organization
      - **Max Hosts**: The maximum number of hosts for the organization
      - **Default Execution Environment**: The default execution environment for the organization
   4. **Click "Save"**

   To create an organization via the API:

   ```bash
   curl -k -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"name": "Example Organization", "description": "Example organization"}' \
     https://controller.example.com/api/v2/organizations/
   ```

   To create an organization via the CLI:

   ```bash
   awx organizations create --name "Example Organization" --description "Example organization"
   ```

   To create an organization via Ansible:

   ```yaml
   - name: Create organization
     awx.awx.tower_organization:
       name: Example Organization
       description: Example organization
       tower_host: https://controller.example.com
       tower_username: admin
       tower_password: password
   ```

   ### Managing Organization Resources

   Organizations can contain:

   - **Teams**: Groups of users
   - **Projects**: Source control repositories
   - **Inventories**: Host and group definitions
   - **Job Templates**: Reusable job definitions
   - **Credentials**: Authentication credentials
   - **Notification Templates**: Notification configurations

   ### Organization Permissions

   Organization permissions can be managed in the UI:

   1. **Navigate to Access > Organizations**
   2. **Select an organization**
   3. **Click "Access" tab**
   4. **Click "Add"**
   5. **Select users or teams**
   6. **Select roles**
   7. **Click "Save"**

   ## Teams

   Teams are groups of users that can be assigned permissions as a unit.

   ### Creating a Team

   To create a team in the UI:

   1. **Navigate to Access > Teams**
   2. **Click "Add"**
   3. **Fill in the form**:
      - **Name**: A unique name for the team
      - **Description**: A description of the team
      - **Organization**: The organization the team belongs to
   4. **Click "Save"**

   To create a team via the API:

   ```bash
   curl -k -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"name": "Example Team", "description": "Example team", "organization": 1}' \
     https://controller.example.com/api/v2/teams/
   ```

   To create a team via the CLI:

   ```bash
   awx teams create --name "Example Team" --description "Example team" --organization 1
   ```

   To create a team via Ansible:

   ```yaml
   - name: Create team
     awx.awx.tower_team:
       name: Example Team
       description: Example team
       organization: Example Organization
       tower_host: https://controller.example.com
       tower_username: admin
       tower_password: password
   ```

   ### Managing Team Members

   Team members can be managed in the UI:

   1. **Navigate to Access > Teams**
   2. **Select a team**
   3. **Click "Users" tab**
   4. **Click "Add"**
   5. **Select users**
   6. **Click "Save"**

   ### Team Permissions

   Team permissions can be managed in the UI:

   1. **Navigate to Access > Teams**
   2. **Select a team**
   3. **Click "Permissions" tab**
   4. **Click "Add"**
   5. **Select resources**
   6. **Select roles**
   7. **Click "Save"**

   ## Role-Based Access Control

   Automation Controller uses role-based access control (RBAC) to manage permissions.

   ### Roles

   Common roles include:

   - **Admin**: Full access to the resource
   - **Use**: Ability to use the resource
   - **Read**: Ability to view the resource
   - **Execute**: Ability to execute jobs using the resource
   - **Update**: Ability to update the resource
   - **Adhoc**: Ability to run ad-hoc commands on the resource
   - **Inventory Admin**: Full access to the inventory
   - **Project Admin**: Full access to the project
   - **Job Template Admin**: Full access to the job template
   - **Credential Admin**: Full access to the credential
   - **Organization Admin**: Full access to the organization
   - **System Administrator**: Full access to the system

   ### Permission Inheritance

   Permissions can be inherited:

   - **Organization**: Permissions granted at the organization level apply to all resources in the organization
   - **Team**: Permissions granted to a team apply to all members of the team
   - **Parent Resource**: Permissions granted to a parent resource apply to all child resources
   EOF
   ```

2. **Create a sample organization and team configuration playbook**
   ```bash
   cat > configure_organizations_teams.yml << 'EOF'
   ---
   - name: Configure Organizations and Teams
     hosts: localhost
     gather_facts: false
     
     vars:
       controller_host: https://controller.example.com
       controller_username: admin
       controller_password: redhat
       controller_validate_certs: false
     
     tasks:
       - name: Create organizations
         awx.awx.tower_organization:
           name: "{{ item.name }}"
           description: "{{ item.description }}"
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
         loop:
           - name: Production
             description: Production environment
           - name: Development
             description: Development environment
           - name: Testing
             description: Testing environment
       
       - name: Create teams
         awx.awx.tower_team:
           name: "{{ item.name }}"
           description: "{{ item.description }}"
           organization: "{{ item.organization }}"
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
         loop:
           - name: Administrators
             description: Administrator team
             organization: Production
           - name: Developers
             description: Developer team
             organization: Development
           - name: Testers
             description: Tester team
             organization: Testing
       
       - name: Create users
         awx.awx.tower_user:
           username: "{{ item.username }}"
           password: "{{ item.password }}"
           email: "{{ item.email }}"
           first_name: "{{ item.first_name }}"
           last_name: "{{ item.last_name }}"
           superuser: "{{ item.superuser | default(false) }}"
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
         loop:
           - username: admin_user
             password: redhat
             email: admin@example.com
             first_name: Admin
             last_name: User
             superuser: true
           - username: dev_user
             password: redhat
             email: dev@example.com
             first_name: Dev
             last_name: User
           - username: test_user
             password: redhat
             email: test@example.com
             first_name: Test
             last_name: User
       
       - name: Add users to teams
         awx.awx.tower_role:
           user: "{{ item.user }}"
           team: "{{ item.team }}"
           role: "{{ item.role }}"
           organization: "{{ item.organization }}"
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
         loop:
           - user: admin_user
             team: Administrators
             role: admin
             organization: Production
           - user: dev_user
             team: Developers
             role: member
             organization: Development
           - user: test_user
             team: Testers
             role: member
             organization: Testing
   EOF
   ```

3. **Commit your changes**
   ```bash
   git add organizations_teams.md configure_organizations_teams.yml
   git commit -m "Add organization and team management documents"
   ```

## Exercise 4: Managing Projects and Job Templates

In this exercise, you'll learn how to manage projects and job templates in Automation Controller.

1. **Create a document explaining project and job template management**
   ```bash
   cat > projects_job_templates.md << 'EOF'
   # Managing Projects and Job Templates

   ## Projects

   Projects are logical collections of Ansible playbooks in Automation Controller.

   ### Creating a Project

   To create a project in the UI:

   1. **Navigate to Resources > Projects**
   2. **Click "Add"**
   3. **Fill in the form**:
      - **Name**: A unique name for the project
      - **Description**: A description of the project
      - **Organization**: The organization the project belongs to
      - **Execution Environment**: The execution environment for the project
      - **Source Control Type**: The type of source control (Git, Subversion, etc.)
      - **Source Control URL**: The URL of the source control repository
      - **Source Control Branch/Tag/Commit**: The branch, tag, or commit to use
      - **Source Control Credential**: The credential for accessing the repository
      - **Update Options**: Options for updating the project
   4. **Click "Save"**

   To create a project via the API:

   ```bash
   curl -k -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"name": "Example Project", "description": "Example project", "organization": 1, "scm_type": "git", "scm_url": "https://github.com/example/example.git"}' \
     https://controller.example.com/api/v2/projects/
   ```

   To create a project via the CLI:

   ```bash
   awx projects create --name "Example Project" --description "Example project" --organization 1 --scm_type git --scm_url https://github.com/example/example.git
   ```

   To create a project via Ansible:

   ```yaml
   - name: Create project
     awx.awx.tower_project:
       name: Example Project
       description: Example project
       organization: Example Organization
       scm_type: git
       scm_url: https://github.com/example/example.git
       tower_host: https://controller.example.com
       tower_username: admin
       tower_password: password
   ```

   ### Updating a Project

   To update a project in the UI:

   1. **Navigate to Resources > Projects**
   2. **Select a project**
   3. **Click "Update"**

   To update a project via the API:

   ```bash
   curl -k -X POST -H "Authorization: Bearer TOKEN" \
     https://controller.example.com/api/v2/projects/1/update/
   ```

   To update a project via the CLI:

   ```bash
   awx projects update 1
   ```

   To update a project via Ansible:

   ```yaml
   - name: Update project
     awx.awx.tower_project_update:
       project: Example Project
       tower_host: https://controller.example.com
       tower_username: admin
       tower_password: password
   ```

   ## Job Templates

   Job templates are reusable job definitions in Automation Controller.

   ### Creating a Job Template

   To create a job template in the UI:

   1. **Navigate to Resources > Templates**
   2. **Click "Add" > "Add job template"**
   3. **Fill in the form**:
      - **Name**: A unique name for the job template
      - **Description**: A description of the job template
      - **Job Type**: The type of job (Run, Check, Scan)
      - **Inventory**: The inventory to use
      - **Project**: The project to use
      - **Execution Environment**: The execution environment to use
      - **Playbook**: The playbook to run
      - **Credentials**: The credentials to use
      - **Variables**: Extra variables for the job
      - **Limit**: Host pattern to limit execution to
      - **Verbosity**: The verbosity level
      - **Options**: Additional options for the job
   4. **Click "Save"**

   To create a job template via the API:

   ```bash
   curl -k -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"name": "Example Job Template", "description": "Example job template", "job_type": "run", "inventory": 1, "project": 1, "playbook": "playbook.yml", "credentials": [1]}' \
     https://controller.example.com/api/v2/job_templates/
   ```

   To create a job template via the CLI:

   ```bash
   awx job_templates create --name "Example Job Template" --description "Example job template" --job_type run --inventory 1 --project 1 --playbook playbook.yml
   ```

   To create a job template via Ansible:

   ```yaml
   - name: Create job template
     awx.awx.tower_job_template:
       name: Example Job Template
       description: Example job template
       job_type: run
       inventory: Example Inventory
       project: Example Project
       playbook: playbook.yml
       credentials:
         - Example Credential
       tower_host: https://controller.example.com
       tower_username: admin
       tower_password: password
   ```

   ### Launching a Job Template

   To launch a job template in the UI:

   1. **Navigate to Resources > Templates**
   2. **Select a job template**
   3. **Click "Launch"**

   To launch a job template via the API:

   ```bash
   curl -k -X POST -H "Authorization: Bearer TOKEN" \
     https://controller.example.com/api/v2/job_templates/1/launch/
   ```

   To launch a job template via the CLI:

   ```bash
   awx job_templates launch 1
   ```

   To launch a job template via Ansible:

   ```yaml
   - name: Launch job template
     awx.awx.tower_job_launch:
       job_template: Example Job Template
       tower_host: https://controller.example.com
       tower_username: admin
       tower_password: password
   ```

   ## Workflow Templates

   Workflow templates are multi-step job definitions in Automation Controller.

   ### Creating a Workflow Template

   To create a workflow template in the UI:

   1. **Navigate to Resources > Templates**
   2. **Click "Add" > "Add workflow template"**
   3. **Fill in the form**:
      - **Name**: A unique name for the workflow template
      - **Description**: A description of the workflow template
      - **Organization**: The organization the workflow template belongs to
      - **Inventory**: The inventory to use
      - **Variables**: Extra variables for the workflow
      - **Options**: Additional options for the workflow
   4. **Click "Save"**
   5. **Click "Visualizer" to design the workflow**

   To create a workflow template via the API:

   ```bash
   curl -k -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"name": "Example Workflow Template", "description": "Example workflow template", "organization": 1}' \
     https://controller.example.com/api/v2/workflow_job_templates/
   ```

   To create a workflow template via the CLI:

   ```bash
   awx workflow_job_templates create --name "Example Workflow Template" --description "Example workflow template" --organization 1
   ```

   To create a workflow template via Ansible:

   ```yaml
   - name: Create workflow template
     awx.awx.tower_workflow_template:
       name: Example Workflow Template
       description: Example workflow template
       organization: Example Organization
       tower_host: https://controller.example.com
       tower_username: admin
       tower_password: password
   ```

   ### Adding Nodes to a Workflow Template

   To add nodes to a workflow template in the UI:

   1. **Navigate to Resources > Templates**
   2. **Select a workflow template**
   3. **Click "Visualizer"**
   4. **Click "Start" to add the first node**
   5. **Select the job template or workflow template to run**
   6. **Configure the node**
   7. **Click "Save"**
   8. **Add additional nodes as needed**

   To add nodes to a workflow template via the API:

   ```bash
   curl -k -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"workflow_job_template": 1, "unified_job_template": 1, "identifier": "node1"}' \
     https://controller.example.com/api/v2/workflow_job_template_nodes/
   ```

   To add nodes to a workflow template via Ansible:

   ```yaml
   - name: Add node to workflow template
     awx.awx.tower_workflow_job_template_node:
       workflow_job_template: Example Workflow Template
       unified_job_template: Example Job Template
       identifier: node1
       tower_host: https://controller.example.com
       tower_username: admin
       tower_password: password
   ```

   ### Launching a Workflow Template

   To launch a workflow template in the UI:

   1. **Navigate to Resources > Templates**
   2. **Select a workflow template**
   3. **Click "Launch"**

   To launch a workflow template via the API:

   ```bash
   curl -k -X POST -H "Authorization: Bearer TOKEN" \
     https://controller.example.com/api/v2/workflow_job_templates/1/launch/
   ```

   To launch a workflow template via the CLI:

   ```bash
   awx workflow_job_templates launch 1
   ```

   To launch a workflow template via Ansible:

   ```yaml
   - name: Launch workflow template
     awx.awx.tower_workflow_job_launch:
       workflow_template: Example Workflow Template
       tower_host: https://controller.example.com
       tower_username: admin
       tower_password: password
   ```
   EOF
   ```

2. **Create a sample project and job template configuration playbook**
   ```bash
   cat > configure_projects_job_templates.yml << 'EOF'
   ---
   - name: Configure Projects and Job Templates
     hosts: localhost
     gather_facts: false
     
     vars:
       controller_host: https://controller.example.com
       controller_username: admin
       controller_password: redhat
       controller_validate_certs: false
     
     tasks:
       - name: Create projects
         awx.awx.tower_project:
           name: "{{ item.name }}"
           description: "{{ item.description }}"
           organization: "{{ item.organization }}"
           scm_type: "{{ item.scm_type }}"
           scm_url: "{{ item.scm_url }}"
           scm_branch: "{{ item.scm_branch | default('main') }}"
           scm_clean: "{{ item.scm_clean | default(true) }}"
           scm_update_on_launch: "{{ item.scm_update_on_launch | default(true) }}"
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
         loop:
           - name: Ansible Examples
             description: Ansible example playbooks
             organization: Production
             scm_type: git
             scm_url: https://github.com/ansible/ansible-examples.git
           - name: System Roles
             description: RHEL system roles
             organization: Production
             scm_type: git
             scm_url: https://github.com/linux-system-roles/auto-maintenance.git
       
       - name: Create inventories
         awx.awx.tower_inventory:
           name: "{{ item.name }}"
           description: "{{ item.description }}"
           organization: "{{ item.organization }}"
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
         loop:
           - name: Production Inventory
             description: Production environment inventory
             organization: Production
           - name: Development Inventory
             description: Development environment inventory
             organization: Development
       
       - name: Create inventory hosts
         awx.awx.tower_host:
           name: "{{ item.name }}"
           description: "{{ item.description }}"
           inventory: "{{ item.inventory }}"
           variables: "{{ item.variables | to_json }}"
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
         loop:
           - name: web1.example.com
             description: Production web server 1
             inventory: Production Inventory
             variables:
               ansible_host: 192.168.1.101
               ansible_user: rhel
           - name: db1.example.com
             description: Production database server
             inventory: Production Inventory
             variables:
               ansible_host: 192.168.1.201
               ansible_user: rhel
       
       - name: Create credentials
         awx.awx.tower_credential:
           name: "{{ item.name }}"
           description: "{{ item.description }}"
           organization: "{{ item.organization }}"
           credential_type: "{{ item.credential_type }}"
           inputs: "{{ item.inputs }}"
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
         loop:
           - name: Production SSH
             description: Production SSH key
             organization: Production
             credential_type: Machine
             inputs:
               username: ansible
               password: redhat
               become_method: sudo
               become_username: root
               become_password: redhat
       
       - name: Create job templates
         awx.awx.tower_job_template:
           name: "{{ item.name }}"
           description: "{{ item.description }}"
           job_type: "{{ item.job_type }}"
           inventory: "{{ item.inventory }}"
           project: "{{ item.project }}"
           playbook: "{{ item.playbook }}"
           credentials: "{{ item.credentials }}"
           extra_vars: "{{ item.extra_vars | to_json }}"
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
         loop:
           - name: Deploy Web Application
             description: Deploy web application to production
             job_type: run
             inventory: Production Inventory
             project: Ansible Examples
             playbook: lamp_haproxy/site.yml
             credentials:
               - Production SSH
             extra_vars:
               app_version: 1.0.0
               deploy_environment: production
       
       - name: Create workflow template
         awx.awx.tower_workflow_template:
           name: "{{ item.name }}"
           description: "{{ item.description }}"
           organization: "{{ item.organization }}"
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
         loop:
           - name: Production Deployment
             description: Production deployment workflow
             organization: Production
   EOF
   ```

3. **Commit your changes**
   ```bash
   git add projects_job_templates.md configure_projects_job_templates.yml
   git commit -m "Add project and job template management documents"
   ```

## Exercise 5: Managing Schedules and Notifications

In this exercise, you'll learn how to manage schedules and notifications in Automation Controller.

1. **Create a document explaining schedule and notification management**
   ```bash
   cat > schedules_notifications.md << 'EOF'
   # Managing Schedules and Notifications

   ## Schedules

   Schedules allow you to run jobs at specific times or intervals.

   ### Creating a Schedule

   To create a schedule in the UI:

   1. **Navigate to Resources > Templates**
   2. **Select a job template or workflow template**
   3. **Click "Schedules" tab**
   4. **Click "Add"**
   5. **Fill in the form**:
      - **Name**: A unique name for the schedule
      - **Description**: A description of the schedule
      - **Start Date/Time**: The date and time to start the schedule
      - **Timezone**: The timezone for the schedule
      - **Repeat Frequency**: How often to repeat the schedule
      - **End Date/Time**: The date and time to end the schedule (optional)
   6. **Click "Save"**

   To create a schedule via the API:

   ```bash
   curl -k -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"name": "Daily Backup", "description": "Daily backup schedule", "unified_job_template": 1, "rrule": "DTSTART:20230101T000000Z RRULE:FREQ=DAILY;INTERVAL=1"}' \
     https://controller.example.com/api/v2/schedules/
   ```

   To create a schedule via the CLI:

   ```bash
   awx schedules create --name "Daily Backup" --description "Daily backup schedule" --unified_job_template 1 --rrule "DTSTART:20230101T000000Z RRULE:FREQ=DAILY;INTERVAL=1"
   ```

   To create a schedule via Ansible:

   ```yaml
   - name: Create schedule
     awx.awx.tower_schedule:
       name: Daily Backup
       description: Daily backup schedule
       unified_job_template: Example Job Template
       rrule: "DTSTART:20230101T000000Z RRULE:FREQ=DAILY;INTERVAL=1"
       tower_host: https://controller.example.com
       tower_username: admin
       tower_password: password
   ```

   ### Schedule Recurrence Rules

   Schedules use iCalendar (RFC 5545) recurrence rules (RRULE) to define when jobs run.

   Common RRULE examples:

   - **Daily**: `RRULE:FREQ=DAILY;INTERVAL=1`
   - **Weekly on Monday**: `RRULE:FREQ=WEEKLY;INTERVAL=1;BYDAY=MO`
   - **Monthly on the 1st**: `RRULE:FREQ=MONTHLY;INTERVAL=1;BYMONTHDAY=1`
   - **Yearly on January 1**: `RRULE:FREQ=YEARLY;INTERVAL=1;BYMONTH=1;BYMONTHDAY=1`
   - **Weekdays at 8am**: `RRULE:FREQ=DAILY;INTERVAL=1;BYDAY=MO,TU,WE,TH,FR`

   ## Notifications

   Notifications allow you to send alerts when jobs start, succeed, or fail.

   ### Creating a Notification Template

   To create a notification template in the UI:

   1. **Navigate to Administration > Notifications**
   2. **Click "Add"**
   3. **Fill in the form**:
      - **Name**: A unique name for the notification template
      - **Description**: A description of the notification template
      - **Organization**: The organization the notification template belongs to
      - **Type**: The type of notification (Email, Slack, etc.)
      - **Configuration**: The configuration for the notification type
   4. **Click "Save"**

   To create a notification template via the API:

   ```bash
   curl -k -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"name": "Email Notification", "description": "Email notification template", "organization": 1, "notification_type": "email", "notification_configuration": {"username": "user", "password": "password", "host": "smtp.example.com", "port": 25, "sender": "rhel@example.com", "recipients": ["admin@example.com"], "use_tls": false, "use_ssl": false}}' \
     https://controller.example.com/api/v2/notification_templates/
   ```

   To create a notification template via the CLI:

   ```bash
   awx notification_templates create --name "Email Notification" --description "Email notification template" --organization 1 --notification_type email --notification_configuration '{"username": "user", "password": "password", "host": "smtp.example.com", "port": 25, "sender": "rhel@example.com", "recipients": ["admin@example.com"], "use_tls": false, "use_ssl": false}'
   ```

   To create a notification template via Ansible:

   ```yaml
   - name: Create notification template
     awx.awx.tower_notification_template:
       name: Email Notification
       description: Email notification template
       organization: Example Organization
       notification_type: email
       notification_configuration:
         username: user
         password: password
         host: smtp.example.com
         port: 25
         sender: rhel@example.com
         recipients:
           - admin@example.com
         use_tls: false
         use_ssl: false
       tower_host: https://controller.example.com
       tower_username: admin
       tower_password: password
   ```

   ### Associating Notifications with Job Templates

   To associate a notification template with a job template in the UI:

   1. **Navigate to Resources > Templates**
   2. **Select a job template**
   3. **Click "Notifications" tab**
   4. **Click "Add"**
   5. **Select a notification template**
   6. **Select notification events (started, success, failure, etc.)**
   7. **Click "Save"**

   To associate a notification template with a job template via the API:

   ```bash
   curl -k -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"notification_template": 1}' \
     https://controller.example.com/api/v2/job_templates/1/success_notifications/
   ```

   To associate a notification template with a job template via Ansible:

   ```yaml
   - name: Associate notification template with job template
     awx.awx.tower_job_template_notification:
       job_template: Example Job Template
       notification_template: Email Notification
       notification_type: success
       tower_host: https://controller.example.com
       tower_username: admin
       tower_password: password
   ```

   ### Notification Types

   Automation Controller supports several notification types:

   - **Email**: Send notifications via email
   - **Slack**: Send notifications to Slack channels
   - **Mattermost**: Send notifications to Mattermost channels
   - **Rocket.Chat**: Send notifications to Rocket.Chat channels
   - **PagerDuty**: Create incidents in PagerDuty
   - **Twilio**: Send SMS notifications via Twilio
   - **Webhook**: Send HTTP POST requests to a URL
   - **IRC**: Send notifications to IRC channels
   - **Microsoft Teams**: Send notifications to Microsoft Teams channels
   EOF
   ```

2. **Create a sample schedule and notification configuration playbook**
   ```bash
   cat > configure_schedules_notifications.yml << 'EOF'
   ---
   - name: Configure Schedules and Notifications
     hosts: localhost
     gather_facts: false
     
     vars:
       controller_host: https://controller.example.com
       controller_username: admin
       controller_password: redhat
       controller_validate_certs: false
     
     tasks:
       - name: Create notification templates
         awx.awx.tower_notification_template:
           name: "{{ item.name }}"
           description: "{{ item.description }}"
           organization: "{{ item.organization }}"
           notification_type: "{{ item.notification_type }}"
           notification_configuration: "{{ item.notification_configuration }}"
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
         loop:
           - name: Email Notification
             description: Email notification template
             organization: Production
             notification_type: email
             notification_configuration:
               username: user
               password: redhat
               host: smtp.example.com
               port: 25
               sender: rhel@example.com
               recipients:
                 - admin@example.com
               use_tls: false
               use_ssl: false
           - name: Slack Notification
             description: Slack notification template
             organization: Production
             notification_type: slack
             notification_configuration:
               url: https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX
               channels:
                 - "#ansible"
       
       - name: Associate notification templates with job templates
         awx.awx.tower_job_template_notification:
           job_template: "{{ item.job_template }}"
           notification_template: "{{ item.notification_template }}"
           notification_type: "{{ item.notification_type }}"
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
         loop:
           - job_template: Deploy Web Application
             notification_template: Email Notification
             notification_type: success
           - job_template: Deploy Web Application
             notification_template: Email Notification
             notification_type: failure
           - job_template: Deploy Web Application
             notification_template: Slack Notification
             notification_type: success
           - job_template: Deploy Web Application
             notification_template: Slack Notification
             notification_type: failure
       
       - name: Create schedules
         awx.awx.tower_schedule:
           name: "{{ item.name }}"
           description: "{{ item.description }}"
           unified_job_template: "{{ item.unified_job_template }}"
           rrule: "{{ item.rrule }}"
           extra_data: "{{ item.extra_data | default({}) }}"
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
         loop:
           - name: Daily Deployment
             description: Daily deployment schedule
             unified_job_template: Deploy Web Application
             rrule: "DTSTART:20230101T000000Z RRULE:FREQ=DAILY;INTERVAL=1"
             extra_data:
               app_version: 1.0.0
           - name: Weekly Backup
             description: Weekly backup schedule
             unified_job_template: Production Deployment
             rrule: "DTSTART:20230101T000000Z RRULE:FREQ=WEEKLY;INTERVAL=1;BYDAY=SU"
   EOF
   ```

3. **Commit your changes**
   ```bash
   git add schedules_notifications.md configure_schedules_notifications.yml
   git commit -m "Add schedule and notification management documents"
   ```

## Exercise 6: Managing Instance Groups and Capacity

In this exercise, you'll learn how to manage instance groups and capacity in Automation Controller.

1. **Create a document explaining instance group and capacity management**
   ```bash
   cat > instance_groups_capacity.md << 'EOF'
   # Managing Instance Groups and Capacity

   ## Instance Groups

   Instance groups are collections of Automation Controller instances that can be used to isolate job execution.

   ### Types of Instance Groups

   - **Default**: The default instance group that all instances belong to
   - **Tower**: The instance group for control plane instances
   - **Custom**: User-defined instance groups for specific purposes

   ### Creating an Instance Group

   To create an instance group in the UI:

   1. **Navigate to Administration > Instance Groups**
   2. **Click "Add"**
   3. **Fill in the form**:
      - **Name**: A unique name for the instance group
      - **Policy Instance Minimum**: The minimum number of instances in the group
      - **Policy Instance Percentage**: The percentage of instances to include in the group
   4. **Click "Save"**

   To create an instance group via the API:

   ```bash
   curl -k -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"name": "Production", "policy_instance_minimum": 2, "policy_instance_percentage": 50}' \
     https://controller.example.com/api/v2/instance_groups/
   ```

   To create an instance group via the CLI:

   ```bash
   awx instance_groups create --name "Production" --policy_instance_minimum 2 --policy_instance_percentage 50
   ```

   To create an instance group via Ansible:

   ```yaml
   - name: Create instance group
     uri:
       url: https://controller.example.com/api/v2/instance_groups/
       method: POST
       body:
         name: Production
         policy_instance_minimum: 2
         policy_instance_percentage: 50
       body_format: json
       headers:
         Authorization: "Bearer TOKEN"
       validate_certs: false
       status_code: 201
   ```

   ### Associating Instances with Instance Groups

   To associate an instance with an instance group in the UI:

   1. **Navigate to Administration > Instance Groups**
   2. **Select an instance group**
   3. **Click "Instances" tab**
   4. **Click "Associate"**
   5. **Select instances**
   6. **Click "Save"**

   To associate an instance with an instance group via the API:

   ```bash
   curl -k -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"id": 1}' \
     https://controller.example.com/api/v2/instance_groups/1/instances/
   ```

   ### Associating Resources with Instance Groups

   Resources that can be associated with instance groups:

   - **Organizations**: All jobs in the organization run on the instance group
   - **Inventories**: All jobs using the inventory run on the instance group
   - **Job Templates**: All jobs from the job template run on the instance group

   To associate a resource with an instance group in the UI:

   1. **Navigate to the resource (Organization, Inventory, or Job Template)**
   2. **Click "Edit"**
   3. **In the "Instance Groups" field, select instance groups**
   4. **Click "Save"**

   To associate a resource with an instance group via the API:

   ```bash
   curl -k -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"id": 1}' \
     https://controller.example.com/api/v2/organizations/1/instance_groups/
   ```

   ## Capacity Management

   Capacity management involves controlling how jobs are distributed across instances.

   ### Instance Capacity

   Each instance has a capacity based on:

   - **CPU**: The number of CPU cores
   - **Memory**: The amount of memory
   - **Fork Count**: The maximum number of concurrent jobs

   ### Job Impact

   Each job type has a different impact on capacity:

   - **Job**: 1 unit
   - **Project Update**: 1 unit
   - **Inventory Update**: 1 unit
   - **Management Job**: 1 unit
   - **Workflow Job**: 0 units (workflows themselves don't consume capacity)

   ### Capacity Allocation

   Jobs are allocated to instances based on:

   1. **Instance Group Assignment**: Jobs run on assigned instance groups
   2. **Available Capacity**: Jobs run on instances with available capacity
   3. **Job Priority**: Higher priority jobs run first

   ### Monitoring Capacity

   To monitor capacity in the UI:

   1. **Navigate to Administration > Instance Groups**
   2. **View the capacity information for each instance group**

   To monitor capacity via the API:

   ```bash
   curl -k -H "Authorization: Bearer TOKEN" \
     https://controller.example.com/api/v2/instance_groups/1/capacity/
   ```

   ### Controlling Capacity

   Ways to control capacity:

   - **Fork Count**: Set the maximum number of concurrent jobs per instance
   - **Instance Groups**: Isolate jobs to specific instances
   - **Job Slicing**: Break large jobs into smaller slices
   - **Job Priority**: Set the priority of jobs
   - **Job Concurrency**: Limit the number of concurrent jobs per job template
   EOF
   ```

2. **Create a sample instance group configuration playbook**
   ```bash
   cat > configure_instance_groups.yml << 'EOF'
   ---
   - name: Configure Instance Groups
     hosts: localhost
     gather_facts: false
     
     vars:
       controller_host: https://controller.example.com
       controller_username: admin
       controller_password: redhat
       controller_validate_certs: false
     
     tasks:
       - name: Create instance groups
         uri:
           url: "{{ controller_host }}/api/v2/instance_groups/"
           method: POST
           body:
             name: "{{ item.name }}"
             policy_instance_minimum: "{{ item.policy_instance_minimum | default(0) }}"
             policy_instance_percentage: "{{ item.policy_instance_percentage | default(0) }}"
           body_format: json
           headers:
             Authorization: "Bearer {{ controller_token }}"
           validate_certs: "{{ controller_validate_certs }}"
           status_code: 201
         loop:
           - name: Production
             policy_instance_minimum: 2
             policy_instance_percentage: 50
           - name: Development
             policy_instance_minimum: 1
             policy_instance_percentage: 25
           - name: Testing
             policy_instance_minimum: 1
             policy_instance_percentage: 25
         vars:
           controller_token: "{{ lookup('pipe', 'curl -k -s -X POST -H \"Content-Type: application/json\" -d \'{\"username\": \"' + controller_username + '\", \"password\": \"' + controller_password + '\"}\' ' + controller_host + '/api/v2/tokens/ | jq -r .token') }}"
       
       - name: Associate organizations with instance groups
         uri:
           url: "{{ controller_host }}/api/v2/organizations/{{ item.organization_id }}/instance_groups/"
           method: POST
           body:
             id: "{{ item.instance_group_id }}"
           body_format: json
           headers:
             Authorization: "Bearer {{ controller_token }}"
           validate_certs: "{{ controller_validate_certs }}"
           status_code: 204
         loop:
           - organization_id: 1
             instance_group_id: 1
           - organization_id: 2
             instance_group_id: 2
           - organization_id: 3
             instance_group_id: 3
         vars:
           controller_token: "{{ lookup('pipe', 'curl -k -s -X POST -H \"Content-Type: application/json\" -d \'{\"username\": \"' + controller_username + '\", \"password\": \"' + controller_password + '\"}\' ' + controller_host + '/api/v2/tokens/ | jq -r .token') }}"
       
       - name: Associate job templates with instance groups
         uri:
           url: "{{ controller_host }}/api/v2/job_templates/{{ item.job_template_id }}/instance_groups/"
           method: POST
           body:
             id: "{{ item.instance_group_id }}"
           body_format: json
           headers:
             Authorization: "Bearer {{ controller_token }}"
           validate_certs: "{{ controller_validate_certs }}"
           status_code: 204
         loop:
           - job_template_id: 1
             instance_group_id: 1
         vars:
           controller_token: "{{ lookup('pipe', 'curl -k -s -X POST -H \"Content-Type: application/json\" -d \'{\"username\": \"' + controller_username + '\", \"password\": \"' + controller_password + '\"}\' ' + controller_host + '/api/v2/tokens/ | jq -r .token') }}"
   EOF
   ```

3. **Commit your changes**
   ```bash
   git add instance_groups_capacity.md configure_instance_groups.yml
   git commit -m "Add instance group and capacity management documents"
   ```

## Exercise 7: Managing Automation Controller via API and CLI

In this exercise, you'll learn how to manage Automation Controller via API and CLI.

1. **Create a document explaining API and CLI management**
   ```bash
   cat > api_cli_management.md << 'EOF'
   # Managing Automation Controller via API and CLI

   ## REST API

   Automation Controller provides a comprehensive REST API for programmatic management.

   ### API Documentation

   API documentation is available at:

   ```
   https://<controller-hostname>/api/
   ```

   ### Authentication

   To authenticate with the API:

   ```bash
   # Get a token
   curl -k -X POST -H "Content-Type: application/json" \
     -d '{"username": "admin", "password": "password"}' \
     https://controller.example.com/api/v2/tokens/

   # Use the token
   curl -k -H "Authorization: Bearer TOKEN" \
     https://controller.example.com/api/v2/me/
   ```

   ### Common API Endpoints

   - **Organizations**: `/api/v2/organizations/`
   - **Users**: `/api/v2/users/`
   - **Teams**: `/api/v2/teams/`
   - **Credentials**: `/api/v2/credentials/`
   - **Projects**: `/api/v2/projects/`
   - **Inventories**: `/api/v2/inventories/`
   - **Hosts**: `/api/v2/hosts/`
   - **Groups**: `/api/v2/groups/`
   - **Job Templates**: `/api/v2/job_templates/`
   - **Jobs**: `/api/v2/jobs/`
   - **Workflow Templates**: `/api/v2/workflow_job_templates/`
   - **Workflow Jobs**: `/api/v2/workflow_jobs/`
   - **Instance Groups**: `/api/v2/instance_groups/`
   - **Instances**: `/api/v2/instances/`
   - **Settings**: `/api/v2/settings/`

   ### API Methods

   - **GET**: Retrieve resources
   - **POST**: Create resources
   - **PUT**: Update resources (full update)
   - **PATCH**: Update resources (partial update)
   - **DELETE**: Delete resources

   ### API Examples

   ```bash
   # Get all organizations
   curl -k -H "Authorization: Bearer TOKEN" \
     https://controller.example.com/api/v2/organizations/

   # Create an organization
   curl -k -X POST -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"name": "Example Organization", "description": "Example organization"}' \
     https://controller.example.com/api/v2/organizations/

   # Update an organization
   curl -k -X PATCH -H "Content-Type: application/json" -H "Authorization: Bearer TOKEN" \
     -d '{"description": "Updated description"}' \
     https://controller.example.com/api/v2/organizations/1/

   # Delete an organization
   curl -k -X DELETE -H "Authorization: Bearer TOKEN" \
     https://controller.example.com/api/v2/organizations/1/
   ```

   ## AWX CLI

   The AWX CLI is a command-line tool for managing Automation Controller.

   ### Installation

   ```bash
   pip install awxkit
   ```

   ### Configuration

   ```bash
   # Configure the CLI
   awx config

   # Set the host
   awx config host https://controller.example.com

   # Set the username and password
   awx config username admin
   awx config password password

   # Verify the configuration
   awx config get
   ```

   ### Common CLI Commands

   - **login**: Log in to Automation Controller
   - **logout**: Log out of Automation Controller
   - **config**: Configure the CLI
   - **version**: Show the CLI version
   - **me**: Show information about the current user
   - **organizations**: Manage organizations
   - **users**: Manage users
   - **teams**: Manage teams
   - **credentials**: Manage credentials
   - **projects**: Manage projects
   - **inventories**: Manage inventories
   - **hosts**: Manage hosts
   - **groups**: Manage groups
   - **job_templates**: Manage job templates
   - **jobs**: Manage jobs
   - **workflow_job_templates**: Manage workflow templates
   - **workflow_jobs**: Manage workflow jobs
   - **instance_groups**: Manage instance groups
   - **instances**: Manage instances
   - **settings**: Manage settings

   ### CLI Examples

   ```bash
   # Get all organizations
   awx organizations list

   # Create an organization
   awx organizations create --name "Example Organization" --description "Example organization"

   # Update an organization
   awx organizations modify 1 --description "Updated description"

   # Delete an organization
   awx organizations delete 1
   ```

   ## Ansible Collection

   The `awx.awx` Ansible collection provides modules for managing Automation Controller.

   ### Installation

   ```bash
   ansible-galaxy collection install awx.awx
   ```

   ### Common Modules

   - **tower_organization**: Manage organizations
   - **tower_user**: Manage users
   - **tower_team**: Manage teams
   - **tower_credential**: Manage credentials
   - **tower_project**: Manage projects
   - **tower_inventory**: Manage inventories
   - **tower_host**: Manage hosts
   - **tower_group**: Manage groups
   - **tower_job_template**: Manage job templates
   - **tower_job_launch**: Launch jobs
   - **tower_workflow_template**: Manage workflow templates
   - **tower_workflow_job_launch**: Launch workflow jobs
   - **tower_role**: Manage roles
   - **tower_settings**: Manage settings

   ### Ansible Examples

   ```yaml
   - name: Manage Automation Controller
     hosts: localhost
     gather_facts: false
     
     collections:
       - awx.awx
     
     tasks:
       - name: Create an organization
         tower_organization:
           name: Example Organization
           description: Example organization
           tower_host: https://controller.example.com
           tower_username: admin
           tower_password: password
       
       - name: Create a team
         tower_team:
           name: Example Team
           description: Example team
           organization: Example Organization
           tower_host: https://controller.example.com
           tower_username: admin
           tower_password: password
       
       - name: Create a user
         tower_user:
           username: example_user
           password: password
           email: user@example.com
           first_name: Example
           last_name: User
           tower_host: https://controller.example.com
           tower_username: admin
           tower_password: password
       
       - name: Add user to team
         tower_role:
           user: example_user
           team: Example Team
           role: member
           tower_host: https://controller.example.com
           tower_username: admin
           tower_password: password
   ```
   EOF
   ```

2. **Create a sample API and CLI management script**
   ```bash
   cat > manage_controller.sh << 'EOF'
   #!/bin/bash

   # This script demonstrates how to manage Automation Controller via API and CLI

   # Variables
   CONTROLLER_HOST="https://controller.example.com"
   CONTROLLER_USERNAME="admin"
   CONTROLLER_PASSWORD="redhat"
   CONTROLLER_VERIFY_SSL="false"

   # Get a token
   get_token() {
     curl -k -s -X POST -H "Content-Type: application/json" \
       -d "{\"username\": \"$CONTROLLER_USERNAME\", \"password\": \"$CONTROLLER_PASSWORD\"}" \
       $CONTROLLER_HOST/api/v2/tokens/ | jq -r .token
   }

   # API Examples
   api_examples() {
     TOKEN=$(get_token)

     echo "API Examples:"
     echo "============="

     # Get all organizations
     echo "Getting all organizations..."
     curl -k -s -H "Authorization: Bearer $TOKEN" \
       $CONTROLLER_HOST/api/v2/organizations/ | jq .

     # Create an organization
     echo "Creating an organization..."
     curl -k -s -X POST -H "Content-Type: application/json" -H "Authorization: Bearer $TOKEN" \
       -d '{"name": "API Example Organization", "description": "Created via API"}' \
       $CONTROLLER_HOST/api/v2/organizations/ | jq .

     # Get the organization ID
     ORG_ID=$(curl -k -s -H "Authorization: Bearer $TOKEN" \
       "$CONTROLLER_HOST/api/v2/organizations/?name=API%20Example%20Organization" | jq -r '.results[0].id')

     # Update the organization
     echo "Updating the organization..."
     curl -k -s -X PATCH -H "Content-Type: application/json" -H "Authorization: Bearer $TOKEN" \
       -d '{"description": "Updated via API"}' \
       $CONTROLLER_HOST/api/v2/organizations/$ORG_ID/ | jq .

     # Create a team
     echo "Creating a team..."
     curl -k -s -X POST -H "Content-Type: application/json" -H "Authorization: Bearer $TOKEN" \
       -d "{\"name\": \"API Example Team\", \"description\": \"Created via API\", \"organization\": $ORG_ID}" \
       $CONTROLLER_HOST/api/v2/teams/ | jq .

     # Create a user
     echo "Creating a user..."
     curl -k -s -X POST -H "Content-Type: application/json" -H "Authorization: Bearer $TOKEN" \
       -d '{"username": "api_user", "password": "redhat", "email": "api_user@example.com", "first_name": "API", "last_name": "User"}' \
       $CONTROLLER_HOST/api/v2/users/ | jq .

     # Delete the organization
     echo "Deleting the organization..."
     curl -k -s -X DELETE -H "Authorization: Bearer $TOKEN" \
       $CONTROLLER_HOST/api/v2/organizations/$ORG_ID/
   }

   # CLI Examples
   cli_examples() {
     echo "CLI Examples:"
     echo "============="

     # Configure the CLI
     echo "Configuring the CLI..."
     awx config host $CONTROLLER_HOST
     awx config username $CONTROLLER_USERNAME
     awx config password $CONTROLLER_PASSWORD
     awx config verify_ssl $CONTROLLER_VERIFY_SSL

     # Get all organizations
     echo "Getting all organizations..."
     awx organizations list

     # Create an organization
     echo "Creating an organization..."
     awx organizations create --name "CLI Example Organization" --description "Created via CLI"

     # Get the organization ID
     ORG_ID=$(awx organizations list --name "CLI Example Organization" -f json | jq -r '.[0].id')

     # Update the organization
     echo "Updating the organization..."
     awx organizations modify $ORG_ID --description "Updated via CLI"

     # Create a team
     echo "Creating a team..."
     awx teams create --name "CLI Example Team" --description "Created via CLI" --organization $ORG_ID

     # Create a user
     echo "Creating a user..."
     awx users create --username cli_user --password redhat --email cli_user@example.com --first_name CLI --last_name User

     # Delete the organization
     echo "Deleting the organization..."
     awx organizations delete $ORG_ID
   }

   # Ansible Examples
   ansible_examples() {
     echo "Ansible Examples:"
     echo "================"

     # Create a playbook
     cat > manage_controller.yml << 'EOL'
   ---
   - name: Manage Automation Controller
     hosts: localhost
     gather_facts: false
     
     collections:
       - awx.awx
     
     vars:
       controller_host: https://controller.example.com
       controller_username: admin
       controller_password: redhat
       controller_validate_certs: false
     
     tasks:
       - name: Create an organization
         awx.awx.tower_organization:
           name: Ansible Example Organization
           description: Created via Ansible
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
       
       - name: Create a team
         awx.awx.tower_team:
           name: Ansible Example Team
           description: Created via Ansible
           organization: Ansible Example Organization
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
       
       - name: Create a user
         awx.awx.tower_user:
           username: ansible_user
           password: redhat
           email: ansible_user@example.com
           first_name: Ansible
           last_name: User
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
       
       - name: Add user to team
         awx.awx.tower_role:
           user: rhel_user
           team: Ansible Example Team
           role: member
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
       
       - name: Delete the organization
         awx.awx.tower_organization:
           name: Ansible Example Organization
           state: absent
           tower_host: "{{ controller_host }}"
           tower_username: "{{ controller_username }}"
           tower_password: "{{ controller_password }}"
           validate_certs: "{{ controller_validate_certs }}"
   EOL

     # Run the playbook
     echo "Running the playbook..."
     ansible-playbook manage_controller.yml
   }

   # Main
   main() {
     echo "Automation Controller Management Examples"
     echo "========================================"
     echo

     # Run API examples
     api_examples

     echo

     # Run CLI examples
     cli_examples

     echo

     # Run Ansible examples
     ansible_examples
   }

   # Run the script
   main
   EOF
   
   chmod +x manage_controller.sh
   ```

3. **Commit your changes**
   ```bash
   git add api_cli_management.md manage_controller.sh
   git commit -m "Add API and CLI management documents"
   ```

## Conclusion

In this lab, you've learned how to:
- Understand Automation Controller concepts and architecture
- Configure Automation Controller
- Manage organizations and teams
- Manage projects and job templates
- Manage schedules and notifications
- Manage instance groups and capacity
- Manage Automation Controller via API and CLI

These skills are essential for the "Manage Automation Controller" objective of the EX374 exam.

## Additional Resources

- [Automation Controller User Guide](https://docs.ansible.com/automation-controller/latest/html/userguide/index.html)
- [Automation Controller Administration Guide](https://docs.ansible.com/automation-controller/latest/html/administration/index.html)
- [Automation Controller API Guide](https://docs.ansible.com/automation-controller/latest/html/api/index.html)
- [AWX CLI Documentation](https://docs.ansible.com/automation-controller/latest/html/awx-cli/index.html)
- [AWX Ansible Collection](https://github.com/ansible/awx/tree/devel/awx_collection)
