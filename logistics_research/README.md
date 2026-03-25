# 物流・運送業界 企業リサーチ

日本の運送・配送業界において「IT導入が進んでいない会社」「誤配送・取り間違いが起こりがちな会社」を調査・リスト化したリサーチ資料です。

## 調査の観点

- **IT未導入スコア（1〜5）**：求人票・導入事例・口コミから「現在も手書き・FAX管理が続いている」証拠の強さを評価
- **誤配送リスクスコア（1〜5）**：口コミフォーラムへの具体的なトラブル報告、業態・管理体制から推測されるリスクの高さを評価

## ディレクトリ構成

```
logistics_research/
├── 01_nationwide/              # 全国版（20社）
│   ├── japan_transport_company_list.xlsx   # 企業リスト（Excel）
│   └── data/
│       ├── research_raw.json   # 並列リサーチ生データ（JSON）
│       └── research_raw.csv    # 並列リサーチ生データ（CSV）
│
├── 02_kansai_v1/               # 関西版 初回（10社）
│   ├── kansai_transport_company_list.xlsx  # 企業リスト（Excel）
│   └── data/
│       ├── research_raw.json
│       └── research_raw.csv
│
├── 03_kansai_v2_large/         # 関西版 大量調査版（67社）
│   ├── kansai_transport_company_list_large.xlsx  # 企業リスト（Excel）
│   └── data/
│       ├── research_raw.json   # 25軸並列リサーチ生データ（JSON）
│       └── research_raw.csv    # 25軸並列リサーチ生データ（CSV）
│
└── scripts/                    # Excelリスト生成スクリプト
    ├── create_nationwide_list.py       # 全国版生成スクリプト
    ├── create_kansai_v1_list.py        # 関西版v1生成スクリプト
    └── create_kansai_v2_large_list.py  # 関西版v2（大量版）生成スクリプト
```

## 各バージョンの概要

| バージョン | 対象地域 | 企業数 | 調査軸数 |
|-----------|---------|--------|---------|
| 01_nationwide | 全国 | 20社 | 8軸 |
| 02_kansai_v1 | 関西（大阪・兵庫・京都・滋賀・奈良・和歌山） | 10社 | 8軸 |
| 03_kansai_v2_large | 関西（同上） | 67社 | 25軸 |

## 調査方法

- IT導入事例サイト（アセンド/ロジックス、hacobell、logizard-zero、Tradiss 等）
- 口コミサイト（エン転職、転職会議、OpenWork、就活会議）
- 求人票（Indeed、はたらいく、バイトル、タウンワーク、ハローワーク 等）
- Amazonセラーセントラルフォーラム
- 近畿トラック協会・国土交通省・中小企業庁の公開資料

## 注意事項

本リストは公開情報をもとに作成したものです。IT導入状況は変化する場合があります（特に「導入事例」として掲載されている企業は現在改善が進んでいる可能性があります）。アプローチ時は最新状況を確認の上ご活用ください。
