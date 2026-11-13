# NETWORKWALKS--GOWRIBALAMURUGAN-B084-WK1-PM1-CYBERSECURITY-LAB-SETUP




# Cybersecurity Lab Environment Setup

**Building an isolated virtual lab for penetration testing and ethical hacking practice**

Project: WK1-PM1 (Week 1, Project Module 1)  
Program: Networkwalks Cybersecurity Internship  
Author: Emmanuel  

---

## Project Overview

This project sets up a virtual cybersecurity and penetration-testing lab using VirtualBox and Kali Linux.

The lab provides a controlled, isolated environment for practising security tools, network scanning, reconnaissance, and vulnerability assessment safely and repeatedly.

It runs on a private virtual network, allowing additional machines such as Windows, Android, and CTF virtual machines to be added later as authorised testing targets.

## Objectives

- Use VirtualBox as the virtualisation platform.
- Set up Kali Linux as the attacker machine.
- Create a NAT Network using the subnet `10.0.0.0/24`.
- Enable bidirectional clipboard and drag-and-drop support.
- Share the host Downloads folder with the Kali virtual machine.
- Assign Kali Linux the static IP address `10.0.0.2/24`.
- Verify Internet access from Kali Linux.
- Create a clean virtual machine snapshot.

## Lab Architecture

```text
Host Operating System (Windows)
  |
  +-- VirtualBox
        |
        +-- NAT Network: NatNetwork (10.0.0.0/24, gateway 10.0.0.1)
              |
              +-- Kali Linux (Attacker) - 10.0.0.2/24
              +-- Future targets (Phase 2): Windows 11, Windows 10, Windows 7, Android, CTF virtual machines
  |
  +-- Internet
```

## IP Address Allocation

| Machine | Role | IP Address | Status |
|---|---|---|---|
| Kali Linux | Attacker | 10.0.0.2/24 | Configured |
| Windows 11 | Target | 10.0.0.11/24 | Planned |
| Windows 10 | Target | 10.0.0.10/24 | Planned |
| Windows 7 | Target | 10.0.0.7/24 | Planned |
| Android 9.x | Target | 10.0.0.9/24 | Planned |

## Requirements

| Item | Recommendation |
|---|---|
| RAM | 8 GB or more |
| Storage | 256 GB SSD or more |
| CPU | Intel Core i3 or i5, with virtualisation enabled in BIOS |
| Host operating system | Windows 10 or Windows 11 |

| Software | Version | Download |
|---|---|---|
| 7-Zip | Latest | https://7-zip.org/download.html |
| VirtualBox | Latest, version 7.x | https://virtualbox.org/wiki/Downloads |
| Kali Linux VirtualBox image | 2026.x | https://kali.org/get-kali |

---

## Setup Steps

### Step 1: Install 7-Zip

The Kali Linux pre-built VirtualBox image is distributed as a `.7z` archive, so 7-Zip is required to extract it.

1. Download the installer from the 7-Zip website.
2. Run the installer and complete the installation.

![7-Zip installed](screenshots/01-7zip-installed.png)

### Step 2: Install VirtualBox

1. Download the latest VirtualBox installer for your host operating system.
2. Run the installer using the default settings.
3. Open VirtualBox and confirm the installed version under **Help > About**.

![VirtualBox version](screenshots/02-virtualbox-version.png)

### Step 3: Create the NAT Network

1. In VirtualBox, open **File > Tools > Network** or press `Ctrl + H`.
2. Open the **NAT Networks** tab.
3. Select **Create**.
4. Configure the network as follows:

| Setting | Value |
|---|---|
| Name | NatNetwork |
| IPv4 Prefix | 10.0.0.0/24 |
| Enable DHCP | Enabled |
| Enable IPv6 | Disabled |

5. Select **Apply**.

![NAT Network created](screenshots/03-nat-network-created.png)

### Step 4: Download and Import Kali Linux

1. Visit https://kali.org/get-kali and open the **Virtual Machines** section.
2. Download the VirtualBox 64-bit image.
3. Extract the downloaded archive using 7-Zip.
4. In VirtualBox, select **Machine > Add** and choose the extracted `.vbox` file.

![Kali imported](screenshots/04-kali-imported.png)

### Step 5: Configure the Kali Virtual Machine

#### Network Adapter

Go to **Settings > Network > Adapter 1** and use the following configuration:

| Setting | Value |
|---|---|
| Enable Network Adapter | Enabled |
| Attached to | NAT Network |
| Name | NatNetwork |
| Adapter Type | Intel PRO/1000 MT Desktop (82540EM) |
| Promiscuous Mode | Allow All |
| Cable Connected | Enabled |

![Network adapter](screenshots/05-kali-network-adapter.png)

#### Clipboard and Drag-and-Drop

Go to **Settings > General > Advanced** and configure the following:

| Setting | Value |
|---|---|
| Shared Clipboard | Bidirectional |
| Drag and Drop | Bidirectional |

