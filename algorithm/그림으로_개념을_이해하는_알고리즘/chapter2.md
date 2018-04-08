# 배열과 연결된 리스트의 장단점

2장에서는 주된 설명은 배열과 연결된 리스트에 대한 비교 설명이 있었다.

`배열`은 검색이 빠른 반면 삽입이 느리다는 것

`연결된 리스트`의 경우 삽입이 빠르지만 검색부분에선 배열보다 느리다는 장단점이 있다.

# 선택정렬 \(Selection Sort\)

2번째 알고리즘 문제이다.

만약 주어진 배열이 있으면 그 배열을 정렬하고자 할 때 선택정렬을 사용할 수 있다.

기본 흐름은 다음과 같다. \(순차적 정렬시\)

1. 배열에 가장 작은 값 검색
2. 가장 작은 값 위치 값 획득
3. 그 위치 값을 배열에서 제거 하고 새로운 배열에 하나씩 추가함
4. 그렇게 해서 최종 새로운 배열로 결과값을 보여줌

Python3 으로 구현시 다음과 같다.

```py
def findSmallest(arr):
    smallest = arr[0]
    smallest_index = 0
    for i in range(1, len(arr)):
        print(">> {0},{1},{2}".format(smallest, smallest_index, arr[i]))
        if arr[i] < smallest:            
            smallest = arr[i]
            smallest_index = i
    return smallest_index


def selectionSort(arr):
    newArr = []
    for i in range(len(arr)):
        print("arr => ", arr)
        smallest = findSmallest(arr)
        print("smallest => ", smallest)
        newArr.append(arr.pop(smallest)) # pop은 꺼낸 후 제거된다. 
    return newArr

print(selectionSort([5, 3, 6, 2, 10]))
```

| try no | idx | smallest | smallest\_index | arr\[index\] |
| :--- | :--- | :--- | :--- | :--- |
| 0 | 0 | 5 | 0 | 3 |
| 0 | 1 | 3 | 1 | 6 |
| 0 | 2 | 3 | 1 | 2 |
| 0 | 3 | 2 \( 제일 작은값 리턴\) | 3 | 10 |

> 2를 arr 에서 pop \(2를 꺼내는 동시에 제거\) 하고 다시 위의 반복문을 돌린다.

위 결과값 출력은 다음과 같다.

```
arr =>  [5, 3, 6, 2, 10]
>> 5,0,3
>> 3,1,6
>> 3,1,2
>> 2,3,10
smallest =>  3
arr =>  [5, 3, 6, 10]
>> 5,0,3
>> 3,1,6
>> 3,1,10
smallest =>  1
arr =>  [5, 6, 10]
>> 5,0,6
>> 5,0,10
smallest =>  0
arr =>  [6, 10]
>> 6,0,10
smallest =>  0
arr =>  [10]
smallest =>  0


[2, 3, 5, 6, 10] <== 최종 정렬된 값
```

빅오 표기법으로는 O\(n^2\) 이라고 한다.

![](/assets/algorithm_ch2_1.png)

다트 버전의 경우는 [온라인 다트 편집기](https://dartpad.dartlang.org/) 에서 테스팅 가능하다. 

```dart
void main() {
  print("result => ${selectionSort([5, 3, 6, 2, 10])}");
}

List<int> selectionSort(List<int> arr) {
  var newArr = [];
  var length = arr.length;
  for(var i = 0; i < length; i++) {    
  	var smallestIndex = findSmallestIndex(arr);    
    newArr.add(arr[smallestIndex]);
    arr.removeAt(smallestIndex);
  }
  return newArr;
}

int findSmallestIndex(List<int> arr) {
  int smallest = arr[0];
  int smallestIdx = 0;
  
  var newArr = arr.sublist(1,arr.length);
  
  newArr.forEach((item) {    
    if(item < smallest) {
      smallest = item;
      smallestIdx = arr.indexOf(item);      
    }
  });  
  return smallestIdx;
}
```



