# OpenClaw エコシステム 類似OSSプロダクト調査レポート

> 調査日: 2026年3月25日

---

## 1. OpenClaw とは

**OpenClaw**（旧称: Clawdbot → Moltbot）は、Peter Steinberger（PSPDFKit創業者）が2025年11月に個人プロジェクトとして開発を開始した、セルフホスト型のオープンソースAIエージェントフレームワーク。2026年1月30日に最終的に「OpenClaw」へリブランドされ、GitHub上で爆発的な成長を遂げた（現在 **324,000+ スター**、GitHubの歴史上最速クラスの成長）。

**コアコンセプト**: 自分のハードウェア上で動作し、WhatsApp・Telegram・Slack・Discord等、すでに使っているチャットアプリを通じてAIエージェントを操作する。

### 名称変遷

| 日付 | 名称 | 理由 |
|------|------|------|
| 2025年11月 | **Clawdbot** | 初期リリース名 |
| 2026年1月27日 | **Moltbot** | Anthropic商標クレーム（"Claude"との類似）によりリブランド |
| 2026年1月30日 | **OpenClaw** | 最終リブランド、コミュニティ投票で決定 |

### 基本情報

| 項目 | 詳細 |
|------|------|
| GitHub | [openclaw/openclaw](https://github.com/openclaw/openclaw) |
| スター数 | 324,000+ |
| 言語 | TypeScript (Node.js 22+) |
| ライセンス | MIT |
| 対応チャンネル | WhatsApp, Telegram, Slack, Discord, Signal, iMessage, Google Chat, Teams, Matrix, LINE, Feishu 等20+ |
| スキルマーケット | ClawHub（13,700+ AgentSkills） |
| 主な特徴 | SOUL.md によるエージェント設定、ブラウザ自動化、cron、マルチエージェント、音声対応 |

### コアアーキテクチャ（6レイヤー）

| レイヤー | 役割 |
|----------|------|
| **Gateway** | 中央コントロールプレーン — メッセージルーティング、セッション、プラグイン、ツール実行ポリシー |
| **Channels** | Telegram/WhatsApp/Discord/iMessage 等を標準メッセージ形式に正規化するアダプタ |
| **Routing + Sessions** | どのエージェントが特定の会話を処理するかを決定 |
| **Agent Runtime** | コンテキスト処理、モデルプロバイダ呼び出し、レスポンスストリーミング、ツールリクエスト |
| **Tools** | 機能群 — Webフェッチ、ブラウザ制御、コマンド実行、デバイスペアリング |
| **Surfaces** | インタラクションポイント — チャットアプリ、Webダッシュボード、macOSメニューバー、Live Canvas |

---

## 2. 類似・派生OSSプロダクト 一覧

OpenClawが公開されてから約6週間で、多数の類似プロダクトが誕生した。それぞれが OpenClaw の異なる弱点（セキュリティ・パフォーマンス・コスト・複雑さ）を攻略点としている。

### 主要プロダクト比較表

| プロダクト | スター数 | 言語 | バイナリサイズ | RAM使用量 | 起動時間 | 主な差別化ポイント |
|---|---|---|---|---|---|---|
| **OpenClaw** | ~324K | TypeScript | ~200MB+ | 数百MB | 数秒 | 最大エコシステム、13,700+スキル |
| **NanoClaw** | ~21,500 | TypeScript | 小（コンテナ化） | 可変 | 中程度 | 700行、必須コンテナ隔離、監査ログ |
| **ZeroClaw** | ~26,200 | Rust | 3.4MB | <5MB | <10ms | $10ハードウェアで動作、22+LLMプロバイダ |
| **Moltis** | ~2,000 | Rust | 単一バイナリ | 中程度 | 高速 | 150K行・unsafe 0、音声I/O、Prometheus対応 |
| **PicoClaw** | ~13,300 | Go | 単一バイナリ | <10MB | 1秒 | $10 RISC-Vボードで動作、1日で開発 |
| **Nanobot** | ~26,800 | Python | N/A | ~45MB | 中程度 | 4,000行、香港大学発、MCP-native |
| **NullClaw** | 少数 | Zig | 678KB | ~1MB | <2ms | 究極のミニマリズム |
| **MicroClaw** | 少数 | Rust | 単一バイナリ | 低 | 高速 | 14+プラットフォームアダプタ（Feishu, DingTalk, QQ等） |
| **IronClaw** | ~1,300 | Rust | WASMモジュール | 低 | 高速 | WASM+暗号検証、NEAR AI製 |
| **TinyClaw** | ~1,300 | Shell | ~400行 | 低 | 高速 | Claude Code + tmux、自己修復、WhatsApp対応 |
| **MimiClaw** | ~4,600 | C | N/A | 極小 | 即時 | $5 ESP32-S3チップで動作、純粋C言語 |
| **zclaw** | 少数 | C | 888KB以下 | 極小 | 即時 | ESP32マイコン向け、暗号化クレデンシャル保存 |
| **Autobot** | 少数 | Crystal | 2MB | ~5MB | <10ms | カーネルレベルサンドボックス |
| **Mini-Claw** | ~72 | TypeScript | N/A | 低 | 高速 | API費用ゼロ（Claude Pro/Max or ChatGPT Plus直接利用） |

---

## 3. 各プロダクト詳細

### NanoClaw

**リポジトリ**: [qwibitai/nanoclaw](https://github.com/qwibitai/nanoclaw)

OpenClaw の翌日（2026/1/31）にリリース。「500,000行のコードは誰も監査できない」という問題意識から、**わずか700行のTypeScript**で同等機能を実現。Anthropic Agents SDK ベースで、全エージェントが Apple Containers（macOS）または Docker（Linux）内で動作し、コンテナ脱出不可能なアーキテクチャ。VentureBeat に「OpenClaw のコンテナ隔離問題を解決した」として取り上げられた。

**主な特徴:**
- 必須コンテナ隔離（Apple Containers / Docker）
- 全ファイルシステム操作・ネットワークリクエストに明示的承認が必要
- 組み込み監査ログ（タイムスタンプ・入出力ペイロード・権限決定を記録）
- エージェントスウォーム（複数エージェントの協調）をエコシステム初で実装

### ZeroClaw

**リポジトリ**: [zeroclaw-labs/zeroclaw](https://github.com/zeroclaw-labs/zeroclaw)

Harvard・MIT学生とSundai.Clubコミュニティが開発。**Rustで書かれた3.4MBの単一バイナリ**が10ms以内に起動し、RAM使用量は5MB以下。$10のシングルボードコンピュータで動作可能。

**動作モード:**
- **Agent mode（CLI）**: 単一エージェントをCLIから実行
- **Gateway mode（HTTP）**: エージェントをHTTPエンドポイントとして公開
- **Daemon mode**: フルプロダクション展開（24/7稼働）

**パフォーマンス比較:**

| 指標 | OpenClaw | ZeroClaw |
|------|----------|----------|
| バイナリサイズ | ~200MB+ | 3.4MB |
| 起動時間 | 数秒 | <10ms |
| RAM使用量 | 数百MB | <5MB |
| 最小ハードウェア | Mac mini（~$500+） | $10ボード |

### Moltis

**リポジトリ**: [moltis-org/moltis](https://github.com/moltis-org/moltis)

Fabien Penso 作。**150,000行のRust**（unsafe ブロック0件）で書かれたエンタープライズ向けフレームワーク。2,300+テスト、Prometheus/OpenTelemetry対応、8 TTS + 7 STT プロバイダによる音声I/O、MCP（stdio・HTTP/SSE）対応、15のライフサイクルフックを持つ。週複数回のリリースを維持する高い開発速度が特徴。

### PicoClaw

**リポジトリ**: [sipeed/picoclaw](https://github.com/sipeed/picoclaw)

組み込みハードウェアメーカーのSipeedが**1日で開発**。Go製の単一バイナリで、$10のRISC-Vボード（Sipeed LicheeRV Nano）上でRAM 10MB以下で動作。コードベースの95%はAI生成。

### Nanobot

**リポジトリ**: [HKUDS/nanobot](https://github.com/HKUDS/nanobot)

香港大学コンピュータサイエンス学科発。**Pythonで約4,000行**。データサイエンティストやAI研究者がTypeScript/Rustへの文脈切り替えなしに使えるよう設計。MCP-nativeで最新研究手法の採用が早い。

### TinyClaw

**リポジトリ**: [warengonzaga/tinyclaw](https://github.com/warengonzaga/tinyclaw)

**約400行のシェルスクリプト**で実装。Claude Code + tmux の組み合わせで動作し、WhatsApp対応、ハートビート監視、cron、自己修復機能を持つ。「OpenClaw の縮小版ではなく、完全に独立したプロダクト」と明示している。

### MimiClaw

**リポジトリ**: [memovai/mimiclaw](https://github.com/memovai/mimiclaw)

**$5のESP32-S3ボード**上で動作する純粋C言語実装。Linux・Node.js・サーバーインフラ不要。USB電源とWiFiがあれば、Telegram経由でAIアシスタントとして機能する。GPIO・センサー・アクチュエータの制御も可能。

**動作要件:**
- チップ: ESP32-S3 必須
- Flash: 16MB 以上
- PSRAM: 8MB 以上

### zclaw

**リポジトリ**: [tnm/zclaw](https://github.com/tnm/zclaw)

MimiClaw と近いコンセプトの別プロジェクト。**888KB以下のファームウェア**でESP32マイコン上で動作。タイムゾーン対応スケジューラ、GPIO制御、暗号化クレデンシャル保存に対応。Telegram または Webリレー経由で操作。

**対応ボード:** ESP32-C3, ESP32-S3, ESP32-C6（他のESP32バリアントも設定変更で対応可能）

### Mini-Claw（MiniClaw）

**リポジトリ**: [htlin222/mini-claw](https://github.com/htlin222/mini-claw)

**「API費用ゼロ」**が最大の特徴。Claude Pro/Max または ChatGPT Plus のサブスクリプションを直接Telegramで利用できる軽量Telegramボット。バックエンドには Pi coding agent を使用し、セッションの永続化・自動圧縮、ファイル添付、レート制限、アクセス制御を実装。

---

## 4. 超軽量・組み込みハードウェア対応プロダクト

### マイコン（ESP32）で動くもの

| プロダクト | ハードウェア | 言語 | ファームウェアサイズ | 特徴 |
|---|---|---|---|---|
| **MimiClaw** | ESP32-S3（$5） | C | N/A | 純粋C、OSなし・Node.jsなし。USB電源+WiFiで動作。Telegram経由で操作。GPIO/センサー制御対応 |
| **zclaw** | ESP32-C3/S3/C6（$5前後） | C | 888KB以下 | タイムゾーン対応スケジューラ、GPIO制御、暗号化クレデンシャル保存。Telegram or Webリレー経由で操作 |

### SBC（Raspberry Pi クラス）で動くもの

| プロダクト | 対応ハードウェア | 言語 | RAM使用量 | 起動時間 |
|---|---|---|---|---|
| **PicoClaw** | RISC-V SBC（$10）、Raspberry Pi Zero クラス、ARM64、x86 | Go | <10MB | 約1秒 |
| **ZeroClaw** | $10 SBC、Raspberry Pi 等 | Rust | <5MB | <10ms |
| **NullClaw** | $5ボード全般（静的バイナリ） | Zig | ~1MB | <2ms |
| **Autobot** | 低スペックLinuxマシン全般 | Crystal | ~5MB | <10ms |
| **NanoBot** | Raspberry Pi 等（Python動作環境があれば） | Python | ~45MB | 中程度 |

---

## 5. M5Stack での動作可否

### MimiClaw / zclaw の動作要件

| プロダクト | チップ要件 | Flash | PSRAM |
|---|---|---|---|
| **MimiClaw** | ESP32-S3 必須 | 16MB 以上 | 8MB 以上 |
| **zclaw** | ESP32-C3/S3/C6（無印も可） | 4MB 以上 | 必須ではない |

### M5Stack 主要機種との対応表

| 機種 | チップ | Flash | PSRAM | MimiClaw | zclaw |
|------|--------|-------|-------|----------|-------|
| **Core S3** | ESP32-S3 | 16MB | 8MB | **◯** | **◯** |
| **Core2 / Core2 for AWS** | ESP32（無印） | 16MB | 8MB | **×**（S3必須） | **△**（要確認） |
| **Basic v2.6** | ESP32（無印） | 16MB | なし | **×** | **△** |
| **Fire v2.7** | ESP32（無印） | 16MB | 8MB | **×** | **△** |
| **Tough** | ESP32（無印） | 16MB | 8MB | **×** | **△** |
| **StampS3** | ESP32-S3 | 8MB | 8MB | **×**（Flash不足） | **◯** |

**注意点:** M5Stack の場合、LCD や電源 IC（AXP192 等）の初期化コードが必要になるケースがあるため、そのまま flash するだけでは動かない可能性がある。zclaw を Core2 等で試す場合は、ESP-IDF のターゲット設定変更とペリフェラル初期化の対応が必要。

---

## 6. 用途別選択ガイド

| 目的 | 推奨プロダクト |
|------|---------------|
| 最大のエコシステムと機能が欲しい | **OpenClaw** |
| セキュリティ・コンプライアンスが最優先 | **NanoClaw** |
| 低スペックハードウェア・エッジ展開 | **ZeroClaw** / **PicoClaw** |
| エンタープライズ・音声I/O・可観測性 | **Moltis** |
| Pythonエコシステムで完結させたい | **Nanobot** |
| API費用をゼロにしたい（サブスク活用） | **Mini-Claw** |
| $5のESP32マイコンで動かしたい | **MimiClaw** / **zclaw** |
| 究極のミニマリズム | **NullClaw** |
| アジア系プラットフォーム（Feishu等）対応 | **MicroClaw** |

---

## 7. 参考リソース

- [awesome-openclaw（rohitg00）](https://github.com/rohitg00/awesome-openclaw) — エコシステム全体の包括的なキュレーションリスト
- [OpenClaw Alternatives Compared (AI Magicx)](https://www.aimagicx.com/blog/openclaw-alternatives-comparison-2026)
- [OpenClaw Alternatives for Raspberry Pi (It's FOSS)](https://itsfoss.com/openclaw-alternatives/)
