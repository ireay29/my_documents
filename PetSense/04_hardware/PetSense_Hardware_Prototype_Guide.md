# PetSense 犬用ボード型デバイス ハードウェア プロトタイプ構築ガイド

**作成日**: 2026年3月26日  
**バージョン**: 1.0  
**対象**: エンジニア、ハードウェア開発者

---

## 1. プロトタイプ構築の全体戦略

### 1.1 フェーズ分け

PetSense のハードウェア プロトタイプ構築は、以下の3つのフェーズに分けて進行します。

**フェーズ1: 基本機能検証（0～4週間）**
- 感圧センサーの動作確認
- 振動センサーの信号取得
- マイコンの基本プログラミング
- Bluetooth 通信の確認

**フェーズ2: 統合テスト（4～8週間）**
- PCB 基板の設計・製造
- 全センサーの統合
- 判定アルゴリズムの実装
- 室内テスト（シミュレーション）

**フェーズ3: 実ユーザーテスト（8～12週間）**
- 実際のペット（犬）でのテスト
- データ精度の検証
- ユーザーフィードバック収集
- 改善・最適化

---

## 2. 必要な部品リスト

### 2.1 主要センサー部品

| 部品名 | 型番 | 数量 | 用途 | 価格 | 備考 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **感圧センサー** | FSR 402 | 5個 | 体重・排泄物重量計測 | ¥300/個 | Interlink Electronics |
| **MEMS 加速度計** | BMI160 | 2個 | 振動パターン検知 | ¥800/個 | Bosch Sensortec |
| **マイコン** | nRF52840 DK | 1個 | データ処理・判定 | ¥8,000 | Nordic Semiconductor |
| **Bluetooth モジュール** | nRF52840 内蔵 | - | 無線通信 | 含む | - |
| **リチウムイオン電池** | 18650 | 2個 | 電源（5000mAh） | ¥500/個 | パナソニック |
| **充電制御IC** | TP4056 | 1個 | USB-C 充電管理 | ¥200 | - |
| **電圧レギュレータ** | AMS1117 | 1個 | 3.3V 安定化 | ¥100 | - |
| **オペアンプ** | TL072 | 2個 | センサー信号増幅 | ¥100/個 | - |

**合計センサー部品コスト**: ¥12,400

### 2.2 PCB・基板関連

| 部品名 | 仕様 | 数量 | 価格 | 備考 |
| :--- | :--- | :--- | :--- | :--- |
| **PCB 基板** | 4層基板、400×600mm | 5枚 | ¥3,000 | JLCPCB など |
| **はんだ付け用工具** | ハンダ、フラックス | 1式 | ¥2,000 | - |
| **コネクタ** | USB-C、ピンヘッダ | 1式 | ¥500 | - |

**合計 PCB コスト**: ¥5,500

### 2.3 筐体・機械部品

| 部品名 | 仕様 | 数量 | 価格 | 備考 |
| :--- | :--- | :--- | :--- | :--- |
| **プラスチック筐体** | ABS、400×600×12mm | 5個 | ¥1,500/個 | 3D プリント可 |
| **防水シリコン** | 厚さ 2mm | 5枚 | ¥500/枚 | 上面コーティング用 |
| **滑り止めゴムパッド** | 厚さ 3mm | 5セット | ¥200/セット | 四隅用 |
| **ネジ・ボルト** | M3、M4 | 1式 | ¥300 | - |

**合計筐体コスト**: ¥10,000

### 2.4 開発・テスト機器

| 機器名 | 用途 | 価格 | 備考 |
| :--- | :--- | :--- | :--- |
| **マルチメータ** | 電圧・電流測定 | ¥3,000 | - |
| **オシロスコープ** | 信号波形確認 | ¥15,000 | USB オシロスコープ推奨 |
| **ロジックアナライザ** | デジタル信号解析 | ¥5,000 | - |
| **電源供給ユニット** | テスト用電源 | ¥3,000 | - |
| **USB デバッグプローブ** | J-Link、ST-Link | ¥5,000 | - |

**合計テスト機器コスト**: ¥31,000

### 2.5 開発ソフトウェア・ツール

| ツール名 | 用途 | 価格 | 備考 |
| :--- | :--- | :--- | :--- |
| **KiCad** | PCB 設計 | 無料 | オープンソース |
| **Segger Embedded Studio** | ファームウェア開発 | 無料 | Nordic 推奨 |
| **nRF Connect SDK** | SDK・ライブラリ | 無料 | Nordic 提供 |
| **MATLAB/Simulink** | 信号処理シミュレーション | ¥100,000/年 | オプション |

