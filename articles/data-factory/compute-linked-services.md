---
title: Azure Data Factory tarafından desteklenen işlem ortamları
description: Verileri dönüştürmek veya işlemek için Azure Data Factory işlem hatları (Azure HDInsight gibi) ile kullanılabilecek bilgi işlem ortamları.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.date: 05/08/2019
ms.openlocfilehash: b9f7cce39e4f51aea7fc4db5ca37ee054f42c5cb
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078681"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Azure Data Factory tarafından desteklenen işlem ortamları

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, verileri işlemek veya dönüştürmek için kullanabileceğiniz farklı işlem ortamları açıklanmaktadır. Ayrıca, bu işlem ortamlarını bir Azure Data Factory 'ye bağlayan bağlı hizmetleri yapılandırırken Data Factory tarafından desteklenen farklı yapılandırma (kendi isteğe bağlı ve kendi kendinize getir) hakkında ayrıntılar sağlar.

Aşağıdaki tabloda, Data Factory tarafından desteklenen işlem ortamlarının listesi ve bunlar üzerinde çalışabilecek etkinlikler verilmiştir. 

| İşlem ortamı                                          | Etkinlikler                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [İsteğe bağlı HDInsight kümesi](#azure-hdinsight-on-demand-linked-service) veya [kendi HDInsight kümeniz](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop akışı](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Özel](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio (klasik)](#azure-machine-learning-studio-classic-linked-service) | [Machine Learning Studio (klasik) etkinlikleri: toplu yürütme ve kaynak güncelleştirme](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning yürütme işlem hattı](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure Synapse Analytics](#azure-synapse-analytics-linked-service), [SQL Server](#sql-server-linked-service) | [Saklı Yordam](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Not defteri](transform-data-databricks-notebook.md), [jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |
| [Azure İşlevi](#azure-function-linked-service)         | [Azure Işlevi etkinliği](control-flow-azure-function-activity.md)
>  

## <a name="hdinsight-compute-environment"></a>HDInsight işlem ortamı

İsteğe bağlı ve KCG (kendi işlem ortamınızı getir) ortamında yapılandırma için desteklenen depolama bağlı hizmet türleri hakkında daha fazla bilgi için aşağıdaki tabloya bakın.

| Işlem bağlantılı hizmetinde | Özellik Adı                | Description                                                  | Blob | ADLS 2. Nesil | Azure SQL DB | ADLS 1. Nesil |
| ------------------------- | ---------------------------- | ------------------------------------------------------------ | ---- | --------- | ------------ | ---------- |
| İsteğe bağlı                 | linkedServiceName            | Verileri depolamak ve işlemek için isteğe bağlı küme tarafından kullanılacak Azure depolama bağlı hizmeti. | Yes  | Yes       | Hayır           | Hayır         |
|                           | additionalLinkedServiceNames | Data Factory hizmeti tarafından sizin adınıza kaydettirilebilmeleri için HDInsight bağlı hizmeti için ek depolama hesapları belirtir. | Yes  | Hayır        | Hayır           | Hayır         |
|                           | hcatalogLinkedServiceName    | HCatalog veritabanına işaret eden Azure SQL bağlı hizmetinin adı. İsteğe bağlı HDInsight kümesi, Azure SQL veritabanı, meta veri deposu olarak kullanılarak oluşturulur. | Hayır   | Hayır        | Yes          | Hayır         |
| BYOC                      | linkedServiceName            | Azure depolama bağlı hizmeti başvurusu.                | Yes  | Yes       | Hayır           | Hayır         |
|                           | additionalLinkedServiceNames | Data Factory hizmeti tarafından sizin adınıza kaydettirilebilmeleri için HDInsight bağlı hizmeti için ek depolama hesapları belirtir. | Hayır   | Hayır        | Hayır           | Hayır         |
|                           | hcatalogLinkedServiceName    | HCatalog veritabanına işaret eden Azure SQL bağlı hizmetine bir başvuru. | Hayır   | Hayır        | Hayır           | Hayır         |

### <a name="azure-hdinsight-on-demand-linked-service"></a>İsteğe bağlı Azure HDInsight bağlı hizmeti

Bu yapılandırmada, bilgi işlem ortamı Azure Data Factory hizmeti tarafından tam olarak yönetilir. Bir iş, verileri işlemek ve iş tamamlandığında kaldırılmadan önce Data Factory hizmeti tarafından otomatik olarak oluşturulur. İsteğe bağlı işlem ortamı için bağlı bir hizmet oluşturabilir, bunu yapılandırabilir ve iş yürütmesi, küme yönetimi ve önyükleme eylemleri için ayrıntılı ayarları kontrol edebilirsiniz.

> [!NOTE]
> İsteğe bağlı yapılandırma Şu anda yalnızca Azure HDInsight kümeleri için desteklenmektedir. Azure Databricks, iş kümelerini kullanarak isteğe bağlı işleri de destekler. Daha fazla bilgi için bkz. [Azure databricks bağlı hizmeti](#azure-databricks-linked-service).

Azure Data Factory hizmeti, verileri işlemek için otomatik olarak isteğe bağlı bir HDInsight kümesi oluşturabilir. Küme, kümeyle ilişkili depolama hesabı (JSON 'daki linkedServiceName özelliği) ile aynı bölgede oluşturulur. Depolama hesabı, `must` genel amaçlı standart bir Azure depolama hesabı. 

İsteğe bağlı HDInsight bağlı hizmeti hakkında aşağıdaki **önemli** noktalara dikkat edin:

* İsteğe bağlı HDInsight kümesi, Azure aboneliğiniz kapsamında oluşturulur. Küme çalışır duruma geldiğinde Azure portal kümeyi görebileceksiniz. 
* İsteğe bağlı HDInsight kümesinde çalıştırılan işlerin günlükleri, HDInsight kümesiyle ilişkili depolama hesabına kopyalanır. Bağlı hizmet tanımınızda tanımlanmış clusterUserName, Kümeparolası, clusterSshUserName, clusterSshPassword, kümenin yaşam döngüsü sırasında ayrıntılı sorun giderme amacıyla kümede oturum açmak için kullanılır. 
* Yalnızca HDInsight kümesinin çalışır duruma ve çalışmaya çalıştığı zaman ücretlendirilirsiniz.
* Azure HDInsight isteğe bağlı hizmeti ile bir **betik eylemi** kullanabilirsiniz.  

> [!IMPORTANT]
> İstek üzerine bir Azure HDInsight kümesi sağlamak için genellikle **20 dakika** veya daha fazla sürer.

#### <a name="example"></a>Örnek

Aşağıdaki JSON, Linux tabanlı isteğe bağlı HDInsight bağlı hizmetini tanımlar. Data Factory hizmeti, gerekli etkinliği işlemek için otomatik olarak bir **Linux tabanlı** HDInsight kümesi oluşturur. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> HDInsight kümesi JSON 'da belirttiğiniz blob depolamada (**Linkedservicename**) bir **varsayılan kapsayıcı** oluşturur. HDInsight, küme silindiğinde bu kapsayıcıyı silmez. Bu davranış tasarım gereğidir. İsteğe bağlı HDInsight bağlı hizmetiyle, HDInsight kümesi her oluşturulduğunda, burada mevcut canlı bir küme (**timeToLive**) olmadıkça bir dilim gerekir ve işlem bittiğinde silinir. 
>
> Daha fazla Etkinlik çalıştırıldığında, Azure Blob depolamada birçok kapsayıcı görürsünüz. İşlerin sorunları giderilmesi için bunlara gerek yoksa, depolama maliyetini azaltmak için bunları silmek isteyebilirsiniz. Bu kapsayıcı adları bir düzene sahiptir: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Azure Blob depolamada kapsayıcıları silmek için [Microsoft Azure Depolama Gezgini](https://storageexplorer.com/) gibi araçları kullanın.

#### <a name="properties"></a>Özellikler

| Özellik                     | Açıklama                              | Gerekli |
| ---------------------------- | ---------------------------------------- | -------- |
| tür                         | Type özelliği **hdınsightondemand** olarak ayarlanmalıdır. | Yes      |
| clusterSize                  | Kümedeki çalışan/veri düğümlerinin sayısı. HDInsight kümesi, bu özellik için belirttiğiniz çalışan düğümü sayısıyla birlikte 2 baş düğüm ile oluşturulur. Düğümler 4 çekirdeğe sahip Standard_D3 boyutlardır, bu nedenle 4 çalışan düğümü kümesi 24 çekirdek alır ( \* çalışan düğümleri için 4 4 = 16 çekirdek, ve \* baş düğümler için 2 4 = 8 çekirdek). Ayrıntılar için bkz. [HDInsight 'Ta Hadoop, Spark, Kafka ve daha fazlası ile kümeleri ayarlama](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) . | Yes      |
| linkedServiceName            | Verileri depolamak ve işlemek için isteğe bağlı küme tarafından kullanılacak Azure depolama bağlı hizmeti. HDInsight kümesi, bu Azure depolama hesabı ile aynı bölgede oluşturulur. Azure HDInsight, desteklediği her bir Azure bölgesinde kullanabileceğiniz toplam çekirdek sayısıyla ilgili sınırlamaya sahiptir. Gerekli clusterSize uyması için bu Azure bölgesinde yeterli çekirdek kotadığınızdan emin olun. Ayrıntılar için bkz. [HDInsight 'Ta Hadoop, Spark, Kafka ve daha fazlası ile küme ayarlama](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Şu anda depolama olarak bir Azure Data Lake Storage (Gen 2) kullanan isteğe bağlı bir HDInsight kümesi oluşturamazsınız. Bir Azure Data Lake Storage (Gen 2) HDInsight işlemeden sonuç verilerini depolamak istiyorsanız, verileri Azure Blob depolamadan Azure Data Lake Storage (Gen 2) kopyalamak için bir kopyalama etkinliği kullanın. </p> | Yes      |
| clusterResourceGroup         | HDInsight kümesi bu kaynak grubunda oluşturulur. | Yes      |
| TimeToLive                   | İsteğe bağlı HDInsight kümesi için izin verilen boşta kalma süresi. Kümede başka bir etkin iş yoksa, bir etkinlik çalıştırıldıktan sonra, isteğe bağlı HDInsight kümesinin ne kadar süreyle etkin kalacağını belirtir. İzin verilen en düşük değer 5 dakikadır (00:05:00).<br/><br/>Örneğin, bir etkinlik çalıştırması 6 dakika sürüyorsa ve TimeToLive, 5 dakika olarak ayarlanırsa, etkinlik çalıştırmasının sonunda 6 dakikadan sonra küme, 5 dakika boyunca etkin kalır. Başka bir etkinlik çalıştırması 6 dakikalık bir pencere ile yürütülürse aynı küme tarafından işlenir.<br/><br/>İsteğe bağlı HDInsight kümesi oluşturma işlemi pahalı bir işlemdir, bu nedenle isteğe bağlı HDInsight kümesini yeniden kullanarak bir veri fabrikasının performansını artırmak için bu ayarı gereken şekilde kullanın.<br/><br/>TimeToLive değerini 0 olarak ayarlarsanız, etkinlik çalıştırması tamamlandıktan hemen sonra küme silinir. Ancak, yüksek bir değer ayarlarsanız, bazı sorun giderme amacıyla oturum açmanız için küme boşta kalabilir, ancak bu durum yüksek maliyetlere neden olabilir. Bu nedenle, gereksinimlerinize göre uygun değeri ayarlamanız önemlidir.<br/><br/>TimeToLive özelliğinin değeri uygun şekilde ayarlandıysa, birden çok işlem hattı isteğe bağlı HDInsight kümesinin örneğini paylaşabilir. | Yes      |
| clusterType                  | Oluşturulacak HDInsight kümesinin türü. İzin verilen değerler "Hadoop" ve "Spark" değerleridir. Belirtilmemişse, varsayılan değer Hadoop ' dır. Kurumsal Güvenlik Paketi etkinleştirilmiş küme isteğe bağlı olarak oluşturulamaz, bunun yerine [var olan bir kümeyi kullanın/kendi işlem ortamınızı getirin](#azure-hdinsight-linked-service). | No       |
| sürüm                      | HDInsight kümesinin sürümü. Belirtilmemişse, geçerli HDInsight tanımlı varsayılan sürümü kullanılıyor. | No       |
| Hostsubscriptionıd           | HDInsight kümesi oluşturmak için kullanılan Azure abonelik KIMLIĞI. Belirtilmemişse, Azure oturum açma bağlamınızın abonelik KIMLIĞINI kullanır. | No       |
| clusterNamePrefix           | HDI küme adının ön eki, bir zaman damgası otomatik olarak küme adının sonuna ekler| No       |
| Mini sürüm                 | Küme türü "Spark" ise Spark sürümü | No       |
| additionalLinkedServiceNames | Data Factory hizmeti tarafından sizin adınıza kaydettirilebilmeleri için HDInsight bağlı hizmeti için ek depolama hesapları belirtir. Bu depolama hesaplarının, linkedServiceName tarafından belirtilen depolama hesabıyla aynı bölgede oluşturulan HDInsight kümesiyle aynı bölgede olması gerekir. | No       |
| osType                       | İşletim sisteminin türü. İzin verilen değerler şunlardır: Linux ve Windows (yalnızca HDInsight 3,3 için). Linux varsayılandır. | No       |
| hcatalogLinkedServiceName    | HCatalog veritabanına işaret eden Azure SQL bağlı hizmetinin adı. İsteğe bağlı HDInsight kümesi, Azure SQL veritabanı, meta veri deposu olarak kullanılarak oluşturulur. | No       |
| connectVia                   | Bu HDInsight bağlı hizmetine etkinlikleri göndermek için kullanılacak Integration Runtime. İsteğe bağlı HDInsight bağlı hizmeti yalnızca Azure Integration Runtime destekler. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | No       |
| clusterUserName                   | Kümeye erişmek için Kullanıcı adı. | No       |
| clusterPassword                   | Kümeye erişmek için güvenli dize türündeki parola. | No       |
| clusterSshUserName         | SSH için Kullanıcı adı kümenin düğümüne uzaktan bağlanır (Linux için). | No       |
| clusterSshPassword         | SSH 'ye yönelik güvenli dize türünde parolanın, kümenin düğümüne uzaktan bağlanmasını sağlama (Linux için). | No       |
| scriptActions | İsteğe bağlı küme oluşturma sırasında [HDInsight kümesi özelleştirmeleri](../hdinsight/hdinsight-hadoop-customize-cluster-linux.md) için betiği belirtin. <br />Şu anda Azure Data Factory Kullanıcı arabirimi yazma aracı yalnızca 1 betik eylemi belirtmeyi destekler, ancak JSON 'da bu sınırlamayı alabilir (JSON 'da birden çok betik eylemi belirtebilirsiniz). | No |


> [!IMPORTANT]
> HDInsight, dağıtılabilecek birden çok Hadoop küme sürümünü destekler. Her sürüm seçimi, Hortonçalışmaverisi platformu (HDP) dağıtımının belirli bir sürümünü ve bu dağıtım içinde yer alan bir bileşen kümesini oluşturur. Desteklenen HDInsight sürümlerinin listesi, en son Hadoop ekosistem bileşenlerini ve düzeltmelerini sağlamak üzere güncelleştiriliyor. HDInsight 'ın desteklenen bir sürümünü kullandığınızdan emin olmak için [desteklenen HDInsight sürümü ve işletim sistemi türünün](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) en güncel bilgilerine her zaman değindiğinizden emin olun. 
>
> [!IMPORTANT]
> Şu anda, HDInsight bağlı hizmetleri HBase, etkileşimli sorgu (Hive LLAP), fırtınası 'yi desteklemez. 

* additionalLinkedServiceNames JSON örneği

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

#### <a name="service-principal-authentication"></a>Hizmet sorumlusu kimlik doğrulaması

Isteğe bağlı HDInsight bağlı hizmeti, sizin adınıza HDInsight kümeleri oluşturmak için bir hizmet sorumlusu kimlik doğrulaması gerektirir. Hizmet sorumlusu kimlik doğrulamasını kullanmak için, bir uygulama varlığını Azure Active Directory (Azure AD) olarak kaydedin ve aboneliğin veya HDInsight kümesinin oluşturulduğu kaynak grubunun **katkıda** bulunan rolünü verin. Ayrıntılı adımlar için bkz. [kaynaklara erişebilen Azure Active Directory uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](../active-directory/develop/howto-create-service-principal-portal.md). Bağlı hizmeti tanımlamak için kullandığınız aşağıdaki değerleri unutmayın:

- Uygulama Kimliği
- Uygulama anahtarı 
- Kiracı Kimliği

Aşağıdaki özellikleri belirterek hizmet sorumlusu kimlik doğrulamasını kullanın:

| Özellik                | Açıklama                              | Gerekli |
| :---------------------- | :--------------------------------------- | :------- |
| **Serviceprincipalıd**  | Uygulamanın istemci KIMLIĞINI belirtin.     | Yes      |
| **Servicesprincipalkey** | Uygulamanın anahtarını belirtin.           | Yes      |
| **tenant**              | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. | Yes      |

#### <a name="advanced-properties"></a>Gelişmiş Özellikler

İsteğe bağlı HDInsight kümesinin ayrıntılı yapılandırması için aşağıdaki özellikleri de belirtebilirsiniz.

| Özellik               | Açıklama                              | Gerekli |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | HDInsight kümesinin oluşturulması için çekirdek yapılandırma parametrelerini (core-site.xml olarak) belirtir. | No       |
| hBaseConfiguration     | HDInsight kümesi için HBase yapılandırma parametrelerini (hbase-site.xml) belirtir. | No       |
| hdfsConfiguration      | HDInsight kümesi için bir yapılandırma parametreleri (hdfs-site.xml) belirtir. | No       |
| hiveConfiguration      | HDInsight kümesi için Hive yapılandırma parametrelerini (hive-site.xml) belirtir. | No       |
| mapReduceConfiguration | HDInsight kümesi için MapReduce yapılandırma parametrelerini (mapred-site.xml) belirtir. | No       |
| Oozıeconfiguration     | HDInsight kümesi için Oozie yapılandırma parametrelerini (oozie-site.xml) belirtir. | No       |
| stormConfiguration     | HDInsight kümesi için fırtınası yapılandırma parametrelerini (storm-site.xml) belirtir. | No       |
| yarnConfiguration      | HDInsight kümesi için Yarn yapılandırma parametrelerini (yarn-site.xml) belirtir. | No       |

* Örnek: gelişmiş özelliklerle isteğe bağlı HDInsight kümesi yapılandırması

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
            "coreConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "hiveConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "mapReduceConfiguration": {
                "mapreduce.reduce.java.opts": "-Xmx4000m",
                "mapreduce.map.java.opts": "-Xmx4000m",
                "mapreduce.map.memory.mb": "5000",
                "mapreduce.reduce.memory.mb": "5000",
                "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
            },
            "yarnConfiguration": {
                "yarn.app.mapreduce.am.resource.mb": "5000",
                "mapreduce.map.memory.mb": "5000"
            },
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

#### <a name="node-sizes"></a>Düğüm boyutları
Aşağıdaki özellikleri kullanarak baş, veri ve Zookeeper düğümleri için boyut belirtebilirsiniz: 

| Özellik          | Açıklama                              | Gerekli |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Baş düğümün boyutunu belirtir. Varsayılan değer: Standard_D3. Ayrıntılar için bkz. **düğüm boyutlarını belirtme** bölümü. | No       |
| Davtanodesize      | Veri düğümünün boyutunu belirtir. Varsayılan değer: Standard_D3. | No       |
| zookeeperNodeSize | Zoo Man düğümünün boyutunu belirtir. Varsayılan değer: Standard_D3. | No       |

* Düğüm boyutlarının belirtilmesi, önceki bölümde bahsedilen özellikler için belirtmeniz gereken dize değerleri için [sanal makine boyutları](../virtual-machines/sizes.md) makalesine bakın. Bu değerler, makalede başvurulan **API 'ler & cmdlet 'lerle** uyumlu olmalıdır. Makalede gördüğünüz gibi büyük (varsayılan) boyutun veri düğümü 7 GB belleğe sahiptir ve bu da senaryonuz için yeterince iyi olmayabilir. 

D4 boyutlu baş düğümleri ve çalışan düğümleri oluşturmak istiyorsanız, headNodeSize ve Davtanodesize özelliklerinin değeri olarak **Standard_D4** belirtin. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Bu özellikler için yanlış bir değer belirtirseniz, şu **hatayı alabilirsiniz:** küme oluşturulamadı. Özel durum: Küme oluşturma işlemi tamamlanamadı. İşlem '400' koduyla başarısız oldu. Küme geride bırakma durumu: 'Hata'. İleti: ' Prelustercreationvalidationfailure '. Bu hatayı aldığınızda, [sanal makinelerin boyutları](../virtual-machines/sizes.md) makalesindeki tabloda **CMDLET & API 'leri** adını kullandığınızdan emin olun.

### <a name="bring-your-own-compute-environment"></a>Kendi işlem ortamınızı getirin
Bu yapılandırmada, kullanıcılar zaten mevcut bir bilgi işlem ortamını Data Factory bağlı hizmet olarak kaydedebilir. Bilgi işlem ortamı Kullanıcı tarafından yönetilir ve Data Factory hizmeti tarafından etkinlikleri yürütmek için kullanılır.

Bu yapılandırma türü aşağıdaki işlem ortamları için desteklenir:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SYNAPSE Analytics, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight bağlı hizmeti
Kendi HDInsight kümenizi Data Factory kaydetmek için bir Azure HDInsight bağlı hizmeti oluşturabilirsiniz.

### <a name="example"></a>Örnek

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Özellikler
| Özellik          | Açıklama                                                  | Gerekli |
| ----------------- | ------------------------------------------------------------ | -------- |
| tür              | Type özelliği **HDInsight** olarak ayarlanmalıdır.            | Yes      |
| clusterUri        | HDInsight kümesinin URI 'SI.                            | Yes      |
| username          | Mevcut bir HDInsight kümesine bağlanmak için kullanılacak kullanıcının adını belirtin. | Yes      |
| password          | Kullanıcı hesabı için parola belirtin.                       | Yes      |
| linkedServiceName | HDInsight kümesi tarafından kullanılan Azure Blob depolama alanına başvuran Azure depolama bağlı hizmetinin adı. <p>Şu anda bu özellik için bir Azure Data Lake Storage (Gen 2) bağlı hizmeti belirtemezsiniz. HDInsight kümesinin Data Lake Store erişimi varsa, Hive/Pig betiklerinden Azure Data Lake Storage (Gen 2) verilerine erişebilirsiniz. </p> | Yes      |
| ıvspenabled      | HDInsight kümesi [Kurumsal güvenlik paketi](../hdinsight/domain-joined/apache-domain-joined-architecture.md) etkinse '*true ' değerini* belirtin. Varsayılan değer '*false*' şeklindedir. | No       |
| connectVia        | Bu bağlı hizmete etkinlikleri göndermek için kullanılacak Integration Runtime. Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. <br />Kurumsal Güvenlik Paketi (ESP) etkin HDInsight kümesi için, kümeye bir görüş satırı içeren, şirket içinde barındırılan bir tümleştirme çalışma zamanı kullanın veya ESP HDInsight kümesiyle aynı sanal ağ içinde dağıtılması gerekir. | No       |

> [!IMPORTANT]
> HDInsight, dağıtılabilecek birden çok Hadoop küme sürümünü destekler. Her sürüm seçimi, Hortonçalışmaverisi platformu (HDP) dağıtımının belirli bir sürümünü ve bu dağıtım içinde yer alan bir bileşen kümesini oluşturur. Desteklenen HDInsight sürümlerinin listesi, en son Hadoop ekosistem bileşenlerini ve düzeltmelerini sağlamak üzere güncelleştiriliyor. HDInsight 'ın desteklenen bir sürümünü kullandığınızdan emin olmak için [desteklenen HDInsight sürümü ve işletim sistemi türünün](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) en güncel bilgilerine her zaman değindiğinizden emin olun. 
>
> [!IMPORTANT]
> Şu anda, HDInsight bağlı hizmetleri HBase, etkileşimli sorgu (Hive LLAP), fırtınası 'yi desteklemez. 
>
> 

## <a name="azure-batch-linked-service"></a>Bağlı hizmet Azure Batch

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bir veri fabrikasına sanal makinelerin (VM) Batch havuzunu kaydetmek için Azure Batch bağlantılı bir hizmet oluşturabilirsiniz. Azure Batch kullanarak özel etkinlik çalıştırabilirsiniz.

Azure Batch Service ' i yeni biliyorsanız aşağıdaki makalelere bakın:

* Azure Batch hizmetine genel bakış hakkında [temel bilgiler Azure Batch](../batch/batch-technical-overview.md) .
* Azure Batch bir hesap oluşturmak için [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) cmdlet 'i, Azure portal kullanarak Azure Batch hesabı oluşturmak için [Azure Portal](../batch/batch-account-create-portal.md) . Cmdlet 'ini kullanma hakkında ayrıntılı yönergeler için [Azure Batch hesabını yönetmek üzere PowerShell kullanma](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account) makalesine bakın.
* Azure Batch havuzu oluşturmak için [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) cmdlet 'i.

> [!IMPORTANT]
> Yeni bir Azure Batch havuzu oluştururken ' Virtualmakineconfiguration ' kullanılmalıdır ve ' CloudServiceConfiguration ' DEĞIL. Daha fazla ayrıntı için [Azure Batch havuz geçiş kılavuzuna](../batch/batch-pool-cloud-service-to-virtual-machine-configuration.md)bakın. 

### <a name="example"></a>Örnek

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Özellikler
| Özellik          | Açıklama                              | Gerekli |
| ----------------- | ---------------------------------------- | -------- |
| tür              | Type özelliği **AzureBatch** olarak ayarlanmalıdır. | Yes      |
| accountName       | Azure Batch hesabının adı.         | Yes      |
| accessKey         | Azure Batch hesabı için erişim anahtarı.  | Yes      |
| batchUri          | Azure Batch hesabınızın URL 'SI, https://*batchaccountname. Region*. Batch.Azure.com biçiminde. | Yes      |
| poolName          | Sanal makine havuzunun adı.    | Yes      |
| linkedServiceName | Bu Azure Batch bağlı hizmetiyle ilişkili Azure depolama bağlı hizmetinin adı. Bu bağlı hizmet, etkinliği çalıştırmak için gereken hazırlama dosyaları için kullanılır. | Yes      |
| connectVia        | Bu bağlı hizmete etkinlikleri göndermek için kullanılacak Integration Runtime. Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | No       |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Azure Machine Learning Studio (klasik) bağlı hizmet
Bir Machine Learning Studio (klasik) toplu Puanlama uç noktasını bir veri fabrikasına kaydetmek için Azure Machine Learning Studio (klasik) bağlı bir hizmet oluşturursunuz.

### <a name="example"></a>Örnek

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Özellikler
| Özellik               | Açıklama                              | Gerekli                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Tür                   | Type özelliği: **AzureML** olarak ayarlanmalıdır. | Yes                                      |
| mlEndpoint             | Toplu işlem Puanlama URL 'SI.                   | Yes                                      |
| apiKey                 | Yayımlanan çalışma alanı modelinin API 'SI.     | Yes                                      |
| updateResourceEndpoint | Tahmine dayalı Web hizmetini eğitilen model dosyasıyla güncelleştirmek için kullanılan Azure Machine Learning Studio (klasik) Web hizmeti uç noktası için kaynak güncelleştirme URL 'SI | No                                       |
| Serviceprincipalıd     | Uygulamanın istemci KIMLIĞINI belirtin.     | UpdateResourceEndpoint belirtilmişse gereklidir |
| Servicesprincipalkey    | Uygulamanın anahtarını belirtin.           | UpdateResourceEndpoint belirtilmişse gereklidir |
| Kiracı                 | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. | UpdateResourceEndpoint belirtilmişse gereklidir |
| connectVia             | Bu bağlı hizmete etkinlikleri göndermek için kullanılacak Integration Runtime. Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | No                                       |

## <a name="azure-machine-learning-linked-service"></a>Bağlı hizmet Azure Machine Learning
Bir Azure Machine Learning çalışma alanını veri fabrikasına bağlamak için Azure Machine Learning bağlı bir hizmet oluşturursunuz.

> [!NOTE]
> Şu anda yalnızca hizmet sorumlusu kimlik doğrulaması Azure Machine Learning bağlı hizmeti için desteklenir.

### <a name="example"></a>Örnek

```json
{
    "name": "AzureMLServiceLinkedService",
    "properties": {
        "type": "AzureMLService",
        "typeProperties": {
            "subscriptionId": "subscriptionId",
            "resourceGroupName": "resourceGroupName",
            "mlWorkspaceName": "mlWorkspaceName",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime?",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Özellikler

| Özellik               | Açıklama                              | Gerekli                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Tür                   | Type özelliği: **AzureMLService** olarak ayarlanmalıdır. | Yes                                      |
| subscriptionId         | Azure abonelik KIMLIĞI              | Yes                                      |
| resourceGroupName      | name | Yes                                      |
| Mlçalışmaalanıadı        | Azure Machine Learning çalışma alanı adı | Yes  |
| Serviceprincipalıd     | Uygulamanın istemci KIMLIĞINI belirtin.     | Yes |
| Servicesprincipalkey    | Uygulamanın anahtarını belirtin.           | Yes |
| Kiracı                 | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. | UpdateResourceEndpoint belirtilmişse gereklidir |
| connectVia             | Bu bağlı hizmete etkinlikleri göndermek için kullanılacak Integration Runtime. Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | No |

## <a name="azure-data-lake-analytics-linked-service"></a>Bağlı hizmet Azure Data Lake Analytics
Bir Azure Data Lake Analytics işlem hizmetini bir Azure Data Factory 'ye bağlamak için **Azure Data Lake Analytics** bağlı bir hizmet oluşturursunuz. İşlem hattındaki Data Lake Analytics U-SQL etkinliği, bu bağlı hizmeti ifade eder. 

### <a name="example"></a>Örnek

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription ID of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Özellikler

| Özellik             | Açıklama                              | Gerekli                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| tür                 | Type özelliği: **AzureDataLakeAnalytics** olarak ayarlanmalıdır. | Yes                                      |
| accountName          | Azure Data Lake Analytics hesap adı.  | Yes                                      |
| Datalakeanaliz Ticsurı | Azure Data Lake Analytics URI 'SI.           | No                                       |
| subscriptionId       | Azure abonelik KIMLIĞI                    | No                                       |
| resourceGroupName    | Azure kaynak grubu adı                | No                                       |
| Serviceprincipalıd   | Uygulamanın istemci KIMLIĞINI belirtin.     | Yes                                      |
| Servicesprincipalkey  | Uygulamanın anahtarını belirtin.           | Yes                                      |
| Kiracı               | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. | Yes                                      |
| connectVia           | Bu bağlı hizmete etkinlikleri göndermek için kullanılacak Integration Runtime. Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | No                                       |



## <a name="azure-databricks-linked-service"></a>Bağlı hizmet Azure Databricks
Databricks iş yüklerini (Not defteri, Jar, Python) çalıştırmak için kullandığınız Databricks çalışma alanını kaydettirmek üzere **Azure Databricks bağlantılı hizmet** oluşturabilirsiniz. 
> [!IMPORTANT]
> Databricks bağlı hizmetleri, sistem tarafından atanan yönetilen kimlik doğrulama & [örnek havuzlarını](https://aka.ms/instance-pools) destekler.

### <a name="example---using-new-job-cluster-in-databricks"></a>Örnek-Databricks 'te yeni iş kümesi kullanma

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Örnek-Databricks 'te mevcut etkileşimli kümeyi kullanma

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>Özellikler

| Özellik             | Açıklama                              | Gerekli                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| name                 | Bağlı hizmetin adı               | Yes   |
| tür                 | Type özelliği: **Azure Databricks** olarak ayarlanmalıdır. | Yes                                      |
| etki alanı               | Databricks çalışma alanının bölgesine göre uygun şekilde Azure bölgesini belirtin. Örnek: https://eastus.azuredatabricks.net | Yes                                 |
| accessToken          | Azure Databricks kimlik doğrulaması için Data Factory erişim belirteci gereklidir. Erişim belirtecinin databricks çalışma alanından oluşturulması gerekir. Erişim belirtecini bulmak için daha ayrıntılı adımlar [burada](/azure/databricks/dev-tools/api/latest/authentication#generate-token) bulunabilir  | No                                       |
| MSI          | Azure Databricks kimlik doğrulamak için Data Factory yönetilen kimliğini (sistem tarafından atanan) kullanın. ' MSI ' kimlik doğrulaması kullanılırken erişim belirtecine ihtiyacınız yoktur  | No                                       |
| Existingclusterıd    | Bu kümedeki tüm işleri çalıştırmak için var olan bir kümenin küme KIMLIĞI. Bu, önceden oluşturulmuş bir etkileşimli küme olmalıdır. Yanıt vermeyi durdurduğunda kümeyi el ile yeniden başlatmanız gerekebilir. Databricks daha fazla güvenilirlik sağlamak için işleri yeni kümeler üzerinde çalıştırmaya öneriyor. Databricks çalışma alanı-> kümeleri-> etkileşimli küme adı-> yapılandırma-> etiketleri üzerinde etkileşimli bir kümenin küme KIMLIĞINI bulabilirsiniz. [Daha fazla ayrıntı](https://docs.databricks.com/user-guide/clusters/tags.html) | No 
| instancePoolId    | Databricks çalışma alanında var olan bir havuzun örnek havuzu KIMLIĞI.  | No  |
| newClusterVersion    | Kümenin Spark sürümü. Databricks içinde bir iş kümesi oluşturur. | No  |
| newClusterNumOfWorker| Bu kümenin sahip olması gereken çalışan düğüm sayısı. Bir kümede, toplam num_workers + 1 Spark düğümü için bir Spark sürücüsü ve num_workers Yürüticileri vardır. "1" gibi bir Int32 dizesi, numOfWorker 'ın 1 veya "1:10" olması anlamına gelir.  | No                |
| newClusterNodeType   | Bu alan tek bir değer ile, bu kümedeki Spark düğümlerinin her biri için kullanılabilir kaynakları kodluyor. Örneğin, Spark düğümleri bellek veya işlem yoğunluğu yoğun iş yükleri için sağlanabilir ve iyileştirilebilir. Bu alan yeni küme için gereklidir                | No               |
| Newclusterparlak conf  | isteğe bağlı, Kullanıcı tarafından belirtilen Spark yapılandırması anahtar-değer çiftleri kümesi. Kullanıcılar ayrıca, sürücü ve yürüticilere, sırasıyla spark. Driver. extraJavaOptions ve spark.executor. extraJavaOptions aracılığıyla ek JVM seçeneklerinin bir dizesini de geçirebilir. | No  |
| Newclusterınitscripts| Yeni küme için isteğe bağlı, Kullanıcı tanımlı başlatma betikleri kümesi. İnit betikleri için DBFS yolunu belirtme. | No  |


## <a name="azure-sql-database-linked-service"></a>Azure SQL Veritabanı bağlı hizmeti

Bir Azure SQL bağlı hizmeti oluşturur ve bir Data Factory işlem hattından saklı yordam çağırmak için [saklı yordam etkinliğiyle](transform-data-using-stored-procedure.md) birlikte kullanırsınız. Bu bağlı hizmet hakkındaki ayrıntılar için bkz. [Azure SQL Bağlayıcısı](connector-azure-sql-database.md#linked-service-properties) makalesi.

## <a name="azure-synapse-analytics-linked-service"></a>Azure SYNAPSE Analytics bağlı hizmeti

Bir Azure SYNAPSE Analytics bağlı hizmeti oluşturun ve bir Data Factory işlem hattından saklı yordam çağırmak için [saklı yordam etkinliğiyle](transform-data-using-stored-procedure.md) birlikte kullanabilirsiniz. Bu bağlı hizmet hakkındaki ayrıntılar için bkz. [Azure SYNAPSE Analytics Bağlayıcısı](connector-azure-sql-data-warehouse.md#linked-service-properties) makalesi.

## <a name="sql-server-linked-service"></a>Bağlı hizmet SQL Server

SQL Server bağlı bir hizmet oluşturur ve bir Data Factory işlem hattından saklı yordam çağırmak için [saklı yordam etkinliğiyle](transform-data-using-stored-procedure.md) birlikte kullanırsınız. Bu bağlı hizmet hakkındaki ayrıntılar için bkz. [SQL Server Bağlayıcısı](connector-sql-server.md#linked-service-properties) makalesi.

## <a name="azure-function-linked-service"></a>Azure Işlevi bağlı hizmeti

Azure Işlev bağlı hizmeti oluşturup Azure Işlevleri 'ni bir Data Factory işlem hattında çalıştırmak için [Azure işlevi etkinliğiyle](control-flow-azure-function-activity.md) birlikte kullanabilirsiniz. Azure işlevinin dönüş türü geçerli bir olmalıdır `JObject` . ( [Jarray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) öğesinin bir *değil* olduğunu aklınızda bulundurun `JObject` .) Diğer herhangi bir dönüş türü `JObject` başarısız olur ve Kullanıcı hata *yanıtı içeriğini başlatır geçerli bir JObject değildir*.

| **Özellik** | **Açıklama** | **Gerekli** |
| --- | --- | --- |
| tür   | Type özelliği: **AzureFunction** olarak ayarlanmalıdır | evet |
| işlev uygulaması URL 'si | Azure İşlev Uygulaması URL 'SI. Biçim `https://<accountname>.azurewebsites.net` . Bu URL, Azure portal İşlev Uygulaması görüntülenirken **URL** bölümündeki değerdir  | evet |
| işlev anahtarı | Azure Işlevi için erişim anahtarı. İlgili işlevin **Yönet** bölümüne tıklayın ve **işlev anahtarını** ya da **ana bilgisayar anahtarını** kopyalayın. Buradan daha fazla bilgi edinebilirsiniz: [Azure IŞLEVLERI http Tetikleyicileri ve bağlamaları](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | evet |
|   |   |   |

## <a name="next-steps"></a>Sonraki adımlar

Azure Data Factory tarafından desteklenen dönüştürme etkinliklerinin listesi için bkz. [verileri dönüştürme](transform-data.md).
