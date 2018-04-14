# Go lang install

```
Visit https://golang.org/dl/ and make note of the latest stable release (v1.8 or later).

To install Go language, run the following commands in your terminal/command line:

$ sudo apt update


$ sudo curl -O https://storage.googleapis.com/golang/go1.9.2.linux-amd64.tar.gz 

Note: Switch out the black portion of the URL with the correct filename.

$ sudo tar -xvf go1.9.2.linux-amd64.tar.gz

$ sudo mv go /usr/local

$ echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.profile

$ source ~/.profile

Check that the Go version is v1.8 or later:

$ go version
```

Hyperledger iroha 개발자 소스

```
https://github.com/hyperledger/iroha
https://github.com/hyperledger/iroha-ios
https://github.com/hyperledger/iroha-android
https://github.com/hyperledger/iroha-javascript
https://github.com/hyperledger/iroha-python
https://github.com/hyperledger/iroha-scala
https://github.com/hyperledger/iroha-dotnet
https://github.com/hyperledger/iroha-api
```

Linux Foundation ID를 사용하여 Rocket.Chat \(Slack의 대안\)에서 실시간 대화에 참여할 수 있습니다.

* * [https://chat.hyperledger.org/channel/iroha](https://chat.hyperledger.org/channel/iroha)
  * [https://chat.hyperledger.org/channel/iroha-smartcontracts](https://chat.hyperledger.org/channel/iroha-smartcontracts)
    .

또 다른 옵션은[https://lists.hyperledger.org/mailman/listinfo/hyperledger-iroha](https://lists.hyperledger.org/mailman/listinfo/hyperledger-iroha)와 같은 기술 토론 및 공지 사항에 대한 메일 링리스트에 참여하는 것입니다.

API 문서

```
https://hyperledger.github.io/iroha-api/#overview
```

# Hyperledger sawtooth 설치

docker compose 내용

```
# Copyright 2017 Intel Corporation
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# ------------------------------------------------------------------------------

version: "2.1"

services:

  settings-tp:
    image: hyperledger/sawtooth-tp_settings:0.8
    container_name: sawtooth-settings-tp-default
    expose:
      - 4004
    depends_on:
      - validator
    entrypoint: settings-tp -vv tcp://validator:4004

  intkey-tp-python:
    image: hyperledger/sawtooth-tp_intkey_python:0.8
    container_name: sawtooth-intkey-tp-python-default
    expose:
      - 4004
    depends_on:
      - validator
    entrypoint: intkey-tp-python -vv tcp://validator:4004

  xo-tp-python:
    image: hyperledger/sawtooth-tp_xo_python:0.8
    container_name: sawtooth-xo-tp-python-default
    expose:
      - 4004
    depends_on:
      - validator
    entrypoint: xo-tp-python -vv tcp://validator:4004

  validator:
    image: hyperledger/sawtooth-validator:0.8
    container_name: sawtooth-validator-default
    expose:
      - 4004
    ports:
      - "4004:4004"
    # start the validator with an empty genesis batch
    entrypoint: "bash -c \"\
        sawtooth admin keygen && \
        sawtooth keygen my_key && \
        sawtooth config genesis -k /root/.sawtooth/keys/my_key.priv && \
        sawtooth admin genesis config-genesis.batch && \
        sawtooth-validator -vv \
          --endpoint tcp://validator:8800 \
          --bind component:tcp://eth0:4004 \
          --bind network:tcp://eth0:8800 \
        \""

  rest-api:
    image: hyperledger/sawtooth-rest_api:0.8
    container_name: sawtooth-rest-api-default
    expose:
      - 4004
      - 8080
    ports:
      - "8080:8080"
    depends_on:
      - validator
    entrypoint: sawtooth-rest-api --connect tcp://validator:4004 --bind rest-api:8080

  client:
    image: hyperledger/sawtooth-all:0.8
    container_name: sawtooth-client-default
    expose:
      - 8080
      - 4004
    depends_on:
      - rest-api
    entrypoint: "bash -c \"\
        sawtooth keygen && \
        tail -f /dev/null \
        \""
```

```
docker-compose up -d
```

The client container is used to run Sawtooth CLI commands, which is the usual way to interact with validators or validator networks at this time.

Open a new terminal window and navigate to the same directory mentioned in this section.

Log into the client container by running the following command:

**`$ docker exec -it sawtooth-client-default bash`**

In your terminal, you will see something similar to the following:

**`root@75b380886502:/#`**

Your environment is now set up and you are ready to start experimenting with the network. But first, let’s confirm that our validator is up and running, and reachable from the client container. To do this, run the following command:

**`$ curl http://rest-api:8080/blocks`**

After running this command, you should see a**json**object response with “data”, array of batches, header, etc.

And, to check the connectivity from the host computer, run the following command in a new terminal window \(it does not need to be the same directory as mentioned previously in this section\):

**`$curl http://localhost:8080/blocks`**

After running this command, you should see a**json**object response with “data”, array of batches, header, etc

