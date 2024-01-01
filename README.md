<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1 align = "center">Installing and Configuring Active Directory in Microsoft Azure</h1>
This lab demonstrates how to install and configure Active Directory using Azure. We will be creating two virtual machines on the same network, one as the <b>Domain Controller</b> and other VM will be used as a <b>Client</b>. Afterwards, we will configure the Active Directory to allow the Client to join the domain as well as creating user accounts using a Powershell script. Check out my guide here for creating <a href = "https://github.com/joshuafinchCC/VM-VN-RDC">Virtual Machines within Microsoft Azure</a>

<br />

<h2>Environments and Technologies Used</h2>
<ul>
  <li>Microsoft Azure</li>
  <li>Remote Desktop</li>
  <li>Active Directory Domain Services</li>
  <li>Powershell</li>
  <li>(OPTIONAL): Notepad for recording login information</li>
</ul>

<br />

<h2>Operating Systems Used</h2>
<ul>
  <li>Windows Server 2022</li>
  <li>Windows 10 Pro (21H2)</li>
</ul>

<br />

<h2>Installation Steps</h2>

<h3>Setting up Resources in Azure</h3>

<p>
  <ul>
    <li>Our first machine will be set up as the <b>Domain Controller</b> using the image <b>Windows Server 2022 Datacenter: Azure Edition - x64 Gen2</b> For the rest of this tutorial, we will refer to this VM as <b>DC1</b></li>
 </ul>
  
 <p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/b93356fc-2a1c-4176-a0fb-270bf6691cad" height="60%" width="60%">
        </p>  
    
<ul> 
<li>Once DC1 is fully deployed, we will set it's IP Address as <i>static</i> since having it dynamic will make it difficult for the Domain Controller to communicate with our client VM.</li>
<li>Go to your Virtual Machines in Azure, go to <b>Networking</b> and then click on the <b>Network Interface</b>. Head to <b>IP Configurations</b> under <b>settings</b>, click the ipconfig link to open up a window to toggle the IP configuration and allocation to <b>Static</b>.</li>
</ul>
      <li>IP Configuration for <b>DC1</b></li>
      <p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/5e9db87a-48a0-4841-9304-d1a8f517e749" height="80%" width="80%">
        </p>  
<ul> 
<li>After configuring the IP settings for <b>DC1</b>, you can now set up your second virtual machine on the same network as <b>DC1</b>. Ensure that this VM is running off the Windows 10 OS, and not the windows server we selected for <b>DC1</b>. Once finished your resource group should look something like this:</li>
</ul>

<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/6e67b10a-5570-4458-8222-ca1df16cc277" height="80%" width="80%">
        </p>  
<br />

<h3>Ensuring Connectivity</h3>

<p>
<li>Next, we will log into <b>DC1</b> through remote desktop to allow <b>Client1</b> to ping it. Search <b>Windows Defender Firewall with Advanced Security</b> on the start menu, head to the <b>Inbound Rules</b> and enable the rules under the protocol <b>ICMPv4</b>-- specifically <i>Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In). There will be two rules for this, Private and Domain. Enable both</i></li>
<li>You will notice a <b>Server Manager</b> will automatically open on <b>DC1</b> upon logging in through remote desktop. You can leave this open for later</li>
    <ul>
	<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/e219ceec-8f90-4e94-b5b3-e56d3cc19684" height="80%" width="80%">
        </p>  
    </ul>
    <li>Now if we log into <b>Client 1</b> and ping it, we should see some replies from <b>DC1</b>. In this instance, our <b>DC1's Private IP</b> is 10.0.0.4, while <b>Client1's Private IP</b> is 10.0.0.5</li>
    <p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/716f15f4-7936-4e22-ad25-f1d5924637d1" height="60%" width="60%">
        </p>  
<br />

<h3>Installing Active Directory on the Domain Controller</h3>

