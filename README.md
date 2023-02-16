## はじめに
この記事は、Spring Boot (Gradle)をDockerコンテナとして稼働させることを目的としています。

## 必要なもの
* 5分
* Docker Desktopをインストール済み
* Gradle コマンドを実行できる

## 事前準備
このリポジトリをクローンします。

    git clone https://github.com/Ryunosuke1998/docker-spring-boot.git




### プロジェクトのビルド、Dockerイメージの作成
下記コマンドを実行する。

    ./gradlew build
    ./gradlew build docker

これで、Dockerイメージが作成されます。Docker Desktopでイメージを確認します。"▶"ボタンでコンテナを起動できます。
起動時の設定をするダイアログが表示されるので、[host port]に8080を設定しましょう。

[http://localhost:8080/]にアクセスします。
"Hello World!!"を表示されることを確認します。


## 補足
### Dockerイメージの作成方法
### ルートディレクトリにDockerfileを作成し、下記内容を記載する。
    FROM openjdk:17-alpine
    VOLUME /tmp
    ARG JAR_FILE
    COPY ${JAR_FILE} app.jar
    EXPOSE 8080:8080
    ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]

※FROM openjdk:17-alpine
はプロジェクトのJavaバージョンに合わせる

### build.gradle
※「// 追加」の部分がDockerまわりで必要な記述です。

    plugins {
        id 'java'
        id 'org.springframework.boot' version '3.0.2'
        id 'io.spring.dependency-management' version '1.1.0'
        // 追加
        id "com.palantir.docker" version "0.22.1"
    }

    group = 'com.example'
    version = '0.0.1-SNAPSHOT'
    sourceCompatibility = '17'

    repositories {
        mavenCentral()
    }

    dependencies {
        implementation 'org.springframework.boot:spring-boot-starter-web'
        testImplementation 'org.springframework.boot:spring-boot-starter-test'
    }

    //tasks.named('test') {
    //    useJUnitPlatform()
    //}

    //追加
    docker {
        name "${project.group}/${bootJar.baseName}"

        files bootJar.archivePath
        buildArgs(['JAR_FILE': "${bootJar.archiveName}"])
    }

以上です。