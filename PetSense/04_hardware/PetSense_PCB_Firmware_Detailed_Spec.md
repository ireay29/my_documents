# PetSense PCB 設計・ファームウェア実装・テスト詳細仕様書

**作成日**: 2026年3月26日  
**バージョン**: 1.0  
**対象**: PCB 設計者、ファームウェア開発者、QA エンジニア

---

## 1. PCB 詳細設計仕様

### 1.1 PCB 基本仕様

**基板サイズ**: 400mm × 600mm × 1.6mm  
**層数**: 4層基板  
**材質**: FR-4（ガラス繊維強化エポキシ樹脂）  
**銅厚**: 35μm（1oz）  
**表面処理**: HASL（鉛フリー）  
**最小トレース幅**: 0.15mm  
**最小ビア径**: 0.3mm  
**インピーダンス制御**: 50Ω（Bluetooth アンテナ用）

### 1.2 層構成と信号配置

**Layer 1（トップ層）**:
- センサー接続トレース
- マイコン周辺回路
- デバッグ用ピン

**Layer 2（GND層）**:
- 全面グラウンド（ノイズ低減）
- センサー GND 接続
- 高周波ノイズ吸収

**Layer 3（電源層）**:
- 3.3V 電源分配
- GND リターン
- デカップリング用

**Layer 4（ボトム層）**:
- 信号戻り経路
- 充電制御回路
- テスト用パッド

### 1.3 電源配線設計

**電源ツリー**:
```
USB-C 5V Input
    ↓
TP4056 (Charging Controller)
    ↓
18650 Battery (3.7V)
    ↓
AMS1117 (3.3V Regulator)
    ├→ nRF52840 (3.3V)
    ├→ Sensors (3.3V)
    ├→ Op-Amps (3.3V)
    └→ Decoupling Caps
```

**デカップリングキャパシタ配置**:
- nRF52840 周辺: 100nF × 4個（各電源ピン近く）
- センサー周辺: 100nF × 2個（各センサー）
- Op-Amps 周辺: 100nF × 2個（各 IC）

**電流容量**:
- USB-C: 最大 2A
- TP4056: 最大 1A
- AMS1117: 最大 1A
- 18650 バッテリー: 最大 2.5A

### 1.4 センサー接続詳細

#### 1.4.1 感圧センサー（FSR 402）接続

**配置**: 5個（四隅 + 中央）

**接続回路**:
```
FSR 402
    ├─ Pin 1 → 3.3V
    └─ Pin 2 → ADC Input (via 10kΩ pull-down)
                   ↓
                Op-Amp (TL072)
                   ↓
                nRF52840 ADC
```

**ADC ポート割り当て**:
- ADC 0: FL（Front Left）
- ADC 1: FR（Front Right）
- ADC 2: BL（Back Left）
- ADC 3: BR（Back Right）
- ADC 4: Center

**キャリブレーション**:
```c
// 既知の重量（1kg、5kg、10kg）を計測
float calibration_factor = (adc_reading_5kg - adc_reading_1kg) / 4000;
```

#### 1.4.2 振動センサー（BMI160）接続

**配置**: 2個（中央部）

**インターフェース**: I2C

**接続回路**:
```
BMI160 #1
    ├─ SDA → nRF52840 P0.26
    ├─ SCL → nRF52840 P0.27
    ├─ INT1 → nRF52840 P0.28
    └─ GND → GND

BMI160 #2
    ├─ SDA → nRF52840 P0.26 (同じ I2C バス)
    ├─ SCL → nRF52840 P0.27 (同じ I2C バス)
    ├─ INT2 → nRF52840 P0.29
    └─ GND → GND
```

**I2C アドレス**:
- BMI160 #1: 0x68（デフォルト）
- BMI160 #2: 0x69（SDO ピンを 3.3V に接続）

**プルアップ抵抗**: 4.7kΩ（SDA、SCL）

#### 1.4.3 充電制御（TP4056）

**接続**:
```
USB-C 5V
    ↓
TP4056
    ├─ OUT+ → 18650 + (via 0.1Ω sense resistor)
    ├─ OUT- → 18650 -
    ├─ PROG → 1kΩ resistor to GND (1A charging current)
    └─ GND → GND
```

**充電電流設定**:
```
I_charge = 1200 / R_prog
R_prog = 1.2kΩ → I_charge = 1A
```

#### 1.4.4 電圧レギュレータ（AMS1117）

**接続**:
```
18650 Battery (3.7V)
    ↓
AMS1117-3.3
    ├─ VIN → Battery +
    ├─ VOUT → 3.3V Rail
    ├─ GND → GND
    └─ Decoupling Caps (10μF)
```

