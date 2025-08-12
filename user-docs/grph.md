```mermaid
graph TD
    subgraph "Control Plane"
        direction LR
        A[Kubernetes API Server]
        KGC[KGateway Controller]
        
        KGC -- watches --> KGW_CRD["Gateway CRD<br>(gateway.kgateway.dev)"]
        KGC -- watches --> KHTTP_CRD["HTTPRoute CRD<br>(gateway.kgateway.dev)"]
        KGC -- watches --> KSVC[KServe InferenceService]
        
        A -- informs --> KGC
        
        KGC -- configures --> EP["Envoy Proxy<br>(Data Plane)"]
    end

    subgraph "Data Plane"
        direction LR
        U[User / Client] --> EP
    end

    subgraph "ML Models (KServe)"
        direction LR
        M1[Model 1 Pod]
        M2[Model 2 Pod]
        MN[Model 'N' Pod]
    end

    EP -- routes traffic to --> M1
    EP -- routes traffic to --> M2
    EP -- routes traffic to --> MN

    style KGC fill:#d4edda,stroke:#155724
    style EP fill:#cce5ff,stroke:#004085
    style M1 fill:#fff3cd,stroke:#856404
    style M2 fill:#fff3cd,stroke:#856404
    style MN fill:#fff3cd,stroke:#856404
