# Data Link Layer (DLL) 

The **Data Link Layer** sits between the **Physical Layer (bit stream)** and the **Network Layer (packets)**.  
It receives raw bits from the Physical Layer, organizes them into **frames**, and delivers them reliably to the Network Layer.  

📌 **Data Unit:** **Frame** (Header + Payload + Trailer)

---

## 1. Logical Link Control (LLC Sublayer – Upper DLL)

### Framing
Breaks bit stream into **frames**.

- **Variable-size framing:**  
  - **Flag Byte (0x7E = 01111110)** marks start/end.  
  - **Byte Stuffing:** ESC is added before accidental flag byte in data.  
  - **Bit Stuffing:** After 5 consecutive 1s, a 0 is stuffed to avoid confusion with flag.  
- **Fixed-size framing:** Used in ATM (cells).

---

### Error Control
Ensures reliable delivery with **error detection + retransmission**.  

- **Checksum:**  
  A simple error-detecting code where the sender sums all bytes (or words) and appends the complement.  
  The receiver recomputes; mismatch means error.  

- **CRC (Cyclic Redundancy Check):**  
  Treats bits as a polynomial.  
  - Sender divides data bits by a generator polynomial `G(x)` and appends the remainder (**Frame Check Sequence, FCS**).  
  - Receiver recomputes; if remainder ≠ 0 → error.  
  - Detects:  
    - All single-bit errors  
    - All double-bit errors  
    - All odd number of errors  
    - Burst errors up to degree of generator  

---

### Flow Control
Prevents fast sender from overwhelming slow receiver.  

- **Stop-and-Wait ARQ:**  
  Sender sends one frame, waits for ACK. Simple but inefficient.  

- **Sliding Window Protocol:**  
  Sender maintains window of multiple outstanding frames.  

  - **Go-Back-N (GBN):**  
    - Sender can send up to *N* frames before ACK.  
    - On error, receiver discards erroneous + subsequent frames.  
    - Sender retransmits that frame + all after it.  
    - ✅ Simple receiver, ❌ wastes bandwidth on errors.  

  - **Selective Repeat (SR):**  
    - Receiver accepts frames out of order and buffers them.  
    - Only retransmits the missing frame(s).  
    - ✅ Efficient bandwidth use, ❌ more complex receiver.  

---

## 2. Medium Access Control (MAC Sublayer – Lower DLL)

Handles **channel sharing** in broadcast networks (e.g., LANs).  
Controls **who transmits when** and uses **physical addresses**.

---

### Physical Addressing
- Every NIC has a **unique 48-bit MAC address** (e.g., `3C-95-09-4B-32-1A`).  
- Used for **hop-to-hop delivery** inside a LAN.  
- Changes at each hop (router).  
- Switches and bridges forward frames using **MAC tables**.

---

### Random Access Protocols
All stations compete for channel → collisions possible.

- **ALOHA:**  
  - Send anytime. Collisions waste channel.  
  - **Slotted ALOHA:** Restricts to time slots, improving efficiency.  

- **CSMA (Carrier Sense Multiple Access):**  
  - Station senses channel before sending.  

  - **CSMA/CD (Collision Detection):**  
    - Used in classic Ethernet (IEEE 802.3).  
    - If collision detected → stop, send jamming signal, wait (binary exponential backoff), retry.  
    - Requires full-duplex (hard in wireless).  

  - **CSMA/CA (Collision Avoidance):**  
    - Used in Wi-Fi (IEEE 802.11).  
    - Avoids collisions by waiting (IFS) + using **NAV timers**.  
    - Handles **Hidden Terminal Problem**:  
      - **RTS/CTS (Request to Send / Clear to Send):** handshake before sending data.  

---

### Channelization Protocols
- **FDMA:** Bandwidth divided into frequency slots.  
- **TDMA:** Bandwidth divided into time slots.  
- **CDMA:** Stations use orthogonal codes to share channel simultaneously.  

---

### Comparison: CSMA/CD vs CSMA/CA

| Feature | CSMA/CD (Ethernet) | CSMA/CA (Wi-Fi) |
|---------|--------------------|-----------------|
| Medium  | Wired LAN          | Wireless LAN    |
| Collision Handling | Detects collision, stops transmission | Tries to avoid collisions beforehand |
| Mechanism | Sense → Transmit → Detect collision | Sense → Wait (IFS) → RTS/CTS → Transmit |
| Efficiency | High in low-collision networks | Better for noisy/wireless networks |
| Used In | IEEE 802.3 Ethernet | IEEE 802.11 Wi-Fi |

---

## Summary of DLL Structure (IEEE 802)

| Sublayer | Role | Key Concepts |
|----------|------|--------------|
| **LLC** (Upper DLL) | Framing, Flow & Error Control | Frames, Checksum, CRC, ARQ, GBN, SR |
| **MAC** (Lower DLL) | Media Access & Addressing | MAC Address, ALOHA, CSMA/CD, CSMA/CA, RTS/CTS |