**出力電圧**: 3.3V ± 3%  
**出力電流**: 最大 1A

### 1.5 アンテナ設計

**Bluetooth アンテナ**:
- タイプ: PCB トレース アンテナ
- 周波数: 2.4GHz
- インピーダンス: 50Ω
- 長さ: 31.5mm（λ/4 設計）
- トレース幅: 0.2mm

**アンテナ配置**:
```
nRF52840
    ↓
Matching Network (LC)
    ↓
PCB Trace Antenna (31.5mm)
    ↓
GND Plane (下層)
```

**マッチングネットワーク**:
- L: 4.7nH（シリーズ）
- C: 2.2pF（シャント）

---

## 2. ファームウェア実装ガイド

### 2.1 開発環境セットアップ

**必要なツール**:
1. Segger Embedded Studio（IDE）
2. nRF Connect SDK v2.0 以上
3. Zephyr RTOS
4. arm-none-eabi-gcc コンパイラ

**インストール手順**:
```bash
# 1. nRF Connect SDK のインストール
git clone https://github.com/nrfconnect/sdk-nrf.git
cd sdk-nrf
west init -m https://github.com/nrfconnect/sdk-nrf --mr main

# 2. 依存関係のインストール
pip install -r requirements.txt

# 3. Segger Embedded Studio のインストール
# https://www.segger.com/downloads/embedded-studio/ からダウンロード
```

### 2.2 プロジェクト構造

```
petsense-firmware/
├── CMakeLists.txt
├── prj.conf
├── src/
│   ├── main.c
│   ├── sensor_driver.c
│   ├── sensor_driver.h
│   ├── vibration_analysis.c
│   ├── vibration_analysis.h
│   ├── excretion_detection.c
│   ├── excretion_detection.h
│   ├── ble_communication.c
│   ├── ble_communication.h
│   ├── power_management.c
│   └── power_management.h
├── boards/
│   └── nrf52840dk_nrf52840.overlay
└── tests/
    ├── test_sensor_driver.c
    ├── test_vibration_analysis.c
    └── test_excretion_detection.c
```

### 2.3 主要モジュール実装

#### 2.3.1 センサードライバ（sensor_driver.c）

```c
#include <zephyr/kernel.h>
#include <zephyr/drivers/adc.h>
#include <zephyr/drivers/i2c.h>

// ADC デバイス構造体
static const struct adc_dt_spec adc_channels[] = {
    ADC_DT_SPEC_GET_BY_IDX(DT_PATH(zephyr_user), 0),  // FL
    ADC_DT_SPEC_GET_BY_IDX(DT_PATH(zephyr_user), 1),  // FR
    ADC_DT_SPEC_GET_BY_IDX(DT_PATH(zephyr_user), 2),  // BL
    ADC_DT_SPEC_GET_BY_IDX(DT_PATH(zephyr_user), 3),  // BR
    ADC_DT_SPEC_GET_BY_IDX(DT_PATH(zephyr_user), 4),  // Center
};

// 感圧センサー初期化
int pressure_sensor_init(void) {
    for (int i = 0; i < 5; i++) {
        if (!adc_is_ready_dt(&adc_channels[i])) {
            printk("ADC %d is not ready\n", i);
            return -ENODEV;
        }
        adc_channel_setup_dt(&adc_channels[i]);
    }
    return 0;
}

// 感圧センサー読み取り
float read_pressure_sensor(int channel) {
    int16_t buf;
    struct adc_sequence sequence = {
        .buffer = &buf,
        .buffer_size = sizeof(buf),
        .channels = BIT(adc_channels[channel].channel_id),
        .resolution = 12,
    };
    
    adc_read_dt(&adc_channels[channel], &sequence);
    
    // ADC 値を電圧に変換
    float voltage = (float)buf * 3.3 / 4096;
    
    // 電圧を重量に変換（キャリブレーション係数を使用）
    float weight_kg = voltage / CALIBRATION_FACTOR;
    
    return weight_kg;
}

// 全センサー読み取り（平均値）
float read_all_pressure_sensors(void) {
    float total = 0;
    for (int i = 0; i < 5; i++) {
        total += read_pressure_sensor(i);
    }
    return total / 5;
}
```

#### 2.3.2 振動解析（vibration_analysis.c）

