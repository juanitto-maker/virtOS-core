# VirtOS - Technical Stack & Architecture

This document details the hybrid architecture, AI model strategy, and distribution plan for the VirtOS project. It complements the `overview.md` file by providing more specific technical implementation details.

---

## 1. The Hybrid App Architecture

VirtOS is designed as a **Hybrid App** to achieve the best balance of cross-platform compatibility, user experience, and high performance. It is composed of three distinct layers.

### Layer 1: The User Interface (PWA Core)
The entire user interface (the virtual desktop, windows, modules) is built with modern web technologies. This ensures a fluid, responsive experience that is easy to develop and maintain.
* **Technology:** HTML, CSS, JavaScript/TypeScript.
* **Framework:** React or Svelte.

### Layer 2: The Native Shell (Standalone App)
To function as a true, installable application, the web-based UI is wrapped in a native shell. This shell acts as a secure bridge between the web UI and the device's native capabilities.
* **Technology:** **Capacitor** (for mobile - Android/iOS) and **Tauri** (for desktop - Windows/macOS/Linux).
* **Function:** Provides access to essential hardware like the BiometricPrompt (for fingerprint login), the local filesystem (for the vault), and implements system-level security features like the screen-recording block. The shell itself is built using the platform's native languages (Java/Kotlin for Android).

### Layer 3: The High-Performance Engine (C++ Core)
For tasks that require maximum speed and security, VirtOS relies on a high-performance backend written in C++. The web UI communicates with this engine.
* **AI Engine:** Powered by C++ based libraries like **`llama.cpp`** to run local language models efficiently.
* **Crypto Core:** Utilizes a battle-tested C++ library like **`libsodium`** for all core encryption and decryption operations within the vault, ensuring maximum security and speed.

---

## 2. Tiered AI Model Strategy

VirtOS will support different classes of AI models to adapt to the user's hardware.

### Tier 1: Mobile & Low-Power Devices
This tier focuses on small, quantized models (typically under 1.5 GB) that can run efficiently on most modern smartphones.
* **Supported Models:** TinyLLaMA, Phi-2, Gemma, Qwen, and other similar-sized models.

### Tier 2: Desktop & High-Performance Devices
For users on powerful hardware (PCs with a dedicated GPU), VirtOS will support larger, more capable models.
* **Supported Models:** Llama 3, Mixtral, Stable Diffusion (for image generation), and other large open-source models.

A **Hardware Evaluation** utility will be included in the app. It will run a quick benchmark upon first install and recommend to the user which tier of models their device can comfortably support.

---

## 3. Distribution and Deployment Strategy

The project will be distributed through two main channels to balance accessibility with security.

### The Secure Version (GitHub Releases)
The full, official, and most secure versions of VirtOS will be provided as installable packages directly from the project's GitHub "Releases" page.
* **Platforms:** Standalone installers for Android (`.apk`), Windows (`.msi`), macOS (`.dmg`), and Linux (`.AppImage`, `.deb`).

### The Public Demo (Web Deployment)
A feature-limited demo version will be hosted on a service like Vercel or Netlify.
* **Purpose:** To allow new users to instantly try out the VirtOS interface without installing anything.
* **Security Notice:** This web version will feature a prominent, permanent banner stating: *"This is a public demo for demonstration purposes only. For full privacy and security, please download the standalone application from our GitHub."* This ensures users understand the privacy trade-offs and directs them to the secure, local-first version for real use.
