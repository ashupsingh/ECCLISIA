# AI-Powered Multilingual Voice Assistant for Digital Form Automation: Research & Architecture

**Problem Statement ID:** `ECCLISIA26-001`  
**Team Members:** Ashutosh Pratap Singh, Aarushi Jain, Anushka Saxena

---

## 1. Recommended System Architecture

To meet all technical requirements with high reliability and low latency, we propose a **modular pipeline**:

```
[Voice Input in 2+ Languages]
           │
           ▼
[Audio Preprocessing & VAD (Silero)]
           │
           ▼
[Multilingual ASR (IndicWhisper / Whisper-Large-v3-Turbo / SeamlessM4T)]
           │ (Transcribed Text + Language Tag)
           ▼
[Schema-Guided Slot Extraction & NLU (Llama-3.1 / Gemma-2 / Qwen-2.5 via Constrained JSON)]
           │ (Extracted Field Values)
           ▼
[Dialogue State Tracker & Rule-Based Validator (Pydantic / Regex)]
    ├── Missing / Invalid Slots ──► [Clarification Prompt Generator] ──► [TTS Spoken Follow-up]
    └── All Mandatory Slots Valid ──► [Interactive Review UI & TTS Readout] ──► [Submission]
```

### Why this approach?
- **Accuracy & Hallucination Control:** Unlike pure end-to-end SpeechLLMs that can hallucinate numbers and names, a modular pipeline separates acoustic transcription from deterministic schema validation.
- **Code-Switching Support:** Supports bilingual mixing (e.g., Hinglish / conversational local speech) common among non-native or bilingual users.
- **Low Latency:** Streaming ASR combined with lightweight structured generation enables real-time field auto-fill while the user speaks.

---

## 2. Recommended Models

| Stage | Model Name | Source / Provider | Key Advantage |
| :--- | :--- | :--- | :--- |
| **ASR (Speech-to-Text)** | **IndicWhisper** / **IndicConformer** | AI4Bharat (IIT Madras) | State-of-the-art accuracy for 12–22 Indian languages; robust to regional accents and Indian English. |
| | **Whisper-Large-v3-Turbo** (`faster-whisper`) | OpenAI / CTranslate2 | Global multilingual support (99+ languages), highly optimized for fast GPU/CPU inference. |
| | **SeamlessM4T v2** | Meta AI | Unified multilingual model supporting direct speech-to-text and cross-lingual translation. |
| **NLU & Field Extraction** | **Llama 3.1 8B / 3B Instruct** | Meta | Exceptional structured JSON extraction, multilingual reasoning, and slot tracking. |
| | **Qwen 2.5 7B / 3B Instruct** | Alibaba Cloud | Top-tier multi-language tokenization and JSON/Function calling capabilities. |
| | **Gemma 2 9B / 2B-IT** | Google DeepMind | Lightweight, fast inference, high parameter efficiency. |
| | **IndicBERT v2 / XLM-RoBERTa** | AI4Bharat / Meta | Ultra-fast token classification / NER model for CPU/edge deployment. |
| **TTS (Speech Synthesis)** | **Microsoft Edge-TTS** | Microsoft Neural | Free, high quality, natural regional accents across 40+ languages. |
| | **IndicTTS** | AI4Bharat | Open-source neural speech synthesis for Indian regional languages. |

---

## 3. Recommended Datasets

### A. Multilingual Speech Recognition
- **Kathbath & Kathbath 2.0 (AI4Bharat):** 1,684 hours of crowdsourced speech across 12 Indian languages and 203 districts.
- **Mozilla Common Voice:** 30,000+ hours of open multilingual audio across 110+ languages.
- **Google FLEURS:** 102 languages benchmark for speech classification and ASR.

### B. Task-Oriented Dialogue & Slot Filling
- **Amazon MASSIVE:** 1M parallel utterances across 51 languages, 18 domains, 60 intents, and 55 slot types. Gold standard for multilingual slot filling.
- **Google Schema-Guided Dialogue (SGD):** 20,000+ conversations over 16+ domains for zero-shot schema generalization.
- **MultiWOZ 2.4:** Standard benchmark for multi-domain dialogue state tracking and slot updates.

### C. Digital Forms & Document Layout
- **XFUND:** Multilingual form understanding and key-value pair linking across 7 languages.
- **FUNSD:** Form understanding in noisy, scanned documents.

---

## 4. Key Academic References & Research Papers

1. **AUDIVA: a Voice-Based Assistive Tool for Form Filling and Multilingual Interaction** (IEEE 2026)  
   *Combines Whisper ASR, LayoutLM, and neural TTS for an assistive, voice-driven form completion tool.*
2. **Slot Filling as a Reasoning Task for SpeechLLMs** (Kadri Hacioglu et al., Oct 2025) — [arXiv:2510.19326](https://arxiv.org/abs/2510.19326)  
   *Formulates slot filling as a step-by-step reasoning process using SpeechLLMs.*
3. **Zero-shot Slot Filling in the Age of LLMs for Dialogue Systems** (ACL / Nov 2024) — [arXiv:2411.18980](https://arxiv.org/abs/2411.18980)  
   *Techniques for slot extraction under conversational interruptions and corrections.*
4. **An Approach to Build Zero-Shot Slot-Filling System for Industry-Grade Conversational Assistants** (G P Shrivatsa Bhargav et al., Jun 2024) — [arXiv:2406.08848](https://arxiv.org/abs/2406.08848)  
   *Industry-grade low-latency slot filling using constrained LLMs.*
5. **IndicSUPERB & Kathbath: A Speech Processing Universal Performance Benchmark for Indian Languages** (Tahir Javed et al., 2022) — [arXiv:2208.11761](https://arxiv.org/abs/2208.11761)  
   *Benchmark and dataset release for 12 Indian languages.*
6. **Vistaar: Diverse Benchmarks and Training Sets for Indian Language ASR** (AI4Bharat, 2023) — [arXiv:2305.13721](https://arxiv.org/abs/2305.13721)  
   *Introduces IndicWhisper evaluated across 59 diverse Indian domains and accents.*
7. **MASSIVE: A 1M-Example Multilingual Natural Language Understanding Dataset with 51 Typologically-Diverse Languages** (Amazon Alexa AI, ACL 2022) — [arXiv:2204.08582](https://arxiv.org/abs/2204.08582)  
   *Massively multilingual dataset for intent classification and slot filling.*
8. **Towards Scalable Multi-Domain Conversational Agents: The Schema-Guided Dialogue Dataset** (Google AI, 2019) — [arXiv:1909.05855](https://arxiv.org/abs/1909.05855)  
   *Dynamic zero-shot schema-guided conversational agent benchmark.*
