# flutter_inappwebview

### 概要

- WebView機能だけでなく、アプリ内で表示できるブラウザを扱う事ができます。（公式より多機能やん）
- 機能は大きくわけて「InAppWebView」「InAppBrowser」「ChromeSafariBrowser」の３つ用意されています。
- このプラグインはとても細かい設定が可能

### 引用記事

- [Flutter】公式より多機能なWebViewプラグイン「flutter_inappwebview」の使い方](https://sashimistudio.site/flutter-inappwebview-plugin/) 2020/01

### 使い方

- InAppWebView （WebView表示機能）
  - `main.dart`
    ```dart
    import 'package:flutter_inappwebview/flutter_inappwebview.dart';
    // InAppWebViewクラス内の引数を指定してWebViewをカスタマイズしていきます。
    class AppWebView extends StatelessWidget {

      // URL
      final String appUrl;

      AppWebView({this.appUrl});

      @override
      Widget build(BuildContext context) {

        ViewWidget _viewWidget = new ViewWidget(appUrl: appUrl);

        return SafeArea(
          left: false,
          right: false,
          bottom: false,
          child: Container(
            child: _viewWidget.createWidget(),
          ),
        );
      }
    }

    /// webviewを表示させるWidget生成クラス
    class ViewWidget {

      // URL
      final String appUrl;

      ViewWidget({this.appUrl});

      InAppWebViewController _webviewController;

      InAppWebView createWidget() {

        return InAppWebView(
          initialUrl: appUrl,
          initialHeaders: {},
          initialOptions: InAppWebViewWidgetOptions(
            inAppWebViewOptions: InAppWebViewOptions(
              // 共通オプション
              horizontalScrollBarEnabled: false,
              verticalScrollBarEnabled: false,
              debuggingEnabled: true,
            ),
            iosInAppWebViewOptions: IosInAppWebViewOptions(
              // iOSオプション
              sharedCookiesEnabled: true,
            ),
            androidInAppWebViewOptions: AndroidInAppWebViewOptions(
              // Androidオプション
              thirdPartyCookiesEnabled: true,
            ),
          ),
          onWebViewCreated: (InAppWebViewController controller) async {
            /// ページ生成後に処理される関数
            /// ページ生成後のオブジェクトを取得可能
            _webviewController = controller;
            Favicon faviconUrl = await _getFavicon();
            debugPrint(faviconUrl.url);
          },
        );
      }

      /// 表示サイトのfaviconを取得する関数
      Future<Favicon> _getFavicon() async {
        List<Favicon> _favicon;
        _favicon = await _webviewController.getFavicons();
        return _favicon[0];
      }
    }
    ```
  - InAppBrowser　（ブラウザ表示機能）
  - ChromeSafariBrowser　（ブラウザ表示機能）

### コード

- `ディレクトリ`

```dart

```

