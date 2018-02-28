인라인 람다에 한해서 리턴시 밖의 함수까지 리턴이 된다. 

```kotlin
data class Person2(val name: String, val age: Int)

val people = listOf(Person2("Alice",29), Person2("Bob",31))

fun lookForAlice(people: List<Person2>) {
    people.forEach {
        if (it.name == "Alice") {
            println("Found!")
            return
        }
    }
    
    println("Alice not found") //현재 실행 안됨
}

lookForAlice(people)
========================
Found!!

```

위와 같이 함수자체를 리턴하는 경우 non-local 이라고 한다. 

그럼 라벨을 붙이는 경우엔 내부적으로 리턴을 할 수 있다. 

```kotlin
fun lookForAlice(people: List<Person2>) {
    people.forEach label@{
        if (it.name == "Alice") {
            println("Found!")
            return@label
        }

        println("Alice not found")
    }
}

=====================================
Found!
Alice not found //<-- 실행됨
```

인라인 함수명을 적어도 된다. 

```kotlin
fun lookForAlice(people: List<Person2>) {
    people.forEach{
        if (it.name == "Alice") {
            println("Found!")
            return@forEach
        }

        println("Alice not found")
    }
}

=====================================
Found!
Alice not found //<-- 실행됨
```



