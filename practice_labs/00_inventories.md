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
