# Ansible Automation Platform 2.4 Installation Guide

This guide provides step-by-step instructions for installing Ansible Automation Platform 2.4 in a single-node configuration with Automation Controller, Private Automation Hub, and PostgreSQL database.

## Prerequisites

- A Proxmox VM set up according to the [Proxmox VM Setup Guide](proxmox_setup_guide.md)
- Red Hat Enterprise Linux 9.5 installed and configured
- System registered with Red Hat Subscription Manager
- Required repositories enabled
- Internet access for downloading packages

## Download the AAP Setup Bundle

1. **Log in to the Red Hat Customer Portal**
   - Navigate to https://access.redhat.com
   - Log in with your Red Hat account credentials

2. **Download the AAP Setup Bundle**
   - Go to `https://access.redhat.com/downloads/content/480/ver=2.4/rhel---9/2.4/x86_64/product-software`

3. **Transfer the Bundle to Your VM**
   - Use SCP or another file transfer method to copy the bundle to your VM
   ```bash
   cd Downloads
   scp ansible-automation-platform-setup-bundle-2.4-*.tar.gz rhel@aap.example.com:/home/rhel/
   ```

## Extract and Prepare the Installation

1. **Log in to Your VM**
   ```bash
   ssh rhel@controller.lab.example.com
   ```

2. **Extract the Bundle**
   ```bash
   cd /home/rhel
   tar -xvf ansible-automation-platform-setup-bundle-2.4-*.tar.gz
   cd ansible-automation-platform-setup-bundle-2.4*/
   ```

3. **Create a Backup of the Default Inventory File**
   ```bash
   cp inventory inventory.bak
   ```

## Configure the Inventory File

Create a custom inventory file for a single-node installation with Automation Controller, Private Automation Hub, and PostgreSQL database:

1. **Edit the Inventory File**
```bash
vim inventory
```

2. **Replace the Contents with the Following Configuration**
```
# Inventory for Initial Single-Node AAP Control Plane Installation (Lab Environment)
#
# Installs Controller, Hub, Catalog, Database, SSO on this local node.
# Execution Nodes can be added later using the 'rhel' user for SSH.
# WARNING: Uses 'redhat' for admin/database passwords - suitable ONLY for non-critical labs.

[automationcontroller]
# This is the primary node where you run the ./setup.sh script.
# It acts as the Controller and also a Hybrid node (can run jobs itself).
localhost ansible_connection=local node_type=hybrid

[automationcontroller:vars]
# Tells the controller which group contains the execution nodes it should manage.
# It will be empty initially but is ready for future additions.
peers=execution_nodes

[automationhub]
# Leave empty. The installer will place Automation Hub on the first
# node listed in [automationcontroller] (i.e., localhost).

[automationcatalog]
# Leave empty. The installer will place Automation Services Catalog on the first
# node listed in [automationcontroller] (i.e., localhost).

[database]
# Leave empty. The installer will manage the PostgreSQL database(s) locally
# on the first node in [automationcontroller] (i.e., localhost) because
# the relevant pg_host variables below are empty.

[sso]
# Leave empty. The installer will place the SSO (Keycloak) service on the first
# node listed in [automationcontroller] (i.e., localhost).

[execution_nodes]
# --- THIS GROUP IS INTENTIONALLY LEFT EMPTY FOR THE INITIAL INSTALL ---

[all:vars]
### Connection Settings for FUTURE Remote Nodes ###
# User for SSH connections TO the execution_nodes WHEN you add them.
# Ensure 'rhel' user exists on workers, has SSH access setup (keys recommended),
# and potentially sudo rights if needed for installation tasks.
ansible_user=rhel
# Uncomment if the 'rhel' user needs sudo privileges on the workers:
# ansible_become=true

### Admin Passwords (Using 'redhat' for Lab Simplicity) ###
# Password for the 'admin' user in the Automation Controller UI
admin_password='redhat'
# Password for the 'admin' user in the Automation Hub UI
automationhub_admin_password='redhat'
# Password for the Keycloak Admin Console user ('admin')
sso_console_admin_password='redhat'
# Password for the SSO keystore
sso_keystore_password='redhat'

### Database Settings (Using 'redhat' for Lab Simplicity) ###
# pg_host='' tells the installer to manage the database locally on the controller node.
pg_host=''
pg_port='5432'
pg_database='awx'
pg_username='awx'
pg_password='redhat'
pg_sslmode='prefer'

### Automation Hub Database Settings (Using 'redhat' for Lab Simplicity) ###
# automationhub_pg_host='' tells the installer to manage the database locally on the controller node.
automationhub_pg_host=''
automationhub_pg_port='5432'
automationhub_pg_database='automationhub'
automationhub_pg_username='automationhub'
automationhub_pg_password='redhat'
automationhub_pg_sslmode='prefer'

### Automation Services Catalog Database Settings (Using 'redhat' for Lab Simplicity) ###
# automationcatalog_pg_host='' tells the installer to manage the database locally on the controller node.
automationcatalog_pg_host=''
automationcatalog_pg_port='5432'
automationcatalog_pg_database='automationservicescatalog'
automationcatalog_pg_username='automationservicescatalog'
automationcatalog_pg_password='redhat'
automationcatalog_pg_sslmode='prefer'

### Red Hat Registry Credentials ###
# !! IMPORTANT: Replace placeholders with your actual Red Hat Registry Service Account credentials !!
# Used to pull AAP container images during installation.
registry_url='registry.redhat.io'
registry_username='<YOUR_REDHAT_USERNAME_OR_SERVICE_ACCOUNT>'
registry_password='<YOUR_REDHAT_PASSWORD_OR_TOKEN>'

### Receptor Communication ###
# Port used for communication between Controller and Execution Nodes.
# Ensure this port is open (TCP) *on this Controller node* for inbound connections
# from future Execution Nodes.
receptor_listener_port=27199
```

