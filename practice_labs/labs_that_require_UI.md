### ⚠️ Objectives that may OPTIONALLY involve the UI (but not required)

- **Manage advanced inventories**
  - Static or dynamic inventories usually configured via CLI, but Automation Controller UI can optionally manage dynamic inventory sources.

- **Upload execution environments into Automation Hub**
  - Automation Hub UI can optionally be used for verification or browsing images.

- **Publish a content collection**
  - Automation Hub UI is optional for viewing published collections, although typically published via CLI.

---

### ❌ Objectives that DO NOT REQUIRE AAP UI (CLI-focused)

- **Understand and use Git**
  - Clone a Git repository
  - Create, modify, and push files in a Git repository

- **Manage inventory variables**
  - Structure host and group variables
  - Override inventory host details and special variables

- **Manage task execution**
  - Control privilege execution
  - Run selected tasks from a playbook

- **Transform data with filters and plugins**
  - Populate variables from external sources
  - Use lookup and query functions
  - Implement loops using lookup plugins and filters
  - Inspect, validate, manipulate networking variables

- **Delegate tasks**
  - Run tasks for a managed host on a different host
  - Control fact delegation

- **Manage content collections**
  - Create and install a content collection

- **Manage execution environments**
  - Build and locally validate execution environments
  - Run playbooks in an execution environment (via ansible-navigator)

- **Manage inventories and credentials**
  - Create dynamic inventories (identity management or databases via plugins)

---

### ✅ Summary

AAP UI is mandatory mainly for operations involving Automation Controller job templates, managing credentials, integrating execution environments with Controller, and pulling content directly from Git or Automation Hub via Controller.

All other operations, including Git workflows, inventory and variable management, task control, lookups, filters, content collection creation, and initial execution environment builds, are primarily performed using the CLI.