```c
#include <zephyr/kernel.h>
#include <zephyr/drivers/i2c.h>
#include <arm_math.h>  // CMSIS DSP ライブラリ

#define BMI160_ADDR 0x68
#define BMI160_ACCEL_DATA_REG 0x12
#define FFT_SIZE 256

// BMI160 初期化
int vibration_sensor_init(void) {
    const struct device *i2c_dev = DEVICE_DT_GET(DT_ALIAS(i2c0));
    
    if (!device_is_ready(i2c_dev)) {
        printk("I2C device not ready\n");
        return -ENODEV;
    }
    
    // BMI160 初期化コマンド
    uint8_t cmd = 0x11;  // Normal mode
    i2c_write_dt(&i2c_dev, BMI160_ADDR, 0x7E, &cmd, 1);
    
    return 0;
}

// 加速度データ読み取り
void read_acceleration_data(float *accel_x, float *accel_y, float *accel_z) {
    const struct device *i2c_dev = DEVICE_DT_GET(DT_ALIAS(i2c0));
    uint8_t data[6];
    
    i2c_read_dt(&i2c_dev, BMI160_ADDR, BMI160_ACCEL_DATA_REG, data, 6);
    
    // 16 ビット加速度値に変換
    int16_t x = (data[1] << 8) | data[0];
    int16_t y = (data[3] << 8) | data[2];
    int16_t z = (data[5] << 8) | data[4];
    
    // m/s² に変換（±2g レンジ）
    *accel_x = (float)x * 2 * 9.81 / 32768;
    *accel_y = (float)y * 2 * 9.81 / 32768;
    *accel_z = (float)z * 2 * 9.81 / 32768;
}

// FFT を用いた周波数解析
void analyze_vibration_frequency(float *peak_freq, float *peak_mag) {
    float accel_data[FFT_SIZE];
    float fft_output[FFT_SIZE];
    
    // 加速度データ取得（サンプリングレート 100Hz、2.56 秒間）
    for (int i = 0; i < FFT_SIZE; i++) {
        float x, y, z;
        read_acceleration_data(&x, &y, &z);
        
        // 合成加速度（3軸の二乗和の平方根）
        accel_data[i] = sqrtf(x*x + y*y + z*z);
        
        k_msleep(10);  // 100Hz サンプリング
    }
    
    // FFT 計算（CMSIS DSP ライブラリ使用）
    arm_rfft_fast_f32_instance instance;
    arm_rfft_fast_init_f32(&instance, FFT_SIZE);
    arm_rfft_fast_f32(&instance, accel_data, fft_output, 0);
    
    // ピーク周波数検出
    float max_magnitude = 0;
    int peak_bin = 0;
    
    for (int i = 1; i < FFT_SIZE / 2; i++) {
        float real = fft_output[2*i];
        float imag = fft_output[2*i+1];
        float magnitude = sqrtf(real*real + imag*imag);
        
        if (magnitude > max_magnitude) {
            max_magnitude = magnitude;
            peak_bin = i;
        }
    }
    
    // ビンを周波数に変換（100Hz サンプリング）
    *peak_freq = (float)peak_bin * 100 / FFT_SIZE;
    *peak_mag = max_magnitude;
}
```

#### 2.3.3 排泄物検知（excretion_detection.c）

```c
#include <zephyr/kernel.h>
#include "sensor_driver.h"
#include "vibration_analysis.h"

typedef struct {
    uint32_t timestamp;
    char excretion_type[16];
    float weight_g;
    float dog_weight_kg;
    int bristol_scale;
    int confidence_score;
} ExcretionRecord;

// 排泄物判定メイン関数
ExcretionRecord detect_excretion(void) {
    ExcretionRecord record;
    record.timestamp = k_uptime_get();
    
    // 1. ベースライン重量計測
    float baseline_weight = read_all_pressure_sensors();
    k_msleep(100);
    
    // 2. 重量変化検知
    float current_weight = read_all_pressure_sensors();
    float weight_change = current_weight - baseline_weight;
    
    if (weight_change < 0.05) {  // 50g 未満
        strcpy(record.excretion_type, "none");
        return record;
    }
    
    // 3. 振動パターン分析
    float peak_freq, peak_mag;
    analyze_vibration_frequency(&peak_freq, &peak_mag);
    
    // 4. 排泄物判定
    if (peak_freq >= 20 && peak_freq <= 50) {
        // スプラッシュ振動 → 尿
        strcpy(record.excretion_type, "urine");
        record.confidence_score = 98;
    } else if (peak_freq >= 10 && peak_freq <= 30) {
        // インパクト振動 → 便
        strcpy(record.excretion_type, "feces");
        
        // 便の硬さ推定
        if (peak_freq >= 40) {
            record.bristol_scale = 1;
        } else if (peak_freq >= 30) {
            record.bristol_scale = 3;
        } else if (peak_freq >= 20) {
            record.bristol_scale = 5;
        } else if (peak_freq >= 10) {
            record.bristol_scale = 6;
        } else {
            record.bristol_scale = 7;
        }
        
        record.confidence_score = 99;
    } else {
        // 判定不可
        strcpy(record.excretion_type, "unknown");
        record.confidence_score = 50;
    }
    
    // 5. データ記録
    record.weight_g = weight_change * 1000;
    record.dog_weight_kg = baseline_weight;
    
    return record;
}
```

