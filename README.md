<br>

# Disable Windows Firewall, Install SQL Server & Create Vulnerabilities

<br>


![Banner](https://github.com/user-attachments/assets/74cda7d0-a70b-4459-b53a-70078edb326f)
<br />
<br />

In this lab we're going to install and set up a **SQL Server Database** inside of the **Windows Virtual Machine**.

We're also going to **Disable the Windows VM's Internal Firewall**.

Finally we'll **Remote Desktop Protocol** into the **Linux VM** and also **Test Ping and Log into it via SSH**, to make sure we can log in and everything is working.

<br />

<details close> 
<summary> <h2> 1️⃣ RDP Into the Windows VM</h2> </summary>
<br>

The first step is to copy the **Public IP Address** of the ```windows-vm```

![azure portal](https://github.com/user-attachments/assets/ae6c81e4-19f9-4a1d-a825-1cd13475f40b)


Using the **Microsoft Remote Desktop** ➜ Remote into the VM using that IP Address and:

- Username: ```labuser```
- Password: ```Cyberlab123!```

![azure portal](https://github.com/user-attachments/assets/ebe1bc6f-f8d5-4e72-9c5c-f69ddb17eeea)

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

![azure portal](https://github.com/user-attachments/assets/1dd6b59f-32bf-4d3b-a833-1320712cb100)

![azure portal](https://github.com/user-attachments/assets/bfdc76ec-18df-4bb8-b333-4d751bc12819)

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

![azure portal](https://github.com/user-attachments/assets/cdfec461-080f-4c95-b1cf-642b9f51bbb8)

In this case we're going to **Download Media**:

![azure portal](https://github.com/user-attachments/assets/d7ada334-1027-4d79-96a6-64c0d3886a51)

We'll download an **ISO** and put it on the **Desktop**:

![azure portal](https://github.com/user-attachments/assets/102e4370-e867-4abe-b528-a7d06e25cbea)

After it's been downloaded, right-click the ISO file and click **Mount**:

![azure portal](https://github.com/user-attachments/assets/e09133dd-5867-4e4e-b119-f4ea8f37358c)

Look for the **SqlSetup** on your PC and then actaully install SQL by clicking on the **setup** file:

![azure portal](https://github.com/user-attachments/assets/3adff4a4-f948-40c4-a01b-23a1c164d9a8)

Afte the SQL setup opens, just click **Instalation** and then **New SQL Server stand-alone installation**:

![azure portal](https://github.com/user-attachments/assets/b9aa18d0-f3bb-405d-a874-d64c149c1a59)

Accept the "license terms" and click "Next" until reaching the **Feature Selection** Tab where you want to tick the ☑ **Database Engine Services** check box:

We'll use ***Mixed Mode for SQL Server Authentication and Windows Authentication***:

By default SQL Server can have an admin account called **"sa"** (for system administrator), so we'll set up the password for this:

- Username: ```sa``` (default)
- Password: ```Cyberlab123!```

We'll also **Add the Current Windows User** ➜ which will make our User ```labuser``` able to **Authenticate and Log Into our SQL instance**.

Click on **"Add Current User"** and it will add the current user ```labuser``` as well

![azure portal](https://github.com/user-attachments/assets/f608f98f-3ae9-4a95-9248-303ef2bfc368)

![azure portal](https://github.com/user-attachments/assets/e4fee95e-257a-4fee-8964-72233b722581)

After all that > when you reach the **"Ready to Install"** Tab ➜ click **"Install"**

![azure portal](https://github.com/user-attachments/assets/d6e5c9f1-5cee-4674-ad90-d2536c0e416a)

  </details>

<h2></h2>

<details close> 
<summary> <h2>4️⃣ Install SSMS (SQL Server Management Studio)</h2> </summary>
<br>

> The next thing we're going to do is install **SQL Server Management Studio**.
> 
> This is just an app that essentially let's us log into **SQL Server Database** and visualize things.
> 
> Basically we're going to use SSMS to attempt to log in and **Generate Logs**.

<br>

You can **[Download SSMS here](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)**

Open the **SSMS-Setup-ENU.exe** File from the Downloads > **Install** it > and **Restart the Vm**:

  ![VM create](https://github.com/user-attachments/assets/f51b171f-0f49-4808-8fd2-56545cdbe638)

  ![VM create](https://github.com/user-attachments/assets/d10649b8-8398-4812-95ca-da4cb2066b68)

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

  ![VM create](https://github.com/user-attachments/assets/4c18ad14-d3fb-4682-8db9-a57ab6afdef7)

For example if we go to Windows **Logs** > **Security** > click on one of the **Events / Logs** ➜ we can se the details: ***"An account was successfully logged on."***

Whenever someone fails a login, or has a succesful login ➜ that's going to be recorded in the **Event Viewer** and we can see it:

  ![VM create](https://github.com/user-attachments/assets/bb0cf8e2-5902-4583-ab4f-f64c46db7636)

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

  ![VM create](https://github.com/user-attachments/assets/34f643ca-676c-475e-ac11-82810483ae7e)

Paste the following Registry path inside it (instead of browsing to it):

```HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Security```

  ![VM create](https://github.com/user-attachments/assets/19e41d71-12c3-42d6-b192-7d82e5d6da0a)

Then we'll right-click the **Security** key > click on **Permissions** > then click on **"Add"**

  ![VM create](https://github.com/user-attachments/assets/79bad0cb-5d12-4773-a144-9ad8ae5f78f4)

Add the ```NETWORK SERVICE``` account to the permission > and thick the ☑ boxes for **"Full Control"** and **"Read"**

Click **"Apply"** and then **"OK"**:

  ![VM create](https://github.com/user-attachments/assets/8ea25590-1cb0-418a-8912-f448496031b2)

<h2></h2>

<br>

➡️ Now we'll **Enable Auditing from SQL Server**

From the Start menu > type **cmd** > right-click on **Command Prompt** and **Run as administrator**

  ![VM create](https://github.com/user-attachments/assets/a55c0a00-dc81-4835-9fc0-dcf11235e5c8)

Paste the following **statement** > press **"Enter"** > and you can see that the command was **successfully executed** ✔️

```auditpol /set /subcategory:"application generated" /success:enable /failure:enable```

<br>

  ![VM create](https://github.com/user-attachments/assets/cc8868c0-51e8-4b14-a426-4210ce78da95)

<h2></h2>

<br>

➡️ The next thing to do is **Open the SSMS** ➜ **Log Into It** ➜ and **Enable Auditing**.

  ![VM create](https://github.com/user-attachments/assets/362f6ee4-acfe-47fd-bc7d-24b94123fb84)

- To Connect to the SQL Server ➜  we can select **"SQL Server Authentication"** as the **Authetication** method.
- We'll use the **SQL Server system administrator credentials** we had set up earlier:

  - Username: ```sa```
  - Password: ```Cyberlab123!```

  ![VM create](https://github.com/user-attachments/assets/f8f0b5e9-446e-4cf3-9c38-5c306b2d0094)

Then we'll go to the **Properties** of the Server we just connected to > go to **"Login auditing"** > and check ◉ **Both failed and successful logins**

This way all the login attempts can be logged to the **Event Log**

  ![VM create](https://github.com/user-attachments/assets/80d51ce4-7e4c-4b12-a44c-7bad576327dc)

And finally we just have to **Restart** the Server ➜ right-click on the **windows-VM SQL Server** and click on **"Restart"**:

  ![VM create](https://github.com/user-attachments/assets/1b293515-03cb-4249-946b-cecfe017de5d)

  </details>

<h2></h2>

<details close> 
<summary> <h2>6️⃣ Test SQL Logging to make sure it is Working Properly</h2> </summary>
<br>

We'll now "attempt" to reconnect to the SQL Server **Intentionally Using a Wrong Password**:

  ![VM create](https://github.com/user-attachments/assets/bf302a15-8b56-486a-a355-b58bc15110ba)

  ![VM create](https://github.com/user-attachments/assets/85fbba3d-6c1b-4d9b-a806-114db7dcff9b)

Then close **SSMS** and open the **Event Viewer**.

Under the **Windows Logs** > **Application Log** ➜ ⚠️ this is where the **SQL Server Login Attempts** are going to be recorded.

You can see bellow the Event of the **Login failed** we intentionally generated using a Wrong Pasword:

  ![VM create](https://github.com/user-attachments/assets/0545a382-75c8-49d3-b0bb-4e42dc72ccaf)

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

  ![VM create](https://github.com/user-attachments/assets/1ae558bf-4af7-4140-85c0-7f137c39af38)

  ![VM create](https://github.com/user-attachments/assets/8e4a2035-4501-4f35-b4a0-7088302d8106)

- Then if you're running Windows ➜ open **Powershell**-
- But if you're running Mac ➜ open **Terminal**

We're going to attempt to ping our **Linux VM** > type in the **IP Address**:

  ![VM create](https://github.com/user-attachments/assets/28439036-1346-4ea0-b75e-77bb28b4dce6)

✅ We can confirm that we were able to successfully **Ping the Linux VM**:

  ![VM create](https://github.com/user-attachments/assets/cae7ee3d-74b6-41e4-8f2a-c41a920de014)

Now to SSH into the Virtual Machine, we have our ```linux-vm``` Username & Password:

- Username: ```labuser```
- Password: ```Cyberlab123!```

To connect into a machine with SSH we just type:

```commandline
ssh USERNAME@IPADDRESS
```
And then we press "Enter"

  ![VM create](https://github.com/user-attachments/assets/b4595e84-cf36-4adb-a3f8-17edd0736c1c)

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

  ![VM create](https://github.com/user-attachments/assets/ca6184c9-ec12-4ad5-a494-12bc6ff65eb3)

You'll see that your prompt changed to **labuser@linux-vm**

  ![VM create](https://github.com/user-attachments/assets/377c3cba-a2c8-4044-93a4-fc8f37ea74d8)

To confirm you´re logged in you can:

- Type ```uname -a``` and it will tell you what Operating System it is running: **Linux**
- And you can also type ```id``` and it will tell you **labuser**

  ![VM create](https://github.com/user-attachments/assets/4563a1fd-44fb-4e6d-a373-418e987cd33a)

✅ This is how you know you were **Successfully Logged into the Linux VM**.

<br>

>  <details close> 
> 
> **<summary> 💡 Note</summary>**
> 
> We're not going to be doing much with this Linux VM in this lab ➜ it's just another Endpoint for people to attack
> 
> Later we'll intall something in the Linux Machine that will let us forward the Logs into our Log Repository.
> 
> But for now I just wanted to make sure it is running ➜ and it is ✔️
> 
>  </details>

  </details>

<br>

<br>

<br>

# Conclusion

<br>

In this lab we:

- Disabled the **Internal Firewall on the Windows Computer**.

- Installed the **SQL Server Database into the Windows Computer**.

- Logged into the **Linux Computer with SSH**.

<br />

Now we will leave our Virtual Machines ON for the at least 24 hours

➡️ This will give bad actors, bots or attackers a chance to **Discover our VMs** and **Generate Logs**.

 
<br />

<br />

<br />  

<br /> 

<br />

<br />  

<br /> 
 
