# AD-Project-w-Splunk-Kali-SOAR-etc.

Started by creating three virtual machines in Vultr.io. These machines were a Windows Server 2025 machine, a Windows client machine, and an Ubuntu machine for Splunk.

<img width="787" height="244" alt="Screenshot 2026-09-29 095852" src="https://github.com/user-attachments/assets/9e2274e7-f556-4d49-8e58-db55b7d8b576" />

I then created a firewall group that along with a am implicit deny rule, had accept rules for RDP and SSH for my specific public IP address. These rules would make it easier to gain access to the machines. I also added a VPC to the virtual machines which would allow them to ping each other as though they were on the same network.

<img width="637" height="343" alt="Screenshot 2026-09-30 090128" src="https://github.com/user-attachments/assets/25a3e2ea-5916-4c3a-8048-9c1815bb699e" />

After adding the firewall group and VPC to the VMs, I promoted my active directory domain to a domain controller and installed Sysmon services onto it. Sysmon will later be used to in the Splunk integration to find more alert types and event IDs. After promoting the AD to a DC, I added a new user by the name of John Smith to 'Active Directory Users and Computers'.

I then switched over to my windows client machine, signed in with admin credentials, and changed the DNS server to that of the DC. After configuring the DNS server, I was able to join the windows client to the AD domain. I then tested to see if the John Smith user account was able to log in on the windows client and it was. I also installed Sysmon on the client machine so that more data could be tracked with Splunk. For my Sysmon download, I decided to use Swift-On-Security for the XML configuration file, which I got from here: https://github.com/SwiftOnSecurity/sysmon-config. After installing the configuration file, I applied it to my sysmon installation through powershell by using the sysmon -c C:\filepath command.

Finally, It was time to set up Splunk within the Ubuntu machine. I started by sshing into my Ubuntu VM and then installing the free trial of Splunk from the Splunk website using wget. I installed Splunk on Ubuntu, set up the username and password, and then allowed port 8000 on both my Vultr firewall group and within the Ubuntu machine itself. This would allow me to access the Splunk web interface from my browser using the public IP address of the Ubuntu machine. After signing into Splunk, it was time to make some configuration changes, I started by downloading the Microsoft Windows add-on, which would allow for greater logging for my AD domain. I had also created a new index for my AD domain and configured a new receiving port for the logging data.

After setting up the initial configuration changes in the Splunk browser, it was time to set up Splunk universal forwarder in my DC and client machine. To simplify the process, I downloaded universal forwarder on my host machine and pasted it into both windows machines via RDP. After setting up the Splunk receiver as my private Ubuntu address with the default port of 9997, i had to copy inputs.conf into the local folder under systems and modify it so that it could generate logs from windows security and sysmon. This involved opening an a notepad process as administrator and then opening the inputs.conf file we copied into the local folder earlier. After opening the inputs.conf file in notepad, scroll all the way down and add these entries to the file replacing splunk name with whatever you named yours.

[WinEventLog://Security]
index = Splunkname
disabled = false

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
index = Splunkname
sourcetype = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
renderXml = true
disabled = false

Do this process on on both the client and DC machines, this will allow telemetry from windows security events and sysmon events from both machines to be logged in Splunk. This allows for several important events to be logged such as account creation, account deletion, successful logins, login failure for windows security. For sysmon, this can log process creation, file creation, network connections, file creation time change, etc.
To view a full list of what can be monitored through windows security and sysmon events, I like using this website as it helps me determine what the most important event IDs to look for are: https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/default.aspx

