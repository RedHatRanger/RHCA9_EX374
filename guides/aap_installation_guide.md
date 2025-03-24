# Ansible Automation Platform 2.4 Installation Guide

This guide provides step-by-step instructions for installing Ansible Automation Platform 2.4 in a single-node configuration with Automation Controller, Private Automation Hub, and PostgreSQL database.

## Prerequisites

- A Proxmox VM set up according to the [Proxmox VM Setup Guide](proxmox_setup_guide.md)
- Red Hat Enterprise Linux 8.8+ or 9.0+ installed and configured
- System registered with Red Hat Subscription Manager
- Required repositories enabled
- Internet access for downloading packages

## Download the AAP Setup Bundle

1. **Log in to the Red Hat Customer Portal**
   - Navigate to https://access.redhat.com
   - Log in with your Red Hat account credentials

2. **Download the AAP Setup Bundle**
   - Go to Downloads > Ansible Automation Platform
   - Select version 2.4
   - Download the appropriate bundle for your architecture:
     - For x86_64: `ansible-automation-platform-setup-bundle-2.4-x86_64.tar.gz`
     - For ARM64: `ansible-automation-platform-setup-bundle-2.4-aarch64.tar.gz`

3. **Transfer the Bundle to Your VM**
   - Use SCP or another file transfer method to copy the bundle to your VM
   ```bash
   scp ansible-automation-platform-setup-bundle-2.4-*.tar.gz rhel@aap.example.com:/home/rhel/
   ```

## Extract and Prepare the Installation

1. **Log in to Your VM**
   ```bash
   ssh rhel@aap.example.com
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
   vi inventory
   ```

2. **Replace the Contents with the Following Configuration**
   ```ini
   [automationcontroller]
   localhost ansible_connection=local

   [automationhub]
   localhost ansible_connection=local

   [database]
   localhost ansible_connection=local

   [all:vars]
   admin_password='redhat'
   pg_host='localhost'
   pg_port='5432'
   pg_database='awx'
   pg_username='awx'
   pg_password='redhat'
   pg_sslmode='prefer'

   registry_url='registry.redhat.io'
   registry_username='<your-registry-username>'
   registry_password='<your-registry-password>'

   automationhub_admin_password='redhat'

   automationhub_pg_host='localhost'
   automationhub_pg_port=5432
   automationhub_pg_database='automationhub'
   automationhub_pg_username='automationhub'
   automationhub_pg_password='redhat'
   automationhub_pg_sslmode='prefer'

   # The default install will deploy a TLS enabled Automation Hub.
   # If for some reason this is not the behavior wanted one can
   # disable TLS enabled deployment.
   #
   # automationhub_disable_https = False
   # The default install will generate self-signed certificates for the Automation
   # Hub service. If you are providing valid certificate via automationhub_ssl_cert
   # and automationhub_ssl_key, one should toggle that value to True.
   #
   # automationhub_ssl_validate_certs = False
   # SSL-related variables
   # If set, this will install a custom CA certificate to the system trust store.
   # custom_ca_cert=/path/to/ca.crt
   # Certificate and key to install in Automation Hub node
   # automationhub_ssl_cert=/path/to/automationhub.cert
   # automationhub_ssl_key=/path/to/automationhub.key

   # Certificate and key to install in nginx for the web UI and API
   # web_server_ssl_cert=/path/to/tower.cert
   # web_server_ssl_key=/path/to/tower.key
   # Server-side SSL settings for PostgreSQL (when we are installing it).
   # postgres_use_ssl=False
   # postgres_ssl_cert=/path/to/pgsql.crt
   # postgres_ssl_key=/path/to/pgsql.key
   ```

3. **Replace Registry Credentials**
   - Replace `<your-registry-username>` with your Red Hat Registry Service Account username
   - Replace `<your-registry-password>` with your Red Hat Registry Service Account password

## Run the Installation

1. **Execute the Setup Script**
   ```bash
   sudo ./setup.sh
   ```

2. **Monitor the Installation**
   - The installation will take 15-30 minutes to complete
   - You'll see detailed output of the installation process
   - If any errors occur, the installer will provide information about the issue

3. **Verify the Installation Completed Successfully**
   - Look for a message indicating the installation was successful
   - Note the URLs provided for accessing Automation Controller and Automation Hub

## Post-Installation Configuration

### Access Automation Controller

1. **Open a Web Browser**
   - Navigate to `https://aap.example.com/`
   - You may need to accept the self-signed certificate warning

2. **Log in to Automation Controller**
   - Username: `admin`
   - Password: `redhat` (as specified in the inventory file)

3. **Complete Initial Setup**
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
