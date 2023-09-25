# dev-log

<!-- Template
### 2023-09-25
#### 目標
#### 決定事項
### 共有事項
### 今後の予定
-->

### 2023-09-25
#### 目標
- 次回のMTG日程
- stage 1 の共有
  - daemaon=Trueにしとく
  - Clinet側で入力の検証（名前が255バイト以下か、メッセージ全体で4096以下か）
- 誰のを採用するか、もしくは誰のどの部分を組み合わせるかなど
- stage 2 の読み合わせ？
- 今後の開発スケジュール決定
- 作業分担
- UMLとか書く？？

#### 決定事項
- mabuoさんのベースで進めてく
- RoomNamesはUTF-8でエンコード/デコードされます。OperationPayloadは、操作と状態に応じて異なる方法でデコードされる可能性があります（整数、文字列、JSONファイルなど）
  - すべての操作でJSON形式
```json
// op 1
  // サーバの初期化（0）
  {
    "roomName": "example"
  }

  // リクエストの応答（1）
  {
    "status": 200,
    "message": "example message"
  }
```

#### 共有事項

#### 今後の予定
- 入力の検証してPR出す（mabuo）
- TODO テスト（オフィスアワーで質問する）
- プロトコルのヘッダーでStateが何を示しているのか解決する
- 

### 2023-09-23
#### やったこと
- 取り組むプロジェクトの決定
- Gitルール
- 開発方針

#### 決定事項
- Online Chat Messenger
  - ステージ１はそれぞれで実装
  - その後分担

- Git
  - developからブランチきって開発
  - PR出して1 approveでleaderがマージ
  - branch prefix
    - feat, doc, fix,
    <br>`git switch -c feat-hoge`
  - commit messege
    <br>`git commit -m "何やった #Issue"`
 
- レビュー観点
  - 関数名とか変数名の命名が適切か
  - 例外処理ができてるか
  - 処理の流れがわかりやすいか
  - 適切な単位で処理を分割できているか

- タスク管理
  - ~Trello,~ GitHub Projects
 
- グループミーティング
  - 週３回
  - 20:00~21:00
 
- 環境
  - devcontainer
 
- CI / CD
  - GitHub Actions (TODO)
  - husky
  - flake8 / autopep8

#### 今後の予定
- 2023-09-25 Mon, 20:00~21:00 MTG (Stage 1が終わってるといい感じ)
- 環境構築 (devcontainer, husky)
- PRのテンプレート (チェックボックスの形式でレビュー観点を入れとく)
- 不明点とか情報共有あったらDiscordで
