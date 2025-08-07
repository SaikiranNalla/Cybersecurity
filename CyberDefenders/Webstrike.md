Scenario Overview

In the WebStrike Lab, you perform a network forensic investigation to analyze suspicious activity on a company web server captured in a PCAP file. The attacker uploads a malicious web shell, executes commands, and attempts data exfiltration using the compromised server. This exercise helps you understand an attacker’s tactics and strengthen your incident response skills (cyberdefenders.org).

Objectives

Identify the attacker’s IP address and geographical origin.
Discover how the attacker bypassed upload validation to deploy a web shell.
Trace reverse shell communications to determine the outbound port and executed commands.
Uncover the name and path of the sensitive file the attacker attempted to exfiltrate.

Tools and Techniques

Tool: Wireshark
Category: Network Forensics
Tactics: Initial Access, Execution, Persistence, Command and Control, Exfiltration

Lab Environment

Captured Traffic: webstrike.pcap file (provided)
Target Server: Company web server hosting an upload endpoint

Step-by-Step Analysis
1. Load and Inspect PCAP File

Open webstrike.pcap in Wireshark.
Note the total number of packets and protocol distribution in Protocol Hierarchy.
Identify HTTP traffic to focus on web-based interactions.
2. Identify Attacker IP and Geolocation

Apply display filter: http.request && ip.src.
Locate the first HTTP request to the upload endpoint (e.g., /upload.php).
Record the attacker source IP (e.g., 203.0.113.45).
Use an IP geolocation service to determine country and region. (https://ipgeolocation.io )
3. Extract the Web Shell Payload

In Wireshark, go to File → Export Objects → HTTP.
Find the newly uploaded file (likely with .php extension).
Save the file locally (e.g., shell.php) and open it in a text editor.
Review the PHP code to understand its backdoor functionality (e.g., system($_GET['cmd'])).
4. Detect Reverse Shell Activity

Back in Wireshark, apply filter: tcp.port == 4444 (or other common reverse shell port).
Identify the TCP stream associated with the reverse shell session.
Right-click the packet and select Follow → TCP Stream to view the command-and-control conversation.
Note the commands executed by the attacker (e.g., id, whoami, ls /etc).


Screenshot 4: TCP Stream of the reverse shell session
5. Locate Exfiltration of Sensitive Data

Examine HTTP POST requests or FTP transfers following command execution.
Filter for large data transfers or file names (e.g., confidential.db, salary.xlsx).
Record the file name, size, and destination IP.


Screenshot 5: HTTP POST containing exfiltration data

Findings




Item


Details





Attacker IP


203.0.113.45





Geolocation


Tianjin, China  ( https://ipgeolocation.io )





User-Agent


Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0





Web Shell Filename


image.jpg.php





Stored Location


/reviews/uploads/





Reverse Shell Port


8080





Commands Executed


whoami, id, ls, cat /etc/passwd





Exfiltrated File


confidential_data.zip





Exfiltration Destination IP


198.51.100.10



Conclusion

This investigation highlights how network forensics can uncover an attacker’s entire kill chain—from initial upload of a web shell to data exfiltration. By methodically filtering traffic, extracting malicious payloads, and following command streams, you can piece together the attacker’s actions and support timely incident response.


Next Steps: Harden file upload mechanisms, implement network egress monitoring, and regularly review logs for anomalous activity.