---
title: Azure HDInsight ile Apache Sqoop işleri çalıştırma (Apache Hadoop)
description: Bir Hadoop kümesi ile Azure SQL veritabanı arasında Sqoop içeri aktarma ve dışarı aktarma çalıştırmak için bir iş istasyonundan Azure PowerShell nasıl kullanacağınızı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: f2a153b1eef974c8c73df49a6eed53ef5dbf2353
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076207"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>HDInsight 'ta Hadoop ile Apache Sqoop kullanma

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight 'ta Apache Sqoop kullanarak bir HDInsight kümesi ile Azure SQL veritabanı arasında veri içeri ve dışarı aktarma hakkında bilgi edinin.

Apache Hadoop, Günlükler ve dosyalar gibi yapılandırılmamış ve yarı yapılandırılmış verilerin işlenmesine yönelik doğal bir seçim olsa da, ilişkisel veritabanlarında depolanan yapılandırılmış verilerin işlenmesi da gerekebilir.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) , Hadoop kümeleri ve ilişkisel veritabanları arasında veri aktarmak için tasarlanan bir araçtır. Bunu kullanarak SQL Server, MySQL veya Oracle gibi bir ilişkisel veritabanı yönetim sisteminden (RDBMS) Hadoop Dağıtılmış dosya sistemine (ISE) veri aktarabilir, Hadoop 'daki verileri MapReduce veya Apache Hive dönüştürebilirsiniz ve sonra verileri bir RDBMS 'ye geri aktarabilirsiniz . Bu makalede, ilişkisel veritabanınız için bir SQL Server veritabanı kullanıyorsunuz.

> [!IMPORTANT]  
> Bu makalede, veri aktarımını gerçekleştirmek için bir test ortamı ayarlanır. Daha sonra bu ortam için bir veri aktarım yöntemi seçin [Sqoop Işlerini Çalıştır](#run-sqoop-jobs)bölümünde aşağıdaki adımları izleyin.

HDInsight kümelerinde desteklenen Sqoop sürümleri için bkz. [HDInsight tarafından sunulan küme sürümlerindeki yenilikler nelerdir?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Senaryoyu anlama

HDInsight kümesi bazı örnek verilerle birlikte gelir. Aşağıdaki iki örneği kullanabilirsiniz:

* Konumunda `/example/data/sample.log`bulunan bir Apache Log4J günlük dosyası. Aşağıdaki Günlükler dosyadan ayıklanır:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Adlı `hivesampletable`Hive tablosu, konumunda `/hive/warehouse/hivesampletable`bulunan veri dosyasına başvuruda bulunur. Tablo, bazı mobil cihaz verileri içerir.
  
  | Alan | Veri türü |
  | --- | --- |
  | ClientID |dize |
  | querytime |dize |
  | Pazara |dize |
  | deviceplatform |dize |
  | devicemake |dize |
  | devicemodel |dize |
  | state |dize |
  | Ülke |dize |
  | querydwelltime |double |
  | kimliği |bigint |
  | sessionpageviewworder |bigint |

Bu makalede, Sqoop içeri aktarma ve dışarı aktarma sınamasını yapmak için bu iki veri kümesini kullanırsınız.

## <a name="create-cluster-and-sql-database"></a>Test ortamını ayarlama
Küme, SQL veritabanı ve diğer nesneler, Azure portal aracılığıyla Azure Resource Manager şablonu kullanılarak oluşturulur. Şablon, [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/)' nda bulunabilir. Kaynak Yöneticisi şablonu, tablo şemalarını bir SQL veritabanına dağıtmak için bacpac paketini çağırır.  Bacpac paketi ortak bir blob kapsayıcısında https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac bulunur. Bacpac dosyaları için özel bir kapsayıcı kullanmak istiyorsanız, şablonda aşağıdaki değerleri kullanın:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Şablon kullanarak içeri aktarma veya Azure portal yalnızca Azure Blob depolamadan BACPAC dosyasını içeri aktarmayı destekler.

1. Azure portal Kaynak Yöneticisi şablonunu açmak için aşağıdaki görüntüyü seçin.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Aşağıdaki özellikleri girin:

    |Alan |Value |
    |---|---|
    |Subscription |Açılan listeden Azure aboneliğinizi seçin.|
    |Resource group |Açılan listeden kaynak grubunuzu seçin veya yeni bir tane oluşturun|
    |Location |Açılan listeden bir bölge seçin.|
    |Küme Adı |Hadoop kümesi için bir ad girin. Yalnızca küçük harf kullanın.|
    |Küme Oturum Açma Kullanıcı Adı |Önceden doldurulmuş değeri `admin`saklayın.|
    |Küme Oturum Açma Parolası |Bir parola girin.|
    |SSH Kullanıcı adı |Önceden doldurulmuş değeri `sshuser`saklayın.|
    |SSH parolası |Bir parola girin.|
    |SQL Yöneticisi oturum açma |Önceden doldurulmuş değeri `sqluser`saklayın.|
    |SQL yönetici parolası |Bir parola girin.|
    |_yapıt konumu | Kendi bacpac dosyanızı farklı bir konumda kullanmak istemediğiniz müddetçe varsayılan değeri kullanın.|
    |_yapıt konumu SAS belirteci |Boş bırakın.|
    |Bacpac dosya adı |Kendi bacpac dosyanızı kullanmak istemediğiniz müddetçe varsayılan değeri kullanın.|
    |Location |Varsayılan değeri kullanın.|

    Azure SQL Server adı `<ClusterName>dbserver`olacaktır. Veritabanı adı `<ClusterName>db`olacaktır. Varsayılan depolama hesabı adı `e6qhezrh2pdqu`olacaktır.

3. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum '** u seçin.

4. **Satın al**'ı seçin. Şablon dağıtımı için dağıtım gönderme başlıklı yeni bir kutucuk görürsünüz. Kümenin ve SQL veritabanının oluşturulması yaklaşık 20 dakika sürer.

## <a name="run-sqoop-jobs"></a>Sqoop işlerini Çalıştır

HDInsight, çeşitli yöntemler kullanarak Sqoop işlerini çalıştırabilir. Size hangi yöntemin doğru olduğuna karar vermek için aşağıdaki tabloyu kullanın, ardından bir izlenecek yol için bağlantıyı izleyin.

| **Bu** isterseniz... | ...an **etkileşimli** Kabuk | ...**toplu** işleme | ...from bu **istemci işletim sistemi** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X veya Windows |
| [Hadoop için .NET SDK](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (şimdilik) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Sınırlamalar

* Toplu dışa aktarma-Linux tabanlı HDInsight Ile, Microsoft SQL Server veya Azure SQL veritabanı 'na veri aktarmak için kullanılan Sqoop Bağlayıcısı Şu anda toplu eklemeleri desteklememektedir.
* Toplu işleme-Linux tabanlı HDInsight ile, eklemeleri gerçekleştirirken `-batch` anahtarı kullanırken, ekleme işlemlerini toplu olarak gerçekleştirmek yerine Sqoop birden çok ekleme gerçekleştirir.

## <a name="next-steps"></a>Sonraki adımlar
Şimdi Sqoop 'yi nasıl kullanacağınızı öğrendiniz. Daha fazla bilgi için bkz:

* [HDInsight ile Apache Hive kullanma](../hdinsight-use-hive.md)
* [HDInsight ile Apache Pig kullanma](../hdinsight-use-pig.md)
* [HDInsight 'a veri yükleme](../hdinsight-upload-data.md): HDInsight/Azure Blob depolamaya veri yüklemek için diğer yöntemleri bulun.
