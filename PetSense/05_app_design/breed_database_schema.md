# 犬種・猫種マスターデータベース設計

## 概要

PetSense アプリでペット登録時に、ユーザーが犬種・猫種をドロップダウンから選択できるようにするためのマスターデータベースです。各犬種・猫種には、平均体重、平均寿命、特性、栄養基準などの情報を含めることで、ユーザーの利便性と健康管理の精度を向上させます。

---

## データモデル設計

### 1. 犬種テーブル（Breeds - Dogs）

```typescript
interface DogBreed {
  id: string;                    // UUID
  name: string;                  // 犬種名（日本語）
  name_en: string;               // 犬種名（英語）
  fci_group: string;             // FCI グループ（例: "Group 1: Sheepdogs and Cattle Dogs"）
  fci_section: string;           // FCI セクション
  origin_country: string;        // 原産国
  
  // 体格情報
  size_category: 'toy' | 'small' | 'medium' | 'large' | 'giant';
  weight_male_min: number;       // オスの最小体重 (kg)
  weight_male_max: number;       // オスの最大体重 (kg)
  weight_female_min: number;     // メスの最小体重 (kg)
  weight_female_max: number;     // メスの最大体重 (kg)
  height_male_min: number;       // オスの最小体高 (cm)
  height_male_max: number;       // オスの最大体高 (cm)
  height_female_min: number;     // メスの最小体高 (cm)
  height_female_max: number;     // メスの最大体高 (cm)
  
  // 寿命・成長
  average_lifespan_min: number;  // 平均寿命（最小）
  average_lifespan_max: number;  // 平均寿命（最大）
  maturity_age_months: number;   // 成犬に達する月齢
  
  // 栄養基準
  daily_calorie_per_kg: number;  // 1kg あたりの1日必要カロリー (kcal/kg)
  protein_percentage: number;    // タンパク質の推奨割合 (%)
  fat_percentage: number;        // 脂肪の推奨割合 (%)
  
  // 特性
  temperament: string[];         // 気質（例: ["friendly", "intelligent", "active"]）
  activity_level: 'low' | 'moderate' | 'high' | 'very_high';
  grooming_needs: 'low' | 'moderate' | 'high';
  shedding_level: 'low' | 'moderate' | 'high';
  
  // 健康情報
  common_health_issues: string[]; // よくある健康問題
  genetic_predispositions: string[]; // 遺伝的素因
  
  // その他
  description: string;           // 説明
  image_url?: string;            // 犬種の画像
  is_active: boolean;            // アクティブフラグ
  created_at: string;            // 作成日時
  updated_at: string;            // 更新日時
}
```

### 2. 猫種テーブル（Breeds - Cats）

```typescript
interface CatBreed {
  id: string;                    // UUID
  name: string;                  // 猫種名（日本語）
  name_en: string;               // 猫種名（英語）
  fife_category: string;         // FIFE カテゴリ（例: "Longhair"）
  origin_country: string;        // 原産国
  
  // 体格情報
  size_category: 'small' | 'medium' | 'large';
  weight_male_min: number;       // オスの最小体重 (kg)
  weight_male_max: number;       // オスの最大体重 (kg)
  weight_female_min: number;     // メスの最小体重 (kg)
  weight_female_max: number;     // メスの最大体重 (kg)
  
  // 寿命・成長
  average_lifespan_min: number;  // 平均寿命（最小）
  average_lifespan_max: number;  // 平均寿命（最大）
  maturity_age_months: number;   // 成猫に達する月齢
  
  // 栄養基準
  daily_calorie_per_kg: number;  // 1kg あたりの1日必要カロリー (kcal/kg)
  protein_percentage: number;    // タンパク質の推奨割合 (%)
  fat_percentage: number;        // 脂肪の推奨割合 (%)
  
  // 特性
  temperament: string[];         // 気質
  activity_level: 'low' | 'moderate' | 'high';
  grooming_needs: 'low' | 'moderate' | 'high';
  shedding_level: 'low' | 'moderate' | 'high';
  
  // 健康情報
  common_health_issues: string[]; // よくある健康問題
  genetic_predispositions: string[]; // 遺伝的素因
  
  // その他
  description: string;           // 説明
  image_url?: string;            // 猫種の画像
  is_active: boolean;            // アクティブフラグ
  created_at: string;            // 作成日時
  updated_at: string;            // 更新日時
}
```

