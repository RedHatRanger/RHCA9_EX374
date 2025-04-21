**Lab 4: Transform Data with Filters & Plugins**

**Objectives**

1. Populate variables with data from external sources using lookup plugins  ← *lookup demos*  
2. Use lookup and query functions to incorporate data from external sources into playbooks and templates  ← *lookup vs. query section*  
3. Implement advanced loops using lookup plugins and filters  ← *network loop demos*  
4. Inspect and manipulate networking-related data using filters  ← *ipaddr filters & template*

---

### Environment & Prerequisites

- **Control node:** controller.lab.example.com (AAP 2.4)  
- **Managed hosts:** node1.lab.example.com, node2.lab.example.com  
- **User:** `rhel` (password: `redhat`)  
- **GitHub repo:** `lab4-data` (URL: `https://github.com/<YourUsername>/lab4-data.git`)

---

### Project Setup

```bash
ssh rhel@controller.lab.example.com  # password: redhat
sudo yum install -y ansible git
mkdir ~/lab4-data && cd ~/lab4-data
git init
```  

---

### Steps

1. **Clone or create your GitHub repo**  
   - On GitHub, create `lab4-data` and copy its HTTPS URL.  
   - Locally:
     ```bash
     git remote add origin https://github.com/<YourUsername>/lab4-data.git
     ```

2. **Create a playbook and templates**  ← *addresses Objectives 1 & 2*

   **`playbooks/data_filters.yml`**:
   ```yaml
   ---
   - name: Demonstrate filters and lookups
     hosts: localhost
     gather_facts: false

     vars:
       users_file: "users.txt"          # Lookup target file  ← Objective 1
       networks:
         - 10.0.1.0/24
         - 192.168.50.0/28

     tasks:
       - name: Read users from file using lookup
         ansible.builtin.set_fact:
           user_list: "{{ lookup('file', users_file).splitlines() }}"  # Lookup plugin  ← Obj 1

       - name: Show all users
         ansible.builtin.debug:
           var: user_list  # Verify data pulled  ← Obj 2

       - name: Fetch public IP via URI lookup
         ansible.builtin.set_fact:
           my_ip: "{{ lookup('url', 'https://ifconfig.me') }}"  # HTTP lookup  ← Obj 1

       - name: Display my IP
         ansible.builtin.debug:
           msg: "Public IP is {{ my_ip }}"  # Confirm incorporation  ← Obj 2

       - name: Loop over networks using ipaddr filter
         ansible.builtin.debug:
           msg: "Network {{ item }} has {{ (item | ipaddr('hosts') | length) }} usable hosts"  # Advanced loop & filter  ← Obj 3 & Obj 4
         loop: "{{ networks }}"
   ```

   **`users.txt`** (in project root):
   ```text
   alice
   bob
   carol
   ```

3. **Define inventory**  

   **`inventory`**:
   ```ini
   [local]
   localhost ansible_connection=local  # Local connection for lookup demos
   ```

4. **Run the playbook**  ← *validates Objectives 1–4*

   ```bash
   ansible-playbook playbooks/data_filters.yml -i inventory
   ```

   - Verify `user_list` shows `['alice','bob','carol']`  ← *lookup success*  
   - Confirm output displays a valid public IP  ← *lookup success*  
   - Check loop iteration prints usable host counts  ← *filter & loop success*

5. **Add a Jinja2 template**  ← *demonstrates templating external data  ← Obj 2 & Obj 4*

   **`templates/network_report.j2`**:
   ```jinja
   Network Report:
   {% for net in networks %}
   {{ net }} -> Broadcast: {{ net | ipaddr('broadcast') }}  # Using ipaddr filter  ← Obj 4
   {% endfor %}
   ```

   Add this task under `tasks:` in `data_filters.yml`:
   ```yaml
       - name: Render network report template
         ansible.builtin.template:
           src: network_report.j2
           dest: /tmp/network_report.txt
         vars:
           networks: "{{ networks }}"  # Pass networks var  ← Obj 2
   ```

   Re-run the playbook and inspect `/tmp/network_report.txt`  ← *template output*

6. **Commit & push**

   ```bash
   git add playbooks/data_filters.yml users.txt templates/network_report.j2 inventory
   git commit -m "Lab4: demonstrate filters, lookups, loops & templating"
   git push -u origin main
   ```

---

**Success criteria**

- Playbook runs without errors (all objectives validated).  
- `user_list` fact correctly split via lookup.  
- `my_ip` fact correctly populated via URI lookup.  
- Loop correctly counts hosts using `ipaddr` filter.  
- `/tmp/network_report.txt` contains broadcast addresses (filter validated).

*End of Lab 4: Transform Data with Filters & Plugins*
