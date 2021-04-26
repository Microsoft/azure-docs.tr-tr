---
title: Apache Hive Azure HDInsight ile Java Kullanıcı tanımlı işlev (UDF)
description: Apache Hive ile birlikte çalışarak Java tabanlı kullanıcı tanımlı bir işlev (UDF) oluşturmayı öğrenin. Bu örnek UDF bir metin dizesi tablosunu küçük harfe dönüştürür.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 11/20/2019
ms.openlocfilehash: bf9c2ea544c6b510a0507b6b020f0eae0b101dc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946558"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>HDInsight 'ta Apache Hive Java UDF kullanma

Apache Hive ile birlikte çalışarak Java tabanlı kullanıcı tanımlı bir işlev (UDF) oluşturmayı öğrenin. Bu örnekteki Java UDF, metin dizelerinin bir tablosunu tümüyle küçük harfli karakterlere dönüştürür.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Hadoop kümesi. Bkz. [Linux 'Ta HDInsight kullanmaya başlama](./apache-hadoop-linux-tutorial-get-started.md).
* [Java geliştirici seti (JDK) sürüm 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* Apache [Maven](https://maven.apache.org/download.cgi) , Apache 'e göre düzgün şekilde [yüklendi](https://maven.apache.org/install.html) .  Maven, Java projeleri için bir proje derleme sistemidir.
* Kümelerinizin birincil depolama alanı için [URI şeması](../hdinsight-hadoop-linux-information.md#URI-and-scheme) . Bu, Azure Storage için wasb://, Azure Data Lake Storage 2. için abfs://veya adl://için Azure Data Lake Storage 1. olacaktır. Azure depolama için güvenli aktarım etkinse URI olur `wasbs://` .  Ayrıca bkz. [Güvenli aktarım](../../storage/common/storage-require-secure-transfer.md).

* Bir metin Düzenleyicisi veya Java IDE

    > [!IMPORTANT]  
    > Python dosyalarını bir Windows istemcisinde oluşturursanız, bir satır sonunda LF kullanan bir düzenleyici kullanmanız gerekir. Düzenleyicinizde LF veya CRLF kullanıp kullanmadığını bilmiyorsanız, CR karakterini kaldırma adımları için [sorun giderme](#troubleshooting) bölümüne bakın.

## <a name="test-environment"></a>Test ortamı

Bu makale için kullanılan ortam, Windows 10 çalıştıran bir bilgisayardır.  Komutlar bir komut isteminde yürütülürler ve çeşitli dosyalar Notepad ile düzenlendi. Ortamınız için uygun şekilde değiştirin.

Bir komut isteminden, çalışan bir ortam oluşturmak için aşağıdaki komutları girin:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Örnek bir Java UDF oluşturma

1. Aşağıdaki komutu girerek yeni bir Maven projesi oluşturun:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Bu komut `exampleudf` , Maven projesini içeren adlı bir dizin oluşturur.

2. Proje oluşturulduktan sonra, `exampleudf/src/test` aşağıdaki komutu girerek projenin bir parçası olarak oluşturulan dizini silin:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. `pom.xml`Aşağıdaki komutu girerek açın:

    ```cmd
    notepad pom.xml
    ```

    Ardından varolan `<dependencies>` girişi AŞAĞıDAKI XML ile değiştirin:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Bu girişler, HDInsight 3,6 ' de bulunan Hadoop ve Hive sürümünü belirtir. HDInsight [Bileşen sürümü oluşturma](../hdinsight-component-versioning.md) belgesinden HDInsight Ile sunulan Hadoop ve Hive sürümleri hakkında bilgi edinebilirsiniz.

    `<build>`Dosyanın sonundaki satırdan önce bir bölüm ekleyin `</project>` . Bu bölüm aşağıdaki XML 'i içermelidir:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```

    Bu girişler projenin nasıl oluşturulacağını tanımlar. Özellikle, projenin kullandığı Java sürümü ve kümeye dağıtım için bir uberjar oluşturma.

    Değişiklikler yapıldıktan sonra dosyayı kaydedin.

4. Yeni bir dosya oluşturmak ve açmak için aşağıdaki komutu girin `ExampleUDF.java` :

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Ardından aşağıdaki Java kodunu kopyalayıp yeni dosyaya yapıştırın. Sonra dosyayı kapatın.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Bu kod bir dize değeri kabul eden bir UDF uygular ve dizenin küçük harfli bir sürümünü döndürür.

## <a name="build-and-install-the-udf"></a>UDF derleme ve yüklemesi

Aşağıdaki komutlarda, `sshuser` farklı ise gerçek kullanıcı adıyla değiştirin. `mycluster`Gerçek küme adıyla değiştirin.

1. Aşağıdaki komutu girerek UDF 'yi derleyin ve paketleyin:

    ```cmd
    mvn compile package
    ```

    Bu komut, UDF 'yi dosya içinde oluşturur ve paketler `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` .

2. `scp`Aşağıdaki komutu girerek dosyayı HDInsight kümesine kopyalamak için komutunu kullanın:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Aşağıdaki komutu girerek SSH kullanarak kümeye bağlanın:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Açık SSH oturumunda, JAR dosyasını HDInsight depolama alanına kopyalayın.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Hive 'den UDF kullanma

1. Aşağıdaki komutu girerek, SSH oturumundan Beeline istemcisini başlatın:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Bu komut, kümeniz için oturum açma hesabı için **yönetici** varsayılanını kullandığınızı varsayar.

2. `jdbc:hive2://localhost:10001/>`Komut istemine ulaştıktan sonra, UDF 'Yi Hive 'ye eklemek ve işlev olarak göstermek için aşağıdakileri girin.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Bir tablodan alınan değerleri küçük harf dizelerine dönüştürmek için UDF 'yi kullanın.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Bu sorgu, tablodan durumu seçer, dizeyi küçük harfe dönüştürür ve ardından bunları değiştirilmemiş adla birlikte görüntüler. Çıktı aşağıdaki metne benzer şekilde görünür:

    ```output
    +---------------+---------------+--+
    |  exampleudf   |     state     |
    +---------------+---------------+--+
    | california    | California    |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | utah          | Utah          |
    | utah          | Utah          |
    | colorado      | Colorado      |
    +---------------+---------------+--+
    ```

## <a name="troubleshooting"></a>Sorun giderme

Hive işini çalıştırırken aşağıdaki metne benzer bir hata ile karşılaşabilirsiniz:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Bu sorun, Python dosyasındaki satır sonları nedeniyle oluşabilir. Birçok Windows Düzenleyicisi, satır sonu olarak CRLF 'yi varsayılan olarak kullanmaktır, ancak Linux uygulamaları genellikle LF bekler.

Dosyayı HDInsight 'a yüklemeden önce CR karakterlerini kaldırmak için aşağıdaki PowerShell deyimlerini kullanabilirsiniz:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Sonraki adımlar

Hive ile çalışmanın diğer yolları için bkz. [HDInsight ile Apache Hive kullanma](hdinsight-use-hive.md).

Hive User-Defined Işlevleri hakkında daha fazla bilgi için apache.org adresindeki Hive wiki 'nin [Apache Hive işleçler ve User-Defined işlevleri](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) bölümüne bakın.