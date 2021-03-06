# 탈중앙 이베이 구현

프로젝트 개요

**상품 전시**

1. : 장터는 판매자가 상품을 전시할 수 있게 해야 합니다. 우리는 무료로 상품을 전시할 수있는 기능을 구축할 것입니다. 쿼리를 쉽게 하기 위해 블록체인과 데이터베이스의 오프 체인에 항목을 저장합니다.
2. **IPFS에 파일 추가**
   : 제품 이미지 및 제품 설명\(많은 분량의 텍스트\)을 IPFS에 업로드하는 기능을 추가할 것입니다.
3. **상품 조회**
   : 카테고리, 옥션 시간 등을 기준으로 상품을 조회하고 필터링하는 기능을 구현할 것입니다.
4. **옥션**
   : 이베이와 유사한 비커리 옥션\(**Vickery Auction\)**
    방식으로 상품가를 입찰하는 시스템을 만들 것입니다. 이더리움 상의 모든 정보는 중앙화된 애플리케이션과는 달리 누구나 볼 수 있기 때문에, 이 프로젝트에서는 조금 다른 방향으로 구현에 접근할 것입니다. [ENS](https://ens.domains/) 입찰의 원리에 가까운 방식을 차용할 예정입니다.
5. **에스크로 계약**
   : 입찰이 끝나고 상품의 구매자가 확정되면, 구매자와 판매자 및 서드파티 감시자 사이를 이어주는 에스크로 계약을 생성할 것입니다.
6. **2-of-3 멀티시그**
   : 부정 행위를 차단하기 위해 2-of-3 멀티시그 솔루션을 구현하여, 참여자 3명 중 2명의 투표를 통한 동의가 있어야 구입금을 판매자에게 전달하거나 구매자에게 환불할 수 있게 만들 것입니다.

개발 아키텍쳐 내용

**웹 프론트엔드**

1. : 웹 프론트엔드는 HTML, CSS, 자바스크립트\(web3js의 비중이 큼\)의 조합으로 구성됩니다. 사용자는 이 프론트엔드 애플리케이션을 통해 블록체인, IPFS, nodeJS 서버와 상호작용하게 됩니다.
2. **블록체인**
   : 대부분의 코드와 트랜잭션이 담기는 애플리케이션의 핵심입니다. 장터의 모든 상품과 사용자 입찰 정보, 에스크로가 블록체인 위에 올려집니다.
3. **몽고DB**
   : 블록체인에 모든 상품을 올린다 해도, 상품을 디스플레이하고 다양한 필터를 적용할 때마다\(특정 카테고리 상품만 보거나, 곧 마감되는 상품을 조회하는 등\) 블록체인에 쿼리를 날리는 것은 비효율적입니다. 그래스 블록체인 대신 몽고DB를 사용해서 상품 정보를 저장하고 쿼리하는 데에 사용할 것입니다.
4. **NodeJS 서버**  
   : 프론트엔드가 데이터베이스와 통신하기 위한 백엔드 서버입니다. 간단한 API를 통해 프론트엔드에서 데이터베이스의 상품 정보를 쿼리하고 받을 수 있게 구현할 것입니다.

5. **IPFS**  
   : 사용자가 장터에 상품을 올리면, 프론트엔드는 상품 파일과 설명을 IPFS에 업로드한 후 업로드된 파일의 해시를 블록체인에 저장합니다.

![](/assets/zastrin3-1.png)

1. 웹 프론트엔드는 사용자가 상품의 상세 정보\(이름, 시작가, 이미지, 설명 등\)를 입력하고 저장하기 위한 HTML 폼을 포함합니다. \(1\)

2. 웹 프론트엔드는 제품 이미지 및 제품 설명을 IPFS에 업로드하고, 업로드된 데이터의 링크를 다시 받습니다. IPFS 링크의 예는   
   [이곳](https://ipfs.io/ipfs/QmaWR99orw8oE5N64SK7iF1pKQtP33xFdULgmZgq2qQAGZ)  
   에서 확인할 수 있습니다. \(2\), \(3\)

3. 다음으로 웹 프론트엔드는 제품 정보와 IPFS 링크를 블록체인에 저장하는 스마트 계약을 호출합니다. 성공적으로 블록체인에 제품을 저장하고 나면 계약에 의해 이벤트가 발동됩니다. 이 이벤트에는 모든 제품 정보가 들어있습니다. \(4\), \(5\)
4. NodeJS 서버는 이러한 이벤트를 수신하며, 스마트 계약에 의해 이벤트가 발생하면 서버는 이벤트의 내용을 읽고 몽고DB에 제품을 삽입합니다. \(6\), \(7\), \(8\)

# 구현단계

1. 우선, 트러플 프레임워크를 사용하여 솔리디티 스마트 계약을 구현하고, 이를 ganache 에 배포하고 트러플 콘솔로 배포된 스마트 계약과 상호작용합니다.
2. 다음으로 IPFS에 대해 배우고, 커맨드 라인으로 이를 설치하고 제어 하는 방법을 익힐 것입니다.
3. 백엔드 구현이 끝나면, 스마트 계약 및 IPFS와 상호작용하기 위한 프론트엔드를 구현합니다. 프론트엔드에는 입찰 기능 및 옥션 공개 기능도 구현합니다.
4. 몽고DB 를 설치하고, 상품을 담기 위한 데이터 구조를 설계할 것입니다. 
5. 데이터베이스가 구동되면, 스마트 계약 이벤트를 모니터링하고 요청을 콘솔에 로깅하는 NodeJS 서버 측 코드를 구현합니다. 그리고 데이터베이스에 상품을 삽입하기 위한 코드도 구현합니다. 
6. 프론트엔드를 업데이트하여, 블록체인 대신 데이터베이스에서 상품을 조회할 수 있도록 합니다. 
7. 에스크로 스마트 계약을 구현하고, 그에 해당하는 프론트엔드를 구현하여 구매자나 판매자에게 에스크로에서 환불 또는 지불이 가능하도록 할 것입니다. 



