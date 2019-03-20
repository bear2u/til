# Flutter + Steho 사용하기

Flutter 로컬 디비 사용시 대중적인 방법은 [sqflite](https://github.com/tekartik/sqflite) 일것이다. 

일반 `sqlite` 와 문법이 똑같으며 사용하기에도 편하다. 

sqlite 를 모바일에서 연동시 페이스북에서 나온 `steho` 라는 라이버러리가 있다. 

안드로이드만 현재 가능하지만 꽤 유용하게 사용가능하다. 

- [stetho로 android 디버깅을 보다 쉽게](https://medium.com/@sungyong/stetho-%EB%A1%9C-android-debugging%EC%9D%84-%EB%B3%B4%EB%8B%A4-%EC%89%BD%EA%B2%8C-54bb5fa0c6ee)

그럼 flutter 에서 어떻게 적용하는지 살펴보자. 

우선 `sqflite` 를 import 로 가져오고 생성시 `getDatabasePath()` 로 가져올 수 있다. 

```
  Future<Database> init() async {
    String documentsDirectory = await getDatabasesPath(); //주의하자.
    final path = join(documentsDirectory, "test.db");
    final db = await openDatabase(
      path,
      version: _version,
      onCreate: _onCreate,
      onUpgrade: (Database db, int oldVersion, int newVersion) async {
        if(newVersion > oldVersion) {
          await File(path).delete();
          _onCreate(db, newVersion);
          print('onUpgrade done');
        }
      }
    );
    return db;
  }
```

그리고 `stetho` 라이버러리를 설치를 하자. 

- [flutter_stetho](https://pub.dartlang.org/packages/flutter_stetho)

마지막으로 `main.dart` 에서 적용하면 된다. 

```
  if(isInDebugMode) {
    Stetho.initialize();
  }
```

중요한 점은 디버깅 모드일 경우에만 실행을 해야 한다. 

그래서 `isInDebugMode` 변수를 지정했다. 

```
bool get isInDebugMode {
  bool inDebugMode = false;
  assert(inDebugMode = true);
  return inDebugMode;
}
```

크롬에서 `chrome://inspect/#devices` 주소를 열어서 왼쪽 `Devices` 항목을 클릭시 `inspect` 항목이 나오는데 그걸 클릭시 새 창이 뜨면서 확인이 가능하다. 

Android 만 가능한걸로 안다. 그 점을 유의하자. 



이상으로 `Flutter` + `Sqflite` + `Stetho` 적용법을 알아보았다. 

