<img width="685" height="747" alt="image" src="https://github.com/user-attachments/assets/2f3513b8-6bc7-4f8b-9177-9c57701149b4" />

# 🧬 Biometric Portal: 3D Face-Mesh Authentication

A high-security biometric authentication system designed for browser extensions. This portal leverages **MediaPipe's Face Landmarker** to capture and verify identity using a **478-point 3D spatial mesh**, featuring real-time liveness detection through blink verification.

---

## 🚀 Key Features

* **3D Spatial Mapping**: Captures true $X, Y, and Z$ coordinates for 478 facial landmarks, offering significantly higher security than traditional 2D point mapping.
* **Real-Time Liveness Detection**: Mandatory "Blink Requirement" to prevent spoofing via high-resolution photographs or digital screens.
* **Centroid Normalization**: Server-side mathematical normalization that centers the mesh relative to the nose tip (Landmark #1), ensuring accurate matches regardless of the user's distance or position in the frame.
* **Fully Local AI Engine**: WASM-based execution within the extension environment to ensure biometric data is processed locally before being hashed for the server.
* **Privacy-First Architecture**: Stores mathematical biometric blueprints instead of raw images or identifiable photos.

---

## 🛠️ Technical Architecture

### Frontend (Browser Extension)
* **Engine**: MediaPipe Vision (Task-based).
* **Acceleration**: Hardware-accelerated via GPU and XNNPACK delegates.
* **Local WASM**: Runtime files are hosted internally in `/website/wasm/` to ensure performance and bypass strict Content Security Policies (CSP).
* **Loop**: Synchronized 60fps `requestAnimationFrame` loop for real-time user feedback.

### Backend (Node.js Server)
* **Validation**: 3D Euclidean Distance Algorithm to measure Mean Square Error (MSE) between live and stored coordinates.
* **Liveness Gatekeeper**: Vertical distance calculation between eyelid landmarks (**159/145** and **386/374**) to confirm a physical blink.

---

## 📊 The Biometric Score

Verification is based on the 3D Euclidean distance formula for each point $i$:

$$d_i = \sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2 + (z_2 - z_1)^2}$$

### Threshold Calibration
| Score Range | Status | Description |
| :--- | :--- | :--- |
| **0.0000 - 0.0010** | **Identity Match** | Near-perfect structural match. |
| **0.0011 - 0.0070** | **Authorized** | Secure match allowing for minor lighting variance. |
| **0.0071 - 0.0150** | **Review** | Close match; usually indicates poor lighting or tilt. |
| **> 0.0150** | **Unauthorized** | Face mismatch or significant spoofing attempt. |

---

## 📂 Installation & Setup

### 1. File Structure
Ensure your extension directory follows this structure for the local engine to load:
```text
website/
├── wasm/
│   ├── face_landmarker.task
│   ├── vision_wasm_internal.wasm
│   └── vision_wasm_internal.js
├── scanner.js
├── vision_bundle.js
└── test.html


















































































































































































