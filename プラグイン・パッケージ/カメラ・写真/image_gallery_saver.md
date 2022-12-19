# image_gallery_saver

### 概要

- image_pickerプラグインを使用して、AndroidおよびiOSの画像ライブラリから画像を選択しますが、ギャラリーに画像を保存することはできません。このプラグインはこの機能を提供できます。

### 引用記事

- [image_gallery_saver 1.7.1](https://pub.dev/packages/image_gallery_saver) 2021/10

### 使い方

- iOS
  - プロジェクトはswiftで作成する必要があります。
Info.plistファイルに次のキーを追加します。
  - NSPhotoLibraryAddUsageDescription - 
  アプリがフォトライブラリの許可を必要とする理由を説明してください。これはプライバシーと呼ばれます - ビジュアルエディタでフォトライブラリの追加使用法の説明
  
- Android
  - 画像をギャラリーに保存するには、ストレージ許可を求める必要があります。ストレージ権限はflutter_permission_handlerを使用して処理できます。Androidバージョン10では、マニフェストファイルを開き、この行をアプリケーションタグに追加します。
    ```dart
    <application android:requestLegacyExternalStorage="true" .....>
	```

### コード

- `インターネットから画像を保存`

```dart
_save() async {
   var response = await Dio().get(
           "https://ss0.baidu.com/94o3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=a62e824376d98d1069d40a31113eb807/838ba61ea8d3fd1fc9c7b6853a4e251f94ca5f46.jpg",
           options: Options(responseType: ResponseType.bytes));
   final result = await ImageGallerySaver.saveImage(
           Uint8List.fromList(response.data),
           quality: 60,
           name: "hello");
   print(result);
  }
```

- `インターネットからファイルを保存`
```dart
_saveVideo() async {
    var appDocDir = await getTemporaryDirectory();
    String savePath = appDocDir.path + "/temp.mp4";
    await Dio().download("http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4", savePath);
    final result = await ImageGallerySaver.saveFile(savePath);
    print(result);
 }
```