### 3. 正常範囲テーブル（拡張版）

```typescript
interface NormalRangeExtended {
  id: string;                    // UUID
  pet_type: 'dog' | 'cat';
  breed_id?: string;             // 犬種/猫種ID（オプション）
  age_range_months_min: number;  // 月齢範囲（最小）
  age_range_months_max: number;  // 月齢範囲（最大）
  
  // 尿の正常範囲
  urine_ph_min: number;
  urine_ph_max: number;
  urine_specific_gravity_min: number;
  urine_specific_gravity_max: number;
  
  // フンの正常範囲
  feces_bristol_scale_min: number;
  feces_bristol_scale_max: number;
  
  // 体重の正常範囲
  weight_min: number;
  weight_max: number;
  
  // 排泄の正常範囲
  urination_frequency_min: number; // 1日の排尿回数（最小）
  urination_frequency_max: number; // 1日の排尿回数（最大）
  defecation_frequency_min: number; // 1日の排便回数（最小）
  defecation_frequency_max: number; // 1日の排便回数（最大）
  
  created_at: string;
}
```

---

## SQL スキーマ

### 犬種テーブル

```sql
CREATE TABLE dog_breeds (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  name_en TEXT NOT NULL,
  fci_group TEXT,
  fci_section TEXT,
  origin_country TEXT,
  
  size_category TEXT NOT NULL,
  weight_male_min REAL,
  weight_male_max REAL,
  weight_female_min REAL,
  weight_female_max REAL,
  height_male_min REAL,
  height_male_max REAL,
  height_female_min REAL,
  height_female_max REAL,
  
  average_lifespan_min INTEGER,
  average_lifespan_max INTEGER,
  maturity_age_months INTEGER,
  
  daily_calorie_per_kg REAL,
  protein_percentage REAL,
  fat_percentage REAL,
  
  temperament TEXT,
  activity_level TEXT,
  grooming_needs TEXT,
  shedding_level TEXT,
  
  common_health_issues TEXT,
  genetic_predispositions TEXT,
  
  description TEXT,
  image_url TEXT,
  is_active BOOLEAN DEFAULT 1,
  created_at TEXT NOT NULL,
  updated_at TEXT NOT NULL
);

CREATE INDEX idx_dog_breeds_name ON dog_breeds(name);
CREATE INDEX idx_dog_breeds_size ON dog_breeds(size_category);
```

### 猫種テーブル

```sql
CREATE TABLE cat_breeds (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  name_en TEXT NOT NULL,
  fife_category TEXT,
  origin_country TEXT,
  
  size_category TEXT NOT NULL,
  weight_male_min REAL,
  weight_male_max REAL,
  weight_female_min REAL,
  weight_female_max REAL,
  
  average_lifespan_min INTEGER,
  average_lifespan_max INTEGER,
  maturity_age_months INTEGER,
  
  daily_calorie_per_kg REAL,
  protein_percentage REAL,
  fat_percentage REAL,
  
  temperament TEXT,
  activity_level TEXT,
  grooming_needs TEXT,
  shedding_level TEXT,
  
  common_health_issues TEXT,
  genetic_predispositions TEXT,
  
  description TEXT,
  image_url TEXT,
  is_active BOOLEAN DEFAULT 1,
  created_at TEXT NOT NULL,
  updated_at TEXT NOT NULL
);

CREATE INDEX idx_cat_breeds_name ON cat_breeds(name);
CREATE INDEX idx_cat_breeds_size ON cat_breeds(size_category);
```

### 正常範囲テーブル（拡張版）

