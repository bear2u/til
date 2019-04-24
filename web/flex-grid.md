# Flex

## container

```
display: flex
```

- 줄어들면 밑으로 떨어지기

  ```
  flex-wrap: wrap
  ```

- 내용을 정렬

  ```
  justify-content: center
  //
  justify-content: space-between
  //
  justify-content: space-arroun
  ```

- cross-axis 기준 정렬

- ```
  align-items: center
  ```

- 세로로 내용정렬

  ```
  flex-wrap: wrap
  align-content: start
  // strech 안되고 줄어든다.
  ```

- 내용을 꽉채울때

- ```
  // 여백을 각각 나눈다.
  flex-grow : 1
  // 전체를 비율로 가진다
  flex-basis: 0 (or auto)
  
  flex: 1 으로 세팅시 basis는 0으로 기본 세팅됨
  
  ```

- 내용 정렬

- ```
  align-self: flex-end
  //순서 정렬
  order:1
  ```

- 

## Grid

```
// 컨테이너
display: grid

grid-template-columns: 4fr 6fr
grid-template-columns: 200px 1fr

grid-gap: 1rem

//높이를 일정하게 정렬
grid-auto-rows: 200px
//일정하게 유지는 하되 최소한 높이를 보장
grid-auto-rows: minmax(200px, auto)

//아이템 정렬
//가로 정렬
justify-items: center

//세로 정렬
align-items: center


```

```
// Items

justify-self: center
align-self: center

//아이템들을 합치기
grid-column: 1/4 (1부터 4까지 합치기)

//위치 조정
grid-column: 3;
grid-row: 2/4

grid-column: 3
grid-row: 3/5

```

