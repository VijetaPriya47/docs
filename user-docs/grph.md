```mermaid
graph TD
    subgraph "External User"
        U[Client / User]
    end

    subgraph "Kubernetes Cluster"
        
        subgraph "KGateway Ingress Layer"
            EP["Envoy Proxy (Data Plane)"]
            KGC["KGateway Controller (Control Plane)"]
        end

        subgraph "KServe ML Models"
            IS[InferenceService]
            IS --> P1[Model Pod 1]
            IS --> P2[Model Pod 2]
        end
        
        subgraph "Configuration Resources (YAMLs)"
            GW_CRD["Gateway CRD<br>(gateway.kgateway.dev)"]
            HTTP_CRD["HTTPRoute CRD<br>(gateway.kgateway.dev)"]
            IS_CRD["InferenceService CRD<br>(serving.kserve.io)"]
        end

        APIServer[Kubernetes API Server]
    end

    %% --- Data Flow ---
    U -- 1. HTTP/HTTPS Request --> EP

    %% --- Configuration Flow ---
    GW_CRD -- 2. Apply Config --> APIServer
    HTTP_CRD -- 2. Apply Config --> APIServer
    IS_CRD -- 2. Apply Config --> APIServer
    
    KGC -- 3. Watches for changes --> APIServer
    KGC -- 4. Generates config & pushes to --> EP
    
    %% --- Internal Routing ---
    EP -- 5. Routes request to --> P1
    EP -- or --> P2

    %% --- Styling ---
    style KGC fill:#d4edda,stroke:#155724,stroke-width:2px
    style EP fill:#cce5ff,stroke:#004085,stroke-width:2px
    style P1 fill:#fff3cd,stroke:#856404,stroke-width:2px
    style P2 fill:#fff3cd,stroke:#856404,stroke-width:2px
