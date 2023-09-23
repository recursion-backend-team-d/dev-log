# dev-log

### 2023-09-23
#### やったこと

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
