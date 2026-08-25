# 軽井沢クロニクル — iOS リリース一式（Capacitor + Codemagic）

フェルミ道場と**まったく同じ方式**で、Windows のまま Mac を使わず App Store に出すためのリポジトリです。
軽井沢クロニクルは単一の `app.html` なので、そのまま Capacitor で包んで Codemagic のクラウド Mac でビルドします。

## このリポジトリの中身
```
app.html               … アプリ本体（フルスクリーン化・フォント外し済み・通信なし）
capacitor.config.json  … appId = jp.karuizawachronicle.app / appName = 軽井沢クロニクル
package.json           … Capacitor 依存
codemagic.yaml         … ビルド設定（フェルミと同じ署名自動生成方式）
icon-1024.png          … アプリアイコン（1024px・透過なし）
store/
  metadata.md          … App Store Connect に入れる文言一式（名前・説明・審査メモ 等）
  privacy-policy.html  … GitHub Pages に置くプライバシーポリシー兼サポートページ
```

## フェルミ道場との違いは実質2点だけ
1. **Bundle ID** … `jp.karuizawachronicle.app`（`codemagic.yaml` と `capacitor.config.json` に設定済み）
2. **アプリ本体** … `app.html` が軽井沢クロニクルの内容

署名の変数グループ（`appstore`）と App Store Connect 連携キー（`FermiDojoKey`）は
**同じ Apple ID なのでそのまま再利用できます**。Codemagic 側の再設定は不要です。

---

## 手順

### 1. GitHub に新しいリポジトリを作る
このフォルダ一式を、新規リポジトリ（例: `karuizawa-chronicle`）にアップロードします。
更新のたびに `app.html` を差し替えて push すればOK（フェルミと同じ運用）。

### 2. サポート／プライバシーページを公開する
`store/privacy-policy.html` を GitHub Pages 用リポジトリに `index.html` として置きます。
公開URL（例: `https://watanabedaiju0613-cloud.github.io/karuizawa-chronicle/`）を控えておきます。

### 3. App Store Connect でアプリを新規作成
- 「マイ App」→ 新規作成
- プラットフォーム iOS、名前「軽井沢クロニクル」
- **Bundle ID は新規に `jp.karuizawachronicle.app` を作成**（フェルミとは別のIDが必要）
- SKU は任意（例: `karuizawa-chronicle-1`）

### 4. Codemagic でビルド
- このリポジトリを Codemagic に接続し、`ios-release` ワークフローを実行
- 署名証明書・プロファイルは自動生成（`fetch-signing-files --create`）
- 成功すると TestFlight にビルドが上がります（15〜30分）

### 5. App Store Connect を埋める
`store/metadata.md` の上から順に入力：
1. スクリーンショット（6.5インチ 5枚）
2. プロモーションテキスト / 概要 / キーワード
3. サポートURL（手順2のURL）
4. ビルド（＋ で最新ビルドを選択）
5. 著作権 `Daiju Watanabe`
6. 年齢制限 4+
7. App Review 情報（サインイン不要のチェックを外す・連絡先・審査メモ）
8. アプリのプライバシー → ポリシーURL入力 →「データ収集：いいえ」→ **「公開」を押す**
9. バージョンのリリース：手動

### 6. 提出
「保存」→「審査用に追加」→「審査へ提出」。審査は最大48時間。

---

## スクリーンショットの撮り方（Mac不要）
- 一番簡単：TestFlight にビルドが上がったら、自分の iPhone にインストールして撮影
- または `app.html` を iPhone の Safari で開き（全画面表示）、ホーム・年表・読み物・クイズ・地図の5画面を撮る

## 補足・既知の小さな点
- フォントは通信を避けるため OS 内蔵の明朝／ゴシックにフォールバックさせています（Zen フォントの同梱は将来の任意アップグレード）
- 観光スポットの「マップで開く」はタップ時のみ標準マップアプリを起動します（データ送信はしません）
- 単機能アプリではなく、年表・読み物・クイズ・ゲーム・地図の複数機能があるため、Guideline 4.2 のリスクは低めです。審査メモでその点を明示しています

---

掲載内容は一般に知られる史実・観光情報にもとづくサンプルです。