2. **Monitor the Installation**
   ```bash
   chmod +x setup.sh
   ./setup.sh -i inventory
   ```
   
   - The installation will take 15-30 minutes to complete
   - You'll see detailed output of the installation process
   - If any errors occur, the installer will provide information about the issue

4. **Verify the Installation Completed Successfully**
   - Look for a message indicating the installation was successful
   - Note the URLs provided for accessing Automation Controller and Automation Hub

## Post-Installation Configuration

### Access Automation Controller

1. **Open a Web Browser**
   - Add to your `C:\Windows\System32\drivers\etc\hosts` file: "192.168.1.201  controller.lab.example.com  controller"
   - Navigate to `https://controller.lab.example.com/`
   - You may need to accept the self-signed certificate warning

3. **Log in to Automation Controller**
   - Username: `admin`
   - Password: `redhat` (as specified in the inventory file)

4. **Complete Initial Setup**
   - Accept the license agreement
   - Create an organization
   - Create a project

### Access Private Automation Hub

1. **Open a Web Browser**
   - Navigate to `https://aap.example.com/hub/`
   - You may need to accept the self-signed certificate warning

2. **Log in to Private Automation Hub**
   - Username: `admin`
   - Password: `redhat` (as specified in the inventory file)

3. **Configure Automation Hub**
   - Set up remote repositories
   - Configure authentication settings

## Troubleshooting

### Installation Fails with Database Connection Errors
- Verify PostgreSQL is running: `sudo systemctl status postgresql`
- Check PostgreSQL logs: `sudo journalctl -u postgresql`
- Ensure the database credentials in the inventory file are correct

### Installation Fails with Registry Authentication Errors
- Verify your Red Hat Registry Service Account credentials
- Ensure your subscription includes access to Ansible Automation Platform
- Check network connectivity to registry.redhat.io

### Web Interface Not Accessible
- Verify the services are running:
  ```bash
  sudo systemctl status automation-controller.service
  sudo systemctl status pulp.service
  ```
- Check the firewall settings:
  ```bash
  sudo firewall-cmd --list-all
  ```
- Check the nginx configuration and logs:
  ```bash
  sudo cat /var/log/nginx/error.log
  ```

## Backup and Recovery

It's recommended to create regular backups of your AAP installation:

1. **Create a Database Backup**
   ```bash
   sudo -u postgres pg_dump awx > awx_db_backup.sql
   sudo -u postgres pg_dump automationhub > automationhub_db_backup.sql
   ```

2. **Backup Configuration Files**
   ```bash
   sudo tar -czf aap_config_backup.tar.gz /etc/tower /etc/pulp
   ```

3. **Store Backups Securely**
   - Copy backups to a secure location
   - Consider automating backups with a cron job

## Next Steps

Your Ansible Automation Platform installation is now complete. Proceed to the [PostgreSQL Configuration Guide](postgres_configuration_guide.md) for optimizing your database setup.

After that, explore the labs in the [labs directory](../labs/) to learn how to use all the features of Ansible Automation Platform and prepare for the EX374 exam.
