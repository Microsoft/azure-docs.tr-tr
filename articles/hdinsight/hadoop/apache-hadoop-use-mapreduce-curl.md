---
title: HDInsight 'ta Apache Hadoop MapReduce kullanma ve kıvrımlı-Azure
description: Bkz. HDInsight 'ta MapReduce işlerini HDInsight üzerinde Apache Hadoop ile uzaktan çalıştırmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: e90dc2c7220caf5bd72b7086adc275934652e150
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939694"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>REST kullanarak HDInsight üzerinde Apache Hadoop MapReduce işleri çalıştırma

MapReduce işlerini HDInsight kümesindeki bir Apache Hadoop üzerinde çalıştırmak için Apache Hive WebHCat REST API nasıl kullanacağınızı öğrenin. Kıvrımlı, MapReduce işlerini çalıştırmak için ham HTTP isteklerini kullanarak HDInsight ile nasıl etkileşim kurabileceğinizi göstermek için kullanılır.

> [!NOTE]  
> Linux tabanlı Hadoop sunucuları kullanmayı zaten biliyorsanız, ancak HDInsight 'a yeni bir adım daha sahipseniz [HDInsight 'Ta Linux tabanlı Apache Hadoop hakkında bilmeniz](../hdinsight-hadoop-linux-information.md) gerekenler bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

* HDInsight üzerinde bir Apache Hadoop kümesi. Bkz. [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md).

Şunlardan biri:
  * Windows PowerShell veya,
  * [JQ](https://stedolan.github.io/jq/) ile [kıvrımlı](https://curl.haxx.se/)

## <a name="run-a-mapreduce-job"></a>MapReduce işi çalıştırma

> [!NOTE]  
> WebHCat ile kıvrımlı veya diğer REST iletişimini kullandığınızda, HDInsight kümesi Yönetici Kullanıcı adı ve parolasını sağlayarak isteklerin kimlik doğrulamasını yapmanız gerekir. İstekleri sunucuya göndermek için kullanılan URI 'nin bir parçası olarak küme adını kullanmanız gerekir.
>
> REST API, [temel erişim kimlik doğrulaması](https://en.wikipedia.org/wiki/Basic_access_authentication)kullanılarak güvenli hale getirilir. Kimlik bilgilerinizin sunucuya güvenli bir şekilde gönderilmesini sağlamak için her zaman HTTPS kullanarak istek yapmalısınız.

### <a name="curl"></a>Curl

1. Kullanım kolaylığı için aşağıdaki değişkenleri ayarlayın. Bu örnek, bir Windows ortamını temel alır ve ortamınız için gerektiği şekilde gözden geçirin.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. HDInsight kümenize bağlanabildiğinizi doğrulamak için bir komut satırında aşağıdaki komutu kullanın:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Bu komutta kullanılan parametreler aşağıdaki gibidir:

   * **-u**: isteğin kimliğini doğrulamak için kullanılan Kullanıcı adını ve parolayı belirtir
   * **-G**: Bu IŞLEMIN bir get isteği olduğunu belirtir

   URI 'nin başlangıcı `https://CLUSTERNAME.azurehdinsight.net/templeton/v1` tüm istekler için aynıdır.

    Aşağıdaki JSON 'a benzer bir yanıt alırsınız:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. MapReduce işi göndermek için aşağıdaki komutu kullanın. **JQ** yolunu gerektiği şekilde değiştirin.

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    URI 'nin sonu (/MapReduce/jar), bu isteğin bir jar dosyasındaki bir sınıftan MapReduce işi başlattığı Web Hcat 'e bildirir. Bu komutta kullanılan parametreler aşağıdaki gibidir:

   * **-d**: `-G` kullanılmıyor, bu nedenle istek Post yöntemine varsayılan olarak yapılır. `-d` istekle birlikte gönderilen veri değerlerini belirtir.
     * **User.Name**: komutunu çalıştıran Kullanıcı
     * **jar**: çalıştırılacak sınıfı içeren jar dosyasının konumu
     * **Class**: MapReduce mantığını içeren sınıf
     * **arg**: MapReduce işine geçirilecek bağımsız değişkenler. Bu durumda, giriş metin dosyası ve çıkış için kullanılan dizin

    Bu komut, işin durumunu denetlemek için kullanılabilecek bir iş KIMLIĞI döndürmelidir: `job_1415651640909_0026` .

1. İşin durumunu denetlemek için aşağıdaki komutu kullanın. Değerini, `JOBID` önceki adımda döndürülen **Gerçek** değer ile değiştirin. **JQ** 'ın yerini gereken şekilde gözden geçirin.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Kullanım kolaylığı için aşağıdaki değişkenleri ayarlayın. `CLUSTERNAME`Gerçek küme adınızla değiştirin. Komutunu yürütün ve istendiğinde küme oturum açma parolasını girin.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. HDInsight kümenize bağlanabildiğinizi doğrulamak için aşağıdaki komutu kullanın:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Aşağıdaki JSON 'a benzer bir yanıt alırsınız:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. MapReduce işi göndermek için aşağıdaki komutu kullanın:

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    URI 'nin sonu (/MapReduce/jar), bu isteğin bir jar dosyasındaki bir sınıftan MapReduce işi başlattığı Web Hcat 'e bildirir. Bu komutta kullanılan parametreler aşağıdaki gibidir:

    * **User.Name**: komutunu çalıştıran Kullanıcı
    * **jar**: çalıştırılacak sınıfı içeren jar dosyasının konumu
    * **Class**: MapReduce mantığını içeren sınıf
    * **arg**: MapReduce işine geçirilecek bağımsız değişkenler. Bu durumda, giriş metin dosyası ve çıkış için kullanılan dizin

   Bu komut, işin durumunu denetlemek için kullanılabilecek bir iş KIMLIĞI döndürmelidir: `job_1415651640909_0026` .

1. İşin durumunu denetlemek için şu komutu kullanın:

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

### <a name="both-methods"></a>Her iki yöntem

1. İş tamamlandıysanız, döndürülen durum olur `SUCCEEDED` .

1. İşin durumu olarak değiştirildiğinde `SUCCEEDED` , Azure Blob depolamadan iş sonuçlarını alabilirsiniz. `statusdir`Sorguyla geçirilen parametre, çıkış dosyasının konumunu içerir. Bu örnekte, konum `/example/curl` . Bu adres, işin çıkışını konumundaki küme varsayılan deposunda depolar `/example/curl` .

[Azure CLI](/cli/azure/install-azure-cli)kullanarak bu dosyaları listeleyebilir ve indirebilirsiniz. Azure Blob depolama ile çalışmak üzere Azure CLı kullanma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure CLI ile Blobları oluşturma, indirme ve listeleme](../../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="next-steps"></a>Sonraki adımlar

HDInsight 'ta Hadoop ile birlikte çalışmak için kullanabileceğiniz diğer yollar hakkında daha fazla bilgi için:

* [HDInsight üzerinde Apache Hadoop MapReduce kullanma](hdinsight-use-mapreduce.md)
* [HDInsight üzerinde Apache Hadoop ile Apache Hive kullanma](hdinsight-use-hive.md)

Bu makalede kullanılan REST arabirimi hakkında daha fazla bilgi için bkz. [Webhcat başvurusu](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
