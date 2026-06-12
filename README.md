# Zero-Copy IPC Performance Benchmark using Apache Arrow

## Overview

This project demonstrates and benchmarks a **Zero-Copy Inter-Process Communication (IPC)** architecture using **Apache Arrow**, **POSIX Shared Memory**, and **PyTorch**. The objective is to compare traditional JSON/REST-based communication against a high-performance zero-copy pipeline and quantify improvements in latency, CPU utilization, and memory efficiency.

The system consists of two microservices:

* **Producer Service** – Generates numerical data (Float32 arrays simulating embeddings/tensors).
* **Consumer Service** – Reads the data and converts it into PyTorch tensors for downstream processing.

An interactive **Streamlit dashboard** provides real-time visualization of performance metrics and benchmark results.

---

## Problem Statement

Modern AI, Machine Learning, and data-intensive applications frequently exchange large volumes of structured data between services.

Traditional communication methods typically involve:

1. Serializing data to JSON.
2. Transmitting via HTTP.
3. Deserializing on the receiving side.
4. Creating multiple memory copies during processing.

These operations introduce:

* Higher latency
* Increased CPU utilization
* Excessive memory consumption
* Reduced scalability

---

## Solution

This project implements a **Zero-Copy IPC pipeline** using Apache Arrow and shared memory.

Instead of converting data into JSON and sending it across the network, both services directly access the same memory region, eliminating unnecessary serialization and memory copies.

### Key Benefits

* Lower latency
* Reduced memory footprint
* Lower CPU overhead
* Faster PyTorch tensor creation
* Better scalability for AI/ML workloads

---

## Architecture

### Traditional JSON/REST Pipeline

```text
Producer
   │
   ▼
JSON Serialization
   │
   ▼
HTTP Transfer
   │
   ▼
JSON Deserialization
   │
   ▼
NumPy Array Creation
   │
   ▼
PyTorch Tensor
```

### Zero-Copy Apache Arrow Pipeline

```text
Producer
   │
   ▼
Apache Arrow RecordBatch
   │
   ▼
Shared Memory (/dev/shm)
   │
   ▼
Arrow Zero-Copy Read
   │
   ▼
NumPy View (No Copy)
   │
   ▼
PyTorch Tensor (No Copy)
```

---

## Technology Stack

### Core Technologies

* Python 3.x
* FastAPI
* Streamlit

### Data Processing

* Apache Arrow (PyArrow)
* NumPy
* PyTorch

### Inter-Process Communication

* POSIX Shared Memory (`/dev/shm`)
* Arrow IPC RecordBatch
* FileLock Synchronization

### Containerization

* Docker
* Docker Compose

### Visualization

* Streamlit Dashboard
* Performance Benchmark Charts

---

## How Zero-Copy Works

1. The Producer generates Float32 arrays.
2. Data is written directly as an Apache Arrow RecordBatch into shared memory.
3. The Consumer accesses the same memory region.
4. Arrow exposes the data as a NumPy array using:

```python
column.to_numpy(zero_copy_only=True)
```

5. NumPy memory is mapped directly into PyTorch:

```python
torch.from_numpy(array)
```

No intermediate copies are created throughout the process.

---

## Benchmark Metrics

The dashboard measures:

* Serialization Time
* Transfer Time
* Deserialization Time
* Tensor Creation Time
* Total End-to-End Latency
* Memory Usage

---

## Project Structure

```text
.
├── producer/
│   ├── producer.py
│   └── requirements.txt
│
├── consumer/
│   ├── consumer.py
│   └── requirements.txt
│
├── dashboard/
│   ├── app.py
│   └── benchmark.py
│
├── docker-compose.yml
├── Dockerfile
└── README.md
```

## Results

The benchmark consistently demonstrates:

* Significant reduction in end-to-end latency
* Near-instant tensor availability
* Lower CPU utilization
* Reduced memory consumption
* Improved throughput for large datasets

---

## Future Improvements

* Apache Arrow Flight integration
* gRPC transport support
* Multi-host deployment
* Distributed benchmarking
* GPU Direct Memory integration
* Real-world ML inference workloads

---

## Applications

This architecture is highly suitable for:

* AI/ML Pipelines
* Vector Databases
* Real-Time Analytics
* High-Frequency Trading Systems
* Recommendation Engines
* Data Engineering Platforms

---

## Conclusion

This project demonstrates how Apache Arrow and shared memory can eliminate serialization overhead and memory duplication, enabling high-performance communication between microservices. By adopting a zero-copy architecture, systems can achieve significantly lower latency and improved resource efficiency compared to traditional JSON/REST-based communication.
