# DUMBOLT - FTTH PON Outage & Telemetry Monitor

**DUMBOLT** is an asynchronous, single-session Telnet monitoring system for EPON/GPON Optical Line Terminals (OLTs). It continuously tracks OLT node reachability, active PON port outages, high-loss optical signals, and maintains a persistent ONU inventory using an interactive Streamlit web interface and background service engine.

---

## Key Features

* **Single Persistent Telnet Session per OLT:** Eliminates management interface lockout and session limit exhaustion by maintaining ONE persistent Telnet connection per OLT node with periodic CRLF keepalive pulses.
* **Simultaneous Multi-OLT Polling:** Asynchronously monitors multiple OLTs simultaneously using Python's `asyncio` event loop.
* **Persistent ONU Registry:** Tracks historical max ONU counts per port, preserving total registered subscriber counts across port flaps and temporary network drops.
* **Optical Signal Thresholding:** Automatically flags PON ports operating under severe optical loss states (configurable default threshold: `< -29 dBm`).
* **Superseding Outage Logic:** Suppresses individual port alarm fatigue when an entire OLT becomes unreachable, elevating the alarm status to an **OLT Down** event.
* **Dual Execution Modes:** Runs seamlessly as a real-time **Streamlit Web Dashboard** or as a headless **Daemon / CLI Background Collector**.

---

## System Architecture

here
                ┌─────────────────────────────────────────┐
                │            config.json                  │
                └────────────────────┬────────────────────┘
                                     │
                                     ▼
                  ┌─────────────────────────────────────────┐
                  │    DUMBOLT Engine (asyncio Loop)        │
                  └────┬───────────────────────────────┬────┘
                       │                               │
         Keepalives &  │                               │ Telnet Telemetry
        Status Checks  ▼                               ▼ Polling
┌──────────────────────────────┐              ┌──────────────────────────────┐
│     OLT Node 1 (C-DATA)      │              │      OLT Node 2 (ZTE)        │
└──────────────────────────────┘              └──────────────────────────────┘
                       │                               │
                       └───────────────┬───────────────┘
                                       │
                                       ▼
                  ┌─────────────────────────────────────────┐
                  │     JSON State & Persistence Storage    │
                  │  (down_ports, onu_registry, olt_state)  │
                  └────────────────────┬────────────────────┘
                                       │
                                       ▼
                  ┌─────────────────────────────────────────┐
                  │         Streamlit Web Dashboard         │
                  └─────────────────────────────────────────┘



---

## Directory Structure

```text
.
├── main.py                     # Main application script (Engine & UI)
├── config.json                 # OLT IP, credentials, & monitoring settings
├── down_ports.json             # Active port & OLT outage tracking (auto-generated)
├── onu_registry.json           # Persistent ONU inventory registry (auto-generated)
├── port_onu_counts.json        # Peak ONU counts observed per port (auto-generated)
├── olt_state.json              # Reachability state cache (auto-generated)
└── requirements.txt            # Python runtime dependencies



Prerequisites
Python 3.9+

Network connectivity (ICMP Ping + TCP Port 23 Telnet) from the monitor host to all target OLT management interfaces.

Required Python Libraries
Create a requirements.txt file with the following dependencies:


Prerequisites
Python 3.9+

Network connectivity (ICMP Ping + TCP Port 23 Telnet) from the monitor host to all target OLT management interfaces.

Required Python Libraries
Create a requirements.txt file with the following dependencies:
