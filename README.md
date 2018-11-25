# Concourse による継続的デリバリ
Pivotal Cloud Foundry への継続的デリバリを **Concourse** を用いて行います。

## 概要 / 説明


## 前提 / 環境
- [事前作業](https://github.com/shinyay/pcf-workshop-prerequisite/blob/master/README.md)

## 手順 / 解説
### Concourse 環境の準備 (Docker)
#### Docker のインストール
- MacOS

```
$ brew cask install docker
```

- Linux

```
yum install docker -y
```

#### Concourse コンテナの準備

```
$ git clone https://github.com/concourse/concourse-docker.git
$ cd concourse-docker
$ ./generate-keys.sh
```

- `CONCOURSE_EXTERNAL_URL` をローカルにアサインされたIPアドレスでアクセスするように設定

```
$ export CONCOURSE_EXTERNAL_URL=http://$(ipconfig getifaddr en0):8080
```

docker-compose.yml を編集し、環境変数の `CONCOURSE_EXTERNAL_URL` を使用するように編集します。

```
$ vi docker-compose.yml
```

以下のように修正します。

```
- CONCOURSE_EXTERNAL_URL=${CONCOURSE_EXTERNAL_URL}
```

#### Concourse コンテナの起動
以下のコマンドで Concourse コンテナを起動します。

```
$ docker-compose up -d
```

認証はデフォルトで設定されている test を使用します。

- User: test
- Pass: test

Concourse には、http://<CONCOURSE_EXTERNAL_URL>:8080 でアクセスできます。

### Concourse CLI (fly)のインストール
アクセスした Councourse の画面から、インストールイメージをダウンロードして導入できます。

![concourse](images/concourse.png)


また、cURL を使用してインストールイメージをダウンロードして導入する事もできます。

```
$ curl -LO https://github.com/concourse/concourse/releases/download/v4.2.1/fly_darwin_amd64
$ chmod +x fly_darwin_amd64
$ mv fly_darwin_amd64 /usr/local/bin/fly
```

```
$ fly --version

4.2.1
```

### アップグレード前のアプリケーションのデプロイ

```
$ git clone https://github.com/shinyay/pcf-workshop-upgrade-code.git
$ git checkout before-upgrade
```

```
$ cf push
```

```
$ curl http://<アプリケーションURL> 

MMMMMMMMMMMMMMMMMWMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMFMMMMMMM
M]                 ?WMMMMMM#MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMF    .M
M]    ........       .HMMMM#MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMF!`  .MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMF    .M
M]    .MMMMMMMMNa.     MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM[    .MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMF    .M
M]    .MMMMMMMMMMN     (MMMMHHHHHMMMMHHHHHHMMMMMMMMMMMMMMHHHHHMMMMMMMMMMMMHMMMMMMMMMMMMMM[    .HHHHHHHHMMMMMMMMMMYMMMMMMMHMMMMMMMMMF    .M
M]    .MMMMMMMMMMM{    .MMM#MMMMMMMMF      HMMMMMMMMMMMMF    .MMMMM#M`         .TMMMMMMMM[            .MMMMMM@M               ,MMMMF    .M
M]    .MMMMMMMMMMM`    .MMM#MMMMMMMMh..    .MMMMMMMMMMMM`   .MMMMB!      ...      ,HMMMMM[    .........MMMM@`     ........    .MMMMF    .M
M]    .MMMMMMMMMM^     JMMM#MMMMMMMMMMM]    4MMMMMMMMMM]    dMMMF    ..MMMMMMNJ     UMMMM[    .MMMMMMMMMMMF     .MMMMMMMMF    .MMMMF    .M
M]    .MMMMMMM=`      .MMMM#MMMMMMMMMMMM,    MMMMMMMMM#    .MMMF    .MMMMMMMMMMN.    MMMM[    .MMMMMMMMMMM`    MMMMMMMMMMF    .MMMMF    .M
M]    .MMM          .MMMMMM#MMMMMMMMMMMMN    (MMMMMMMM^   .MMMM%    dMMMMMMMMMMMb    (MMM[    .MMMMMMMMMM#    .MMMMMMMMMMF    .MMMMF    .M
M]    .MMM.  ....(MMMMMMMMM#MMMMMMMMMMMMM]    MMMMMMMF    JMMMM)    MMMMMMMMMMMM#    .MMM[    .MMMMMMMMMMF    (MMMMMMMMMMF    .MMMMF    .M
M]    .MMMMMMMMMMMMMMMMMMMM#MMMMMMMMMMMMMM,   .MMMMMM`   .MMMMM)    MMMMMMMMMMMM#    .MMM[    .MMMMMMMMMMF    (MMMMMMMMMMF    .MMMMF    .M
M]    .MMMMMMMMMMMMMMMMMMMM#MMMMMMMMMMMMMMb    dMMMMt   .MMMMMM)    MMMMMMMMMMMMF    -MMM[    .MMMMMMMMMM#    .MMMMMMMMMMF    .MMMMF    .M
M]    .MMMMMMMMMMMMMMMMMMMM#MMMMMMMMMMMMMMM[   .MMM#    (MMMMMMb    -MMMMMMMMMMM^    dMMM[    .MMMMMMMMMMM,    UMMMMMMMMMF    .MMMMF    .M
M]    .MMMMMMMMMMMMMMMMMMMM#MMMMMMMMMMMMMMMN.   (MM'   .MMMMMMMM,    -MMMMMMMM#'    .MMMM]    .MMMMMMMMMMMN.    ?HMMMMMMMF    .MMMMF    .M
M]    .MMMMMMMMMMMMMMMMMMMM#MMMMMMMMMMMMMMMMb    7^    MMMMMMMMMMx      ?MMM^      .MMMMMb       `````(MMMMN,         JMMF    .MMMMF    .M
M]    .MMMMMMMMMMMMMMMMMMMM#MMMMMMMMMMMMMMMMMb       .dMMMMMMMMMMMNJ.           ..MMMMMMMMN.          .MMMMMMN,.      JMMF    .MMMMF    .M
MNMMMMNMMMMMMMMMMMMMMMMMMMMNMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMNMMMMMMMMMMMMMMMMMMMMMMMNMMMMMMMNMMMMMMMMMMMNNMMMMMNMMMMMMMMMNMMMMNM

