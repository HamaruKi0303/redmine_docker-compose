# Redmine Docker-compose

![](https://i.imgur.com/dZ9jvob.png)


# Index

- [Introduction](#introduction)
- [Updates!!](#updates)
- [Coming soon](#coming-soon)
- [environment](#environment)
- [Quick Start](#quick-start)
  - [Google Drive のインストール](#google-drive-のインストール)
  - [初期化されたフォルダのコピー](#初期化されたフォルダのコピー)
  - [再度 Docker の起動](#再度-docker-の起動)
- [Detail](#detail)
  - [システム構成](#システム構成)
  - [フォルダ構成](#フォルダ構成)
- [Reference site](#reference-site)

## Introduction


`Redmine`を`Docker-compose`を使って秒速で構築できます．



## Updates!!
* 【2022/10/14】 [Redmineを秒速導入`Docker`ファイル作成](https://hamaruki.com/2022/10/14/docker_001/)
* 【2021/10/15】 [テーマ変更方法の追加](https://hamaruki.com/2022/10/15/docker_003/)
* 【2021/10/16】 [スマホからアクセスする環境の構築](https://hamaruki.com/2022/10/16/docker_004/)
* 【2021/12/10】 [バックアップ環境の構築]()

## Coming soon
- [ ] 未定

## environment

- Windows11
- Google Drive

## Quick Start

### Google Drive のインストール

[Google drive](https://www.google.com/intl/ja_jp/drive/) をインストールしてフォルダにアクセスできる環境を構築してください．

![](https://i.imgur.com/lbj4w8w.png)

### 初期化されたフォルダのコピー

GoogleDriveをコンテナにマウントするコマンドを一度コメントアウトします．

```docker
version: '3.8'
services:
  mariadb:
    image: docker.io/bitnami/mariadb:10.3
    environment:
      - ALLOW_EMPTY_PASSWORD=yes
      - MARIADB_USER=bn_redmine
      - MARIADB_DATABASE=bitnami_redmine
    volumes:
      # コンテナからデータを引っ張る用のフォルダ
      - 'D:\GoogleDrive\Prj\Redmine\data:/share'
      # データ本体 GoogleDriveでバックアップ
    #   - 'D:\GoogleDrive\Prj\Redmine\data\mariadb:/bitnami/mariadb'
      
  redmine:
    image: docker.io/bitnami/redmine:4.2.5
    environment:
      - REDMINE_DB_USERNAME=bn_redmine
      - REDMINE_DB_NAME=bitnami_redmine
      - ALLOW_EMPTY_PASSWORD=yes
    ports:
      - '8888:3000'
    volumes:
      # コンテナからデータを引っ張る用のフォルダ
      - 'D:\GoogleDrive\Prj\Redmine\data:/share'
      # データ本体 GoogleDriveでバックアップ
    #   - 'D:\GoogleDrive\Prj\Redmine\data\themes:/opt/bitnami/redmine/public/themes'
    #   - 'D:\GoogleDrive\Prj\Redmine\data\db:/opt/bitnami/redmine/public/db'
    #   - 'D:\GoogleDrive\Prj\Redmine\data\files:/opt/bitnami/redmine/public/files'
    #   - 'D:\GoogleDrive\Prj\Redmine\data\plugins:/opt/bitnami/redmine/public/plugins'

    depends_on:
      - mariadb
    tty:
      true

```

下記のコマンドで Docker を起動します．

```bash
docker-compose up -d
```

`redmine`コンテナの中に入ります．

```bash
docker-compose exec redmine /bin/bash
```

マウントするフォルダをコピーしてきます．

```bash
root@14a791e43546:/opt/bitnami/redmine# cp -R db/ /share
root@14a791e43546:/opt/bitnami/redmine# cp -R files/ /share/
root@14a791e43546:/opt/bitnami/redmine# cp -R plugins/ /share/
root@14a791e43546:/opt/bitnami/redmine# cp -R themes/ /share/
```

コピーしたらいったんコンテナから出ます．次に，`mariadb`コンテナの中に入ります．

```bash
docker-compose exec mariadb /bin/bash
```

マウントするフォルダをコピーしてきます．

```bash
cp -R mariadb/ /share/
```

これで完了です．


これをやる理由としてはマウント先のフォルダが上書きされてしまうからです．そのため，現状では空のGoogleDriveのフォルダをマウントしていまうと，Dockerのビルド時に初期化されたフォルダが空になってしまいエラーが多発します．そこで，最初はGoogleDriveのフォルダをマウントせずに起動してから，`/share`フォルダにマウントするフォルダをコピーしてビルド時に初期化されたフォルダをGoogleDriveに持ってきます．


### 再度 Docker の起動

下記のようにコメントアウトして箇所を戻します．



```docker
version: '3.8'
services:
  mariadb:
    image: docker.io/bitnami/mariadb:10.3
    environment:
      - ALLOW_EMPTY_PASSWORD=yes
      - MARIADB_USER=bn_redmine
      - MARIADB_DATABASE=bitnami_redmine
    volumes:
      # コンテナからデータを引っ張る用のフォルダ
      - 'D:\GoogleDrive\Prj\Redmine\data:/share'
      # データ本体 GoogleDriveでバックアップ
      - 'D:\GoogleDrive\Prj\Redmine\data\mariadb:/bitnami/mariadb'
      
  redmine:
    image: docker.io/bitnami/redmine:4.2.5
    environment:
      - REDMINE_DB_USERNAME=bn_redmine
      - REDMINE_DB_NAME=bitnami_redmine
      - ALLOW_EMPTY_PASSWORD=yes
    ports:
      - '8888:3000'
    volumes:
      # コンテナからデータを引っ張る用のフォルダ
      - 'D:\GoogleDrive\Prj\Redmine\data:/share'
      # データ本体 GoogleDriveでバックアップ
      - 'D:\GoogleDrive\Prj\Redmine\data\themes:/opt/bitnami/redmine/public/themes'
      - 'D:\GoogleDrive\Prj\Redmine\data\db:/opt/bitnami/redmine/public/db'
      - 'D:\GoogleDrive\Prj\Redmine\data\files:/opt/bitnami/redmine/public/files'
      - 'D:\GoogleDrive\Prj\Redmine\data\plugins:/opt/bitnami/redmine/public/plugins'

    depends_on:
      - mariadb
    tty:
      true

```


下記のコマンドで Docker を起動します．

```bash
docker-compose up -d
```

無事にアクセスすることができました．

![](https://i.imgur.com/dZ9jvob.png)



## Detail

### システム構成

下記のような構成です．Windows11上のDockerでRedmineとmariadbを構築します．そのコンテナ内のデータをGoogle Driveのフォルダをマウントすることでバックアップ環境を構築します．

これによりデータはローカルとGoogleのサーバーに保存されたことになり万が一ローカルのPCが破損しても問題なく復旧できる環境を構築しました．

![](https://i.imgur.com/jjG2L3R.png)

### フォルダ構成

リポジトリはこのような構成になっており`docker-compose.yml`が本体です．

```bash
D:.
│  docker-compose.yml   ← Dockerファイル本体
│  LICENSE
│  README.md
│
└─docs
```

oogle Driveのフォルダはこのような構成になっています．

```bash
D:.
├─GoogleDrive           ← Google Driveのフォルダ
│  └─Prj
│  　 └─Redmine
│  　     └─data
│  　         ├─db      ← バックアップ兼データ保存フォルダ
│  　         ├─files   ← バックアップ兼データ保存フォルダ
│  　         ├─mariadb ← バックアップ兼データ保存フォルダ
│  　         ├─plugins ← バックアップ兼データ保存フォルダ
│  　         ├─share   ← コンテナからデータを引っ張る用のフォルダ
│  　         └─themes  ← バックアップ兼データ保存フォルダ
```




## Reference site

- [はまる木 redmine 関係のサイト](https://hamaruki.com/?s=redmine)
- [Dockerを使ってEC2にredmineを構築する(前編)](https://www.geekfeed.co.jp/geekblog/docker-redmine)
- [Docker Compose で Redmine 公式イメージを起動する](https://blog1.mammb.com/entry/2020/05/24/090000)
- [Docker ComposeでRedmineを動かす](https://www.orzs.tech/docker-compose-redmine/)