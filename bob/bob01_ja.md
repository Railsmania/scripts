## Bootstrap!

このビデオでは、新しいRailsアプリケーションを作成し、無料のHTMLダッシュボードテーマを追加の仕方を紹介します。このテーマはすでにいろんなグラフがたくさん含まれているほか、ブートストラップ(Bootstrap)に基づいているので、モバイルフレンドリーです。

Railsのプロジェクトにブートストラップのテーマを追加するのは非常に簡単ですので、さっそく始めましょう！

最初に、ターミナルウィンドウを開いてRailsプロジェクトを作成しましょう。プロジェクトには"bob"という名前をつきましょう。プロジェクトを作るために適当なディレクトリに```rails new bob```というコマンドを実行します。

プロジェクトのすべてのファイルを生成するために、しばらくの時間がかかるかもしれません。

終わったら問題がないことを確認するためにHTTPサバーを起動しましょう。

ターミナルで"bundle exec rails　server"というコマンドを実行します。新しいブラウザタブを開いて、サーバのURLにアクセスしてください。

生成されたRailsのデフォルトのページを見ることができます。このページは現在のアプリケーションを実行している環境に関する情報が表示されています。もしこのデフォルトのページを見ることができればすべてが正常に動作しています。

質問：どのようにデフォルト以外のページを表示できますか？

答え：routes.rbファイルにルート(route)を追加します。アプリケーションのページのURLのすべてがroutes.rbファイルに追加される必要があります。こうしましょう：

routes.rbファイルを開きます。rootが定義されているコードのコメントを外してみましょう​。
クールな管理ダッシュボードにするために、rootのルート(route)をダッシュボードコントローラーとインデックスアクションの名前に変更してファイルを保存しましょう。
````
root 'dashboard#index'
````
ブラウザタブを再読み込みしてください。デフォルトのページがなくなっていることに注意してください。かわりにこの素敵なエラーメッセージページに置き換えられています。ルーティングエラーとなっていることを示しています。原因はDashboardsControllerが存在しないためです。

ダッシュボードコントローラとインデックスを作成してみましょう。

app/controllersのディレクトリで、dashboards_controller.rbという名前のファイルを作成して、次のコードを書きましょう：
```
class DashboardsController < ApplicationController
  def index
  end
end
```

DashboardsControllerはすでにRailsに作られてあるApplicationControllerを継承します。indexアクションは特別なロジックがないのでなにも書く必要がありません。

次に、このアクション用のビューを作成する必要があります。app/viewsディレクトリでコントローラーと同じ名前のdashboardsというディレクトリを作りましょう。そのディレクトリの中にindex.html.erbのファイルを追加します。次に、index.html.erbに、この短いテキストを入力しましょう。
```
<h1>In Dashboards Index</h1>
```

保存して、rails server を再起動し、ブラウザでプロジェクトを表示するとダッシュボードのインデックスビューとそのテキストが表示されます。

今からBootstrapのテーマを追加してみましょう。ほとんどのBootstrapのテーマの中にはHTMLのサンプルとすべてのCSSとJavascriptがフォルダごとに含まれています。今このままで置いておきましょう。

テーマのすべてのCSSをassets/stylesheetsのディレクトリにコピーします。もし同じCSSで改行されたファイルと改行されていないファイルがあったら、改行されていないほうをコピーしてください。

Javascriptをassets/javascripts/にコピーします。CSSと同じように同じJavascriptで改行されたファイルと改行されていないファイルがあったら、改行されていないほうをコピーしてください。

画像ファイルをassets/images/にコピーします。

最後に、カスタムフォントがある場合, assets/fontsというディレクトリを作成し、フォントをそこにコピーしてください。


HTMLのサンプルをビユーに利用します。一番簡単な方法としてrailsのレイアウト（app/views/layouts/application.html.erb）に利用できますが、一つのアプリケーションでたくさんのテーマを使えるようにする方法としてレイアウトにHTMLは設定しません。レイアウトではjavascript_include_tagのタグを今の場所からページのbodyの一番下に移動します。つぎに、サンプルのHTMLの最初からjavascriptが定義されている手前の行までを選択して/app/views/dashboards/index.html.erbのファイルにコピーしてください。


ファイルを保存して、ブラウザを再読み込みしてください。どうでしょう、素晴らしいように見えますが、不足していることもいくつかあります。ブラウザの開発ツールを使うと何が壊れているかを見ることができます。この問題を修正する方法ですが、少し裏技を使ってこうします：

FontAwesomeのcssファイルを、ERBファイルに変換してください。こうするとRailsのパスヘルパーを使用して、パスを修正することができます:

```
  src: url('<%= asset_path("fontawesome-webfont.eot") %>?v=4.1.0');
  src: url('<%= asset_path("fontawesome-webfont.eot") %>?#iefix&v=4.1.0') format('embedded-opentype'), url('<%= asset_path("fontawesome-webfont.woff") %>?v=4.1.0') format('woff'), url('<%= asset_path("fontawesome-webfont.ttf") %>?v=4.1.0') format('truetype'), url('<%= asset_path("fontawesome-webfont.svg") %>?v=4.1.0#fontawesomeregular') format('svg');

````

次に、Javascriptの一部を修正する必要があります。ブラウザの開発ツールのコンソールでエラーが出ている二つのJavaScriptファイルをスタブします。これらの2つのファイルは、ただのデモファイルなのでスタブのキーワードを使用して無視するようにしても問題はありません。
app/assets/javascripts/application.jsに追加します：
```
//= stub plugins/flot/flot-data
//= stub plugins/dataTables/dataTables.bootstrap
```

こうすると後からでも利用できるように、プロジェクトにファイルを残したままで、スタブのキーワードがそれらを無視するように指示します。

ブラウザを再び読み込むとページはエラーを示めさず、テーマがすべてちゃんとここに表示されています。

これは今回のレッスンでした。RailsMania.comで共有している関連コンテンツやその他のより詳細なトピックをご覧ください。
