# Active Directory Homelab

This project demonstrates the setup and management of an Active Directory environment, including domain controller deployment, user and group management, organizational unit configuration, and integration of a Windows client into the domain.


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
- **Memory:** `4096 MB (4 GB)`
- **Processors:** `2`

![Choose OS](/images/vm-creation/03-choose-os.png)
![Memory selection page](/images/vm-creation/04-set-ram.png)

4. Select **Install Windows 10 or higher** and **Install drivers and SPICE tools**. Then click `Browse...` and select the Windows installation ISO.

![ISO selection page](/images/vm-creation/05-choose-iso.png)
![ISO file](/images/vm-creation/06-choose-iso2.png)

5. Set the storage size to `60 GiB`.

![Server storage size selection](/images/vm-creation/07-storage-size.png)

6. Rename the machine to `DC01` and click `Save`.

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
- **Storage:** `40 GiB`
- **Virtual Machine Name:** `Client01`

![Client ISO selection](/images/vm-creation/12-client-iso.png)
![Client storage size selection](/images/vm-creation/13-client-storage.png)
![Rename client VM](/images/vm-creation/14-rename-clientvm.png)


## Server Configuration

### Windows Installation

Once the virtual machine was configured, `Windows Server 2025` was installed using the default installation settings. Additionally, the UTM Windows Guest Tools were installed to improve performance and display resolution, although their installation is not required.


### Server Hostname Configuration

After installing Windows, the server was assigned a hostname that identifies its role as the Domain Controller. To avoid confusion, the server was given the same name as the virtual machine it is operating in, `DC01`.

The hostname was configured through **Server Manager → Local Server → Computer name → Change**. After renaming the server, the computer was restarted to apply the changes made. 

![Server rename](/images/server-configuration/server-namechange/01-server-rename.png)


### Network Configuration 

Following the assignment of a new hostname, the server was configured with a static IP address. The network configuration was accessed through **Server Manager → Local Server** by selecting **IPv4 address assigned by DHCP, IPv6 enabled** next to **Ethernet**.

![Network connections window](/images/server-configuration/network-config/01-network-connections.png)
*As mentioned earlier, the VM was configured with two network adapters: one for communication between the virtual machines and another for internet access. The connection labeled "Unidentified network" corresponds to the **Host Only** network adapter. Therefore, the **Ethernet 2** adapter was selected for static IP configuration.* 

By right-clicking **Ethernet 2** and navigating to **Properties → Internet Protocol Version 4 (TCP/IPv4) → Properties**, the server was configured with the following network settings:
- **IP address:** `192.168.108.10`
- **Subnet Mask:** `255.255.255.0`
- **DNS:** `127.0.0.1` 

![IPv4 properties window](/images/server-configuration/network-config/02-static-ip.png)
*To ensure the changes were applied, the server was restarted.*


### Active Directory Installation

After re-logging in, the **Add Roles and Features Wizard** was accessed through **Server Manager → Manage → Add Roles and Features**. The default settings were accepted until reaching the **Server Roles** section.

![Add Roles and Features Wizard](/images/server-configuration/ad-config/01-server-roles.png)

By selecting **Active Directory Domain Services → Add Features** and proceeding through the wizard, the **Active Directory Domain Services** role was installed.

![AD role installation](/images/server-configuration/ad-config/02-role-installation.png)


### Domain Controller Deployment

The next step after role installation was to promote the server to a domain controller. Without closing the wizard, **Promote the server to a domain controller** was clicked.

![Installation complete / promote AD server](/images/server-configuration/ad-config/03-promote-server.png)

For the deployment operation, **Add a new forest** was chosen along with the domain name `adlab.local` before clicking `Next`.

![Add domain forest](/images/server-configuration/ad-config/04-add-forest.png)

A password was set for Directory Services Restore Mode (DSRM) before continuing the configuration process. The remaining options were left at their default settings. Once the installation was completed, the server was automatically restarted for the changes to go into effect.

