# IPFS 설치

[https://dist.ipfs.io/\#go-ipfs](https://dist.ipfs.io/#go-ipfs)에서 IPFS 구현체를 내려받을 수 있다. 행성간 파일 시스템의 약자이며 모든 컴퓨터 장치를 동일한 파일 시스템으로 연결할려고 하는 P2P 분산 파일 시스템이다. 

또한 파일을 저장하고 공유하는 영구적이고 분산된 방법을 뜻한다. 

IPFS에 대한 자세한 내용은 [ipfs.io](https://ipfs.io/),[ipfs 원리](https://github.com/ipfs/ipfs#how-%20ipfs-works) 및 [ipfs 정의\(위키\)](https://en.wikipedia.org/wiki/InterPlanetary_File_System)를 클릭하세요.

설치 

> 터미날1

```
tar xzvf go-ipfs_v0.4.10_linux-386.tar.gz (your file name might be slightly different)
cd go-ipfs
./ipfs init
./ipfs config --json API.HTTPHeaders.Access-Control-Allow-Origin '["*"]'
./ipfs daemon
```

> 터미날2

```
cd go-ipfs
./ipfs cat /ipfs/QmYwAPJzv5CZsnA625s3Xf2nemtYgPpHdWEz79ojWnPbdG/readme
```

IPFS를 활용할 수 있는 멋진 UI 프론트엔드도 있다.

[http://localhost:5001/webui](http://localhost:5001/webui)  에서 확인가능하다. 

