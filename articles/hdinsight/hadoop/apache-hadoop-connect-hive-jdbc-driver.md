---
title: JDBC sürücüsü aracılığıyla sorgu Apache Hive-Azure HDInsight
description: HDInsight 'ta Hadoop 'a Apache Hive sorguları göndermek için bir Java uygulamasındaki JDBC sürücüsünü kullanın. Program aracılığıyla ve SQUIRREL SQL istemcisinden bağlanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 6073000f2f14f835e2bfbd91b41619101c36b10f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866854"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>HDInsight’ta JDBC sürücüsü üzerinden Apache Hive’ı sorgulama

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Bir Java uygulamasından JDBC sürücüsünü nasıl kullanacağınızı öğrenin. Azure HDInsight 'ta Apache Hadoop Apache Hive sorguları göndermek için. Bu belgedeki bilgiler, programlama yoluyla ve SQUIRREL SQL istemcisinden nasıl bağlanabileceğinizi gösterir.

Hive JDBC arabirimi hakkında daha fazla bilgi için bkz. [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Önkoşullar

* Hadoop kümesi An HDInsight. Bir tane oluşturmak için bkz. [Azure HDInsight kullanmaya başlama](apache-hadoop-linux-tutorial-get-started.md). Service HiveServer2 'ın çalıştığından emin olun.
* [Java geliştirici seti (JDK) sürüm 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) veya üstü.
* [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/). SQUIRREL bir JDBC istemci uygulamasıdır.

## <a name="jdbc-connection-string"></a>JDBC bağlantı dizesi

Azure 'da bir HDInsight kümesine JDBC bağlantıları 443 numaralı bağlantı noktası üzerinden yapılır. Trafik, TLS/SSL kullanılarak güvenli hale getirilir. Kümelerin arkasındaki genel ağ geçidi, trafiği HiveServer2 'in gerçekten dinlediği bağlantı noktasına yönlendirir. Aşağıdaki bağlantı dizesi HDInsight için kullanılacak biçimi gösterir:

```http
    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2
```

`CLUSTERNAME` değerini HDInsight kümenizin adıyla değiştirin.

Ya da bağlantıyı, **ambarı Kullanıcı arabirimi > Hive > configs > gelişmiş**' i aracılığıyla bulabilirsiniz.

:::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png" alt-text="Ambarı aracılığıyla JDBC bağlantı dizesi al" border="true":::

### <a name="host-name-in-connection-string"></a>Bağlantı dizesinde konak adı

Bağlantı dizesindeki ' CLUSTERNAME.azurehdinsight.net ' konak adı, küme URL 'niz ile aynı. Azure portal aracılığıyla edinebilirsiniz.

### <a name="port-in-connection-string"></a>Bağlantı dizesinde bağlantı noktası

Kümeye yalnızca Azure sanal ağı dışındaki bazı yerlerden bağlanmak için **443 numaralı bağlantı noktasını** kullanabilirsiniz. HDInsight Yönetilen bir hizmettir. Bu, kümeye yapılan tüm bağlantıların güvenli bir ağ geçidi üzerinden yönetildiği anlamına gelir. 10001 veya 10000 bağlantı noktalarında doğrudan HiveServer 2 ' ye bağlanamazsınız. Bu bağlantı noktaları dışarıdan gösterilmez.

## <a name="authentication"></a>Kimlik Doğrulaması

Bağlantıyı kurarken, kimlik doğrulamak için HDInsight kümesi yönetici adını ve parolasını kullanın. SQUIRREL SQL gibi JDBC istemcilerinden, istemci ayarlarında yönetici adı ve parola girin.

Bir Java uygulamasından bağlantı kurarken adı ve parolayı kullanmanız gerekir. Örneğin, aşağıdaki Java kodu yeni bir bağlantı açar:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>SQUIRREL SQL istemcisiyle bağlantı

SQUIRREL SQL, HDInsight kümeniz ile Hive sorgularını uzaktan çalıştırmak için kullanılabilen bir JDBC istemcidir. Aşağıdaki adımlarda, SQUIRREL SQL 'i zaten yüklemiş olduğunuz varsayılır.

1. Kümeinizden kopyalanacak belirli dosyaları içerecek bir dizin oluşturun.

2. Aşağıdaki betikte, ' ın `sshuser` küme IÇIN SSH kullanıcı hesabı adıyla değiştirin.  `CLUSTERNAME`HDInsight kümesi adıyla değiştirin.  Bir komut satırından, çalışma dizininizi önceki adımda oluşturulan bir şekilde değiştirin ve ardından bir HDInsight kümesinden dosya kopyalamak için aşağıdaki komutu girin:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. SQUIRREL SQL uygulamasını başlatın. Pencerenin sol tarafında, **sürücüler**' i seçin.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png" alt-text="Pencerenin sol tarafındaki sürücüler sekmesi" border="true":::

4. **Sürücüler** iletişim kutusunun üst kısmındaki simgelerden **+** bir sürücü oluşturmak için simgeyi seçin.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png" alt-text="SQUIRREL SQL uygulama sürücüleri simgesi" border="true":::

5. Sürücü Ekle iletişim kutusunda aşağıdaki bilgileri ekleyin:

    |Özellik | Değer |
    |---|---|
    |Ad|Hive|
    |Örnek URL|`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`|
    |Ek sınıf yolu|Daha önce indirilen tüm jar dosyalarını eklemek için **Ekle** düğmesini kullanın.|
    |Sınıf Adı|org. Apache. Hive. JDBC. HiveDriver|

   :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png" alt-text="parametrelerle sürücü iletişim kutusu Ekle" border="true":::

   Bu ayarları kaydetmek için **Tamam ' ı** seçin.

6. SQuirreL SQL penceresinin sol tarafında **diğer adlar**' ı seçin. Ardından **+** bir bağlantı diğer adı oluşturmak için simgeyi seçin.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png" alt-text="' SQUIRREL SQL yeni diğer ad Ekle iletişim kutusu '" border="true":::

7. **Diğer ad Ekle** iletişim kutusu için aşağıdaki değerleri kullanın:

    |Özellik |Değer |
    |---|---|
    |Ad|HDInsight üzerinde Hive|
    |Sürücü|**Hive** sürücüsünü seçmek için açılan eklentiyi kullanın.|
    |URL|`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`. **CLUSTERNAME** değerini HDInsight kümenizin adıyla değiştirin.|
    |User Name|HDInsight kümeniz için küme oturum açma hesabı adı. **Yönetici** varsayılandır.|
    |Parola|Küme oturum açma hesabının parolası.|

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png" alt-text="parametrelerle diğer ad iletişim kutusu Ekle" border="true":::

    > [!IMPORTANT]
    > Bağlantının çalıştığını doğrulamak için **Test** düğmesini kullanın. **Bağlanılacak: HDInsight üzerinde Hive** iletişim kutusu görüntülenirse, testi gerçekleştirmek için **Bağlan** ' ı seçin. Sınama başarılı olursa **başarılı bir bağlantı** iletişim kutusu görürsünüz. Bir hata oluşursa bkz. [sorun giderme](#troubleshooting).

    Bağlantı diğer adını kaydetmek için **diğer ad Ekle** iletişim kutusunun altındaki **Tamam** düğmesini kullanın.

8. SQuirreL SQL 'in en üstündeki açılan menüde bulunan **bağlantı** noktasından, **HDInsight 'ta Hive**' yi seçin. İstendiğinde, **Bağlan**' ı seçin.

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png" alt-text="parametrelerle bağlantı iletişim kutusu" border="true":::

9. Bağlandıktan sonra, SQL sorgu iletişim kutusuna aşağıdaki sorguyu girin ve sonra **Çalıştır** simgesini (çalışan bir kişi) seçin. Sonuçlar alanında sorgunun sonuçları gösterilmelidir.

    ```hiveql
    select * from hivesampletable limit 10;
    ```

    :::image type="content" source="./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png" alt-text="sonuçlar dahil SQL sorgu iletişim kutusu" border="true":::

## <a name="connect-from-an-example-java-application"></a>Örnek Java uygulamasından bağlanma

HDInsight 'ta Hive sorgulamak için Java istemcisi kullanmanın bir örneği, adresinde bulunabilir [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc) . Örneği derlemek ve çalıştırmak için depodaki yönergeleri izleyin.

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

1. SQUIRREL programından çıkın ve sonra da bilgisayarınızda SQUIRREL 'in yüklü olduğu dizine gidin `C:\Program Files\squirrel-sql-4.0.0\lib` . SQUIRREL dizininde, dizin altında, `lib` var olan Commons-codec. jar dosyasını HDInsight kümesinden indirilen ile değiştirin.

1. SQUIRREL 'i yeniden başlatın. HDInsight 'ta Hive 'e bağlanılırken hata artık gerçekleşmemelidir.

### <a name="connection-disconnected-by-hdinsight"></a>HDInsight tarafından bağlantısı kesilen bağlantı

**Belirtiler**: JDBC/ODBC aracılığıyla çok büyük miktarlarda veri (birkaç GB) indirmeye çalışırken, indirme sırasında, bağlantı HDInsight tarafından beklenmedik şekilde kesilir.

**Neden**: Bu hata, ağ geçidi düğümlerinde sınırlama nedeniyle oluşur. JDBC/ODBC 'den veri alırken tüm verilerin ağ geçidi düğümünden geçmesi gerekir. Ancak ağ geçidi, çok büyük miktarda veriyi indirmek üzere tasarlanmamıştır, bu nedenle trafiği işleyemezse ağ geçidi bağlantıyı kapatabilir.

**Çözüm**: çok büyük miktarlarda veriyi ındırmek için JDBC/ODBC sürücüsü kullanmaktan kaçının. Bunun yerine doğrudan blob depolamadan verileri kopyalayın.

## <a name="next-steps"></a>Sonraki adımlar

Artık, Hive ile çalışmak için JDBC 'ı nasıl kullanacağınızı öğrendiğinize göre, Azure HDInsight ile çalışmanın diğer yollarını araştırmak için aşağıdaki bağlantıları kullanın.

* [Apache Hive verilerini Azure HDInsight 'Ta Microsoft Power BI Ile görselleştirin](apache-hadoop-connect-hive-power-bi.md).
* [Azure HDInsight 'ta Power BI etkileşimli sorgu Hive verilerini görselleştirin](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Excel 'i Microsoft Hive ODBC sürücüsü Ile HDInsight 'A bağlayın](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Power Query kullanarak Excel 'i Apache Hadoop bağlama](apache-hadoop-connect-excel-power-query.md).
* [HDInsight ile Apache Hive kullanma](hdinsight-use-hive.md)
* [HDInsight ile Apache Pig kullanma](../use-pig.md)
* [HDInsight ile MapReduce işleri kullanma](hdinsight-use-mapreduce.md)
