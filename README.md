# Optical File Transfer (OFT)

**An offline, camera-to-screen file bridge.** 

Optical File Transfer is a zero-dependency, self-contained web utility that allows you to securely transfer any file between two devices using only flashing light (QR code animations) and a camera. **No server, no pairing, no account, and no shared Wi-Fi network are required.**

---

## Features

- **100% Offline & Air-Gapped:** The entire application lives inside a single HTML file. Nothing leaves your device; files are processed entirely in browser memory.
- **Built-in Pure-JS QR Decoder:** Features a custom, optimized Reed-Solomon error-correcting QR decoder embedded directly in the script. Works seamlessly on iOS Safari, Android Chrome, and desktop browsers without requiring native platform APIs like `BarcodeDetector`.
- **Robust Protocol (`OFT1`):** Employs an independent, checksum-validated framing system where metadata and data chunks are sequenced and independently verified via CRC-32 and SHA-256.
- **Continuous Loop Broadcasting:** The sender cycles through its frames indefinitely, ensuring that any missed frames on the receiver's end are automatically picked up on subsequent passes.
- **Adjustable Transmission Speed:** Fine-tune your broadcasting pace (from 5 FPS up to 30 FPS) based on your hardware capabilities and lighting conditions.

---

## How It Works

1. **Sender Device:**
   - Selects any file (stored safely within browser memory).
   - Generates a unique transfer session ID and computes a **SHA-256 checksum** of the original file.
   - Converts metadata and chunks of byte payloads into repeating animated QR codes using an internal encoder.
2. **Receiver Device:**
   - Starts the camera and points it at the sender's screen.
   - Decodes incoming frames in real-time, instantly checking their CRC-32 integrity.
   - Displays a progress bar tracking missing chunks.
   - Once all chunks are collected, it rebuilds the file, verifies the final SHA-256 hash, and provides a direct download link.

---

## Protocol Specification (`OFT1`)

All frames follow a comma-separated text format:
```text
OFT1,type,session,sequence,total,CRC-32,payload
```
- **`OFT1`**: Protocol identifier.
- **`type`**: `M` for Metadata frames or `D` for Data frames.
- **`session`**: A unique 5–12 alphanumeric session ID string.
- **`sequence`**: The current sequence index of the frame.
- **`total`**: The total count of frames for that stream type.
- **`CRC-32`**: A hex checksum verifying the frame's payload integrity.
- **`payload`**: Base64url-encoded chunk data or metadata string.

---

## Getting Started

Because this is a completely self-contained solution:
1. Save the source code as `index.html`.
2. Open `index.html` directly in a browser on the **Sender** device to pick and broadcast your file.
3. Open `index.html` on the **Receiver** device (ensuring a secure context like HTTPS or `localhost` for camera permissions) to scan and assemble the file.

### Browser & Security Requirements
- **Camera Scanning:** Browsers strictly require a **Secure Context (`https://` or `http://localhost`)** to grant camera access. Opening the file via a raw `file://` URL will block camera APIs on mobile platforms like iOS Safari.
- **Memory Limit:** Files are held in browser memory during transit. Keep transfers to practical sizes (avoiding hundreds of megabytes to prevent browser out-of-memory crashes).

---

## Tips for Best Scanning Performance
- **Increase Brightness:** Set both screens to high brightness to improve optical contrast.
- **Avoid Glare:** Eliminate direct overhead reflections on the sender's screen.
- **Steady Hands:** Keep the receiving camera steady within the viewfinder guide.
- **Lower the Pace:** If the receiver skips frames, lower the sender's target FPS using the slider control.