#### 2.3.4 Bluetooth 通信（ble_communication.c）

```c
#include <zephyr/kernel.h>
#include <zephyr/bluetooth/bluetooth.h>
#include <zephyr/bluetooth/gatt.h>
#include <zephyr/bluetooth/uuid.h>

#define BT_UUID_PETSENSE_SERVICE \
    BT_UUID_128_ENCODE(0x12345678, 0x1234, 0x1234, 0x1234, 0x123456789012)

#define BT_UUID_EXCRETION_DATA \
    BT_UUID_128_ENCODE(0x00000001, 0x1234, 0x1234, 0x1234, 0x123456789012)

// Bluetooth 初期化
int ble_init(void) {
    int err = bt_enable(NULL);
    if (err) {
        printk("Bluetooth init failed (err %d)\n", err);
        return err;
    }
    
    printk("Bluetooth initialized\n");
    
    // アドバタイジング開始
    err = bt_le_adv_start(BT_LE_ADV_CONN_NAME, NULL, 0, NULL, 0);
    if (err) {
        printk("Advertising failed to start (err %d)\n", err);
        return err;
    }
    
    printk("Advertising started\n");
    
    return 0;
}

// GATT サービス定義
static struct bt_gatt_attr attrs[] = {
    BT_GATT_PRIMARY_SERVICE(BT_UUID_PETSENSE_SERVICE),
    BT_GATT_CHARACTERISTIC(BT_UUID_EXCRETION_DATA,
                           BT_GATT_CHRC_NOTIFY,
                           BT_GATT_PERM_READ,
                           NULL, NULL, NULL),
    BT_GATT_CCC(NULL, BT_GATT_PERM_READ | BT_GATT_PERM_WRITE),
};

static struct bt_gatt_service svc = BT_GATT_SERVICE(attrs);

// 排泄データ送信
int send_excretion_data(ExcretionRecord *record) {
    // JSON フォーマット
    char json_buffer[256];
    snprintf(json_buffer, sizeof(json_buffer),
             "{\"type\":\"%s\",\"weight_g\":%.1f,\"bristol\":%d,\"confidence\":%d}",
             record->excretion_type,
             record->weight_g,
             record->bristol_scale,
             record->confidence_score);
    
    // BLE で送信
    bt_gatt_notify(NULL, &attrs[1], json_buffer, strlen(json_buffer));
    
    return 0;
}
```

### 2.4 メインループ

```c
// main.c
#include <zephyr/kernel.h>
#include "sensor_driver.h"
#include "vibration_analysis.h"
#include "excretion_detection.h"
#include "ble_communication.h"
#include "power_management.h"

void main(void) {
    printk("PetSense Firmware Starting...\n");
    
    // 初期化
    pressure_sensor_init();
    vibration_sensor_init();
    ble_init();
    power_management_init();
    
    printk("All systems initialized\n");
    
    // メインループ
    while (1) {
        // 排泄物検知
        ExcretionRecord record = detect_excretion();
        
        // 検知された場合、BLE で送信
        if (strcmp(record.excretion_type, "none") != 0) {
            printk("Excretion detected: %s (weight: %.1fg)\n",
                   record.excretion_type,
                   record.weight_g);
            
            send_excretion_data(&record);
        }
        
        // 電力管理（スリープモード）
        k_msleep(1000);  // 1 秒ごとにチェック
    }
}
```

---

## 3. テスト計画と実装

### 3.1 ユニットテスト

#### 3.1.1 感圧センサーテスト

```c
// test_sensor_driver.c
#include <zephyr/ztest.h>
#include "sensor_driver.h"

ZTEST_SUITE(pressure_sensor_tests, NULL, NULL, NULL, NULL);

ZTEST(pressure_sensor_tests, test_pressure_sensor_read) {
    float weight = read_pressure_sensor(0);
    zassert_true(weight >= 0 && weight <= 50, "Weight out of range");
}

ZTEST(pressure_sensor_tests, test_calibration) {
    // 1kg 計測
    float weight_1kg = read_all_pressure_sensors();
    zassert_true(weight_1kg >= 0.9 && weight_1kg <= 1.1, "1kg calibration failed");
    
    // 5kg 計測
    float weight_5kg = read_all_pressure_sensors();
    zassert_true(weight_5kg >= 4.9 && weight_5kg <= 5.1, "5kg calibration failed");
}
```