![Set DSRM password](/images/server-configuration/ad-config/05-dsrm-password.png)


## Client Configuration

### Windows Installation

Like the Windows Server installation, the default settings were used, with the exception of selecting `Windows 11 Pro` as the installation image. The UTM Windows Guest Tools were also installed on the client, but as mentioned earlier, their installation is not a requirement.

![Windows image selection](/images/client-configuration/01-image-selection.png)


### Network Configuration

After installing `Windows 11`, the client was configured to use the Domain Controller's IP address for DNS resolution. By navigating to **Control Panel → Network and Internet → Network and Sharing Center → Change adapter settings**, the **Network Connections** window was opened.

![Network connections window](/images/client-configuration/02-network-connections-window.png)

By right-clicking **Ethernet 2** and navigating to **Properties → Internet Protocol Version 4 (TCP/IPv4) → Properties**, the client was configured to use the DNS address `192.168.108.10`.

![Client IPv4 properties window](/images/client-configuration/03-dns-config.png)


### Client Hostname Configuration

The hostname `Client01` was configured through **Control Panel → System and Security → System → Rename This PC**. To ensure the changes made were applied, the computer was restarted after renaming the client. 

![Client rename](/images/client-configuration/04-rename-client.png)


### Client Domain Join

After logging back in, `Client01` was added to the domain `adlab.local` by navigating to **Control Panel → System and Security → System → Domain or workgroup → Change...**.

![Add client to domain](/images/client-configuration/05-add-to-domain.png)
![Domain added verification](/images/client-configuration/06-domain-add-verification.png)


## Active Directory Management

### Organizational Unit Creation

With `Client01` configured, the focus shifted towards Active Directory management through the creation of OUs, users, and groups. **Active Directory Users and Computers** was accessed through **Server Manager → Tools → Active Directory Users and Computers**, where the newly created domain and its existing organizational units were displayed.

![Domain OU window](/images/server-configuration/ad-management/01-domain-ou-window.png)

By right-clicking `adlab.local` and selecting **New → Organizational Unit**, an OU named **IT** was created.

![IT OU creation](/images/server-configuration/ad-management/02-ou-name.png)

Following this, the same process was performed on the `IT` organizational unit,  creating two sub-OUs: `Computers` and `Users`.

![Sub-OU creation](/images/server-configuration/ad-management/03-sub-ous.png)

Two additional OUs, `Finance` and `HR`, were created under the `adlab.local` domain using the same sub-OU structure.

![Domain AD Structure](/images/server-configuration/ad-management/04-ad-structure.png)


### User Account Creation

After setting up the Active Directory structure of `adlab.local`, users were created in their respective OUs. The **Users** OUs were selected, and **New → User** was chosen to create each account. The table below displays all accounts created.

| User | OU      | User logon name |
|------|---------|-----------------|
| Adam | IT      | asmith          |
| John | IT      | jwilliams       |
| Mary | Finance | mwilson         |
| Ryan | HR      | rjohnson        |

![Create user wizard](/images/server-configuration/ad-management/05-create-user.png)
![User password creation](/images/server-configuration/ad-management/06-user-password.png)
*Since this is a lab environment, **User must change password at next logon** was left unchecked. In a real-world environment, it is generally recommended that users change the password assigned by the administrator at their first logon.*


### Group Creation and User Assignment

To simplify permission management, security groups were created and users were assigned to the appropriate groups. By right-clicking `IT` and selecting **New → Group**, a group was created with the following configurations:
- **Group name:** `IT Support`
- **Group scope:** `Global`
- **Group type:** `Security`

![IT Support group creation](/images/server-configuration/ad-management/07-ITSupport-group.png)

After creating the `IT Support` group, one of the recently created users was added. By double-clicking the user, navigating to **Member Of → Add...**, entering `IT Support`, and selecting **Check Names**, the user `Adam` was successfully added to the group.