**合計ソフトウェアコスト**: 無料～¥100,000

---

## 3. PCB 設計仕様

### 3.1 PCB レイアウト

**基板サイズ**: 400mm × 600mm × 1.6mm（4層基板）

**層構成**:
- **Layer 1（トップ層）**: 信号層（センサー接続、マイコン）
- **Layer 2（GND層）**: グラウンド層（ノイズ低減）
- **Layer 3（電源層）**: 電源層（3.3V、GND）
- **Layer 4（ボトム層）**: 信号層（戻り経路）

### 3.2 センサー配置

```
┌─────────────────────────────────────┐
│  感圧センサー（5点配置）            │
│  ┌─────────────────────────────┐   │
│  │ FL    中央    FR           │   │
│  │                            │   │
│  │                            │   │
│  │ BL              BR         │   │
│  └─────────────────────────────┘   │
│                                     │
│  振動センサー（2点配置）            │
│  ┌─────────────────────────────┐   │
│  │ V1              V2          │   │
│  └─────────────────────────────┘   │
│                                     │
│  マイコン・Bluetooth（中央）        │
│  ┌─────────────────────────────┐   │
│  │ nRF52840 DK                 │   │
│  └─────────────────────────────┘   │
│                                     │
│  電源・充電制御（側面）             │
│  ┌─────────────────────────────┐   │
│  │ USB-C  TP4056  AMS1117      │   │
│  └─────────────────────────────┘   │
└─────────────────────────────────────┘
```

### 3.3 配線設計

**感圧センサー接続**:
```
FSR 402 (5個) 
  ↓
アナログ入力 (ADC 0-4)
  ↓
nRF52840 (ADC ポート)
```

**振動センサー接続**:
```
BMI160 (2個)
  ↓
I2C インターフェース
  ↓
nRF52840 (I2C ポート)
```

**電源配線**:
```
USB-C 入力 (5V)
  ↓
TP4056 (充電制御)
  ↓
18650 バッテリー (3.7V)
  ↓
AMS1117 (3.3V レギュレータ)
  ↓
全センサー・マイコン
```

---

## 4. ファームウェア仕様

### 4.1 開発環境

**IDE**: Segger Embedded Studio  
**SDK**: nRF Connect SDK v2.0 以上  
**言語**: C/C++  
**リアルタイム OS**: Zephyr RTOS

### 4.2 ファームウェア構成

```
nRF52840 Firmware
├── Main Loop
│   ├── Sensor Data Acquisition
│   │   ├── Read Pressure Sensors (ADC)
│   │   ├── Read Vibration Sensors (I2C/BMI160)
│   │   └── Timestamp Recording
│   ├── Data Processing
│   │   ├── Signal Filtering (Low-pass filter)
│   │   ├── Vibration Pattern Analysis
│   │   ├── Excretion Type Detection
│   │   └── Bristol Scale Estimation
│   ├── Alert Generation
│   │   ├── Abnormality Detection
│   │   └── Confidence Score Calculation
│   ├── Bluetooth Communication
│   │   ├── Data Transmission
│   │   └── Command Reception
│   └── Power Management
│       ├── Sleep Mode
│       └── Battery Monitoring
└── Peripheral Drivers
    ├── ADC Driver (Pressure Sensors)
    ├── I2C Driver (BMI160)
    ├── UART Driver (Debug)
    └── Bluetooth LE Driver
```

### 4.3 主要アルゴリズム

#### 4.3.1 感圧センサー処理

```c
// 体重計測
float measure_weight() {
    float total_pressure = 0;
    for (int i = 0; i < 5; i++) {
        total_pressure += read_adc(i);
    }
    float weight_kg = (total_pressure / 5) / CALIBRATION_FACTOR;
    return weight_kg;
}

// 排泄物重量計測
float measure_excretion_weight(float baseline_weight) {
    float current_weight = measure_weight();
    float excretion_weight = current_weight - baseline_weight;
    return excretion_weight;
}
```

#### 4.3.2 振動パターン分析

