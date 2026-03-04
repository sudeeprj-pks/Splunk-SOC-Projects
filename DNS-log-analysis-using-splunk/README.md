# DNS Log Analysis Using Splunk (Zeek DNS Logs)

## 1. Objective

This lab demonstrates how to analyze DNS traffic using **Splunk Enterprise** by ingesting and querying **Zeek DNS logs**. The goal is to identify:

* Most frequently queried domain names
* Most active source IPs generating DNS traffic
* Distribution of DNS query types (A, AAAA, PTR, etc.)

This README also explains common issues faced during ingestion and how to fix them.

---

## 2. Environment

* **Operating System:** Ubuntu Linux
* **Splunk Version:** Splunk Enterprise 9.x
* **Log Type:** Zeek DNS logs (`dns.log`)
* **Index Used:** `dns_lab`
* **Source File:** `dns.log`

---

## 3. Data Ingestion Steps

1. Open **Splunk Web**
2. Navigate to:

   ```
   Settings → Add Data → Upload
   ```
3. Upload the file `dns.log`
4. Set the following options:

   * **Source type:** json (used initially)
   * **Index:** dns_lab
5. Complete the upload and confirm indexing using:

   ```spl
   index=dns_lab | head 5
   ```

---

## 4. Important Note (Why Fields Were Missing)

Although the sourcetype was set to `json`, the DNS logs are actually **Zeek tab-separated (TSV) logs**, not true JSON. Because of this:

* Splunk did **not automatically extract fields** such as domain name, source IP, or query type
* Fields like `query`, `id.orig_h`, and `qtype` were not available

To solve this, **manual field extraction using regex (`rex`)** was required.

---

## 5. Understanding the Raw Log Format

Example `_raw` DNS log entry:

```
1332013964.540000 CaNeBQ3WJP8ZW9d4 192.168.202.100 44133 192.168.207.4 53 udp 41507 safebrowsing.clients.google.com 1 C_INTERNET 28 AAAA 3 NXDOMAIN F F T
```

Key fields by position:

* **Source IP:** 3rd column
* **Domain Name:** 9th column
* **DNS Query Type:** Near the end (A, AAAA, PTR, CNAME)

---

## 6. Lab Tasks and Correct SPL Queries

### Task 1: Identify the Most Frequently Queried Domain Names

```spl
index=dns_lab source="dns.log"
| rex field=_raw "^\S+\s+\S+\s+\S+\s+\S+\s+\S+\s+\S+\s+\S+\s+\S+\s+(?<domain>\S+)"
| stats count by domain
| sort -count
```

**Explanation:**

* Extracts the domain name from the 9th column
* Counts how often each domain is queried

---

### Task 2: Find the Most Active Source IPs

```spl
index=dns_lab source="dns.log"
| rex field=_raw "^\S+\s+\S+\s+(?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count by src_ip
| sort -count
```

**Explanation:**

* Extracts the source IP address (client generating DNS queries)
* Shows which hosts generate the most DNS traffic

---

### Task 3: Breakdown of DNS Query Types

```spl
index=dns_lab source="dns.log"
| rex field=_raw "(?<qtype>A|AAAA|PTR|CNAME)"
| stats count by qtype
```

**Explanation:**

* Extracts DNS query types
* Provides visibility into IPv4, IPv6, reverse lookups, etc.

---

## 7. Verification Query (Optional)

To verify extracted fields visually:

```spl
index=dns_lab source="dns.log"
| rex field=_raw "^\S+\s+\S+\s+(?<src_ip>\d+\.\d+\.\d+\.\d+).*?\s+(?<domain>\S+)\s+\d+\s+\S+\s+\d+\s+(?<qtype>A|AAAA|PTR|CNAME)"
| table _time src_ip domain qtype
| head 10
```

---

## 8. Common Mistakes and Fixes

| Issue                                 | Cause                     | Fix                          |
| ------------------------------------- | ------------------------- | ---------------------------- |
| No results for `stats count by query` | Field does not exist      | Extract domain using `rex`   |
| No extracted fields                   | Logs not JSON             | Use regex extraction         |
| Wrong sourcetype                      | Zeek logs indexed as json | Re-index or extract manually |

---

**Tool:** Splunk Enterprise
**Use Case:** SOC / DNS Traffic Analysis
