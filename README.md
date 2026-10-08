========================================================================
                      SECURE CAFE: NETWORK GUARD
========================================================================

Overview
--------
Secure Cafe: Network Guard is a lightweight, passive network security 
monitoring console and incident response system tailored specifically for 
cafe owners and non-technical staff. It provides real-time visibility into 
connected subnet devices, automatically detects network anomalies (such as 
ARP IP connection conflicts and severe DNS Wi-Fi slowness), and provides 
clear, step-by-step mitigation checklists to clear threats without requiring 
advanced cybersecurity knowledge.

System Architecture & Components
--------------------------------
The system consists of three core Python scripts and a Jinja2 HTML web interface:

1. app.py (Flask Web Application)
   - Serves the operator web interface at http://localhost:5000.
   - Features Role-Based Access Control (RBAC): Admin vs. Staff operators.
   - Live Alert Banner with real-time state polling API (/api/threat-status).
   - Sequential incident resolution queue for managing multiple active threats.
   - Secret mobile trigger routes for live attack simulation (/simulate-attack and /simulate-dns-attack).
   - Device Inventory Management, Historical Threat Audit Logs, Settings, and User Management.

2. scanner.py (Passive Network Monitor Engine)
   - Runs continuously in the background to discover connected devices via multi-threaded ARP/ping sweeps.
   - Automatically adapts to Wi-Fi subnet changes mid-flight and prunes stale device entries.
   - Measures DNS latency and triggers "Severe Wi-Fi Slowness" alerts when latency exceeds 800ms.
   - Detects "Device Connection Conflict" (ARP spoofing / duplicate IP assignment) anomalies.
   - Dynamically checks system settings on every 1-second tick to adapt scan intervals and prune old logs.

3. setup_db.py (Database Initializer)
   - Configures the SQLite database (network_guard.db) with Write-Ahead Logging (WAL) mode for concurrent access.
   - Schema includes: `users`, `devices`, `threat_logs`, and `settings`.
   - Populates default operator accounts (`admin` / `staff`).

4. Web Templates (`templates/`)
   - `base.html`: Main layout with theme toggling (Light/Dark mode) and real-time state polling script.
   - `dashboard.html`: Live metrics cards, dynamic red alert banner, and step-by-step mitigation modal.
   - `inventory.html`: Track and search all scanned devices; edit authorization status and operator notes.
   - `threats.html`: Audit trail of active and historical security incidents with local system timestamps.
   - `settings.html`: Adjust background sweep frequency (5s–120s) and threat log retention days.
   - `users.html`: Manage staff credentials and access levels (Admin only).
   - `login.html` & `register.html`: Secure authentication interface.


Prerequisites & Requirements (Install if you didn't have it yet)
----------------------------
- Python 3.8 or higher
- Operating System: Windows, macOS, or Linux
- Python Packages: Flask, Werkzeug (built-in standard library modules: sqlite3, socket, subprocess, time, uuid, os, datetime)


Quick Start Guide
-----------------

1. Initialize the Database
   Run the setup script once to create `network_guard.db` and set up default accounts:
   
   $ python setup_db.py

   Default Login Credentials:
   - Administrator: Username = `admin` | Password = `admin123`
   - Operational Staff: Username = `staff` | Password = `staff123`

2. Launch the Flask Web Dashboard
   In your primary terminal, start the web server:
   
   $ python app.py

   Open your browser and navigate to: http://localhost:5000

3. Start the Passive Scanner Engine
   In a second terminal window, launch the network scanner:
   
   $ python scanner.py


Attack Simulation Guide (Live Demo)
-----------------------------------
To demonstrate incident detection and non-technical mitigation without risking live network downtime:

1. Connect your mobile phone or any device that connect to the same Wi-Fi to your laptop's Wi-Fi.
2. Open your phone's web browser and trigger either attack:

   - Test Attack 1: Severe Wi-Fi Slowness (DNS Latency)
     Navigate to: http://<YOUR_LAPTOP_IP>:5000/simulate-dns-attack

   - Test Attack 2: Device Connection Conflict (ARP)
     Navigate to: http://<YOUR_LAPTOP_IP>:5000/simulate-attack

3. Watch the laptop dashboard:
   - The Live Alert Banner instantly turns solid red with STATUS: [ALERT].
   - Click "VIEW SIMPLE FIX" to open the step-by-step mitigation checklist.
   - Click "CLEAR THIS INCIDENT" to resolve threats sequentially.


License & Disclaimer
--------------------
Developed for educational and operational demonstration in small business / cafe environments.
