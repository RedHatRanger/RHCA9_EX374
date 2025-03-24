# Proxmox VM Setup Guide for Ansible Automation Platform 2.4

This guide provides step-by-step instructions for setting up a virtual machine on Proxmox that will host a single-node Ansible Automation Platform 2.4 installation with Automation Controller, Private Automation Hub, and PostgreSQL database.

## Prerequisites

- Proxmox VE 7.x or later installed and operational
- Internet access for the Proxmox host
- Red Hat Enterprise Linux 8.8+ or 9.0+ ISO available
- Red Hat Ansible Automation Platform 2.4 setup bundle downloaded

## Hardware Requirements

Based on the official Red Hat documentation, the following specifications are recommended for a single-node AAP setup:

| Component | Minimum Requirement |
|-----------|---------------------|
| CPU | 6 vCPUs |
| RAM | 24 GB |
| Storage | 100 GB |
| Network | 1 Gbps |

## VM Creation Steps

1. **Log in to Proxmox Web Interface**
   - Open a web browser and navigate to your Proxmox server's IP address (https://your-proxmox-ip:8006)
   - Log in with your credentials

2. **Create a New Virtual Machine**
   - Click on "Create VM" in the top right corner
   - In the "General" tab:
     - Enter a VM ID (e.g., 100)
     - Enter a Name (e.g., "aap-single-node")
     - Click "Next"

3. **Configure OS Settings**
   - In the "OS" tab:
     - Select "Use CD/DVD disc image file (iso)"
     - Browse and select your RHEL ISO
     - OS Type: Linux
     - Version: Red Hat Enterprise Linux 8 or 9 (depending on your ISO)
     - Click "Next"

4. **Configure System Settings**
   - In the "System" tab:
     - Graphics card: Default
     - SCSI Controller: VirtIO SCSI
     - Qemu Agent: Checked
     - BIOS: Default (SeaBIOS)
     - Machine: Default (i440fx)
     - Click "Next"

5. **Configure Disk Settings**
   - In the "Hard Disk" tab:
     - Bus/Device: SCSI
     - Storage: Select your preferred storage
     - Disk size: 100 GB
     - Format: QEMU image format (qcow2)
     - Cache: Default (No cache)
     - Discard: Checked
     - Click "Next"

6. **Configure CPU Settings**
   - In the "CPU" tab:
     - Sockets: 1
     - Cores: 6
     - Type: Host
     - Click "Next"

7. **Configure Memory Settings**
   - In the "Memory" tab:
     - Memory: 24576 MB (24 GB)
     - Ballooning Device: Checked
     - Click "Next"

8. **Configure Network Settings**
   - In the "Network" tab:
     - Bridge: vmbr0 (or your preferred bridge)
     - Model: VirtIO
     - MAC Address: Auto
     - Click "Next"

9. **Confirm Settings and Create VM**
   - Review your settings
   - Click "Finish"

## RHEL Installation

1. **Start the VM**
   - Select your newly created VM
   - Click "Start"
   - Click "Console" to access the VM console

2. **Install RHEL**
   - Follow the standard RHEL installation process
   - Select "Server with GUI" as the base environment
   - Configure networking with a static IP address
   - Create a root password (use "redhat" for consistency with other guides)
   - Create a user account (e.g., "ansible" with password "redhat")

3. **Complete Installation and Reboot**
   - Wait for the installation to complete
   - Reboot the VM

## Post-Installation Configuration

1. **Register with Red Hat Subscription Manager**
   ```bash
   sudo subscription-manager register --username=<your-username> --password=<your-password>
   ```

2. **Attach a Subscription**
   ```bash
   sudo subscription-manager attach --auto
   ```

3. **Enable Required Repositories**
   
   For RHEL 8:
   ```bash
   sudo subscription-manager repos --enable=rhel-8-for-x86_64-baseos-rpms
   sudo subscription-manager repos --enable=rhel-8-for-x86_64-appstream-rpms
   sudo subscription-manager repos --enable=ansible-automation-platform-2.4-for-rhel-8-x86_64-rpms
   ```
   
   For RHEL 9:
   ```bash
   sudo subscription-manager repos --enable=rhel-9-for-x86_64-baseos-rpms
   sudo subscription-manager repos --enable=rhel-9-for-x86_64-appstream-rpms
   sudo subscription-manager repos --enable=ansible-automation-platform-2.4-for-rhel-9-x86_64-rpms
   ```

4. **Update the System**
   ```bash
   sudo dnf update -y
   ```

5. **Install Required Packages**
   ```bash
   sudo dnf install -y chrony wget unzip tar
   ```

6. **Configure Firewall**
   ```bash
   sudo firewall-cmd --permanent --add-service=https
   sudo firewall-cmd --permanent --add-service=http
   sudo firewall-cmd --permanent --add-port=5432/tcp  # PostgreSQL
   sudo firewall-cmd --permanent --add-port=27199/tcp # Automation Hub
   sudo firewall-cmd --reload
   ```

7. **Configure NTP with Chrony**
   ```bash
   sudo systemctl enable --now chronyd
   sudo chronyc sources
   ```

8. **Set Hostname**
   ```bash
   sudo hostnamectl set-hostname aap.example.com
   ```

9. **Update /etc/hosts**
   ```bash
   sudo echo "127.0.0.1 localhost" > /etc/hosts
   sudo echo "$(hostname -I | awk '{print $1}') aap.example.com aap" >> /etc/hosts
   ```

## VM Snapshot

Before proceeding with the AAP installation, it's recommended to create a snapshot of your VM:

1. **Shut Down the VM**
   ```bash
   sudo shutdown -h now
   ```

2. **Create a Snapshot in Proxmox**
   - Select your VM in the Proxmox web interface
   - Click on "Snapshots"
   - Click "Take Snapshot"
   - Enter a name (e.g., "pre-aap-install")
   - Add a description
   - Click "Create"

## Next Steps

Your Proxmox VM is now ready for the Ansible Automation Platform installation. Proceed to the [AAP Installation Guide](aap_installation_guide.md) for the next steps.

## Troubleshooting

### VM Performance Issues
- If the VM is running slowly, consider increasing the CPU cores or memory
- Ensure your Proxmox host has sufficient resources available

### Network Connectivity Issues
- Verify the VM can access the internet: `ping google.com`
- Check the network configuration: `ip addr show`
- Verify DNS resolution: `nslookup redhat.com`

### Subscription Management Issues
- If you encounter issues with subscription-manager, verify your Red Hat credentials
- Ensure your subscription includes access to Ansible Automation Platform
