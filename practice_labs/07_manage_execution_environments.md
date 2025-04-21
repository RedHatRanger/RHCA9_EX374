**Lab 7: Managing Execution Environments**

**Objectives**

1. **Build a custom automation execution environment** using **ansible-builder**
2. **Run playbooks** inside a specified execution environment with **ansible-navigator**
3. **Upload execution environment images** to a private Automation Hub registry
4. **Configure and use** execution environments in Automation Controller 2.4

---

#### Lab Overview

In this lab, you’ll create a portable, container‑based execution environment for Ansible, test it locally, push it to a private Automation Hub registry, and configure Automation Controller to run jobs inside it. This ensures consistent, reproducible automation runs in development and production.

### 1. Environment & Setup

- **Control node (Controller UI):** controller.lab.example.com (AAP 2.4)  
- **Build node (CLI):** workstation.lab.example.com  
- **Private Hub registry:** hub.lab.example.com (user `student` / pass `redhat`)  
- **Project root:** `/home/student/lab7-ee`  

```bash
ssh rhel@workstation.lab.example.com    # password: redhat
sudo yum install -y ansible ansible-builder podman
mkdir -p ~/lab7-ee && cd ~/lab7-ee
```

---

### 2. Build a Custom Execution Environment  ← Objective 1

1. Create `execution-environment.yml`:
   ```yaml
   version: 1
   dependencies:
     galaxy:
       requirements.yml: requirements.yml
     python:
       requirements: requirements.txt
   ```
2. Define your requirements files:
   - **`requirements.yml`** for Ansible Collections:
     ```yaml
     collections:
       - name: community.crypto
         version: 2.7.0
     ```
   - **`requirements.txt`** for Python libraries:
     ```text
     requests>=2.25.0
     ```
3. Build the execution environment with ansible-builder:
   ```bash
   ansible-builder build \
     --tag hub.lab.example.com/custom-ee:latest \
     --container-runtime podman
   ```

---

### 3. Validate Locally with ansible-navigator  ← Objective 2

1. List and inspect your new image:
   ```bash
   ansible-navigator images \
     --eei hub.lab.example.com/custom-ee:latest
   ```
2. Run a sample playbook inside it:
   ```bash
   cat > sample-play.yml << 'EOF'
   - hosts: localhost
     gather_facts: false
     tasks:
       - name: Test custom EE
         ansible.builtin.uri:
           url: https://www.example.com
           return_content: false
   EOF

   ansible-navigator run sample-play.yml \
     --eei hub.lab.example.com/custom-ee:latest \
     --pp missing
   ```

---

### 4. Push to Private Automation Hub  ← Objective 3

1. Log in to your hub registry:
   ```bash
   podman login hub.lab.example.com \
     --username student --password redhat
   ```
2. Push the built image:
   ```bash
   podman push \
     hub.lab.example.com/custom-ee:latest
   ```

---

### 5. Configure in Automation Controller  ← Objective 4

1. In the Controller web UI, navigate to **Execution Environments → Add** and enter:
   - **Name:** custom-ee  
   - **Image URL:** `hub.lab.example.com/custom-ee:latest`  
2. Save and then use **custom-ee** in a Job Template’s **Execution Environment** setting.  
3. Launch the job and inspect stdout to confirm it runs inside your custom EE.

---

### 6. Wrap‑up

- You defined and built a custom Execution Environment with `ansible-builder`.  
- You tested it locally with `ansible-navigator`.  
- You published it to your private Automation Hub registry.  
- You configured Automation Controller to leverage your execution environment.

*End of Lab 7: Managing Execution Environments*