```c
// FFT を用いた周波数解析
void analyze_vibration_pattern() {
    // 1. 加速度データ取得（サンプリングレート 100Hz、1秒間）
    float accel_data[100];
    for (int i = 0; i < 100; i++) {
        accel_data[i] = read_bmi160();
        delay_ms(10);
    }
    
    // 2. FFT 計算
    float fft_result[50];
    compute_fft(accel_data, fft_result);
    
    // 3. 周波数ピーク検出
    float peak_frequency = find_peak_frequency(fft_result);
    float peak_magnitude = find_peak_magnitude(fft_result);
    
    // 4. 振動パターン判定
    if (peak_frequency >= 20 && peak_frequency <= 50) {
        // スプラッシュ振動 → 尿
        excretion_type = URINE;
    } else if (peak_frequency >= 10 && peak_frequency <= 30) {
        // インパクト振動 → 便
        excretion_type = FECES;
        
        // 便の硬さ推定
        if (peak_frequency >= 40) {
            bristol_scale = 1; // 非常に硬い
        } else if (peak_frequency >= 30) {
            bristol_scale = 3; // 硬い
        } else if (peak_frequency >= 20) {
            bristol_scale = 5; // 普通
        } else if (peak_frequency >= 10) {
            bristol_scale = 6; // 柔らかい
        } else {
            bristol_scale = 7; // 非常に柔らかい
        }
    }
}
```

#### 4.3.3 排泄物判定ロジック

```c
typedef struct {
    uint32_t timestamp;
    char excretion_type[16];  // "urine" or "feces"
    float weight_g;
    float dog_weight_kg;
    int bristol_scale;
    int confidence_score;
} ExcretionRecord;

ExcretionRecord detect_excretion() {
    ExcretionRecord record;
    record.timestamp = get_timestamp();
    
    // 1. 重量変化検知
    float baseline_weight = measure_weight();
    delay_ms(100);
    float current_weight = measure_weight();
    float weight_change = current_weight - baseline_weight;
    
    if (weight_change < 50) {
        // 排泄なし
        return record;
    }
    
    // 2. 振動パターン分析
    analyze_vibration_pattern();
    
    // 3. データ記録
    record.weight_g = weight_change * 1000;
    record.dog_weight_kg = baseline_weight;
    record.excretion_type = excretion_type;
    record.bristol_scale = bristol_scale;
    record.confidence_score = calculate_confidence();
    
    return record;
}
```

### 4.4 Bluetooth 通信仕様

**プロトコル**: BLE GATT  
**サービス UUID**: 12345678-1234-1234-1234-123456789012  
**キャラクタリスティック**:

| キャラクタリスティック | UUID | 用途 | データ形式 |
| :--- | :--- | :--- | :--- |
| Excretion Data | 00000001-... | 排泄データ送信 | JSON |
| Device Status | 00000002-... | デバイス状態 | JSON |
| Battery Level | 00000003-... | バッテリー残量 | uint8 (0-100) |
| Configuration | 00000004-... | デバイス設定受信 | JSON |

**データ送信フォーマット（JSON）**:
```json
{
  "device_id": "dog_board_001",
  "timestamp": "2026-03-26T10:30:45Z",
  "pet_id": "pet_001",
  "event_type": "defecation",
  "excretion_type": "urine",
  "weight_g": 120,
  "dog_weight_kg": 3.2,
  "urine_quality": "normal",
  "feces_bristol_scale": null,
  "duration_seconds": 6,
  "confidence_score": 98,
  "vibration_pattern": "splash",
  "signal_strength": -45
}
```

---

## 5. テスト計画

### 5.1 ユニットテスト

**感圧センサーテスト**:
- 既知の重量（1kg、5kg、10kg）を載せて精度確認
- ±100g 以内の誤差を確認
- 100 回以上の計測で安定性確認

**振動センサーテスト**:
- 既知の周波数（10Hz、20Hz、30Hz、40Hz、50Hz）の信号を入力
- FFT 計算結果の精度確認
- ノイズ除去フィルタの効果確認

**Bluetooth 通信テスト**:
- ペアリング成功確認
- データ送受信の正確性確認
- 通信距離 10m 以上での安定性確認

### 5.2 統合テスト

**シミュレーションテスト**:
- 尿のシミュレーション: 水をボード上に落とし、振動パターンを記録
- 便のシミュレーション: 粘土などを落とし、振動パターンを記録
- 混合排泄のシミュレーション: 水と粘土を同時に落とし、判定精度確認

**実ユーザーテスト**:
- 期間: 3 ヶ月
- テスト対象: 10～20 頭の犬
- テスト項目:
  - 排泄物判定の正確性（尿・便・混合）
  - 便の硬さ推定の正確性
  - バッテリー持続時間
  - ユーザーの利便性
  - 不具合の報告

### 5.3 品質保証テスト

**防水テスト**:
- IP67 等級確認（1m の深さで 30 分間の浸水）
- 尿・便・水に対する耐性確認

**耐久性テスト**:
- 2,000 回の排泄シミュレーション
- 故障率 5% 以下を確認

