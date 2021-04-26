---
title: Azure HDInsight 'ta Spark kümesine iş göndermek için Livy Spark kullanma
description: Spark işlerini bir Azure HDInsight kümesine uzaktan göndermek için Apache Spark REST API nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: ff63f4fbadd7cb9e7584e2aa045583a35e0363fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930119"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>HDInsight Spark kümesine uzak işleri göndermek için Apache Spark REST API’sini kullanma

Uzak işleri bir Azure HDInsight Spark kümesine göndermek için kullanılan Apache Spark REST API [Apache Livy](https://livy.incubator.apache.org/)'ı nasıl kullanacağınızı öğrenin. Ayrıntılı belgeler için bkz. [Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html).

Etkileşimli Spark kabukları çalıştırmak veya Spark üzerinde çalıştırılacak toplu işleri göndermek için Livy 'ı kullanabilirsiniz. Bu makale toplu işleri göndermek için Livy kullanma hakkında konuşur. Bu makaledeki kod parçacıkları, Livy Spark uç noktasına REST API çağrısı yapmak için kıvrımlı kullanır.

## <a name="prerequisites"></a>Önkoşullar

HDInsight üzerinde bir Apache Spark kümesi. Yönergeler için bkz. [Azure HDInsight'ta Apache Spark kümeleri oluşturma](apache-spark-jupyter-spark-sql.md).

## <a name="submit-an-apache-livy-spark-batch-job"></a>Apache Livy Spark toplu işi gönderme

Bir toplu iş göndermeden önce, uygulama jar öğesini kümeyle ilişkili küme depolamasına yüklemeniz gerekir. Bunu yapmak için, bir komut satırı yardımcı programı olan [AzCopy](../../storage/common/storage-use-azcopy-v10.md)’yi kullanabilirsiniz. Veri yüklemek için kullanabileceğiniz çeşitli başka istemciler vardır. [HDInsight 'ta Apache Hadoop işleri Için karşıya yükleme verilerinde](../hdinsight-upload-data.md)daha fazla bilgi bulabilirsiniz.

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Örnekler

* Jar dosyası küme depolamadaki (GB)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Jar dosya adını ve ClassName bir giriş dosyasının parçası olarak geçirmek istiyorsanız (Bu örnekte, input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Küme üzerinde çalışan Livy Spark toplu işleri hakkında bilgi alın

Söz dizimi:

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Örnekler

* Kümede çalışan tüm Livy Spark toplu işlerini almak istiyorsanız:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

* Belirli bir toplu iş KIMLIĞINE sahip belirli bir toplu işi almak istiyorsanız

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Livy Spark toplu işini silme

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Örnek

Toplu iş KIMLIKLI bir Batch işi siliniyor `5` .

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark ve yüksek kullanılabilirlik

Livy, kümede çalışan Spark işleri için yüksek kullanılabilirlik sağlar. Birkaç örnek aşağıda verilmiştir.

* Bir Spark kümesine uzaktan bir iş gönderdikten sonra Livy hizmeti aşağı gittiğinde, iş arka planda çalışmaya devam eder. Livy yedeklenme durumunda işin durumunu geri yükler ve rapor geri alır.
* HDInsight için jupyıter Not defterleri arka uçta Livy ile desteklenir. Bir not defteri bir Spark işi çalıştırıyorsa ve Livy hizmeti yeniden başlatılırsa, Not defteri kod hücrelerini çalıştırmaya devam eder.

## <a name="show-me-an-example"></a>Bir örnek göster

Bu bölümde, toplu işi göndermek, işin ilerlemesini izlemek ve sonra silmek için Livy Spark kullanma örneklerine bakacağız. Bu örnekte kullandığımız uygulama, [tek başına bir Scala uygulaması oluşturma ve HDInsight Spark kümesinde çalıştırma](apache-spark-create-standalone-application.md)makalesinde geliştirilen bir uygulamadır. Buradaki adımlarda şunu varsayın:

* Uygulama jar üzerinden kümeyle ilişkili depolama hesabına zaten kopyaladınız.
* Bu adımları denediğiniz bilgisayarda yüklü olan bilgisayara Kıvyorsunuz.

Aşağıdaki adımları gerçekleştirin:

1. Kullanım kolaylığı için ortam değişkenlerini ayarlayın. Bu örnek, bir Windows ortamını temel alır, ortamınız için gerektiğinde değişkenleri gözden geçirin. `CLUSTERNAME`Ve `PASSWORD` değerlerini uygun değerlerle değiştirin.

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. Küme üzerinde Livy Spark 'ın çalıştığını doğrulayın. Bu işlemi, çalışan toplu işlerin bir listesini alarak yapabiliriz. İlk kez Livy kullanarak bir iş çalıştırıyorsanız, çıkış sıfır döndürmelidir.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
    ```

    Aşağıdaki kod parçacığına benzer bir çıktı almalısınız:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Çıktıda son satırın, çalışan toplu işlem olmayan **0** olarak nasıl göründüğünü fark edin.

1. Şimdi bir toplu iş göndermemize izin verin. Aşağıdaki kod parçacığı, jar adını ve sınıf adını parametreler olarak geçirmek için bir giriş dosyası (input.txt) kullanır. Bu adımları bir Windows bilgisayarından çalıştırıyorsanız, bir giriş dosyası kullanılması önerilen yaklaşımdır.

    ```cmd
    curl -k --user "admin:%password%" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://%clustername%.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    Dosya **input.txt** parametreler aşağıdaki gibi tanımlanır:

    ```text
    { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Aşağıdaki kod parçacığına benzer bir çıktı görmeniz gerekir:

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Çıktının son satırının **durum: başlangıç** olarak nasıl göründüğünü unutmayın. Ayrıca, **ID: 0** olur. Burada, **0** toplu iş kimliğidir.

1. Artık toplu iş KIMLIĞINI kullanarak bu belirli toplu işin durumunu alabilirsiniz.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Aşağıdaki kod parçacığına benzer bir çıktı görmeniz gerekir:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Çıktı şimdi, işin başarıyla tamamlandığını öneren **durum: başarılı**' i gösterir.

1. İsterseniz, toplu işi de silebilirsiniz.

    ```cmd
    curl -k --user "admin:%password%" -v -X DELETE "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Aşağıdaki kod parçacığına benzer bir çıktı görmeniz gerekir:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Çıktının son satırı, toplu işin başarıyla silindiğini gösterir. Bir işi silme, çalışırken de işi de işten çıkar. Tamamlanmış, başarıyla veya başka bir şekilde tamamlanan bir işi silerseniz, iş bilgilerini tamamen siler.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>HDInsight 3,5 sürümünden başlayarak Livy yapılandırma güncelleştirmeleri

HDInsight 3,5 kümeleri ve üzeri, varsayılan olarak, örnek veri dosyalarına veya jars 'e erişmek için yerel dosya yollarının kullanımını devre dışı bırakır. `wasbs://`Kümeden jar dosyaları dışındaki veya örnek veri dosyalarına erişmek için yolu kullanmanızı öneririz.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Azure sanal ağı içindeki bir küme için Livy iş gönderme

Bir Azure sanal ağının içinden bir HDInsight Spark kümesine bağlanıyorsanız, kümedeki Livy 'a doğrudan bağlanabilirsiniz. Böyle bir durumda, Livy uç noktasının URL 'SI `http://<IP address of the headnode>:8998/batches` . Burada **8998** , küme headnode üzerinde, Livy 'ın çalıştırıldığı bağlantı noktasıdır. Genel olmayan bağlantı noktalarında hizmetlere erişme hakkında daha fazla bilgi için bkz. [HDInsight üzerinde Apache Hadoop Hizmetleri tarafından kullanılan bağlantı noktaları](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Apache Livy REST API belgeleri](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Azure HDInsight’ta Apache Spark kümesi kaynaklarını yönetme](apache-spark-resource-manager.md)
* [HDInsight’ta bir Apache Spark kümesinde çalışan işleri izleme ve hata ayıklama](apache-spark-job-debugging.md)