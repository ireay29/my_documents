# ローカルLLM 推奨スペックガイド（2026年版）

ローカル環境で大規模言語モデル（LLM）を動作させるための推奨スペックを、主要なモデルおよびパラメータ規模ごとにまとめました。ローカルLLMの実行において最も重要なハードウェアリソースは**VRAM（ビデオメモリ）**であり、モデルのパラメータ数と量子化（Quantization）の度合いによって必要な容量が大きく変動します [1] [2]。

## 1. VRAM要件の基本原則

ローカルLLMを動作させるためのVRAM要件は、以下の計算式が目安となります。

* **FP16（16ビット精度）**: 10億（1B）パラメータあたり約2GBのVRAMが必要です [3]。
* **Q8（8ビット量子化）**: 10億（1B）パラメータあたり約1GBのVRAMが必要です [4]。
* **Q4（4ビット量子化）**: 10億（1B）パラメータあたり約0.6〜0.7GBのVRAMが必要です [4]。

これに加えて、コンテキストウィンドウ（入力および生成されるテキストの長さ）を保持するための**KVキャッシュ**として、通常10%〜20%の追加VRAMが必要となります [5]。Ollamaなどの一般的なローカルLLM実行ツールでは、デフォルトで4ビット量子化（Q4_K_Mなど）が採用されており、VRAM消費を大幅に抑えることができます [6]。

## 2. 主要モデル別 推奨スペック一覧

以下に、2026年現在主流となっている主要なオープンモデル（Llama、Mistral、Gemma、Phi、Qwen、DeepSeek）のパラメータ規模ごとの推奨VRAMおよびシステム要件をまとめます。以下の表は、一般的な4ビット量子化（Q4）および8ビット量子化（Q8）を前提とした目安です。

### Llama 3 / Llama 4 シリーズ (Meta)

MetaのLlamaシリーズは、ローカルLLMのデファクトスタンダードとして広く利用されています。

| モデル規模 | 代表的なモデル | 推奨VRAM (Q4) | 推奨VRAM (Q8/FP16) | 推奨GPUの目安 | 備考 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **8B** | Llama 3.1 8B | 6GB〜8GB | 8GB〜16GB | RTX 3060 / 4060 (8GB) | 一般的なゲーミングPCで快適に動作 [7] |
| **17B (MoE)** | Llama 4 Scout | 12GB〜16GB | 24GB〜 | RTX 4070 Ti / 4080 (16GB) | 109Bの総パラメータのうち17Bがアクティブ [8] |
| **70B** | Llama 3.3 70B | 35GB〜42GB | 70GB〜140GB | RTX 3090/4090 x2 (48GB) | 複数GPU、またはMac Studio (64GB以上) が必要 [9] |
| **405B** | Llama 3.1 405B | 230GB〜 | 400GB〜800GB | サーバー級GPU x8 | 個人環境での実行は極めて困難 [10] |

### Qwen 2.5 / Qwen 3 シリーズ (Alibaba)

AlibabaのQwenシリーズは、軽量モデルから超大型モデルまで幅広いラインナップを持ち、特にコーディングや日本語性能に優れています。

| モデル規模 | 代表的なモデル | 推奨VRAM (Q4) | 推奨VRAM (Q8/FP16) | 推奨GPUの目安 | 備考 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **0.5B〜1.5B** | Qwen2.5 1.5B | 2GB〜4GB | 4GB〜6GB | ノートPC内蔵GPU / CPU | エッジデバイスでも動作可能 [11] |
| **7B / 8B** | Qwen3 8B | 6GB〜8GB | 8GB〜16GB | RTX 3060 / 4060 (8GB) | 標準的なローカル用途に最適 [12] |
| **14B** | Qwen2.5 14B | 10GB〜12GB | 16GB〜28GB | RTX 4070 (12GB) | 性能とリソースのバランスが良い [13] |
| **32B** | Qwen2.5 32B | 20GB〜24GB | 32GB〜64GB | RTX 3090 / 4090 (24GB) | ハイエンドGPU 1枚で動作する限界ライン [14] |
| **72B** | Qwen3 72B | 40GB〜48GB | 72GB〜144GB | RTX 3090/4090 x2 (48GB) | 複数GPU環境が必要 [15] |

### Mistral / Mixtral シリーズ (Mistral AI)

