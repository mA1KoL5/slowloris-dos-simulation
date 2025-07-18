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

![Screenshot 1A]("screenshots/Screenshot 1A.png")  
We ensure the Host-Only network adapter exists and uses a 192.168.56.1/24 prefix.

---

### Screenshot 1B: Adapter Setup on Ubuntu VM

![Screenshot 1B]("screenshots/Screenshot 1B.png")  
The attacker Ubuntu VM is configured to use the Host-Only Adapter.

---

### Screenshot 1C: Adapter Setup on Victim VM

![Screenshot 1C]("screenshots/Screenshot 1C.png")  
The victim Windows VM is also set to use the Host-Only Adapter, completing isolated communication.

---

### Screenshot 2: Confirming Apache Web Page

![Screenshot 2]("screenshots/Screenshot 2.png")  
We visit `localhost` in a browser to confirm Apache is serving the default dashboard.

---

### Screenshot 3: Checking Victim VM IP Address

![Screenshot 3]("screenshots/Screenshot 3.png")  
Using `ipconfig`, we get the IP address of the victim VM (e.g. `192.168.56.110`).  
This gives us the target IP for the Slowloris script.

---

### Screenshot 4: Running Slowloris Script

![Screenshot 4]("screenshots/Screenshot 4.png")  
On the Ubuntu attacker VM, we run:

```bash
python3 slowloris.py -s 200 -timeout 15 -ua random -v 192.168.56.110
```

Connections begin flooding the server with incomplete HTTP headers.  
Each socket remains open, sending partial requests to keep the connection alive, gradually exhausting Apache’s available resources.

---

### Screenshot 5: Apache Still Responsive

![Screenshot 5]("screenshots/Screenshot 5.png")  
Apache is still handling requests at this early stage, though responses may be slower.

---

### Screenshot 6: Increasing Socket Count

![Screenshot 6]("screenshots/Screenshot 6.png")  
Socket count climbs into the hundreds.  
Apache begins to show signs of stress from incomplete requests.

---

### Screenshot 7: Ping to Victim

![Screenshot 7]("screenshots/Screenshot 7.png")  
The attacker confirms connectivity to the victim via `ping`.

---

### Screenshot 8: XAMPP Control Panel

![Screenshot 8]("screenshots/Screenshot 8.png")  
Shows Apache service running in XAMPP and confirms VM has proper IP config.

---

### Screenshot 9: Apache Service Crashing

![Screenshot 9]("screenshots/Screenshot 9.png")  
Eventually, the server stops responding in the browser (blank page), while the attacker script continues firing sockets.

---

### Screenshot 10: Final Slowloris Output

![Screenshot 10]("screenshots/Screenshot 10.png")  
The attack concludes with `Errno 111: Connection refused`, indicating Apache has stopped accepting connections.
