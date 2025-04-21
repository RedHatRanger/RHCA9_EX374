**Lab 5: Delegating Tasks & Facts**

**Objectives**

1. **Delegate task execution** to a different host using `delegate_to`.  
   _Source: Chapter 8 “Delegating Tasks and Facts”_ citeturn0file0
2. **Control fact delegation**—decide whether the task’s registered variables are stored under the target host or the controller—using `delegate_facts`.  
   _Source: Chapter 8 “Delegating Tasks and Facts”_ citeturn0file0

---

#### Lab Overview

In this lab, you will explore Ansible’s delegation features to offload task execution and fact gathering to a designated utility host. You’ll first run a baseline playbook with no delegation, then implement `delegate_to` to redirect tasks, and finally use `delegate_facts` to control where registered variables are stored. This hands-on exercise will reinforce how delegation can centralize complex operations and manage where data resides in multi‑host environments.

### 1. Environment & Setup

- **Control node:** controller.lab.example.com (Ansible Automation Platform 2.4)  
- **Managed hosts:** node1.lab.example.com, node2.lab.example.com  
- **User:** `rhel` (password: `redhat`)

```bash
ssh rhel@controller.lab.example.com  # password: redhat
sudo yum install -y ansible
```  

---

### 2. Define Inventory

**`inventory`**
```ini
[webservers]
node1.lab.example.com
node2.lab.example.com

[utility]
node1.lab.example.com
```

> **Note:** `utility` represents a separate node used to execute delegated tasks.

---

### 3. Baseline Playbook (no delegation)

Create **`site.yml`**:
```yaml
---
- name: Baseline uptime on webservers
  hosts: webservers
  gather_facts: no

  tasks:
    - name: Run uptime locally
      shell: uptime
      register: up

    - name: Show local uptime
      debug:
        msg: "{{ inventory_hostname }} uptime: {{ up.stdout }}"
```

Run it:
```bash
ansible-playbook -i inventory site.yml
```
Each webserver should report its own uptime.

---

### Understanding `delegate_to`

The `delegate_to` keyword tells Ansible to run a specific task on a host different from the one under `hosts:`. Even though your play iterates over each member of the `webservers` group, tasks marked with `delegate_to: node1.lab.example.com` will execute **only** on that utility node. Ansible still records the results under the original inventory_hostname, letting you centralize heavy operations (e.g., snapshots, API calls) on a single proxy or bastion without modifying your group definitions.

---

### 4. Delegate Tasks to Utility Host  ← Objective 1  ← Objective 1

Update **`site.yml`** to delegate the `uptime` task:
```yaml
---
- name: Delegate uptime to utility
  hosts: webservers
  gather_facts: no

  tasks:
    - name: Run uptime on utility for each webserver
      shell: uptime
      register: up
      delegate_to: node1.lab.example.com     # runs on utility node
      delegate_facts: no                      # facts stored under utility by default

    - name: Show delegated uptime
      debug:
        msg: "{{ inventory_hostname }} uptime (delegated): {{ up.stdout }}"
```

Re-run:
```bash
ansible-playbook -i inventory site.yml
```
- You’ll see `uptime` executed only on **node1.lab.example.com** but reported per webserver.

---

### 5. Control Fact Delegation  ← Objective 2

Enable storing the `up` result under each webserver by setting `delegate_facts: yes`:
```yaml
---
- name: Delegate uptime and facts
  hosts: webservers
  gather_facts: no

  tasks:
    - name: Run and delegate uptime + facts
      shell: uptime
      register: up
      delegate_to: node1.lab.example.com
      delegate_facts: yes                   # store `up` under each webserver's hostvars

    - name: Show delegated uptime fact for {{ inventory_hostname }}
      debug:
        var: hostvars[inventory_hostname].up.stdout
```

Re-run:
```bash
ansible-playbook -i inventory site.yml
```
- Now each webserver’s `hostvars[<host>].up.stdout` is defined.

---

### 6. Inspect Hostvars

(Optional) Add:
```yaml
    - name: Dump hostvars for node1
      debug:
        var: hostvars['node1.lab.example.com'].up.stdout
```
Run again to confirm where facts landed.

---

*End of Lab 5: Delegating Tasks & Facts*
