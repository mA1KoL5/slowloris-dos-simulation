# Slowloris DoS Simulation (Offline VM Lab)

This project simulates a Slowloris Denial-of-Service attack on a locally hosted Apache server using XAMPP. The goal was to safely understand how low-and-slow attacks affect resource allocation on vulnerable web servers. This was done entirely offline using VirtualBox and isolated VMs.

## Lab Setup

- Attacker: Ubuntu 22.04 VM  
- Victim: Windows 10 VM with XAMPP installed  
- Network: VirtualBox Host-Only Adapter (no internet)  
- Attack Tool: Modified slowloris.py (Python3)

## Step-by-Step Walkthrough

### Screenshot 1: XAMPP Installation and Launch  
![Screenshot 1](screenshots/Screenshot%201.png)  
We begin by installing and launching XAMPP on the Windows VM. Apache is started from the control panel.  
Apache server is now live and serving files on localhost.

### Screenshot 2: Confirming Apache Web Page  
![Screenshot 2](screenshots/Screenshot%202.png)  
We visit localhost in a browser to confirm Apache is serving the default dashboard.  
Apache is up and running on port 80.

### Screenshot 3: Checking Victim VM IP Address  
![Screenshot 3](screenshots/Screenshot%203.png)  
Using ipconfig, we get the IP address of the victim VM (e.g. 192.168.56.110).  
This gives us the IP to target with Slowloris.

### Screenshot 4: Running Slowloris Script  
![Screenshot 4](screenshots/Screenshot%204.png)  
On the Ubuntu attacker VM, we run the following:

```bash
python3 slowloris.py -s 200 -timeout 15 -ua random -v 192.168.56.110
```

Connections begin flooding the server with incomplete HTTP headers. Each socket remains open, sending partial requests to keep the connection alive, gradually exhausting Apache’s available resources for new incoming connections.

### Screenshot 5: Apache Still Responsive  
![Screenshot 5](screenshots/Screenshot%205.png)  
The Apache server still handles requests.  
Server is slightly slower, but still up — early stage of the attack.

### Screenshot 6: Increasing Socket Count  
![Screenshot 6](screenshots/Screenshot%206.png)  
Socket count increases to several hundred.  
Apache begins to show signs of stress from incomplete requests.

### Screenshot 7: Timeout Begins  
![Screenshot 7](screenshots/Screenshot%207.png)  
Refreshing in browser leads to timeout.  
Apache is now unable to serve new connections.

### Screenshot 8: Firefox Hang Confirmed  
![Screenshot 8](screenshots/Screenshot%208.png)  
Browser keeps spinning — no response from server.  
This confirms the attack is effective. Apache threads are exhausted.

### Screenshot 9: Attacker Maintains Pressure  
![Screenshot 9](screenshots/Screenshot%209.png)  
The Slowloris tool reports active sockets holding.  
Attack continues — no additional effort needed from attacker.

### Screenshot 10: Post-Attack Recovery  
![Screenshot 10](screenshots/Screenshot%2010.png)  
After stopping the script, the Apache dashboard loads again.  
Once pressure stops, server frees resources and recovers.

## Skills Used

- VirtualBox networking and isolation  
- Apache (XAMPP) configuration  
- Python scripting and terminal usage  
- HTTP protocol understanding  
- Network DoS mechanics and limitations  
- Linux command-line fluency

## Real-World Application

This lab mirrors a realistic low-bandwidth DoS threat. In professional environments, this kind of knowledge helps with:

- Defending against slow HTTP attacks  
- Tuning Apache config to handle unexpected behavior  
- Building IDS/IPS rules to detect resource exhaustion  
- Justifying use of rate-limiting and timeout modules such as mod_reqtimeout

## Disclaimer

This attack was performed 100 percent offline in a controlled, virtual environment. Never launch any denial-of-service or security tests against real infrastructure without explicit written permission.

## Files Included

- slowloris.py — The DoS tool used  
- README.md — This documentation  
- screenshots/ folder — Contains:  
  - Screenshot 1.png through Screenshot 10.png

## References

- https://github.com/gkbrk/slowloris  
- https://www.apachefriends.org/index.html
