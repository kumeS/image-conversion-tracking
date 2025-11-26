# Image Conversion Tracker

ブラウザ上でノードベースのワークフローを作成し、AI 画像変換を可視的に管理するシステムです。フレームワークやバンドラー不要で、vanilla JavaScript・Tailwind CSS・vis-network を利用しています。

## 概要
- **ノード／エッジの編集**: ノードの追加・削除、接続、詳細パネルの参照、オートレイアウトに対応し、視覚的にフローを組み立てられます。
- **プロンプトとアスペクト比の管理**: エッジにプロンプト・モデル・アスペクト比を保存し、プロンプトモーダルから編集可能。詳細パネルでは `1:1` や `16:9` などの比率を確認できます。
- **バッチ生成ツールバー**: バッチ生成、アップロード、マージ、変換の各ボタンが `workflowApp.js` と `canvasController.js` を経由してネットワーク状態を更新します。
- **API キー設定**: 設定モーダルで Replicate API キー（および IO Intelligence API キー）を入力し、`localStorage` に保存します。
- **Replicate / Nano Banana 連携**: `TransformationService` が Cloudflare Worker 経由で `google/nano-banana` を呼び出し、アスペクト比や出力形式の指定を尊重します。

## 構成要素
- **フロントエンド**: `index.html`、`css/workflow.css`、`js/` 配下のモジュール（`workflowApp.js`、`canvasController.js`、`config.js`、`transformationService.js` など）。
- **Cloudflare Worker プロキシ**: `cloudflare-worker/cloudflare-worker-v5.js` が Replicate API へのプロキシを実装し、許可されたエンドポイントの検証や R2 への保存を任意で行います。
- **シェル補助**: `sh/imgen_batch.sh` が Replicate へのバッチ呼び出し用 CLI ラッパーを提供します。

## 使い方
1. リポジトリのルートを任意の静的サーバーで配信し（例: `python -m http.server 8000`）、ブラウザで `index.html` を開きます。
2. 設定モーダル（⚙️）で Replicate API キーを入力します。既定のプロキシは `https://replicate-nanobanana.skume-bioinfo.workers.dev/proxy` です。
3. ノードを追加してエッジを接続し、プロンプトとアスペクト比を設定したうえでツールバーから変換またはバッチ生成を実行します。

## 実装状況（この版）
- ✅ アスペクト比フィールドとプロンプト編集 UI は `index.html` / `workflowApp.js` に実装済みで、エッジに保存されます。
- ✅ Replicate への呼び出しは `TransformationService` を介して Cloudflare Worker プロキシへルーティングされます。
- ⚠️ `../favicon/nu-circle.svg` を参照していますが、リポジトリに `favicon/` ディレクトリは存在しません。
- ⚠️ Cloudflare Worker のデプロイは自動化されていません。`cloudflare-worker-v5.js` を手動でデプロイし、エンドポイントを指定してください。
