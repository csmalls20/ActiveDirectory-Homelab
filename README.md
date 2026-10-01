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

![UTM home screen](/images/01-utm-home.png)

2. Select **Virtualize**. 

![Method of virtualization](/images/02-virtualization-method.png)

3. Select **Windows**, followed by the following hardware configuration:
* **Memory:** 4096 MB (4 GB)
* **Processors:** 2


![Choose OS](/images/03-choose-os.png)
![Memory selection page](/images/04-set-ram.png)

4. Select **Install Windows 10 or higher** and **Install drivers and SPICE tools**. Then click **Browse...** and select the Windows installation ISO.

![ISO selection page](/images/05-choose-iso.png)
![ISO file](/images/06-choose-iso2.png)

5. Set the storage size to **60 GiB**.

![Server storage size selection](/images/07-storage-size.png).

6. Rename the machine to **DC01** and click `Save`.

![Rename server VM](/images/08-rename-servervm.png)

7. Right-click the virtual machine and select **Edit**.

![Edit VM](/images/09-edit-vm.png)

8. Under `Devices`, click **New...** and select **Network**.

![Add new network](/images/10-add-network.png)

9. Set the **Network Mode** to **Host Only** and click `Save`.

![Choose network](/images/11-choose-network.png)
*Two network adapters were configured for each virtual machine: one for communication between the two devices and another for internet access. This configuration keeps the lab network isolated while still allowing the virtual machines to reach the internet.*


### Client
The same steps performed for the Domain Controller were replicated with differences in Windows ISO installation, storage size, and name configuration.
* **Storage:** 40 GiB
* **Virtual Machine Name:** Client01

![Client ISO selection](/images/12-client-iso.png)
![Client storage size selection](/images/13-client-storage.png)
![Rename client VM](/images/14-rename-clientvm.png)


## Server Configuration

### Windows Installation