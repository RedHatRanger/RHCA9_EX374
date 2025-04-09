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

