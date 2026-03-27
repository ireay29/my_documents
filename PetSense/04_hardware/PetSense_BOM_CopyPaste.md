# PetSense 犬用ボード型デバイス - 部品リスト（コピペ用）

## センサー部品

```
部品名,型番,数量,用途,価格/個,合計価格,備考
感圧センサー,FSR 402,5,体重・排泄物重量計測,300,1500,Interlink Electronics
MEMS加速度計,BMI160,2,振動パターン検知,800,1600,Bosch Sensortec
マイコン,nRF52840 DK,1,データ処理・判定,8000,8000,Nordic Semiconductor
リチウムイオン電池,18650,2,電源（5000mAh）,500,1000,パナソニック
充電制御IC,TP4056,1,USB-C充電管理,200,200,
電圧レギュレータ,AMS1117,1,3.3V安定化,100,100,
オペアンプ,TL072,2,センサー信号増幅,100,200,
```

## PCB・基板関連

```
部品名,仕様,数量,価格/個,合計価格,備考
PCB基板,4層基板400×600mm,5,600,3000,JLCPCB等
はんだ付け用工具,ハンダ・フラックス,1式,2000,2000,
コネクタ,USB-C・ピンヘッダ,1式,500,500,
```

## 筐体・機械部品

```
部品名,仕様,数量,価格/個,合計価格,備考
プラスチック筐体,ABS 400×600×12mm,5,1500,7500,3Dプリント可
防水シリコン,厚さ2mm,5,500,2500,上面コーティング用
滑り止めゴムパッド,厚さ3mm,5,200,1000,四隅用
ネジ・ボルト,M3・M4,1式,300,300,
```

## 開発・テスト機器

```
機器名,用途,価格,備考
マルチメータ,電圧・電流測定,3000,
オシロスコープ,信号波形確認,15000,USBオシロスコープ推奨
ロジックアナライザ,デジタル信号解析,5000,
電源供給ユニット,テスト用電源,3000,
USBデバッグプローブ,J-Link・ST-Link,5000,
```

## 開発ソフトウェア・ツール

```
ツール名,用途,価格,備考
KiCad,PCB設計,無料,オープンソース
Segger Embedded Studio,ファームウェア開発,無料,Nordic推奨
nRF Connect SDK,SDK・ライブラリ,無料,Nordic提供
MATLAB/Simulink,信号処理シミュレーション,100000/年,オプション
```

## 部品調達リスト（JSON形式）

