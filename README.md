SSH Brute-Force Detection Using Splunk  
A Splunk-based investigation and detection project focused on identifying SSH brute-force activity through log analysis, SPL queries, and visual dashboards.

This project was created by **Temitope Ilori** on **26th November 2025**.

---

Project Overview
This project demonstrates how Splunk can be used to analyse authentication logs and detect patterns consistent with SSH brute-force attacks.  
The objectives of this investigation were to:

- Parse Linux `large_auth.log` authentication logs  
- Identify failed and successful SSH login attempts  
- Extract attacking source IP addresses  
- Detect possible compromised accounts  
- Visualize attack trends over time  
- Build a SOC-style monitoring dashboard in Splunk Classic  

The repository includes screenshots, SPL queries, and a full investigative report.

---

Dashboards Created
The following dashboards were built in **Splunk Classic**:

### **1. Total Failed Password Attempts**
A single-value panel showing the total number of failed SSH login attempts detected.

### **2. Top Source IPs by Failed Attempts**
A table listing the IP addresses responsible for the highest number of failed login attempts.

### **3. Successful Logins After Failed Attempts**
A panel showing accepted logins, helping identify accounts that may have been successfully brute-forced.

### **4. Brute-Force Activity Over Time**
A timechart visualizing spikes in failed logins throughout the monitored period.

### **5. Most Targeted User Accounts**
A bar chart showing which usernames attackers attempted most frequently.

### **6. Distribution of Failed Attempts per Source IP**
A histogram displaying the distribution of failed attempts across attacking IP addresses.

All dashboard screenshots are included in this repository.

---

## 🧪 SPL Queries Used

### **Total Failed Attempts**
```spl
index=main "Failed password"
| stats count
```

### **Top Attacking Source IPs**
```spl
index=main "Failed password"
| rex "from\s(?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count by src_ip
| sort - count
```

### **Failed vs Successful Login Correlation**
```spl
index=main ("Failed password" OR "Accepted password")
| rex "from\s(?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count(eval(searchmatch("Failed password"))) as failed_attempts
       count(eval(searchmatch("Accepted password"))) as successful_logins
       by src_ip
| where successful_logins > 0
```

### **Targeted Usernames**
```spl
index=main "Failed password"
| rex "Failed password for (?<user>\w+)"
| stats count by user
| sort - count
```

### **Brute-Force Activity Over Time**
```spl
index=main "Failed password"
| timechart span=1m count
```

---

## Repository Contents
| File | Description |
|------|-------------|
| `SSH_Brute_Force_Report_Temitope_Ilori.docx` | Full investigation report |
| `count.png` | Total failed password attempts (visual) |
| `time.png` | Brute-force attempts timeline |
| `Brute-Force Activity Over Time.png` | Timechart visual |
| `Most-Targeted-User-Accounts.png` | Targeted username chart |
| `Distribution-Of-Failed-Attempts-Per-Source-IP.png` | Histogram visual |
| `README.md` | Documentation |

---

 Key Findings
- Multiple IP addresses generated repeated failed SSH login attempts.
- Attackers heavily targeted common accounts such as **admin**, **root**, **guest**, and **ubuntu**.
- Some IPs recorded successful logins after multiple failures, indicating potential account compromise.
- Attack patterns were consistent with automated brute-force attacks rather than manual attempts.

---

 Skills Demonstrated
- SIEM log analysis  
- SPL query development  
- Threat hunting & detection  
- Dashboard creation in Splunk Classic  
- Authentication event analysis  
- Report writing  
- SOC-style investigation  
- GitHub documentation & project management  

---

 Author
**Temitope Ilori**  
Cybersecurity Student | SOC & SIEM Enthusiast | Splunk Practitioner

---
