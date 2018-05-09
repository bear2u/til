# truffle 관련 문제 모음

```
Cannot read property 'apply' of undefined
```

관련 링크 : [https://github.com/trufflesuite/truffle-contract/issues/57](https://github.com/trufflesuite/truffle-contract/issues/57)

```
if (typeof MyContract.currentProvider.sendAsync !== "function") {
    MyContract.currentProvider.sendAsync = function() {
        return MyContract.currentProvider.send.apply(
            MyContract.currentProvider, arguments
        );
    };
}
```

---

네트워크에 배포 안 된 문제일 경우

```
//.../migrations/2_deploy_contracts.js 확인
var ProjectFactory = artifacts.require('ProjectFactory');

module.exports = function (deployer) {
  deployer.deploy(ProjectFactory);
};

//truffle migrate --reset
```



