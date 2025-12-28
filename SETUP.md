# HORIZON CITY 管理画面 セットアップガイド

このガイドでは、v0環境でHORIZON CITY管理画面を動作させるための手順を説明します。

## 必要な環境変数

v0チャット画面の左サイドバーから「**Vars**」セクションを開き、以下の環境変数を追加してください。

### 1. Discord OAuth設定

#### DISCORD_CLIENT_ID
- **説明**: Discord アプリケーションのクライアントID
- **取得方法**:
  1. [Discord Developer Portal](https://discord.com/developers/applications)にアクセス
  2. 「New Application」をクリックして新しいアプリケーションを作成
  3. 作成したアプリケーションをクリック
  4. 左メニューの「OAuth2」→「General」を選択
  5. 「CLIENT ID」をコピー
- **例**: `1234567890123456789`

#### DISCORD_CLIENT_SECRET
- **説明**: Discord アプリケーションのクライアントシークレット
- **取得方法**:
  1. 同じく「OAuth2」→「General」画面で
  2. 「CLIENT SECRET」の「Reset Secret」をクリック（初回は自動表示）
  3. 表示されたシークレットをコピー（二度と表示されないので注意）
- **例**: `abcdefghijklmnopqrstuvwxyz123456`
- **重要**: このシークレットは絶対に公開しないでください

#### DISCORD_REDIRECT_URI
- **説明**: OAuth認証後のリダイレクトURL
- **設定方法**:
  1. Discord Developer Portalの「OAuth2」→「General」画面で
  2. 「Redirects」セクションの「Add Redirect」をクリック
  3. `https://your-project.vercel.app/api/auth/discord/callback` を追加
  4. v0プレビューURL: `https://[your-v0-preview-url]/api/auth/discord/callback`
- **v0での設定値**: あなたのv0プレビューURLを使用
  - 例: `https://abc123-v0-preview.vercel.app/api/auth/discord/callback`

### 2. JWT設定

#### JWT_SECRET
- **説明**: セッション管理用の署名キー
- **生成方法**: ランダムな64文字以上の文字列を生成
  - コマンドライン（Mac/Linux）: `openssl rand -base64 64`
  - オンライン: [Random.org](https://www.random.org/strings/)
- **例**: `your-super-secret-jwt-key-min-32-characters-long-random-string-here-12345`
- **重要**: 本番環境では必ず安全なランダム文字列を使用してください

## 環境変数の追加方法（v0）

1. v0チャット画面の左側にあるサイドバーを開く
2. 「**Vars**」アイコンをクリック
3. 各環境変数を以下の形式で追加:
   - **Name**: 環境変数名（例: `DISCORD_CLIENT_ID`）
   - **Value**: 対応する値
   - 「Add Variable」をクリック
4. すべての環境変数を追加したら、プレビューを再読み込み

## データベースのセットアップ

環境変数を設定したら、データベースを初期化します。

### 1. スキーマの作成

`scripts/001_initial_schema.sql` が自動的に実行されます。これにより以下のテーブルが作成されます：
- `users`: 管理者アカウント
- `players`: プレイヤー情報
- `announcements`: お知らせ
- `events`: イベント
- `activity_logs`: 操作ログ
- `settings`: システム設定

### 2. 初期データの投入

`scripts/002_seed_data.sql` を編集して、あなたのDiscord IDを設定します：

```sql
-- あなたのDiscord IDに変更してください
INSERT INTO users (discord_id, username, role, is_active)
VALUES ('YOUR_DISCORD_ID_HERE', 'あなたの名前', 'owner', 1);
```

**Discord IDの確認方法**:
1. Discordの設定 → 詳細設定 → 開発者モードをONにする
2. 自分のユーザー名を右クリック → 「IDをコピー」

## 動作確認

1. すべての環境変数を追加
2. プレビューを再読み込み
3. ログインページで「Discord でログイン」をクリック
4. Discord認証を完了
5. ダッシュボードにリダイレクトされることを確認

## トラブルシューティング

### "Discord client ID not configured" エラー
→ 環境変数 `DISCORD_CLIENT_ID` が設定されていません。Varsセクションで追加してください。

### "このDiscordアカウントは管理者として登録されていません" エラー
→ データベースにあなたのDiscord IDが登録されていません。`002_seed_data.sql` で正しいDiscord IDを設定してください。

### "認証に失敗しました" エラー
→ Discord Developer Portalで設定したRedirect URIが正しいか確認してください。

## セキュリティ注意事項

- `DISCORD_CLIENT_SECRET` と `JWT_SECRET` は絶対に公開しないでください
- 本番環境では強力なランダム文字列を使用してください
- 定期的にシークレットをローテーションすることを推奨します

## サポート

問題が解決しない場合は、v0のヘルプまたは開発者にお問い合わせください。
