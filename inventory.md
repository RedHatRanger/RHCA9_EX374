```yaml
# Instructions for setup by Luca Berton: https://www.youtube.com/watch?v=NlG8RGY3P8s
# COPY TO YOUR HOST AND RENAME IT 'inventory'.  Then, use it with the AAP Setup Bundle's install.sh script.  

[automationcontroller]
localhost node_type=hybrid ansible_connection=local

[automationcontroller:vars]
peers=execution_nodes

[execution_nodes]
localhost

[automationhub]

[automationcatalog]

[database]

[sso]

[all:vars]
ansible_user=rhel
admin_password='redhat'

pg_host='controller.lab.example.com'
pg_port=5432

pg_database='awx'
pg_username='awx'
pg_password='redhat'
pg_sslmode='prefer'

registry_url='registry.redhat.io'
registry_username='<YOUR_REDHAT_USERNAME>'
registry_password='<YOUR_REDHAT_PASSWORD>'

receptor_listener_port=27199

automationhub_admin_password='redhat'

automationhub_pg_host=''
automationhub_pg_port=5432
automationhub_pg_password='redhat'
automationhub_pg_sslmode='prefer'


automationcatalog__pg_host=''
automationcatalog_pg_port=5432
automationcatalog_pg_database='automationservicescatalog'
automationcatalog_pg_username='automationservicescatalog'
automationcatalog__pg_password='redhat'

sso_keystore_password='redhat'
sso_console_admin_password='redhat'
```
