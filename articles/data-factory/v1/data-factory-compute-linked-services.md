---
title: Azure Data Factory sürüm 1 tarafından desteklenen işlem ortamları
description: Verileri dönüştürmek veya işlemek için Azure Data Factory işlem hatları (Azure HDInsight gibi) içinde kullanabileceğiniz işlem ortamları hakkında bilgi edinin.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 5f6fbcb73b4139c0a80ea8352071d8683c401d6e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782954"
---
# <a name="compute-environments-supported-by-azure-data-factory-version-1"></a>Azure Data Factory sürüm 1 tarafından desteklenen işlem ortamları
> [!NOTE]
> Bu makale, Azure Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [işlem bağlantılı Hizmetleri içinde](../compute-linked-services.md).

Bu makalede, verileri işlemek veya dönüştürmek için kullanabileceğiniz işlem ortamları açıklanmaktadır. Ayrıca, bu işlem ortamlarını bir Azure Data Factory 'ye bağlayan bağlı hizmetleri yapılandırırken Data Factory desteklediği farklı yapılandırmalara (isteğe bağlı ve kendi kendinize karşı) ilişkin ayrıntılar da sağlar.

Aşağıdaki tabloda, Data Factory tarafından desteklenen işlem ortamlarının listesi ve bunlar üzerinde çalışabilecek etkinlikler verilmiştir. 

