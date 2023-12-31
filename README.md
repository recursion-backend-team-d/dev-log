# dev-log

<!-- Template
### 2023-09-25
#### 目標
#### 決定事項
#### 共有事項
#### 今後の予定
-->

### 2023-10-06
#### 目標
#### 決定事項
#### 共有事項
#### 今後の予定
- 次回MTG 10/8 18:00~20:00
- deploy関係 (server deploy ec2? ecr?, client installer) TODO (Sunday)
- メッセージの暗号化 - TODO (Sunday)
1. 入室可能なルーム（パスワード保護の有無）とルームのメンバをサーバからクライアントへ通知 TCP
  - 通知のタイミング（TCPの接続確立直後にサーバから送る）　- server (kara), electron (mabuo)
2. UI kaizen
  - チャット欄にメッセージが増えたら、チャットランそれ自体をスクロール可能にする
  - Errorのユーザへの通知の仕方
  - 入室可能なルーム一覧ページ、チャットルームのメンバ一覧ページ
2. ルームからの切断について UDP - kara, mabuo
```
CREATE ROOM
ROOM1    ROOM2    ROOM3...
ROOM5    ROOM6...
```
```json
// ヘッダー（32バイト）：RoomNameSize（1バイト） | Operation（1バイト） | State（1バイト） | OperationPayloadSize（29バイト）
// 0, 0, 0, sizeof (payload)
{
  "rooms": [
    "room_name1": {
      "members": ["member_name1", "member_name2"...],
      "password_required": true
    }
  ]
}
```
```json
// 通常メッセージ
{
  "sender": "example sender",
  "message": "example",
}
// システム（送受信）
// ヘッダー：RoomNameSize（1バイト）| TokenSize（1バイト）
// operation 1(入室), 2（退室）
{
  "sender": "system",
  "operation": 0,
  "username": "example,
}
```

### 2023-10-05
#### 目標
- やってきたことの共有
- 残りのタスク整理
- 次回のMTG

#### 決定事項

#### 共有事項

#### 今後の予定
- 10/6 20:00~21:00 Electronの結合
  - Electronでpassword - mabuo / akiba
  - deploy関係
- TCPタイムアウトの設定 - mabuo (client), kara (server)
- Serverのリファクタ - kara
- 入室可能なルームをサーバからクライアントへ通知 - TODO
- メッセージの暗号化 - TODO

### 2023-10-02
#### 目標
- 今後の予定確認
  - stage 3
- 次回MTGの日程

#### 決定事項
- 10/5 20:00 ~ 21:00 (タスク大体できてる感じ)

#### 共有事項
- クライアントのTCPにタイムアウト設定？

#### 今後の予定
  - password - kara
  - desktop app for client 
    - front - aki
    - back - mabuo
  - devcontainerでnode - kara
  - electronの雛形 - kara

### 2023-09-30
#### 目標
- 進捗の共有
  - 共有内容に対して疑問点があれば確認
- つまずいている点の解決
- 今後の予定の確認
- 次回のMTGの日程設定

#### 決定事項
- サーバーのレスポンスについてもrequestと同様のプロトコル （送受信）
  - ヘッダー（32バイト）：RoomNameSize（1バイト） | Operation（1バイト） | State（1バイト） | OperationPayloadSize（29バイト）
  - ボディ：最初のRoomNameSizeバイトがルーム名で、その後にOperationPayloadSizeバイトが続きます。ルーム名の最大バイト数は2^8バイトであり、OperationPayloadSizeの最大バイト数は2^29バイトです。
  - ボディはjson.dumpsでエンコード
- struct.pack, struct.unpackで、request, responseを扱う
- サーバのTCPソケットは8000番、UDPソケットは9000番
- ユニットテストは無し
- UDPのプロトコル (送受信)
  - Client側でメッセージのサイズを検証。4096を超えたら再入力を促す。
  - ヘッダー：RoomNameSize（1バイト）| TokenSize（1バイト）
  - ボディ：最初のRoomNameSizeバイトはルーム名、次のTokenSizeバイトはトークン文字列、そしてその残りが実際のメッセージです。
