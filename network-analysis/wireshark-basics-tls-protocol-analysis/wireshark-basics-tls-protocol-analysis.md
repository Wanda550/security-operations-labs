# Wireshark Basics – TLS Protocol Analysis

## 🎯 Objective

Learn how to capture and analyze **TLS-encrypted traffic** using Wireshark.  
Understand TLS handshakes, certificate exchange, and encrypted application data for network monitoring and security analysis.

---

## 🖥️ Requirements

**Systems:**  

- Windows 10/11, Linux, or macOS  

**Tools:**  

- [Wireshark](https://www.wireshark.org/) (latest stable version)
- Web browser or TLS-enabled client for generating traffic

---

## 🔧 Step 1: Capture TLS Traffic

1. Open Wireshark.
2. Select your **network interface** (Wi-Fi or Ethernet).
3. Start the capture.
4. Open a web browser and navigate to a secure website (e.g., `https://example.com`).
5. Stop the capture after a few seconds.

---

## 🔧 Step 2: Filter TLS Traffic

Use the display filter:

```text
tls
