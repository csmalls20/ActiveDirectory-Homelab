# Active Directory Homelab

This project demonstrates the process of creating and setting up an Active Directory environment, consisting of the creation and management of users and groups, configuration of domain services, and integration of a Windows client with a Domain Controller.


## Lab Environment
**Note:** The host OS used for this project is a MacBook Pro with an Apple M1 Pro chip and 16GB of memory. As a result, the ISO image used to install the Domain Controller needed to be compatible with the **ARM64** architecture. The other specifications listed reflect personal preferences; alternative configurations can be used to create a similar environment. Links to each resource are provided below.

**Hypervisor**: [UTM](https://mac.getutm.app/)

**Domain Controller:** [Windows Server 2025](https://archive.org/details/26404.5000.250426-1100.-ge-prerelease-serverstandard-oemret-a-64-fre-en-us) (ISO)

**Client:** [Windows 11](https://www.microsoft.com/en-us/software-download/windows11arm64)


## VM Creation
Both virtual machines were created using UTM with nearly identical hardware specifications, with the exception of storage capacity. The virtual machines also differ in the operating system ISO used for installation and their respective machine names, with one configured as the Domain Controller and the other as the Windows client. The following steps were performed to create each machine:

### Domain Controller

1. Select **Create a New Virtual Machine**.

![UTM home screen](/images/vm-creation/01-utm-home.png)

2. Select **Virtualize**. 

![Method of virtualization](/images/vm-creation/02-virtualization-method.png)

3. Select **Windows**, followed by the following hardware configuration:
- **Memory:** 4096 MB (4 GB)
- **Processors:** 2

![Choose OS](/images/vm-creation/03-choose-os.png)
![Memory selection page](/images/vm-creation/04-set-ram.png)

4. Select **Install Windows 10 or higher** and **Install drivers and SPICE tools**. Then click `Browse...` and select the Windows installation ISO.

![ISO selection page](/images/vm-creation/05-choose-iso.png)
![ISO file](/images/vm-creation/06-choose-iso2.png)

5. Set the storage size to **60 GiB**.

![Server storage size selection](/images/vm-creation/07-storage-size.png)

6. Rename the machine to **DC01** and click `Save`.

![Rename server VM](/images/vm-creation/08-rename-servervm.png)

7. Right-click the virtual machine and select **Edit**.

![Edit VM](/images/vm-creation/09-edit-vm.png)

8. Under **Devices**, click `New...` and select **Network**.

![Add new network](/images/vm-creation/10-add-network.png)

9. Set the **Network Mode** to **Host Only** and click `Save`.

![Choose network](/images/vm-creation/11-choose-network.png)
*Two network adapters were configured for each virtual machine: one for communication between the two devices and another for internet access. This configuration keeps the lab network isolated while still allowing the virtual machines to access the internet.*


### Client
The same VM creation process was used for the Windows client, with differences in the Windows installation ISO, storage capacity, and virtual machine name.
- **Storage:** 40 GiB
- **Virtual Machine Name:** Client01

![Client ISO selection](/images/vm-creation/12-client-iso.png)
![Client storage size selection](/images/vm-creation/13-client-storage.png)
![Rename client VM](/images/vm-creation/14-rename-clientvm.png)


## Server Configuration

### Windows Installation
Once the virtual machine was configured, Windows Server 2025 was installed using the default installation settings. Additionally, the UTM Windows Guest Tools were installed to improve performance and display resolution, although their installation is not required.

### Rename Server
After installing Windows Server 2025, the server was assigned a hostname that identifies its role as the Domain Controller. To avoid confusion, the server was given the same name as the virtual machine it is operating in, **DC01**.

The hostname was configured through **Server Manager → Local Server → Computer name → Change**. After renaming the server, the computer was restarted to apply the changes made. 

![Server Rename](/images/server-configuration/server-namechange/01-server-rename.png)

### Network Configuration 

Following the assignment of a new hostname, the server was configured with a static IP address. The network configuration was accessed through **Server Manager → Local Server** by selecting **IPv4 address assigned by DHCP, IPv6 enabled** next to **Ethernet**.

![Network connections window](/images/server-configuration/network-config/01-network-connections.png)
*As mentioned earlier, the virtual machine was configured with two network adapters: one for communication between the devices and another for internet access. Ethernet 2 was configured with the Host Only network mode and was therefore selected for the static IP configuration.*

By right-clicking **Ethernet 2** and navigating to **Properties → Internet Protocol Version 4 (TCP/IPv4)→ Properties**, the server was configured with the following network settings:
- **IP address:** `192.168.64.10`
- **Subnet Mask:** `255.255.255.0`
- **DNS:** `127.0.0.1` 

![Ethernet properties window](/images/server-configuration/network-config/02-static-ip.png)
*To ensure the changes were applied, the server was restarted.*

### Active Directory Installation

After logging in, the **Add Roles and Features Wizard** was accessed through **Server Manager → Manage → Add Roles and Features**. The default settings were accepted until reaching the **Server Roles** section.

![Add Roles and Features Wizard](/images/server-configuration/ad-config/01-server-roles.png)

By selecting **Active Directory Domain Services → Add Features** and proceeding through the wizard, the **Active Directory Domain Services** role was installed.

![AD role selection](/images/server-configuration/ad-config/02-role-selection.png)
![AD role installation](/images/server-configuration/ad-config/03-role-installation.png)

The next step after role installation was to promote the server to a domain controller. Without closing the wizard, **Promote the server to a domain controller** was clicked.

![Installation complete / promote AD server](/images/server-configuration/ad-config/04-promote-server.png)

For the deployment operation, **Add a new forest** was chosen along with the domain name "**adlab.test**" before clicking `Next`.

![Add domain forest](/images/server-configuration/ad-config/05-add-forest.png)

A password was set for Directory Services Restore Mode (DSRM) before continuing the configuration process. The remaining options were left at their default settings. Once the installation was completed, the server was automatically restarted for the changes to go into effect.

![Set DSRM password](/images/server-configuration/ad-config/06-dsrm-password.png)

### Organizational Unit Creation