```sql
CREATE TABLE normal_ranges_extended (
  id TEXT PRIMARY KEY,
  pet_type TEXT NOT NULL,
  breed_id TEXT,
  age_range_months_min INTEGER NOT NULL,
  age_range_months_max INTEGER NOT NULL,
  
  urine_ph_min REAL,
  urine_ph_max REAL,
  urine_specific_gravity_min REAL,
  urine_specific_gravity_max REAL,
  
  feces_bristol_scale_min INTEGER,
  feces_bristol_scale_max INTEGER,
  
  weight_min REAL,
  weight_max REAL,
  
  urination_frequency_min INTEGER,
  urination_frequency_max INTEGER,
  defecation_frequency_min INTEGER,
  defecation_frequency_max INTEGER,
  
  created_at TEXT NOT NULL,
  FOREIGN KEY (breed_id) REFERENCES dog_breeds(id) OR FOREIGN KEY (breed_id) REFERENCES cat_breeds(id)
);

CREATE INDEX idx_normal_ranges_pet_type ON normal_ranges_extended(pet_type);
CREATE INDEX idx_normal_ranges_breed ON normal_ranges_extended(breed_id);
```

---

## 初期データ（サンプル）

### 犬種データ（主要な20品種）

| 犬種名 | 英名 | サイズ | オス体重(kg) | メス体重(kg) | 平均寿命 | 活動レベル | 1日必要カロリー |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| トイプードル | Toy Poodle | toy | 2-3 | 2-3 | 12-15 | moderate | 90-110 |
| チワワ | Chihuahua | toy | 1.5-3 | 1.5-3 | 12-18 | moderate | 80-100 |
| ダックスフンド | Dachshund | small | 7-14 | 7-14 | 12-16 | moderate | 100-130 |
| ビーグル | Beagle | small | 10-15 | 10-15 | 12-15 | high | 120-150 |
| コーギー | Corgi | small | 11-15 | 11-15 | 12-13 | high | 130-160 |
| 柴犬 | Shiba Inu | medium | 9-14 | 8-13 | 12-15 | moderate | 110-140 |
| ビション・フリーゼ | Bichon Frise | small | 5-8 | 5-8 | 12-15 | moderate | 100-120 |
| ゴールデン・レトリバー | Golden Retriever | large | 29-34 | 25-30 | 10-12 | high | 1200-1500 |
| ラブラドール・レトリバー | Labrador Retriever | large | 27-36 | 25-32 | 10-12 | very_high | 1200-1500 |
| ジャーマン・シェパード | German Shepherd | large | 30-40 | 22-32 | 9-13 | very_high | 1300-1600 |
| ボーダー・コリー | Border Collie | medium | 18-22 | 18-22 | 12-15 | very_high | 140-180 |
| ポメラニアン | Pomeranian | toy | 1.4-3.2 | 1.4-3.2 | 12-16 | moderate | 80-100 |
| ヨークシャー・テリア | Yorkshire Terrier | toy | 2-3.2 | 2-3.2 | 11-15 | moderate | 90-110 |
| フレンチ・ブルドッグ | French Bulldog | small | 9-14 | 8-13 | 10-14 | low | 100-130 |
| パグ | Pug | small | 6-8 | 6-8 | 12-15 | low | 100-120 |
| ビーグル・ミックス | Beagle Mix | small | 10-15 | 10-15 | 12-15 | high | 120-150 |
| マルチーズ | Maltese | toy | 2-3 | 2-3 | 12-15 | low | 80-100 |
| ミニチュア・シュナウザー | Miniature Schnauzer | small | 6-8 | 6-8 | 12-14 | moderate | 110-130 |
| シーズー | Shih Tzu | small | 4.5-8 | 4.5-8 | 10-18 | low | 100-120 |
| キャバリア・キング・チャールズ・スパニエル | Cavalier King Charles Spaniel | small | 13-18 | 13-18 | 9-14 | moderate | 130-160 |

### 猫種データ（主要な15品種）

