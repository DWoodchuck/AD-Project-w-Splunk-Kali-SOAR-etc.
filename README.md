# AD-Project-w-Splunk-Kali-SOAR-etc.

Started by creating three virtual machines in Vultr.io. These machines were a Windows Server 2025 machine, a Windows client machine, and an Ubuntu machine for Splunk.

<img width="787" height="244" alt="Screenshot 2026-09-29 095852" src="https://github.com/user-attachments/assets/9e2274e7-f556-4d49-8e58-db55b7d8b576" />

I then created a firewall group that along with a am implicit deny rule, had accept rules for RDP and SSH for my specific public IP address. These rules would make it easier to gain access to the machines. I also added a VPC to the virtual machines which would allow them to ping each other as though they were on the same network.

<img width="637" height="343" alt="Screenshot 2026-09-30 090128" src="https://github.com/user-attachments/assets/25a3e2ea-5916-4c3a-8048-9c1815bb699e" />

After adding the firewall group and VPC to the VMs, I promoted my active directory domain to a domain controller and installed Sysmon services onto it. Sysmon will later be used to in the Splunk integration to find more alert types and event IDs. After promoting the AD to a DC, I added a new user by the name of John Smith to 'Active Directory Users and Computers'.

I then switched over to my windows client machine, signed in with admin credentials, and changed the DNS server to that of the DC. After configuring the DNS server, I was able to join the windows client to the AD domain. I then tested to see if the John Smith user account was able to log in on the windows client and it was. I also installed Sysmon on the client machine so that more data could be tracked with Splunk.
