add table of contents
# Cowrie
[Cowrie](https://github.com/cowrie/cowrie) is a honeypot, meaning it's a program that pretends to be a vulnerable SSH server in order to attract attackers/automated bots. It's great for learning about attacker behaviors or patterns because it logs every action and even captures anything they attempt to install onto your 'file system'.

# Safety
Keep in mind that Cowrie is attracting **real attacks.**

I highly recommend you only use a virtual private server to keep the attackers as far from your actual network and devices as possible.

Use [SSH keys](https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server), [fail2ban](https://github.com/fail2ban/fail2ban), and/or use SSH over [Tailscale](https://tailscale.com/) to reduce or eliminate the chances of attackers breaking into your real server.

### DO NOT RUN DOWNLOADED FILES ON YOUR REAL SYSTEM
**Downloaded files located in `var/lib/cowrie/downloads/` within your Cowrie folder are malware that attackers have attempted to download onto your system. Only upload these files to dedicated malware analysis services such as [VirusTotal](https://www.virustotal.com/gui/home/upload), [Tria.ge](https://tria.ge/) (account required, but it gives you the most information), or use the [Virustotal Integration](https://docs.cowrie.org/en/latest/virustotal/README.html) (keep in mind, if you auto deny outbound connections, you'll have to analyze the malware manually, because the integration will be blocked by the firewall.)

To safely analyze the malware, send it straight to malware analysis through APIs or download them onto a virtual machine and upload them via the web interfaces manually. 



# Setup
To use Cowrie you'll want to setup a Linux server. Ideally a virtual private server instead of one you run yourself. Keep in mind that Cowrie is attracting real attacks, so it's ideal to keep the server you're running Cowrie on away from your actual network and devices.

If you do use a VPS, make sure that you're allowed to run honeypots such as Cowrie on their servers. You can check their terms or use or even email support to find out. I use a rented Linode server, I emailed support and they said as long as I'm not doing the attacking I can run a honeypot.

Cowrie only passively collects data and does not scan the internet or retailiate towards attackers in any way, so you won't get in trouble using it on most providers, but it's best to check in case.

You can get away with using a budget server if all you're planning to do is run Cowrie, 1 or 2 GB of RAM and 1-2 CPU cores will suffice as long as you're running a server distribution of Linux on it.

I highly recommend changing the Cowrie port to 22 in order to capture the most traffic.
**Make sure that you switch your actual SSH port to something else before setting Cowrie to port 22.**
