# その他の機能

## Javascript

[警告](https://shigeyukey.github.io/anki-manual-jp/templates/styling.html#javascript)はコンピュータ版にもAnkiMobileにも適用されます。

上記に加えて、AnkiMobileではコードがAnkiMobileのタップ検出とうまく連携する必要があることにも注意してください。A/BUTTON要素やonclickハンドラを持つ要素へのタップは、期待通りに動作するはずです。

タップイベントを受け取る必要がある他の要素がある場合は、それらにクラス 'tappable' を付与して、AnkiMobile 2.0.39以降がタップを要素に渡すように指示してください。

## 再生ボタン

オーディオ再生ボタンのスタイルを変更するには、<https://shigeyukey.github.io/anki-manual-jp/templates/styling.html#オーディオ再生ボタン> を参照してください。

## 画像のリサイズ

フォトギャラリーから画像を添付すると、自動的に最長辺が1024になるようにリサイズされます。

最近のAnkiバージョンでは、**設定 > 一般** 画面でこの制限をカスタマイズし、クリップボードから貼り付けた画像にもこの制限を適用するかどうかを制御できます。