| 猫種名 | 英名 | サイズ | オス体重(kg) | メス体重(kg) | 平均寿命 | 活動レベル | 1日必要カロリー |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| スコティッシュ・フォールド | Scottish Fold | medium | 3-5 | 2.5-4 | 11-14 | moderate | 40-50 |
| ペルシャ | Persian | medium | 3.5-6 | 2.5-4.5 | 10-17 | low | 40-50 |
| メインクーン | Maine Coon | large | 5.5-11 | 4-6.5 | 9-15 | moderate | 50-70 |
| ラグドール | Ragdoll | large | 4.5-9 | 3.5-6 | 12-17 | moderate | 45-60 |
| ベンガル | Bengal | medium | 4-7 | 3-5.5 | 10-16 | high | 50-65 |
| アビシニアン | Abyssinian | medium | 3.5-5.5 | 2.5-4.5 | 9-15 | very_high | 50-65 |
| シャム | Siamese | medium | 3-5.5 | 2.5-4.5 | 8-15 | high | 45-60 |
| ブリティッシュ・ショートヘア | British Shorthair | medium | 4-8 | 3-6 | 12-17 | low | 45-55 |
| ロシアンブルー | Russian Blue | small | 3-5 | 2.5-3.5 | 11-20 | moderate | 40-50 |
| ノルウェージャン・フォレスト・キャット | Norwegian Forest Cat | large | 4.5-9 | 3.5-6 | 12-16 | moderate | 50-65 |
| ラパーマ | LaPerm | small | 2.5-4 | 2-3 | 10-15 | high | 40-50 |
| ボンベイ | Bombay | medium | 3-6 | 2.5-3.5 | 13-20 | moderate | 45-55 |
| ビルマ | Burmese | medium | 3.5-6 | 2.5-3.5 | 10-18 | moderate | 45-55 |
| オリエンタル | Oriental | small | 2.5-4.5 | 2-3.5 | 9-15 | high | 40-50 |
| ソマリ | Somali | medium | 3.5-5 | 2.5-3.5 | 11-16 | very_high | 50-65 |

---

## 初期データ挿入スクリプト（TypeScript）

