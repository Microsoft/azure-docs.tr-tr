---
title: HDInsight 'ta kıvrımlı Apache Hadoop Hive kullanma-Azure
description: Apache Pig işlerini kıvrımlı kullanarak Azure HDInsight 'a uzaktan göndermeyi öğrenin.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: hrasheed
ms.openlocfilehash: e1fbeb48acdfd9d09cad2616aed9793e2ff513ad
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736096"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>REST kullanarak HDInsight 'ta Apache Hadoop Apache Hive sorguları çalıştırma

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Azure HDInsight kümesinde Apache Hadoop ile Apache Hive sorguları çalıştırmak için WebHCat REST API nasıl kullanacağınızı öğrenin.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Hadoop kümesi. Bkz. [Linux 'Ta HDInsight kullanmaya başlama](./apache-hadoop-linux-tutorial-get-started.md).

* REST istemcisi. Bu belge Windows PowerShell 'de [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) ve [Bash](https://docs.microsoft.com/windows/wsl/install-win10)üzerinde [kıvrımlı](https://curl.haxx.se/) kullanır.

* Bash kullanıyorsanız, bir komut satırı JSON işlemcisi olan JQ da gerekir.  Bkz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>REST API için temel URI

HDInsight `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`üzerinde REST API için temel Tekdüzen Kaynak tanımlayıcısı (URI), burada `CLUSTERNAME` kümenizin adıdır.  URI 'Lerinde küme adları **büyük/küçük harfe duyarlıdır**.  URI (`CLUSTERNAME.azurehdinsight.net`) öğesinin tam etki alanı adı (FQDN) bölümünde küme adı büyük/küçük harfe duyarsız olsa da, URI 'deki diğer oluşumlar büyük/küçük harfe duyarlıdır.

## <a name="authentication"></a>Authentication

Web Hcat ile bir veya daha fazla REST iletişimini kullanırken, HDInsight kümesi yöneticisinin Kullanıcı adını ve parolasını sağlayarak isteklerin kimlik doğrulamasını yapmanız gerekir. REST API’sinin güvenliği [temel kimlik doğrulaması](https://en.wikipedia.org/wiki/Basic_access_authentication) ile sağlanır. Kimlik bilgilerinizin sunucuya güvenli bir şekilde gönderildiğinden emin olmak için, istekleri her zaman güvenli HTTP (HTTPS) kullanarak yapın.

### <a name="setup-preserve-credentials"></a>Kurulum (kimlik bilgilerini koru)
Her bir örneğe yeniden girmemek için kimlik bilgilerinizi koruyun.  Küme adı ayrı bir adımda korunacaktır.

**A. Bash**  
Aşağıdaki betiği, gerçek parolanızla değiştirerek `PASSWORD` düzenleyin.  Sonra komutunu girin.

```bash
export password='PASSWORD'
```  

**KENARI. PowerShell** aşağıdaki kodu yürütün ve açılır pencerede kimlik bilgilerinizi girin:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Doğru şekilde oluşturulmuş küme adını tanımla
Kümenin nasıl oluşturulduğuna bağlı olarak, küme adının gerçek büyük küçük harfleri beklediğinizden farklı olabilir.  Buradaki adımlarda gerçek büyük küçük harf görüntülenir ve ardından sonraki tüm örnekler için bir değişkende depoda yer verilmiştir.

Aşağıdaki komut dosyalarını, küme adınızla `CLUSTERNAME` değiştirmek için düzenleyin. Sonra komutunu girin. (FQDN için küme adı, büyük/küçük harfe duyarlı değildir.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a id="curl"></a>Hive sorgusu çalıştırma

1. HDInsight kümenize bağlanabildiğinizi doğrulamak için aşağıdaki komutlardan birini kullanın:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Aşağıdaki metne benzer bir yanıt alıyorsunuz:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Bu komutta kullanılan parametreler aşağıdaki gibidir:

    * `-u`-İsteğin kimliğini doğrulamak için kullanılan Kullanıcı adı ve parola.
    * `-G`-Bu isteğin bir GET işlemi olduğunu gösterir.

1. URL `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`'nin başlangıcı tüm istekler için aynıdır. Yolu `/status`, isteğin, sunucu için webhcat (tempkaton olarak da bilinir) durumunun döndürülmeyeceğini gösterir. Ayrıca, aşağıdaki komutu kullanarak Hive sürümünü isteyebilirsiniz:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Bu istek aşağıdaki metne benzer bir yanıt döndürür:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. **Log4jLogs**adlı bir tablo oluşturmak için aşağıdakileri kullanın:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Bu istek, REST API isteğin bir parçası olarak veri gönderen POST yöntemini kullanır. Şu veri değerleri istekle birlikte gönderilir:

     * `user.name`-Komutunu çalıştıran kullanıcı.
     * `execute`-Yürütülecek HiveQL deyimleri.
     * `statusdir`-Bu işin durumunun yazıldığı dizin.

   Bu deyimler aşağıdaki eylemleri gerçekleştirir:

   * `DROP TABLE`-Tablo zaten varsa, silinir.
   * `CREATE EXTERNAL TABLE`-Hive içinde yeni bir ' External ' tablosu oluşturur. Dış tablolar yalnızca Hive içindeki tablo tanımını depolar. Veriler özgün konumda bırakılır.

     > [!NOTE]  
     > Dış tablolar, temel alınan verilerin bir dış kaynak tarafından güncelleştirilmesini beklediğinde kullanılmalıdır. Örneğin, otomatik bir veri yükleme işlemi veya başka bir MapReduce işlemi.
     >
     > Dış tablonun atılması, yalnızca tablo tanımı olan **verileri silmez.**

   * `ROW FORMAT`-Veriler nasıl biçimlendirilir. Her günlükteki alanlar boşlukla ayrılır.
   * `STORED AS TEXTFILE LOCATION`-Verilerin nerede depolandığını (örnek/veri dizini) ve metin olarak saklandığı yerdir.
   * `SELECT`- **T4** sütununun **[Error]** değerini içerdiği tüm satırların sayısını seçer. Bu ifade, bu değeri içeren üç satır olduğu için **3** değerini döndürür.

     > [!NOTE]  
     > Hiveql deyimleri arasındaki boşlukların, kıvrımlı ile kullanıldığında `+` karakteriyle değiştirildiğine dikkat edin. Sınırlayıcı gibi bir boşluk içeren tırnak içine alınmış değerler ile `+`değiştirilmemelidir.

      Bu komut, işin durumunu denetlemek için kullanılabilecek bir iş KIMLIĞI döndürür.

1. İşin durumunu denetlemek için şu komutu kullanın:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    İş tamamlandıysa, durum **başarılı**olur.

1. İşin durumu **başarılı**olarak değiştirildikten sonra, Azure Blob depolamadan iş sonuçlarını alabilirsiniz. Sorguyla geçirilen `/example/rest`parametre, çıkış dosyasının konumunu içerir; bu durumda,. `statusdir` Bu adres, çıktıyı `example/curl` varsayılan depolama alanı kümelerindeki dizinde depolar.

    [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)kullanarak bu dosyaları listeleyebilir ve indirebilirsiniz. Azure depolama ile Azure CLı kullanma hakkında daha fazla bilgi için bkz. Azure [CLI 'Yi Azure depolama belgesiyle kullanma](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) .

## <a id="nextsteps"></a>Sonraki adımlar

HDInsight ile Hive hakkında genel bilgi için:

* [HDInsight üzerinde Apache Hadoop ile Apache Hive kullanma](hdinsight-use-hive.md)

HDInsight 'ta Hadoop ile birlikte çalışmak için kullanabileceğiniz diğer yollar hakkında daha fazla bilgi için:

* [HDInsight üzerinde Apache Hadoop Apache Pig kullanma](hdinsight-use-pig.md)
* [HDInsight üzerinde Apache Hadoop MapReduce kullanma](hdinsight-use-mapreduce.md)

Bu belgede kullanılan REST API hakkında daha fazla bilgi için bkz. [Webhcat başvuru](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) belgesi.