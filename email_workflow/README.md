# ガルーンメールAI分析 & 長期記憶システム + 未返信管理 + 月次レポート

## 概要
このワークフローはN8Nビルダー Proで自動生成されました。

## ワークフロー名
AI

## ノード構成
- Garoon Webhook (webhook)
- 即時応答 (respondToWebhook)
- データ整形・迷惑判定 (code)
- 迷惑メール除外 (if)
- OpenAI Embeddings (embeddingsOpenAi)
- 類似メール検索 (vectorStoreMongoDBAtlas)
- コンテキスト生成 (code)
- メール分析AI Agent (agent)
- Claude Haiku (lmChatAnthropic)
- MongoDB Chat Memory (memoryMongoDbChat)
- Structured Output Parser (outputParserStructured)
- AI出力検証 (code)
- メールベクトル保存 (vectorStoreMongoDBAtlas)
- OpenAI Embeddings (保存用) (embeddingsOpenAi)
- 重要度分岐 (switch)
- Garoon下書き保存 (httpRequest)
- LINE WORKS通知(高) (httpRequest)
- LINE WORKS通知(中) (httpRequest)
- Google Sheets記録 (googleSheets)
- 処理合流 (merge)
- Error Trigger (errorTrigger)
- エラー通知 (httpRequest)

## 要設定項目
- OpenAI Embeddings: openAiApiの認証情報を設定
- 類似メール検索: mongoDbApiの認証情報を設定
- Claude Haiku: anthropicApiの認証情報を設定
- MongoDB Chat Memory: mongoDbApiの認証情報を設定
- メールベクトル保存: mongoDbApiの認証情報を設定
- OpenAI Embeddings (保存用): openAiApiの認証情報を設定
- Garoon下書き保存: httpHeaderAuthの認証情報を設定
- Garoon下書き保存: URLを実際のエンドポイントに変更
- LINE WORKS通知(高): httpHeaderAuthの認証情報を設定
- LINE WORKS通知(高): URLを実際のエンドポイントに変更
- LINE WORKS通知(中): httpHeaderAuthの認証情報を設定
- LINE WORKS通知(中): URLを実際のエンドポイントに変更
- Google Sheets記録: googleSheetsOAuth2Apiの認証情報を設定
- エラー通知: httpHeaderAuthの認証情報を設定
- エラー通知: URLを実際のエンドポイントに変更

## 使い方
1. n8nにログイン
2. Settings > Import from File でJSONをインポート
3. 必要な認証情報を設定（上記「要設定項目」参照）
4. ワークフローを有効化

## 生成日時
2025-12-04T04:49:48.751Z
