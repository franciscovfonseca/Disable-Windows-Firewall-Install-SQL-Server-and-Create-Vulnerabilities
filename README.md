<br>

# Disable Windows Firewall, Install SQL Server & Create Vulnerabilities

<br>


![Banner](https://github.com/user-attachments/assets/74cda7d0-a70b-4459-b53a-70078edb326f)
<br />
<br />

In this lab we're going to install and set up a **SQL Server Database** inside of the **Windows Virtual Machine**.

We're also going to **Disable the Windows Internal Firewall**.

Finally we'll **Remote Desktop Protocol** into the **Linux VM** and also **Test Ping and Log into it via SSH**, to make sure we can get in and everything is working.

<br />

<details close> 
<summary> <h2> 1️⃣ RDP Into the Windows VM</h2> </summary>
<br>

The first step is to copy the **Public IP Address** of the ```windows-vm```

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)


Using the **Microsoft Remote Desktop** ➜ Remote into the VM using that IP Address and:

- Username: ```labuser```
- Password: ```Cyberlab123!```

![azure portal](https://github.com/user-attachments/assets/59c10e90-33da-4cf3-a40c-802f76edf858)

  </details>

<h2></h2>

<details close> 
<summary> <h2>2️⃣ Turn off Windows Firewall</h2> </summary>
<br>

> By **Turnning off the Firewall**, the Virtual Machine is essentially going to respond to ping requests and all sorts of traffic ➜ making it easier to be discovered on the internet by bad actors.
> 
> We have previously opened up the **NSG** (which in a sense is the **Azure Firewall**), but inside of the Operating System there's another **Firewall** ➜ so we're going to disable that.

<br>

Back in our Wondows Vm, the first thing to do is **Disable the Internal Windows Firewall**.

Inside the Windows Firewall ➜ click on **Windows Defender Firewall Properties**.

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

You can **[Download SQL Server here](https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2019)**

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
> 
> This is just an app that essentially let's us log into **SQL Server Database** and visualize things.
> 
> Basically we're going to use SSMS to attempt to log in and **Generate Logs** or **Failure to Authenticate Logs**.

<br>

You can **[Download SSMS here](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)**

Open the **SSMS Setup ENU exe** File, install it and Restart the Vm:

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)


>   <details close> 
>   
> **<summary> 💡 Note</summary>**
> 
> Again this is just an App that let's us connect to our SQL Database.
> 
> Because our Virtual Machine is completely exposed to the Internet: The NSG is wide open & the local Firewall is wide open ➜ theoretically anyone could attempt to connect to the SQL Database we just installed.
> 
> It doesn't have to be someone on the VM, it can be someone from anywhere worldwide, as long as they can access our VM's IP Address.
> 
>   </details>

<br>

  </details>

<h2></h2>
<details close>
  
<summary> <h2>5️⃣ Enable Logging for SQL Server to be ported into Windows Event Viewer</h2> </summary>
<br>

> The next thing we're going to do is **Enable Logging for SQL Server**, in order to send the logs to the **Windows Event Log**.
> 
> This part is a bit troublesome to do ➜ there's a few steps we have to do to **Enable Logging for SQL Server**.

<br>

