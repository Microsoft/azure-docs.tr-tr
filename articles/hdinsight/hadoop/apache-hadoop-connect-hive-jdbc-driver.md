---
title: JDBC sürücüsü aracılığıyla sorgu Apache Hive-Azure HDInsight
description: HDInsight 'ta Hadoop 'a Apache Hive sorguları göndermek için bir Java uygulamasındaki JDBC sürücüsünü kullanın. Program aracılığıyla ve SQUIRREL SQL istemcisinden bağlanın.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 2250e41bffc26bd9ae59dfc652a06d08016d227a
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053802"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>HDInsight 'ta JDBC sürücüsü aracılığıyla Apache Hive sorgulama

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Azure HDInsight 'ta Apache Hadoop Apache Hive sorguları göndermek için bir Java uygulamasından JDBC sürücüsünü nasıl kullanacağınızı öğrenin. Bu belgedeki bilgiler, programlama yoluyla ve SQUIRREL SQL istemcisinden nasıl bağlanabileceğinizi gösterir.

Hive JDBC arabirimi hakkında daha fazla bilgi için bkz. [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Önkoşullar

* Hadoop kümesi An HDInsight. Bir tane oluşturmak için bkz. [Azure HDInsight kullanmaya başlama](apache-hadoop-linux-tutorial-get-started.md).
* [Java geliştirici seti (JDK) sürüm 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) veya üstü.
* [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/). SQUIRREL bir JDBC istemci uygulamasıdır.

## <a name="jdbc-connection-string"></a>JDBC bağlantı dizesi

Azure 'da bir HDInsight kümesine JDBC bağlantıları 443 numaralı bağlantı noktası üzerinden yapılır ve trafik SSL kullanılarak korunmuş olur. Kümelerin arkasındaki genel ağ geçidi, trafiği HiveServer2 'in gerçekten dinlediği bağlantı noktasına yönlendirir. Aşağıdaki bağlantı dizesi HDInsight için kullanılacak biçimi gösterir:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

`CLUSTERNAME` değerini HDInsight kümenizin adıyla değiştirin.

## <a name="authentication"></a>Kimlik Doğrulaması

Bağlantıyı kurarken, küme ağ geçidinde kimlik doğrulaması yapmak için HDInsight kümesi yönetici adını ve parolasını kullanmanız gerekir. SQUIRREL SQL gibi JDBC istemcilerinden bağlanırken, istemci ayarlarında yönetici adı ve parolasını girmeniz gerekir.

Bir Java uygulamasından bağlantı kurarken adı ve parolayı kullanmanız gerekir. Örneğin, aşağıdaki Java kodu bağlantı dizesi, yönetici adı ve parolayı kullanarak yeni bir bağlantı açar:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>SQUIRREL SQL istemcisiyle bağlantı

SQUIRREL SQL, HDInsight kümeniz ile Hive sorgularını uzaktan çalıştırmak için kullanılabilen bir JDBC istemcidir. Aşağıdaki adımlarda, SQUIRREL SQL 'i zaten yüklemiş olduğunuz varsayılır.

1. Kümeinizden kopyalanacak belirli dosyaları içerecek bir dizin oluşturun.

2. Aşağıdaki betikte, `sshuser`, küme için SSH kullanıcı hesabı adıyla değiştirin.  `CLUSTERNAME`, HDInsight küme adıyla değiştirin.  Bir komut satırından, çalışma dizininizi önceki adımda oluşturulan bir şekilde değiştirin ve ardından bir HDInsight kümesinden dosya kopyalamak için aşağıdaki komutu girin:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. SQUIRREL SQL uygulamasını başlatın. Pencerenin sol tarafında, **sürücüler**' i seçin.

    ![Pencerenin sol tarafındaki sürücüler sekmesi](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. **Sürücüler** iletişim kutusunun üst kısmındaki simgeler ' de bir sürücü oluşturmak için **+** simgesini seçin.

    ![SQUIRREL SQL uygulama sürücüleri simgesi](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. Sürücü Ekle iletişim kutusunda aşağıdaki bilgileri ekleyin:

    |Özellik | Değer |
    |---|---|
    |Adı|Hive|
    |Örnek URL|JDBC: hive2:/localhost: 443/varsayılan; transportMode = HTTP; SSL = true; httpPath =/hive2|
    |Ek sınıf yolu|Daha önce indirilen tüm jar dosyalarını eklemek için **Ekle** düğmesini kullanın.|
    |Sınıf adı|org. Apache. Hive. JDBC. HiveDriver|

   ![parametrelerle sürücü iletişim kutusu Ekle](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Bu ayarları kaydetmek için **Tamam ' ı** seçin.

6. SQuirreL SQL penceresinin sol tarafında **diğer adlar**' ı seçin. Sonra bağlantı diğer adı oluşturmak için **+** simgesini seçin.

    ![SQUIRREL SQL yeni diğer ad Ekle iletişim kutusu](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. **Diğer ad Ekle** iletişim kutusu için aşağıdaki değerleri kullanın:

    |Özellik |Değer |
    |---|---|
    |Adı|HDInsight üzerinde Hive|
    |Sürücü|**Hive** sürücüsünü seçmek için açılan eklentiyi kullanın.|
    |URL|JDBC: hive2://CLUSTERNAME.azurehdinsight.net: 443/default; transportMode = HTTP; SSL = true; httpPath =/hive2. **CLUSTERNAME** değerini HDInsight kümenizin adıyla değiştirin.|
    |User Name|HDInsight kümeniz için küme oturum açma hesabı adı. **Yönetici**varsayılandır.|
    |Parola|Küme oturum açma hesabının parolası.|

    ![parametrelerle diğer ad iletişim kutusu Ekle](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Bağlantının çalıştığını doğrulamak için **Test** düğmesini kullanın. **Bağlanılacak: HDInsight üzerinde Hive** iletişim kutusu görüntülenirse, testi gerçekleştirmek için **Bağlan** ' ı seçin. Sınama başarılı olursa **başarılı bir bağlantı** iletişim kutusu görürsünüz. Bir hata oluşursa bkz. [sorun giderme](#troubleshooting).

    Bağlantı diğer adını kaydetmek için **diğer ad Ekle** iletişim kutusunun altındaki **Tamam** düğmesini kullanın.

8. SQuirreL SQL 'in en üstündeki açılan menüde bulunan **bağlantı** noktasından, **HDInsight 'ta Hive**' yi seçin. İstendiğinde, **Bağlan**' ı seçin.

    ![parametrelerle bağlantı iletişim kutusu](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Bağlandıktan sonra, SQL sorgu iletişim kutusuna aşağıdaki sorguyu girin ve sonra **Çalıştır** simgesini (çalışan bir kişi) seçin. Sonuçlar alanında sorgunun sonuçları gösterilmelidir.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![sonuçlar dahil SQL sorgu iletişim kutusu](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Örnek Java uygulamasından bağlanma

HDInsight 'ta Hive sorgulamak için Java istemcisi kullanmanın bir örneği [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)adresinden edinilebilir. Örneği derlemek ve çalıştırmak için depodaki yönergeleri izleyin.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>SQL bağlantısı açılmaya çalışılırken beklenmeyen bir hata oluştu

**Belirtiler**: 3,3 veya üzeri bir HDInsight kümesine bağlanırken beklenmeyen bir hata oluştuğunu belirten bir hata alabilirsiniz. Bu hata için yığın izlemesi aşağıdaki satırlarla başlar:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Neden**: Bu hata, SQUIRREL 'e dahil edilen eski bir Commons-codec. jar dosyası nedeniyle oluşur.

**Çözüm**: Bu hatayı çözmek için aşağıdaki adımları kullanın:

1. SQUIRREL programından çıkın ve sonra da `C:\Program Files\squirrel-sql-4.0.0\lib`, bu durumda SQUIRREL 'in sisteminizde yüklü olduğu dizine gidin. SQUIRREL dizininde, `lib` dizini altında, var olan Commons-codec. jar dosyasını HDInsight kümesinden indirilen ile değiştirin.

1. SQUIRREL 'i yeniden başlatın. HDInsight 'ta Hive 'e bağlanılırken hata artık gerçekleşmemelidir.

## <a name="next-steps"></a>Sonraki adımlar

Artık, Hive ile çalışmak için JDBC 'ı nasıl kullanacağınızı öğrendiğinize göre, Azure HDInsight ile çalışmanın diğer yollarını araştırmak için aşağıdaki bağlantıları kullanın.

* [Apache Hive verilerini Azure HDInsight 'Ta Microsoft Power BI Ile görselleştirin](apache-hadoop-connect-hive-power-bi.md).
* [Azure HDInsight 'ta Power BI etkileşimli sorgu Hive verilerini görselleştirin](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Azure HDInsight 'ta Apache Hive sorguları çalıştırmak Için Apache Zeppelin kullanın](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Excel 'i Microsoft Hive ODBC sürücüsü Ile HDInsight 'A bağlayın](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Power Query kullanarak Excel 'i Apache Hadoop bağlama](apache-hadoop-connect-excel-power-query.md).
* [Azure HDInsight 'A bağlanın ve Visual Studio Data Lake araçları 'nı kullanarak Apache Hive sorguları çalıştırın](apache-hadoop-visual-studio-tools-get-started.md).
* [Visual Studio Code Için Azure HDInsight aracını kullanın](../hdinsight-for-vscode.md).
* [HDInsight 'a veri yükleme](../hdinsight-upload-data.md)
* [HDInsight ile Apache Hive kullanma](hdinsight-use-hive.md)
* [HDInsight ile Apache Pig kullanma](hdinsight-use-pig.md)
* [HDInsight ile MapReduce işleri kullanma](hdinsight-use-mapreduce.md)