```json
{
  "sensors": [
    {
      "name": "感圧センサー",
      "model": "FSR 402",
      "quantity": 5,
      "unit_price": 300,
      "supplier": "Interlink Electronics",
      "url": "https://www.interlink-electronics.com/",
      "notes": "Force Sensitive Resistor"
    },
    {
      "name": "MEMS加速度計",
      "model": "BMI160",
      "quantity": 2,
      "unit_price": 800,
      "supplier": "Bosch Sensortec",
      "url": "https://www.bosch-sensortec.com/",
      "notes": "6-axis IMU"
    },
    {
      "name": "マイコン",
      "model": "nRF52840 DK",
      "quantity": 1,
      "unit_price": 8000,
      "supplier": "Nordic Semiconductor",
      "url": "https://www.nordicsemi.com/",
      "notes": "Development Kit"
    },
    {
      "name": "リチウムイオン電池",
      "model": "18650",
      "quantity": 2,
      "unit_price": 500,
      "supplier": "パナソニック",
      "url": "https://www.panasonic.com/",
      "notes": "5000mAh"
    },
    {
      "name": "充電制御IC",
      "model": "TP4056",
      "quantity": 1,
      "unit_price": 200,
      "supplier": "Aliexpress/秋月電子",
      "url": "https://www.akizuki.com/",
      "notes": "USB-C Charging Controller"
    },
    {
      "name": "電圧レギュレータ",
      "model": "AMS1117",
      "quantity": 1,
      "unit_price": 100,
      "supplier": "秋月電子",
      "url": "https://www.akizuki.com/",
      "notes": "3.3V Linear Regulator"
    },
    {
      "name": "オペアンプ",
      "model": "TL072",
      "quantity": 2,
      "unit_price": 100,
      "supplier": "秋月電子",
      "url": "https://www.akizuki.com/",
      "notes": "Dual Op-Amp"
    }
  ],
  "pcb_components": [
    {
      "name": "PCB基板",
      "specification": "4層基板 400×600mm",
      "quantity": 5,
      "unit_price": 600,
      "supplier": "JLCPCB",
      "url": "https://jlcpcb.com/",
      "notes": "FR-4 1.6mm"
    },
    {
      "name": "はんだ付け用工具",
      "specification": "ハンダ・フラックス",
      "quantity": 1,
      "unit_price": 2000,
      "supplier": "秋月電子",
      "url": "https://www.akizuki.com/",
      "notes": "鉛フリー推奨"
    },
    {
      "name": "コネクタ",
      "specification": "USB-C・ピンヘッダ",
      "quantity": 1,
      "unit_price": 500,
      "supplier": "秋月電子",
      "url": "https://www.akizuki.com/",
      "notes": ""
    }
  ],
  "enclosure_parts": [
    {
      "name": "プラスチック筐体",
      "specification": "ABS 400×600×12mm",
      "quantity": 5,
      "unit_price": 1500,
      "supplier": "3Dプリント業者",
      "url": "https://www.dmm.com/print/",
      "notes": "3Dプリント可"
    },
    {
      "name": "防水シリコン",
      "specification": "厚さ2mm",
      "quantity": 5,
      "unit_price": 500,
      "supplier": "Amazon",
      "url": "https://www.amazon.co.jp/",
      "notes": "上面コーティング用"
    },
    {
      "name": "滑り止めゴムパッド",
      "specification": "厚さ3mm",
      "quantity": 5,
      "unit_price": 200,
      "supplier": "Amazon",
      "url": "https://www.amazon.co.jp/",
      "notes": "四隅用"
    },
    {
      "name": "ネジ・ボルト",
      "specification": "M3・M4",
      "quantity": 1,
      "unit_price": 300,
      "supplier": "秋月電子",
      "url": "https://www.akizuki.com/",
      "notes": "ステンレス推奨"
    }
  ],
  "test_equipment": [
    {
      "name": "マルチメータ",
      "purpose": "電圧・電流測定",
      "price": 3000,
      "supplier": "Amazon",
      "url": "https://www.amazon.co.jp/",
      "notes": ""
    },
    {
      "name": "オシロスコープ",
      "purpose": "信号波形確認",
      "price": 15000,
      "supplier": "Amazon",
      "url": "https://www.amazon.co.jp/",
      "notes": "USBオシロスコープ推奨"
    },
    {
      "name": "ロジックアナライザ",
      "purpose": "デジタル信号解析",
      "price": 5000,
      "supplier": "Amazon",
      "url": "https://www.amazon.co.jp/",
      "notes": ""
    },
    {
      "name": "電源供給ユニット",
      "purpose": "テスト用電源",
      "price": 3000,
      "supplier": "Amazon",
      "url": "https://www.amazon.co.jp/",
      "notes": ""
    },
    {
      "name": "USBデバッグプローブ",
      "purpose": "J-Link・ST-Link",
      "price": 5000,
      "supplier": "秋月電子",
      "url": "https://www.akizuki.com/",
      "notes": ""
    }
  ]
}
```

## 部品調達リスト（Excel用TSV形式）

