# 비트코인에서 public key 와 private key 개념 정리

블록체인상에서의 모든 거래를 블록체인에 포함되기 위해서는 유효한 서명이 있어야 하고 이러한 서명은 유효한 디지털 키가 필요합니다. 

디지털 키는 `개인키(private key)`, `공개키(public key)` 쌍으로 구성되어 집니다. 

공개키는 은행 계좌번호를 말하며
개인키는 그 계좌번호를 열수있는 비밀번호를 뜻한다. 

지갑주소는 공개키를 사용해서 생성이 되며 순서는 다음과 같다. 
1. private 키 사용 타원곡선 곱셈함수를 통해 public 키를 생성
2. public 키를 Hash 함수 사용
3. bitcoin 지갑 주소 획득

각각의 변환은 단방향으로 각각 역방향으로의 디코딩이 불가능합니다. 

## 1. 개인키 ( Private Key )

Private 키는 무작위로 추출한 단순한 숫자로 구성되어 있습니다. 
private 키로 사용되는 256비트의 난수는 프로그래밍 언어가 제공하는 단순한 난수 생성기를 사용하지 말고 암호학적으로 안전한 의사난수생성기 사용 추천
절대 중복되지 않아야 하며 키를 생성하는 작업은 1에서 2의 256승 사이의 숫자를 선택하는 것과 동일함