# shared_preferences
最終編集：19/12/2022 12:07

### 概要

- Flutterでちょっとしたデータをローカルに保存したい場合、Shared PreferencesというFlutter公式のプラグインを使用することで簡単に実現できる。例えばアプリに関する設定情報などのデータを保存したい場合に便利
- 保存できるデータ型
  - int
  - double
  - bool
  - String
  - String型のList

### 引用記事

- [FlutterでSharedPreferencesを使ってローカルにデータを保存する](https://virment.com/how-to-use-shared-preferences-in-flutter/) 2019/11/20

### 使い方
- 最初にインスタンスを取得する
```dart
final SharedPreferences prefs = await SharedPreferences.getInstance();
```
- データを読み書きする
```dart
//保存
prefs.setInt('counter', 123);
//取得
prefs.getInt('counter') ?? 0;
```

ね！簡単でしょ！

### コード

- `main.dart`

```dart
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);

  final String title;

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State {
  int _counter = 0;

  void _incrementCounter() async {
    setState(() {
      _counter++;
      _setPrefItems();  // Shared Preferenceに値を保存する。
    });
  }

  // Shared Preferenceに値を保存されているデータを読み込んで_counterにセットする。
  _getPrefItems() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    // 以下の「counter」がキー名。見つからなければ０を返す
    setState(() {
      _counter = prefs.getInt('counter') ?? 0;
    });
  }

  // Shared Preferenceにデータを書き込む
  _setPrefItems() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    // 以下の「counter」がキー名。
    prefs.setInt('counter', _counter);
  }

  // Shared Preferenceのデータを削除する
  _removePrefItems() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();

    setState(() {
      _counter = 0;
      // 以下の「counter」がキー名。
      prefs.remove('counter');
    });
  }

  @override
  void initState() {
    super.initState();
    // 初期化時にShared Preferencesに保存している値を読み込む
    _getPrefItems();
  }

  @override
  Widget build(BuildContext context) {
   
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(
              'You have pushed the button this many times:',
            ),
            Text(
              '$_counter',
              style: Theme.of(context).textTheme.display1,
            ),
            RaisedButton(
              child: Text("削除する"),
              color: Colors.blue,
              onPressed: () => _removePrefItems(),
            )
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}

```
