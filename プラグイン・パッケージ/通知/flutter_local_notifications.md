# flutter_local_notifications

最終更新：19/12/2022 14:25

### 概要

- ローカル通知を表示するためのクロスプラットフォームプラグイン。
- さまざまオプションが存在するので公式リファレンスで都度確認するようにしよう！

### 引用記事

- [lutter_local_notifications 12.0.4](https://pub.dev/packages/flutter_local_notifications) 2022/11
- [[Flutter] ローカルプッシュ通知](https://dev-yakuza.posstree.com/flutter/flutter_local_notifications/) 2022/10
- [flutter_local_notificationでローカル通知を出す方法](https://zenn.dev/tomon9086/articles/d2624f6ab37c4c) 2021/8/19

### 使い方

- iOS
  - ありがちな info.plist の編集は不要ですが、Swiftのコードをイジる必要があります。
Flutterプロジェクトに自動生成される AppDelegate.swift というファイルに以下のコードを追加します。

 `ios/Runner/AppDelegate.swift`
```swift
@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
  override func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
  ) -> Bool {
+   // flutter_local_notification
+   if #available(iOS 10.0, *) {
+     UNUserNotificationCenter.current().delegate = self as? UNUserNotificationCenterDelegate
+   }

    GeneratedPluginRegistrant.register(with: self)
    return super.application(application, didFinishLaunchingWithOptions: launchOptions)
  }
}
```

- Android
  - 特に設定しなくても通知自体は動きますが、バナーを出したい場合はfullScreenIntentに対応する必要があるので AndroidManifest.xml に以下のように追加します。
`android/app/src/main/AndroidManifest.xml`
```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="com.example.myapp">
    <application
          android:label="myapp"
          android:icon="@mipmap/ic_launcher">
          <activity
              android:name=".MainActivity"
              android:launchMode="singleTop"
              android:theme="@style/LaunchTheme"
              android:configChanges="orientation|keyboardHidden|keyboard|screenSize|smallestScreenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"
              android:hardwareAccelerated="true"
+             android:showWhenLocked="true"
+             android:turnScreenOn="true">
                ︙
```



### コード

- iOS
  - ボタンか何かに書いて実行すると、通知権限の許可ダイアログが出て、即時でバナーが出ます。
  - initialize のタイミングで権限がリクエストされるので、アプリ起動時にリクエストしたいときは main とかでinitializeします。また、initialize 前に show を呼んでも権限がないので通知が出せません。
何度initializeしても問題なさそうなので、権限のリクエストが初めて通知するタイミングで良ければ、このnotify関数をいじってラッパーにしてしまっても便利だと思います。

```dart
import 'package:flutter_local_notifications/flutter_local_notifications.dart';

Future<void> notify() {
  final flnp = FlutterLocalNotificationsPlugin();
  return flnp.initialize(
    InitializationSettings(
      iOS: IOSInitializationSettings(),
    ),
  ).then((_) => flnp.show(0, 'title', 'body', NotificationDetails()));
}
```

- Android
  - ボタンか何かに書いて実行すると、通知権限の許可ダイアログが出て、即時でバナーが出ます。
  - Androidは通知に使うアイコンを指定するためにinitializeが必要です。
また、通知チャネルの概念が存在するので、NotificationDetailsに設定を渡す必要があります。
iOSユーザのために軽く説明すると、通知チャネルは通知の種類を定義するもので、ユーザは設定からチャネルごとに通知をオンオフできます。例えばECアプリで「おすすめ商品」と「クーポン情報」でチャネルを分けておくと、クーポンだけ通知がほしいユーザはおすすめ商品の通知を選択的にオフにできます。

```dart
import 'package:flutter_local_notifications/flutter_local_notifications.dart';

Future<void> notify() {
  final flnp = FlutterLocalNotificationsPlugin();
  return flnp.initialize(
    InitializationSettings(
      android: AndroidInitializationSettings('@mipmap/ic_launcher'),
    ),
  ).then((_) => flnp.show(0, 'title', 'body', NotificationDetails(
    android: AndroidNotificationDetails(
      'channel_id',
      'channel_name',
      'channel_description',
    ),
  )));
}
```
