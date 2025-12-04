プロジェクト名: ガルーンメールAI分析 & 長期記憶システム + 未返信管理 + 月次レポート

目的: Garoonで受信したメールをAIが分析・分類し、重要メールのみLINE WORKSへ通知します。さらに過去のやり取りを長期記憶することで返信案の精度を高め、未返信管理と月次レポートでメール対応業務全体を効率化・高度化します。

機能:
1. 【随時】GaroonメールをWebhookで受信し、AI(Claude Haiku)がジャンル・重要度を判定、過去のやり取りをMongoDBから参照して返信案を生成。Garoonに下書き保存し、LINE WORKSへ通知、Google Sheetsに記録。
2. 【毎日9時】Google Sheetsを参照し、24時間以上前の未返信メールを抽出し、LINE WORKSへリマインダーを送信。
3. 【随時】LINE WORKSのボタン操作で、返信不要なメールをGoogle Sheets上で「スキップ」に更新。
4. 【毎月1日10時】Google Sheetsのデータを集計・分析し、前月のパフォーマンスレポートをLINE WORKSへ送信。

ノード:
- Webhook (n8n-nodes-base.webhook): Garoonからのメール受信、LINE WORKSからのスキップ操作のトリガー
- Schedule (n8n-nodes-base.schedule): リマインダー(毎日9時)と月次レポート(毎月1日10時)のトリガー
- Code (n8n-nodes-base.code): 日時取得(JST)、迷惑メール判定、データ集計・整形などのカスタムロジック
- MongoDb (n8n-nodes-base.mongoDb): 長期記憶の検索・保存（Vector Storeとは別にChat Historyなどで使用）
- n8n-langchain.agent: AIエージェントの中核。言語モデル、メモリ、ツールを統合
- n8n-langchain.embeddingsOpenAi: OpenAIのモデルでメール本文をベクトル化
- n8n-langchain.vectorStoreMongoDBAtlas: ベクトル化されたデータをMongoDB Atlasに保存・検索
- n8n-langchain.llms.lmChatAnthropic: AIエージェントが使用する言語モデルとしてClaude Haikuを指定
- Switch (n8n-nodes-base.switch): AIが判定したジャンルに基づき処理を分岐
- HTTP Request (n8n-nodes-base.httpRequest): Garoon API(下書き保存)、LINE WORKS API(通知)との連携
- Google Sheets (n8n-nodes-base.googleSheets): 全データの記録、読み込み、更新
- If (n8n-nodes-base.if): 未返信メールの有無を判定
- Merge (n8n-nodes-base.merge): 並行処理の合流
- Respond to Webhook (n8n-nodes-base.respondToWebhook): Garoonへの応答
- Error Trigger (n8n-nodes-base.errorTrigger): ワークフロー全体のエラーハンドリング

AI構成: はい（Claude 4.5 Haiku, OpenAI text-embedding-ada-002）
※ AIエージェントには、言語モデル(Anthropic)、長期記憶(MongoDB)、Embedding(OpenAI)を接続します。

トリガー:
- Webhook: Garoonからの新規メール受信時、LINE WORKSからのボタンアクション時
- Schedule (Cron): 毎日9:00 (リマインダー)、毎月1日10:00 (レポート)

データフロー:
1. メール受信&分析: (Garoon) -> Webhook -> Code -> MongoDB(検索) -> AI Agent -> MongoDB(保存) -> Switch -> [HTTP Request(Garoon), HTTP Request(LINE WORKS), Google Sheets]
2. リマインダー: Schedule -> Code -> Google Sheets(読込) -> Code -> If -> HTTP Request(LINE WORKS)
3. スキップ処理: (LINE WORKS) -> Webhook -> Code -> Google Sheets(検索・更新) -> HTTP Request(LINE WORKS)
4. 月次レポート: Schedule -> Code -> Google Sheets(読込) -> Code -> HTTP Request(LINE WORKS)

認証が必要なサービス:
- Garoon: API Token
- LINE WORKS: Bot API Access Token
- Google Sheets: OAuth2
- Anthropic: API Key
- MongoDB Atlas: Connection String
- OpenAI: API Key