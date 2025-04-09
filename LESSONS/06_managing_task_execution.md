**Lesson 6 Summary: Managing Job Execution (Simple Version)**

---

### ✅ Objectives Checklist (EX374):
- [ ] Launch and monitor jobs using job templates
- [ ] Use surveys to pass input to jobs
- [ ] Understand and apply job slicing and labels
- [ ] Interpret job output and job history
- [ ] Use notifications and callbacks for job events

---

### 🧠 What You Learn in This Chapter:
This chapter teaches you how to run, monitor, and manage automation jobs using **job templates** in the Automation Controller. You learn how to get input from users with **surveys**, and how to split jobs across many systems with **slicing**.

You also learn how to read job results, set up notifications (like emails), and let other systems start jobs using **callbacks**.

---

### 🎯 Why It Matters for the EX374 Exam:
- You must know how to launch jobs from templates
- You’ll need to work with surveys, labels, and slicing
- You should know how to read and troubleshoot job output
- You may need to integrate jobs with other tools via notifications and callbacks

---

### ✅ Easy Terms:
- **Job Template**: A saved setup that tells Ansible which playbook to run and how
- **Survey**: A form that asks the user for input when launching a job
- **Job Slicing**: Splitting one job into multiple parts to run in parallel
- **Label**: A tag that helps organize or filter jobs
- **Notification**: A message sent when a job starts or finishes (email, webhook, etc.)
- **Callback**: A way for another system to trigger a job in Ansible automatically

---

### 🛠️ UI Steps for Managing Job Execution:

#### 1. Launch a Job from a Template
- Go to **Templates**
- Click the rocket icon next to the job template name

#### 2. Add a Survey to a Job Template
- Edit a job template
- Click the **Survey** tab > **Add**
- Add questions (e.g., target host, version, user input)
- Save and enable the survey

#### 3. Apply a Label
- When editing a job template, scroll to the **Labels** section
- Add a label like `dev` or `critical`

#### 4. Use Job Slicing
- In the job template, find the **Job Slicing** field
- Choose a number (like 2) to split the job across slices
- This helps run large jobs faster on many hosts

#### 5. View Job Output and History
- Go to **Jobs**
- Click any job to see its standard output and status
- Review past jobs to troubleshoot or re-run

#### 6. Configure Notifications
- Go to **Notifications** > **Add**
- Choose type: Email, Slack, Webhook, etc.
- Connect it to job templates to trigger on start, success, or failure

#### 7. Enable Callback for External Launches
- In a job template, enable **Enable Webhook** or **Enable Callback**
- Choose the source (GitHub, GitLab, etc.)
- Save the template and copy the URL/token for external tools

---

### 💻 Helpful Commands (CLI):

Here is an example playbook used in this chapter:

```yaml
---
- name: Enable intranet services
  hosts: node1.lab.example.com
  become: true
  tasks:
    - name: latest version of httpd and firewalld installed
      ansible.builtin.yum:
        name:
          - httpd
          - firewalld
        state: latest

    - name: test html page is installed
      ansible.builtin.copy:
        content: "Welcome to the example.com intranet!
"
        dest: /var/www/html/index.html

    - name: firewalld enabled and running
      ansible.builtin.service:
        name: firewalld
        enabled: true
        state: started

    - name: firewalld permits http service
      ansible.posix.firewalld:
        service: http
        permanent: true
        state: enabled
        immediate: true

    - name: httpd enabled and running
      ansible.builtin.service:
        name: httpd
        enabled: true
        state: started

- name: Test intranet web server
  hosts: localhost
  become: false
  tasks:
    - name: connect to intranet web server
      ansible.builtin.uri:
        url: http://node1.lab.example.com
        return_content: true
        status_code: 200
```

These help test and validate job execution, especially for CLI-based testing and troubleshooting:

```bash
# Run a job template manually using ansible-navigator (for testing)
ansible-navigator run playbook.yml -i inventory/hosts.yml --eei my-ee

# Re-run a job that has failed or finished
# (Requires REST API or UI — not CLI)

# Run a playbook manually for testing (example: intranet.yml)
ansible-navigator run intranet.yml

# Test the web service from the target node
curl node1.lab.example.com

# Restore a modified playbook file from Git
git restore intranet.yml
```

---

### 📦 What You Should Be Able to Do:
- Launch and monitor jobs through the UI
- Use surveys to collect user input
- Apply labels and slicing to optimize jobs
- Read and troubleshoot job output
- Set up notifications and use webhooks/callbacks

