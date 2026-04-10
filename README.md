# 授業チャット — セットアップガイド

## ファイル構成

```
classroom-chat/
├── student.html   ← 学生がスマホ・PCで開くページ
├── teacher.html   ← 先生が授業中に開くページ
└── README.md      ← このファイル
```

---

## STEP 1: Firebase プロジェクトを作成する

1. https://console.firebase.google.com にアクセス（Googleアカウントでログイン）
2. 「プロジェクトを追加」をクリック
3. プロジェクト名を入力（例: `classroom-chat`）→ 「続行」→ 「プロジェクトを作成」

---

## STEP 2: Realtime Database を有効にする

1. 左メニューの「構築」→「Realtime Database」をクリック
2. 「データベースを作成」ボタンをクリック
3. ロケーションは **asia-southeast1（シンガポール）** を選択
4. セキュリティルールは「テストモードで開始」を選択
   - ⚠ テストモードは30日間有効です。本番運用時はルールを設定してください。
5. 「有効にする」をクリック

---

## STEP 3: Firebase の設定情報をコピーする

1. 左メニュー上部の「プロジェクトの概要」の歯車アイコン → 「プロジェクトの設定」
2. 「マイアプリ」セクションで「</>」（ウェブ）アイコンをクリック
3. アプリ名を入力（例: `classroom-chat-web`）→「アプリを登録」
4. 表示された `firebaseConfig` の中身をコピーしておく

```javascript
// コピーする部分の例（値は自分のものを使用してください）
const firebaseConfig = {
  apiKey: "AIzaSyXXXXXXXXXXXXXXXXXXX",
  authDomain: "classroom-chat-xxxxx.firebaseapp.com",
  databaseURL: "https://classroom-chat-xxxxx-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "classroom-chat-xxxxx",
  storageBucket: "classroom-chat-xxxxx.appspot.com",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:xxxxxxxxxxxxxxxx"
};
```

---

## STEP 4: HTMLファイルに設定を貼り付ける

`student.html` と `teacher.html` の両方を開き、
`YOUR_API_KEY` などのプレースホルダーを自分の設定値に書き換えます。

**対象箇所**（両ファイルに同じ設定を貼り付けてください）:

```javascript
/* ================================================================
   ★ Firebase 設定 — ここを自分のプロジェクト情報に書き換えてください
   ================================================================ */
const firebaseConfig = {
  apiKey:            "YOUR_API_KEY",          ← ここを書き換え
  authDomain:        "YOUR_PROJECT_ID...",    ← ここを書き換え
  databaseURL:       "https://...",           ← ここを書き換え
  ...
};
```

---

## STEP 5: ファイルを公開する（学生がアクセスできるようにする）

### 方法A: GitHub Pages（無料・おすすめ）
1. GitHubアカウントを作成（https://github.com）
2. 新しいリポジトリを作成（Public）
3. `student.html` と `teacher.html` をアップロード
4. Settings → Pages → Source を「main ブランチ」に設定
5. 公開URLが発行される（例: `https://yourusername.github.io/classroom-chat/`）

### 方法B: Firebase Hosting（同じプロジェクトで完結）
1. Node.jsをインストール: https://nodejs.org
2. ターミナルで以下を実行:
```bash
npm install -g firebase-tools
firebase login
firebase init hosting
firebase deploy
```

### 方法C: ローカルネットワーク（学内Wi-Fi限定）
- PCにPythonがあれば、HTMLファイルのフォルダで以下を実行:
```bash
python -m http.server 8080
```
- PCのIPアドレスを確認して、学生に `http://192.168.X.X:8080/student.html` を案内

---

## 授業での使い方

### 授業前
1. PCで `teacher.html` を開く
2. 科目名を入力し「授業を開始する」をクリック
3. 表示された **4文字のルームコード** を学生に伝える
4. プロジェクターにteacher.htmlを映す

### 授業中
- 学生はスマホで `student.html` を開き、ルームコードを入力して参加
- QRコードで共有すると便利（student.htmlのURLをQRコードに変換して投影）

### 表示設定（先生画面右側）
| 設定 | 説明 |
|------|------|
| とどまるモード | 投稿が一定時間カードとして残る。プロジェクターでの表示に最適 |
| 流れるモード | 新しい投稿が上から順に並ぶ |
| 表示時間 | カードが消えるまでの秒数（5〜60秒） |
| 同時表示枚数 | 画面に表示するカード数の最大値 |

---

## セキュリティについて

- 学生は**匿名**（ランダムな動物名）で投稿します
- ルームコードを知らないと投稿できません
- テストモードのFirebaseルールは30日で期限切れになります
- 継続して使用する場合は Realtime Database のルールを更新してください:

```json
{
  "rules": {
    "classrooms": {
      "$roomId": {
        ".read": true,
        ".write": true
      }
    }
  }
}
```

---

## よくある質問

**Q: 同時に何人まで使えますか？**
A: Firebaseの無料プラン（Spark）では同時接続100台まで対応しています。通常の授業では問題ありません。

**Q: 授業が終わったらデータは消えますか？**
A: Firebaseにはデータが残ります。Firebase Console から手動で削除できます。

**Q: スマホのブラウザで動きますか？**
A: iOS Safari・Android Chrome で動作確認済みです。
