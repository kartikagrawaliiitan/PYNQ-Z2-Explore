# PYNQ-Z2-Explore
# PYNQ-Z2 — Lesson 1: Setup & Jupyter Access

This is **Lesson 1** of learning the PYNQ-Z2 platform.

The objective of this lesson is to understand the basic PYNQ-Z2 environment, establish communication with the board, configure Ethernet networking, and access Jupyter Notebook from a computer.

---

## 1. What is PYNQ?

**PYNQ (Python Productivity for Zynq)** is an open-source framework for working with AMD/Xilinx Zynq-based FPGA platforms using Python.

The PYNQ-Z2 contains two major parts:

```text
                    PYNQ-Z2
                       │
             ┌─────────┴─────────┐
             │                   │
            PS                  PL
    Processing System    Programmable Logic
             │                   │
        ARM Processor          FPGA
             │                   │
          Linux                HDL
          Python             Vivado
          Jupyter
```

### Processing System (PS)

The PS contains the ARM processor and runs:

* Linux
* Python
* Jupyter Notebook
* Other software applications

### Programmable Logic (PL)

The PL is the FPGA fabric used to implement digital hardware such as:

* Counters
* FSMs
* GPIO
* Digital signal processing
* Custom hardware circuits

The PS and PL can communicate using hardware interfaces such as **AXI**.

---

# 2. Basic PYNQ-Z2 Setup

The initial setup requires:

* PYNQ-Z2 board
* Computer
* USB-to-Serial connection
* Ethernet connection
* PuTTY or another serial terminal
* PYNQ Linux
* Jupyter Notebook / JupyterLab

---

# 3. Serial Communication

A serial connection provides access to the PYNQ Linux terminal.

### PuTTY Configuration

```text
Connection type : Serial
Baud rate       : 115200
```

The COM port depends on the computer and USB-Serial device.

After a successful connection, the PYNQ terminal appears as:

```bash
xilinx@pynq:~$
```

### Important

The COM port can change when the USB device is reconnected.

Check the available COM port in:

```text
Device Manager
→ Ports (COM & LPT)
```

Only one application should use the serial port at a time.

---

# 4. Checking the PYNQ Network

The Ethernet interface of the PYNQ-Z2 is:

```text
Interface : eth0
IP address: 192.168.2.99
Subnet    : 255.255.255.0
```

The network can be checked from the PYNQ terminal using:

```bash
ip addr
```

Look for the `eth0` interface and its IPv4 address.

---

# 5. Jupyter Environment

Jupyter can be checked from the PYNQ terminal:

```bash
jupyter --version
```

The setup used:

```text
Jupyter Notebook : 6.4.12
JupyterLab       : 3.4.4
```

Jupyter is used as the interface for running Python programs on the PYNQ-Z2.

---

# 6. Configuring Jupyter

A Jupyter configuration file can be created at:

```text
~/.jupyter/jupyter_notebook_config.py
```

The required settings are:

```python
c.NotebookApp.ip = '0.0.0.0'
c.NotebookApp.port = 8888
c.NotebookApp.allow_remote_access = True
c.NotebookApp.open_browser = False
```

Jupyter can then be started using:

```bash
jupyter notebook --no-browser --ip=0.0.0.0 --port=8888 &
```

### Note

Depending on the Jupyter version, warnings may appear indicating that some `NotebookApp` settings have moved to `ServerApp`.

These warnings do not necessarily indicate a failure.

---

# 7. Checking Whether Jupyter Is Running

Use:

```bash
ss -lntp | grep 8888
```

A result containing:

```text
0.0.0.0:8888
```

means Jupyter is listening on all IPv4 interfaces.

Jupyter can also be tested locally on the PYNQ board:

```bash
curl http://127.0.0.1:8888
```

An HTTP response confirms that the Jupyter server is running.

---

# 8. Ethernet 4 Configuration

When directly connecting the computer to the PYNQ-Z2 using an Ethernet cable, the computer may not automatically receive an IPv4 address because there may be no DHCP server.

In this case, Windows may assign an address such as:

```text
169.254.x.x
```

This is a link-local/APIPA address.

For communication with the PYNQ-Z2, the computer should be configured on the same subnet.

### Windows Ethernet Configuration

Open:

```text
Win + R
```

and enter:

```text
ncpa.cpl
```

Then:

```text
Ethernet 4
→ Properties
→ Internet Protocol Version 4 (TCP/IPv4)
→ Properties
```

Select:

```text
Use the following IP address
```

Configure:

```text
IP address     : 192.168.2.10
Subnet mask    : 255.255.255.0
Default gateway: Leave blank
DNS            : Leave blank
```

The PYNQ-Z2 remains:

```text
192.168.2.99
```

Therefore both devices are on:

```text
192.168.2.0/24
```

---

# 9. Final Ethernet Connection

The resulting direct connection is:

```text
Computer
   │
   │ Ethernet
   │
Ethernet 4
192.168.2.10
   │
   │
   ▼
PYNQ-Z2
192.168.2.99
```

The computer's Wi-Fi connection can remain enabled for Internet access.

Ethernet 4 is used specifically for communication with the PYNQ-Z2.

---

# 10. Testing the Connection

From Windows Command Prompt:

```cmd
ping 192.168.2.99
```

A successful connection should produce responses similar to:

```text
Reply from 192.168.2.99: bytes=32 time<1ms TTL=64
```

If the computer has an address such as:

```text
169.254.x.x
```

instead of:

```text
192.168.2.10
```

the Ethernet interface is not configured on the same subnet.

---

# 11. Accessing Jupyter From the Computer

Once the Ethernet connection is working, open a web browser and enter:

```text
http://192.168.2.99:8888
```

This connects to the Jupyter server running on the PYNQ-Z2.

The Jupyter token can be obtained from the PYNQ terminal when the server starts.

---

# 12. Troubleshooting Summary

### Problem 1 — Serial connection not working

**Check:**

```text
Device Manager → Ports (COM & LPT)
```

Make sure PuTTY is using the correct COM port.

---

### Problem 2 — Jupyter does not open in the browser

First check whether Jupyter is running:

```bash
ss -lntp | grep 8888
```

Expected:

```text
0.0.0.0:8888
```

Then check the PYNQ IP:

```bash
ip addr
```

---

### Problem 3 — Windows cannot ping PYNQ

Check:

```cmd
ipconfig
```

If Ethernet 4 shows:

```text
169.254.x.x
```

configure a static IP:

```text
IP address : 192.168.2.10
Subnet     : 255.255.255.0
```

Then test again:

```cmd
ping 192.168.2.99
```

---

# 13. Key Concepts Learned

By completing this setup, the following concepts are introduced:

* PYNQ-Z2 architecture
* Processing System (PS)
* Programmable Logic (PL)
* PYNQ Linux
* Serial communication
* PuTTY
* Ethernet networking
* Static IP addressing
* Subnet masks
* APIPA/link-local addresses
* Jupyter Notebook
* JupyterLab
* Jupyter server ports
* `0.0.0.0`
* Localhost (`127.0.0.1`)
* Basic network troubleshooting

---

## Setup Complete

The PYNQ-Z2 is successfully connected to the computer through Ethernet, and Jupyter Notebook is accessible from the computer.

**Lesson 1: Setup & Jupyter Access — Completed ✅**
