# Networking & Security Hands‑On — 10 Exercises (Self‑Contained)

Audience: You have Cisco devices to practice on and access to Azure VNet flow logs and other Azure logs.  
Goal: Practical, end‑to‑end tasks with **clear datasets or instructions** so you can start immediately.  
Format: Each exercise includes **task**, **dataset/URL or what to use**, and **hints**.

---

## 1) Parse Cisco Syslog — Severities & Top Message IDs
**Task:** Given a sample Cisco syslog file, compute:
- Count of messages per **severity** (0–7).
- Top 5 **message IDs** (`%FACILITY-SEVERITY-MNEMONIC:` style).

**Dataset:** Save the following as `cisco_syslog_sample.log`:
```
<189>Feb 10 10:21:30 R1 64614: %LINK-3-UPDOWN: Interface GigabitEthernet0/1, changed state to up
<190>Feb 10 10:21:35 R1 64615: %LINK-3-UPDOWN: Interface GigabitEthernet0/2, changed state to down
<191>Feb 10 10:22:01 R1 64616: %SYS-5-CONFIG_I: Configured from console by vty0 (10.0.0.15)
<189>Feb 10 10:23:30 R1 64620: %LINK-3-UPDOWN: Interface GigabitEthernet0/1, changed state to down
<190>Feb 10 10:23:35 R1 64621: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up
<189>Feb 10 10:24:30 R1 64622: %LINK-3-UPDOWN: Interface GigabitEthernet0/2, changed state to up
```

**Hints:**  
- Severity can be parsed from the numeric in the tag (`<PRI>`), where `severity = PRI % 8`.  
- Extract message ID (e.g., `%LINK-3-UPDOWN`) via regex: `r"%([A-Z]+-[0-7]-[A-Z_]+)"`.  
- Use `collections.Counter`.

---

## 2) Parse `show ip interface brief` into CSV
**Task:** Convert Cisco IOS output to a CSV with headers: `Interface, IP-Address, OK?, Method, Status, Protocol`.

**Dataset:** Save as `show_ip_int_brief.txt`:
```
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     192.168.1.1     YES manual up                    up
GigabitEthernet0/1     unassigned      YES unset  administratively down down
GigabitEthernet0/2     10.0.0.1        YES manual up                    up
```

**Hints:**  
- Split by whitespace, but `Status` can have spaces (e.g., "administratively down").  
- Use fixed columns via slicing or a smarter regex.  
- Pandas: `pd.read_fwf` (fixed‑width) is ideal.

---

## 3) SSH to Cisco with Netmiko — Collect Facts
**Task:** Using **Netmiko**, connect to a Cisco device and collect:
- `show version`
- `show ip interface brief`
- Save outputs to `outputs/<device>_<command>.txt`

**Dataset/Env:** Use your lab device (or a CSR/IOS‑XE VM).

**Hints:**  
- Install: `pip install netmiko`.  
- Device dict keys: `device_type`, `host`, `username`, `password`.  
- Use `ConnectHandler` and `send_command`.  
- Wrap in `try/except` and timeout handling.

---

## 4) RESTCONF to Pull Interface Counters (IOS‑XE)
**Task:** Query RESTCONF on your IOS‑XE device to get interface statistics; write a CSV with: `name, in-octets, out-octets`.

**Dataset/Env:** Your IOS‑XE device with RESTCONF enabled:
```
ip http secure-server
restconf
```
**Hints:**  
- Python: `requests.get("https://<host>/restconf/data/ietf-interfaces:interfaces", auth=..., headers={"Accept":"application/yang-data+json"}, verify=False)`  
- For counters: `ietf-interfaces:interfaces-state` (oper data) or `ietf-interfaces:interfaces` (config).  
- Use `urllib3.disable_warnings()` when `verify=False` (lab only).

---

## 5) Azure NSG (VNet) Flow Logs v2 — Parse & Top Talkers
**Task:** Parse sample **flow logs v2** JSON and compute:
- Top 5 `(srcIp, dstIp, dstPort)` by **flow count**.
- Count of **allow vs deny**.

**Dataset:** Save as `nsg_flowlogs_v2_sample.json`:
```json
{
  "records": [
    {
      "time": "2024-05-10T10:00:00Z",
      "systemId": "nsg-xyz",
      "category": "NetworkSecurityGroupFlowEvent",
      "properties": {
        "Version": 2,
        "flows": [
          {
            "rule": "DefaultRule_AllowInternetOutbound",
            "flows": [
              {"mac": "000D3A1B2C3D", "flowTuples": [
                "1715335200,10.0.0.4,8.8.8.8,53012,53,U,T,I,A",
                "1715335205,10.0.0.4,8.8.4.4,53013,53,U,T,I,A"
              ]}
            ]
          },
          {
            "rule": "UserRule_DenyRDPInbound",
            "flows": [
              {"mac": "000D3A1B2C3D", "flowTuples": [
                "1715335210,52.23.22.1,10.0.0.4,60001,3389,T,S,I,D",
                "1715335215,13.66.1.2,10.0.0.4,60002,3389,T,S,I,D"
              ]}
            ]
          }
        ]
      }
    }
  ]
}
```