You can **[Follow this Link to Write SQL Server Audit Events to the Security log](https://learn.microsoft.com/en-us/sql/relational-databases/security/auditing/write-sql-server-audit-events-to-the-security-log?view=sql-server-ver16)**

<br>

  <details close> 
  
**<summary> 📝 Summary</summary>**

We can view all the logs for the Windows VM through the **Event Viewer**.

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

For example if we go to Windows **Logs** > **Security** > click on one of the **Events / Logs** ➜ we can se the details: ***"An account was successfully logged on."***

Whenever someone fails a login, or has a succesful login ➜ that's going to be recorded in the **Event Viewer** and we can see it:

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

<br>

> Basically what we're doing right now is set up the **SQL Server** ➜ so that when somebody **Fails to Authenticate** against it, we'll be able to see the logs for that in the **Event Viewer**
> 
> And to achieve that we first need to provide full permissions for the SQL Server service account to the registry hive (**Registry Editor**).
> 
> The **Windows Registry** is a place in the computer where we can make a lot of granular configurations to affect the way the OS behaves.

  </details>

<br>

<br>

First we're going to open the **Registry Editor**:

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

Paste the following Registry path inside it (instead of browsing to it):

```HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Security```

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

Then we'll right-click the **Security** key > click on **Permissions**

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

Add the ```NETWORK SERVICE``` account to the permission > and thick the ☑ boxes **Full Control** and **Read**

Click **"Apply"** and then **"OK"**:

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

<br>

<h2></h2>

<br>

Now we'll **Enable Auditing from SQL Server**

From the Start menu > type **cmd** > right-click on **Command Prompt** and **Run as administrator**

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

Paste the following **statement** > press **"Enter"** > and you can see that the command was **successfully executed** ✔️

```auditpol /set /subcategory:"application generated" /success:enable /failure:enable```

<br>

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

<br>

<h2></h2>

<br>

The next thing to do is open the **SSMS**, log into it and **Enable Auditing**.

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

To Connect to the SQL Server ➜  we can select **"SQL Server Authentication"** and use the **SQL Server system administrator credentials** we had set up earlier:

- Username: ```sa```
- Password: ```Cyberlab123!```

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

Then we'll go to the **Properties** of the Server we just connected to > go to **"Login auditing"** > and check ◉ **Both failed and successful logins**

This way all the login attempts can be logged to the **Event Log**

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

And finally we just have to **Restart** the Server ➜ right-click on the **windows-VM SQL Server** and click on **"Restart"**:

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

  </details>

<h2></h2>

<details close> 
<summary> <h2>6️⃣ Test SQL Logging to make sure it is Working Properly</h2> </summary>
<br>

We'll now "attempt" to reconnect to the SQL Server **Intentionally Using a Wrong Password**:

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

Then close **SSMS** and open the **Event Viewer**.

Under the **Windows Logs** > **Application Log** ➜ ⚠️ this is where the **SQL Server Login Attempts** are going to be recorded.

You can see bellow the Event of the **Login failed** we intentionally generated using a Wrong Pasword:

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

✅ We can confirm that this is working properly.

<br>

  </details>

<h2></h2>

<details close> 
<summary> <h2>7️⃣ Test Ping & Logging into the Linux VM via SSH</h2> </summary>
<br>

> The next thing we have to do is Ping the Linux Virtual Machine ➜ make sure it is pingable:
> 
> We'll log into the Linux VM with Secure Shell (SSH) Protocol
>
> <br>
> 
>   <details close> 
>   
> **<summary> 💡 Note</summary>**
> 
> In our case here there's no Linux interface by default ➜ so when we log in to our VM we're just going to use a Command Line Interface.
> 
>   </details>

<br>

Back to the Azure Portal, we'll go to the ```linux-vm``` > copy the **Public IP Address**

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

- Then if you're running Windows ➜ open **Powershell**-
- But if you're running Mac ➜ open **Terminal**

We're going to attempt to ping our **Linux VM** > type in the **IP Address**:

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

✅ We can confirm that we were able to successfully **Ping the Linux VM**:

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

Now to SSH into the Virtual Machine, we have our ```linux-vm``` Username & Password:

- Username: ```labuser```
- Password: ```Cyberlab123!```

To connect into a machine with SSH we just type:

```commandline
ssh USERNAME@IPADDRESS
```
And then we press "Enter"

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

<br>

>   <details close> 
>   
> **<summary> 💡 Note</summary>**
> 
> Then it'll ask us if we want to trust the certificate that the Virtual Machine is "offering" to establish the SSH connection:
> 
> - So we're just going to say **"Yes"** to it.
> 
>   ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)
> 
> Then we'll import the certification into our computer, and our computer will trust it ✔️
> 
>   </details>

<br>

Then we'll just have type in our **Password** for SSH to log into the **Linux VM**.

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

You'll see that your prompt changed to **labuser@linux-vm**

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

To confirm you´re logged in you can:

- Type ```uname -a``` and it will tell you what Operating System it is running: **Linux**
- And you can also type ```id``` and it will tell you **labuser**

  ![VM create](https://github.com/user-attachments/assets/fd16cae4-cdfd-45c8-b0a3-d94a04c9677d)

✅ This is how you know you were **Successfully Logged into the Linux VM**.

<br>

 <details close> 

> **<summary> 💡 Note</summary>**
> 
> We're not going to be doing much with this Linux VM in this lab ➜ it's just another Endpoint for people to attack
> 
> Later we'll intall something in the Linux Machine that will let us forward the Logs into our Log Repository.
> 
> But for now I just wanted to make sure it is running ➜ and it is ✔️
> 
>  </details>

<br>

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
  
 
 
