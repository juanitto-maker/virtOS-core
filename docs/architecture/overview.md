# VirtOS - High-Level Architecture Overview

## Introduction

VirtOS is designed as a fully sandboxed, local-first virtual operating environment. Its architecture prioritizes security and privacy by assuming a potentially hostile host OS and minimizing trust. The core of the system is a single, encrypted container ("The Vault") that holds all user data and state.

## Core Components

The system is composed of several key, decoupled components:

1.  **The App Shell:**
    * **[span_0](start_span)Function:** Renders the virtual desktop UI[span_0](end_span) and manages the windowing of internal modules. It is the user's primary interface.
    * **[span_1](start_span)Implementation:** A web-technology-based frontend (e.g., React, Svelte) running inside an optional native wrapper (e.g., Tauri or Capacitor)[span_1](end_span).
    * **[span_2](start_span)[span_3](start_span)Security:** Responsible for implementing the `FLAG_SECURE` screen-recording block[span_2](end_span)[span_3](end_span).

2.  **The Vault (Core Logic):**
    * **[span_4](start_span)Function:** Manages the single-file encrypted container where all user data is stored[span_4](end_span). It handles all cryptographic operations (encryption/decryption).
    * **Implementation:** Utilizes established, audited cryptographic libraries (e.g., libsodium, Web Crypto API) for AES-256 encryption. [span_5](start_span)All decryption happens in-memory, with no decrypted data ever written to the host OS's persistent storage[span_5](end_span).
    * **Security:** The heart of the Zero-Knowledge principle.

3.  **The Security Kernel:**
    * **Function:** A collection of services that provide proactive defense against threats from the host OS.
    * **Modules:**
        * **[span_6](start_span)Biometric Handler:** Interfaces with the host OS's biometric APIs (WebAuthn, BiometricPrompt)[span_6](end_span) to authorize vault decryption without using passwords.
        * **[span_7](start_span)Embedded Keyboard:** A custom, self-contained keyboard rendered by the App Shell to prevent host OS keyloggers from capturing input[span_7](end_span).
        * **[span_8](start_span)"Canary" Integrity Monitor:** Continuously validates the cryptographic hash of the Vault file and the VirtOS application code itself to detect tampering by host-level malware[span_8](end_span).
        * **[span_9](start_span)Secure Transfer Service:** Generates and verifies checksums for the Vault file during physical transfer to ensure its integrity[span_9](end_span).

4.  **The AI Engine (Local-First):**
    * **[span_10](start_span)Function:** Executes on-device AI/ML models for features like search and content generation[span_10](end_span).
    * **[span_11](start_span)Implementation:** Leverages local inference engines like llama.cpp or WebGPU-based runners to operate on data directly from RAM[span_11](end_span).
    * **[span_12](start_span)[span_13](start_span)Security:** Ensures that all AI processing of user data happens offline by default, fulfilling a core privacy promise[span_12](end_span)[span_13](end_span).

5.  **The Module Manager:**
    * **[span_14](start_span)Function:** Loads and manages the lifecycle of internal "apps" or plugins (e.g., Text Editor, PDF Summarizer)[span_14](end_span).
    * **Implementation:** A secure plugin system that ensures modules only run within the VirtOS sandbox and only have access to the APIs provided by VirtOS.

6.  **The Connectivity Manager:**
    * **Function:** Manages all optional outbound network requests.
    * **[span_15](start_span)Implementation:** By default, all networking is blocked[span_15](end_span). [span_16](start_span)[span_17](start_span)This component is responsible for establishing user-initiated, secure P2P tunnels (e.g., WireGuard, Tor) for the optional cloud compute feature[span_16](end_span)[span_17](end_span).

## Conceptual Data Flow (Example: Saving a Note)

1.  User types into the **Embedded Keyboard**.
2.  Input is sent directly to the active module (e.g., Markdown Editor) inside the **App Shell**.
3.  The text exists in-memory (RAM) within the VirtOS process.
4.  User clicks "Save."
5.  The **Vault** component encrypts the text and writes it back into the single encrypted container file on the host's storage.
6.  The **"Canary" Monitor** updates the vault's integrity hash.
