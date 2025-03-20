---
title: Python SDK
description: How to use the Cosdata Python SDK to interact with the vector database
---

The Cosdata Python SDK provides a convenient way to interact with the Cosdata vector database from Python applications. This guide will help you get started with the SDK and show you how to perform common operations.

## Installation

You can install the Cosdata Python SDK directly from GitHub:

```bash
# Clone the repository
git clone https://github.com/cosdata/cosdata-sdk-python.git

# Install the library
cd cosdata-sdk-python
pip install -e .
```

## Basic Usage

### Connecting to Cosdata

First, import the Cosdata client and establish a connection:

```python
from cosdata import CosdataClient

# Initialize the client with your server details
client = Client(
    host = "http://127.0.0.1:8443",
)

# Full parameter client creation
client = Client(
    host = "http://127.0.0.1:8443",
    username = "admin",
    password = "admin",
    verify = False  # Whether to verify SSL certificates
)
```

### Creating a Collection

Create a new vector collection:

```python
# Create a collection for storing 3-dimensional vectors
collection = client.create_collection(
    name="documents",
    description="Collection for document embeddings",
    dimension=3  # Dimensionality of vectors to be stored
)
```

### Listing Collections

Retrieve a list of all collections:

```python
# Retrieve a list of all collections in the vector database
collections = client.list_collections()
```

### Getting a Collection

Retrieve an existing collection by name:

```python
# Get an existing collection by name
collection = client.get_collection(collection_name="documents")
```

### Getting Collection Information

Retrieve metadata and details about a collection:

```python
# Get information about an existing collection
info = collection.get_info()
```

### Iterating Over Collections

Iterate over all available collections:

```python
# Iterate over all collections in the vector database
for collection in client.collections():
    print(collection.get_info)
```

### Creating an Index

Create an index for efficient vector search:

```python
# Create a simple index
index = collection.create_index(
    distance_metric="cosine"
)

# Create an index with specified parameters
index = collection.create_index(
    distance_metric="cosine",      # Similarity metric for comparisons
    num_layers=7,                  # Number of layers in the index
    max_cache_size=1000,           # Maximum cache size for indexing
    ef_construction=512,           # Size of dynamic list during index construction
    ef_search=256,                 # Search depth for nearest neighbors
    neighbors_count=32,            # Number of neighbors per layer
    level_0_neighbors_count=64     # Number of neighbors at the base level
)
```

### Adding Vectors

Add vectors to your collection:

```python
#  Single and Batch insertion method is similar
vectors = [
    {
        "id": "doc2",
        "values": [0.2, 0.3, 0.4],
    },
    {
        "id": "doc3",
        "values": [0.3, 0.4, 0.5],
    }
]

index = collection.create_index(
    distance_metric="cosine" 
)

# Upsert all vectors in a single transaction (SDK will handle batching)
with index.transaction() as txn: 
    txn.upsert(vectors)  

```

### Searching Vectors

Perform similarity search:

```python
# Search for similar vectors
results = index.query(
    vector=[0.1, 0.2, 0.3, ...],
    nn_count=2
)

# Search for vector by id
results = index.fetch_vector(
    vector_id=1  # Vector id can be string or int
) 

# Print results
for result in results:
    print(f"Query result: {results}")
```

### Using Transactions

Perform operations within a transaction:

```python
# Start a transaction
transaction = client.get_collection("documents").create_index(distance_metric="cosine").transaction()

try:
     # Upsert multiple vectors within the transaction
    vectors = [
        {"id": "doc2","values": [0.2, 0.3, 0.4]},
        {"id": "doc2","values": [0.3, 0.4, 0.5]}
    ]
    
    transaction.upsert(vectors)
    
    # Commit the transaction (save changes)
    transaction.commit()
    print("Transaction committed successfully.")
except Exception as e:
    # Abort the transaction on error (revert changes)
    transaction.abort()
    print(f"Transaction aborted due to error: {e}")
```

## Best Practices

### Connection Management

- Reuse client instances when possible
- Implement connection pooling for high-throughput applications
- Handle connection errors with appropriate retry logic

### Vector Operations

- Normalize vectors before insertion
- Use batch operations for better performance
- Keep vector dimensions consistent

### Transaction Management

- Keep transactions short-lived
- Always commit or abort transactions
- Implement proper error handling and retry logic

### Performance Optimization

- Use appropriate batch sizes (100-1000 vectors)
- Create indexes for frequently searched collections
- Monitor and optimize query performance

## Reference

For more detailed information, refer to:
- [REST API Documentation](/api/documentation/)
- [cosQuery Language](/api/cosquery/)
- [GitHub Repository](https://github.com/cosdata/cosdata-sdk-python) 