**環境テスト**:
- 温度: 0～50℃
- 湿度: 30～90%
- での動作確認

---

## 6. 開発スケジュール

### フェーズ1: 基本機能検証（0～4週間）

| 週 | タスク | 成果物 | 担当 |
| :--- | :--- | :--- | :--- |
| 1 | 部品調達、開発環境構築 | 部品リスト確認、IDE インストール | HW エンジ |
| 2 | 感圧センサー動作確認 | テストコード、キャリブレーション | HW エンジ |
| 3 | 振動センサー動作確認 | FFT 実装、周波数解析テスト | FW エンジ |
| 4 | Bluetooth 通信確認 | ペアリング・データ送受信テスト | FW エンジ |

**完了条件**: 全センサーが独立して動作確認

### フェーズ2: 統合テスト（4～8週間）

| 週 | タスク | 成果物 | 担当 |
| :--- | :--- | :--- | :--- |
| 5-6 | PCB 設計・製造 | PCB ガーバーファイル、基板 5 枚 | HW エンジ |
| 7 | センサー統合・はんだ付け | 統合ボード 5 個 | HW エンジ |
| 8 | 判定アルゴリズム実装 | ファームウェア v1.0 | FW エンジ |
| 9 | シミュレーションテスト | テストレポート | QA |

**完了条件**: 尿・便判定精度 95% 以上、シミュレーションテスト合格

### フェーズ3: 実ユーザーテスト（8～12週間）

| 週 | タスク | 成果物 | 担当 |
| :--- | :--- | :--- | :--- |
| 10-11 | 実ユーザーテスト準備 | テスト契約、テスト用デバイス 10 個 | PM |
| 12-14 | 実ユーザーテスト実施 | 週次レポート | QA |
| 15 | データ分析・改善 | 改善案、ファームウェア v2.0 | FW エンジ |
| 16 | 最終評価・量産準備 | 最終レポート、量産計画 | PM |

**完了条件**: 尿・便判定精度 98% 以上、ユーザー満足度 80% 以上

---

## 7. 予算見積もり

### 7.1 部品・機器コスト

| カテゴリ | コスト |
| :--- | :--- |
| センサー部品 | ¥12,400 |
| PCB・基板 | ¥5,500 |
| 筐体・機械部品 | ¥10,000 |
| テスト機器 | ¥31,000 |
| **小計** | **¥58,900** |

### 7.2 人件費（4ヶ月）

| 職種 | 人数 | 月給 | 合計 |
| :--- | :--- | :--- | :--- |
| ハードウェアエンジニア | 1 | ¥600,000 | ¥2,400,000 |
| ファームウェアエンジニア | 1 | ¥600,000 | ¥2,400,000 |
| QA エンジニア | 1 | ¥450,000 | ¥1,800,000 |
| プロジェクトマネージャー | 1 | ¥500,000 | ¥2,000,000 |
| **小計** | - | - | **¥8,600,000** |

### 7.3 その他コスト

| 項目 | コスト |
| :--- | :--- |
| オフィス・設備費 | ¥500,000 |
| 消耗品・雑費 | ¥300,000 |
| テスト用ペット飼育費 | ¥200,000 |
| **小計** | **¥1,000,000** |

### 7.4 合計予算

**プロトタイプ開発総予算**: ¥8,658,900（約 865万円）

---

## 8. リスク管理

### 8.1 技術的リスク

| リスク | 発生確率 | 影響度 | 対策 |
| :--- | :--- | :--- | :--- |
| センサー精度不足 | 中 | 高 | 複数のセンサー検証、キャリブレーション強化 |
| Bluetooth 通信不安定 | 低 | 中 | Wi-Fi バックアップ、通信テスト強化 |
| バッテリー持続時間不足 | 中 | 中 | 電力最適化、低消費電力センサー検討 |
| FFT 計算負荷 | 低 | 低 | マイコン性能確認、アルゴリズム最適化 |

### 8.2 スケジュールリスク

| リスク | 対策 |
| :--- | :--- |
| 部品調達遅延 | 早期発注、複数サプライヤー確保 |
| PCB 製造遅延 | 国内・海外メーカー並行発注 |
| ユーザーテスト参加者不足 | 早期募集、インセンティブ提供 |

---

## 9. 次のステップ

1. **部品調達**: 2 週間以内に全部品を確保
2. **開発環境構築**: IDE、SDK のインストール完了
3. **フェーズ1 開始**: 感圧センサーの動作確認から開始
4. **週次レビュー**: 毎週金曜日に進捗確認

---

**承認者**: PetSense 技術委員会  
**最終更新**: 2026年3月26日
