# Drift
最終編集：19/12/2022 12:08

### 概要
- Drift とは Dart/Flutterのアプリケーションでデータの永続化を行うためのライブラリです。
- sqflite や sql.js のようなデータベースライブラリの上に構築されていて、
  Streamでデータを受け取ったり、
  SQLの知識が無くともDartのみでデータの追加や更新ができる機能を持っています。
  
### 引用記事
- [【Flutter】 Drift の基本的な使い方解説](https://blog.flutteruniv.com/flutter-drift/#toc1)

### 使い方
-

### コード

- `drift_sample/drift_sample/lib/main.dart`

```dart
import 'package:drift_sample/src/drift/todos.dart';
import 'package:flutter/material.dart';

void main() {
  final database = MyDatabase();
  runApp(MyApp(database: database));
}

class MyApp extends StatelessWidget {
  const MyApp({
    Key? key,
    required this.database,
  }) : super(key: key);

  final MyDatabase database;

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: DriftSample(database: database),
    );
  }
}

class DriftSample extends StatelessWidget {
  const DriftSample({
    Key? key,
    required this.database,
  }) : super(key: key);

  final MyDatabase database;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            Expanded(
              child: StreamBuilder(
                stream: database.watchEntries(),
                builder:
                    (BuildContext context, AsyncSnapshot<List<Todo>> snapshot) {
                  if (snapshot.connectionState == ConnectionState.waiting) {
                    return const Center(child: CircularProgressIndicator());
                  }
                  return ListView.builder(
                    itemCount: snapshot.data!.length,
                    itemBuilder: (context, index) => TextButton(
                      child: Text(snapshot.data![index].content),
                      onPressed: () async {
                        await database.updateTodo(
                          snapshot.data![index],
                          'updated',
                        );
                      },
                    ),
                  );
                },
              ),
            ),
            Row(
              mainAxisAlignment: MainAxisAlignment.spaceEvenly,
              children: [
                Expanded(
                  child: Padding(
                    padding: const EdgeInsets.all(8),
                    child: ElevatedButton(
                      child: const Text('Add'),
                      onPressed: () async {
                        await database.addTodo(
                          'test test test',
                        );
                      },
                    ),
                  ),
                ),
                Expanded(
                  child: Padding(
                    padding: const EdgeInsets.all(8),
                    child: ElevatedButton(
                      child: const Text('remove'),
                      onPressed: () async {
                        final list = await database.allTodoEntries;
                        if (list.isNotEmpty) {
                          await database.deleteTodo(list[list.length - 1]);
                        }
                      },
                    ),
                  ),
                ),
              ],
            ),
          ],
        ),
      ),
    );
  }
}
```

- `drift_sample/drift_sample/lib/src/drift/todos.dart`

```dart
import 'dart:io';

import 'package:drift/drift.dart';
import 'package:drift/native.dart';
import 'package:path/path.dart' as p;
import 'package:path_provider/path_provider.dart';

part 'todos.g.dart';

class Todos extends Table {
  IntColumn get id => integer().autoIncrement()();
  TextColumn get content => text()();
}

@DriftDatabase(tables: [Todos])
class MyDatabase extends _$MyDatabase {
  MyDatabase() : super(_openConnection());

  @override
  int get schemaVersion => 1;

  Stream<List<Todo>> watchEntries() {
    return (select(todos)).watch();
  }

  Future<List<Todo>> get allTodoEntries => select(todos).get();

  Future<int> addTodo(String content) {
    return into(todos).insert(TodosCompanion(content: Value(content)));
  }

  Future<int> updateTodo(Todo todo, String content) {
    return (update(todos)..where((tbl) => tbl.id.equals(todo.id))).write(
      TodosCompanion(
        content: Value(content),
      ),
    );
  }

  Future<void> deleteTodo(Todo todo) {
    return (delete(todos)..where((tbl) => tbl.id.equals(todo.id))).go();
  }
}

LazyDatabase _openConnection() {
  return LazyDatabase(() async {
    final dbFolder = await getApplicationDocumentsDirectory();
    final file = File(p.join(dbFolder.path, 'db.sqlite'));
    return NativeDatabase(file);
  });
}
```