<p>
  <ul>
	  <li>While logged into <b>DC1</b>, go to the Server Manager Dashboard and click on <b>Add Roles and Features</b>. Go through the installation process and upon getting to <b>Server Roles</b>, make sure to check the box for <b>Active Directory Domain Services</b>. After adding this feature, proceed through the rest of the installation</li>

<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/9cdcdd6c-61cb-4e48-aace-4ac1bfe4e1d8" height="60%" width="60%">
        </p>  
   <li>Once installed, we now have to promote the server to a domain controller. To do so, you may notice a <b>warning notification</b> on the top right where the flag icon is. Click the flag and select <b>Promote this server to a domain controller</b>. Click on Add a new forest and specify a domain name. For this tutorial, we'll name the domain <b>myportfolio.com</b>, specifiy the password, and proceed with the install. Noted, you will be automatically signed out, re-log in through Remote Desktop, and the installation is completed</li>
<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/5ca0f104-b7aa-4121-9b4a-21f8f5b31475" height="40%" width="40%">
        </p>  
<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/29bc8e90-e060-43d3-9157-8cca99bb564b" height="40%" width="40%">
        </p>  
   
  </ul>
</p>
<br />
<h3>Important Notes</h3>
<p>
  <ul>
          <li>While setting up the domain, you may have to wait a few moments between each installation step as the installer populates each field. Once the installation is complete you will be signed out of <b>DC1</b></li>  
	  <li>When logging back in to the domain controller VM through Remote Desktop Connection, it is important to log in with the <b>context of the domain.</b></li>
          <li>Type out the domain path and then the name of the user ([domain name].com\[user name]). For example: <b>myportfolio.com\portfoliouser.</b></li>  
  </ul>
<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/da97c7d1-1d1f-4b56-b900-c43361f76e47" height="20%" width="20%">
        </p>  
  


<br />

<h2>Configuration Steps</h2>

<h3>Creating Organizational Units (OUs) and Users</h3>

<p>
  <ul>
    <li>Through the Server Manager, you can select <b>Tools</b> -> <b>Acitve director Users and Computers</b>This menu allows us access to Organizational Units</li>
        <p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/2f2bdb96-194b-4298-a2c2-65c5ab13edb3" height="40%" width="40%">
        </p>  
   <li>Organizational Units act like folders that hold information, privileges, and login access of users in the directory</li>
   <li>We can select the dropdown for our domain(myportfolio.com) and make two new OUs, <b>_ADMIN</b> and <b>_EMPLOYEES</b>. We will use these OU's for a later step when we start creating multiple accounts for our domain</li>

<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/a384239f-c0b9-4183-861b-01adc0a69e06" height="40%" width="40%">
        </p> 
   
<li>In the _ADMIN OU, we'll create the user <b>Jane Smith</b> with the user name <b>jane_admin</b> and password of your preference (NOTE: rememberthe password you create as it will be needed for login). </li>
<li>When creating your new admin's password, be sure to unceck the box requiring the user to change their password at next logon</li>
<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/9fbe9986-3765-49e7-b93e-238380a4af58" height="40%" width="40%">
        </p> 

<li>We'll be granting Jane some admin privileges: even though this user is part of an organizational unit <b>labled _ADMINS</b> this does not actually grant them admininstrative capabilities. To grant them permissions, right click on the user and open their <b>Properties</b>, click <b>Member Of</b> and then <b>Add</b>. Here we can type "domain" to both check the names of all the security groups under "domain" as well as add Jane to said security group. Select <b>Domain Admins</b> and hit OK</li>

<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/da9d9596-3343-4fa5-8a82-86cacf199be6" height="60%" width="60%">
        </p> 
<li>Now we can log into <b>DC1</b> with the username "jane_admin" and the password we set for them. This will be an administrative login, allowing us to continue to make changes</li>
 
<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/f27ae4d0-fc1a-4c63-b429-d3fc03ee327c" height="20%" width="20%">
        </p> 