Mistral AIのモデルは、効率的なアーキテクチャにより、比較的小さなパラメータ数で高い性能を発揮します。

| モデル規模 | 代表的なモデル | 推奨VRAM (Q4) | 推奨VRAM (Q8/FP16) | 推奨GPUの目安 | 備考 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **7B** | Mistral 7B | 5GB〜6GB | 8GB〜15GB | RTX 3060 / 4060 (8GB) | 非常に軽量で高速 [16] |
| **24B** | Mistral Small 3 | 16GB〜20GB | 24GB〜48GB | RTX 4080 (16GB) / 4090 | 70Bクラスに匹敵する性能を24GB VRAMで実現 [17] |
| **47B (MoE)** | Mixtral 8x7B | 24GB〜28GB | 48GB〜90GB | RTX 3090 / 4090 (24GB) | MoEアーキテクチャにより推論は高速 [18] |

### Gemma シリーズ (Google)

GoogleのGemmaシリーズは、Geminiと同じ技術基盤を持つオープンモデルです。

| モデル規模 | 代表的なモデル | 推奨VRAM (Q4) | 推奨VRAM (Q8/FP16) | 推奨GPUの目安 | 備考 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **2B / 4B** | Gemma 4 4B | 4GB〜6GB | 6GB〜10GB | RTX 3050 / 4060 (8GB) | 軽量で扱いやすい [19] |
| **9B / 12B** | Gemma 3 12B | 8GB〜10GB | 12GB〜24GB | RTX 4060 Ti (16GB) | 16GB VRAM環境に最適 [20] |
| **27B** | Gemma 4 27B | 18GB〜20GB | 28GB〜54GB | RTX 3090 / 4090 (24GB) | ハイエンドGPU 1枚でQ4動作が可能 [21] |

### Phi シリーズ (Microsoft)

MicrosoftのPhiシリーズは、高品質な学習データを用いることで、小規模ながら高い推論能力（Reasoning）を持ちます。

| モデル規模 | 代表的なモデル | 推奨VRAM (Q4) | 推奨VRAM (Q8/FP16) | 推奨GPUの目安 | 備考 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **3.8B** | Phi-3 Mini | 3GB〜4GB | 6GB〜8GB | ノートPC内蔵GPU / 8GB GPU | 非常に軽量 [22] |
| **14B** | Phi-4 | 8GB〜10GB | 14GB〜28GB | RTX 4060 Ti / 4070 (12GB) | 高度な推論能力をミドルクラスGPUで実現 [23] |

### DeepSeek シリーズ (DeepSeek)

DeepSeek-R1などの推論特化モデルは、蒸留（Distill）された小規模モデルから、巨大なMoEモデルまで提供されています。

| モデル規模 | 代表的なモデル | 推奨VRAM (Q4) | 推奨VRAM (Q8/FP16) | 推奨GPUの目安 | 備考 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **7B / 8B** | R1-Distill-Qwen-7B | 6GB〜8GB | 8GB〜16GB | RTX 3060 / 4060 (8GB) | 軽量な推論モデル [24] |
| **14B** | R1-Distill-Qwen-14B | 10GB〜12GB | 16GB〜28GB | RTX 4070 (12GB) | ミドルクラスGPU向け [25] |
| **32B** | R1-Distill-Qwen-32B | 20GB〜24GB | 32GB〜64GB | RTX 3090 / 4090 (24GB) | ハイエンドGPU 1枚向け [26] |
| **671B (MoE)** | DeepSeek-R1 | 150GB〜 | 350GB〜700GB | サーバー級GPU x4〜8 | 個人環境での実行は極めて困難 [27] |

## 3. ハードウェア選定のガイドライン

ローカルLLMを快適に動作させるためのハードウェア構成の目安は以下の通りです。

### エントリークラス (VRAM 8GB)
* **推奨GPU**: NVIDIA RTX 3060 12GB, RTX 4060 8GB
* **実行可能なモデル**: 7B〜8Bクラスのモデル（Llama 3.1 8B, Mistral 7B, Qwen3 8Bなど）をQ4量子化で快適に実行可能。
* **特徴**: 最もコストパフォーマンスが高く、ローカルLLMの入門に最適です。

