# Zonkey キーボード プロジェクト概要

## プロジェクト概要

**Zonkey** は無線分割キーボードのZMKファームウェア設定プロジェクトです。

## ハードウェア仕様

### 基本構成
- **コントローラー**: Seeeduino Xiao BLE (nRF52840) × 2
- **接続**: Bluetooth Low Energy (BLE) 無線接続
- **キー数**: 42キー (左右各21キー)
- **レイアウト**: ロウスタッガード配列
- **キーピッチ**: 19mm
- **対応スイッチ**: ロープロファイルスイッチ専用 (Kailh Choc V2, Gateron KS-33 LP 2.0など)

### バッテリー
- **左手側**: 300mAh
- **右手側**: 500mAh
- **使用目安**: 1日8時間使用で約1ヶ月
- LED表示でバッテリー残量を確認可能

## 特殊機能

### 1. トラックボール (右手側)
**ファイル**: `config/boards/shields/zonkey /zonkey_R.overlay:52-62`

- **センサー**: PMW3610
- **CPI**: 800 (設定可能)
- **対応サイズ**: 34mm または 25mm
- **オートマウスモード**: トラックボール移動中は自動的にレイヤー6(MOUSE)に切り替わる
- **スクロールモード**: レイヤー5でスクロールとして機能
- **接続**: SPI (SCK: P0.5, MOSI/MISO: P0.4, CS: P0.9, IRQ: P0.2)

**トラックボール設定** (`zonkey_R.conf:16-33`):
```
CONFIG_PMW3610_CPI=800
CONFIG_PMW3610_CPI_DIVIDOR=1
CONFIG_PMW3610_ORIENTATION_180=y
CONFIG_PMW3610_SCROLL_TICK=16
CONFIG_PMW3610_INVERT_X=n
CONFIG_PMW3610_INVERT_SCROLL_X=y
CONFIG_PMW3610_FORCE_AWAKE=y
CONFIG_PMW3610_POLLING_RATE_250=y
CONFIG_PMW3610_AUTOMOUSE_TIMEOUT_MS=0
```

### 2. ロータリーエンコーダー (左手側)
**ファイル**: `config/boards/shields/zonkey /zonkey.dtsi:33-39`

- **タイプ**: ALPS EC11 (EVQWGD001)
- **ピン**: A-GPIO: D5, B-GPIO: D0
- **ステップ数**: 24
- **デフォルト機能**:
  - レイヤー0: ボリュームアップ/ダウン
  - その他のレイヤー: スクロールアップ/ダウン

## キーマップ構成

**ファイル**: `config/zonkey.keymap`

| レイヤー | 名称 | 主な機能 | LED色 | 行番号 |
|---------|------|---------|------|--------|
| 0 | win_layer_0 | 通常文字キー (QWERTY配列) | 無灯 | 49-66 |
| 1 | win_layer_1 | 数字キー(1-0)、記号、TAB | 赤 | 68-83 |
| 2 | win_layer_2 | ファンクションキー(F1-F12)、テンキー | 緑 | 85-100 |
| 3 | win_layer_3 | F1-F12、矢印キー、BS/DEL | 黄色 | 102-117 |
| 4 | BT_layer_4 | Bluetooth設定、Mac/Win切り替え | 青 | 119-134 |
| 5 | SCROLL | スクロール、マウスボタン (Windows用) | 紫 | 136-151 |
| 6 | MOUSE | マウス操作 (Windows用・自動起動) | 水色 | 153-162 |
| 7 | MAC_MODE_LAYER | Macモードトグルレイヤー (空レイヤー) | - | 177-190 |
| 8 | MAC_SCROLL | スクロール、マウスボタン (Mac用) | 紫 | 192-208 |
| 9 | MAC_MOUSE | マウス操作 (Mac用・自動起動) | 水色 | 210-222 |

### レイヤー詳細

