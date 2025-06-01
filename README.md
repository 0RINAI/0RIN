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

graph LR
    subgraph Client Side
        A["CLI: generate-keys"] --> B{"src_main.py"};
        B --> C["src_pqc_utils.py"]:::pqc --> D{"Generate KEM/Sign Pairs"};
        D --> E["src_config_utils.py"]:::config --> F["Save Client Keys (.pub, .sec)"];

        G["CLI: run-inference/etc."] --> B;
        B --> H{"Initialize Client"};
        H --> E --> I{"Load Client Keys?"};
        I -- Found --> J["Use Keys"];
        I -- Not Found --> D;

        H --> E --> K{"Load Server Keys?"};
        K -- Found --> J;
        K -- Not Found --> L{"Fetch Server Keys?"};
        L -- Calls --> E --> M["GET /keys"]:::net;
        M -- Response --> E --> N["Save Server Keys (.pub)"];
        N --> J;
        L -- Fetch Fail --> O["(Error - Cannot Proceed)"];
    end

    subgraph "Server Side (Mock)" 
        P["Server Startup"] --> Q["scripts_mock_mcp_server.py"];
        Q --> R["src_config_utils.py"]:::config --> S{"Load/Generate Server Keys"};
        S --> T["Save Server Keys (.pub, .sec)"];
        Q --> U["Register /keys Endpoint"];
        U -- Request for /keys --> V{"Return Server Public Keys"};
    end

    subgraph "Filesystem (Key Dir)"
        F
        T
        N
    end

    classDef pqc fill:#f9d,stroke:#333,stroke-width:2px;
    classDef config fill:#cfc,stroke:#333,stroke-width:2px;
    classDef net fill:#cdf,stroke:#333,stroke-width:2px;

