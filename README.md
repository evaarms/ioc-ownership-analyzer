# 🛡️ Advanced IoC Batch Analyzer

![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)

Modular CLI tool designed for **Cybersecurity Blue Teams** and **OSINT Analysts**.

This tool allows for the **batch processing** of Indicators of Compromise (IoCs). It moves away from legacy WHOIS parsing, utilizing the modern **RDAP protocol (JSON)** and **DNSPython** for accurate, structured data extraction. It automatically cleans obfuscated indicators and resolves **all** available IPv4 and IPv6 records.

---

## Key Features

* **Batch Queue System:** Input IoCs one by one or paste bulk lists (space-separated). The tool queues them and processes the entire batch upon the `exit` command.
* **Modern RDAP Lookup:** Uses HTTP/JSON queries (RDAP) instead of raw port 43 WHOIS, ensuring better parsing accuracy and fewer connection errors.
* **Full DNS Resolution:** Resolves and displays **ALL** available A (IPv4) and AAAA (IPv6) records for a domain, not just the first one.
* **Auto De-obfuscation:** Instantly sanitizes defensive formats:
    * `hxxp://` → `http://`
    * `1.1.1[.]1` → `1.1.1.1`
    * `example[.]com` → `example.com`
* **Split-Table Reporting:** Results are intelligently separated into two clear tables: **"Domains & URLs"** and **"IP Addresses"**.
  
---

## Installation

* **Python 3.x**
* **Internet connection (for external queries)**
* **Setup**

    * **Clone the repository:**

      git clone [https://github.com/your-username/ioc-batch-analyzer.git](https://github.com/your-username/ioc-batch-analyzer.git)
     cd ioc-batch-analyzer


    * **Install Dependencies:** This version relies on requests (for RDAP), dnspython (for DNS), and tabulate (for UI).
      
      pip install requests dnspython tabulate
            
---
## Project Structure

The project follows a clean, modular architecture:

```text
ioc-analyzer/
│
├── 📄 main.py            # CLI Entry Point: Manages the input queue and table rendering.
├── 📄 identify_ioc.py    # Logic Module: Regex validation, classification, and de-obfuscation.
├── 📄 whois_lookup.py    # Network Module: RDAP client and DNS resolver.

___

## Usage

```text

python main.py

* **Workflow**

    * **Queue IoCs:** Paste any mix of IPs, Domains, or URLs.
    * **Start Analysis::** Type exit (or quit) to stop adding items and process the queue.
    * **View Results:** The tool prints two organized tables.
´´´

---

## Example Output

```text

Enter IoCs (space-separated for multiple entries).
Type 'exit' to start processing.

IoCs> google.com 1.1.1.1
   Total IoCs: 2

IoCs> hxxps://example[.]com
   Total IoCs: 3

IoCs> exit

==============================
      FINAL RESULTS
==============================

--- DOMAINS AND URLs ---
+-----------------------+---------------+---------+----------------+------------------+------------------+-------------------+
| Original              | De-obfuscated | Type    | A (IPv4)       | AAAA (IPv6)      | Registrar        | NS                |
+=======================+===============+=========+================+==================+==================+===================+
| google.com            | google.com    | Domain  | 142.250.200.46 | 2a00:1450:4003:: | MarkMonitor Inc. | ns1.google.com... |
|                       |               |         | 142.250.200.47 |                  |                  |                   |
+-----------------------+---------------+---------+----------------+------------------+------------------+-------------------+
| hxxps://example[.]com | example.com   | URL     | 93.184.216.34  | 2606:2800:220... | RESERVED-Internet| a.iana-servers... |
+-----------------------+---------------+---------+----------------+------------------+------------------+-------------------+

--- IP ADDRESSES ---
+------------+---------------+--------+---------------------+------------+---------+-----------------+
| Original   | De-obfuscated | Type   | Range               | Org        | Country | PTR (Reverse)   |
+============+===============+========+=====================+============+=========+=================+
| 1.1.1.1    | 1.1.1.1       | IP     | 1.1.1.0 - 1.1.1.255 | Cloudflare | AU      | one.one.one.one |
+------------+---------------+--------+---------------------+------------+---------+-----------------+
       

