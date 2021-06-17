# Blue Team: Summary of Operations

## Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further

### Network Topology

![Azure_Ntework_Topology](https://user-images.githubusercontent.com/74498617/122336908-8a7d7600-cf0b-11eb-89d3-f2b914edf9cd.png)

The following machines were identified on the network:

**Network**
- Address Range: 192.168.1.0/24
 - Netmask: 255.255.255.0
  - Gateway: 192.168.1.1

**Machines**

- IPv4: 192.168.1.1,  OS: Windows, Hostname: **ML-RefVM-684427**

- IPv4: 192.168.1.8, OS: Linux, Hostname: **Kali**

- IPv4: 192.168.1.110, OS: Linux, Hostname: **Target 1**

- IPv4: 192.168.1.115, OS: Linu, Hostname: **Target 2**

- IPv4: 192.168.1.105, OS: Linux, Hostname: **Capstone**

- IPv4: 192.168.1.100, OS: Linux, Hostname: **ELK**

### Description of Targets

The target of this attack was: Target 1 (IPv4: 192.168.1.110).
Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:

* [Excessive HTTP Errors](#excessive-http-errors)
* [HTTP Request Size Monitor](#http-request-size-monitor)
* [CPU Usage Monitor](#cpu-usage-monitor)



### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

#### Excessive HTTP Errors
Alert 1 is implemented as follows:
  - **Metric**: `http.response.status_code` > 400
  - **Threshold**: 5 in last 5 minutes
  - **Vulnerability Mitigated**: Brute force attack/Enumeration (with this alert, the SOC team can block the attacking IP or change the password being bruteforced.
  - **Reliability**: No, this alert does not generate a lot of false positives. This alert is highly reliable in identifying brute force attacks.
  - The 400 Bad Request Error is an HTTP response status code that indicates that the server was unable to process the request. This alert does not generate lots of false positives because it measures all HTTP errors (> 400) **every 5 minutes**.
![HTTP-Errors](https://user-images.githubusercontent.com/74498617/122436371-c560c780-cf66-11eb-8830-a4c21e245f55.png)

#### HTTP Request Size Monitor
Alert 2 is implemented as follows:
  - **Metric**: `http.request.bytes`
  - **Threshold**: 3500 in last 1 minute
  - **Vulnerability Mitigated**: By controlling the number of http request size through a filter it protects against DDOS attacks
  - **Reliability**: No, this alert doesn't generate a lot of false positives bc it is reliable.

![request-size](https://user-images.githubusercontent.com/74498617/122436471-d6a9d400-cf66-11eb-87c3-e05bbe878fec.png)

#### CPU Usage Monitor
Alert 3 is implemented as follows:
  - **Metric**: `system.process.cpu.total.pct`
  - **Threshold**: 0.5 in last 5 minutes
  - **Vulnerability Mitigated**: By controlling the CPU usuage percentage at 50%, it will trigger a memory dump of stored information is generated
  - **Reliability**: Yes this alert can generate a lot of false positives bc the cpu can spike even if there is not an attack.
![cpu-Usage](https://user-images.githubusercontent.com/74498617/122436580-f0e3b200-cf66-11eb-87fd-f2b226978767.png)

## Watcher History

![Watcher_History](https://user-images.githubusercontent.com/74498617/122436835-238daa80-cf67-11eb-9f29-3f35eec09b46.png)



### Suggestions for Going Further (Optional)
- Each alert above pertains to a specific vulnerability/exploit. Recall that alerts only detect malicious behavior, but do not stop it. For each vulnerability/exploit identified by the alerts above, suggest a patch. E.g., implementing a blocklist is an effective tactic against brute-force attacks. It is not necessary to explain _how_ to implement each patch.

The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats, identified by the alerts above. In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:
- Vulnerability 1- Excessive HTTP Errors
  - **Patch**: Require a stronger password policy in the user account settings. Update the account password policy in Windows group policy through /etc/security/pwquality.conf & through /etc/security/pwquality.conf in Linux
  -  **Why It Works**: By having a strong password it will be almost impossible to guess or brute force
  
- Vulnerability 2 - HTTP Request Size Monitor
  - **Patch**: Use advanced intrusion prevention and threat management systems, which combine firewalls, VPN, anti-spam, content filtering, load balancing, and other layers of DDoS defense techniques. Together they enable constant and consistent network protection to prevent a DDoS attack from happening. This includes everything from identifying possible traffic inconsistencies with the highest level of precision in blocking the attack
  - **Why It Works**: Given the complexity of DDoS attacks, there’s hardly a way to defend against them without appropriate systems to identify anomalies in traffic and provide instant response. Backed by secure infrastructure and a battle-plan, such systems can minimize the threat.
 
- Vulnerability 3 - CPU Usage Monitor
  - **Patch**: Use Host Instrusion Prevention System to identify DOS attack
  - **Why It Works**: This stops malware by monitoring the behavior of code