```typescript
import Database from 'expo-sqlite';

const db = Database.openDatabase('petsense.db');

export const initializeBreedData = async () => {
  try {
    // 犬種データの挿入
    const dogBreeds = [
      {
        id: 'breed_dog_001',
        name: 'トイプードル',
        name_en: 'Toy Poodle',
        fci_group: 'Group 9: Companion and Toy Dogs',
        fci_section: 'Section 2: Poodles',
        origin_country: 'France',
        size_category: 'toy',
        weight_male_min: 2,
        weight_male_max: 3,
        weight_female_min: 2,
        weight_female_max: 3,
        height_male_min: 24,
        height_male_max: 28,
        height_female_min: 24,
        height_female_max: 28,
        average_lifespan_min: 12,
        average_lifespan_max: 15,
        maturity_age_months: 12,
        daily_calorie_per_kg: 45,
        protein_percentage: 18,
        fat_percentage: 12,
        temperament: JSON.stringify(['friendly', 'intelligent', 'active']),
        activity_level: 'moderate',
        grooming_needs: 'high',
        shedding_level: 'low',
        common_health_issues: JSON.stringify(['patellar luxation', 'eye problems']),
        genetic_predispositions: JSON.stringify(['hip dysplasia']),
        description: '小型で知能が高く、人懐っこい犬種です。毎日のグルーミングが必要です。',
        image_url: null,
        is_active: 1,
        created_at: new Date().toISOString(),
        updated_at: new Date().toISOString(),
      },
      // ... その他の犬種
    ];

    for (const breed of dogBreeds) {
      await db.execAsync(
        `INSERT INTO dog_breeds (
          id, name, name_en, fci_group, fci_section, origin_country,
          size_category, weight_male_min, weight_male_max, weight_female_min, weight_female_max,
          height_male_min, height_male_max, height_female_min, height_female_max,
          average_lifespan_min, average_lifespan_max, maturity_age_months,
          daily_calorie_per_kg, protein_percentage, fat_percentage,
          temperament, activity_level, grooming_needs, shedding_level,
          common_health_issues, genetic_predispositions,
          description, image_url, is_active, created_at, updated_at
        ) VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)`,
        [
          breed.id, breed.name, breed.name_en, breed.fci_group, breed.fci_section, breed.origin_country,
          breed.size_category, breed.weight_male_min, breed.weight_male_max, breed.weight_female_min, breed.weight_female_max,
          breed.height_male_min, breed.height_male_max, breed.height_female_min, breed.height_female_max,
          breed.average_lifespan_min, breed.average_lifespan_max, breed.maturity_age_months,
          breed.daily_calorie_per_kg, breed.protein_percentage, breed.fat_percentage,
          breed.temperament, breed.activity_level, breed.grooming_needs, breed.shedding_level,
          breed.common_health_issues, breed.genetic_predispositions,
          breed.description, breed.image_url, breed.is_active, breed.created_at, breed.updated_at
        ]
      );
    }

    // 猫種データの挿入も同様に実施
    const catBreeds = [
      {
        id: 'breed_cat_001',
        name: 'スコティッシュ・フォールド',
        name_en: 'Scottish Fold',
        fife_category: 'Longhair',
        origin_country: 'Scotland',
        size_category: 'medium',
        weight_male_min: 3,
        weight_male_max: 5,
        weight_female_min: 2.5,
        weight_female_max: 4,
        average_lifespan_min: 11,
        average_lifespan_max: 14,
        maturity_age_months: 12,
        daily_calorie_per_kg: 50,
        protein_percentage: 26,
        fat_percentage: 9,
        temperament: JSON.stringify(['sweet', 'calm', 'affectionate']),
        activity_level: 'moderate',
        grooming_needs: 'high',
        shedding_level: 'moderate',
        common_health_issues: JSON.stringify(['ear problems', 'polycystic kidney disease']),
        genetic_predispositions: JSON.stringify(['osteochondrodysplasia']),
        description: '折れた耳が特徴的な猫種です。穏やかで人懐っこい性格です。',
        image_url: null,
        is_active: 1,
        created_at: new Date().toISOString(),
        updated_at: new Date().toISOString(),
      },
      // ... その他の猫種
    ];

    for (const breed of catBreeds) {
      await db.execAsync(
        `INSERT INTO cat_breeds (
          id, name, name_en, fife_category, origin_country,
          size_category, weight_male_min, weight_male_max, weight_female_min, weight_female_max,
          average_lifespan_min, average_lifespan_max, maturity_age_months,
          daily_calorie_per_kg, protein_percentage, fat_percentage,
          temperament, activity_level, grooming_needs, shedding_level,
          common_health_issues, genetic_predispositions,
          description, image_url, is_active, created_at, updated_at
        ) VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)`,
        [
          breed.id, breed.name, breed.name_en, breed.fife_category, breed.origin_country,
          breed.size_category, breed.weight_male_min, breed.weight_male_max, breed.weight_female_min, breed.weight_female_max,
          breed.average_lifespan_min, breed.average_lifespan_max, breed.maturity_age_months,
          breed.daily_calorie_per_kg, breed.protein_percentage, breed.fat_percentage,
          breed.temperament, breed.activity_level, breed.grooming_needs, breed.shedding_level,
          breed.common_health_issues, breed.genetic_predispositions,
          breed.description, breed.image_url, breed.is_active, breed.created_at, breed.updated_at
        ]
      );
    }

    console.log('Breed data initialized successfully');
  } catch (error) {
    console.error('Error initializing breed data:', error);
  }
};
```

---

## ペット登録フローでの活用

### 1. 犬種選択画面

