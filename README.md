# Blue Team: Summary of Operations

## Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further

### Network Topology

![Azure_Ntework_Topology](https://user-images.githubusercontent.com/74498617/122696053-1ba46380-d210-11eb-94a4-f219d562f5a3.png)

The following machines were identified on the network:

**Network**
- Address Range: 192.168.1.0/24
 - Netmask: 255.255.255.0
  - Gateway: 192.168.1.1
--------------


**Machines**

- IPv4: 192.168.1.1,  OS: Windows, Hostname: **ML-RefVM-684427**

- IPv4: 192.168.1.8, OS: Linux, Hostname: **Kali**

- IPv4: 192.168.1.110, OS: Linux, Hostname: **Target 1**

- IPv4: 192.168.1.115, OS: Linu, Hostname: **Target 2** (Not included in the attack).

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
## Watcher History
![Watcher_History](https://user-images.githubusercontent.com/74498617/122436835-238daa80-cf67-11eb-9f29-3f35eec09b46.png)

#### Excessive HTTP Errors
Alert 1 is implemented as follows:
  - **Metric**: `http.response.status_code` > 400
  - **Threshold**: 5 in last 5 minutes
  - **Vulnerability Mitigated**: Brute force attack/Enumeration (with this alert, the SOC team can block the attacking IP or change the password being bruteforced.
  - **Reliability**: The 400 Bad Request Error is an HTTP response status code that indicates that the server was unable to process the request. 
                     This alert is highly unlikely to generate lots of false positives because it measures all HTTP errors (> 400) **every 5 minutes**.
 

### Before the Attack
![HTTP-Errors](https://user-images.githubusercontent.com/74498617/122436371-c560c780-cf66-11eb-8830-a4c21e245f55.png)

 
### During the Attack
![Excessive-Http-errors](https://user-images.githubusercontent.com/74498617/122979756-edd13300-d365-11eb-98c8-342b3309780e.png)

#### HTTP Request Size Monitor
Alert 2 is implemented as follows:
  - **Metric**: `http.request.bytes`
  - **Threshold**: 3500 in last 1 minute
  - **Vulnerability Mitigated**: http.request.bytes measures the total size in bytes of the request (body and headers). Setting a threshold “above 3500 in 1 minute” for this metric, could prevent DDos attacks and/or Cross-Site Scripting (XSS) attacks.

  - **Reliability**: False positives could be generated by this alert in case of a large amount of HTTP requests are legitimate. Thus, this alert can be rated as medium reliability.

### Before the Attack
![request-size](https://user-images.githubusercontent.com/74498617/122436471-d6a9d400-cf66-11eb-87c3-e05bbe878fec.png)

### During the Attack

![http-request-monitor](https://user-images.githubusercontent.com/74498617/122983094-9d5bd480-d369-11eb-9c6b-7e64bff76bca.png)

#### CPU Usage Monitor
Alert 3 is implemented as follows:
  - **Metric**: `system.process.cpu.total.pct`
  - **Threshold**: 0.5 in last 5 minutes
  - **Vulnerability Mitigated**: :  A sign that may indicate your computer has become infected with malicious software  such as adware, spyware, trojans or some other viruses.

  - **Reliability**:  False positives can be generated by this alert such as:
“MRT.exe is short for Malicious Removal Tool, which is a legitimate Windows program. When this program runs, it will spike CPU and Memory Usage due to the resources being consumed by it which it needs to perform its functions.”
https://appuals.com/fix-high-cpu-and-memory-usage-by-mrt-exe/
However, this alert is still **highly reliable** because knowing that “CPU usage is above 50% in the last 5 minutes)” helps check/troubleshoot what caused this high CPU usage.
### Before the Attack
![cpu-Usage](https://user-images.githubusercontent.com/74498617/122436580-f0e3b200-cf66-11eb-87fd-f2b226978767.png)

### During the Attack
![CPU-Usage-Monitor](https://user-images.githubusercontent.com/74498617/122983238-c2e8de00-d369-11eb-8e49-aa583272e63f.png)

  ### Suggestions for Going Further
**Suggest a patch for each vulnerability identified by the alerts above.** Remember: alerts only detect malicious behavior. They do not prevent it. It is not necessary to explain how to implement each patch.

The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats. In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:

**Excessive HTTP Errors**
- Patch: Harden they wordpress by:
    - Implementing regular updates to WordPress by installing the latest versions of PHP and Plugins
    - Installing security plugin(s) 
  
Suggestions on how to implement this patch are available in https://wordpress.org/plugins/clearfy/

- Why It Works: 
    - Regular updates implement patches and fix vulnerabilities.
**HTTP Request Size Monitor**

- Patch: 

     - Limit the number of HTTP requests within a specified time.
     - Block HTTP requests from suspicious ips by configuring web server settings
     
Suggestions on how to implement this patch are available in 
https://clouddocs.f5.com/api/irules/Limit-the-number-of-HTTP-requests-by-a-client-within-a-specified-time.html

- Why It Works: 
  
     - Blocking/Removing public access to WordPress reduces the number of attacks		
     - It helps  reject the HTTP requests that are too large.
  
**CPU Usage Monitor**
- Patch:  

  - Use strong antivirus software
  - Implement advanced intrusion Detection/prevention and threat management system.
       
- Why It Works: 
     - Strong Antivirus should be able to detect/remove malicious malware which are one of the main causes of High CPU usage.  
      
      
    
