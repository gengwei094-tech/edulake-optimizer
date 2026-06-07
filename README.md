# edulake-optimizer
This repository contains the implementation of a deep reinforcement learning-based storage migration framework (DQN-SM) for Lakehouse architecture in educational information systems. The framework leverages multi-scale teaching-period-aware load modeling (MSTP-LM) and multi-dimensional fusion data value evaluation (MFD-VE) to enable intelligent, forward-looking data tiering decisions that align with the periodic workload patterns inherent in academic environments.

# Model Description

The DQN-SM framework integrates three core components:

1. **MSTP-LM (Multi-Scale Teaching-Period-Aware Load Modeling)** — Extracts and fuses multi-scale temporal features—including daily/weekly cycles, academic calendar events, and course schedule embeddings—via a cross-scale attention mechanism to accurately predict future storage workloads.

2. **MFD-VE (Multi-dimensional Fusion Data Value Evaluation)** — Assesses the future access probability of data objects by fusing access frequency, recency, instructional relevance, and relevance propagation through an adaptive weighting mechanism sensitive to instructional phases.

3. **DQN-SM (Deep Q-Network Storage Migration)** — Makes tiered storage migration decisions using a dual-stream architecture that decouples semantic and system information, trained with curriculum learning and domain-knowledge-guided reward shaping.

The framework is evaluated on a 4-node cluster with tiered storage (NVMe SSD / SATA SSD / HDD) and demonstrates substantial improvements over static and heuristic baselines, achieving a 71.1% reduction in average access latency and a 99.1% SLA compliance rate.