![Clipboard and drag and drop](screenshots/06-clipboard-dragdrop.png)

#### Shared Folder

Go to **Settings > Shared Folders > Add** and configure the following:

| Setting | Value |
|---|---|
| Folder Path | Host Downloads folder |
| Folder Name | Downloads |
| Auto-mount | Enabled |
| Make Permanent | Enabled |
| Read-only | Disabled |

Inside Kali Linux, the shared folder is usually mounted at:

```bash
/media/sf_Downloads
```

If access is denied, add your user to the `vboxsf` group and log in again:

```bash
sudo usermod -aG vboxsf $USER
```

![Shared folder](screenshots/07-shared-folder.png)

### Step 6: Configure a Static IP Address

1. Start the Kali Linux virtual machine.
2. Select the network icon in the top panel.
3. Choose **Edit Connections**.
4. Select **Wired connection 1**.
5. Open the **IPv4 Settings** tab.
6. Enter the following details:

| Field | Value |
|---|---|
| Method | Manual |
| Address | 10.0.0.2 |
| Netmask | 24 |
| Gateway | 10.0.0.1 |
| DNS Servers | 8.8.8.8, or 10.0.0.1 if Internet access fails |

7. Select **Save** and reconnect to the network.

![Static IP](screenshots/08-kali-static-ip.png)

### Step 7: Verify Network Connectivity

Run the following commands in Kali Linux:

```bash
ip a
ip route
ping -c 4 10.0.0.1
ping -c 4 8.8.8.8
ping -c 4 google.com
```

| Test | Expected Result |
|---|---|
| `ip a` shows `10.0.0.2/24` | Pass |
| Ping gateway `10.0.0.1` | Replies received |
| Ping `8.8.8.8` | Replies received |
| Ping `google.com` | Replies received |

![Connectivity tests](screenshots/09-connectivity-tests.png)

### Step 8: Create a Snapshot

1. Select the Kali Linux virtual machine in VirtualBox.
2. Open the **Snapshots** section.
3. Select **Take**.
4. Name the snapshot, for example:

```text
Fresh Kali - 10.0.0.2 - Working Internet
```

This creates a clean restore point before installing tools or making further changes.

![Snapshot](screenshots/10-snapshot.png)

---

## Troubleshooting

### Problem: Kali Linux Has No Internet Access After Setting a Static IP

**Symptoms**

The static IP address is configured correctly, but commands such as `ping 8.8.8.8` or `ping google.com` fail.

This issue can occur with VirtualBox version 7 and Kali Linux 2026.1 or newer.

**Solution**

Run the following commands to disable the IPv4 duplicate address detection delay and restart the network connection:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

Restart Kali Linux if the issue continues.

If Internet access still does not work, check the following:

- The NAT Network `10.0.0.0/24` was created correctly.
- The Kali virtual machine adapter is attached to the correct NAT Network.
- No other virtual machine on the same NAT Network is using the IP address `10.0.0.2`.
- DNS is set to `8.8.8.8` or `10.0.0.1`.
- The host firewall or antivirus software is not blocking VirtualBox.
- Restart both the virtual machine and the host computer.

## What I Learned

- How to create an isolated virtual lab using VirtualBox.
- How a NAT Network allows virtual machines to communicate while retaining Internet access.
- How to assign and troubleshoot a static IP address in Kali Linux using NetworkManager and `nmcli`.
- How shared folders, clipboard sharing, and drag-and-drop improve the lab workflow.
- Why virtual machine snapshots are important for safe and repeatable testing.

## Next Steps: Phase 2

- Add Windows 11, Windows 10, Windows 7, and Android virtual machines to the same NAT Network.
- Test connectivity between all virtual machines.
- Create a snapshot for each virtual machine.
- Add intentionally vulnerable and CTF machines for authorised practice.

## Repository Structure

```text
NETWORKWALKS-EMMANUEL-B083-WK1-PM1-CYBERSECURITY-LAB-SETUP/
|-- README.md
`-- screenshots/
    |-- 01-7zip-installed.png
    |-- 02-virtualbox-version.png
    |-- 03-nat-network-created.png
    |-- 04-kali-imported.png
    |-- 05-kali-network-adapter.png
    |-- 06-clipboard-dragdrop.png
    |-- 07-shared-folder.png
    |-- 08-kali-static-ip.png
    |-- 09-connectivity-tests.png
    `-- 10-snapshot.png
```

---

## Legal Disclaimer

This project is for educational and research purposes only. It was built and tested in an isolated virtual lab on my own device.

Security testing is legal only when:

- You test a device or network that you own or control.
- You have written and documented permission from the owner.
- You are working as an authorised security professional within an agreed scope.

Unauthorised access to computer systems is illegal in most countries. I accept full responsibility for my actions.

## Acknowledgements

- Networkwalks Academy, for the programme and project guidance.
- Instructor: Waqas Karim, CCIE.

`#Networkwalks` `#Cybersecurity` `#EthicalHacking` `#KaliLinux` `#VirtualBox`
```
 
