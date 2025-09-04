# VirtOS - High-Level Architecture Overview

## Introduction

[span_35](start_span)[span_36](start_span)VirtOS is designed as a fully sandboxed, local-first virtual operating environment[span_35](end_span)[span_36](end_span). [span_37](start_span)Its architecture prioritizes security and privacy by assuming a potentially hostile host OS and minimizing trust[span_37](end_span). [span_38](start_span)The core of the system is a single, encrypted container ("The Vault") that holds all user data and state[span_38](end_span).

## Core Components

The system is composed of several key, decoupled components:

1.  **The App Shell:**
    * **[span_39](start_span)[span_40](start_span)Function:** Renders the virtual desktop UI and manages the windowing of internal modules[span_39](end_span)[span_40](end_span). It is the user's primary interface.
    * **[span_41](start_span)Implementation:** A web-technology-based frontend (e.g., React, Svelte) running inside an optional native wrapper (e.g., Tauri or Capacitor)[span_41](end_span).
    * **[span_42](start_span)Security:** Responsible for implementing the `FLAG_SECURE` screen-recording block[span_42](end_span).

2.  **The Vault (Core Logic):**
    * **[span_43](start_span)Function:** Manages the single-file encrypted container where all user data is stored[span_43](end_span). [span_44](start_span)It handles all cryptographic operations (encryption/decryption)[span_44](end_span).
    * **Implementation:** Utilizes established, audited cryptographic libraries (e.g., libsodium, Web Crypto API) for AES-256 encryption. [span_45](start_span)All decryption happens in-memory, with no decrypted data ever written to the host OS's persistent storage[span_45](end_span).
    * **[span_46](start_span)Security:** The heart of the Zero-Knowledge principle[span_46](end_span).

3.  **The Security Kernel:**
    * **[span_47](start_span)Function:** A collection of services that provide proactive defense against threats from the host OS[span_47](end_span).
    * **Modules:**
        * **[span_48](start_span)[span_49](start_span)[span_50](start_span)Biometric Handler:** Interfaces with the host OS's biometric APIs (WebAuthn, BiometricPrompt) to authorize vault decryption without using passwords[span_48](end_span)[span_49](end_span)[span_50](end_span).
        * **[span_51](start_span)Embedded Keyboard:** A custom, self-contained keyboard rendered by the App Shell to prevent host OS keyloggers from capturing input[span_51](end_span).
        * **[span_52](start_span)"Canary" Integrity Monitor:** Continuously validates the cryptographic hash of the Vault file and the VirtOS application code itself to detect tampering by host-level malware[span_52](end_span).
        * **[span_53](start_span)Secure Transfer Service:** Generates and verifies checksums for the Vault file during physical transfer to ensure its integrity[span_53](end_span).

4.  **The AI Engine (Local-First):**
    * **[span_54](start_span)[span_55](start_span)Function:** Executes on-device AI/ML models for features like search and content generation[span_54](end_span)[span_55](end_span).
    * **[span_56](start_span)Implementation:** Leverages local inference engines like llama.cpp or WebGPU-based runners to operate on data directly from RAM[span_56](end_span).
    * **[span_57](start_span)[span_58](start_span)Security:** Ensures that all AI processing of user data happens offline by default, fulfilling a core privacy promise[span_57](end_span)[span_58](end_span).

5.  **The Module Manager:**
    * **[span_59](start_span)[span_60](start_span)Function:** Loads and manages the lifecycle of internal "apps" or plugins (e.g., Text Editor, PDF Summarizer)[span_59](end_span)[span_60](end_span).
    * **Implementation:** A secure plugin system that ensures modules only run within the VirtOS sandbox and only have access to the APIs provided by VirtOS.

6.  **The Connectivity Manager:**
    * **Function:** Manages all optional outbound network requests.
    * **[span_61](start_span)Implementation:** By default, all networking is blocked[span_61](end_span). [span_62](start_span)This component is responsible for establishing user-initiated, secure P2P tunnels (e.g., WireGuard, Tor) for the optional cloud compute feature[span_62](end_span).

## Conceptual Data Flow (Example: Saving a Note)

1.  [span_63](start_span)User types into the **Embedded Keyboard**[span_63](end_span).
2.  [span_64](start_span)[span_65](start_span)Input is sent directly to the active module (e.g., Markdown Editor) inside the **App Shell**[span_64](end_span)[span_65](end_span).
3.  The text exists in-memory (RAM) within the VirtOS process.
4.  User clicks "Save."
5.  [span_66](start_span)The **Vault** component encrypts the text and writes it back into the single encrypted container file on the host's storage[span_66](end_span).
6.  [span_67](start_span)The **"Canary" Monitor** updates the vault's integrity hash to ensure it has not been tampered with[span_67](end_span).