### ミドルクラス (VRAM 12GB〜16GB)
* **推奨GPU**: NVIDIA RTX 4070 12GB, RTX 4060 Ti 16GB, RTX 4070 Ti Super 16GB
* **実行可能なモデル**: 12B〜14Bクラスのモデル（Phi-4, Qwen2.5 14B, Gemma 3 12Bなど）をQ4量子化で実行可能。8Bクラスであればコンテキストウィンドウを大きく取ることができます。
* **特徴**: 16GBのVRAMがあれば、最新の高性能な中規模モデルを余裕を持って動かすことができます。

### ハイエンドクラス (VRAM 24GB)
* **推奨GPU**: NVIDIA RTX 3090 24GB, RTX 4090 24GB
* **実行可能なモデル**: 24B〜32Bクラスのモデル（Mistral Small 3, Qwen2.5 32B, Gemma 4 27Bなど）をQ4量子化で実行可能。
* **特徴**: コンシューマー向けGPU単体で到達できる最高峰です。特に中古のRTX 3090は、VRAM容量あたりのコストパフォーマンスが高く、ローカルLLM愛好家に人気があります。

### ウルトラハイエンド / ワークステーション (VRAM 48GB以上)
* **推奨GPU**: NVIDIA RTX 3090/4090 x2 (48GB), RTX 6000 Ada (48GB)
* **実行可能なモデル**: 70Bクラスのモデル（Llama 3.3 70B, Qwen3 72Bなど）をQ4量子化で実行可能。
* **特徴**: 70Bクラスのモデルを動かすためには、複数のGPUを組み合わせるか、高価なワークステーション向けGPUが必要です。

### Apple Silicon Mac (M1/M2/M3/M4) について
Macの「ユニファイドメモリ」は、CPUとGPUでメモリを共有する仕組みのため、大容量のメモリを搭載したMac（64GBや128GBなど）であれば、VRAMとして大容量を割り当てることができ、70Bクラスの巨大なモデルでも単一のマシンで実行可能です [28]。ただし、メモリ帯域幅の制限により、ハイエンドNVIDIA GPUと比較するとトークン生成速度（推論速度）は劣る傾向にあります。

---

## References

[1] Local LLM Hardware Requirements in 2026 | AI Hub
[2] VRAM Requirements for AI 2026: Complete Guide by Model Size
[3] Information on VRAM usage of LLM model : r/LocalLLaMA - Reddit
[4] Model Quantization - Ollama - Mintlify
[5] GPU Memory Requirements for LLMs: VRAM Calculator - Spheron
[6] Can You Run This LLM? VRAM Calculator
[7] What's hardware requirements to run Llama 3.1 8b on a ... - Reddit
[8] Llama 4 Scout and Maverick Are Here—How Do They ... - RunPod
[9] Running Llama 3.3 70B on Your Home Server - LinkedIn
[10] Llama 3.1 - 405B, 70B & 8B with multilinguality and long ... - Hugging Face
[11] Qwen Models Guide: 600M to 1 Trillion Parameters
[12] Deploy Qwen 3 on GPU Cloud: Hardware Requirements ... - Spheron
[13] VRAM requirements for all Qwen3 models (0.6B–32B) - Reddit
[14] Comment your qwen coder 2.5 setup t/s here : r/LocalLLaMA - Reddit
[15] GPU Requirements Cheat Sheet 2026: Every Major AI Model - Spheron
[16] Mistral Models GPU Requirements: VRAM Guide for Local ...
[17] Mistral Small 3.1 - Mistral AI
[18] How to run Mistral 7B locally - GetDeploying
[19] Gemma 4 model overview | Google AI for Developers
[20] How to run Gemma 3 locally - GetDeploying
[21] What Is the Gemma 4 Mixture of Experts Architecture? - MindStudio
[22] Phi-4: Specifications and GPU VRAM Requirements
[23] Running Phi-4 Locally with Microsoft Foundry Local - Microsoft Tech Community
[24] DeepSeek-R1 on Consumer Hardware - SitePoint
[25] Can I run DeepSeek R1 Distill Qwen 14B on 12GB of vram ... - Reddit
[26] DeepSeek R1 Local Setup: Complete Guide to Running 671B Locally
[27] DeepSeek R1 VRAM Requirements: Too Heavy for Home GPUs? - Medium
[28] Best Local LLMs to Run On Every Apple Silicon Mac in 2026