```
部品カテゴリ	部品名	型番	数量	単価	合計	仕入先	URL	備考
センサー	感圧センサー	FSR 402	5	300	1500	Interlink Electronics	https://www.interlink-electronics.com/	Force Sensitive Resistor
センサー	MEMS加速度計	BMI160	2	800	1600	Bosch Sensortec	https://www.bosch-sensortec.com/	6-axis IMU
センサー	マイコン	nRF52840 DK	1	8000	8000	Nordic Semiconductor	https://www.nordicsemi.com/	Development Kit
センサー	リチウムイオン電池	18650	2	500	1000	パナソニック	https://www.panasonic.com/	5000mAh
センサー	充電制御IC	TP4056	1	200	200	秋月電子	https://www.akizuki.com/	USB-C Charging
センサー	電圧レギュレータ	AMS1117	1	100	100	秋月電子	https://www.akizuki.com/	3.3V Linear
センサー	オペアンプ	TL072	2	100	200	秋月電子	https://www.akizuki.com/	Dual Op-Amp
PCB	PCB基板	4層基板	5	600	3000	JLCPCB	https://jlcpcb.com/	400×600mm
PCB	はんだ付け工具	ハンダ・フラックス	1	2000	2000	秋月電子	https://www.akizuki.com/	鉛フリー
PCB	コネクタ	USB-C・ピン	1	500	500	秋月電子	https://www.akizuki.com/	
筐体	プラスチック筐体	ABS	5	1500	7500	3Dプリント	https://www.dmm.com/print/	400×600×12mm
筐体	防水シリコン	2mm厚	5	500	2500	Amazon	https://www.amazon.co.jp/	上面用
筐体	滑り止めゴム	3mm厚	5	200	1000	Amazon	https://www.amazon.co.jp/	四隅用
筐体	ネジ・ボルト	M3・M4	1	300	300	秋月電子	https://www.akizuki.com/	ステンレス
テスト	マルチメータ	-	1	3000	3000	Amazon	https://www.amazon.co.jp/	
テスト	オシロスコープ	USB型	1	15000	15000	Amazon	https://www.amazon.co.jp/	
テスト	ロジックアナライザ	-	1	5000	5000	Amazon	https://www.amazon.co.jp/	
テスト	電源供給ユニット	-	1	3000	3000	Amazon	https://www.amazon.co.jp/	
テスト	USBデバッグプローブ	J-Link	1	5000	5000	秋月電子	https://www.akizuki.com/	
```

## 部品調達リスト（Markdown テーブル）

```markdown
| カテゴリ | 部品名 | 型番 | 数量 | 単価 | 合計 | 仕入先 | 備考 |
|---------|--------|------|------|------|------|--------|------|
| センサー | 感圧センサー | FSR 402 | 5 | ¥300 | ¥1,500 | Interlink | Force Sensitive Resistor |
| センサー | MEMS加速度計 | BMI160 | 2 | ¥800 | ¥1,600 | Bosch | 6-axis IMU |
| センサー | マイコン | nRF52840 DK | 1 | ¥8,000 | ¥8,000 | Nordic | Development Kit |
| センサー | 電池 | 18650 | 2 | ¥500 | ¥1,000 | パナソニック | 5000mAh |
| センサー | 充電IC | TP4056 | 1 | ¥200 | ¥200 | 秋月電子 | USB-C |
| センサー | レギュレータ | AMS1117 | 1 | ¥100 | ¥100 | 秋月電子 | 3.3V |
| センサー | Op-Amp | TL072 | 2 | ¥100 | ¥200 | 秋月電子 | Dual |
| PCB | PCB基板 | 4層 | 5 | ¥600 | ¥3,000 | JLCPCB | 400×600mm |
| PCB | はんだ工具 | - | 1 | ¥2,000 | ¥2,000 | 秋月電子 | 鉛フリー |
| PCB | コネクタ | USB-C | 1 | ¥500 | ¥500 | 秋月電子 | - |
| 筐体 | プラスチック | ABS | 5 | ¥1,500 | ¥7,500 | 3Dプリント | 400×600×12mm |
| 筐体 | 防水シリコン | 2mm | 5 | ¥500 | ¥2,500 | Amazon | 上面用 |
| 筐体 | ゴムパッド | 3mm | 5 | ¥200 | ¥1,000 | Amazon | 四隅用 |
| 筐体 | ネジ・ボルト | M3/M4 | 1 | ¥300 | ¥300 | 秋月電子 | ステンレス |
| テスト | マルチメータ | - | 1 | ¥3,000 | ¥3,000 | Amazon | - |
| テスト | オシロスコープ | USB | 1 | ¥15,000 | ¥15,000 | Amazon | - |
| テスト | ロジックアナライザ | - | 1 | ¥5,000 | ¥5,000 | Amazon | - |
| テスト | 電源ユニット | - | 1 | ¥3,000 | ¥3,000 | Amazon | - |
| テスト | デバッグプローブ | J-Link | 1 | ¥5,000 | ¥5,000 | 秋月電子 | - |
```

## 部品調達リスト（Python辞書形式）

