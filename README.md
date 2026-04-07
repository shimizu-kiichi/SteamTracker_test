# SteamTracker_test
steamtracker静的HTMLの実験

## 現在の実装メモ

- フロントは GitHub Pages 想定の単一ファイル構成（index.html）。
- ヒーロー画像は外部 Drive 参照をやめ、ローカル相対パス参照に変更済み。
	- stuffFormロゴ/RDXロゴ.jpg
	- stuffFormロゴ/STEAMコモンズロゴ.jpg
- GAS 呼び出しは CORS + JSON 応答を前提に変更済み（no-cors は不使用）。

## 必須設定（index.html）

index.html の GAS_URL を Web アプリURLに差し替えること。

const GAS_URL = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';
const DOMAIN = 'mail.ryukoku.ac.jp';

## GAS 側で必要な doPost 実装例

```javascript
function doPost(e) {
	try {
		const payload = JSON.parse(e.postData.contents);
		const action = payload.action;

		let result;
		if (action === 'uploadPhoto') {
			result = uploadPhoto(payload.dataUrl, payload.filename);
		} else if (action === 'submitForm') {
			result = submitForm(payload);
		} else {
			result = { status: 'error', message: '不明なアクションです' };
		}

		return ContentService
			.createTextOutput(JSON.stringify(result))
			.setMimeType(ContentService.MimeType.JSON);
	} catch (err) {
		return ContentService
			.createTextOutput(JSON.stringify({ status: 'error', message: String(err) }))
			.setMimeType(ContentService.MimeType.JSON);
	}
}
```

## CORS 対応メモ

- GAS 側で Access-Control-Allow-Origin を返せる設計にしておくと、フロントで応答を読める。
- no-cors に戻すと、失敗時もフロントで検知できないため非推奨。
