# FafnirDT: Dynamic Timestamp in Lock-based Concurrency Control Protocols

## 🧠 Overview

**FafnirDT** extends the **2PLSF (Two-Phase Locking with Starvation Freedom)** protocol to fix a critical scalability flaw in lock-based concurrency:

> When all available threads are occupied by long transactions, 2PLSF can enter a state of **zero throughput** — no new transactions can be scheduled.

FafnirDT introduces:
- A **Dynamic Timestamp Tracker**
- **Elastic arrays** for announce timestamps and read indicators
- A dedicated **monitoring thread** that injects new thread slots dynamically when delays are detected

✅ Preserves serializability  
✅ Maintains starvation freedom  
✅ Recovers throughput under pathological workloads

---

## 💡 Motivation

**Problem:** 2PLSF uses a fixed-size metadata array tied to CPU core count. If all threads are running long transactions, short transactions must wait — resulting in **0 TPS** even when they could execute.

**FafnirDT** solves this by:
- Monitoring global delay using a timer
- Dynamically expanding metadata arrays
- Injecting additional threads into the protocol, bypassing lockout

---

## 🔬 Experimental Setup

- **Architecture**: x86_64  
- **CPUs**: 2× Intel Xeon Gold 6130 @ 2.10GHz  
- **Cores**: 32 physical (64 logical)  
- **Memory**: 512 GB DRAM  
- **Benchmark Tool**: CCBench  
- **Workload**: YCSB (100% writes)  
- **Dataset Size**: 10,000 tuples

---

## 📈 Key Results

| Scenario                                      | 2PLSF Throughput         | FafnirDT Throughput       | Notes                                                      |
|----------------------------------------------|---------------------------|----------------------------|------------------------------------------------------------|
| **Normal Workload** (short-only txns)        | 1.000x                    | **0.9535x**                | FafnirDT incurs ~4.65% overhead vs. 2PLSF                   |
| **Delayed Threads** (10 sec long txns)       | **0 TPS**                 | **~3,700 TPS**             | 2PLSF starves; FafnirDT sustains execution                 |
| **Thread Scaling (8 → 64)** under delay      | N/A (0 TPS base)          | **~6,100 TPS → ~3,700 TPS** | FafnirDT degrades ~39.45%, but throughput remains >0 TPS  |

✅ FafnirDT eliminates starvation  
✅ Graceful degradation when scaling under contention  
✅ Minimal overhead in low-contention scenarios

---

## 📂 Usage Instructions

Usage instructions are provided in the main CCBench README at:  
https://github.com/Ryuichi-Yamafuji-Lun/ccbench

---

## ✅ TL;DR

**FafnirDT** is a systems-level enhancement to 2PLSF that dynamically injects threads and timestamps to restore throughput when all original threads are blocked by long transactions. It demonstrates:  
- Performance where 2PLSF yields zero  
- Elastic scalability on multicore systems  
- Minimal trade-offs under normal workloads

---

## 📬 Contact

- **Email**: ryuichi.y.lun@gmail.com  
- **LinkedIn**: [linkedin.com/in/ryulun](https://www.linkedin.com/in/ryulun/)