```python
parts_list = {
    "sensors": [
        {"name": "感圧センサー", "model": "FSR 402", "qty": 5, "price": 300, "total": 1500},
        {"name": "MEMS加速度計", "model": "BMI160", "qty": 2, "price": 800, "total": 1600},
        {"name": "マイコン", "model": "nRF52840 DK", "qty": 1, "price": 8000, "total": 8000},
        {"name": "リチウムイオン電池", "model": "18650", "qty": 2, "price": 500, "total": 1000},
        {"name": "充電制御IC", "model": "TP4056", "qty": 1, "price": 200, "total": 200},
        {"name": "電圧レギュレータ", "model": "AMS1117", "qty": 1, "price": 100, "total": 100},
        {"name": "オペアンプ", "model": "TL072", "qty": 2, "price": 100, "total": 200},
    ],
    "pcb": [
        {"name": "PCB基板", "spec": "4層基板", "qty": 5, "price": 600, "total": 3000},
        {"name": "はんだ付け工具", "spec": "ハンダ・フラックス", "qty": 1, "price": 2000, "total": 2000},
        {"name": "コネクタ", "spec": "USB-C・ピンヘッダ", "qty": 1, "price": 500, "total": 500},
    ],
    "enclosure": [
        {"name": "プラスチック筐体", "spec": "ABS", "qty": 5, "price": 1500, "total": 7500},
        {"name": "防水シリコン", "spec": "2mm厚", "qty": 5, "price": 500, "total": 2500},
        {"name": "滑り止めゴムパッド", "spec": "3mm厚", "qty": 5, "price": 200, "total": 1000},
        {"name": "ネジ・ボルト", "spec": "M3・M4", "qty": 1, "price": 300, "total": 300},
    ],
    "test_equipment": [
        {"name": "マルチメータ", "price": 3000},
        {"name": "オシロスコープ", "price": 15000},
        {"name": "ロジックアナライザ", "price": 5000},
        {"name": "電源供給ユニット", "price": 3000},
        {"name": "USBデバッグプローブ", "price": 5000},
    ]
}

# 合計計算
total_sensors = sum(p["total"] for p in parts_list["sensors"])
total_pcb = sum(p["total"] for p in parts_list["pcb"])
total_enclosure = sum(p["total"] for p in parts_list["enclosure"])
total_test = sum(p["price"] for p in parts_list["test_equipment"])

print(f"センサー部品: ¥{total_sensors:,}")
print(f"PCB・基板: ¥{total_pcb:,}")
print(f"筐体・機械部品: ¥{total_enclosure:,}")
print(f"テスト機器: ¥{total_test:,}")
print(f"合計: ¥{total_sensors + total_pcb + total_enclosure + total_test:,}")
```

## 部品調達リスト（ShoppingList形式）

```
【センサー部品】
☐ 感圧センサー FSR 402 × 5個 (¥1,500) - Interlink Electronics
☐ MEMS加速度計 BMI160 × 2個 (¥1,600) - Bosch Sensortec
☐ マイコン nRF52840 DK × 1個 (¥8,000) - Nordic Semiconductor
☐ リチウムイオン電池 18650 × 2個 (¥1,000) - パナソニック
☐ 充電制御IC TP4056 × 1個 (¥200) - 秋月電子
☐ 電圧レギュレータ AMS1117 × 1個 (¥100) - 秋月電子
☐ オペアンプ TL072 × 2個 (¥200) - 秋月電子

【PCB・基板関連】
☐ PCB基板 4層基板 × 5枚 (¥3,000) - JLCPCB
☐ はんだ付け工具 (¥2,000) - 秋月電子
☐ コネクタ USB-C・ピンヘッダ (¥500) - 秋月電子

【筐体・機械部品】
☐ プラスチック筐体 ABS × 5個 (¥7,500) - 3Dプリント業者
☐ 防水シリコン 2mm厚 × 5枚 (¥2,500) - Amazon
☐ 滑り止めゴムパッド 3mm厚 × 5セット (¥1,000) - Amazon
☐ ネジ・ボルト M3・M4 (¥300) - 秋月電子

【テスト機器】
☐ マルチメータ (¥3,000) - Amazon
☐ オシロスコープ USB型 (¥15,000) - Amazon
☐ ロジックアナライザ (¥5,000) - Amazon
☐ 電源供給ユニット (¥3,000) - Amazon
☐ USBデバッグプローブ J-Link (¥5,000) - 秋月電子
```
