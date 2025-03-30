# Security Remediation Plan

## Identified Security Vulnerabilities



## Guides

<details>
  <summary>Steps to Recreate Compromise</summary>
  
# Recreating the Compromise


## nmap scanning
Through a nmap scan with -Pn set we can find that the following ports are open
- 22  SSH 
- 53  
- 80  HTTP

![nmap](https://github.com/Miner221272/Writeups/blob/main/Easy/Marai/screenshots/Nmap_scan.png)

if we move to port 80 in our web browser we get to a blank page.

Going to robots.txt reveals nothing however navigating to /admin we get to the following page.

![webpage](https://github.com/Miner221272/Writeups/blob/main/Easy/Marai/screenshots/admin-webpage.png)

Through some further web searching we know this machine is a raspberry pi and has a default account.

![nmap](https://github.com/Miner221272/Writeups/blob/main/Easy/Marai/screenshots/OSINT_PASSWORD.png)

- pi:raspberry

Attempting to login with these credentials will give us out initial access

## Taking Control

Using the default credentials above we can log into the machine.

![nmap](https://github.com/Miner221272/Writeups/blob/main/Easy/Marai/screenshots/initial_access.png)

Looking into the desktop of the pi user we can find out first flag marked "user.flag". 
As we look deeper into the system at the file system mounts we find a usb mounted.

Looking at this directory we find a file with interesting contents.

![diving](https://github.com/Miner221272/Writeups/blob/main/Easy/Marai/screenshots/privesc_searching.png)

If we look at out current permisions with "sudo -L" we find we can run anything on this system.
Using sudo we can find the final flag using strings on the drive.

![strings](https://github.com/Miner221272/Writeups/blob/main/Easy/Marai/screenshots/Final_flag.png)

With this we've completed the CTF but can use "sudo bash" and fully compromise the system.

</details>

<details>
  <summary>SMB Guest Access Lockdown</summary>
  
  1. **Step 1:** Disable guest access to the SMB share by adjusting the appropriate access control settings on the server.
  2. **Step 2:** Review and confirm that only authorized users and groups have access to the share.
  3. **Step 3:** Ensure the system is configured to prevent guest access in the future by disabling guest accounts or limiting permissions.
  4. **Step 4:** Document the updated access control configuration and test to confirm restrictions are enforced.

</details>
