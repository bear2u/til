자원관리시 using을 사용가능하다. 

```kotlin
BufferedReader(StringReader(path)).use{br ->
        return br.readLine()
      }
```



