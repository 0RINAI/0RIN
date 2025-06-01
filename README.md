# 0RIN Quantum-Safe MCP Client

This project provides a client application (qu3-app) for secure interaction with Quantum-Safe Multi-Compute Provider (MCP) environments. It leverages post-quantum cryptography (PQC) standards for establishing secure communication channels, ensuring client authenticity, and verifying server attestations.

This client is designed to work with MCP servers that support the QU3 interaction protocols. For development and testing, a compatible mock server implementation is included in scripts/mock_mcp_server.py.

# Client Component Interaction
This diagram shows how the main Python modules within the client application interact:
Configuration & Keys

User CLI (Type)
├── src_main
│   └── Users
├── src_config_utils
│   └── Users
│       └── configuration & Keys
├── PyYAML
│   └── Key Files
├── Users
│   └── requests Session
│       └── Networking
├── Users
│   └── src_pqc_utils
│       └── Users
├── libops_python
│   └── Cryptography
│       └── Users
└── cryptography

# Key Management Overview
Keys are crucial for the security protocols. Here's how they are managed:

![image](https://github.com/user-attachments/assets/957517f2-27d4-42da-9167-6db2ea4dd05f)

# Core Components
src/main.py: Command-line interface (CLI) built with Typer. Handles user commands, orchestrates client operations, and displays results. Includes commands for key generation, inference, agent workflows, and policy updates.
src/mcp_client.py: The main client class (MCPClient) responsible for:
Managing PQC keys.
Defining request (MCPRequest) and response (MCPResponse) data structures.
Establishing secure sessions via KEM handshake (connect).
Sending signed and encrypted requests (send_request).
Processing and verifying encrypted/signed responses.
Handling disconnection (disconnect).
src/pqc_utils.py: Utility functions for PQC operations (Kyber KEM, SPHINCS+ signing) using liboqs-python, AES-GCM encryption/decryption using cryptography, and HKDF key derivation.
src/config_utils.py: Handles loading configuration from config.yaml, loading/saving keys from/to files, and fetching server public keys from the /keys endpoint.
scripts/mock_mcp_server.py: A FastAPI development/test server that simulates an MCP environment. Implements the server-side logic for KEM handshake, request decryption/verification, basic model execution, attestation signing, response encryption, policy updates, and key distribution.
config.yaml: Configuration file for storing settings like the default key directory (key_directory) and server URL (server_url).
tests/: Directory containing unit tests (unittest) for core components (pqc_utils, config_utils, mcp_client).

# Features
PQC Algorithms: Uses NIST PQC finalists:
KEM: Kyber-768
Signature: SPHINCS+-SHA2-128f-simple
PQC Key Management: Generates and loads Kyber and SPHINCS+ key pairs.
Secure Session Establishment: Uses Kyber KEM over a network handshake (/kem-handshake/initiate) to establish a shared secret.
Key Derivation: Derives a 32-byte AES-256 key from the KEM shared secret using HKDF-SHA256.
Encrypted Communication: Encrypts request/response payloads (after KEM handshake) using AES-256-GCM with the derived session key.
Client Authentication: Client signs requests using SPHINCS+; server verifies.
Server Attestation: Server signs responses (attestation data) using SPHINCS+; client verifies.
Configuration: Loads key directory and server URL from config.yaml.
Automated Server Key Fetching: Client automatically fetches server public keys from the /keys endpoint if not found locally.
CLI Commands:
generate-keys: Creates client key pairs.
run-inference: Sends a single, secured inference request.
run-agent: Executes a sequential workflow (modelA->modelB), passing outputs as inputs (wraps non-dict output), with step-by-step reporting and robust failure handling.
update-policy: Sends an encrypted and signed policy file to the server.
Mock Server: Includes endpoints (/, /keys, /kem-handshake/initiate, /inference, /policy-update) implementing the corresponding server-side PQC and communication logic for testing. Provides example models model_caps and model_reverse.
Unit Tests: Includes unit tests covering core cryptographic utilities, configuration management, and client communication logic (with network mocking).
