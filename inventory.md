```yaml
# Instructions for setup by Luca Berton: https://www.youtube.com/watch?v=NlG8RGY3P8s
# COPY TO YOUR HOST AND RENAME IT 'inventory'.  Then, use it with the AAP Setup Bundle's install.sh script.  
# inventory for single-node AAP 2.4 lab setup (all services on one VM)
# All groups ([database], [automationhub], etc.) point to localhost, so every component lives on that VM.
# The installer will deploy PostgreSQL in a Podman container and initialize the awx, Hub and Catalog databases for you.
# Replace <YOUR_REDHAT_USERNAME>/<YOUR_REDHAT_PASSWORD> with your own registry credentials before running install.sh.

[automationcontroller]
localhost node_type=hybrid ansible_connection=local

[automationcontroller:vars]
# treat localhost as both controller + execution node
peers=execution_nodes

[execution_nodes]
localhost

[database]
localhost

[automationhub]
localhost

[automationcatalog]
localhost

[sso]
localhost

[all:vars]
# SSH user on your RHEL host
ansible_user=rhel

# AWX (Controller) admin password
admin_password='redhat'

# PostgreSQL for Controller (installer-managed container)
pg_host='localhost'
pg_port=5432
pg_database='awx'
pg_username='awx'
pg_password='redhat'
pg_sslmode='prefer'

# Private Automation Hub registry credentials
registry_url='registry.redhat.io'
registry_username='<YOUR_REDHAT_USERNAME>'
registry_password='<YOUR_REDHAT_PASSWORD>'

# Receptor listener port (default)
receptor_listener_port=27199

# Automation Hub service
automationhub_admin_password='redhat'
automationhub_pg_host='localhost'
automationhub_pg_port=5432
# (If you want Hub on its own DB, set automationhub_pg_database & automationhub_pg_username here)
automationhub_pg_password='redhat'
automationhub_pg_sslmode='prefer'

# Automation Services Catalog
automationcatalog__pg_host='localhost'
automationcatalog_pg_port=5432
automationcatalog_pg_database='automationservicescatalog'
automationcatalog_pg_username='automationservicescatalog'
automationcatalog__pg_password='redhat'

# SSO (Keycloak)
sso_keystore_password='redhat'
sso_console_admin_password='redhat'
```
