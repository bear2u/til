# 이진탐색 \(Binary Search\)

시나리오

> 1~1000개 숫자 의 배열에서 특정 숫자만 뽑아내고 싶을 경우 어떻게 해야 될까?

1. 리스트로 한개씩 넣어서 검색해서 운좋으면 빠른 시간안에 검색이 될 수 있으나 최악의 경우 1000번의 수행을 해야 한다. 
2. 이진탐색 알고리즘을 통해서 전체의 반을 검색하고 다시 반을 검색하는 방법을 사용할 수 있다. 

예를 들어 100의 중간 50부터 시작되는 함수로 시작된다

4번만에 답을 찾은 경우이다. 그럼 log~2~ 16 = 4 이라고 표시한다.

즉 로그는 거듭제곱의 반대말이다.

> 지수표현이 잘 안됨..

* 10^2^ = 100 -&gt; log~10~100 = 2
* 10^3 = 100 -&gt; log~10~1000 = 3
* 2^3 = 8 -&gt; log~2~8 = 3 \(결과값을 앞으로 보내면 된다.\)
* 2^4 = 16 -&gt; log~2~16 = 4
* 2^5 = 32 -&gt; log~2~32 = 5

위의 경우에는 정렬된 배열에 한해 해당되는 내용이었다. 하지만 실무에서는 보통 정렬이 안되어있는 경우가 많은 편이다.

그럼 어떻게 구현이 될까? 파이썬으로 시작해보자.

우선 먼저 배열 전체 길이를 설정하자.

```
low = 0
high = len(list) - 1
```

그리고 가운데를 딱 나누어서 원소를 확인해보자.

```
mid = (low + high) / 2 //반으로 나눈값 설정
guess = list(mid) //리스트의 반이 무엇인지 예측
```

추측한 값이 너무 작으면 low 값을 다음과 같이 변경 처리

```
if guess < item:
  low = mid + 1
```

또 큰 경우 high 값을 설정

```
if guess > item
  hight = mid -1
```

표로 정리해보면 다음과 같다.

| idx | low | high | mid | guess | result | result |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 0 | 0 | 99 | 49 \(50 - 1\) | 50 | 50 &lt; 57 | 크다 |
| 1 | 50 | 99 | 74 \(75 - 1\) | 75 | 75 &gt; 57 | 작다 |
| 2 | 50 | 73 | 61 \(62 -1\) | 62 | 62 &gt; 57 | 작다 |
| 3 | 50 | 60 | 55 \(56-1\) | 56 | 56 &lt; 57 | 크다 |
| 4 | 56 | 60 | 58 | 59 | 59 &gt;57 | 작다 |
| 5 | 56 | 57 | 56 | 57 | 57 == 57 | 정답 |

```py
def binary_search(list, item):
    cnt = 0
    low = 0
    high = len(list) - 1
    print("찾는 문자 : ", item)

    while low <= high:
        mid = (low + high) // 2
        guess = list[mid]
        print("번호:",cnt, " low:", low, " high:", high, " mid:", mid, " guess:", guess )
        if guess == item:
            print("in ", guess, "==", item, " return ", mid)
            return mid
        if guess > item: #item보다 guess 가 큰경ㅇ 
            print("in ", guess, ">", item, " 작다")
            high = mid - 1
        else:
            print("in ", guess, "<", item, " 크다")
            low = mid + 1
        cnt=cnt+1    


    return None #아이템이 리스트에 없음


my_list = list(range(1,101))
print(binary_search(my_list, 57))
```

결과값

```
찾는 문자 :  57
번호: 0  low: 0  high: 99  mid: 49  guess: 50
in  50 < 57  크다
번호: 1  low: 50  high: 99  mid: 74  guess: 75
in  75 > 57  작다
번호: 2  low: 50  high: 73  mid: 61  guess: 62
in  62 > 57  작다
번호: 3  low: 50  high: 60  mid: 55  guess: 56
in  56 < 57  크다
번호: 4  low: 56  high: 60  mid: 58  guess: 59
in  59 > 57  작다
번호: 5  low: 56  high: 57  mid: 56  guess: 57
in  57 == 57  return  56
56
```

다트 버전

[온라인 에디터 바로가기](https://dartpad.dartlang.org/)

```dart
void main() {
  //1~100까지 생성  
  int targetNum = 57;
  var list = new List<int>.generate(100, (i) => i+1);
  print("final result =  ${binary_search(list, targetNum)}");
}

int binary_search(List<int> list, int item) {
  //초기화  
  int low = 0;
  //리스트 갯수 - 1
  int high = list.length - 1;

  //while low <= high
  while(low <= high) {  
    int mid = ((low + high) / 2).floor();    
    int guess = list[mid];
    print("$low,$high,$mid,$guess");
    if(guess == item) {
      return mid;
    }else if(guess > item) {
      high = mid -1;      
    }else{
      low = mid + 1;
    }          
  }

  return 0;

}
```

결과

```bash
0,99,49,50
50,99,74,75
50,73,61,62
50,60,55,56
56,60,58,59
56,57,56,57
final result =  56
```