#### 3.1.2 振動センサーテスト

```c
// test_vibration_analysis.c
#include <zephyr/ztest.h>
#include "vibration_analysis.h"

ZTEST_SUITE(vibration_tests, NULL, NULL, NULL, NULL);

ZTEST(vibration_tests, test_frequency_detection) {
    float peak_freq, peak_mag;
    analyze_vibration_frequency(&peak_freq, &peak_mag);
    
    // 周波数が 5～50Hz 範囲内か確認
    zassert_true(peak_freq >= 5 && peak_freq <= 50, "Frequency out of range");
}
```

#### 3.1.3 排泄物検知テスト

```c
// test_excretion_detection.c
#include <zephyr/ztest.h>
#include "excretion_detection.h"

ZTEST_SUITE(excretion_tests, NULL, NULL, NULL, NULL);

ZTEST(excretion_tests, test_urine_detection) {
    // 尿シミュレーション（水をボード上に落とす）
    ExcretionRecord record = detect_excretion();
    
    zassert_str_equal(record.excretion_type, "urine", "Urine detection failed");
    zassert_true(record.confidence_score >= 80, "Confidence score too low");
}

ZTEST(excretion_tests, test_feces_detection) {
    // 便シミュレーション（粘土をボード上に落とす）
    ExcretionRecord record = detect_excretion();
    
    zassert_str_equal(record.excretion_type, "feces", "Feces detection failed");
    zassert_true(record.bristol_scale >= 1 && record.bristol_scale <= 7, 
                 "Bristol scale out of range");
}
```

### 3.2 統合テスト

**テスト環境**:
- テスト用ボード 5 個
- テスト用デバイス（スマートフォン）
- テスト用ペット（犬 3～5 頭）

**テスト項目**:

| テスト項目 | テスト内容 | 合格基準 |
| :--- | :--- | :--- |
| 尿検知精度 | 50 回の尿排泄をテスト | 98% 以上の正確性 |
| 便検知精度 | 50 回の便排泄をテスト | 99% 以上の正確性 |
| 便の硬さ推定 | ブリストル・スケール 1～7 を検証 | 90% 以上の正確性 |
| バッテリー持続時間 | 連続 60 日間の動作確認 | 60 日以上 |
| Bluetooth 通信 | 10m 距離での通信確認 | 100% 通信成功 |
| 防水性 | IP67 テスト実施 | 完全防水 |

---

## 4. デバッグとトラブルシューティング

### 4.1 一般的な問題と対策

| 問題 | 原因 | 対策 |
| :--- | :--- | :--- |
| ADC 値が不安定 | ノイズ混入 | デカップリングキャパシタ追加、グラウンド接続強化 |
| I2C 通信エラー | アドレス競合 | I2C アドレス確認、プルアップ抵抗確認 |
| Bluetooth 接続失敗 | アンテナ問題 | アンテナ設計確認、マッチングネットワーク調整 |
| バッテリー消費が大きい | 電力最適化不足 | スリープモード実装、センサー読み取り間隔調整 |

### 4.2 デバッグ方法

**UART デバッグ**:
```c
// UART ポート設定
const struct device *uart_dev = DEVICE_DT_GET(DT_ALIAS(uart0));

// デバッグ出力
printk("Debug message: %d\n", value);
```

**RTT（Real-Time Transfer）デバッグ**:
```c
// Segger RTT を使用したリアルタイムデバッグ
#include <SEGGER_RTT.h>

SEGGER_RTT_printf(0, "Value: %d\n", value);
```

---

## 5. 量産準備

### 5.1 DFM（Design for Manufacturing）チェックリスト

- [ ] PCB トレース幅・間隔が製造仕様内か確認
- [ ] ビア径が最小径以上か確認
- [ ] 部品配置が実装可能か確認
- [ ] テストパッド配置が適切か確認
- [ ] 部品の入手可能性確認
- [ ] コスト最適化の検討

### 5.2 品質管理

**ICT（In-Circuit Test）**:
- 全電気接続の確認
- 短絡・断線検査
- 部品の実装確認

**FCT（Functional Test）**:
- 全機能の動作確認
- センサー精度検証
- Bluetooth 通信確認

**ESS（Environmental Stress Screening）**:
- 温度サイクルテスト（0～50℃）
- 湿度テスト（30～90%）
- 振動テスト

---

**承認者**: PetSense 技術委員会  
**最終更新**: 2026年3月26日
