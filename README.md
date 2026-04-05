<div align="center">

# 🧠 Brain-to-Text: NP-MST
### Neuro-Phonetic Multi-Scale Transformer (NP-MST)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/release/python-3100/)
[![PyTorch 2.1+](https://img.shields.io/badge/PyTorch-2.1+-red.svg)](https://pytorch.org/)

**Neuro-Phonetic Multi-Scale Transformer (NP-MST) for direct neural linguistic decoding.**

</div>

## 🚀 Overview
The **NP-MST** is a high-performance system for decoding speech directly from neural spiking activity. This project addresses the challenge of translating high-dimensional neural signals (256+ channels) into coherent linguistic text in real-time. By leveraging a multi-scale temporal transformer architecture and phonetic prior distillation, NP-MST achieves robust decoding even in scenarios with impaired physical articulation.

## ✨ Key Features
- **Spatio-Temporal Feature Extractor**: Parallel 1D-convolutional layers (kernels 3, 5, 7) for multi-scale temporal dependency capture.
- **Phonetic Prior Distillation**: Predicts International Phonetic Alphabet (IPA) tokens to bridge the gap between brain signals and orthography.
- **Hybrid Loss Function**: Combines CTC loss for alignment with Label-Smoothed Cross-Entropy for text synthesis.
- **LLM-Enhanced Inference**: Integrated 5-gram KenLM language model rescoring for semantic coherence.

## 🏗️ Architecture Detail
### Stage 1: Neural Manifold Projection
Neural spiking data is binned into 20ms windows, variance-stabilized via square-root transformation, and z-score normalized. The convolutional backbone projects this data into a low-dimensional neural manifold.

### Stage 2: Phonetic Alignment & Synthesis
A 6-layer Transformer Encoder processes the manifold, feeding into a dual-headed decoder. The **CTC Head** provides chronological phonetic bias, while the **Cross-Attention Decoder** synthesizes final text strings.

## 🛠️ System Requirements
- **Hardware**: NVIDIA GPU (RTX 4090 / A100+) with 24GB+ VRAM.
- **Software**: Python 3.10+, PyTorch 2.1+, Hugging Face `transformers`, `tokenizers`, `KenLM`.

## 📜 Claims & Innovation
NP-MST introduces a recursive adaptation engine that calculates linguistic confidence metrics to dynamically update adaptive projection parameters in real-time, minimizing divergence between neural activity and linguistic intent.

---
**Author**: M. Keith Rapoport
**License**: MIT
