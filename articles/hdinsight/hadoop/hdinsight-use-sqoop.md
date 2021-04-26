---
title: Azure HDInsight ile Apache Sqoop işleri çalıştırma (Apache Hadoop)
description: Bir Hadoop kümesi ile Azure SQL veritabanı arasında Sqoop içeri aktarma ve dışarı aktarma çalıştırmak için bir iş istasyonundan Azure PowerShell nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: 1c34b673cd970a9e7577b7ff01d27eb0e4cc1ac1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946442"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>HDInsight'ta Hadoop ile Apache Sqoop'u kullanma

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight 'ta Apache Sqoop kullanarak HDInsight kümesi ile Azure SQL veritabanı arasında veri içeri ve dışarı aktarma hakkında bilgi edinin.

Apache Hadoop, Günlükler ve dosyalar gibi yapılandırılmamış ve yarı yapılandırılmış verilerin işlenmesine yönelik doğal bir seçim olsa da, ilişkisel veritabanlarında depolanan yapılandırılmış verilerin işlenmesi da gerekebilir.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) , Hadoop kümeleri ve ilişkisel veritabanları arasında veri aktarmak için tasarlanan bir araçtır. SQL Server, MySQL veya Oracle gibi bir ilişkisel veritabanı yönetim sisteminden (RDBMS) verileri Hadoop Dağıtılmış dosya sistemine (ISE) içeri aktarmak, Hadoop 'daki verileri MapReduce veya Apache Hive dönüştürmek ve ardından verileri bir RDBMS 'ye dışarı aktarmak için kullanabilirsiniz. Bu makalede, ilişkisel veritabanınız için Azure SQL veritabanı 'nı kullanıyorsunuz.

