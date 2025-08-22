# Slowloris DoS Simulation (Offline VM Lab)

This project simulates a Slowloris Denial-of-Service attack on a locally hosted Apache server using XAMPP. The goal was to safely understand how low-and-slow attacks work in a closed lab environment.

---

## Lab Setup

- **Attacker**: Ubuntu 22.04 VM  
- **Victim**: Windows 10 VM with XAMPP installed  
- **Network**: VirtualBox Host-Only Adapter (no internet)  
- **Attack Tool**: Modified `slowloris.py` (Python3)

---

## Step-by-Step Walkthrough

### Screenshot 1A: VirtualBox Host-Only Network

![Screenshot 1A](screenshots/Screenshot%201A.png)  
We begin by creating a Host-Only network adapter in VirtualBox. This ensures both VMs can communicate directly with each other without accessing the internet. It provides a safe and contained environment for testing attacks without external risk.

---

### Screenshot 1B: Adapter Setup on Ubuntu VM

![Screenshot 1B](screenshots/Screenshot%201B.png)  
The attacker (Ubuntu) VM is configured to use the Host-Only network. This step guarantees that the attack traffic stays within the isolated network and doesn’t leak to the outside world or affect other devices.
---

### Screenshot 1C: Adapter Setup on Victim VM

![Screenshot 1C](screenshots/Screenshot%201C.png)  
We mirror the adapter setup on the victim (Windows) VM. Both machines now sit on the same subnet (192.168.56.x) and are fully isolated. This allows us to simulate real attack conditions in a safe virtual space.

---

### Screenshot 2: Checking Attacker VM IP Address

![Screenshot 2](screenshots/Screenshot%202.png)  
On the Ubuntu attacker VM, we run ip a to inspect the network interfaces. This reveals that the system has been assigned an IP address (192.168.56.101/24) on the Host-Only network. This confirms that the attacker VM is correctly connected and reachable by the victim VM within the isolated network.
---

### Screenshot 3: Checking Victim VM IP Address

![Screenshot 3](screenshots/Screenshot%203.png)  
We use the `ipconfig` command to identify the local IP address of the Windows VM (victim). This IP (e.g., `192.168.56.110`) will be used by the attacker VM to target the Apache server running on the victim. 
This gives us the target IP for the Slowloris script.

---

### Screenshot 4: Testing Victim VM Connectivity

![Screenshot 4](screenshots/Screenshot%204.png)  
Before launching any attacks, we verify that the attacker VM can communicate with the victim VM. This is done by pinging the victim's IP (192.168.56.110). The terminal shows successful ICMP replies with consistent latency, confirming reliable communication between the machines over the Host-Only network.

---

### Screenshot 5: Apache Server Running on Victim VM

![Screenshot 5](screenshots/Screenshot%205.png)  
This screenshot confirms that the Apache server is running successfully on the victim Windows 10 VM using XAMPP.
You can see that the Apache service is marked as “Running” in the XAMPP Control Panel. Additionally, the command prompt displays the victim's IP address (192.168.56.110) using the ipconfig command.
This IP will be used by the attacker VM to target the Apache server during the simulated DoS attack.
---

### Screenshot 6: Launching the Slowloris Attack

![Screenshot 6](screenshots/Screenshot%206.png)  
In this step, the attack is initiated from the Ubuntu attacker VM using a modified version of the slowloris.py script.
The command being executed is:

python3 slowloris.py 192.168.56.110 -p 8080

This command targets the victim machine at 192.168.56.110 on port 8080, where the Apache web server is running.
Once launched, this script will attempt to flood the server with partial HTTP requests, holding connections open and gradually exhausting available sockets.

---

### Screenshot 7: Screenshot 7: Slowloris Attack in Progress

![Screenshot 7](screenshots/Screenshot%207.png)  
This screenshot shows the attack script actively running. The terminal output logs the current actions being taken by the script:

It is creating sockets and sending partial HTTP headers.

The “Socket count: 0” indicates sockets are being opened and managed as intended.

The attack is configured to maintain 150 open connections to overwhelm the victim server.

This live log confirms that the attack is underway and helps monitor its behavior over time.

---

### Screenshot 8: Apache Still Responding Mid-Attack

![Screenshot 8](screenshots/Screenshot%208.png)  
In this moment, the Slowloris script continues to open and maintain hundreds of socket connections while sending partial headers. Despite the ongoing attack, the Apache server is still responding normally.

This is the early phase of the attack — the browser on the victim VM can still successfully load the XAMPP dashboard at localhost. This validates that the attack hasn't fully consumed all available connections yet, but stress is clearly building up in the background.

---

### Screenshot 9: Apache Service Becomes Unresponsive

![Screenshot 9](screenshots/Screenshot%209.png)  
At this stage of the Slowloris attack, the number of open sockets has increased significantly — hitting 662 in this example. As the script attempts to open more connections (338 new sockets), the server starts refusing them with [Errno 111] Connection refused.

On the left, the browser on the victim machine tries to access localhost, but the page fails to load entirely. This indicates that Apache has stopped responding due to resource exhaustion. The Slowloris attack has successfully tied up all available connections, leading to a Denial-of-Service (DoS) state.

---

### Screenshot 10: Apache Recovers After Attack is Stopped

![Screenshot 10](screenshots/Screenshot%2010.png)  
Once the Slowloris attack is manually stopped on the Ubuntu attacker VM (as shown by the KeyboardInterrupt message), we refresh the Apache server on the Windows VM.

On the left, the browser successfully loads the XAMPP dashboard again, proving that Apache has recovered. The server can now accept new incoming connections since the previously held sockets have been released. This reinforces how Slowloris doesn’t crash the server—it just holds resources until they're freed.
