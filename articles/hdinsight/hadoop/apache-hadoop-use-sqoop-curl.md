---
title: Azure HDInsight 'ta Apache Sqoop ile verileri dışarı aktarmak için kıvrımlı kullanma
description: Kıvrımlı kullanarak Apache Sqoop işlerini Azure HDInsight 'a uzaktan göndermeyi öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: f70c0a0b68e24e3d61a6c0cef238d1f60911e271
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810720"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>HDInsight 'ta kıvrımlı ile Apache Sqoop işleri çalıştırma
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight 'ta Apache Hadoop kümesinde Apache Sqoop işleri çalıştırmak için kıvrımlı kullanmayı öğrenin. Bu makalede, Azure depolama 'dan verileri dışarı aktarma ve kıvrımlı kullanarak bir SQL Server veritabanına aktarma işlemlerinin nasıl yapılacağı gösterilir. Bu makalede, [HDInsight 'Ta Hadoop Ile Apache Sqoop kullanma](./hdinsight-use-sqoop.md)işlemi devam ediyor.

Kıvrımlı, Sqoop işlerinin sonuçlarını çalıştırmak, izlemek ve almak için ham HTTP istekleri kullanarak HDInsight ile nasıl etkileşim kurabileceğinizi göstermek için kullanılır. Bu, HDInsight kümeniz tarafından sağlanmış olan WebHCat REST API (eski adıyla Temptaton) kullanılarak işe yarar.

## <a name="prerequisites"></a>Önkoşullar

* [Test ortamını ayarlama](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) , [HDInsight 'Ta Hadoop Ile Apache Sqoop kullanın](./hdinsight-use-sqoop.md).

* Azure SQL veritabanını sorgulamak için bir istemci. [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) veya [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)kullanmayı düşünün.

* [Kıvır](https://curl.haxx.se/). Kıvrımlı bir HDInsight kümesine veya bir HDInsight kümesinden veri aktarmaya yönelik bir araçtır.

* [JQ](https://stedolan.github.io/jq/). JQ yardımcı programı, REST isteklerinden döndürülen JSON verilerini işlemek için kullanılır.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Kıvrımlı kullanarak Apache Sqoop işleri gönderme

Azure depolama 'dan SQL Server ' ye Apache Sqoop işleri kullanarak verileri dışarı aktarmak için kıvrımlı kullanın.

> [!NOTE]  
> Curl’ü veya WebHCat ile başka bir REST iletişimini kullanırken HDInsight küme yöneticisinin kullanıcı adı ve parolasını sağlayarak isteklerin kimliğini doğrulamanız gerekir. Ayrıca, sunucuya istek göndermek için kullanılan Tekdüzen Kaynak Tanımlayıcısı’nın (URI) bir parçası olarak küme adını kullanmanız gerekir.

Bu bölümdeki komutlar için, küme kimliğini doğrulamak `USERNAME` için kullanıcıyla değiştirin ve Kullanıcı hesabı parolasıyla değiştirin. `PASSWORD` `CLUSTERNAME` değerini kümenizin adıyla değiştirin.
 
REST API’sinin güvenliği [temel kimlik doğrulaması](https://en.wikipedia.org/wiki/Basic_access_authentication) ile sağlanır. Kimlik bilgilerinizin sunucuya güvenli bir şekilde gönderilmesi için istekleri her zaman Güvenli HTTP (HTTPS) kullanarak yapmanız gerekir.

1. HDInsight kümenize bağlanabildiğinizi doğrulamak için bir komut satırında aşağıdaki komutu kullanın:

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Aşağıdakine benzer bir yanıt almanız gerekir:

    ```json
    {"status":"ok","version":"v1"}
    ```

2. ,,,,,,,,,,,,,,,, `SQLDATABASESERVERNAME` `USERNAME@SQLDATABASESERVERNAME` `PASSWORD` `SQLDATABASENAME` Bir Sqoop işi göndermek için aşağıdakileri kullanın:

    ```cmd
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Bu komutta kullanılan parametreler aşağıdaki gibidir:

   * **-d** -bu `-G` yana kullanılmadığından, istek varsayılan Post yöntemine göre yapılır. `-d`istekle birlikte gönderilen veri değerlerini belirtir.

       * **User.Name** -komutunu çalıştıran kullanıcı.

       * **komut** -yürütülecek Sqoop komutu.

       * **statusdir** -bu işin durumunun yazılacağı dizin.

     Bu komut, işin durumunu denetlemek için kullanılabilecek bir iş KIMLIĞI döndürür.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. İşin durumunu denetlemek için aşağıdaki komutu kullanın. Önceki `JOBID` adımda döndürülen değerle değiştirin. Örneğin, dönüş değeri `{"id":"job_1415651640909_0026"}` `JOBID` olsaydı `job_1415651640909_0026`, olur.

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    İş tamamlandıysa, durum **başarılı**olur.
   
   > [!NOTE]  
   > Bu kıvrımlı istek, işle ilgili bilgiler içeren bir JavaScript Nesne Gösterimi (JSON) belgesi döndürür; JQ yalnızca durum değerini almak için kullanılır.

4. İşin durumu **başarılı**olarak değiştirildikten sonra, Azure Blob depolamadan iş sonuçlarını alabilirsiniz. Sorguyla geçirilen `wasb:///example/data/sqoop/curl`parametre, çıkış dosyasının konumunu içerir; bu durumda,. `statusdir` Bu adres, iş `example/data/sqoop/curl` çıktısını HDInsight kümeniz tarafından kullanılan varsayılan depolama kapsayıcısının dizininde depolar.

    Stderr ve STDOUT bloblarına erişmek için Azure portal kullanabilirsiniz.

5. Verilerin verildiğini doğrulamak için, SQL istemcinizden aşağıdaki sorguları kullanarak aktarılmış verileri görüntüleyin:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Sınırlamalar
* Toplu dışa aktarma-Linux tabanlı HDInsight Ile, Microsoft SQL Server veya Azure SQL veritabanı 'na veri aktarmak için kullanılan Sqoop Bağlayıcısı Şu anda toplu eklemeleri desteklememektedir.
* Toplu işleme-Linux tabanlı HDInsight ile, eklemeleri gerçekleştirirken `-batch` anahtarı kullanırken, ekleme işlemlerini toplu olarak tamamlamak yerine Sqoop birden çok ekleme gerçekleştirir.

## <a name="summary"></a>Özet
Bu belgede gösterildiği gibi, HDInsight kümenizdeki Sqoop işlerinin sonuçlarını çalıştırmak, izlemek ve görüntülemek için ham bir HTTP isteği kullanabilirsiniz.

Bu makalede kullanılan REST arabirimi hakkında daha fazla bilgi için bkz. <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API Kılavuzu</a>.

## <a name="next-steps"></a>Sonraki adımlar
[HDInsight üzerinde Apache Hadoop Apache Sqoop kullanma](hdinsight-use-sqoop.md)

Kıvrımlı ile ilgili diğer HDInsight makaleleri için:
 
* [Azure REST API kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [REST kullanarak HDInsight 'ta Apache Hadoop Apache Hive sorguları çalıştırma](apache-hadoop-use-hive-curl.md)
* [REST kullanarak HDInsight üzerinde Apache Hadoop MapReduce işleri çalıştırma](apache-hadoop-use-mapreduce-curl.md)

