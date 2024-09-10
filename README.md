# Vulnerability Management
  * Vulnerability management project within a homelab
  * Simulated attacks on exposed hosts and followed protocol to deal with the attacks.

<h1 align='center'>Overview</h1>
Using a homelab made in VirtualBox, I've set up a vulnerability scanner, Nessus, and an EDR solution, LimaCharlie, which also operates as a SIEM. 
Using a KALI VM(Virtual Machine), I attacked my exposed Windows VM with several payloads, generating activity which can be logged and analyzed using the previously mentioned software.
Post attack, I made new alerts to detect for future attacks.

## <h1 align='center'><strong>MS2 and Nessus</strong></h1>
 * KALI : Main host used to scan vulnerabilities on metasploitable vm and windows vm
 * Windows : Vulnerable windows host
 * MS2: Metasploitable host

<p align='center'><strong>KALI VM setup</strong></p>
Installed Nessus to scan for hosts on internal network
<br>All hosts are in a shared internal network to prevent payloads from leaking into main network.<br>
Made a new account with Tennable port during installation for Nessus<br>
Import plugins and system updates to access new scans.<br>
Configured scans to scan for MS2 host and Windows host.<br>

<p align='center'><strong>Exploiting MS2</strong></p>
Used netdiscover to find MS2 host(10.0.2.7) i.e netdiscover -r [subnet range] <br>
<img src='https://imgur.com/uILufgP.png'
 width='80%'
 height='40%'
 alt='netdiscover results for MS2'
 >
Scanning using Nessus revealed 30 open ports on MS2, verified this by using nmap to find all open ports.<br>
<img src='https://imgur.com/TXKViCb.png'
 width='80%'
 height='80%'
 alt='Nessus scan results'
 >
Used hydra to access MS2 via FTP port and used the Metasploit console to set up a remote host and exploit the MS2 host<br>
<img src='https://imgur.com/70yZgj5.png'
 width='80%'
 height='40%'
 alt='hydra output'
 >
Post attack, closed vulnerable ports and updated rules on nessus to check for outbound connections.<br>

<h1 align='center'>Windows and LimaCharlie</h1>
<p>Vulnerable Windows VM<br>
 -Turned off Windows Defender and related security software to prepare the host for attacks.</p>
<p>LimaCharlie <br>
 -Installed the agent on the Windows host to link it to the LimaCharlie cloud and use its telemetry.</p>

<h1>KALI VM attack vector</h1>
<p>On the KALI VM, I installed a Sliver server and created payloads to exploit the Windows VM. Opened a temporary python webserver on KALI to allow the Windows VM to download  and start payload.<br>
On LimaCharlie, I was able to find the payload being downloaded and see its source i.p as well as checking the hash of my created payload against a virus database.</p>
<p>Back in the KALI VM, the Sliver server was set to listen to port 80 where the payload was downloaded. After the payload was installed, I started a session in the Windows VM via the Sliver server, allowing me to write commands such as whoami and getprivs. </p>

<h1 align='center'>Lessons learned</h1>
<p>Foreign files suspected of being malicious with no data found in virus databases should not be ignored. When comparing my new payload's hash with the virus database, there was no data found on this payload since it was brand new. New malicious files and codes with no related data are difficult to protect against with basic EDR configurations, balancing protection and convenience in small or personal hosts is easier compared to the enterprise level.</p>
