# 기본 함수

> 기본 출력

```
void main() {
    var test = "test";
    print('test = $test');
}
```

> 기본값 설정

```
void printMsg(String msg, [String value = 'undefined']) {
  print('msg = $msg, value = $value');
}
void main() {
  printMsg('1234', '5678');
  printMsg('1234');
}

........................
msg = 1234, value = 5678
msg = 1234, value = undefined
```



