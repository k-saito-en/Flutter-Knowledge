# webview_flutter

記事作成日：2022/12/20

### 概要

- WebViewウィジェットを提供するFlutterプラグイン。
- iOSでは、WebViewウィジェットは[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview)によってバックアップされています。Androidでは、WebViewウィジェットはaWebViewによってバックアップされています。

### 引用記事

- [webview_flutter 4.0.0](https://pub.dev/packages/webview_flutter) 2022/12/18
- [Flutter公式のwebview_flutterでWebViewを表示する](https://zuma-lab.com/posts/flutter-web-view) 2021/3

### 使い方

- Androidの場合
  - お約束の依存関係追記.yaml
  - /android/app/build.gradle の minSdkVersion を変更する（SurfaceAndroidWebView を利用する為の最低 API version が 19 である為に変更が必要）
    ```gradle
        android {
        defaultConfig {
            // Required by the Flutter WebView plugin.
            // minSdkVersion を 19 に変更
            minSdkVersion 19
        }
    }
    ```
  - WebView を起動する画面を実装する
    `lib/first_view.dart`
    ```dart
    import 'package:flutter/material.dart';
    import 'package:flutter_web_view/web_view_screen.dart';

    class FirstView extends StatelessWidget {
      @override
      Widget build(BuildContext context) {
        return MaterialApp(
          home: Scaffold(
            appBar: AppBar(
              title: const Text('Web View Sample'),
            ),
            body: _FirstView(),
          ),
        );
      }
    }

    class _FirstView extends StatelessWidget {
      @override
      Widget build(BuildContext context) {
        return Center(
          child: ElevatedButton(
            onPressed: () {
              Navigator.push(
                context,
                MaterialPageRoute<WebViewScreen>(
                  builder: (BuildContext _context) => WebViewScreen(),
                ),
              );
            },
            child: const Text('Launch Web View'),
          ),
        );
      }
    }
    ```
  - WebView 画面を実装する
    `lib/web_view_screen.dart`

    ```dart
    import 'dart:async';
    import 'dart:io';

    import 'package:flutter/material.dart';
    import 'package:webview_flutter/webview_flutter.dart';

    class WebViewScreen extends StatefulWidget {
      @override
      _WebViewScreenState createState() => _WebViewScreenState();
    }

    class _WebViewScreenState extends State<WebViewScreen> {
      // この WebViewController は Web サイトタイトルを取得する為に必要
      final Completer<WebViewController> _controller =
          Completer<WebViewController>();

      // 次にローディング中状態を保持する _isLoading フラグと、Web サイトタイトルを保持する _title を定義します。
      // このプロパティを setState で状態変更してローディング状態や、AppBar に表示する Web サイトタイトルを管理します。
      bool _isLoading = false;
      String _title = '';

      // Android はデフォルトの WebView 設定では日本語入力ができません。
      // そこで initState の初期化処理で WebView に SurfaceAndroidWebView を設定します。
      // キーボード入力が必要な場合は、Hybrid Composition ベースの PlatformView を使うことが推奨されています。
      // 上記のコードを追加することで Hybrid Composition ベースの PlatformView を使うので、日本語入力等出来るようになるわけです。 -->
      @override
      void initState() {
        super.initState();
        if (Platform.isAndroid) {
          WebView.platform = SurfaceAndroidWebView();
        }
      }

      // ローディング状態や Web サイトタイトルを画面に反映させる
      // setState で変更された状態が画面に反映される処理をいれます
      // ここでは AppBar に先程定義した _title プロパティを設定しています。
      // また、先程定義した _isLoading フラグが true の場合 AppBar の下部に LinearProgressIndicator を表示する実装をしています。 -->
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          appBar: AppBar(
            title: Text(_title),
          ),
          body: _buildBody(),
        );
      }

      Widget _buildBody() {
        return Column(
          children: [
            if (_isLoading) const LinearProgressIndicator(),
            Expanded(
              child: _buildWebView(),
            ),
          ],
        );
      }
      
      /// initialUrl に表示したいサイト URL を設定します。
      /// プロダクトでは動的に URL を設定する思いますが、今回はサンプルの為ハードコーディングしています。
      /// JavaScript はデフォルトで無効になっているので、JavascriptMode.unrestricted で 有効にします。
      /// onWebViewCreated で WebViewController を WebView に登録します。
      /// onPageStarted で Web サイトの読み込みが開始した場合の処理を実装します。
      /// 今回は読み込み中にローディングインジケーターを表示するので、 _isLoading = true としています。
      /// onPageFinished で Web サイトの読み込み終了した場合の処理を実装します。
      /// 読み込み中のローディングインジケーターを非表示にする為、 _isLoading = false としています。
      /// 最後に controller.getTitle() で web サイトタイトルを取得、 _title プロパティに代入して AppBar にタイトルを表示します。
      
      Widget _buildWebView() {
        return WebView(
          initialUrl: 'https://flutter.dev',
          // jsを有効化
          javascriptMode: JavascriptMode.unrestricted,
          // controllerを登録
          onWebViewCreated: _controller.complete,
          // ページの読み込み開始
          onPageStarted: (String url) {
            // ローディング開始
            setState(() {
              _isLoading = true;
            });
          },
          // ページ読み込み終了
          onPageFinished: (String url) async {
            // ローディング終了
            setState(() {
              _isLoading = false;
            });
            // ページタイトル取得
            final controller = await _controller.future;
            final title = await controller.getTitle();
            setState(() {
              if (title != null) {
                _title = title;
              }
            });
          },
        );
      }
    }
    ```
