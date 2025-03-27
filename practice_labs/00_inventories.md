# Simple inventory file example
```
cat inventory

monitor.example.com

[webservers]
web1.example.com
web2.example.com

[databases]
db1.example.com
db2.example.com

# Using ansible-navigator to check the parsed inventory
[user@host ~]$ ansible-navigator inventory --mode stdout -i inventory --list
```

```
ansible-navigator inventory --mode stdout --graph
@all:
|--@servers:
| |--@webservers:
| | |--servera.lab.example.com
|--@ungrouped:
| |--workstation.lab.example.com
```

```
Convert this to YAML:

[lb_servers]
proxy.example.com
[web_servers]
web1.example.com
web2.example.com
[backend_server_pool]
appsrv-[a:e].example.com
```

```
Answer:

lb_servers:
  hosts:
    proxy.example.com:
web_servers:
  hosts:
    web1.example.com:
    web2.example.com:
backend_server_pool:
  hosts:
    appsrv-[a:e].example.com:
```

```
Note
It is often a best practice to avoid storing variables in static inventory files.
Experienced Ansible developers prefer to use static inventory files only to list the
hosts that Ansible manages, and to organize them in groups. The variables and their
values are stored in the host_vars or group_vars files for the inventory.
However, you might want to define variables such as ansible_port or
ansible_connection in the same file as the inventory itself, to keep this
information in one place. If you set variables in too many places, then it is harder to
remember where a particular variable is defined.
```

```
In a group block, use the vars keyword to set group variables directly in a YAML inventory file.
The following static inventory file in INI format sets the smtp_relay variable for all the hosts in
monitoring group.
[monitoring]
watcher.example.com
[monitoring:vars]
smtp_relay=smtp.example.com
The equivalent static inventory file in YAML format is as follows:
monitoring:
hosts:
watcher.example.com:
vars:
smtp_relay: smtp.example.com
```

```
In YAML, to set a variable for a specific host instead of a group, indent the variable under that host.
The following static inventory file in INI format sets the ansible_connection variable for the
localhost host.
[databases]
maria1.example.com
localhost ansible_connection=local
maria2.example.com
The equivalent static inventory file in YAML format is as follows:
databases:
hosts:
maria1.example.com:
localhost:
ansible_connection: local
maria2.example.com:
```

* To convert a static INI inventory to a YAML:
```
# Sample INI `origin_inventory`:
[lb_servers]
proxy.example.com
[web_servers]
web1.example.com
web2.example.com
[web_servers:vars]
http_port=8080
[backend_server_pool]
appsrv-[a:e].example.com
190 DO374-RHAAP2.2-en-1-20230131
Chapter 5 | Managing Inventories
[dc1]
web1.example.com
appsrv-e.example.com

# Now, convert it to YAML:
ansible-navigator inventory --mode stdout -i origin_inventory --list --yaml

# Output:
all:
  children:
    backend_server_pool:
      hosts:
        appsrv-[a:e].example.com: {}
    dc1:
      hosts:
        appsrv-e.example.com: {}
        web1.example.com: {}
    lb_servers:
      hosts:
        proxy.example.com: {}
  ungrouped: {}
  web_servers:
    hosts:
      web1.example.com: {}
      web2.example.com: {}
    vars:
      http_port: 8080
```

* YAML LAB:
```
# Convert this to YAML:
[active_web_servers]
server[b:c].lab.example.com

[inactive_web_servers]
server[d:f].lab.example.com

[web_servers:children]
active_web_servers
inactive_web_servers

[all_servers]
servera.lab.example.com

[all_servers:children]
web_servers

# Converted YAML format:
active_web_servers:
  hosts:
    server[b:c].lab.example.com:

inactive_web_servers:
  hosts:
    server[d:f].lab.example.com:

web_servers:
  children:
    active_web_servers:
    inactive_web_servers:

all_servers:
  hosts:
    servera.lab.example.com:
  children:
    web_servers:

# Test the converted YAML inventory
ansible-navigator run -i inventory.yml --mode stdout test-ping-all-servers.yml
```

