**Lab 6: Managing Content Collections**

**Objectives**

1. **Create an Ansible Content Collection scaffold** using `ansible-galaxy collection init`.
2. **Install a community collection** into your project with `ansible-galaxy collection install`.
3. **Publish your custom collection** to a private Automation Hub using `ansible-galaxy collection publish`.

---

#### Lab Overview

In this lab, you’ll build and share reusable Ansible content. First, you’ll scaffold a new collection structure locally. Next, you’ll install an existing community collection into your project. Finally, you’ll package and publish your custom collection to a private Automation Hub so that others in your organization can consume it.

### 1. Environment & Setup

- **Control node:** workstation.lab.example.com
- **Private Hub:** hub.lab.example.com (login: `student` / `redhat123`)
- **Project root:** `/home/student/lab6-collections`

```bash
ssh student@workstation.lab.example.com    # password: redhat123
sudo yum install -y ansible
mkdir ~/lab6-collections && cd ~/lab6-collections
```

---

### 2. Scaffold a New Collection  ← Objective 1

1. Run the Galaxy CLI to create a new collection skeleton:

    ```bash
    ansible-galaxy collection init my_namespace.my_collection
    ```

2. Examine the generated directory:

    ```text
    my_namespace-my_collection/
    ├── docs/
    ├── galaxy.yml
    ├── plugins/
    │   ├── modules/
    │   └── roles/
    └── README.md
    ```

3. Edit **`galaxy.yml`** and fill in metadata:

    ```yaml
    namespace: my_namespace
    name: my_collection
    version: 0.1.0
    readme: README.md
    authors:
      - Your Name <you@example.com>
    ```

---

### 3. Install an Existing Collection  ← Objective 2

1. Create a local `collections/` directory:

    ```bash
    mkdir collections
    ```

2. Install `community.crypto` into that path:

    ```bash
    ansible-galaxy collection install community.crypto -p collections/
    ```

3. Verify installation:

    ```bash
    ansible-galaxy collection list | grep community.crypto
    ```

---

### 4. Add Content to Your Collection

1. Create a sample role inside your collection:

    ```bash
    cd my_namespace-my_collection
    ansible-galaxy role init plugins/roles/sample_role
    ```

2. In **`plugins/roles/sample_role/tasks/main.yml`**, add:

    ```yaml
    ---
    - name: Sample role task
      ansible.builtin.debug:
        msg: "Hello from sample_role!"
    ```

3. Update **`README.md`** with usage instructions.

---

### 5. Publish to Private Automation Hub  ← Objective 3

1. Log in to your private Hub registry:

    ```bash
    export ANSIBLE_GALAXY_SERVER_HUB_TOKEN=$(# copy token from Hub UI)
    ```

2. Publish the collection:

    ```bash
    ansible-galaxy collection publish \
      --server hub \
      --api-key $ANSIBLE_GALAXY_SERVER_HUB_TOKEN \
      --repository rh-certified \
      my_namespace-my_collection/
    ```

3. Confirm in the Hub UI under **Collections » Published**.

---

### 6. Consume Your Collection

1. In a fresh project:

    ```bash
    mkdir ~/test-project && cd ~/test-project
    ansible-galaxy collection install my_namespace.my_collection -p collections/
    ```

2. Create **`play.yml`**:

    ```yaml
    ---
    - hosts: localhost
      collections:
        - my_namespace.my_collection

      tasks:
        - name: Run sample_role
          import_role:
            name: sample_role
    ```

3. Run the play:

    ```bash
    ansible-playbook -i localhost, play.yml
    ```

---

### 7. Wrap-up

- You scaffolded and published a custom collection.
- You installed both community and private collections.
- You can now share and reuse automation content via Automation Hub.

*End of Lab 6: Managing Content Collections*
