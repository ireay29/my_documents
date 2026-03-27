# PetSense アプリ全体設計ドキュメント

**プロジェクト名**: PetSense（ペットセンス）  
**バージョン**: 1.0 (MVP)  
**最終更新**: 2026年3月25日  
**作成者**: Manus AI

---

## 目次

1. [エグゼクティブサマリー](#エグゼクティブサマリー)
2. [プロジェクト概要](#プロジェクト概要)
3. [システムアーキテクチャ](#システムアーキテクチャ)
4. [データモデル](#データモデル)
5. [画面フロー](#画面フロー)
6. [API 仕様](#api-仕様)
7. [技術スタック](#技術スタック)
8. [セキュリティ](#セキュリティ)
9. [パフォーマンス](#パフォーマンス)
10. [テスト戦略](#テスト戦略)
11. [デプロイメント](#デプロイメント)
12. [今後の拡張](#今後の拡張)

---

## エグゼクティブサマリー

PetSense は、犬と猫の健康管理を一元化するスマートペットヘルスケアプラットフォームです。本設計ドキュメントは、React Native を用いた iOS/Android 対応のモバイルアプリケーションの全体構成を定義します。

**主要な特徴**:

- **マルチペット対応**: 1つのアカウントで複数のペットを管理可能
- **スマートデバイス統合**: スマートシーツ、給餌器、活動量計などと連携
- **尿・フン分析**: AI による自動健康判定
- **家族共有**: 複数ユーザーによるリアルタイム同期
- **獣医師連携**: 健康データの自動共有と相談機能

**対象ユーザー**: ペット飼育者（特に犬・猫の飼い主）、獣医師、保険会社

**開発期間**: 12ヶ月（短期 3ヶ月 + 中期 3ヶ月 + 長期 6ヶ月）

---

## プロジェクト概要

### ビジネスゴール

PetSense は、ペット関連市場（1兆9,108億円）の中でも、特に「犬用の体調管理ツール」という未開拓市場を開拓することを目的としています。既存の猫専用サービス（Catlog、Toletta など）と異なり、犬のシーツ派、散歩派、多頭飼い世帯に対応した、包括的なペットヘルスケアプラットフォームを構築します。

### 機能スコープ（フェーズ1 MVP）

**フェーズ1 の主要機能**:

1. **ユーザー管理**: ユーザー登録、ログイン、プロフィール管理
2. **ペット管理**: ペット登録、プロフィール編集、複数ペット対応
3. **デバイス管理**: スマートシーツ、スマートベース、給餌器などの登録・管理
4. **計測記録**: 体重、尿、フンの記録（シンプル版）
5. **食事管理**: 食事記録の追加・編集
6. **メモ記録**: 日々の観察メモの記録
7. **ホーム画面**: ペットの健康状態の一覧表示
8. **アラート**: 異常検知時の通知（ローカル）
9. **設定**: ユーザー設定、デバイス設定、通知設定

**フェーズ1 の対象外機能**:

- 給餌器の自動制御（フェーズ2）
- カロリー自動計算（フェーズ2）
- 獣医師ネットワーク（フェーズ3）
- 保険連携（フェーズ3）
- 活動量計連動（フェーズ3）

### ターゲットユーザー

| ユーザータイプ | 特徴 | ペイン | PetSense の価値 |
| :--- | :--- | :--- | :--- |
| **犬飼い主（シーツ派）** | 小型犬、室内排泄 | 排泄の異常を見逃しやすい | スマートシーツで自動計測 |
| **犬飼い主（散歩派）** | 中・大型犬、屋外排泄 | 排泄データが分散 | スマホカメラで統合管理 |
| **猫飼い主** | 既存の猫用サービス利用者 | 複数ペット管理が複雑 | 犬猫兼用プラットフォーム |
| **多頭飼い世帯** | 複数のペット | 個体ごとの健康管理が煩雑 | 一元管理、家族共有 |
| **獣医師** | 動物病院勤務 | ペットの健康データが不足 | 飼い主から自動でデータ受信 |

---

## システムアーキテクチャ

### 全体構成図

```
┌─────────────────────────────────────────────────────────────┐
│                     PetSense アプリ                          │
│  (React Native: iOS/Android)                                 │
└─────────────────────────────────────────────────────────────┘
                              ↓
        ┌─────────────────────┴─────────────────────┐
        ↓                                            ↓
   ┌──────────────┐                         ┌──────────────┐
   │ ローカルDB   │                         │ バックエンド │
   │(SQLite)      │                         │   API        │
   └──────────────┘                         └──────────────┘
        ↓                                            ↓
   ┌──────────────┐                         ┌──────────────┐
   │ デバイス     │                         │ クラウドDB   │
   │(Bluetooth)   │                         │(PostgreSQL)  │
   └──────────────┘                         └──────────────┘
```

### レイヤーアーキテクチャ

```
┌─────────────────────────────────────────────┐
│          プレゼンテーション層                 │
│  (React Components, Navigation, Screens)    │
└─────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────┐
│          ビジネスロジック層                   │
│  (Hooks, Context, State Management)         │
└─────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────┐
│          データアクセス層                     │
│  (SQLite, API Client, Device Communication) │
└─────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────┐
│          外部インテグレーション層             │
│  (Bluetooth, Cloud API, Analytics)          │
└─────────────────────────────────────────────┘
```

### フェーズ別アーキテクチャの進化

**フェーズ1（現在）**: ローカルDB + ローカル通知

**フェーズ2**: ローカルDB + バックエンド API + リアルタイム同期

**フェーズ3**: クラウドDB + マイクロサービス + AI エンジン

---

## データモデル

### 1. ユーザーモデル

```typescript
interface User {
  id: string;                    // UUID
  email: string;                 // メールアドレス
  password_hash: string;         // パスワードハッシュ
  name: string;                  // ユーザー名
  profile_image_url?: string;    // プロフィール画像
  phone?: string;                // 電話番号
  address?: string;              // 住所
  created_at: string;            // 作成日時 (ISO 8601)
  updated_at: string;            // 更新日時 (ISO 8601)
  is_active: boolean;            // アクティブフラグ
}
```

### 2. ペットモデル

```typescript
interface Pet {
  id: string;                    // UUID
  owner_id: string;              // オーナーID (FK: User)
  name: string;                  // ペット名
  type: 'dog' | 'cat' | 'other'; // ペットの種類
  breed?: string;                // 犬種/猫種
  birth_date: string;            // 生年月日 (YYYY-MM-DD)
  gender: 'male' | 'female';     // 性別
  weight?: number;               // 体重 (kg)
  color?: string;                // 毛色
  microchip_id?: string;         // マイクロチップID
  profile_image_url?: string;    // プロフィール画像
  medical_history?: string;      // 医療履歴
  allergies?: string;            // アレルギー情報
  created_at: string;            // 作成日時
  updated_at: string;            // 更新日時
}
```

### 3. デバイスモデル

```typescript
interface Device {
  id: string;                    // UUID
  owner_id: string;              // オーナーID (FK: User)
  name: string;                  // デバイス名（例: "リビングのシーツ"）
  type: 'scale' | 'urine_sensor' | 'camera' | 'feeder' | 'activity_tracker';
  model: string;                 // デバイスモデル名
  serial_number: string;         // シリアルナンバー
  mac_address?: string;          // MAC アドレス (Bluetooth)
  firmware_version: string;      // ファームウェアバージョン
  battery_level?: number;        // バッテリー残量 (%)
  last_sync_time?: string;       // 最終同期時刻
  is_connected: boolean;         // 接続状態
  created_at: string;            // 作成日時
  updated_at: string;            // 更新日時
}
```

### 4. ペット・デバイス紐付けモデル

```typescript
interface PetDevice {
  id: string;                    // UUID
  pet_id: string;                // ペットID (FK: Pet)
  device_id: string;             // デバイスID (FK: Device)
  is_primary: boolean;           // プライマリデバイスフラグ
  created_at: string;            // 作成日時
}
```

### 5. 計測記録モデル

```typescript
interface Measurement {
  id: string;                    // UUID
  pet_id: string;                // ペットID (FK: Pet)
  device_id?: string;            // デバイスID (FK: Device) - 自動計測の場合
  measurement_type: 'urine' | 'feces' | 'weight';
  
  // 尿計測
  urine_ph?: number;             // pH値 (6.0-7.0 が正常)
  urine_specific_gravity?: number; // 比重 (1.015-1.045 が正常)
  urine_protein?: 'negative' | 'trace' | 'positive';
  urine_ketone?: 'negative' | 'trace' | 'positive';
  urine_wbc?: 'negative' | 'trace' | 'positive';
  urine_rbc?: 'negative' | 'trace' | 'positive';
  
  // フン計測
  feces_bristol_scale?: number;  // ブリストル・スケール (1-7)
  feces_color?: string;          // 色 (例: "brown", "black", "pale")
  feces_shape?: string;          // 形状 (例: "normal", "soft", "hard")
  
  // 体重計測
  weight?: number;               // 体重 (kg)
  
  measured_at: string;           // 計測日時
  created_at: string;            // 作成日時
}
```

### 6. 食事記録モデル

```typescript
interface Meal {
  id: string;                    // UUID
  pet_id: string;                // ペットID (FK: Pet)
  meal_type: 'breakfast' | 'lunch' | 'dinner' | 'snack';
  food_name: string;             // 食事名
  amount: number;                // 量 (g)
  calories?: number;             // カロリー (kcal)
  notes?: string;                // メモ
  fed_at: string;                // 給餌日時
  created_at: string;            // 作成日時
}
```

### 7. メモ記録モデル

```typescript
interface Note {
  id: string;                    // UUID
  pet_id: string;                // ペットID (FK: Pet)
  title: string;                 // タイトル
  content: string;               // 内容
  image_url?: string;            // 画像URL
  tags?: string[];               // タグ (例: ["behavior", "health"])
  created_at: string;            // 作成日時
  updated_at: string;            // 更新日時
}
```

### 8. アラートモデル

```typescript
interface Alert {
  id: string;                    // UUID
  pet_id: string;                // ペットID (FK: Pet)
  alert_type: 'urine_abnormality' | 'feces_abnormality' | 'weight_change' | 'frequency_change';
  severity: 'info' | 'warning' | 'critical';
  title: string;                 // アラートタイトル
  description: string;           // アラート説明
  measurement_id?: string;       // 関連する計測記録ID
  is_read: boolean;              // 既読フラグ
  created_at: string;            // 作成日時
}
```

### 9. ユーザー・ペット紐付けモデル（家族共有用）

```typescript
interface PetUser {
  id: string;                    // UUID
  pet_id: string;                // ペットID (FK: Pet)
  user_id: string;               // ユーザーID (FK: User)
  role: 'owner' | 'caretaker' | 'viewer';
  permissions: {
    can_view: boolean;           // 閲覧可能
    can_edit: boolean;           // 編集可能
    can_manage_users: boolean;   // ユーザー管理可能
  };
  created_at: string;            // 作成日時
}
```

### 10. 正常範囲定義モデル

```typescript
interface NormalRange {
  id: string;                    // UUID
  pet_type: 'dog' | 'cat';
  age_range: {
    min: number;                 // 最小年齢 (月)
    max: number;                 // 最大年齢 (月)
  };
  
  // 尿の正常範囲
  urine_ph_min: number;
  urine_ph_max: number;
  urine_specific_gravity_min: number;
  urine_specific_gravity_max: number;
  
  // フンの正常範囲
  feces_bristol_scale_min: number;
  feces_bristol_scale_max: number;
  
  // 体重の正常範囲（犬種別）
  weight_min?: number;
  weight_max?: number;
  
  created_at: string;
}
```

### データベーススキーマ（SQLite）

```sql
-- ユーザーテーブル
CREATE TABLE users (
  id TEXT PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  password_hash TEXT NOT NULL,
  name TEXT NOT NULL,
  profile_image_url TEXT,
  phone TEXT,
  address TEXT,
  created_at TEXT NOT NULL,
  updated_at TEXT NOT NULL,
  is_active BOOLEAN DEFAULT 1
);

-- ペットテーブル
CREATE TABLE pets (
  id TEXT PRIMARY KEY,
  owner_id TEXT NOT NULL,
  name TEXT NOT NULL,
  type TEXT NOT NULL,
  breed TEXT,
  birth_date TEXT,
  gender TEXT NOT NULL,
  weight REAL,
  color TEXT,
  microchip_id TEXT,
  profile_image_url TEXT,
  medical_history TEXT,
  allergies TEXT,
  created_at TEXT NOT NULL,
  updated_at TEXT NOT NULL,
  FOREIGN KEY (owner_id) REFERENCES users(id)
);

-- デバイステーブル
CREATE TABLE devices (
  id TEXT PRIMARY KEY,
  owner_id TEXT NOT NULL,
  name TEXT NOT NULL,
  type TEXT NOT NULL,
  model TEXT NOT NULL,
  serial_number TEXT NOT NULL,
  mac_address TEXT,
  firmware_version TEXT NOT NULL,
  battery_level INTEGER,
  last_sync_time TEXT,
  is_connected BOOLEAN DEFAULT 0,
  created_at TEXT NOT NULL,
  updated_at TEXT NOT NULL,
  FOREIGN KEY (owner_id) REFERENCES users(id)
);

-- ペット・デバイス紐付けテーブル
CREATE TABLE pet_devices (
  id TEXT PRIMARY KEY,
  pet_id TEXT NOT NULL,
  device_id TEXT NOT NULL,
  is_primary BOOLEAN DEFAULT 0,
  created_at TEXT NOT NULL,
  FOREIGN KEY (pet_id) REFERENCES pets(id),
  FOREIGN KEY (device_id) REFERENCES devices(id)
);

-- 計測記録テーブル
CREATE TABLE measurements (
  id TEXT PRIMARY KEY,
  pet_id TEXT NOT NULL,
  device_id TEXT,
  measurement_type TEXT NOT NULL,
  urine_ph REAL,
  urine_specific_gravity REAL,
  urine_protein TEXT,
  urine_ketone TEXT,
  urine_wbc TEXT,
  urine_rbc TEXT,
  feces_bristol_scale INTEGER,
  feces_color TEXT,
  feces_shape TEXT,
  weight REAL,
  measured_at TEXT NOT NULL,
  created_at TEXT NOT NULL,
  FOREIGN KEY (pet_id) REFERENCES pets(id),
  FOREIGN KEY (device_id) REFERENCES devices(id)
);

-- 食事記録テーブル
CREATE TABLE meals (
  id TEXT PRIMARY KEY,
  pet_id TEXT NOT NULL,
  meal_type TEXT NOT NULL,
  food_name TEXT NOT NULL,
  amount REAL NOT NULL,
  calories REAL,
  notes TEXT,
  fed_at TEXT NOT NULL,
  created_at TEXT NOT NULL,
  FOREIGN KEY (pet_id) REFERENCES pets(id)
);

-- メモ記録テーブル
CREATE TABLE notes (
  id TEXT PRIMARY KEY,
  pet_id TEXT NOT NULL,
  title TEXT NOT NULL,
  content TEXT NOT NULL,
  image_url TEXT,
  tags TEXT,
  created_at TEXT NOT NULL,
  updated_at TEXT NOT NULL,
  FOREIGN KEY (pet_id) REFERENCES pets(id)
);

-- アラートテーブル
CREATE TABLE alerts (
  id TEXT PRIMARY KEY,
  pet_id TEXT NOT NULL,
  alert_type TEXT NOT NULL,
  severity TEXT NOT NULL,
  title TEXT NOT NULL,
  description TEXT NOT NULL,
  measurement_id TEXT,
  is_read BOOLEAN DEFAULT 0,
  created_at TEXT NOT NULL,
  FOREIGN KEY (pet_id) REFERENCES pets(id),
  FOREIGN KEY (measurement_id) REFERENCES measurements(id)
);

-- ユーザー・ペット紐付けテーブル
CREATE TABLE pet_users (
  id TEXT PRIMARY KEY,
  pet_id TEXT NOT NULL,
  user_id TEXT NOT NULL,
  role TEXT NOT NULL,
  permissions TEXT NOT NULL,
  created_at TEXT NOT NULL,
  FOREIGN KEY (pet_id) REFERENCES pets(id),
  FOREIGN KEY (user_id) REFERENCES users(id)
);

-- 正常範囲定義テーブル
CREATE TABLE normal_ranges (
  id TEXT PRIMARY KEY,
  pet_type TEXT NOT NULL,
  age_range_min INTEGER NOT NULL,
  age_range_max INTEGER NOT NULL,
  urine_ph_min REAL,
  urine_ph_max REAL,
  urine_specific_gravity_min REAL,
  urine_specific_gravity_max REAL,
  feces_bristol_scale_min INTEGER,
  feces_bristol_scale_max INTEGER,
  weight_min REAL,
  weight_max REAL,
  created_at TEXT NOT NULL
);
```

---

## 画面フロー

### 1. ナビゲーション構造

```
┌─────────────────────────────────────────────────────────┐
│                    ボトムタブナビゲーション               │
├─────────────────────────────────────────────────────────┤
│  ホーム    │   記録    │   食事    │  アラート │  設定   │
└─────────────────────────────────────────────────────────┘
```

### 2. 各タブの画面構成

#### 2.1 ホーム（Home）

**画面構成**:
- ペット選択ドロップダウン
- ペットプロフィールカード（名前、画像、年齢）
- 健康ダッシュボード
  - 最新体重
  - 最新排尿・排便の状態
  - 本日の食事摂取量
  - 異常検知バナー（ある場合）
- クイックアクション（計測追加、食事記録、メモ追加）

**遷移先**:
- ペット詳細画面
- 計測追加画面
- 食事記録画面
- メモ追加画面

#### 2.2 記録（Records）

**画面構成**:
- 計測タイプ選択（尿 / フン / 体重）
- 計測記録一覧（時系列）
- グラフ表示（1週間 / 1ヶ月 / 3ヶ月）
- 計測追加ボタン

**遷移先**:
- 計測追加画面
- 計測詳細画面

#### 2.3 食事（Meals）

**画面構成**:
- 本日の食事記録
- 本日の摂取カロリー表示
- 食事記録一覧
- 食事追加ボタン

**遷移先**:
- 食事追加画面
- 食事詳細画面

#### 2.4 アラート（Alerts）

**画面構成**:
- アラート一覧（未読 / 既読）
- アラート詳細表示
- 獣医師への相談ボタン

**遷移先**:
- アラート詳細画面

#### 2.5 設定（Settings）

**画面構成**:
- ユーザープロフィール
- ペット管理
- デバイス管理
- 家族共有
- 通知設定
- アプリ設定

**遷移先**:
- プロフィール編集画面
- ペット管理画面
- デバイス管理画面
- 家族共有画面
- 通知設定画面

### 3. 主要な画面詳細

#### 計測追加画面（Measurement Add）

**フィールド**:
- 計測日時（デフォルト: 現在時刻）
- 計測タイプ（ラジオボタン: 尿 / フン / 体重）

**尿計測の場合**:
- pH値（数値入力: 6.0-7.0）
- 比重（数値入力: 1.015-1.045）
- タンパク質（ドロップダウン: 陰性 / 微量 / 陽性）
- ケトン体（ドロップダウン: 陰性 / 微量 / 陽性）
- 白血球（ドロップダウン: 陰性 / 微量 / 陽性）
- 赤血球（ドロップダウン: 陰性 / 微量 / 陽性）

**フン計測の場合**:
- ブリストル・スケール（スライダー: 1-7）
- 色（ドロップダウン: 正常 / 黒 / 淡色 / その他）
- 形状（ドロップダウン: 正常 / 軟便 / 硬便 / その他）

**体重計測の場合**:
- 体重（数値入力: kg）

**アクション**:
- 保存ボタン
- キャンセルボタン

#### 食事記録画面（Meal Add）

**フィールド**:
- 給餌日時（デフォルト: 現在時刻）
- 食事タイプ（ドロップダウン: 朝食 / 昼食 / 夕食 / おやつ）
- 食事名（テキスト入力）
- 量（数値入力: g）
- カロリー（数値入力: kcal、オプション）
- メモ（テキストエリア）

**アクション**:
- 保存ボタン
- キャンセルボタン

#### ペット管理画面（Pet Management）

**画面構成**:
- ペット一覧（カード表示）
- ペット追加ボタン
- ペット編集ボタン
- ペット削除ボタン

**ペット追加/編集フォーム**:
- ペット名
- ペットの種類（ドロップダウン: 犬 / 猫 / その他）
- 犬種/猫種
- 生年月日
- 性別
- 体重（初期値）
- 毛色
- マイクロチップID（オプション）
- プロフィール画像
- 医療履歴
- アレルギー情報

#### デバイス管理画面（Device Management）

**画面構成**:
- デバイス一覧（接続状態、バッテリー残量表示）
- デバイス追加ボタン
- デバイス詳細画面

**デバイス追加フロー**:
1. デバイスタイプ選択（スマートシーツ / スマートベース / 給餌器 / 活動量計）
2. Bluetooth ペアリング
3. デバイス名設定
4. ペットへの紐付け

---

## API 仕様

### 1. 認証 API

#### ユーザー登録

```
POST /api/v1/auth/register
Content-Type: application/json

Request:
{
  "email": "user@example.com",
  "password": "securepassword123",
  "name": "John Doe"
}

Response (201):
{
  "id": "uuid",
  "email": "user@example.com",
  "name": "John Doe",
  "token": "jwt_token",
  "created_at": "2026-03-25T10:00:00Z"
}
```

#### ログイン

```
POST /api/v1/auth/login
Content-Type: application/json

Request:
{
  "email": "user@example.com",
  "password": "securepassword123"
}

Response (200):
{
  "id": "uuid",
  "email": "user@example.com",
  "name": "John Doe",
  "token": "jwt_token"
}
```

### 2. ペット API

#### ペット一覧取得

```
GET /api/v1/pets
Authorization: Bearer {token}

Response (200):
{
  "data": [
    {
      "id": "uuid",
      "name": "Fluffy",
      "type": "cat",
      "breed": "Persian",
      "birth_date": "2020-01-15",
      "gender": "female",
      "weight": 4.5,
      "profile_image_url": "https://..."
    }
  ]
}
```

#### ペット作成

```
POST /api/v1/pets
Authorization: Bearer {token}
Content-Type: application/json

Request:
{
  "name": "Fluffy",
  "type": "cat",
  "breed": "Persian",
  "birth_date": "2020-01-15",
  "gender": "female",
  "weight": 4.5
}

Response (201):
{
  "id": "uuid",
  "name": "Fluffy",
  "type": "cat",
  "breed": "Persian",
  "birth_date": "2020-01-15",
  "gender": "female",
  "weight": 4.5,
  "created_at": "2026-03-25T10:00:00Z"
}
```

### 3. 計測記録 API

#### 計測記録作成

```
POST /api/v1/measurements
Authorization: Bearer {token}
Content-Type: application/json

Request:
{
  "pet_id": "uuid",
  "measurement_type": "urine",
  "urine_ph": 6.5,
  "urine_specific_gravity": 1.030,
  "urine_protein": "negative",
  "urine_ketone": "negative",
  "urine_wbc": "negative",
  "urine_rbc": "negative",
  "measured_at": "2026-03-25T10:00:00Z"
}

Response (201):
{
  "id": "uuid",
  "pet_id": "uuid",
  "measurement_type": "urine",
  "urine_ph": 6.5,
  "urine_specific_gravity": 1.030,
  "urine_protein": "negative",
  "urine_ketone": "negative",
  "urine_wbc": "negative",
  "urine_rbc": "negative",
  "measured_at": "2026-03-25T10:00:00Z",
  "created_at": "2026-03-25T10:00:00Z"
}
```

#### 計測記録一覧取得

```
GET /api/v1/pets/{pet_id}/measurements?type=urine&limit=30&offset=0
Authorization: Bearer {token}

Response (200):
{
  "data": [
    {
      "id": "uuid",
      "pet_id": "uuid",
      "measurement_type": "urine",
      "urine_ph": 6.5,
      "urine_specific_gravity": 1.030,
      "urine_protein": "negative",
      "urine_ketone": "negative",
      "urine_wbc": "negative",
      "urine_rbc": "negative",
      "measured_at": "2026-03-25T10:00:00Z",
      "created_at": "2026-03-25T10:00:00Z"
    }
  ],
  "total": 100,
  "limit": 30,
  "offset": 0
}
```

### 4. アラート API

#### アラート一覧取得

```
GET /api/v1/pets/{pet_id}/alerts?is_read=false
Authorization: Bearer {token}

Response (200):
{
  "data": [
    {
      "id": "uuid",
      "pet_id": "uuid",
      "alert_type": "urine_abnormality",
      "severity": "warning",
      "title": "尿pH異常",
      "description": "尿のpH値が正常範囲外です",
      "measurement_id": "uuid",
      "is_read": false,
      "created_at": "2026-03-25T10:00:00Z"
    }
  ]
}
```

---

## 技術スタック

### フロントエンド

| 層 | 技術 | バージョン | 用途 |
| :--- | :--- | :--- | :--- |
| **フレームワーク** | React Native | 0.73+ | クロスプラットフォーム開発 |
| **言語** | TypeScript | 5.0+ | 型安全性 |
| **ナビゲーション** | React Navigation | 6.0+ | 画面遷移管理 |
| **状態管理** | Context API + Hooks | - | ローカル状態管理 |
| **ローカルDB** | expo-sqlite | 11.0+ | SQLite データベース |
| **HTTP クライアント** | axios | 1.6+ | API 通信 |
| **UI コンポーネント** | React Native Paper | 5.0+ | Material Design |
| **グラフ表示** | react-native-chart-kit | 6.0+ | データ可視化 |
| **Bluetooth** | react-native-ble-plx | 2.0+ | デバイス通信 |
| **ローカル通知** | expo-notifications | 0.20+ | プッシュ通知 |
| **日時処理** | date-fns | 2.30+ | 日時フォーマット |
| **バリデーション** | zod | 3.22+ | スキーマバリデーション |

### バックエンド（フェーズ2以降）

| 層 | 技術 | バージョン | 用途 |
| :--- | :--- | :--- | :--- |
| **フレームワーク** | Node.js + Express | 18.0+ | REST API サーバー |
| **言語** | TypeScript | 5.0+ | 型安全性 |
| **データベース** | PostgreSQL | 14.0+ | クラウドDB |
| **ORM** | Prisma | 5.0+ | データベースアクセス |
| **認証** | JWT | - | トークンベース認証 |
| **リアルタイム同期** | Firebase Realtime DB | - | リアルタイム更新 |
| **キャッシング** | Redis | 7.0+ | セッション・キャッシュ |
| **メッセージング** | Bull | 4.0+ | ジョブキュー |
| **ロギング** | Winston | 3.0+ | ログ管理 |

### 開発ツール

| ツール | バージョン | 用途 |
| :--- | :--- | :--- |
| **パッケージマネージャー** | npm / yarn | 9.0+ |
| **ビルドツール** | Expo CLI | 49.0+ |
| **テストフレームワーク** | Jest | 29.0+ |
| **E2E テスト** | Detox | 20.0+ |
| **コード品質** | ESLint + Prettier | - |
| **バージョン管理** | Git | - |
| **CI/CD** | GitHub Actions | - |

---

## セキュリティ

### 1. 認証・認可

**実装方針**:

- JWT（JSON Web Token）によるトークンベース認証
- アクセストークン（有効期限: 1時間）とリフレッシュトークン（有効期限: 7日間）の分離
- パスワードは bcrypt でハッシュ化（salt rounds: 12）
- ロール・ベースのアクセス制御（RBAC）: owner / caretaker / viewer

### 2. データ保護

**実装方針**:

- すべての通信は HTTPS で暗号化
- ローカルDB のデータは暗号化（SQLCipher）
- 個人情報（メールアドレス、電話番号）は暗号化して保存
- 医療情報は最高レベルのセキュリティで保護

### 3. プライバシー

**実装方針**:

- ユーザーの明示的な同意なしに、データを第三者と共有しない
- GDPR、CCPA などの個人情報保護規制に準拠
- ユーザーは自分のデータを削除できる権利を持つ（Right to be forgotten）

### 4. API セキュリティ

**実装方針**:

- レート制限（1分間に 100 リクエスト）
- CORS（Cross-Origin Resource Sharing）の設定
- CSRF（Cross-Site Request Forgery）対策
- SQL インジェクション対策（パラメータ化クエリ）

---

## パフォーマンス

### 1. アプリケーションパフォーマンス

**目標値**:

- 初期起動時間: 3秒以下
- 画面遷移時間: 500ms 以下
- API レスポンス時間: 1秒以下
- メモリ使用量: 100MB 以下

**最適化方針**:

- コンポーネントの遅延ロード（Code Splitting）
- 画像の最適化（WebP、圧縮）
- キャッシング戦略（ローカルDB、メモリキャッシュ）
- バーチャルスクロール（大量データ表示時）

### 2. ネットワークパフォーマンス

**目標値**:

- API レスポンスサイズ: 100KB 以下
- ネットワークリクエスト数: 最小化

**最適化方針**:

- GraphQL の導入（オーバーフェッチ防止）
- バッチリクエスト（複数の API 呼び出しを 1つにまとめる）
- CDN の利用（画像、静的ファイル）

### 3. データベースパフォーマンス

**目標値**:

- クエリ実行時間: 100ms 以下
- インデックス戦略の最適化

**最適化方針**:

- 主要なカラムにインデックスを作成
- クエリプランの分析と最適化
- 定期的な VACUUM・ANALYZE の実行

---

## テスト戦略

### 1. ユニットテスト

**対象**: ビジネスロジック、ユーティリティ関数、ホック

**ツール**: Jest

**カバレッジ目標**: 80% 以上

**例**:
```typescript
describe('calculateNormalRange', () => {
  it('should return correct normal range for adult cat', () => {
    const result = calculateNormalRange('cat', 36);
    expect(result.urine_ph_min).toBe(6.0);
    expect(result.urine_ph_max).toBe(7.0);
  });
});
```

### 2. 統合テスト

**対象**: API 呼び出し、DB 操作、複数コンポーネント間の連携

**ツール**: Jest + Supertest

**例**:
```typescript
describe('POST /api/v1/measurements', () => {
  it('should create a measurement and trigger alert if abnormal', async () => {
    const response = await request(app)
      .post('/api/v1/measurements')
      .send({
        pet_id: 'uuid',
        measurement_type: 'urine',
        urine_ph: 8.5  // 異常値
      });
    
    expect(response.status).toBe(201);
    expect(response.body.id).toBeDefined();
    
    // アラートが作成されたか確認
    const alerts = await Alert.find({ pet_id: 'uuid' });
    expect(alerts.length).toBeGreaterThan(0);
  });
});
```

### 3. E2E テスト

**対象**: ユーザーフロー全体（ログイン → ペット登録 → 計測記録 → アラート表示）

**ツール**: Detox

**例**:
```typescript
describe('Pet health tracking flow', () => {
  it('should complete full user flow', async () => {
    // ログイン
    await element(by.id('email_input')).typeText('user@example.com');
    await element(by.id('password_input')).typeText('password123');
    await element(by.id('login_button')).tap();
    
    // ホーム画面が表示されるまで待機
    await waitFor(element(by.text('ホーム')))
      .toBeVisible()
      .withTimeout(5000);
    
    // ペット選択
    await element(by.id('pet_dropdown')).tap();
    await element(by.text('Fluffy')).tap();
    
    // 計測追加
    await element(by.id('add_measurement_button')).tap();
    await element(by.id('urine_ph_input')).typeText('6.5');
    await element(by.id('save_button')).tap();
    
    // 計測が保存されたか確認
    await expect(element(by.text('尿計測が保存されました'))).toBeVisible();
  });
});
```

### 4. パフォーマンステスト

**対象**: 初期起動時間、メモリ使用量、バッテリー消費量

**ツール**: React Native Debugger、Xcode Instruments

### 5. セキュリティテスト

**対象**: 認証・認可、データ暗号化、API セキュリティ

**ツール**: OWASP ZAP、Burp Suite

---

## デプロイメント

### 1. ビルドプロセス

**ステップ**:

1. コード品質チェック（ESLint、Prettier）
2. ユニットテスト実行
3. ビルド（Expo Build Service）
4. APK/IPA 生成

### 2. デプロイメント先

**iOS**: Apple App Store

**Android**: Google Play Store

### 3. リリース計画

**フェーズ1 MVP**: 3ヶ月後

**フェーズ2**: 6ヶ月後

**フェーズ3**: 12ヶ月後

### 4. ローリングアップデート

**戦略**: 段階的ロールアウト（10% → 50% → 100%）

---

## 今後の拡張

### フェーズ2（4-6ヶ月後）

- 給餌器連動ロジック
- カロリー自動計算
- レポート出力（PDF）
- 家族共有機能
- バックエンド API 構築

### フェーズ3（7-12ヶ月後）

- 活動量計連動
- 獣医師ネットワーク
- 保険連携
- AI ヘルスコーチ
- B2B ダッシュボード

### 長期的な拡張

- 多言語対応
- 地域別の正常範囲定義
- 機械学習による予測分析
- IoT デバイスの拡張（温度計、カメラなど）

---

## 結論

本設計ドキュメントは、PetSense の MVP（フェーズ1）の包括的な技術仕様を定義しています。データモデル、画面フロー、API 仕様、技術スタック、セキュリティ、テスト戦略を含む、実装に必要なすべての情報が含まれています。

このドキュメントに基づいて、開発チームは効率的かつ高品質なアプリケーションを構築できます。