#### レイヤー0 (win_layer_0) - 通常入力
- QWERTY配列の基本文字キー
- モディファイアキー: SHIFT, CTRL, WIN, ALT
- 日本語入力切り替え: LANGUAGE_1 (無変換), LANGUAGE_2 (変換)
- レイヤータップで他のレイヤーへアクセス

#### レイヤー1 (win_layer_1) - 数字・記号
- 数字キー: 1-0 (上段)
- 記号: -, @, [, ], ¥, など
- TAB, SHIFT+TAB

#### レイヤー2 (win_layer_2) - ファンクション・テンキー
- 左側: F1-F12
- 右側: テンキー (0-9, +, -, *, /, =)
- 括弧: (, )

#### レイヤー3 (win_layer_3) - ナビゲーション
- F1-F12
- 矢印キー: ↑, ↓, ←, →
- BACKSPACE, DELETE

#### レイヤー4 (BT_layer_4) - Bluetooth設定・OS切り替え
- BT0-BT4: デバイス選択 (最大5台ペアリング可能)
- BT_CLR: 現在のペアリング削除
- BT_CLR_ALL: 全ペアリング削除
- **MAC（Dキー）**: Mac/Windowsモード切り替え（トグル）
- to 0: レイヤー0に戻る（Rキーまたはその右）

#### レイヤー5 (SCROLL) - スクロール・ウィンドウ操作
- タブ操作: CTRL+TAB, SHIFT+CTRL+TAB, CTRL+W
- マウスボタン: MB1(左), MB2(右), MB3(中)
- ウィンドウ移動: ALT+←, ALT+→, WIN+←, WIN+→
- WIN+D: デスクトップ表示

#### レイヤー6 (MOUSE) - マウスモード (Windows用)
- トラックボール移動時に自動起動 (automouse-layer)
- Jキー: 左クリック (MB1)
- Kキー: 中クリック (MB3)
- Lキー: 右クリック (MB2)
- その他のショートカットはレイヤー5と同様

#### レイヤー7 (MAC_MODE_LAYER) - Macモードトグル
- このレイヤーは空のレイヤーですが、ONにすることでMac用のショートカットに切り替わります
- Layer 4の`MAC`キー（Dキー位置）でトグル
- LEDによる視覚的なフィードバックはありませんが、ショートカットの動作で確認できます

#### レイヤー8 (MAC_SCROLL) - スクロールモード (Mac用)
- Gキー長押しでアクセス（Macモード時）
- トラックボールでスクロール
- タブ操作: Cmd+Tab, Shift+Cmd+Tab, Cmd+W
- マウスボタン: MB1(左), MB2(右), MB3(中)
- ブラウザ操作: Cmd+[（戻る）, Cmd+]（進む）
- デスクトップ切り替え: Ctrl+Left/Right
- デスクトップ表示: F11

#### レイヤー9 (MAC_MOUSE) - マウスモード (Mac用)
- トラックボール移動時に自動起動（Macモード時）
- Jキー: 左クリック (MB1)
- Kキー: 中クリック (MB3)
- Lキー: 右クリック (MB2)
- その他のショートカットはレイヤー8と同様

## Bluetooth設定

**ファイル**: `zonkey_R.conf:11-14`, `zonkey_R.conf:48-53`

### 接続パラメータ
```
CONFIG_BT_PERIPHERAL_PREF_MIN_INT=12     # 最小接続間隔: 12ms
CONFIG_BT_PERIPHERAL_PREF_MAX_INT=12     # 最大接続間隔: 12ms
CONFIG_BT_PERIPHERAL_PREF_LATENCY=0      # レイテンシ: 0
CONFIG_BT_PERIPHERAL_PREF_TIMEOUT=400    # タイムアウト: 400
```