![Add user to IT Support group](/images/server-configuration/ad-management/08-IT-add-to-group.png)

The same process was repeated with the `HR` OU, creating the `HR Staff` group and assigning `Ryan` as a member.

![HR Staff group creation](/images/server-configuration/ad-management/09-HRStaff-group.png)
![Add user to HR Staff group](/images/server-configuration/ad-management/10-HR-add-to-group.png)


### Client Computer Addition

In addition to assigning users to security groups, computer accounts were organized into organizational units that reflect their respective departments. Since the lab contains only one client virtual machine, `Client01` was designated as a computer belonging to the `IT` department. By navigating to the default `Computers` container, right-clicking `Client01`, and selecting **Move**, the computer account was moved to the `Computers` OU under `IT`.

![Default computers OU window](/images/server-configuration/ad-management/11-default-computer-ou.png)
![Move computer window](/images/server-configuration/ad-management/12-move-computer.png)

## Group Policy

To demonstrate centralized administration and policy management, Group Policy Objects (GPOs) were created and linked to the appropriate OUs. The policies below were configured through **Server Manager → Tools → Group Policy Management**, with each GPO linked to the appropriate OU based on its intended scope.

### Restrict Control Panel Access

Control Panel is a Windows configuration tool that allows users to view, manage, and modify system settings. In an organizational environment, access to these settings may be restricted to prevent unauthorized changes. For this lab, a GPO was created to restrict Control Panel access for users in the `HR` organizational unit.

By right-clicking `HR` and selecting **Create a GPO in this domain, and Link it here...**, the GPO `Restrict Control Panel Access` was created.

![Group Policy Management structure](/images/group-policy/control-panel/01-group-policy-management.png)
![Control Panel restriction GPO](/images/group-policy/control-panel/02-control-panel-gpo.png)

Following its creation, the GPO was modified to enforce restrictions on users who have the ability to access Control Panel. By right-clicking `Restrict Control Panel Access`, selecting **Edit**, and navigating to **User Configuration → Policies → Administrative Templates → Control Panel → Prohibit access to Control Panel and PC settings**, the policy was enabled.

![Control Panel GPO list](/images/group-policy/control-panel/03-control-panel-gpolist.png)
![Enable Control Panel policy window](/images/group-policy/control-panel/04-control-panel-enable.png)

After configuring the policy, it was verified by logging in to `Client01` as the user `Ryan`. Since Ryan is a member of the `HR` department, he was not permitted to open Control Panel.

![Control Panel restriction pop up](/images/group-policy/control-panel/05-ryan-restriction.png)

### Password Policy

A password policy is an essential component of information security, helping reduce the risk of unauthorized access to company resources. To simulate password management practices in a business environment, a domain-wide password policy was configured.

Since the policy applies across the domain, the **Default Domain Policy** was configured by navigating to **Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy**. The following settings were modified:
- **Minimum password length:** `10 characters`
- **Maximum password age:** `60 days` 

![Minimum password length configuration](/images/group-policy/password-policy/01-min-password-length.png)
![Maximum password age](/images/group-policy/password-policy/02-max-password-age.png)
*These settings were configured specifically for the lab environment and may not reflect recommended security practices in a production environment.*

## Key Skills Demonstrated
- **Active Directory Administration:** Deployed a domain Controller and configured an Active Directory domain using Active Directory Domain Services (AD DS).
- **User and Group Management:** Created and managed user accounts and security groups.
- **Organizational Unit (OU) Management:** Designed a departmental OU structure to organize users and computer accounts.
- **Network Configuration:** Configured static IP addressing and DNS settings to support communication between the Domain Controller and Windows client.
- **Domain Integration**: Joined a Windows 11 Pro client to the Active Directory domain.
- **Group Policy Management:** Created and linked Group Policy Objects to restrict Control Panel access for HR users and configure domain password settings.