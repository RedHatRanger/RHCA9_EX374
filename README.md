# Red Hat Certified Architect (RHCA) - Ansible Automation Platform Study Guide

This repository contains a comprehensive study guide for preparing for the Red Hat Certified Specialist in Developing Automation with Ansible Automation Platform exam (EX374), which counts towards the Red Hat Certified Architect (RHCA) certification.

## Overview

This self-contained study guide is designed for individuals who want to learn Ansible Automation Platform without requiring a Red Hat learning subscription. It provides step-by-step instructions for setting up a single-node AAP environment on Proxmox with:

1. Automation Controller
2. Private Automation Hub
3. PostgreSQL database

## Repository Structure

- **[guides/](guides/)**: Detailed installation and configuration guides
  - Proxmox VM setup guide
  - AAP 2.4 installation guide
  - PostgreSQL configuration guide
  
- **[labs/](labs/)**: Hands-on labs covering all EX374 exam objectives
  - Git repository management
  - Task execution management
  - Data transformation with filters and plugins
  - Task delegation
  - Content collection management
  - Execution environment management
  - Inventory and credential management
  - Automation controller management

- **[images/](images/)**: Screenshots and diagrams for visual reference

- **[scripts/](scripts/)**: Helper scripts for lab setup and configuration

## Prerequisites

- A Proxmox server (or similar virtualization platform)
- Basic Linux administration skills
- Access to Red Hat Ansible Automation Platform installation files
- Internet access for downloading packages

## Getting Started

1. Begin with the [Proxmox VM Setup Guide](guides/proxmox_setup_guide.md)
2. Follow the [AAP Installation Guide](guides/aap_installation_guide.md)
3. Complete the [PostgreSQL Configuration Guide](guides/postgres_configuration_guide.md)
4. Work through the labs in the [labs directory](labs/) in order

## Exam Objectives

This study guide covers all objectives for the EX374 exam:

- Understand and use Git
- Manage task execution
- Transform data with filters and plugins
- Delegate tasks
- Manage content collections
- Manage execution environments
- Manage inventories and credentials
- Manage automation controller

For a detailed breakdown of the exam objectives, see [EX374 Objectives](ex374_objectives.md).

## Notes

- All passwords in configuration examples are set to "redhat" for simplicity
- This guide is intended for lab/study environments, not production use
- All labs are provided in Markdown (.md) format for easy viewing on GitHub

## Contributing

Feel free to submit issues or pull requests to improve this study guide.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
