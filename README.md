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

# Collecting logs
- Connect to your server via SFTP using your client of choice.
- SFTP uses the same port and user credentials as SSH.
- Once you have access to the file system, navigate to the Cowrie installation folder and head to (FILE LOCATION HERE)
- Cowrie generates two versions of each log file, each the same data but in different formats, .log (essentially a .txt file) and .json
- If you want to combine all the log files into one for easier analysis, use the command 'cat cowrie.lo* > whatever_you_want.log'
- If you need the .json version of the logs instead 'cat cowrie.lo* > whatever_you_want.jso*'
- You can then use your SFTP client to download the combined logs if desired.
- Or
- You may setup a supported log aggregator as detailed in the Cowrie documentation.

# Analyzing Logs
If you choose to not run a log aggregator like I did not, you have several options to view the log information.

# Manually using Notepad++
### View a list of username + password attempts
- Open your Cowrie.log in Notepad++
- Hit ctrl F or Search > Find in the top menu
- Paste `login attempt` in the search prompt
- Hit 'Find all in current document'
- This will create a list with every result from your log that you can scroll through and read at your leisure.
- Format: "[IP_HERE] login attempt [b'Attempted Username'/b'Attempted Password'] succeeded/failed"

### Count instances of username/password
- Open your Cowrie.json in Notepad++
- Hit ctrl F or Search > Find in the top menu
- Paste `"username":"YOUR_USERNAME"` in the search prompt, replacing YOUR_USERNAME with the username you want to count
- Hit `Count` in the search window

- To count occurances of a password
- do the same as above, but paste `"password":"YOUR_PASSWORD"` with the password you want, replacing YOUR_PASSWORD with the password you want to count
- Hit `Count` in the search window

### Viewing Attempted Commands
- Open your Cowrie.log in Notepad++
- Hit ctrl F or Search > Find in the top menu
- Paste `CMD` in the search prompt
- Hit 'Find all in current document'

Using Notepad++ is ideal if you just want to scroll through but it is very inefficient for collecting actual statistics.

# Using Cowrie IoC Analyzer
[Cowrie IoC Analyzer](https://github.com/rmcglamery/cowrie-ioc-analyzer) is a small Python program you can use to easily parse Cowrie logs locally.
- There are installation and use instructions on the repo. It's easiest to install it directly on your Cowrie server. 
- Make sure you have Pyton installed. `python --version`
- If it returns an error, install Python
- `sudo apt install python3`
- Note: when using the program, give it the full path to the Cowrie log file, not just the directory.
- Ensure you give it your combined .json log for the most accurate data.
- You'll likely want to use the option to export to a .txt file so you can read it in a text editor instead of a terminal.
- However, it does not give specific numbers in terms of attempts of specific usernames or passwords and it can become very long.

# Using jq
jq is a command line .json processor.
- It's easiest to run it on your Cowrie server.
- `sudo apt install jq`
- Once installed, navigate to your cowrie log directory and run these commands:
### jq usernames
- `jq -r 'select(.eventid == "cowrie.login.failed" or .eventid == "cowrie.login.success") | .username' COWRIE_JSON_NAME* | sort | uniq -c | sort -nr | head -n 10 > changeme.txt` will give you the 10 most commonly attempted usernames. Edit the '10' at the end to extend the list, or delete the entire head command to list everything.
- Make sure to replace 'cowrie_json_name' with your cowrie log file name, preferably the combined log file, and change `changeme.txt` to whatever filename you want to contain your results. These directions apply to all subsequent jq commands.
### jq passwords
- jq -r 'select(.eventid == "cowrie.login.failed" or .eventid == "cowrie.login.success") | .password' COWRIE_JSON_NAME* | sort | uniq -c | sort -nr | head -n 10
### jq commands
- jq -r 'select(.eventid == "cowrie.command.input") | .input' cowrie.json* | sort | uniq -c | sort -nr | head -n 10
### jq username/password combinations
- jq -r 'select(.eventid == "cowrie.login.failed" or .eventid == "cowrie.login.success") | "\(.username):\(.password)"' cowrie.json* | sort | uniq -c | sort -nr | head -n 10

# Note about using jq
These jq commands seem to successfully identity what appears the most, and usually the count is correct. However, verifying the results by manually searching with Notepad++ I've noticed some variance, like one jq result printing 285 hits versus Notepad++ reporting 309 matches.

I can't figure out what is causing the differences, for the most accurate data it is best to use jq and verify using Notepad++, or use another solution for counting results. 

If you have an idea for a fix please make a pull request.

# Using Longitudal Analysis
[Longitudal Analysis Cowrie](https://github.com/deroux/longitudinal-analysis-cowrie) is another Github program for analyzing Cowrie logs. 

I have not used it because I don't need the complex charts and data it provides, but it seems like another good option if you have a massive amount of log data, want more in depth charts and stats, or are running multiple Cowrie instances at once. 

If you use this it is not necessary to compile all of your .json files into one because it reads multiple.


