# Build example mysql docker image of debezium to support Apple M1 chip

自行編譯 [debezium tutorial](https://debezium.io/documentation/reference/1.8/tutorial.html) 裡的 mysql example docker image, 以便能在 Apple M1 macbook 運行

## 問題

在 M1 macbook 執行 debezium tutorial 提供的指令:
```shell
$ docker run -it --rm --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=debezium -e MYSQL_USER=mysqluser -e MYSQL_PASSWORD=mysqlpw debezium/example-mysql:1.8
```

會遇到以下錯誤:
```
[ERROR] [MY-012585] [InnoDB] Linux Native AIO interface is not supported on this platform. Please check your OS documentation and install appropriate binary of InnoDB.
```

## 說明

- Apple M1 屬於 arm64v8 架構
- Debezium examples 所提供的 mysql docker image 是源自 docker offical image(https://hub.docker.com/_/mysql), 但此 mysql image 並不支援 Apple M1.

## 解決

- 在 [docker offical images 說明](https://github.com/docker-library/official-images#architectures-other-than-amd64)可以看出, 要改用其他的 docker offical image: https://hub.docker.com/r/arm64v8/mysql/

## 步驟

- Clone debezium docker-images
```
$ git clone https://github.com/debezium/docker-images.git
```

- 修改 docker-images/examples/mysql/1.8/Dockerfile
原來的 Dockerfile:
```
FROM mysql:8.0
```

修改的內容
```
FROM FROM arm64v8/mysql:8-oracle
```

- Build image
```
[1.8]$ docker build --tag debezium-example-mysql:1.8 .
```

- 執行指令
```shell
$ docker run -it --rm --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=debezium -e MYSQL_USER=mysqluser -e MYSQL_PASSWORD=mysqlpw debezium-example-mysql:1.8
```