```json
{
  "sender": "example sender",
  "message": "example"
}
```

- Client側でbind(('localhost', 0)) -> TCPでリクエストを送るときに、UDPのIPとポートも一緒に送る
  - Server側でChatClientをインスタンス化
- ChatRoom
  - self.rooms[room_name.decode()].broadcast(address, token, msg.decode())
- ChatClient
  - init(name, address, token, is_host)
- Server
  - notify_available_rooms実装なし （今後実装あるかも）
- Serverでエラーが起きた時（op = 1ですでにroom_nameが存在する時など）は失敗statusを返す
  - statusコードは今後決める。Client側では送られてきたメッセージを出力したりする

#### 共有事項
- bind
  - どのポートで受け取るのか指定
  - self.udp_socket.bind(('', 0))
  - ポート 0 を指定することで空いているポートが指定される。
  - 勝手にbindしてくれる？？
- akibaさん 10/2, 10/3はMTG参加不可
- README更新
- UDPの時はBUFFER_SIZE分を一気にrecvfromする
- payloadのroomNameは削除

#### 今後の予定
- 次回のMTG 10/2 20:00 ~ 21:00 (とりあえず担当範囲は完成が目標)

### オフィスアワー
- 非機能要件のテストはどのように行うか。毎秒10000パケットの送信。1000人の同時接続。
  - UDPソケットを使うだけで満たせる。テストをあえてするなら、反復的にメッセージを送り出すプログラムを作成する。ネットワーキングツールも使えるが依存性の問題もあり、今回の規模では必要ない。
- クラスごとのテスト
  - ユニットテストは機能の実装と同じくらいかかる。将来的なメリットも考えてやる意味があるのかを検討する。
- TCPとUDPのポートは分けるべきか
  - 分けるべき。おそらくエラーが生じる。
- TDDでなければ手動テスト、その後にユニットテストを書くという順番か
  - Yes

### 2023-09-28
#### 目標
- クラス図の確認
- 作業の分担

#### 決定事項
- テストはモックオブジェクト使ってやる
- Server - Kara
- Client - mabuo
- ChatRoom, ChatClient - akiba

#### 共有事項
- ServerとClientの間のプロトコルは決まってるから、クラス図をやりやすいように変えてもあまり問題ないはず。その際はREADMEのクラス図も更新できるといい

#### 今後の予定
- 2023-09-30 20:00~21:00 5wari

### 2023-09-27
#### 目標
- stage 2 のクラス図を共有
- クラス図の決定
- 作業分担（できれば）

#### 決定事項
- classごとにファイル
- server、clientでそれぞれディレクトリ


#### 共有事項
なし

#### 今後の予定
- 分担（クラス図の詳細書き込んでから）
- Server
- Client
- ChatRoom, ChatClient
- 2023-9-28 21:00~21:30

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
- UMLとか書く

#### 決定事項
- mabuoさんのベースで進めてく
- RoomNamesはUTF-8でエンコード/デコードされます。OperationPayloadは、操作と状態に応じて異なる方法でデコードされる可能性があります（整数、文字列、JSONファイルなど）
  - すべての操作でJSON形式
- import secretsでtoken生成
- プロトコル（statusはHTTP status参照）
```json
// op 1, op 2, op3
  // サーバの初期化（0）
  {
    "username": "example"
    "ip": "",
    "port": "",
  }

  // リクエストの応答（1）
  {
    "status": 202,
    "message": "example message"
  }

  // リクエストの完了（2）
  {
    "status": 201,
    "message": "example message"
  }
```

#### 共有事項
なし

#### 今後の予定
- 入力の検証してPR出す（mabuo）
- TODO テスト（オフィスアワーで質問する）
- プロトコルのヘッダーでStateが何を示しているのか解決する
- MermaidでClass図を書いてみよう（all）
- 9/27 20:00~21:00（Class図を書いてみる）

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
