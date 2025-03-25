---
title: Installation & Quick Start
description: Install Cosdata and get up and running quickly with your first vector database
---

**This guide will walk you through installation and setup of Cosdata**

## Installation Guide


### **Quick Install (Linux)**  

For a simple one-step installation on Linux, run:  

```bash
curl -sL https://cosdata.io/install.sh | bash
```  

This script will handle all dependencies and set up Cosdata automatically.  

---

### **Install via Docker (Mac & Windows)**  

For Mac & Windows, use our Docker-based installation:  

1. Ensure Docker is installed and running.  
2. Pull and run the Cosdata image from our registry.  

```bash
docker run -it --name cosdata -p 8443:8443 -p 50051:50051 arjun7356/cosdata:latest
```  

**Note:** Full Docker setup instructions are available in [Docker Install Docs](#).  

---

### **Developer Installation (Build from Source)**  

For developers looking to modify or contribute to Cosdata, follow these steps:  
    

1. Install Rust and Cargo:  

   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   ```  

2. Ensure dependencies are installed:  
   - **Linux**: `libssl-dev`, `pkg-config`  
   - **Mac**: `brew install openssl pkg-config`  

3. Clone and build Cosdata:  

   ```bash
   git clone https://github.com/cosdata-ai/cosdata.git
   cd cosdata
   cargo build --release
   ```  

4. Start the server:

    ```bash
    cargo run -- --admin-key 12345
    ```

---

This version strictly follows your preferred format. Let me know if you need any modifications! 🚀

## Running Cosdata

Start the Cosdata server with your admin key:

```bash
cosdata --admin-key <your-admin-key>
```

You should see log lines similar to the following:

```
[2025-02-21T02:30:29Z INFO  cosdata::web_server] starting HTTP server at http://127.0.0.1:8443
[2025-02-21T02:30:29Z INFO  actix_server::builder] starting 20 workers
[2025-02-21T02:30:29Z INFO  actix_server::server] Actix runtime found; starting in Actix runtime
[2025-02-21T02:30:29Z INFO  actix_server::server] starting service: "actix-web-service-127.0.0.1:8443", workers: 20, listening on: 127.0.0.1:8443
[2025-02-21T02:30:29Z INFO  cosdata::grpc::server] gRPC server listening on [::1]:50051
```

---

## **Need Help?**  
If you encounter any issues during installation, join our [Discord community](https://discord.gg/XMdtTBrtKT) for real-time support from the Cosdata team and community members.

---

## Configuration Options

Cosdata can be configured using command-line arguments or a configuration file:

### **Command-line Arguments**
```bash
cosdata --admin-key <your-admin-key> --port 8443 --data-dir /path/to/data
```

### **Configuration File**
Create a `config.toml` file:

```toml
admin_key = "your-admin-key"
port = 8443
data_dir = "/path/to/data"
log_level = "info"
```

Then run Cosdata with:

```bash
cosdata --config config.toml
```

---

## Quick Start: Testing Your Installation

Now that Cosdata is running, let's verify the installation by running a simple test script.

### Setting Up the Test Environment

1. **Install Python Dependencies**

   ```bash
   # Navigate to the test directory
   cd tests
   
   # Install dependencies using uv (or pip)
   uv sync
   ```

2. **Run the Basic Test Script**

   The `test.py` script performs the following:
   
   - Creates a test collection and a Dense HNSW Index
   - Submits batches of random vectors in a transaction
   - Uses about 10% of the vectors as query vectors by adding small perturbations
   - Issues queries to the server and performs a brute force search locally
   - Compares the results

   ```bash
   uv run test.py
   ```

   If successful, you should see output confirming that the test passed, with metrics on query performance.

### Testing with Real-World Datasets

For a more comprehensive test with real-world data:

```bash
uv run test-dataset.py
```

This script loads sample datasets and performs similarity searches to demonstrate Cosdata's capabilities in a realistic scenario.

## Creating Your First Collection

Let's create a simple collection and add some vectors using the Python SDK:

```python
from cosdata.client import Client

# Connect to the Cosdata server
client = Client(
    host = "http://127.0.0.1:8443",
)

# Create a collection for document embeddings
collection = client.create_collection(
    name="documents",
    description="Collection for document embeddings",
    dimension=3  # Dimensionality of vectors to be stored
)

index = collection.create_index(
    distance_metric="cosine" 
)

vectors = [
    {"id": "doc1", "values": [0.1, 0.2, 0.3]}, 
    {"id": "doc2", "values": [0.1, 0.7, 0.2]},
]

# Upsert all vectors in a single transaction (SDK will handle batching)
with index.transaction() as txn: 
    txn.upsert(vectors)  

# Search for similar vectors
results = index.query(
    vector=[0.1, 0.2, 0.3],
    nn_count=1
)

# Print results
for result in results:
    print(f"Query result: {results}")
```

## Verifying Your Installation

To verify that Cosdata is running correctly, you can use the health check endpoint:

```bash
curl http://localhost:8443/health
```

If everything is working, you should receive a `200 OK` response.

## Next Steps

Now that you have Cosdata up and running, you can:

- Explore the [API Reference](/api/overview/) to learn how to interact with Cosdata
- Learn about [Search Relevance](/features/search-relevance/) features
- Discover how to optimize [Performance](/features/performance/)
- Try out the [Cos Graph Query Language](/api/cosquery/) for complex queries
- Use the [Python SDK](/api/python-sdk/) to build applications with Cosdata
- Contribute to the project on <a href="https://github.com/cosdata/cosdata" target="_blank" rel="noopener noreferrer">GitHub</a>
- Join our <a href="https://discord.gg/XMdtTBrtKT" target="_blank" rel="noopener noreferrer">Discord community</a> to connect with other Cosdata users 