Current app version = 1
Current java version = 25.192-b12
App index = 0
App host = 10.10.148.171
```

### Concourse パイプラインの作成
#### Concourse へのログイン

```
$ fly -t hello-ci login -c <CONCOURSE_EXTERNAL_URL>

ogging in to team 'main'

navigate to the following URL in your browser:

  http://192.168.100.118:8080/sky/login?redirect_uri=http://127.0.0.1:62332/auth/callback

or enter token manually:
target saved
```

#### パイプラインの登録

```
$ fly -t hello-ci set-pipeline -p simple-pipeline -c pipeline.yml
```

```
resources:
  resource pcfapp has been added:
+ name: pcfapp
+ type: git
+ source:
+   branch: master
+   uri: https://github.com/shinyay/pcf-workshop-upgrade-code
+ check_every: 10s

jobs:
  job unit-test has been added:
+ name: unit-test
+ plan:
+ - get: pcfapp
+   trigger: true
+ - task: gradle-test
+   config:
+     platform: linux
+     image_resource:
+       type: docker-image
+       source:
+         repository: gradle
+     run:
+       path: bash
+       args:
+       - -c
+       - |-
+         set -e
+         cd pcfapp
+         gradle test
+     inputs:
+     - name: pcfapp

apply configuration? [yN]: y
pipeline created!
you can view your pipeline here: http://192.168.100.118:8080/teams/main/pipelines/simple-pipeline

the pipeline is currently paused. to unpause, either:
  - run the unpause-pipeline command
  - click play next to the pipeline in the web ui
```

![simple pipeline](images/simple-pipeline.png)

```

$ fly -t hello-ci unpause-pipeline -p simple-pipeline

unpaused 'simple-pipeline'
```

```
$ fly -t hello-ci trigger-job -j simple-pipeline/unit-test

started simple-pipeline/unit-test #1
```

![unit test](images/unit-test.png)

![unit test execution](images/unit-test-execution.png )

![unit test succeed](images/unit-test-succeed.png)

### ビルド＆デプロイ用パイプラインの追加
#### パイプラインの作成
ビルド＆デプロイ用の処理が追加されたパイプラインを作成します。
以下の YAML の記述で `<YOUR_USERID>` `<YOUR_PASSWD>` `<YOUR_ORG>` をそれぞれ自分の情報に書き換えます。

- pipeline-build-and-deploy.yml

```yaml

---
resources:
- name: pcfapp
  type: git
  source:
    uri: https://github.com/shinyay/pcf-workshop-upgrade-code.git
    branch: master
  check_every: 10s
- name: deploy-to-cf
  type: cf
  source:
    api: api.run.pivotal.io
    username: <YOUR_USERID>
    password: <YOUR_PASSWD>
    organization: <YOUR_ORG>
    space: development
    skip_cert_check: true
jobs:
- name: unit-test
  plan:
  - get: pcfapp
    trigger: true
  - task: gradle-test
    config:
      platform: linux
      image_resource:
        type: docker-image
        source:
          {repository: java, tag: openjdk-8}
      inputs:
      - name: pcfapp
      run:
        path: bash
        args:
        - -c
        - |
          cd pcfapp
          ./gradlew tasks
- name: build-and-deploy
  plan:
  - get: pcfapp
    passed: [ unit-test ]
    trigger: true
  - task: build
    config:
      platform: linux
      image_resource:
        type: docker-image
        source:
          {repository: java, tag: openjdk-8}
      inputs:
      - name: pcfapp
      run:
        path: bash
        args:
        - -c
        - |
          cd pcfapp
          ./gradlew clean build -x test
  - put: deploy-to-cf
    params:
      manifest: pcfapp/manifest.yml
      current_app_name: hello-pcf-upgrade
```

#### パイプラインの更新

```
$ fly -t hello-ci set-pipeline -p simple-pipeline -c pipeline-build-and-deploy.yml
```

![build deploy pipeline](images/build-deploy-pipeline.png)

## まとめ / 振り返り

### 今回のソース
- []()
