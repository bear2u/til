# Truffle를 활용한 Pet-Shop 튜터리얼 분석

Truffle 튜터리얼 중 펫샵을 활용해서 프론트 까지 적용해서 어떤식으로 웹에서 트렉잭션이 이루어 지는 지 공부하는 곳이 있다.

* [http://truffleframework.com/tutorials/pet-shop](http://truffleframework.com/tutorials/pet-shop)

여기서 사용되는 소스를 하나씩 분석하고자 한다.

이더리움 플랫폼 위 `DAPP`개발 프레임워크 중 하나인 `truffle`사용시 개발시 순서는 다음과 같다.

* 개발환경을 설정한다. 
* truffle box\( 보일러 플레이트처럼 만들어준다.\)를 이용해서 pet-shop 을 해제
* 스마트 계약 소스를 \(.sol\) 작성한다. 
* 컴파일 및 배포를 한다. 
* [Ganeche](http://truffleframework.com/ganache) 를 통해 테스팅을 한다. 
* 웹 브라우저에서 스마트 계약이 정상적으로 인터랙션이 제대로 이루어지는지 확인해본다. 

아래 이미지는 결과 화면이다. Adopt 버튼을 통해서 success 이 제대로 이루어 지는 지 보면 된다.

![](/assets/truffle2_1.png)

> MetaMask 를 통해서 창이 열리면서 Submit를 클릭해준다.

![](/assets/truffle2-2.png)

최종적으로 채택이 된 것을 웹에서 확인해볼 수 있다. \( 새로고침을 해야한다. \)

![](/assets/truffle2-3.png)

결과물은 따라하다보면 쉽게 볼 수 있을것이다. 여기서 특이한 점은

> `MetaMask`를 통한 `Custom RPC` 로 `ganache private network` 로 접속해서 진행

![](/assets/truffle2-4.png)

> 스마트 계약을 테스팅시 솔리디티 언어로 테스팅 하는 방법





