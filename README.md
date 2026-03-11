# 🔍 IOC Analyzer

A command-line tool for analyzing **Indicators of Compromise (IoCs)** — including IPs, domains, and URLs. It automatically de-obfuscates common threat-intel formats, classifies each indicator, and enriches them with DNS and RDAP/WHOIS data.

---

## Features

- **De-obfuscation** — handles `hxxp`, `[.]`, `[:]`, `dot`, and URL encoding automatically
- **IOC Classification** — detects and categorizes IPs (v4/v6), URLs, and Domains
- **DNS Resolution** — resolves A, AAAA, and PTR records
- **RDAP/WHOIS Enrichment** — retrieves registration data (registrar, creation/expiration dates, nameservers, ASN/org/country for IPs)
- **Formatted Output** — results displayed in clean, grid-style tables grouped by IOC type

---

## Project Structure

```
.
├── main.py            # Entry point — handles user input and displays results
├── identify_ioc.py    # IOC parsing, de-obfuscation, and classification logic
└── whois_lookup.py    # DNS resolution and RDAP query functions
```

---

## Installation

```bash
git clone https://github.com/your-username/ioc-analyzer.git
cd ioc-analyzer
pip install tabulate
```

> No other external dependencies required — uses Python's built-in `ipaddress`, `urllib`, and `re` modules.

---

## Usage

```bash
python main.py
```

Enter one or more IOCs (space-separated). Type `exit` when done to trigger analysis.

```
Enter IoCs (space-separated for multiple entries).
Type 'exit' to start processing.

IoCs> 8.8.8.8 hxxps://evil[.]example[.]com 192.168.1[.]1:8080
Total Iocs: 3

IoCs> exit
```

### Example Output

```
==============================
      FINAL RESULTS
==============================

--- DOMAINS AND URLs ---
+----------------------+---------------------------+------+----------+-------------+--------+------------+------------+-----------+----+
| Original             | De-obfuscated             | Type | A (IPv4) | AAAA (IPv6) | Handle | Creation   | Expiration | Registrar | NS |
+----------------------+---------------------------+------+----------+-------------+--------+------------+------------+-----------+----+
| hxxps://evil[.]ex... | https://evil.example.com  | URL  | 1.2.3.4  | N/A         | ...    | 2020-01-01 | 2026-01-01 | GoDaddy   | .. |
+----------------------+---------------------------+------+----------+-------------+--------+------------+------------+-----------+----+

--- IP ADDRESSES ---
+-------------+---------------+------+----------------+----------+---------+---------------+
| Original    | De-obfuscated | Type | Range          | Org      | Country | PTR (Reverse) |
+-------------+---------------+------+----------------+----------+---------+---------------+
| 8.8.8.8     | 8.8.8.8       | IP   | 8.8.8.0/24     | Google   | US      | dns.google    |
+-------------+---------------+------+----------------+----------+---------+---------------+
```

---

## Supported Obfuscation Formats

| Obfuscated        | Resolved              |
|-------------------|-----------------------|
| `hxxp://...`      | `http://...`          |
| `hxxps://...`     | `https://...`         |
| `evil[.]com`      | `evil.com`            |
| `host[:]8080`     | `host:8080`           |
| `evil dot com`    | `evil.com`            |
| `%2F`, `%3A` etc. | URL-decoded           |

---

## IOC Classification Logic

Classification follows a strict priority order to avoid ambiguity:

1. **IP** — validated as IPv4 or IPv6 (with optional port handling)
2. **URL** — must have `http`/`https` scheme and a valid host
3. **Domain** — strict label validation (no numeric TLDs, RFC-compliant labels)
4. **Unknown** — anything that doesn't match the above

---

## Requirements

- Python 3.8+
- [`tabulate`](https://pypi.org/project/tabulate/) — for table rendering

---

## License

MIT License. See `LICENSE` for details.

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
       

