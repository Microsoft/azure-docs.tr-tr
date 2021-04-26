---
title: Apache Hadoop için Java MapReduce oluşturma-Azure HDInsight
description: Apache Maven kullanarak Java tabanlı MapReduce uygulaması oluşturma ve bunu Azure HDInsight 'ta Hadoop ile çalıştırma hakkında bilgi edinin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 01/16/2020
ms.openlocfilehash: 7c1a6623883cbee46ba98982808f3c392dc50ffa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946657"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>HDInsight üzerinde Apache Hadoop için Java MapReduce programları geliştirme

Apache Maven kullanarak Java tabanlı MapReduce uygulaması oluşturma ve bunu Azure HDInsight üzerinde Apache Hadoop ile çalıştırma hakkında bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

* [Java geliştirici seti (JDK) sürüm 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* Apache [Maven](https://maven.apache.org/download.cgi) , Apache 'e göre düzgün şekilde [yüklendi](https://maven.apache.org/install.html) .  Maven, Java projeleri için bir proje derleme sistemidir.

## <a name="configure-development-environment"></a>Geliştirme ortamını yapılandırma

Bu makale için kullanılan ortam, Windows 10 çalıştıran bir bilgisayardır. Komutlar bir komut isteminde yürütülürler ve çeşitli dosyalar Notepad ile düzenlendi. Ortamınız için uygun şekilde değiştirin.

Bir komut isteminden, çalışan bir ortam oluşturmak için aşağıdaki komutları girin:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven projesi oluşturma

1. **Wordcountjava** adlı bir Maven projesi oluşturmak için aşağıdaki komutu girin:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Bu komut, parametresi tarafından belirtilen ada sahip bir dizin oluşturur `artifactID` (Bu örnekte **wordcountjava** .) Bu dizin aşağıdaki öğeleri içerir:

    * `pom.xml` -Projeyi oluşturmak için kullanılan bilgileri ve yapılandırma ayrıntılarını içeren [proje nesne modeli (pod)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) .
    * src\main\java\org\apache\hadoop\examples: uygulama kodunuzu Içerir.
    * src\test\java\org\apache\hadoop\examples: uygulamanız için testler Içerir.

1. Oluşturulan örnek kodu kaldırın. Oluşturulan test ve uygulama dosyalarını silin `AppTest.java` ve `App.java` aşağıdaki komutları girerek:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Proje nesne modelini Güncelleştir

pom.xml dosyasının tam bir başvurusu için bkz https://maven.apache.org/pom.html .. `pom.xml`Aşağıdaki komutu girerek açın:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Bağımlılık Ekle

İçinde `pom.xml` , bölümüne aşağıdaki metni ekleyin `<dependencies>` :

```xml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-examples</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
```

Bu, gerekli kitaplıkları (ArtifactId içinde &lt; listelenmiştir \> ) belirli bir sürümle (sürüm içinde listelenmiştir &lt; \> ) tanımlar. Derleme zamanında, bu bağımlılıklar varsayılan Maven deposundan indirilir. [Maven depo aramasını](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) kullanarak daha fazla bilgi görüntüleyebilirsiniz.

`<scope>provided</scope>`Maven, HDInsight kümesi tarafından çalışma zamanında sağlandığı için bu bağımlılıkların uygulamayla paketlenmemelidir.

> [!IMPORTANT]
> Kullanılan sürüm, kümenizde bulunan Hadoop sürümüyle eşleşmelidir. Sürümler hakkında daha fazla bilgi için bkz. [HDInsight bileşen sürümü oluşturma](../hdinsight-component-versioning.md) belgesi.

### <a name="build-configuration"></a>Yapı yapılandırması

Maven eklentileri projenin derleme aşamalarını özelleştirmenizi sağlar. Bu bölüm eklenti, kaynak ve diğer derleme yapılandırma seçeneklerini eklemek için kullanılır.

Aşağıdaki kodu `pom.xml` dosyasına ekleyin ve dosyayı kaydedin ve kapatın. Bu metin dosyadaki etiketlerin içinde olmalıdır `<project>...</project>` , örneğin, `</dependencies>` ve `</project>` .

```xml
<build>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
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
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.6.1</version>
        <configuration>
        <source>1.8</source>
        <target>1.8</target>
        </configuration>
    </plugin>
    </plugins>
</build>
```

Bu bölüm Apache Maven derleyicisi eklentisini ve Apache Maven gölge eklentisini yapılandırır. Derleyici eklentisi, topolojiyi derlemek için kullanılır. Gölge eklentisi, Maven tarafından oluşturulan JAR paketindeki lisans çoğaltmasını engellemek için kullanılır. Bu eklenti, HDInsight kümesinde çalışma zamanında bir "yinelenen lisans dosyaları" hatası oluşmasını engellemek için kullanılır. Uygulama ile Maven-gölge-eklentisi kullanılması `ApacheLicenseResourceTransformer` hatayı önler.

Maven-gölge-eklentisi, uygulamanın gerektirdiği tüm bağımlılıkları içeren bir Uber jar de oluşturur.

`pom.xml` dosyasını kaydedin.

## <a name="create-the-mapreduce-application"></a>MapReduce uygulamasını oluşturma

1. Yeni bir dosya oluşturmak ve açmak için aşağıdaki komutu girin `WordCount.java` . Yeni bir dosya oluşturmak için istemde **Evet** ' i seçin.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Ardından aşağıdaki Java kodunu kopyalayıp yeni dosyaya yapıştırın. Sonra dosyayı kapatın.

    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```

    Paket adı `org.apache.hadoop.examples` ve sınıf adı olduğuna dikkat edin `WordCount` . MapReduce işini gönderdiğinizde bu adları kullanırsınız.

## <a name="build-and-package-the-application"></a>Uygulamayı derleyin ve paketleyin

`wordcountjava`Dizininden, uygulamayı içeren BIR jar dosyası oluşturmak için aşağıdaki komutu kullanın:

```cmd
mvn clean package
```

Bu komut önceki tüm derleme yapılarını temizler, henüz yüklenmemiş olan bağımlılıkları indirir ve ardından uygulamayı derler ve paketleyebilir.

Komut tamamlandığında, `wordcountjava/target` Dizin adlı bir dosya içerir `wordcountjava-1.0-SNAPSHOT.jar` .

> [!NOTE]
> `wordcountjava-1.0-SNAPSHOT.jar`Dosya, yalnızca WordCount işi değil, aynı zamanda işin çalışma zamanında gerektirdiği bağımlılıkları içeren bir uberjar dosyasıdır.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>JAR 'yi karşıya yükleme ve işleri çalıştırma (SSH)

Aşağıdaki adımlar, `scp` jar 'Yi HDInsight kümesindeki Apache HBase 'in birincil baş düğümüne kopyalamak için kullanılır. Bu `ssh` komut daha sonra kümeye bağlanmak ve örneği doğrudan baş düğümde çalıştırmak için kullanılır.

1. Jar 'yi kümeye yükleyin. `CLUSTERNAME`Öğesini HDInsight kümenizin adıyla değiştirin ve aşağıdaki komutu girin:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Kümeye bağlanın. `CLUSTERNAME`Öğesini HDInsight kümenizin adıyla değiştirin ve aşağıdaki komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH oturumunda, MapReduce uygulamasını çalıştırmak için aşağıdaki komutu kullanın:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Bu komut WordCount MapReduce uygulamasını başlatır. Giriş dosyası `/example/data/gutenberg/davinci.txt` ve çıkış dizini `/example/data/wordcountout` . Hem giriş dosyası hem de çıkış, küme için varsayılan depolama alanına depolanır.

1. İş tamamlandıktan sonra, sonuçları görüntülemek için aşağıdaki komutu kullanın:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Aşağıdaki metne benzer değerler içeren sözcüklerin ve sayımların bir listesini almalısınız:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, Java MapReduce işi geliştirmeyi öğrendiniz. HDInsight ile çalışmanın diğer yolları için aşağıdaki belgelere bakın.

* [HDInsight ile Apache Hive kullanma](hdinsight-use-hive.md)
* [HDInsight ile MapReduce kullanma](hdinsight-use-mapreduce.md)
* [Java Geliştirici Merkezi](https://azure.microsoft.com/develop/java/)