# cached_network_image

### 概要

- ネットワークから取得した画像をキャッシュして、2 回目以降の表示を高速化できる
- http を使ってネットワークから取得した画像を path_provider によって提供されたアプリの一時ディレクトリに file を利用して保存しています。ファイルに関する情報は sqflite を使用してデータベースに保存しています。
- これらのキャッシュ機能は flutter_cache_manager を利用して実現しています。cacheManager プロパティを与えることで、ファイルの最大経過時間、キャッシュする画像の最大数、ファイル保存先や保存方法の変更などをカスタマイズできます。

### 引用記事

- [【Flutter】ネットワークから取得した画像をキャッシュして表示を高速化する](https://zenn.dev/susatthi/articles/20220615-160504-flutter-cached-network-image-test) 2022/6

### 使い方

- URLを渡してあげるだけ！非常に簡単

```dart

CachedNetworkImage(
  imageUrl: 'https://avatars.githubusercontent.com/u/13707135?v=4',
),

```
- 読み込み中に進捗表示する（オプション例）
```dart
CachedNetworkImage(
  imageUrl: 'https://avatars.githubusercontent.com/u/13707135?v=4',
+  progressIndicatorBuilder: (context, url, downloadProgress) =>
+      CircularProgressIndicator(value: downloadProgress.progress),
),
```
  

### コード

- `ディレクトリ`

```dart

```
