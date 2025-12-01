# 🛡️ IoC & Ownership Analyzer

![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)

**IoC & Ownership Analyzer** is a modular Python CLI tool designed for cybersecurity analysts, SOC operators, and Incident Responders. 

It automates the process of **identifying**, **normalizing**, and **enriching** Indicators of Compromise (IoCs). The tool handles obfuscated URLs, distinguishes between IPs and Domains, and performs deep ownership lookups while being aware of modern privacy protections (GDPR/WhoisGuard).

---

## Key Features

* **Modular Architecture:** Clean separation of concerns between logic, networking, and user interface for easy maintenance and scalability.
* **Smart Identification:** Automatically classifies inputs using robust Regex (IPv4, Domain, or URL) and parses hosts from complex URL structures.
* **Auto De-obfuscation:** Detects and decodes URL-encoded strings (e.g., `%20`, `%2F`) to reveal the true target before analysis.
* **Privacy Awareness:** explicitly detects and warns when WHOIS data is masked by **GDPR** or **Proxy Services** (e.g., "Redacted for Privacy").
* **Network Enrichment:** Performs DNS resolution and detailed WHOIS lookups (Registrar, Creation Date, Name Servers).

---

## Prerequisites

* **Python 3.x**
* **Internet connection (for external WHOIS/DNS queries)**
  
---

## Installation

* **Clone the repository:**
  ```text
  
  git clone [https://github.com/your-username/ioc-ownership-analyzer.git](https://github.com/your-username/ioc-ownership-analyzer.git)
cd ioc-ownership-analyzer

* **Install dependencies:** This tool relies on the python-whois library.
   ```text

  pip install python-whois

---

## Project Structure

The codebase is organized into three specific modules to ensure cleaner logic:

```text
root/
├── 📄 main.py            # CLI Entry Point: Handles user interaction and loop.
├── 📄 identify_ioc.py    # Logic Module: Regex definitions, parsing, and de-obfuscation.
├── 📄 whois_lookup.py    # Network Module: WHOIS queries, GDPR checks, and DNS resolution.
└── 📄 requirements.txt   # Dependencies.
