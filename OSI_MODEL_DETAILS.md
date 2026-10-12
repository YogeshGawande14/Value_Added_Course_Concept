# OSI MODEL — Open Systems Interconnection Model

The **OSI (Open Systems Interconnection) model** is a conceptual framework that explains how data moves through a network. It divides network communication into **seven layers**, and each layer has a specific responsibility.

## The Seven Layers

### Layer 1: Physical

- **Main role:** Transmits raw bits as electrical, optical, or radio signals.
- **Deals with:** Cables, connectors, radio signals, voltage, and physical transmission.
- **Example:** Connecting a LAN cable or sending data through Wi-Fi signals.

### Layer 2: Data Link

- **Main role:** Transfers data between devices on the same local network link.
- **Uses:** MAC addresses and frames.
- **Devices / examples:** Ethernet switches and a laptop's MAC address.
- **Example:** A switch forwards an Ethernet frame to the appropriate device on a local network.

### Layer 3: Network

- **Main role:** Handles logical addressing and routing between networks.
- **Uses:** IP addresses and packets.
- **Devices / examples:** Routers and IP routing.
- **Example:** Routing a packet from `192.168.1.10` toward `8.8.8.8`.

### Layer 4: Transport

- **Main role:** Provides communication between applications on different devices.
- **Protocols:** TCP and UDP.
- **TCP:** Provides reliable, ordered delivery and retransmits lost data when needed.
- **UDP:** Provides datagram delivery without TCP's built-in reliability and ordering guarantees.
- **Example:** TCP is commonly used for file downloads; UDP is commonly used for real-time voice and video.

### Layer 5: Session

- **Main role:** Establishes, manages, and ends communication sessions between applications.
- **Example:** Managing an ongoing application conversation or session.
- **Note:** In modern TCP/IP networking, session-layer responsibilities are often handled by application protocols and libraries rather than a separate OSI layer.

### Layer 6: Presentation

- **Main role:** Represents data in a form the receiving application can understand.
- **Deals with:** Data formats, encoding, compression, and encryption/decryption concepts.
- **Example:** Converting data between formats or encoding text.
- **Note:** TLS provides encryption for many network connections, but it does not map exclusively to the OSI Presentation layer.

### Layer 7: Application

- **Main role:** Provides network services used by applications.
- **Protocols / services:** HTTP/HTTPS, SMTP, DNS, and FTP.
- **Example:** Opening Gmail in a browser or visiting a website using HTTPS.
- **Important:** The Application layer is not the same thing as the entire application interface; it describes network services used by applications.

---

## Mnemonic to Remember the Layers

**“Please Do Not Throw Sausage Pizza Away”**

| First letter | OSI layer |
|---|---|
| P | Physical |
| D | Data Link |
| N | Network |
| T | Transport |
| S | Session |
| P | Presentation |
| A | Application |

Remember the layers from **Layer 1 to Layer 7**, starting with Physical and ending with Application.

## Quick Summary

| Layer | Name | Key idea |
|---:|---|---|
| 7 | Application | Network services used by applications |
| 6 | Presentation | Data format, encoding, encryption concepts |
| 5 | Session | Managing communication sessions |
| 4 | Transport | TCP/UDP and end-to-end transport |
| 3 | Network | IP addresses and routing |
| 2 | Data Link | Frames and MAC addresses |
| 1 | Physical | Signals, cables, and raw bits |

## Easy Way to Understand Data Flow

When you send data over a network, it moves conceptually **from Layer 7 down to Layer 1** on the sending device. The receiving device processes it **from Layer 1 up to Layer 7**.

```text
SENDER                         RECEIVER
Layer 7: Application           Layer 7: Application
Layer 6: Presentation          Layer 6: Presentation
Layer 5: Session               Layer 5: Session
Layer 4: Transport             Layer 4: Transport
Layer 3: Network               Layer 3: Network
Layer 2: Data Link             Layer 2: Data Link
Layer 1: Physical  ----------> Layer 1: Physical
              Network medium
```

This is a conceptual view. Real-world TCP/IP networks do not always implement seven separate layers exactly as shown in the OSI model.

## Important Addressing Terms

- **MAC address:** Used for delivery on a local network link.
- **IP address:** Used for logical addressing and routing between networks.
- **Port number:** Identifies a service or application endpoint at the Transport layer.

## Final Revision

- **Physical:** Signals and hardware transmission.
- **Data Link:** Frames and MAC addresses.
- **Network:** Packets, IP addresses, and routing.
- **Transport:** TCP/UDP and transport between endpoints.
- **Session:** Managing communication sessions.
- **Presentation:** Data representation, encoding, and encryption concepts.
- **Application:** Network services such as HTTP, DNS, and email protocols.

---

*This file provides a beginner-friendly overview of the OSI model.*
