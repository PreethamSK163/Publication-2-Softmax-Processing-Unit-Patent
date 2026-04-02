# Patent - Low-Power High-Performance Fixed-Point Softmax Processing Unit with LUT-Based Exponent and Mitchell Logarithmic Divider

> A fully fixed-point hardware architecture for the Softmax activation function — featuring dual-mode LUT-based exponentiation and an 8-stage pipelined Mitchell logarithmic division unit — designed for deployment in Deep Neural Network inference accelerators.

![Status](https://img.shields.io/badge/Status-Patent%20Under%20Review-yellow)
![Type](https://img.shields.io/badge/Type-Indian%20Patent-blue)
![Tool](https://img.shields.io/badge/Tool-Cadence%20Genus-blue)
![Tool](https://img.shields.io/badge/Tool-Cadence%20Innovus-blue)
![PDK](https://img.shields.io/badge/PDK-TSMC%20180nm-orange)
![Language](https://img.shields.io/badge/Language-Verilog%20HDL-purple)
![Application](https://img.shields.io/badge/Application-DNN%20Inference%20Accelerators-green)



## 📄 Patent Details

| Field | Details |
|:---|:---|
| **Title** | Low-Power High-Performance Fixed-Point Softmax Processing Unit with LUT-Based Exponent and Mitchell Logarithmic Divider |
| **Status** | Patent Application Under Review — Indian Patent Office |
| **Patent Attorney** | Khurana & Khurana, Advocates and IP Attorneys |
| **Field of Invention** | VLSI Design / AI Hardware Acceleration |
| **Applicant** | Vellore Institute of Technology, Chennai |



## 👥 Inventors

| Name | Affiliation |
|:---|:---|
| **Preetham SK** (First Inventor) | School of Electrical Engineering, VIT Chennai |
| **Sreehari R** (First Inventor) | School of Electrical Engineering, VIT Chennai |
| Dr. P. Sasipriya | Professor Grade 1, CNVD, School of Electronics Engineering, VIT Chennai |
| Dr. A. Anita Angeline | Professor, CNVD, School of Electronics Engineering, VIT Chennai |
| Prof. V. Anantha Krishnan | Assistant Professor, School of Electrical Engineering, VIT Chennai |


## 🔍 Overview

The Softmax activation function is a critical computational block in Deep Neural Networks — used in the output layer of CNNs, Transformers, and other multi-class classifiers to convert raw logits into a normalised probability distribution. Despite its mathematical simplicity, hardware implementation of Softmax is challenging due to the computational intensity of exponentiation and division operations, which are particularly demanding in fixed-point arithmetic where precision and dynamic range must be carefully managed.

This invention presents a **fully fixed-point hardware architecture** for the Softmax function, specifically designed for resource-constrained DNN inference accelerators. The architecture eliminates the need for floating-point computation and input normalisation while maintaining high classification accuracy — enabling efficient deployment in ASICs and edge AI systems.



## 🧠 Problem Addressed

Existing Softmax hardware implementations suffer from four key limitations:

**1. Hardware Complexity in Exponentiation:** Taylor series expansion and CORDIC-based methods require multiple iterative stages — resulting in high area, long delay, and increased power consumption impractical for real-time inference hardware.

**2. Precision-Area Tradeoff in LUT-Based Designs:** Coarse LUT quantisation introduces significant output error; fine-grained LUTs maintain accuracy but require impractically large memory structures for ASIC deployment.

**3. Sequential Division Bottleneck:** Conventional restoring/non-restoring array dividers require multiple clock cycles per division. For a Softmax layer with N inputs, total latency grows as O(N × cycles_per_division) — creating a severe throughput bottleneck for neural network inference pipelines.

**4. Input Normalisation Overhead:** Most existing designs require maximum-value subtraction before exponentiation — necessitating additional sorting hardware that increases system latency and area.



## 💡 Proposed Architecture

The Softmax Processing Unit comprises three coordinated hardware blocks controlled by a top-level Finite State Machine:

### 1. Exponent Block — Dual-Mode LUT-Based Design
Two architectural configurations are provided:

**Single-LUT Design** — Uses a 61-entry lookup table with linear interpolation over 1024 sub-steps per interval, operating on a 16-bit Q3.12 signed input. Achieves fine-grained precision without requiring a large table, producing a 32-bit Q14.18 fixed-point exponential output. Eliminates input normalisation entirely.

**Dual-LUT Design** — Uses two cascaded lookup tables: LUT1 with 16 entries at integer steps and LUT2 with 64 entries at 1/64 fractional steps. Integer and fractional components are retrieved independently and combined multiplicatively — achieving equivalent accuracy to a single large table through a compact two-table factorization approach.

Both configurations operate on Q3.12 format input (16-bit signed, 3 integer bits, 12 fractional bits) and produce Q14.18 format output (32-bit unsigned, 14 integer bits, 18 fractional bits).

### 2. Accumulator Block
A 36-bit Q22.14 format accumulator performs sequential addition of exponent outputs. The 36-bit width supports accumulation of up to N=239 worst-case exponent values before overflow, providing full support for DNN output layers with N up to 64 with significant headroom.

### 3. Division Block — 8-Stage Pipelined Mitchell Logarithmic Divider
The Mitchell binary logarithmic approximation algorithm is implemented as a fully pipelined 8-stage design:

- **Stage 1:** Leading Zero Detection (LZD) on dividend and divisor
- **Stage 2:** Operand normalisation and mantissa extraction
- **Stage 3:** Integer logarithm computation from LZD results
- **Stage 4:** Fractional mantissa extraction
- **Stage 5:** Log-domain subtraction (log(dividend) − log(divisor))
- **Stage 6:** Antilogarithm integer reconstruction
- **Stage 7:** Fractional antilogarithm reconstruction and output assembly
- **Stage 8:** Output clamping and Q1.15 formatting

The pipelined implementation achieves **one Softmax probability output per clock cycle** after pipeline fill — delivering approximately **8.9× throughput improvement** over a 16-cycle sequential restoring binary divider for N=10 inputs.


### Fixed-Point Datapath
Four carefully selected fixed-point formats ensure numerical stability across all computation stages:
- **Input:** 16-bit Q3.12 signed
- **Exponent output:** 32-bit Q14.18 unsigned
- **Accumulated sum:** 36-bit Q22.14 unsigned
- **Final probability output:** 16-bit Q1.15 unsigned



## ⚙️ Implementation

| Parameter | Details |
|:---|:---|
| **HDL** | Verilog HDL |
| **Functional Verification** | Cadence® NC Launch + Python reference model |
| **Logic Synthesis** | Cadence® Genus |
| **Physical Design** | Cadence® Innovus |
| **Technology Node** | TSMC 180nm CMOS |
| **Clock Frequency** | 100 MHz |
| **Input Format** | 16-bit Q3.12 signed fixed-point |
| **Output Format** | 16-bit Q1.15 unsigned fixed-point |
| **Pipeline Depth** | 8 stages (Division Block) |
| **Supported N** | 2 to 239 input classes |


## 📊 Key Results

| Metric | Single-LUT | Dual-LUT |
|:---|:---|:---|
| **Technology Node** | TSMC 180nm | TSMC 180nm |
| **Clock Frequency** | 100 MHz | 100 MHz |
| **Total Power** | 13.44 mW | 14.28 mW |
| **Throughput** | 1 output/cycle | 1 output/cycle |
| **Throughput vs Sequential Divider** | 8.9× improvement | 8.9× improvement |
| **MNIST Classification Accuracy** | 97.89% | 97.89% |
| **Hardware-Software Output Match** | 100% | 100% |
| **Validation Dataset** | 10,000 MNIST samples | 10,000 MNIST samples |

**Functional Verification:** Architecture validated against a Python floating-point reference model using 10,000 samples from the MNIST handwritten digit dataset — achieving 97.89% classification accuracy with zero misclassification between hardware and reference outputs across 100,000 probability values.


## 🎯 Applications

- **DNN Inference Accelerators** — Output layer Softmax for CNN and Transformer models
- **Edge AI and IoT** — Resource-constrained ASIC inference on wearable and embedded devices
- **Real-Time Image Classification** — Hardware Softmax for mobile vision applications
- **Natural Language Processing Hardware** — Attention score normalisation in Transformer accelerators
- **Medical Imaging** — Low-power hardware inference for diagnostic classification systems


## 🔗 Related Work

| Output | Details |
|:---|:---|
| **Patent** | Application under review — Indian Patent Office |
| **SCI Journal** | Manuscript in progress |
| **Research Supervisor** | Dr. P. Sasipriya, CNVD, VIT Chennai |
| **Research Centre** | Centre for Nanoelectronics and VLSI Design (CNVD), VIT Chennai |


## 🤝 Connect

| **Author** | Preetham SK |
|:---|:---|
| **Programme** | B.Tech. Electrical and Electronics Engineering, VIT Chennai |
| **LinkedIn** | [linkedin.com/in/preethamsk16](https://www.linkedin.com/in/preethamsk16) |
| **GitHub** | [github.com/PreethamSK163](https://github.com/PreethamSK163) |
| **Portfolio** | [preethamsk163.github.io](https://preethamsk163.github.io) |
| **Email** | preethamsk163@gmail.com |
