# URLスキーム

AnkiMobileは、サードパーティの辞書を開くためや、他のアプリケーションからAnkiMobileにコンテンツを追加するためのURLスキームをサポートしています。

## AnkiMobileのURLスキーム

### ノートの追加

「URLスキーム」とは、ウェブサイトへのリンクのようなもので、アプリへのリンクです。簡単な例は次のとおりです：

    anki://

これをデバイスのSafariに入力すると、AnkiMobileが開きます。

バージョン2.0.30以降、AnkiMobileはコレクションに新しいノートを追加するためのスキームを提供しています。これは、URLスキームを介して他のアプリを呼び出すことをサポートする辞書アプリや、Workflowのような自動化アプリで使用できます。

カードを追加するためのスキームは次のようになります：

    anki://x-callback-url/addnote?profile=User%201&type=Basic&deck=Default&fldFront=front%20text&fldBack=back%20text

最初の部分は常に必要です：

    anki://x-callback-url/addnote?

最初の部分の後、キーと値はアンパサンドで区切られます。以下のキーは常に提供する必要があります：

- `type=<ノートタイプ名>`

- `deck=<デッキ名>`
  - ネストされたデッキは `::` で区切ります
    - `deck=<デッキ名>::<サブデッキ名>`

フィールドは名前の前に "fld" を付けて入力します。例えば、最初のフィールドが "Text" と呼ばれる場合、キーは "fldText" になります。フィールドのテキストはHTMLとして解釈されるため、テキストに改行を入れたい場合は "line 1&lt;br&gt;line 2" のようにします。

URL内の特殊文字はエスケープする必要があります。例えば、フィールドにスペースが含まれている場合、それは `%20` で表現する必要があります。Shortcutsアプリを使用している場合、URLエンコードアクションが役立つかもしれません。

残りのキーはオプションです：

- `profile=<プロファイル名>`

  指定された場合、指定されたプロファイルが現在アクティブでないと追加は失敗します。

- `tags=<スペースで区切られたタグ>`

- `dupes=1` - 指定された場合、既存のノートに同じ内容があってもノートを追加できます。

- `x-success=<他のアプリのURLスキーム>` - ノートが追加された後に自動的に他のアプリに戻るために使用します。

提供されたフィールドが画像や音声ファイルへのリンクである場合、AnkiMobileはそのメディアを自動的にダウンロードし、フィールドにリンクを配置します。例：

    fldFront=http://example.com/image.jpg

リンクは、認識されたファイル拡張子で終わる場合にのみダウンロードされます。リンクが動的なウェブページの場合、末尾に拡張子を持つ偽の引数を追加できます。例えば、`http://example.com/getImage?imgID=1234&fakePath=foo.jpg` のようにします。URLスキームに含める前にURLをエスケープするように注意してください。そうしないと、`?` や `&` の文字がURLスキームの一部として解釈されてしまいます。

### 追加に関する情報

サードパーティのアプリがプロファイル、デッキ、およびノートタイプの名前を取得するための別のURLスキームがあります。これにより、ユーザーにわかりやすい形で情報を提示できます。

アプリは次のURLを呼び出す必要があります：

`anki://x-callback-url/infoForAdding?x-success=...`

ユーザーがリクエストを承認すると、要求されたデータがクリップボードに保存され、以下の方法で取得できます。データを取得した後は、クリップボードをクリアする必要があります。

```swift
let PASTEBOARD_TYPE = "net.ankimobile.json"
if let data = UIPasteboard.general.data(forPasteboardType: PASTEBOARD_TYPE) {
   // clear clipboard
   UIPasteboard.general.setData(Data(), forPasteboardType: PASTEBOARD_TYPE)
   // ... handle json
}
```

### 検索

AnkiMobile 2.0.90以降で利用可能です。指定された検索文字列で検索を開くことができます。

`anki://x-callback-url/search?query=...`

### 同期

AnkiMobile 2.0.90以降で利用可能です。同期ボタンをタップするのと同じ動作を行います。

`anki://x-callback-url/sync`

## 辞書リンク

デスクトップ版のAnkiでは、フィールドの内容に基づいてウェブページを開く機能が提供されており、オンライン辞書サイトへのリンクを簡単に作成できます。これはマニュアルの[このセクション](https://shigeyukey.github.io/anki-manual-jp/templates/fields.html#辞書リンク)に記載されていますが、まず[カードとテンプレート](https://shigeyukey.github.io/anki-manual-jp/templates/intro.html)のセクションから始めてください。上記のリンクはその前提で書かれています。

デスクトップ版と同様に、AnkiMobileも辞書ウェブサイトへのリンクをサポートしています。さらに、URLスキームを使用してデバイスにインストールされている異なる辞書アプリへのリンクも作成できます。

一部の辞書アプリは、特定のフレーズを検索するためのURLスキームを提供しています。例えば、iDict+というアプリは以下のようなURLを許可しています：

    idictplus://?search=mysearchtext

iDict+がインストールされている場合、SafariにこのURLを入力すると、iDict+が開き、「mysearchtext」をすぐに検索します。

iDict+はリターンリンクを渡すこともサポートしています。上記を次のように置き換えることができます：

    idictplus://?search=txt&scheme=anki://

これをSafariに入力すると、以前と同様に「mysearchtext」を検索しますが、「リターン」ボタンも表示され、押すとAnkiが開きます。

このテキストを取り、上記のデスクトップ版の指示と組み合わせることで、カード上に他のアプリで単語を検索し、完了したらAnkiに戻るリンクを作成することが可能です。

残念ながら、URLスキームには標準がありません。一部のアプリはそれを実装しており、一部はしておらず、実装方法も異なる場合があります。例えば、「Kotoba!」というアプリでは、URLスキームは次のようになります：

    kotoba://dictionary?word=mysearchtext

アプリ名だけでなく、その後のテキストも異なることに注意してください。また、Kotoba!は開いたアプリに自動的に戻る機能を提供していないため、ホームボタンをダブルタップしてAnkiMobileに手動で戻る必要があります。

一部の辞書アプリは、ドキュメントでURLスキームを公開しています。使用している辞書アプリが公開していない場合は、著者に連絡して詳細を尋ねてください。