<br />

<h3>Joining the Client1 to the Domain</h3>

<p>
  <ul>
    <li>To allow <b>Client1</b> to log into our newly created domain, first we need to configure this VM's Domain Name System (DNS) server. Go to your Client VM in the Azure Portal and go to <b>Networking</b> and then <b>Network Interface</b>. Head to <b>DNS Servers</b> under <b>settings</b>, and set the DNS Server to <b>Custom</b>. Then, enter the Domain Controller's private IP address and save the changes. Restart the client VM in order to ensure the DNS changes are saved.(NOTE: Be sure you are using <b>DC1's Private IP Address</b> for the DNS server. In this case it is 10.0.0.4)</li>

<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/779e0681-f96d-4aef-a948-18515b2699f6" height="40%" width="40%">
        </p> 
 <li>Save your new DNS settings and then restart <b>Client1</b></li>
 <li>Note: When logging into <b>Client1</b> you will still need to use your original username and password, as we havent actually added Client1 to the domain yet. </li>
 <li>Right click your Windows Icon and select system, click on Rename this PC (advanced) and Change.</li>
    <li>Enter the domain and necessary credentials in order to add <b>Client1</b> to the domain (logging in as jane_admin). It is important to note that the login credentials have to be input within the context of the domain path (myportfolio.com\jane_admin).</li>

<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/f41db32d-08c2-406b-9417-0878327d6c8a" height="60%" width="100%">
        </p> 
   <li>After selecting OK, you will get a Windows Security prompt asking you to log in with an account that has permissions to join the domain. Simply use your jane_admin login. Afterwards, <b>Client1</b> will prompt you to restart: After the restart is complete you can now log in with your domain credentials instead of your normal username and password. On the domain controller, the client should now appear in Computers in the Active Directory Users and Computers panel.</li>

<h3>Setting up Remote Desktop for non-administrative users on Client VM</h3>

<li>Once you re-login through your jane_admin on <b>Client1</b>, you can right click the windows icon and select System. Select Remote Desktop on the right and then click "Select Users that can remotely access this PC" at the bottom. Currently only jane_admin can login to our domain, but we want to set it so every user added to the domain can also log into this computer! </li>
    
<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/f41db32d-08c2-406b-9417-0878327d6c8a" height="60%" width="100%">
        </p> 
<li>Click "Add" and then type "domain users" and check names. This will allow every member of the <b>Domain Users</b> securtity group to also log into Client1</li>
<br />

<h3>Creating Users and attempt to log into the Client VM with one of the users</h3>

<p>
  <ul>
    <li>For our last experiment, we will add a brand new user to the domain and try logging into <b>Client1</b></li>
    <li>Using our jane_admin account, log into <b>DC1</b> through Remote Desktop connection. Search "Active Directory Users and Computers". Here we can select the _EMPLOYEES folder we created and add a new user (right click->new->user). Set up your new employee's account information and password (note that the new user is automatically a part of domain users!)</li>
    <p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/0818a3b5-6f12-4acc-9012-88e9e30d5cd9" height="60%" width="80%">
        </p> 

<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/861b2fd0-d3cc-420a-881f-f9b3e7a6e26f" height="60%" width="80%">
        </p> 
   
   <li>Attempt to log in the Client VM using the generate username you have selected (username being <b>mydomainname.com\username</b>) and the password you set</li>
  </ul>

<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/2b852389-18e2-4e8b-a0c2-d4e92c5c6f07" height="30%" width="30%">
        </p> 
   
<li>ALMOST DONE! if everything is setup correctly, we should be able to log into this brand new account thats has never before logged into <b>Client1</b></li>

<p align="center">
        <img src="https://github.com/joshuafinchCC/Activedirectory-config/assets/155266044/911d2b90-89f6-4536-888e-fe533db26f85" height="30%" width="30%">
        </p> 
<b>Success!</b>
<br />