**Hints:**  
- Each `flowTuple` is CSV: `ts,src,dst,srcPort,dstPort,proto,flowState,traffic,action(A/D)`.  
- Use `json` to load, loop through `records[].properties.flows[].flows[].flowTuples`.  
- Build a DataFrame and `groupby(["src","dst","dstPort"]).size()`.

---

## 6) PCAP ➜ CSV ➜ Analyze Protocols (tshark)
**Task:** Convert a PCAP into CSV and print top 5 protocols by packet count.

**Dataset/Env:** Use your own `capture.pcap`.  
**Hints:**  
- Command: `tshark -r capture.pcap -T fields -e frame.protocols -E header=y -E separator=, > packets.csv`  
- Then in Python, split `frame.protocols` by `:` and count last protocol.  
- Use `collections.Counter`.

---

## 7) Azure Log Analytics — Query with Python (KQL)
**Task:** Query Log Analytics for **AzureFirewallNetworkRule** logs in the last 24h and print top 10 destinations by count.

**Dataset/Env:** Your workspace.  
**Hints:**  
- Install: `pip install azure-monitor-query azure-identity`.  
- Use `DefaultAzureCredential()`; env vars/VS Code/sign‑in as appropriate.  
- KQL sample:
```
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
| where TimeGenerated > ago(24h)
| summarize count() by DstIp_s
| top 10 by count_
```

---

## 8) Time‑Series Anomaly: Flows per Minute (Rolling Z‑Score)
**Task:** From the NSG flow logs (exercise #5 dataset or your real logs), build a per‑minute flow count series and flag minutes where `|z| >= 3` (anomaly).

**Dataset:** Reuse `nsg_flowlogs_v2_sample.json` or your data.  
**Hints:**  
- Convert epoch seconds to `pd.to_datetime(..., unit="s", utc=True)`.  
- Resample to `1T` and fill missing minutes with 0.  
- Rolling mean/std with `window=30`; z‑score `(x - mean)/std`.

---

## 9) Parse Palo Alto Traffic Log (CSV) — Top Blocked Apps
**Task:** Given sample Palo Alto traffic log CSV, compute top 5 **blocked** applications.

**Dataset:** Save as `pan_traffic_sample.csv` (minimal illustrative subset):
```csv
receive_time,src,dst,app,action,bytes,rule
2024/05/10 10:00:01,10.0.0.4,8.8.8.8,dns,allow,1200,Allow-DNS
2024/05/10 10:00:05,52.23.22.1,10.0.0.4,rdp,deny,0,Block-RDP
2024/05/10 10:00:08,13.66.1.2,10.0.0.4,rdp,deny,0,Block-RDP
2024/05/10 10:00:10,10.0.0.4,142.250.74.78,ssl,allow,78000,Allow-Web
2024/05/10 10:00:15,203.0.113.9,10.0.0.4,ssh,deny,0,Block-SSH
```

**Hints:**  
- Read via `pd.read_csv`; filter `action == "deny"`; `value_counts()` on `app`.

---

## 10) Linux Auth Log — Failed SSH Attempts by IP
**Task:** Parse a Linux `/var/log/auth.log` sample and list top 5 source IPs with failed SSH logins.

**Dataset:** Save as `auth_sample.log`:
```
May 10 10:21:01 server sshd[1122]: Failed password for invalid user admin from 203.0.113.10 port 54012 ssh2
May 10 10:21:05 server sshd[1123]: Failed password for root from 198.51.100.7 port 54022 ssh2
May 10 10:21:09 server sshd[1124]: Failed password for root from 203.0.113.10 port 54032 ssh2
May 10 10:21:15 server sshd[1125]: Failed password for invalid user test from 203.0.113.11 port 54042 ssh2
May 10 10:21:18 server sshd[1126]: Failed password for root from 198.51.100.7 port 54052 ssh2
```

**Hints:**  
- Regex for IP: `r"(?:\d{1,3}\.){3}\d{1,3}"`.  
- Filter lines containing `"Failed password"`.  
- Use `Counter` to rank top source IPs.

---

### General Tips
- Create a virtual environment per project (`python -m venv .venv && source .venv/bin/activate`).
- Useful libs: `pandas`, `numpy`, `requests`, `beautifulsoup4`, `netmiko`, `matplotlib`, `azure-monitor-query`, `azure-identity`.