| İşlem ortamı                      | Etkinlikler                               |
| ---------------------------------------- | ---------------------------------------- |
| [İsteğe bağlı Azure HDInsight kümesi](#azure-hdinsight-on-demand-linked-service) veya [kendi HDInsight kümeniz](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop akışı](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [Olmalı](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (klasik)](#azure-machine-learning-studio-classic-linked-service) | [Studio (klasik) etkinlikleri: toplu yürütme ve kaynak güncelleştirme](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure Synapse Analytics](#azure-synapse-analytics-linked-service), [SQL Server](#sql-server-linked-service) | [Saklı Yordam Etkinliği](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>Data Factory desteklenen HDInsight sürümleri
Azure HDInsight, dilediğiniz zaman dağıtabileceğiniz birden çok Hadoop küme sürümünü destekler. Desteklenen her sürüm, Hortonçalışmaveri platformu (HDP) dağıtımının belirli bir sürümünü ve dağıtım içindeki bir bileşen kümesini oluşturur. 

Microsoft, desteklenen HDInsight sürümlerinin listesini en son Hadoop ekosistemi bileşenleri ve düzeltmeleri ile güncelleştirir. Ayrıntılı bilgi için bkz. [desteklenen HDInsight sürümleri](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Linux tabanlı HDInsight sürüm 3,3, 31 Temmuz 2017 tarihinde kullanımdan kaldırıldı. Data Factory sürüm 1 isteğe bağlı HDInsight bağlı Hizmetleri müşterileri, HDInsight 'ın sonraki bir sürümünü test etmek ve yükseltmek için 15 Aralık 2017 ' e kadar verilirler. Windows tabanlı HDInsight 31 Temmuz 2018 tarihinde kullanımdan kaldırılacaktır.
>
> 

### <a name="after-the-retirement-date"></a>Kullanımdan kaldırma tarihinden sonra 

15 Aralık 2017 ' den sonra:

- Artık Data Factory sürüm 1 ' de isteğe bağlı bir HDInsight bağlı hizmetini kullanarak Linux tabanlı HDInsight sürüm 3,3 (veya önceki sürümler) kümeleri oluşturamazsınız. 
- [ **OSType** ve **Sürüm** özellikleri](#azure-hdinsight-on-demand-linked-service) , mevcut bir Data Factory 1. sürüm 1 Isteğe bağlı HDInsight bağlantılı hizmetine yönelik JSON tanımında açıkça belirtilmemişse, varsayılan değer **sürüm = 3.1, OSType = Windows** to **Version = \<latest HDI default version\> ( https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) , OSType = Linux**) olarak değişir.

31 Temmuz 2018 ' den sonra:

- Data Factory sürüm 1 ' deki isteğe bağlı HDInsight bağlı hizmetini kullanarak artık Windows tabanlı HDInsight kümelerinin herhangi bir sürümünü oluşturamazsınız. 

### <a name="recommended-actions"></a>Önerilen Eylemler

- En son Hadoop ekosistemi bileşenlerini ve düzeltmeleri kullanabilmeniz için, etkilenen Data Factory 1. sürüm, isteğe bağlı HDInsight bağlı hizmet tanımlarındaki [ **OSType** ve **Sürüm** özelliklerini](#azure-hdinsight-on-demand-linked-service) daha yeni Linux tabanlı HDInsight sürümlerine (HDInsight 3,6) güncelleştirin. 
- 15 Aralık 2017 ' den önce, test Data Factory sürüm 1 Hive, Pig, MapReduce ve etkilenen bağlı hizmete başvuran Hadoop akış etkinlikleri. Yükseltmekte olduğunuz yeni **OSType** ve **Sürüm** varsayılan değerleri (**Version = 3.6**, **OSType = Linux**) veya açık HDInsight sürümü ve işletim sistemi türü ile uyumlu olduklarından emin olun. 
  Uyumluluk hakkında daha fazla bilgi edinmek için bkz. [Windows tabanlı HDInsight kümesinden Linux tabanlı bir kümeye geçirme](../../hdinsight/index.yml) ve [HDInsight ile kullanılabilen Hadoop bileşenleri ve sürümleri nelerdir?](../../hdinsight/hdinsight-component-versioning.md). 
- Windows tabanlı HDInsight kümeleri oluşturmak için Data Factory sürüm 1 isteğe bağlı HDInsight bağlı hizmetini kullanmaya devam etmek için, **OSType** 'Yi 15 Aralık 2017 ' dan önce açık olarak **Windows** 'a ayarlayın. Linux tabanlı HDInsight kümelerine 31 Temmuz 2018 tarihinden önce geçiş yapmanızı öneririz. 
- Data Factory sürüm 1 DotNet özel etkinliğini yürütmek için isteğe bağlı bir HDInsight bağlı hizmeti kullanıyorsanız, DotNet özel etkinlik JSON tanımını bunun yerine Azure Batch bağlı bir hizmet kullanacak şekilde güncelleştirin. Daha fazla bilgi için bkz. Data Factory işlem hattında [özel etkinlikler kullanma](./data-factory-use-custom-activities.md). 

> [!Note]
> Mevcut, kendi sahip olduğunuz küme HDInsight bağlı cihazınızı Data Factory sürüm 1 ' de veya kendi kendinize getir ve isteğe bağlı HDInsight bağlı hizmeti Azure Data Factory ' de kullanırsanız, hiçbir işlem yapmanız gerekmez. Bu senaryolarda, HDInsight kümelerinin en son sürüm destek ilkesi zaten zorlandı. 
>
> 


## <a name="on-demand-compute-environment"></a>İsteğe bağlı işlem ortamı
İsteğe bağlı bir yapılandırmada, Data Factory işlem ortamını tam olarak yönetir. Data Factory, verileri işlemek için bir iş gönderilmeden önce işlem ortamını otomatik olarak oluşturur. İş bittiğinde Data Factory işlem ortamını kaldırır. 

İsteğe bağlı bir işlem ortamı için bağlı bir hizmet oluşturabilirsiniz. İşlem ortamını yapılandırmak ve iş yürütmeye, küme yönetimine ve önyükleme eylemlerine yönelik ayrıntılı ayarları denetlemek için bağlı hizmeti kullanın.

> [!NOTE]
> Şu anda isteğe bağlı yapılandırma yalnızca HDInsight kümeleri için desteklenir.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>İsteğe bağlı Azure HDInsight bağlı hizmeti
Data Factory, verileri işlemek için otomatik olarak Windows tabanlı veya Linux tabanlı bir HDInsight kümesi oluşturabilir. Küme, kümeyle ilişkili depolama hesabıyla aynı bölgede oluşturulur. Kümeyi oluşturmak için JSON **Linkedservicename** özelliğini kullanın.

İsteğe bağlı HDInsight bağlı hizmeti hakkında aşağıdaki *ana* noktalara göz önünde edin:

* İsteğe bağlı HDInsight kümesi Azure aboneliğinizde görünmüyor. Data Factory hizmeti, isteğe bağlı HDInsight kümesini sizin adınıza yönetir.
* İsteğe bağlı HDInsight kümesinde çalıştırılan işlerin günlükleri, HDInsight kümesiyle ilişkili depolama hesabına kopyalanır. Bu günlüklere erişmek için Azure portal, **etkinlik çalıştırma ayrıntıları** bölmesine gidin. Daha fazla bilgi için bkz. işlem [hatlarını izleme ve yönetme](data-factory-monitor-manage-pipelines.md).
* Yalnızca HDInsight kümesinin çalışır duruma geçen süre için ücretlendirilirsiniz.

> [!IMPORTANT]
> İsteğe bağlı HDInsight kümesi sağlamak için genellikle *20 dakika* veya daha fazla sürer.
>
> 

### <a name="example"></a>Örnek
Aşağıdaki JSON, Linux tabanlı isteğe bağlı HDInsight bağlı hizmetini tanımlar. Data Factory, bir veri dilimini işlediğinde otomatik olarak *Linux tabanlı* HDInsight kümesi oluşturur. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> HDInsight kümesi, JSON **Linkedservicename** özelliğinde belirttiğiniz Azure Blob depolamada *varsayılan bir kapsayıcı* oluşturur. Tasarım ile, HDInsight, küme silindiğinde bu kapsayıcıyı silmez. İsteğe bağlı bir HDInsight bağlı hizmetinde, var olan bir canlı küme (**TimeToLive**) olmadıkça bir dilim her işlenmek üzere her işlendiği her seferinde bir HDInsight kümesi oluşturulur. İşlem tamamlandığında küme silinir. 
>
> Daha fazla dilim işlendikçe, blob depolamada birçok kapsayıcı görürsünüz. İş sorunlarını gidermeye yönelik kapsayıcılara ihtiyacınız yoksa, depolama maliyetini azaltmak için kapsayıcıları silmek isteyebilirsiniz. Bu kapsayıcı adları bir düzene sahiptir: `adf<your Data Factory name>-<linked service name>-<date and time>`. Blob depolamada kapsayıcıları silmek için [Microsoft Azure Depolama Gezgini](https://storageexplorer.com/) gibi bir araç kullanabilirsiniz.
>
> 

### <a name="properties"></a>Özellikler
| Özellik                     | Açıklama                              | Gerekli |
| ---------------------------- | ---------------------------------------- | -------- |
| tür                         | Type özelliğini **hdınsightondemand** olarak ayarlayın. | Yes      |
| clusterSize                  | Kümedeki çalışan ve veri düğümlerinin sayısı. HDInsight kümesi, bu özellik için belirttiğiniz çalışan düğümü sayısına ek olarak 2 baş düğüm ile oluşturulur. Düğümler, 4 çekirdek içeren Standard_D3 boyutudur. 4 çalışan düğüm kümesi 24 çekirdek alır ( \* çalışan düğümleri için 4 4 = 16 çekirdek, ve \* baş düğümler için 2 4 = 8 çekirdek). Standard_D3 katmanı hakkında daha fazla bilgi için bkz. [HDInsight 'Ta Linux tabanlı Hadoop kümeleri oluşturma](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Yes      |
| timeToLive                   | İsteğe bağlı HDInsight kümesi için izin verilen boşta kalma süresi. Kümede başka etkin iş yoksa, bir etkinlik çalıştırması bittiğinde isteğe bağlı HDInsight kümesinin ne kadar süreyle etkin kalacağını belirtir.<br /><br />Örneğin, bir etkinlik çalıştırması 6 dakika sürüyorsa ve **TimeToLive** , 5 dakika olarak ayarlanırsa, etkinlik çalıştırmasının sonunda 6 dakikadan sonra küme, 5 dakika boyunca etkin kalır. 6 dakikalık bir pencerede başka bir etkinlik çalıştırması yürütülürse aynı küme tarafından işlenir.<br /><br />İsteğe bağlı HDInsight kümesi oluşturmak pahalı bir işlemdir (biraz zaman alabilir). İsteğe bağlı HDInsight kümesini yeniden kullanarak bir veri fabrikasının performansını artırmak için bu ayarı gereken şekilde kullanın.<br /><br />**TimeToLive** değerini **0** olarak ayarlarsanız, etkinlik çalıştırması tamamlandıktan hemen sonra küme silinir. Ancak, yüksek bir değer ayarlarsanız küme boşta kalabilir ve gereksiz bir şekilde yüksek maliyetlerle sonuçlanır. Gereksinimlerinize göre uygun değeri ayarlamanız önemlidir.<br /><br />**TimeToLive** değeri uygun şekilde ayarlanmışsa, Isteğe bağlı HDInsight kümesinin örneğini birden çok işlem hattı paylaşabilir. | Yes      |
| sürüm                      | HDInsight kümesinin sürümü. İzin verilen HDInsight sürümleri için bkz. [desteklenen HDInsight sürümleri](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions). Bu değer belirtilmezse, [en son HDI varsayılan sürümü](../../hdinsight/hdinsight-component-versioning.md) kullanılır. | No       |
| linkedServiceName            | Verileri depolamak ve işlemek için isteğe bağlı küme tarafından kullanılacak Azure depolama bağlı hizmeti. HDInsight kümesi bu depolama hesabı ile aynı bölgede oluşturulur.<p>Şu anda depolama olarak Azure Data Lake Store kullanan isteğe bağlı bir HDInsight kümesi oluşturamazsınız. Data Lake Store 'de HDInsight işlemeden elde edilen sonuç verilerini depolamak istiyorsanız, verileri blob depolamadan Data Lake Store kopyalamak için kopyalama etkinliğini kullanın. </p> | Yes      |
| additionalLinkedServiceNames | HDInsight bağlı hizmeti için ek depolama hesapları belirtir. Data Factory depolama hesaplarını sizin yerinize kaydeder. Bu depolama hesaplarının, HDInsight kümesiyle aynı bölgede olması gerekir. HDInsight kümesi, **Linkedservicename** özelliği tarafından belirtilen depolama hesabıyla aynı bölgede oluşturulur. | No       |
| osType                       | İşletim sisteminin türü. İzin verilen değerler **Linux** ve **Windows**. Bu değer belirtilmemişse, **Linux** kullanılır.  <br /><br />Linux tabanlı HDInsight kümelerinin kullanılması önemle önerilir. Windows 'da HDInsight için emeklilik tarihi 31 Temmuz 2018 ' dir. | No       |
| hcatalogLinkedServiceName    | HCatalog veritabanına işaret eden Azure SQL bağlı hizmetinin adı. İsteğe bağlı HDInsight kümesi, SQL veritabanı, meta veri deposu olarak kullanılarak oluşturulur. | No       |

#### <a name="example-linkedservicenames-json"></a>Örnek: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Gelişmiş Özellikler
İsteğe bağlı HDInsight kümesinin ayrıntılı yapılandırması için aşağıdaki özellikleri belirtebilirsiniz:

| Özellik               | Açıklama                              | Gerekli |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Oluşturulacak HDInsight kümesi için çekirdek yapılandırma parametrelerini (core-site.xml) belirtir. | No       |
| hBaseConfiguration     | HDInsight kümesi için HBase yapılandırma parametrelerini (hbase-site.xml) belirtir. | No       |
| hdfsConfiguration      | HDInsight kümesi için bir yapılandırma parametreleri (hdfs-site.xml) belirtir. | No       |
| hiveConfiguration      | HDInsight kümesi için Hive yapılandırma parametrelerini (hive-site.xml) belirtir. | No       |
| mapReduceConfiguration | HDInsight kümesi için MapReduce yapılandırma parametrelerini (mapred-site.xml) belirtir. | No       |
| Oozıeconfiguration     | HDInsight kümesi için Oozie yapılandırma parametrelerini (oozie-site.xml) belirtir. | No       |
| stormConfiguration     | HDInsight kümesi için fırtınası yapılandırma parametrelerini (storm-site.xml) belirtir. | No       |
| yarnConfiguration      | HDInsight kümesi için YARN yapılandırma parametrelerini (yarn-site.xml) belirtir. | No       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Örnek: gelişmiş özelliklerle isteğe bağlı HDInsight kümesi yapılandırması

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
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
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Düğüm boyutları
Baş, veri ve ZooKeeper düğümlerinin boyutunu belirtmek için aşağıdaki özellikleri kullanın: 

| Özellik          | Açıklama                              | Gerekli |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Baş düğümün boyutunu ayarlar. Varsayılan değer **Standard_D3**. Ayrıntılar için bkz. [düğüm boyutlarını belirtme](#specify-node-sizes). | No       |
| Davtanodesize      | Veri düğümünün boyutunu ayarlar. Varsayılan değer **Standard_D3**. | No       |
| zookeeperNodeSize | ZooKeeper düğümünün boyutunu ayarlar. Varsayılan değer **Standard_D3**. | No       |

#### <a name="specify-node-sizes"></a>Düğüm boyutlarını belirtin
Önceki bölümde açıklanan özellikler için belirtmeniz gereken dize değerleri için bkz. [sanal makine boyutları](../../virtual-machines/sizes.md). Değerler, [sanal makine boyutlarında](../../virtual-machines/sizes.md)başvurulan cmdlet 'Ler ve API 'lerle uyumlu olmalıdır. Büyük (varsayılan) veri düğümü boyutunun 7 GB belleği vardır. Bu senaryo için yeterli olmayabilir. 

D4-size baş düğümleri ve çalışan düğümleri oluşturmak istiyorsanız, **Headnodesize** ve **Dadtanodesize** özelliklerinin değeri olarak **Standard_D4** belirtin: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Bu özellikler için yanlış bir değer ayarlarsanız aşağıdaki iletiyi görebilirsiniz:

  Küme oluşturulamadı. Özel durum: Küme oluşturma işlemi tamamlanamadı. İşlem '400' koduyla başarısız oldu. Küme geride bırakma durumu: 'Hata'. İleti: ' Prelustercreationvalidationfailure '. 
  
Bu iletiyi görürseniz, [sanal makine boyutlarında](../../virtual-machines/sizes.md)tablodaki CMDLET ve API adlarını kullandığınızdan emin olun.  

> [!NOTE]
> Şu anda, Data Factory birincil depo olarak Data Lake Store kullanan HDInsight kümelerini desteklemez. HDInsight kümelerinin birincil deposu olarak Azure Storage 'ı kullanın. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Kendi işlem ortamınızı getir
Mevcut bir işlem ortamını Data Factory bağlı hizmet olarak kaydedebilirsiniz. İşlem ortamını yönetirsiniz. Data Factory hizmeti, etkinlikleri yürütmek için işlem ortamını kullanır.

Bu yapılandırma türü aşağıdaki işlem ortamları için desteklenir:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning Studio (klasik)
* Azure Data Lake Analytics
* Azure SQL veritabanı, Azure SYNAPSE Analytics, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight bağlı hizmeti
Kendi HDInsight kümenizi Data Factory kaydetmek için bir HDInsight bağlı hizmeti oluşturabilirsiniz.

### <a name="example"></a>Örnek

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Özellikler
| Özellik          | Açıklama                              | Gerekli |
| ----------------- | ---------------------------------------- | -------- |
| tür              | Type özelliğini **HDInsight** olarak ayarlayın. | Yes      |
| clusterUri        | HDInsight kümesinin URI 'SI.        | Yes      |
| username          | Mevcut bir HDInsight kümesine bağlanmak için kullanılacak kullanıcı hesabının adı. | Yes      |
| password          | Kullanıcı hesabının parolası.   | Yes      |
| linkedServiceName | HDInsight kümesi tarafından kullanılan BLOB depolama alanına başvuran depolama bağlı hizmetinin adı. <p>Şu anda bu özellik için Data Lake Store bağlı bir hizmet belirtemezsiniz. HDInsight kümesinin Data Lake Store erişimi varsa Hive veya Pig betiklerinden Data Lake Store verilere erişebilirsiniz. </p> | Yes      |

## <a name="azure-batch-linked-service"></a>Bağlı hizmet Azure Batch
Bir veri fabrikasına sanal makinelerin (VM 'Ler) Batch havuzunu kaydetmek için bir Batch bağlantılı hizmeti oluşturabilirsiniz. Batch veya HDInsight kullanarak Microsoft .NET özel etkinlikler çalıştırabilirsiniz.

Batch hizmetini kullanmaya yeni çalışıyorsanız:

* [Azure Batch temel bilgileri](../../azure-sql/database/sql-database-paas-overview.md)hakkında bilgi edinin.
* [New-AzureBatchAccount](/previous-versions/azure/mt125880(v=azure.100)) cmdlet 'i hakkında bilgi edinin. Batch hesabı oluşturmak için bu cmdlet 'i kullanın. Ya da, Batch hesabını [Azure Portal](../../batch/batch-account-create-portal.md)kullanarak oluşturabilirsiniz. Cmdlet 'ini kullanma hakkında ayrıntılı bilgi için bkz. [PowerShell kullanarak Batch hesabını yönetme](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account).
* [New-AzureBatchPool](/previous-versions/azure/mt125936(v=azure.100)) cmdlet 'i hakkında bilgi edinin. Batch havuzu oluşturmak için bu cmdlet 'i kullanın.

### <a name="example"></a>Örnek

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

**AccountName** özelliği için, ekleyin **. \<region name\>** Batch hesabınızın adına. Örnek:

```json
"accountName": "mybatchaccount.eastus"
```

Diğer bir seçenek de **Batchuri** uç noktası sağlamaktır. Örnek:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Özellikler
| Özellik          | Açıklama                              | Gerekli |
| ----------------- | ---------------------------------------- | -------- |
| tür              | Type özelliğini **AzureBatch** olarak ayarlayın. | Yes      |
| accountName       | Batch hesabının adı.         | Yes      |
| accessKey         | Batch hesabı için erişim anahtarı.  | Yes      |
| poolName          | VM havuzunun adı.    | Yes      |
| linkedServiceName | Bu Batch bağlantılı hizmeti ile ilişkili depolama bağlı hizmetinin adı. Bu bağlı hizmet, etkinliği çalıştırmak için gereken hazırlama dosyaları için ve Etkinlik yürütme günlüklerini depolamak için kullanılır. | Yes      |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Azure Machine Learning Studio (klasik) bağlı hizmet
Bir veri fabrikasına Studio (klasik) toplu Puanlama uç noktasını kaydetmek için Azure Machine Learning Studio (klasik) bağlantılı bir hizmet oluşturabilirsiniz.

### <a name="example"></a>Örnek

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Özellikler
| Özellik   | Açıklama                              | Gerekli |
| ---------- | ---------------------------------------- | -------- |
| Tür       | Type özelliğini **AzureML** olarak ayarlayın. | Yes      |
| mlEndpoint | Toplu işlem Puanlama URL 'SI.                   | Yes      |
| apiKey     | Yayımlanan çalışma alanı modelinin API 'SI.     | Yes      |

## <a name="azure-data-lake-analytics-linked-service"></a>Bağlı hizmet Azure Data Lake Analytics
Bir Data Lake Analytics işlem hizmetini bir Azure Data Factory 'ye bağlamak için Data Lake Analytics bağlantılı bir hizmet oluşturabilirsiniz. İşlem hattındaki Data Lake Analytics U-SQL etkinliği, bu bağlı hizmeti ifade eder. 

Aşağıdaki tabloda JSON tanımında kullanılan genel özellikler açıklanmaktadır:

| Özellik                 | Açıklama                              | Gerekli                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| tür                 | Type özelliğini **AzureDataLakeAnalytics** olarak ayarlayın. | Yes                                      |
| accountName          | Data Lake Analytics hesap adı.  | Yes                                      |
| Datalakeanaliz Ticsurı | Data Lake Analytics URI 'SI.           | No                                       |
| subscriptionId       | Azure abonelik KIMLIĞI.                    | No<br /><br />(Belirtilmemişse, Data Factory aboneliği kullanılır.) |
| resourceGroupName    | Azure Kaynak grubu adı.                | No<br /><br /> (Belirtilmemişse, Data Factory kaynak grubu kullanılır.) |

### <a name="authentication-options"></a>Kimlik doğrulaması seçenekleri
Data Lake Analytics bağlı hizmetiniz için, hizmet sorumlusu veya Kullanıcı kimlik bilgileri kullanarak kimlik doğrulaması arasında seçim yapabilirsiniz.

#### <a name="service-principal-authentication-recommended"></a>Hizmet sorumlusu kimlik doğrulaması (önerilir)
Hizmet sorumlusu kimlik doğrulamasını kullanmak için, Azure Active Directory (Azure AD) bir uygulama varlığı kaydedin. Ardından Data Lake Store için Azure AD erişimi verin. Ayrıntılı adımlar için bkz. [hizmetten hizmete kimlik doğrulaması](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Bağlı hizmeti tanımlamak için kullandığınız aşağıdaki değerleri unutmayın:
* Uygulama Kimliği
* Uygulama anahtarı 
* Kiracı Kimliği

Aşağıdaki özellikleri belirterek hizmet sorumlusu kimlik doğrulamasını kullanın:

| Özellik                | Açıklama                              | Gerekli |
| :---------------------- | :--------------------------------------- | :------- |
| Serviceprincipalıd  | Uygulamanın istemci KIMLIĞI.     | Yes      |
| Servicesprincipalkey | Uygulamanın anahtarı.           | Yes      |
| Kiracı              | Uygulamanızın bulunduğu kiracı bilgileri (etki alanı adı veya kiracı KIMLIĞI). Bu bilgileri almak için, farenizi Azure portal sağ üst köşesine getirin. | Yes      |

**Örnek: hizmet sorumlusu kimlik doğrulaması**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Kullanıcı kimlik bilgisi kimlik doğrulaması
Data Lake Analytics için Kullanıcı kimlik bilgileri kimlik doğrulaması için aşağıdaki özellikleri belirtin:

| Özellik          | Açıklama                              | Gerekli |
| :---------------- | :--------------------------------------- | :------- |
| yetkilendirme | Data Factory Düzenleyicisi 'nde **Yetkilendir** düğmesini seçin. Bu özelliğe otomatik olarak oluşturulan yetkilendirme URL 'sini atayan kimlik bilgisini girin. | Yes      |
| Kimliği     | OAuth yetkilendirme oturumundan gelen OAuth oturum KIMLIĞI. Her oturum KIMLIĞI benzersizdir ve yalnızca bir kez kullanılabilir. Bu ayar Data Factory düzenleyicisini kullandığınızda otomatik olarak oluşturulur. | Yes      |

**Örnek: Kullanıcı kimlik bilgisi kimlik doğrulaması**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Belirteç süre sonu
**Yetkilendir** düğmesini seçerek oluşturduğunuz yetkilendirme kodu, bir ayarlanan aralıktan sonra dolar. 

Kimlik doğrulama belirtecinin süresi dolarsa aşağıdaki hata iletisini görebilirsiniz: 

  Kimlik bilgisi işlemi hatası: invalid_grant-AADSTS70002: kimlik bilgileri doğrulanırken hata oluştu. AADSTS70008: belirtilen erişim izni, zaman aşımına uğradı veya iptal edildi. İzleme KIMLIĞI: d18629e8-af88-43c5-88e3-d8419eb1fca1 bağıntı KIMLIĞI: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 zaman damgası: 2015-12-15 21:09:31Z

Aşağıdaki tabloda Kullanıcı hesabı türüne göre süre sonları gösterilmektedir: 

| Kullanıcı türü                                | Süre sonu                            |
| :--------------------------------------- | :--------------------------------------- |
| Azure AD *tarafından yönetilmeyen Kullanıcı* hesapları (hotmail, canlı, vb.) | 12 saat.                                 |
| Azure AD tarafından *yönetilen Kullanıcı* hesapları | Son dilimin çalıştırıldıktan 14 gün sonra. <br /><br />90 gün, OAuth tabanlı bağlı bir hizmeti temel alan bir dilim 14 günde en az bir kez çalışır. |

Bu hatayı önlemek veya çözmek için, belirtecin süresi dolarsa **Yetkilendir** düğmesini seçerek yeniden Yetkilendir yapın. Ardından, bağlantılı hizmeti yeniden dağıtın. Aşağıdaki kodu kullanarak, program aracılığıyla **SessionID** ve **Yetkilendirme** özellikleri için değerler de oluşturabilirsiniz:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Bu kod örneğinde kullanılan Data Factory sınıfları hakkında daha fazla bilgi için bkz.:
* [AzureDataLakeStoreLinkedService sınıfı](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
* [AzureDataLakeAnalyticsLinkedService sınıfı](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* [AuthorizationSessionGetResponse sınıfı](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse)

**WindowsFormsWebAuthenticationDialog** sınıfı için Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll bir başvuru ekleyin. 

## <a name="azure-sql-linked-service"></a>Azure SQL bağlı hizmeti
Bir SQL bağlı hizmeti oluşturabilir ve bir Data Factory işlem hattından saklı yordam çağırmak için [saklı yordam etkinliğiyle](data-factory-stored-proc-activity.md) birlikte kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure SQL Bağlayıcısı](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-synapse-analytics-linked-service"></a>Azure SYNAPSE Analytics bağlı hizmeti
Bir Azure SYNAPSE Analytics bağlı hizmeti oluşturabilir ve bir Data Factory işlem hattından saklı yordam çağırmak için [saklı yordam etkinliğiyle](data-factory-stored-proc-activity.md) birlikte kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics Bağlayıcısı](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Bağlı hizmet SQL Server
Bir SQL Server bağlı hizmeti oluşturabilir ve bir Data Factory işlem hattından saklı yordam çağırmak için [saklı yordam etkinliği](data-factory-stored-proc-activity.md) ile kullanabilirsiniz. Daha fazla bilgi için bkz. [SQL Server Bağlayıcısı](data-factory-sqlserver-connector.md#linked-service-properties).