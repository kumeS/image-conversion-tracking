# Image Conversion Tracker

ブラウザ上で AI を用いた画像変換ワークフローを視覚的に管理するためのツールです。フロントエンドのみで完結しており、バンドラやフレームワークは不要で、HTML/CSS/バニラ JavaScript と vis-network だけで動作します。ワークフローの編集と、Cloudflare Worker 経由の Replicate 呼び出しを組み合わせて、プロンプト付きの画像生成や一括処理を行えます。

## できること
- **ノードベースのワークフロー編集**: ノードの作成・削除、エッジの接続、詳細パネルでの属性確認、読みやすさを保つ自動レイアウト。
- **プロンプトとアスペクト比の編集**: エッジごとにプロンプト、モデル、アスペクト比を保持し、プロンプトモーダルから編集可能。アスペクト比は `1:1`、`4:3`、`16:9` などの形式で表示されます。
- **バッチ生成操作**: バッチ生成、ノードへのアップロード、マージ、トランスフォームといった操作ボタンが `workflowApp.js` と `canvasController.js` を通じてネットワーク状態を更新します。
- **API 設定の保存**: 設定モーダルで Replicate API キー（および IO Intelligence キー）を入力し、`localStorage` に保存します。
- **Replicate / Nano Banana 連携**: `transformationService.js` から Cloudflare Worker プロキシ（`cloudflare-worker-v5.js`）に対して `google/nano-banana` の推論を実行し、アスペクト比や出力形式の指定を反映します。

## システム構成
- **フロントエンド**: 主要な UI は `index.html`、`css/workflow.css`、および `js/` 配下のモジュール（`workflowApp.js`、`canvasController.js`、`config.js`、`transformationService.js` など）で構成されています。`vis-network` によりキャンバス上でノード・エッジを描画し、`tailwind.css` のユーティリティを活用したスタイリングを行っています。
- **Cloudflare Worker プロキシ**: `cloudflare-worker/cloudflare-worker-v5.js` は Replicate へのプロキシとして動作し、R2 への保存や許可済みエンドポイントの検証に対応します。独自にデプロイしてエンドポイント URL を得る必要があります。
- **シェルスクリプト**: `sh/imgen_batch.sh` はバッチで Replicate を呼び出す CLI ラッパーで、ブラウザを開かずにまとめて生成したいときに使えます。

## 使い方
1. リポジトリのルートを静的サーバーで配信します（例: `python -m http.server 8000`）。ブラウザで `index.html` を開きます。
2. 右上の設定モーダル（⚙️）から Replicate API キーを入力し、必要に応じて Cloudflare Worker のプロキシ URL を変更します（既定値: `https://replicate-nanobanana.skume-bioinfo.workers.dev/proxy`）。
3. ノードを追加してエッジを接続し、プロンプトやアスペクト比を設定します。ツールバーからトランスフォームやバッチ生成を実行すると、エッジ情報を基に Cloudflare Worker にリクエストが送信されます。

## 運用上の注意
- アスペクト比やプロンプトの入力欄は `index.html` と `workflowApp.js` に実装されています。保存先はエッジのデータ属性で、`localStorage` には API キー等の設定のみ保存されます。
- Replicate への呼び出しは `TransformationService` を経由して Cloudflare Worker に送られます。プロキシのエンドポイントが無効な場合や CORS の設定に問題がある場合、ブラウザのコンソールにエラーが表示されます。
- `../favicon/nu-circle.svg` が参照されていますが、`favicon/` ディレクトリは同梱されていません。必要であれば自身で追加してください。
- Cloudflare Worker のデプロイは自動化されていません。`cloudflare-worker/cloudflare-worker-v5.js` を Cloudflare にアップロードし、得られたエンドポイントを設定モーダルに入力してください。

## 関連ドキュメント
- `paper_jpn.md`: 仕組みや背景の解説を日本語でまとめたドキュメントです。併せて参照してください。

