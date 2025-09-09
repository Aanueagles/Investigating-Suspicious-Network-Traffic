
# 🛡️ Investigating Suspicious Network Traffic

## 📌 Objective
Simulate suspicious network reconnaissance activity (ICMP ping + Nmap scan) and analyze the captured traffic with Wireshark, documenting findings as a Blue Team analyst.

---

## 🖥️ Lab Setup
- **Attacker:** Kali Linux (`192.168.1.4`)  
- **Target:** Ubuntu Server (`192.168.1.5`)  

**Tools Used:**  
- `nmap` (network scanner)  
- `ping` (ICMP requests)  
- `Wireshark` (packet analysis)  

---

## 🔧 Simulation Steps
From **Kali → Ubuntu**:

1. **ICMP Ping**
   ```bash
   ping -c 4 192.168.1.5


2. **Nmap SYN Scan (ports 1–1000)**

   ```bash
   nmap -sS -p 1-1000 192.168.1.5
   ```

3. Captured traffic with Wireshark and saved as
   👉 [capture.pcap](https://drive.google.com/file/d/1wst4Hf9dRMbz1uuhYlyG9tmzah53CxbV/view?usp=drive_link)

---

## 📊 Packet Analysis

### 1. ICMP Traffic

* Applied filter:

  ```wireshark
  icmp
  ```
* Observed Echo Request (from attacker) and Echo Reply (from victim).
* Confirms **host discovery attempt**.

![ICMP Traffic](https://drive.google.com/uc?export=view\&id=11N0nkux9J1zMbOJxC8NLh_uI0QWsAXQG)

---

### 2. SYN Scan Behavior

* Applied filter (SYN probes):

  ```wireshark
  tcp.flags.syn==1 && tcp.flags.ack==0
  ```

  → Shows attacker sending SYN probes.

* Applied filter (SYN/ACK responses):

  ```wireshark
  tcp.flags.syn==1 && tcp.flags.ack==1
  ```

  → Victim replies with **SYN/ACK on port 22 (SSH)** → indicates **open port**.

* Most other ports returned **no response (filtered)** or **RST (closed)**.

![Nmap Scan](https://drive.google.com/uc?export=view\&id=1Pq0Aan4sNyYY5Juz7DJjo4OEUhxbp3f0)

---

### 3. Port State Summary

* **22/tcp (SSH):** Open
* **Other ports (1–1000):** Filtered/Closed

![Port Summary](https://drive.google.com/uc?export=view\&id=1liYSGzY0vdfMjsr8MkHnpQc0HERm9ySV)

---

## 📑 Incident Report

**Incident Summary**
Suspicious reconnaissance activity detected from `192.168.1.4` targeting `192.168.1.5`. Activity included ICMP probing and TCP SYN scans.

**Indicators**

* ICMP Echo Requests
* TCP SYN scan (1–1000)
* Open port discovered: SSH (22/tcp)

**Impact**

* Reconnaissance only. No exploitation observed.
* Potential risk if SSH is misconfigured or weak credentials are used.

**Recommended Actions**

* Review SSH access logs for brute-force attempts
* Restrict SSH access with firewall rules (allow only trusted IPs)
* Monitor network IDS/IPS for repeated scanning activity
* Maintain logs for compliance and further correlation

---

## 📝 Key Takeaway

Blue Team analysts must be able to identify reconnaissance patterns like **ICMP sweeps** and **SYN scans**. Detecting them early helps prevent later exploitation.

```


