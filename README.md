add table of contents
# Cowrie
[Cowrie](https://github.com/cowrie/cowrie) is a honeypot, meaning it's a program that pretends to be a vulnerable SSH server in order to attract attackers/automated bots. It's great for learning about attacker behaviors or patterns because it logs every action and even captures anything they attempt to install onto your 'file system'.

# Safety
Keep in mind that Cowrie is attracting **real attacks.**

I highly recommend you only use a virtual private server to keep the attackers as far from your actual network and devices as possible.

Use [SSH keys](https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server), [fail2ban](https://github.com/fail2ban/fail2ban), and/or use SSH over [Tailscale](https://tailscale.com/) to reduce or eliminate the chances of attackers breaking into your real server.

### DO NOT RUN DOWNLOADED FILES ON YOUR REAL SYSTEM
**Downloaded files located in `var/lib/cowrie/downloads/` within your Cowrie folder are malware that attackers have attempted to download onto your system.**

Only upload these files to dedicated malware analysis services such as [VirusTotal](https://www.virustotal.com/gui/home/upload), [Tria.ge](https://tria.ge/) (account required, but it gives you the most information), or use the [Virustotal Integration](https://docs.cowrie.org/en/latest/virustotal/README.html) (keep in mind, if you auto deny outbound connections, you'll have to analyze the malware manually, because the integration will be blocked by the firewall.)

To safely analyze the malware, send it to malware analysis through APIs or download them onto a virtual machine and upload them via the web interfaces manually. 

Finally, read the server provider's terms of use or email support to make sure running honeypots such as Cowrie are permitted on their servers.

# Preparation

Prerequisates 
- A Linux Virtual Private Server OR an internet facing server you run yourself (not recommended!) running a supported distribution such as Ubuntu Server
- Root access to the server
- SSH Client, such as [Putty](https://putty.org/index.html) or your terminal
- SFTP Client, such as [WinSCP](https://winscp.net/eng/download.php) or [Filezilla](https://filezilla-project.org/)

# Setup
- SSH into your server
- Create an admin user account for yourself if it doesn't exist
- Update the system
- Enable the firewall, either locally (UFW or Iptables) or through the server provider's firewall (safer in case the server is somehow compromised, the attacker can't change the provider sided firewall)
- ADD FIREWALL RULES HERE
- INSERT STEPS TO CHANGE PORT
- [Follow these steps to install Cowrie](https://docs.cowrie.org/en/latest/INSTALL.html)
- It is highly recommended to follow step 7 to change Cowrie's port to 22

# After Setup
Cowrie should now be up and running. 
To test if it's working properly, you can attempt to SSH into your server using the port you assigned to Cowrie, using generic login information such as username 'root' and password 'password'

If it works properly, it should act as if you actually connected to a terminal. Type some test commands and then exit the session. You may then check the logs, and if you're going to analyze the logs or share them, remember to remove your own information from the logs.

If everything checks out, exit the session and leave Cowrie running to collect attempts passively over the next few days at least.