---

This chapter is all about **controlling the jobs you launch** and making them flexible, organized, and responsive!

<br><br><br><br>
---

**Lesson 6 Quiz: Managing Task Execution**

---

**1. What is the default strategy used by Ansible for task execution across hosts?**

a) serial  
b) linear  
c) free  
d) rolling  
**Answer: b**

---

**2. What does the 'serial' keyword control in a playbook?**

a) Task execution order within a role  
b) File transfer method used by Ansible  
c) Number of hosts executing tasks in parallel at one time  
d) Network bandwidth throttling  
**Answer: c**

---

**3. Which strategy allows each host to complete tasks independently without waiting for others?**

a) free  
b) linear  
c) async  
d) rolling  
**Answer: a**

---

**4. How can you limit the number of forks used by Ansible when running tasks?**

a) In the playbook using `max_forks`  
b) In the inventory using `forks:`  
c) By setting `forks` in ansible.cfg  
d) Using the --limit-forks CLI flag  
**Answer: c**

---

**5. Which module is used to run tasks asynchronously in Ansible?**

a) async  
b) wait_for  
c) background  
d) poll  
**Answer: a**

---

**6. In asynchronous tasks, what does the 'poll: 0' directive do?**

a) Forces synchronous execution  
b) Disables polling entirely  
c) Lets the task run in the background without waiting  
d) Repeats the task until it fails  
**Answer: c**

---

**7. What is the purpose of the `strategy_plugins` directory in Ansible?**

a) To hold callback plugins  
b) To define new connection types  
c) To contain custom execution strategies  
d) To list third-party roles  
**Answer: c**

---

**8. What configuration file is used to define a custom strategy plugin path?**

a) inventory.ini  
b) playbook.yml  
c) ansible.cfg  
d) hosts.yml  
**Answer: c**

---

**9. What does setting `serial: 1` in a playbook accomplish?**

a) Runs one task per playbook  
b) Limits each play to one task at a time  
c) Limits task execution to one host at a time  
d) Makes the playbook run faster  
**Answer: c**

---

**10. Why would an administrator choose the `free` strategy over the default?**

a) To improve syntax highlighting  
b) To allow tasks to complete faster by removing host synchronization  
c) To apply global variables more consistently  
d) To increase logging detail  
**Answer: b**

---

**11. What does the `order` keyword do in a play?**

a) Sets the execution order of tasks  
b) Sorts inventory files by host group  
c) Controls the order in which hosts are selected from inventory  
d) Sets the order of handlers  
**Answer: c**

---

**12. What does the `run_once: true` directive accomplish?**

a) Ensures the playbook runs only once globally  
b) Runs the task on only the first host in the batch  
c) Runs the play once for all batches  
d) Executes one task per batch cycle  
**Answer: b**

---

**13. What is the purpose of the `max_fail_percentage` setting in a playbook?**

a) Allows multiple tasks to fail before aborting  
b) Sets a hard failure limit on the number of tasks  
c) Specifies the max percentage of hosts that can fail before the play aborts  
d) It adjusts async retry limits  
**Answer: c**

---

**14. What is the function of the `listen` directive in a task?**

a) Controls host ordering  
b) Triggers a handler under an alias from multiple tasks  
c) Runs multiple tasks in a loop  
d) Skips tagged tasks  
**Answer: b**

---

**15. What are `pre_tasks` and `post_tasks` used for in a playbook?**

a) Logging and output formatting  
b) Control execution order of custom plugins  
c) Run tasks before or after roles and main tasks  
d) Set limits on fork execution  
**Answer: c**

---

**16. What is the purpose of the `always`, `never`, `tagged`, and `untagged` special tags?**

a) Define default tags in templates  
b) Control task inclusion in special playbook modes  
c) Organize variable precedence  
d) Apply conditionals to group_vars  
**Answer: b**

---

**17. Which `ansible-navigator` options are used to control task inclusion based on tags? (Select two)**

a) --tags  
b) --skip-tags  
c) --task-limit  
d) --with-tags  
**Answer: a, b**

---

**18. What does enabling `profile_tasks` or `timer` callback plugins help you measure?**

a) YAML parsing failures  
b) Role dependencies  
c) Task execution time and performance insights  
d) Inventory reachability  
**Answer: c**