```typescript
import { Picker } from '@react-native-picker/picker';

export const BreedSelectionScreen = ({ petType, onBreedSelect }) => {
  const [breeds, setBreeds] = useState([]);

  useEffect(() => {
    const loadBreeds = async () => {
      const db = Database.openDatabase('petsense.db');
      const table = petType === 'dog' ? 'dog_breeds' : 'cat_breeds';
      const result = await db.getAllAsync(
        `SELECT id, name FROM ${table} WHERE is_active = 1 ORDER BY name`
      );
      setBreeds(result);
    };
    loadBreeds();
  }, [petType]);

  return (
    <Picker
      selectedValue={selectedBreedId}
      onValueChange={(value) => onBreedSelect(value)}
    >
      <Picker.Item label="犬種を選択..." value={null} />
      {breeds.map((breed) => (
        <Picker.Item key={breed.id} label={breed.name} value={breed.id} />
      ))}
    </Picker>
  );
};
```

### 2. 犬種情報の自動入力

```typescript
export const useBreedInfo = (breedId, petType) => {
  const [breedInfo, setBreedInfo] = useState(null);

  useEffect(() => {
    const loadBreedInfo = async () => {
      const db = Database.openDatabase('petsense.db');
      const table = petType === 'dog' ? 'dog_breeds' : 'cat_breeds';
      const result = await db.getFirstAsync(
        `SELECT * FROM ${table} WHERE id = ?`,
        [breedId]
      );
      setBreedInfo(result);
    };
    if (breedId) {
      loadBreedInfo();
    }
  }, [breedId, petType]);

  return breedInfo;
};
```

### 3. ペット登録フォームでの自動入力

```typescript
export const PetRegistrationForm = () => {
  const [selectedBreedId, setSelectedBreedId] = useState(null);
  const breedInfo = useBreedInfo(selectedBreedId, 'dog');
  const [formData, setFormData] = useState({
    name: '',
    breed: '',
    weight: '',
    // ... その他のフィールド
  });

  useEffect(() => {
    if (breedInfo) {
      // 犬種情報から自動入力
      setFormData((prev) => ({
        ...prev,
        breed: breedInfo.name,
        // 体重の平均値を提案
        weight: ((breedInfo.weight_male_min + breedInfo.weight_male_max) / 2).toFixed(1),
      }));
    }
  }, [breedInfo]);

  return (
    <View>
      <BreedSelectionScreen
        petType="dog"
        onBreedSelect={setSelectedBreedId}
      />
      {breedInfo && (
        <View>
          <Text>推奨体重: {breedInfo.weight_male_min}-{breedInfo.weight_male_max} kg</Text>
          <Text>平均寿命: {breedInfo.average_lifespan_min}-{breedInfo.average_lifespan_max} 年</Text>
          <Text>活動レベル: {breedInfo.activity_level}</Text>
        </View>
      )}
      {/* フォームの残りの部分 */}
    </View>
  );
};
```

---

## 正常範囲の活用

犬種・猫種・年齢に基づいて、正常範囲を自動的に取得できます。

```typescript
export const getNormalRange = async (petType, breedId, ageMonths) => {
  const db = Database.openDatabase('petsense.db');
  const result = await db.getFirstAsync(
    `SELECT * FROM normal_ranges_extended
     WHERE pet_type = ? AND breed_id = ? AND age_range_months_min <= ? AND age_range_months_max >= ?`,
    [petType, breedId, ageMonths, ageMonths]
  );
  return result;
};
```

---

## 拡張性

このデータベース設計は、以下の拡張に対応できます。

1. **地域別の正常範囲**: 気候や食文化による違いに対応
2. **毛色情報**: 毛色による健康リスクの管理
3. **混合犬種**: 複数の犬種の組み合わせに対応
4. **ユーザーカスタム犬種**: ユーザーが独自の犬種情報を追加可能
5. **栄養基準の更新**: 最新の栄養学研究に基づいた更新

---

## まとめ

このマスターデータベースにより、ペット登録時にユーザーが犬種・猫種を選択するだけで、以下の情報が自動的に入力されます。

- 推奨体重
- 平均寿命
- 栄養基準
- よくある健康問題
- 正常な尿・フン・排泄の範囲

これにより、ユーザーの利便性が大幅に向上し、より正確な健康管理が可能になります。