> [!IMPORTANT]  
> Bu makalede, veri aktarımını gerçekleştirmek için bir test ortamı ayarlanır. Daha sonra bu ortam için bir veri aktarım yöntemi seçin [Sqoop Işlerini Çalıştır](#run-sqoop-jobs)bölümünde aşağıdaki adımları izleyin.

HDInsight kümelerinde desteklenen Sqoop sürümleri için bkz. [HDInsight tarafından sunulan küme sürümlerindeki yenilikler nelerdir?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Senaryoyu anlama

HDInsight kümesi bazı örnek verilerle birlikte gelir. Aşağıdaki iki örneği kullanabilirsiniz:

* Konumunda bulunan bir Apache Log4J günlük dosyası `/example/data/sample.log` . Aşağıdaki Günlükler dosyadan ayıklanır:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Adlı Hive tablosu `hivesampletable` , konumunda bulunan veri dosyasına başvuruda bulunur `/hive/warehouse/hivesampletable` . Tablo, bazı mobil cihaz verileri içerir.
  
  | Alan | Veri türü |
  | --- | --- |
  | ClientID |string |
  | querytime |string |
  | pazara |string |
  | deviceplatform |string |
  | devicemake |string |
  | devicemodel |string |
  | state |string |
  | ülke |string |
  | querydwelltime |double |
  | kimliği |bigint |
  | sessionpageviewworder |bigint |

Bu makalede, Sqoop içeri aktarma ve dışarı aktarma sınamasını yapmak için bu iki veri kümesini kullanırsınız.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Test ortamını ayarlama

Küme, SQL veritabanı ve diğer nesneler, Azure portal aracılığıyla Azure Resource Manager şablonu kullanılarak oluşturulur. Şablon, [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/)' nda bulunabilir. Kaynak Yöneticisi şablonu, tablo şemalarını bir SQL veritabanına dağıtmak için bacpac paketini çağırır.  Bacpac paketi ortak bir blob kapsayıcısında bulunur https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac . Bacpac dosyaları için özel bir kapsayıcı kullanmak istiyorsanız, şablonda aşağıdaki değerleri kullanın:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Şablon kullanarak içeri aktarma veya Azure portal yalnızca Azure Blob depolamadan BACPAC dosyasını içeri aktarmayı destekler.

1. Azure portal Kaynak Yöneticisi şablonunu açmak için aşağıdaki görüntüyü seçin.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Aşağıdaki özellikleri girin:

    |Alan |Değer |
    |---|---|
    |Abonelik |Açılan listeden Azure aboneliğinizi seçin.|
    |Kaynak grubu |Açılan listeden kaynak grubunuzu seçin veya yeni bir tane oluşturun|
    |Konum |Açılan listeden bir bölge seçin.|
    |Küme Adı |Hadoop kümesi için bir ad girin. Yalnızca küçük harf kullanın.|
    |Küme Oturum Açma Kullanıcı Adı |Önceden doldurulmuş değeri saklayın `admin` .|
    |Küme Oturum Açma Parolası |Bir parola girin.|
    |SSH Kullanıcı adı |Önceden doldurulmuş değeri saklayın `sshuser` .|
    |SSH parolası |Bir parola girin.|
    |SQL Yöneticisi oturum açma |Önceden doldurulmuş değeri saklayın `sqluser` .|
    |SQL yönetici parolası |Bir parola girin.|
    |_artifacts konumu | Kendi bacpac dosyanızı farklı bir konumda kullanmak istemediğiniz müddetçe varsayılan değeri kullanın.|
    |_artifacts konumu SAS belirteci |Boş bırakın.|
    |Bacpac dosya adı |Kendi bacpac dosyanızı kullanmak istemediğiniz müddetçe varsayılan değeri kullanın.|
    |Konum |Varsayılan değeri kullanın.|

    [MANTıKSAL SQL Server](../../azure-sql/database/logical-servers.md) adı olacaktır `<ClusterName>dbserver` . Veritabanı adı olacaktır `<ClusterName>db` . Varsayılan depolama hesabı adı olacaktır `e6qhezrh2pdqu` .

3. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum '** u seçin.

4. **Satın al**'ı seçin. Şablon dağıtımı için dağıtım gönderme başlıklı yeni bir kutucuk görürsünüz. Kümenin ve SQL Database’in oluşturulması yaklaşık 20 dakika sürer.

## <a name="run-sqoop-jobs"></a>Sqoop işlerini Çalıştır

HDInsight, çeşitli yöntemler kullanarak Sqoop işlerini çalıştırabilir. Size hangi yöntemin doğru olduğuna karar vermek için aşağıdaki tabloyu kullanın, ardından bir izlenecek yol için bağlantıyı izleyin.

| İsterseniz **bunu kullanın** ... | ... **etkileşimli** bir kabuk | ... **toplu** işleme | ... Bu **istemci işletim sisteminden** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X veya Windows |
| [Hadoop için .NET SDK](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (şimdilik) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Sınırlamalar

* Toplu dışa aktarma-Linux tabanlı HDInsight Ile, verileri Microsoft SQL Server veya SQL veritabanına aktarmak için kullanılan Sqoop Bağlayıcısı Şu anda toplu eklemeleri desteklememektedir.
* Toplu işleme-Linux tabanlı HDInsight Ile, `-batch` eklemeleri gerçekleştirirken anahtarı kullanırken, ekleme işlemlerini toplu olarak gerçekleştirmek yerine Sqoop birden çok ekleme gerçekleştirir.

## <a name="next-steps"></a>Sonraki adımlar

Şimdi Sqoop 'yi nasıl kullanacağınızı öğrendiniz. Daha fazla bilgi edinmek için şu makalelere bakın:

* [HDInsight ile Apache Hive kullanma](./hdinsight-use-hive.md)
* [HDInsight 'a veri yükleme](../hdinsight-upload-data.md): HDInsight/Azure Blob depolamaya veri yüklemek için diğer yöntemleri bulun.
* [HDInsight üzerinde Apache Hadoop ile SQL Veritabanı arasında veri içeri ve dışarı aktarma işlemleri için Apache Sqoop'u kullanma](./apache-hadoop-use-sqoop-mac-linux.md)