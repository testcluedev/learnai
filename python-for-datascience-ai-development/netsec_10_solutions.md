# Solutions — Networking & Security Hands‑On (10 Exercises)

These are reference solutions; adapt paths, credentials, and device specifics to your lab.

---

## 1) Cisco Syslog — Severities & Top Message IDs
```python
import re, json
from collections import Counter

def pri_to_severity(pri):
    return int(pri) % 8

sev_counter = Counter()
msgid_counter = Counter()

with open("cisco_syslog_sample.log", "r", encoding="utf-8") as f:
    for line in f:
        m_pri = re.match(r"<(\d+)>", line)
        if not m_pri:
            continue
        sev = pri_to_severity(m_pri.group(1))
        sev_counter[sev] += 1

        m_id = re.search(r"(%[A-Z]+-[0-7]-[A-Z_]+)", line)
        if m_id:
            msgid_counter[m_id.group(1)] += 1

print("Counts by severity:", dict(sev_counter))
print("Top 5 message IDs:", msgid_counter.most_common(5))
```

---

## 2) Parse `show ip interface brief` into CSV
```python
import pandas as pd

df = pd.read_fwf("show_ip_int_brief.txt")
df.to_csv("show_ip_int_brief.csv", index=False)
print(df)
```

---

## 3) Netmiko — Collect Facts
```python
from netmiko import ConnectHandler
from pathlib import Path

device = {
    "device_type": "cisco_ios",
    "host": "10.0.0.1",
    "username": "admin",
    "password": "adminpw",
    "fast_cli": True
}

commands = ["show version", "show ip interface brief"]
Path("outputs").mkdir(exist_ok=True)

try:
    with ConnectHandler(**device) as conn:
        for cmd in commands:
            out = conn.send_command(cmd)
            safe_cmd = cmd.replace(" ", "_")
            with open(f"outputs/{device['host']}_{safe_cmd}.txt", "w", encoding="utf-8") as f:
                f.write(out)
            print(f"Saved {cmd}")
except Exception as e:
    print("Connection failed:", e)
```

---

## 4) RESTCONF — Interface Counters
```python
import requests, urllib3, pandas as pd

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

host = "10.0.0.1"
url = f"https://{host}/restconf/data/ietf-interfaces:interfaces-state/interface"
headers = {
    "Accept": "application/yang-data+json"
}
auth = ("admin", "adminpw")

resp = requests.get(url, headers=headers, auth=auth, verify=False, timeout=20)
resp.raise_for_status()
data = resp.json()

rows = []
for iface in data.get("interface", []):
    name = iface.get("name")
    stats = iface.get("statistics", {})
    rows.append({
        "name": name,
        "in-octets": stats.get("in-octets"),
        "out-octets": stats.get("out-octets")
    })

df = pd.DataFrame(rows)
df.to_csv("restconf_interface_counters.csv", index=False)
print(df.head())
```

---

## 5) Azure NSG Flow Logs v2 — Parse & Top Talkers
```python
import json
import pandas as pd
from collections import Counter

with open("nsg_flowlogs_v2_sample.json", "r", encoding="utf-8") as f:
    doc = json.load(f)

rows = []
for rec in doc["records"]:
    for fset in rec["properties"]["flows"]:
        for fl in fset["flows"]:
            for tup in fl["flowTuples"]:
                ts, src, dst, srcp, dstp, proto, state, traffic, action = tup.split(",")
                rows.append({
                    "ts": int(ts),
                    "src": src,
                    "dst": dst,
                    "srcPort": int(srcp),
                    "dstPort": int(dstp),
                    "proto": proto,
                    "action": action  # A/D
                })

df = pd.DataFrame(rows)
top = df.groupby(["src","dst","dstPort"]).size().sort_values(ascending=False).head(5)
ad_counts = df["action"].value_counts()
print(top)
print("Allow/Deny counts:\n", ad_counts)
```

---

## 6) PCAP ➜ CSV ➜ Top Protocols
```python
import pandas as pd
from collections import Counter

df = pd.read_csv("packets.csv")
proto_counts = Counter()

for p in df["frame.protocols"].fillna(""):
    parts = p.split(":")
    if parts:
        proto_counts[parts[-1]] += 1

print(proto_counts.most_common(5))
```

---

## 7) Azure Log Analytics — Query with Python
```python
from azure.identity import DefaultAzureCredential
from azure.monitor.query import LogsQueryClient, LogsQueryStatus
import pandas as pd

credential = DefaultAzureCredential()
client = LogsQueryClient(credential)

workspace_id = "<YOUR-LAW-WORKSPACE-ID>"

kql = '''
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
| where TimeGenerated > ago(24h)
| summarize count() by DstIp_s
| top 10 by count_
'''

resp = client.query_workspace(workspace_id, kql, timespan=None)
if resp.status == LogsQueryStatus.SUCCESS:
    table = resp.tables[0]
    df = pd.DataFrame([dict(zip([c.name for c in table.columns], row)) for row in table.rows])
    print(df)
else:
    for err in resp.partial_error.error_details:
        print(err)
```

---

## 8) Time‑Series Anomaly — Rolling Z‑Score
```python
import json, pandas as pd, numpy as np

with open("nsg_flowlogs_v2_sample.json","r",encoding="utf-8") as f:
    doc = json.load(f)

timestamps = []
for rec in doc["records"]:
    for fset in rec["properties"]["flows"]:
        for fl in fset["flows"]:
            for tup in fl["flowTuples"]:
                ts = int(tup.split(",")[0])
                timestamps.append(ts)

s = pd.Series(1, index=pd.to_datetime(timestamps, unit="s", utc=True))
per_min = s.resample("1T").sum().fillna(0)

roll_mean = per_min.rolling(30, min_periods=5).mean()
roll_std = per_min.rolling(30, min_periods=5).std().replace(0, np.nan)
z = (per_min - roll_mean) / roll_std

anomalies = per_min[(z.abs() >= 3)]
print("Anomalous minutes:")
print(anomalies.dropna())
```

---

## 9) Palo Alto Traffic Log — Top Blocked Apps
```python
import pandas as pd

df = pd.read_csv("pan_traffic_sample.csv")
blocked = df[df["action"].str.lower() == "deny"]
print(blocked["app"].value_counts().head(5))
```

---

## 10) Linux Auth Log — Failed SSH Attempts by IP
```python
import re
from collections import Counter

ip_re = re.compile(r"(?:\d{1,3}\.){3}\d{1,3}")
counter = Counter()

with open("auth_sample.log","r",encoding="utf-8") as f:
    for line in f:
        if "Failed password" in line:
            m = ip_re.search(line)
            if m:
                counter[m.group(0)] += 1

print(counter.most_common(5))
```