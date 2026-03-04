# HTTP Log Analysis Using Splunk

## 📘 Objective

In this lab, you will learn how to **ingest and analyze HTTP logs using Splunk SIEM** to identify errors, anomalies, and suspicious web activity.

By completing this project, you will be able to:

* Ingest JSON-formatted Zeek HTTP logs into Splunk
* Detect client-side and server-side HTTP errors
* Identify suspicious User-Agents and URI access attempts
* Detect large file transfers that may indicate data exfiltration

This lab reflects **real-world SOC analyst web traffic monitoring tasks**.

---

## 🧪 Lab Setup

* **Splunk:** Installed and accessible
* **Data Source:** JSON-formatted Zeek-style HTTP logs
* **Log File:** `http_logs.json`
* **Access:** Splunk Web (`http://localhost:8000`)

---

## 📂 Project Files

```
http-log-analysis/
│── http_logs.json   # Zeek-style HTTP log dataset
│── README.md        # Documentation
```

---

## ⚙️ Preparation Steps

### Step 1️⃣ Upload HTTP Logs to Splunk

1. Open **Splunk Web**
2. Navigate to:

   ```
   Settings → Add Data → Upload
   ```
3. Select the file:

   ```
   http_logs.json
   ```
4. Configure the upload:

   * **Source Type:** `json` or custom `zeek:http`
   * **Index:** `http_lab` (or `main`)
5. Complete the upload

### Step 2️⃣ Confirm Indexing

Verify data ingestion using:

```spl
index=http_lab | head 5
```

---

## 🔍 Lab Tasks & SPL Queries

### 🧩 Task 1 — Top 10 Endpoints Generating Web Traffic

Identify the most active source IPs generating HTTP requests.

```spl
index=http_lab sourcetype="json"
| stats count by "id.orig_h"
| sort -count
| head 10
```

**Purpose:**

* Identify high-traffic clients
* Detect abnormal traffic sources

---

### 🚨 Task 2 — Count Server Errors (HTTP 5xx)

Detect backend server failures.

```spl
index=http_lab sourcetype="json" status_code>=500 status_code<600
| stats count as server_errors
```

**Purpose:**

* Monitor server health
* Identify application instability or attacks

---

### 🤖 Task 3 — Detect Suspicious User-Agents

Identify possible scripted or automated attacks.

```spl
index=http_lab sourcetype="json" user_agent IN (
"sqlmap/1.5.1",
"curl/7.68.0",
"python-requests/2.25.1",
"botnet-checker/1.0"
)
| stats count by user_agent
```

**Purpose:**

* Detect scanners and automation tools
* Identify reconnaissance or exploitation attempts

---

### 📦 Task 4 — Detect Large File Transfers (> 500 KB)

Identify unusually large HTTP responses.

```spl
index=http_lab sourcetype="json" resp_body_len>500000
| table ts "id.orig_h" "id.resp_h" uri resp_body_len
| sort -resp_body_len
```

**Purpose:**

* Detect possible data exfiltration
* Monitor abnormal downloads

---

### ⚠️ Task 5 — Detect Suspicious URI Access Attempts

Identify access to sensitive or malicious paths.

```spl
index=http_lab sourcetype="json" uri IN ("/admin","/shell.php","/etc/passwd")
| stats count by uri, "id.orig_h"
```

**Purpose:**

* Detect web exploitation attempts
* Identify attackers probing sensitive endpoints

---

## 📊 Dashboards & Alerts (Optional)

This lab can be extended by creating:

* Dashboard panels for HTTP errors
* Alerts for large file transfers
* Alerts for suspicious User-Agents or URIs

---

## 🧠 Learning Outcomes

By completing this project, you have:

* Learned HTTP log ingestion in Splunk
* Performed web traffic security analysis
* Detected anomalies and attack indicators
* Practiced SPL for SOC investigations

---

## ⚠️ Disclaimer

This project is intended for **educational and authorized lab use only**. Do not analyze web traffic from systems without proper permission.

---

## 📄 License

This project is provided for **learning, academic, and portfolio purposes**.

---

**Project Title:** HTTP Log Analysis Using Splunk
**Category:** SIEM • SOC • Web Security
**Tool:** Splunk Enterprise
**Author:** Sudeep