### スタックサイズ設定
```
CONFIG_ZMK_BLE_THREAD_STACK_SIZE=2048
CONFIG_ZMK_LOW_PRIORITY_THREAD_STACK_SIZE=2048
CONFIG_INPUT_THREAD_STACK_SIZE=2048
CONFIG_ZMK_SPLIT_BLE_PERIPHERAL_STACK_SIZE=2048
CONFIG_ZMK_SPLIT_BLE_CENTRAL_SPLIT_RUN_STACK_SIZE=3096
CONFIG_BT_CTLR_PHY_2M=y  # 右手側のみ有効
```

## マトリックス構成

**ファイル**: `config/boards/shields/zonkey /zonkey.dtsi:9-19`

- **列数**: 11列
- **行数**: 4行
- **ダイオード方向**: col2row
- **行GPIO**: D1, D2, D3, D6
- **列GPIO** (左手): D10, D9, D8, D7, P0.10, P0.9
- **列GPIO** (右手): D10, D9, D8, D7, P0.10

## Mac/Windows切り替え機能

**ファイル**: `config/zonkey.keymap:28-38`

ZMKの**conditional layers**機能を使用して、Mac/Windowsのショートカットキーを自動的に切り替えます。

### 使い方

1. **Windowsモード（デフォルト）**
   - 特に何もしなければWindowsモードで動作します
   - Gキー長押し → Layer 5 (SCROLL) が起動
   - トラックボール移動 → Layer 6 (MOUSE) が自動起動

2. **Macモードに切り替え**
   - Bキー長押し → Layer 4 (BT設定レイヤー) にアクセス
   - Dキー（上から2行目、左から3列目）を押す → Macモードに切り替わる
   - これ以降、Gキー長押しで → Layer 8 (MAC_SCROLL) が起動
   - トラックボール移動で → Layer 9 (MAC_MOUSE) が自動起動

3. **Windowsモードに戻す**
   - 再度、Layer 4 → Dキーを押す → Windowsモードに戻る

### 仕組み

**Conditional Layers**の設定（`zonkey.keymap:28-38`）:
```c
conditional_layers {
    compatible = "zmk,conditional-layers";
    mac_scroll {
        if-layers = <MAC_MODE 5>;  // Layer 7 AND Layer 5 が両方ONの時
        then-layer = <8>;           // Layer 8 (MAC_SCROLL) を起動
    };
    mac_mouse {
        if-layers = <MAC_MODE MOUSE>;  // Layer 7 AND Layer 6 が両方ONの時
        then-layer = <9>;               // Layer 9 (MAC_MOUSE) を起動
    };
};
```

- Layer 7 (MAC_MODE_LAYER) がONの状態で、
- Layer 5 (SCROLL) が起動されると → 自動的にLayer 8 (MAC_SCROLL) が優先される
- Layer 6 (MOUSE) が起動されると → 自動的にLayer 9 (MAC_MOUSE) が優先される

### ショートカットの違い

| 機能 | Windows | Mac |
|------|---------|-----|
| アプリ切り替え | Win+Tab | Cmd+Tab |
| タブを閉じる | Ctrl+W | Cmd+W |
| ブラウザ戻る | Alt+Left | Cmd+[ |
| ブラウザ進む | Alt+Right | Cmd+] |
| デスクトップ切り替え | Win+Left/Right | Ctrl+Left/Right |
| デスクトップ表示 | Win+D | F11 |

## カスタムビヘイビア

**ファイル**: `config/zonkey.keymap:40-56`

### 1. lt_to_layer_0
- hold-tap ビヘイビア
- ホールド: モーメンタリレイヤー起動
- タップ: レイヤー0に戻る + キー入力
- タッピングターム: 200ms

### 2. scroll_up_down
- センサー回転ビヘイビア
- スクロールダウン/アップを制御
- タップ間隔: 20ms

## LED表示機能

**ファイル**: `zonkey_R.conf:37-45`

### バッテリー残量表示
- 起動時にバッテリー残量を色で表示
- 高: 30%以上 (緑)
- 警告: 10-30% (黄色)
- 危険: 10%以下 (赤)

