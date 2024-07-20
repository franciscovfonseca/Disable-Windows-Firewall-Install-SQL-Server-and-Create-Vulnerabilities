<br>

# Disable Windows Firewall, Install SQL Server & Create Vulnerabilities

<br>


![Banner](https://github.com/user-attachments/assets/74cda7d0-a70b-4459-b53a-70078edb326f)
<br />
<br />

To kick off my **Azure Honeynet project**, we must first set up the **Virtual Machines (VMs)** we'll use.

Virtual machines are like computers in the cloud, and they'll form the foundation of our Honeynet.

Here are the steps we'll take in **Microsoft Azure**:

<br />

<details close> 
<summary> <h2> 1️⃣ Remote Into the VM (windows-vm)</h2> </summary>
<br>

The first step is to copy the **Public IP Address** of the ```windows-vm```

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)


Using the **Microsoft Remote Desktop** 🡆 Remote into the VM using that IP Address and:

- Username: ```labuser```
- Password: ```Cyberlab123!```

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)

  </details>

<h2></h2>

<details close> 
<summary> <h2>2️⃣ Turn off Windows Firewall</h2> </summary>
<br>

> By **Turnning off the Firewall**, the Virtual Machine is essentially going to respond to ping requests and all sorts of traffic 🡆 making it easier to be discovered on the internet by bad actors.
> 
> We have previously opened up the **NSG** (which in a sense is the **Azure Firewall**), but inside of the Operating System there's another **Firewall** 🡆 so we're going to disable that.

<br>

Back in our Wondows Vm, the first thing to do is **Disable the Internal Windows Firewall**.

Inside the Windows Firewall 🡆 click on **Windows Defender Firewall Properties**.

Then trun of the **Firewall State** for the **Domain**, **Private** and **Public Profiles**:

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)

  </details>

<h2></h2>

<details close> 
<summary> <h2>3️⃣ Install SQL Server Evaluation</h2> </summary>
<br>

> We're going to use SQL Server as another component of our Honeynet that we can let attacker discover and try to hack into.
> 
> We're not actually going to do anything with SQL, we're not going to put any data in there, it's just going to serve as another Endpoint for people to attack and we're going to ghenerate logs with it.

<br>

You can **[download SQL Server here](https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2019)**

Download the EXE file and install it on the VM:

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)

In this case we're going to **Download Media**:

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)

We'll download an **ISO** and put it on the **Desktop**:

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)

After it's been downloaded, right-click the ISO file and click **Mount**:

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)

Look for the **SqlSetup** on your PC and then actaully install SQL by clicking on the **setup** file:

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)

Afte the SQL setup opens, just click **Instalation** and then **New SQL Server stand-alone installation**:

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)

Accept the "license terms" and click "Next" until reaching the **Feature Selection** Tab where you want to tick the ☑ **Database Engine Services** check box:

We'll use Mixed Mode for SQL Server Authentication and Windows Authentication:

By default SQL Server can have an admin account called "sa" (for system administrator), so we'll set up the password for this

- Username: ```sa``` (default)
- Password: ```Cyberlab123!```

We'll also add the current Windows User, which will make our User ```labuser``` able to Authenticate and "log into" our SQL instance.

Click on **"Add Current User"** and it will add the current user ```labuser``` as well

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)

  </details>

<h2></h2>

<details close> 
<summary> <h2>4️⃣ Install SSMS (SQL Server Management Studio)</h2> </summary>
<br>

> The next thing we're going to do is install **SQL Server Management Studio**.

> This is just an app that essentially let's us log into **SQL Server** and visualize things.

> Basically we're going to use SSMS to attempt to log in and **Generate Logs** or **Failure to Authenticate Logs**.

<br>

You can **[Download SSMS here](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms )**





- Same **Region**, **Resource Group**, and **VNet** as the ```windows-vm``` Virtual Machine

- I named this second **VM** ```linux-vm```

- For the **Image** I selected the ```Ubuntu Server 20.04 LTS```

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

- I used the same **Size**, **Admin Username and Password** as the ones used for the Windows VM

- For **Virtual Network**, I made sure I selected the same VNet ```Lab-VNet``` that I had created while setting up the Windows VM.


<br>

  </details>

<h2></h2>
<details close> 
<summary> <h2>5️⃣ NSG & Inbound Security Rule Configuration</h2> </summary>
<br>

> In this step I opened up **Network Security Groups (NSGs)** for both VMs:
> 
> - Configure the NSGs (Layer 4 Firewalls) to allow **all inbound traffic**.

<br>

### Navigate to the Network Security Group (NSG):

- In the Azure portal, search for "**Network Security Groups**" in the search bar at the top.
- Once there, select the NSG associated with your virtual machine.

 ![NSG](https://github.com/user-attachments/assets/f65a25f9-c6a7-461e-8542-3a5a3c84ca38)
  
### Create an inbound security rule:

- Inside the NSG, you'll find a section for "**Inbound security rules**".
- This is where we control what kind of traffic is allowed to reach our VM.
- Click on "***Add***" to create a "**new rule**".

 ![NSG](https://github.com/user-attachments/assets/13424b7c-9402-4ed2-bc14-51e3de7ba08c)

### Configure the rule:

- We'll be prompted to input some details about our new rule.
  
### Source:

- This defines where the incoming traffic is coming from.
  
  - We can set this to ```Any``` to allow traffic from any location.
  
### Source port ranges:

- This specifies the ports on the source (the computer initiating the connection) that are allowed.

  - Again, we can set this to ```*``` or ```Any``` to allow all ports.

### Destination:

- This defines where the traffic is going to.

  - Since we want the traffic to reach our VM, we can set this to ```Any```.
  
### Destination port ranges:

- This specifies the ports on our VM that are allowed to receive traffic.

  - We can set this to ```*``` or ```Any``` to open all ports.
  
### Priority:

- Setting priorities in Network Security Groups (NSGs) is an essential step.
- The priority determines the order in which rules are applied.
- Rules with lower priority numbers are processed before rules with higher priority numbers because the lower the number, the higher the priority.

  - For the purpose of this lab, I set the priority to ```300``` to ensure that this honeypot functions as intended!

### Action:

- We'll set this to ```Allow```, which means that traffic matching this rule will be allowed to reach our VM. 
  
 ![NSG](https://github.com/franciscovfonseca/Setting-Up-Vulnerable-VMs-in-Azure/assets/172988970/0bf17e13-1f81-42ac-8692-dbf3bbbdf893)

  
### Review & Create:

- After configuring all the details we need for this inbound rule, click "***Add***" to **Create the Rule**.

<br>

➡️ I then followed the exact same process for the ```linux-vm``` Virtual Machine

<br>

  </details>

<h2></h2>

<br>

# Conclusion

<br>

By **Creating our VMs** and **Open Inbound Security Rules**, we're essentially leaving the front door of our VMs wide open.

This is generally not something you'd do in a real production environment, as it would make your system extremely vulnerable to attacks.

However, in the context of our honeynet, it's exactly what we want to do!

This allows us to **Attract Potential Attackers** and **Observe their Actions in a Controlled Environment**.

  
<br />
<br />
  
 
 
