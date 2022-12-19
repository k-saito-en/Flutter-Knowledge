# Secure Storage

最終編集：19/12/2022 13:09

### 概要

- 安全なストレージにデータを保存するためのFlutterプラグイン:

  - [キーチェーン](https://developer.apple.com/library/content/documentation/Security/Conceptual/keychainServConcepts/01introduction/introduction.html#//apple_ref/doc/uid/TP30000897-CH203-TP1)はiOSで使用されます
  - AES暗号化はAndroidで使用されます。AESシークレットキーはRSAで暗号化され、RSAキーは[KeyStore](https://developer.android.com/training/articles/keystore.html)に保存されます
V5.0.0では、次のようにAndroidオプションで有効にすることで、Androidで[EncryptedSharedPreferences](https://developer.android.com/topic/security/data)を使用できます。
- AndroidはGoogleドライブにデータをバックアップします

### 引用記事

- [flutter_secure_storage 7.0.1](https://pub.dev/packages/flutter_secure_storage) 2022/12/13
- [flutter_secure_storageでセキュアに情報を保存する](https://flutter.tnantoka.com/entry/2019/05/29/225128) 2019/5/29

### 使い方

- 導入（いつものお約束yaml）
- Androidの方は使い方が少々複雑そうなので、使う際に再度調べよう

### コード

```dart
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

// Create storage
final storage = new FlutterSecureStorage();

// Read value
String value = await storage.read(key: key);

// Read all values
Map<String, String> allValues = await storage.readAll();

// Delete value
await storage.delete(key: key);

// Delete all
await storage.deleteAll();

// Write value
await storage.write(key: key, value: value);
```