### レイヤー表示
右手側のLEDが現在のレイヤーを色で表示:
- レイヤー0: 無灯
- レイヤー1: 赤
- レイヤー2: 緑
- レイヤー3: 黄色
- レイヤー4: 青
- レイヤー5: 紫
- レイヤー6: 水色

### 接続状態表示
- 左右の無線接続が成功している場合: 緑→青の順で点滅

## ビルド設定

**ファイル**: `build.yaml`

```yaml
include:
  - board: seeeduino_xiao_ble
    shield: zonkey_R rgbled_adapter
  - board: seeeduino_xiao_ble
    shield: zonkey_L rgbled_adapter
  - board: seeeduino_xiao_ble
    shield: settings_reset
```

- 左右それぞれのファームウェアをビルド
- rgbled_adapter: LED機能用のアダプター
- settings_reset: 設定リセット用ファームウェア

## ファイル構成

```
zmk-config-zonkey4/
├── config/
│   ├── zonkey.keymap              # キーマップ定義
│   ├── zonkey.json                # Keymap Editor用設定
│   ├── west.yml                   # ZMK依存関係設定
│   └── boards/shields/zonkey /
│       ├── zonkey.dtsi            # 共通ハードウェア定義
│       ├── zonkey.zmk.yml         # ZMKメタデータ
│       ├── zonkey_L.overlay       # 左手側オーバーレイ
│       ├── zonkey_L.conf          # 左手側設定
│       ├── zonkey_R.overlay       # 右手側オーバーレイ (トラックボール)
│       ├── zonkey_R.conf          # 右手側設定
│       ├── Kconfig.defconfig      # Kconfig デフォルト設定
│       └── Kconfig.shield         # シールド設定
├── build.yaml                     # ビルドマトリックス
├── .github/workflows/build.yml    # GitHub Actions設定
└── README.md                      # 日本語ドキュメント

```

## 参考リンク

- [ZMK Firmware 公式ドキュメント](https://zmk.dev/)
- [ZMK キーコードリスト](https://zmk.dev/docs/keymaps/list-of-keycodes#keyboard)
- [Keymap Editor](https://nickcoutsos.github.io/keymap-editor/)
- [zonkeyキーマップ解説ページ](https://note.com/goya_k)
- オリジナルリポジトリ:
  - [zmk-config-zonkey (JIS)](https://github.com/kureyakey/zmk-config-zonkey)
  - [zmk-config-zonkey2 (JIS・オートマウスなし)](https://github.com/kureyakey/zmk-config-zonkey2)
  - [zmk-config-zonkey3 (US)](https://github.com/kureyakey/zmk-config-zonkey3)

## カスタマイズのヒント

### キーマップ編集方法
1. [Keymap Editor](https://nickcoutsos.github.io/keymap-editor/)でGitHubにログイン
2. このリポジトリを選択
3. グラフィカルにキー配置を編集
4. 保存すると自動的にGitHub Actionsでビルドが実行される

### トラックボール感度調整
`zonkey_R.conf`の以下の値を変更:
- `CONFIG_PMW3610_CPI`: マウス感度 (デフォルト: 800)
- `CONFIG_PMW3610_SCROLL_TICK`: スクロール感度 (デフォルト: 16)

### エンコーダー機能変更
`zonkey.keymap`の各レイヤーの`sensor-bindings`を編集

## トラブルシューティング

### Bluetooth接続できない
1. `settings_reset-seeeduino_xiao_ble-zmk.uf2`を書き込み
2. 両側のリセットボタンを押す
3. PC側のBluetooth設定からペアリングし直す

### トラックボールの動きがおかしい
1. 左右のリセットボタンを押して再接続
2. トラックボールケースとセンサーの距離を調整

### 一部キーが反応しない
- ハンダ不良の可能性
- スイッチの挿入を確認
- 初期不良の場合は販売元に連絡
