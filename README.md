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
* **Memory:** 4096 MB (4 GB)
* **Processors:** 2


![Choose OS](/images/vm-creation/03-choose-os.png)
![Memory selection page](/images/vm-creation/04-set-ram.png)

4. Select **Install Windows 10 or higher** and **Install drivers and SPICE tools**. Then click **Browse...** and select the Windows installation ISO.

![ISO selection page](/images/vm-creation/05-choose-iso.png)
![ISO file](/images/vm-creation/06-choose-iso2.png)

5. Set the storage size to **60 GiB**.

![Server storage size selection](/images/vm-creation/07-storage-size.png).

6. Rename the machine to **DC01** and click `Save`.

![Rename server VM](/images/vm-creation/08-rename-servervm.png)

7. Right-click the virtual machine and select **Edit**.

![Edit VM](/images/vm-creation/09-edit-vm.png)

8. Under `Devices`, click **New...** and select **Network**.

![Add new network](/images/vm-creation/10-add-network.png)

9. Set the **Network Mode** to **Host Only** and click `Save`.

![Choose network](/images/vm-creation/11-choose-network.png)
*Two network adapters were configured for each virtual machine: one for communication between the two devices and another for internet access. This configuration keeps the lab network isolated while still allowing the virtual machines to access the internet.*


### Client
The same VM creation process was used for the Windows client, with differences in the Windows installation ISO, storage capacity, and virtual machine name.
* **Storage:** 40 GiB
* **Virtual Machine Name:** Client01

![Client ISO selection](/images/vm-creation/12-client-iso.png)
![Client storage size selection](/images/vm-creation/13-client-storage.png)
![Rename client VM](/images/vm-creation/14-rename-clientvm.png)


## Server Configuration

### Windows Installation
Once the virtual machine was configured, Windows Server 2025 was installed using the default installation settings. Additionally, the UTM Windows Guest Tools were installed to improve performance and display resolution, although their installation is not required.

### Rename Server
After installing Windows Server 2025, the server was assigned a hostname that identifies its role as Domain Controller. To avoid complication, the server was given the same name as the virtual machine it is operating in, **DC01**.

1. Open **Server Manager** and select **Local Server**.
2. Click 

![Server Manager (Local Manager) screen](/images/15-server-manager-screen.png)



![Server Rename](/images/16-server-rename.png)