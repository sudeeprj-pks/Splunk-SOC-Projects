# SSH Log Analysis Using Splunk

## 📘 Project Overview

This project demonstrates **SSH authentication log analysis using Splunk SIEM** to detect suspicious and malicious SSH activity.

The lab simulates **real-world SOC analyst tasks**, including log ingestion, search creation, visualization, dashboard building, and alert configuration.

The analysis focuses on identifying:

* Successful SSH logins
* Failed SSH login attempts
* Brute-force indicators (multiple failed authentications)
* Suspicious SSH connections without authentication

---

## 🎯 Learning Objectives

* Ingest and parse JSON logs in Splunk
* Perform SSH security analysis using SPL
* Detect brute-force and suspicious behavior
* Create dashboards and alerts
* Understand attacker vs legitimate SSH patterns

---

## 🧪 Lab Environment

* **Splunk Enterprise / Free** (Local Ubuntu VM)
* **Ubuntu Linux** machine
* **ssh_log.json** dataset
* Web browser access to Splunk (`http://localhost:8000`)

---

## 📂 Project Files

```
ssh-log-analysis/
│── ssh_log.json    # Raw SSH log dataset
│── README.md       # Documentation
```

---

## 🚀 Lab Setup

### Step 1️⃣ Log in to Splunk

Open your browser and navigate to:

```
http://localhost:8000
```

Login using your Splunk admin credentials.

---

### Step 2️⃣ Upload SSH Logs

Navigate to:

```
Apps → Search & Reporting → Add Data → Upload File
```

Upload:

* **File:** `ssh_log.json`

Set the following options:

* **Sourcetype:** `_json`
* **Index:** `ssh_logs1`

Click **Start Searching** once ingestion is complete.

---

## 🔎 Fields Verified After Ingestion

Splunk successfully extracts the following fields:

* `event_type`
* `auth_success`
* `auth_attempts`
* `id.orig_h` (Source IP)
* `id.resp_h` (Destination Host)

---

## 🧩 Task 1 — Validate Log Ingestion

### SPL Query

```spl
index=ssh_logs1
| stats count by event_type
```

### ✅ What This Confirms

* Logs are successfully ingested
* SSH event types are parsed correctly
* Data is ready for analysis

---

## 🚫 Task 2 — Analyze Failed Login Attempts

### SPL Query

```spl
index=ssh_logs1 event_type="Failed SSH Login"
| stats count by id.orig_h
```

### 🔍 Findings

* Identified IP addresses generating failed SSH attempts
* Highlighted suspicious source IPs

### 📊 Visualization

* **Bar Chart:** Failed SSH Login Attempts per Source IP

---

## 🔐 Task 3 — Detect Brute-Force Attempts

### SPL Query

```spl
index=ssh_logs1 event_type="Multiple Failed Authentication Attempts"
| stats count by id.orig_h, id.resp_h
```

### 🚨 Alert Configuration

**Trigger Condition:**

* More than **5 login attempts** from the same IP within **10 minutes**

**Steps Performed:**

* Saved the search
* Created a Splunk alert
* Scheduled search to run every 10 minutes
* Configured UI / Email notification

---

## ✅ Task 4 — Track Successful SSH Logins

### SPL Query

```spl
index=ssh_logs1 event_type="Successful SSH Login"
| stats count by id.orig_h, id.resp_h
```

### 🎯 Purpose

* Detect legitimate SSH access
* Compare successful logins with prior failed attempts
* Identify potential account compromise

### 📊 Dashboard Panel Created

* **Top Source IPs with Successful SSH Logins**

---

## ⚠️ Task 5 — Suspicious SSH Connections (No Authentication)

### SPL Query

```spl
index=ssh_logs1 event_type="Connection Without Authentication"
| stats count by id.orig_h
```

### ⏱️ Time-Based Behavior Analysis

```spl
index=ssh_logs1 event_type="Connection Without Authentication"
| timechart count by id.orig_h
```

### 🔍 Purpose

* Identify reconnaissance or scanning activity
* Detect abnormal SSH connection patterns

---

## 📊 Dashboards & Alerts

This project includes:

* Failed login activity dashboard
* Successful login tracking panel
* Brute-force detection alert
* Time-based SSH anomaly visualization

---

## 🧠 SOC Analyst Takeaways

* Early detection of brute-force attacks
* Visibility into SSH misuse
* Improved incident response readiness
* Practical SPL experience

---

## ⚠️ Disclaimer

This project is for **educational and authorized lab use only**. Do not analyze logs from systems you do not own or have permission to monitor.

---

## 📄 License

Provided for **learning, lab practice, and academic use**.

---

**Project Title:** SSH Log Analysis Using Splunk
**Category:** SIEM • SOC • Log Analysis
**Tool:** Splunk Enterprise
**Author:** Sudeep
