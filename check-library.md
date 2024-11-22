これはJava21でいける？
com.squareup.okhttp3:mockwebserver:3.6.0
  ビルドで要確認
  
org.tomlj:tomlj:1.0.0
  ビルドで要確認

org.apache.commons:commons-compress:1.21

net.java.dev.jna:jna:5.7.0
	Java 21では互換性の問題が発生する可能性があります。
	特に、Java 9以降のモジュールシステムに対応していないため、
	JNAの最新バージョン（5.9.0以降）にアップグレードしたほうが良い。

com.google.code.findbugs:findbugs:3.0.1
	findbugs:3.0.1は比較的古いバージョンで、2016年にリリースされました。
	FindBugsは、Java 8やそれ以前のバージョンに対応していますが、Java 9以降、特にJava 11以降での互換性に関しては公式にサポートされていない部分があります。

org.mockito:mockito-junit-jupiter:3.7.0

org.dbunit:dbunit:2.7.3
	DbUnit 2.7.3は古いライブラリで、最後に更新されたのが2016年頃です。
	Java 8以降の互換性については公式で明示されておらず、Java 21のような最新バージョンのサポートについても保証されていません。


org.apache.poi:poi5.2.3

org.projectlombok:lombok:1.18.28

org.atomikos:transactions-spring-boot-starter:5.0.9



org.springframework.boot:3.2.0
com.github.spotbugs version5.0.14