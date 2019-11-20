---
title: Azure Data Factory tarafından desteklenen işlem ortamları
description: Verileri dönüştürmek veya işlemek için Azure Data Factory işlem hatları (Azure HDInsight gibi) içinde kullanabileceğiniz işlem ortamları hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 59e31f0c280687dfd2a79b3a40d8474c82b794d4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681571"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Azure Data Factory tarafından desteklenen işlem ortamları
Bu makalede, verileri işlemek veya dönüştürmek için kullanabileceğiniz farklı işlem ortamları açıklanmaktadır. Ayrıca, bu işlem ortamlarını bir Azure Data Factory 'ye bağlayan bağlı hizmetleri yapılandırırken Data Factory tarafından desteklenen farklı yapılandırma (kendi isteğe bağlı ve kendi kendinize getir) hakkında ayrıntılar sağlar.

Aşağıdaki tabloda, Data Factory tarafından desteklenen işlem ortamlarının listesi ve bunlar üzerinde çalışabilecek etkinlikler verilmiştir. 

| İşlem ortamı                                          | etkinlikler                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [İsteğe bağlı HDInsight kümesi](#azure-hdinsight-on-demand-linked-service) veya [kendi HDInsight kümeniz](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop akışı](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Özel](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio](#azure-machine-learning-studio-linked-service) | [Machine Learning etkinlikleri: Toplu Yürütme ve Kaynak Güncelleştirme](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning yürütme işlem hattı](transform-data-machine-learning-service.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning yürütme işlem hattı](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL veri ambarı](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Saklı Yordam](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Not defteri](transform-data-databricks-notebook.md), [jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |
| [Azure Işlevi](#azure-function-linked-service)         | [Azure Işlevi etkinliği](control-flow-azure-function-activity.md)
>  

## <a name="on-demand-hdinsight-compute-environment"></a>İsteğe bağlı HDInsight işlem ortamı
Bu yapılandırmada, bilgi işlem ortamı Azure Data Factory hizmeti tarafından tam olarak yönetilir. Bir iş, verileri işlemek ve iş tamamlandığında kaldırılmadan önce Data Factory hizmeti tarafından otomatik olarak oluşturulur. İsteğe bağlı işlem ortamı için bağlı bir hizmet oluşturabilir, bunu yapılandırabilir ve iş yürütmesi, küme yönetimi ve önyükleme eylemleri için ayrıntılı ayarları kontrol edebilirsiniz.

> [!NOTE]
> İsteğe bağlı yapılandırma Şu anda yalnızca Azure HDInsight kümeleri için desteklenmektedir. Azure Databricks ayrıca iş kümelerini kullanarak isteğe bağlı işleri destekler, daha fazla ayrıntı için [Azure Databricks bağlı hizmeti](#azure-databricks-linked-service) 'ne bakın.

## <a name="azure-hdinsight-on-demand-linked-service"></a>İsteğe bağlı Azure HDInsight bağlı hizmeti
Azure Data Factory hizmeti, verileri işlemek için otomatik olarak isteğe bağlı bir HDInsight kümesi oluşturabilir. Küme, kümeyle ilişkili depolama hesabı (JSON 'daki linkedServiceName özelliği) ile aynı bölgede oluşturulur. Depolama hesabı, genel amaçlı standart bir Azure depolama hesabı olmalıdır. 

İsteğe bağlı HDInsight bağlı hizmeti hakkında aşağıdaki **önemli** noktalara dikkat edin:

* İsteğe bağlı HDInsight kümesi, Azure aboneliğiniz kapsamında oluşturulur. Küme çalışır duruma geldiğinde Azure portal kümeyi görebileceksiniz. 
* İsteğe bağlı HDInsight kümesinde çalıştırılan işlerin günlükleri, HDInsight kümesiyle ilişkili depolama hesabına kopyalanır. Bağlı hizmet tanımınızda tanımlanmış clusterUserName, Kümeparolası, clusterSshUserName, clusterSshPassword, kümenin yaşam döngüsü sırasında ayrıntılı sorun giderme amacıyla kümede oturum açmak için kullanılır. 
* Yalnızca HDInsight kümesinin çalışır duruma ve çalışmaya çalıştığı zaman ücretlendirilirsiniz.
* Azure HDInsight isteğe bağlı hizmeti ile bir **betik eylemi** kullanabilirsiniz.  

> [!IMPORTANT]
> İstek üzerine bir Azure HDInsight kümesi sağlamak için genellikle **20 dakika** veya daha fazla sürer.

### <a name="example"></a>Örnek
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
> HDInsight kümesi JSON’da belirttiğiniz blob depolamada (**linkedServiceName**) bir **varsayılan kapsayıcı** oluşturur. HDInsight, küme silindiğinde bu kapsayıcıyı silmez. Bu davranış tasarım gereğidir. İsteğe bağlı HDInsight bağlı hizmetiyle, HDInsight kümesi her oluşturulduğunda, burada mevcut canlı bir küme (**timeToLive**) olmadıkça bir dilim gerekir ve işlem bittiğinde silinir. 
>
> Daha fazla Etkinlik çalıştırıldığında, Azure Blob depolamada birçok kapsayıcı görürsünüz. İşlerin sorunları giderilmesi için bunlara gerek yoksa, depolama maliyetini azaltmak için bunları silmek isteyebilirsiniz. Bu kapsayıcı adları bir düzene sahiptir: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Azure blob depolamada kapsayıcı silmek için [Microsoft Storage Gezgini](https://storageexplorer.com/) gibi araçları kullanın.
>
> 

### <a name="properties"></a>Özellikler
| Özellik                     | Açıklama                              | Gerekli |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Type özelliği **hdınsightondemand**olarak ayarlanmalıdır. | Evet      |
| clusterSize                  | Kümedeki çalışan/veri düğümlerinin sayısı. HDInsight kümesi, bu özellik için belirttiğiniz çalışan düğümü sayısıyla birlikte 2 baş düğüm ile oluşturulur. Düğümler 4 çekirdeğe sahip Standard_D3 boyutudur, bu nedenle 4 çalışan düğümü kümesi 24 çekirdek alır (çalışan düğümleri için 4\*4 = 16 çekirdek, ve baş düğümler için 2\*4 = 8 çekirdek). Ayrıntılar için bkz. [HDInsight 'Ta Hadoop, Spark, Kafka ve daha fazlası ile kümeleri ayarlama](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) . | Evet      |
| linkedServiceName            | Verileri depolamak ve işlemek için isteğe bağlı küme tarafından kullanılacak Azure depolama bağlı hizmeti. HDInsight kümesi, bu Azure depolama hesabı ile aynı bölgede oluşturulur. Azure HDInsight, desteklediği her bir Azure bölgesinde kullanabileceğiniz toplam çekirdek sayısıyla ilgili sınırlamaya sahiptir. Gerekli clusterSize uyması için bu Azure bölgesinde yeterli çekirdek kotadığınızdan emin olun. Ayrıntılar için bkz. [HDInsight 'Ta Hadoop, Spark, Kafka ve daha fazlası ile küme ayarlama](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Şu anda depolama olarak bir Azure Data Lake Store kullanan isteğe bağlı bir HDInsight kümesi oluşturamazsınız. Bir Azure Data Lake Store HDInsight işlemeden elde edilen sonuç verilerini depolamak istiyorsanız, verileri Azure Blob depolama alanından Azure Data Lake Store kopyalamak için bir kopyalama etkinliği kullanın. </p> | Evet      |
| clusterResourceGroup         | HDInsight kümesi bu kaynak grubunda oluşturulur. | Evet      |
| timeToLive                   | İsteğe bağlı HDInsight kümesi için izin verilen boşta kalma süresi. Kümede başka bir etkin iş yoksa, bir etkinlik çalıştırıldıktan sonra, isteğe bağlı HDInsight kümesinin ne kadar süreyle etkin kalacağını belirtir. İzin verilen en düşük değer 5 dakikadır (00:05:00).<br/><br/>Örneğin, bir etkinlik çalıştırması 6 dakika sürüyorsa ve TimeToLive, 5 dakika olarak ayarlanırsa, etkinlik çalıştırmasının sonunda 6 dakikadan sonra küme, 5 dakika boyunca etkin kalır. Başka bir etkinlik çalıştırması 6 dakikalık bir pencere ile yürütülürse aynı küme tarafından işlenir.<br/><br/>İsteğe bağlı HDInsight kümesi oluşturma işlemi pahalı bir işlemdir, bu nedenle isteğe bağlı HDInsight kümesini yeniden kullanarak bir veri fabrikasının performansını artırmak için bu ayarı gereken şekilde kullanın.<br/><br/>TimeToLive değerini 0 olarak ayarlarsanız, etkinlik çalıştırması tamamlandıktan hemen sonra küme silinir. Ancak, yüksek bir değer ayarlarsanız, bazı sorun giderme amacıyla oturum açmanız için küme boşta kalabilir, ancak bu durum yüksek maliyetlere neden olabilir. Bu nedenle, gereksinimlerinize göre uygun değeri ayarlamanız önemlidir.<br/><br/>TimeToLive özelliğinin değeri uygun şekilde ayarlandıysa, birden çok işlem hattı isteğe bağlı HDInsight kümesinin örneğini paylaşabilir. | Evet      |
| clusterType                  | Oluşturulacak HDInsight kümesinin türü. İzin verilen değerler "Hadoop" ve "Spark" değerleridir. Belirtilmemişse, varsayılan değer Hadoop ' dır. Kurumsal Güvenlik Paketi etkinleştirilmiş küme isteğe bağlı olarak oluşturulamaz, bunun yerine [var olan bir kümeyi kullanın/kendi işlem ortamınızı getirin](#azure-hdinsight-linked-service). | Hayır       |
| version                      | HDInsight kümesinin sürümü. Belirtilmemişse, geçerli HDInsight tanımlı varsayılan sürümü kullanılıyor. | Hayır       |
| hostsubscriptionıd           | HDInsight kümesi oluşturmak için kullanılan Azure abonelik KIMLIĞI. Belirtilmemişse, Azure oturum açma bağlamınızın abonelik KIMLIĞINI kullanır. | Hayır       |
| clusterNamePrefix           | HDI küme adının ön eki, küme adının sonuna bir zaman damgası otomatik olarak eklenir| Hayır       |
| sparkVersion                 | Küme türü "Spark" ise Spark sürümü | Hayır       |
| additionalLinkedServiceNames | Data Factory hizmeti tarafından sizin adınıza kaydettirilebilmeleri için HDInsight bağlı hizmeti için ek depolama hesapları belirtir. Bu depolama hesaplarının, linkedServiceName tarafından belirtilen depolama hesabıyla aynı bölgede oluşturulan HDInsight kümesiyle aynı bölgede olması gerekir. | Hayır       |
| osType                       | İşletim sisteminin türü. İzin verilen değerler şunlardır: Linux ve Windows (yalnızca HDInsight 3,3 için). Linux varsayılandır. | Hayır       |
| hcatalogLinkedServiceName    | HCatalog veritabanına işaret eden Azure SQL bağlı hizmetinin adı. İsteğe bağlı HDInsight kümesi, Azure SQL veritabanı, meta veri deposu olarak kullanılarak oluşturulur. | Hayır       |
| connectVia                   | Bu HDInsight bağlı hizmetine etkinlikleri göndermek için kullanılacak Integration Runtime. İsteğe bağlı HDInsight bağlı hizmeti yalnızca Azure Integration Runtime destekler. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | Hayır       |
| clusterUserName                   | Kümeye erişmek için Kullanıcı adı. | Hayır       |
| clusterPassword                   | Kümeye erişmek için güvenli dize türündeki parola. | Hayır       |
| clusterSshUserName         | SSH için Kullanıcı adı kümenin düğümüne uzaktan bağlanır (Linux için). | Hayır       |
| clusterSshPassword         | SSH 'ye yönelik güvenli dize türünde parolanın, kümenin düğümüne uzaktan bağlanmasını sağlama (Linux için). | Hayır       |
| scriptActions | İsteğe bağlı küme oluşturma sırasında [HDInsight kümesi özelleştirmeleri](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) için betiği belirtin. <br />Şu anda Azure Data Factory Kullanıcı arabirimi yazma aracı yalnızca 1 betik eylemi belirtmeyi destekler, ancak JSON 'da bu sınırlamayı alabilir (JSON 'da birden çok betik eylemi belirtebilirsiniz). | Hayır |


> [!IMPORTANT]
> HDInsight, dağıtılabilecek birden çok Hadoop küme sürümünü destekler. Her sürüm seçimi, Hortonçalışmaverisi platformu (HDP) dağıtımının belirli bir sürümünü ve bu dağıtım içinde yer alan bir bileşen kümesini oluşturur. Desteklenen HDInsight sürümlerinin listesi, en son Hadoop ekosistem bileşenlerini ve düzeltmelerini sağlamak üzere güncelleştiriliyor. HDInsight 'ın desteklenen bir sürümünü kullandığınızdan emin olmak için [desteklenen HDInsight sürümü ve işletim sistemi türünün](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) en güncel bilgilerine her zaman değindiğinizden emin olun. 
>
> [!IMPORTANT]
> Şu anda, HDInsight bağlı hizmetleri HBase, etkileşimli sorgu (Hive LLAP), fırtınası 'yi desteklemez. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON örneği

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Hizmet sorumlusu kimlik doğrulaması

Isteğe bağlı HDInsight bağlı hizmeti, sizin adınıza HDInsight kümeleri oluşturmak için bir hizmet sorumlusu kimlik doğrulaması gerektirir. Hizmet sorumlusu kimlik doğrulamasını kullanmak için, bir uygulama varlığını Azure Active Directory (Azure AD) olarak kaydedin ve aboneliğin veya HDInsight kümesinin oluşturulduğu kaynak grubunun **katkıda** bulunan rolünü verin. Ayrıntılı adımlar için bkz. [kaynaklara erişebilen Azure Active Directory uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Bağlı hizmeti tanımlamak için kullandığınız aşağıdaki değerleri unutmayın:

- Uygulama Kimliği
- Uygulama anahtarı 
- Kiracı Kimliği

Aşağıdaki özellikleri belirterek hizmet sorumlusu kimlik doğrulamasını kullanın:

| Özellik                | Açıklama                              | Gerekli |
| :---------------------- | :--------------------------------------- | :------- |
| **Serviceprincipalıd**  | Uygulamanın istemci KIMLIĞINI belirtin.     | Evet      |
| **Servicesprincipalkey** | Uygulamanın anahtarını belirtin.           | Evet      |
| **Kiracı**              | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. | Evet      |

### <a name="advanced-properties"></a>Gelişmiş Özellikler

İsteğe bağlı HDInsight kümesinin ayrıntılı yapılandırması için aşağıdaki özellikleri de belirtebilirsiniz.

| Özellik               | Açıklama                              | Gerekli |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | HDInsight kümesinin oluşturulması için çekirdek yapılandırma parametrelerini (Core-site. xml içinde olduğu gibi) belirtir. | Hayır       |
| hBaseConfiguration     | HDInsight kümesi için HBase yapılandırma parametrelerini (HBase-site. xml) belirtir. | Hayır       |
| hdfsConfiguration      | HDInsight kümesi için bir yapılandırma parametreleri (HDFS-site. xml) belirtir. | Hayır       |
| hiveConfiguration      | HDInsight kümesi için Hive yapılandırma parametrelerini (Hive-site. xml) belirtir. | Hayır       |
| mapReduceConfiguration | HDInsight kümesi için MapReduce yapılandırma parametrelerini (mapred-site. xml) belirtir. | Hayır       |
| Oozıeconfiguration     | HDInsight kümesi için Oozie yapılandırma parametrelerini (Oozie-site. xml) belirtir. | Hayır       |
| stormConfiguration     | HDInsight kümesi için fırtınası yapılandırma parametrelerini (Storm-site. xml) belirtir. | Hayır       |
| yarnConfiguration      | HDInsight kümesi için Yarn yapılandırma parametrelerini (Yarn-site. xml) belirtir. | Hayır       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Örnek: gelişmiş özelliklerle isteğe bağlı HDInsight kümesi yapılandırması

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

### <a name="node-sizes"></a>Düğüm boyutları
Aşağıdaki özellikleri kullanarak baş, veri ve Zookeeper düğümleri için boyut belirtebilirsiniz: 

| Özellik          | Açıklama                              | Gerekli |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Baş düğümün boyutunu belirtir. Varsayılan değer: Standard_D3. Ayrıntılar için bkz. **düğüm boyutlarını belirtme** bölümü. | Hayır       |
| Davtanodesize      | Veri düğümünün boyutunu belirtir. Varsayılan değer: Standard_D3. | Hayır       |
| zookeeperNodeSize | Zoo Man düğümünün boyutunu belirtir. Varsayılan değer: Standard_D3. | Hayır       |

#### <a name="specifying-node-sizes"></a>Düğüm boyutlarını belirtme
Önceki bölümde bahsedilen özellikler için belirtmeniz gereken dize değerleri için [sanal makine boyutları](../virtual-machines/linux/sizes.md) makalesine bakın. Bu değerler, makalede başvurulan **API 'ler & cmdlet 'lerle** uyumlu olmalıdır. Makalede gördüğünüz gibi büyük (varsayılan) boyutun veri düğümü 7 GB belleğe sahiptir ve bu da senaryonuz için yeterince iyi olmayabilir. 

D4 boyutlu baş düğümleri ve çalışan düğümleri oluşturmak istiyorsanız, headNodeSize ve Davtanodesize özelliklerinin değeri olarak **Standard_D4** belirtin. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Bu özellikler için yanlış bir değer belirtirseniz, şu **hatayı alabilirsiniz:** küme oluşturulamadı. Özel durum: Küme oluşturma işlemi tamamlanamadı. İşlem '400' koduyla başarısız oldu. Küme geride bırakma durumu: 'Hata'. İleti: ' Prelustercreationvalidationfailure '. Bu hatayı aldığınızda, [sanal makinelerin boyutları](../virtual-machines/linux/sizes.md) makalesindeki tabloda **CMDLET & API 'leri** adını kullandığınızdan emin olun.        

## <a name="bring-your-own-compute-environment"></a>Kendi işlem ortamınızı getirin
Bu yapılandırmada, kullanıcılar zaten mevcut bir bilgi işlem ortamını Data Factory bağlı hizmet olarak kaydedebilir. Bilgi işlem ortamı Kullanıcı tarafından yönetilir ve Data Factory hizmeti tarafından etkinlikleri yürütmek için kullanılır.

Bu yapılandırma türü aşağıdaki işlem ortamları için desteklenir:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

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
| type              | Type özelliği **HDInsight**olarak ayarlanmalıdır.            | Evet      |
| clusterUri        | HDInsight kümesinin URI 'SI.                            | Evet      |
| kullanıcı adı          | Mevcut bir HDInsight kümesine bağlanmak için kullanılacak kullanıcının adını belirtin. | Evet      |
| password          | Kullanıcı hesabı için parola belirtin.                       | Evet      |
| linkedServiceName | HDInsight kümesi tarafından kullanılan Azure Blob depolama alanına başvuran Azure depolama bağlı hizmetinin adı. <p>Şu anda bu özellik için Azure Data Lake Store bağlı bir hizmet belirtemezsiniz. HDInsight kümesinin Data Lake Store erişimi varsa Hive/Pig betiklerinden Azure Data Lake Store verilere erişebilirsiniz. </p> | Evet      |
| ıvspenabled      | HDInsight kümesi [Kurumsal güvenlik paketi](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-architecture) etkinse '*true ' değerini*belirtin. Varsayılan değer '*false*' şeklindedir. | Hayır       |
| connectVia        | Bu bağlı hizmete etkinlikleri göndermek için kullanılacak Integration Runtime. Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. <br />Kurumsal Güvenlik Paketi (ESP) etkin HDInsight kümesi için, kümeye bir görüş satırı içeren, şirket içinde barındırılan bir tümleştirme çalışma zamanı kullanın veya ESP HDInsight kümesiyle aynı sanal ağ içinde dağıtılması gerekir. | Hayır       |

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
* Azure Batch bir hesap oluşturmak için [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) cmdlet 'i, Azure portal kullanarak Azure Batch hesabı oluşturmak için [Azure Portal](../batch/batch-account-create-portal.md) . Cmdlet 'ini kullanma hakkında ayrıntılı yönergeler için [Azure Batch hesabını yönetmek üzere PowerShell kullanma](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) makalesine bakın.
* Azure Batch havuzu oluşturmak için [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) cmdlet 'i.

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
| type              | Type özelliği **AzureBatch**olarak ayarlanmalıdır. | Evet      |
| Adı       | Azure Batch hesabının adı.         | Evet      |
| accessKey         | Azure Batch hesabı için erişim anahtarı.  | Evet      |
| batchUri          | Azure Batch hesabınızın URL 'SI, https://*batchaccountname. Region*. Batch.Azure.com biçiminde. | Evet      |
| poolName          | Sanal makine havuzunun adı.    | Evet      |
| linkedServiceName | Bu Azure Batch bağlı hizmetiyle ilişkili Azure depolama bağlı hizmetinin adı. Bu bağlı hizmet, etkinliği çalıştırmak için gereken hazırlama dosyaları için kullanılır. | Evet      |
| connectVia        | Bu bağlı hizmete etkinlikleri göndermek için kullanılacak Integration Runtime. Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | Hayır       |

## <a name="azure-machine-learning-studio-linked-service"></a>Bağlı hizmet Azure Machine Learning Studio
Bir Machine Learning Batch Puanlama uç noktasını bir veri fabrikasına kaydetmek için Azure Machine Learning Studio bağlı bir hizmet oluşturursunuz.

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
| Tür                   | Type özelliği: **AzureML**olarak ayarlanmalıdır. | Evet                                      |
| mlEndpoint             | Toplu işlem Puanlama URL 'SI.                   | Evet                                      |
| apiKey                 | Yayımlanan çalışma alanı modelinin API 'SI.     | Evet                                      |
| updateResourceEndpoint | Tahmine dayalı Web hizmetini eğitilen model dosyasıyla güncelleştirmek için kullanılan bir Azure Machine Learning Web hizmeti uç noktasının güncelleştirme kaynak URL 'SI | Hayır                                       |
| Serviceprincipalıd     | Uygulamanın istemci KIMLIĞINI belirtin.     | UpdateResourceEndpoint belirtilmişse gereklidir |
| Servicesprincipalkey    | Uygulamanın anahtarını belirtin.           | UpdateResourceEndpoint belirtilmişse gereklidir |
| Kiracı                 | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. | UpdateResourceEndpoint belirtilmişse gereklidir |
| connectVia             | Bu bağlı hizmete etkinlikleri göndermek için kullanılacak Integration Runtime. Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | Hayır                                       |

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
| Tür                   | Type özelliği: **AzureMLService**olarak ayarlanmalıdır. | Evet                                      |
| subscriptionId         | Azure abonelik KIMLIĞI              | Evet                                      |
| resourceGroupName      | ad | Evet                                      |
| Mlçalışmaalanıadı        | Azure Machine Learning çalışma alanı adı | Evet  |
| Serviceprincipalıd     | Uygulamanın istemci KIMLIĞINI belirtin.     | Hayır |
| Servicesprincipalkey    | Uygulamanın anahtarını belirtin.           | Hayır |
| Kiracı                 | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. | UpdateResourceEndpoint belirtilmişse gereklidir | Hayır |
| connectVia             | Bu bağlı hizmete etkinlikleri göndermek için kullanılacak Integration Runtime. Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | Hayır |    

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
| type                 | Type özelliği: **AzureDataLakeAnalytics**olarak ayarlanmalıdır. | Evet                                      |
| Adı          | Azure Data Lake Analytics hesap adı.  | Evet                                      |
| Datalakeanaliz Ticsurı | Azure Data Lake Analytics URI 'SI.           | Hayır                                       |
| subscriptionId       | Azure abonelik KIMLIĞI                    | Hayır                                       |
| resourceGroupName    | Azure kaynak grubu adı                | Hayır                                       |
| Serviceprincipalıd   | Uygulamanın istemci KIMLIĞINI belirtin.     | Evet                                      |
| Servicesprincipalkey  | Uygulamanın anahtarını belirtin.           | Evet                                      |
| Kiracı               | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. | Evet                                      |
| connectVia           | Bu bağlı hizmete etkinlikleri göndermek için kullanılacak Integration Runtime. Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | Hayır                                       |



## <a name="azure-databricks-linked-service"></a>Bağlı hizmet Azure Databricks
Databricks iş yüklerini (Not defteri, Jar, Python) çalıştırmak için kullanacağınız Databricks çalışma alanını kaydettirmek üzere **Azure Databricks bağlantılı hizmet** oluşturabilirsiniz. 
> [!IMPORTANT]
> Databricks bağlı Hizmetleri [örnek havuzlarını](https://aka.ms/instance-pools)destekler. 

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
| ad                 | Bağlı hizmetin adı               | Evet   |
| type                 | Type özelliği: **Azure Databricks**olarak ayarlanmalıdır. | Evet                                      |
| alanını               | Databricks çalışma alanının bölgesine göre uygun şekilde Azure bölgesini belirtin. Örnek: https://eastus.azuredatabricks.net | Evet                                 |
| accessToken          | Azure Databricks kimlik doğrulaması için Data Factory erişim belirteci gereklidir. Erişim belirtecinin databricks çalışma alanından oluşturulması gerekir. Erişim belirtecini bulmak için daha ayrıntılı adımlar [burada](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token) bulunabilir  | Evet                                       |
| Existingclusterıd    | Bu kümedeki tüm işleri çalıştırmak için var olan bir kümenin küme KIMLIĞI. Bu, önceden oluşturulmuş bir etkileşimli küme olmalıdır. Yanıt vermeyi durdurduğunda kümeyi el ile yeniden başlatmanız gerekebilir. Databricks daha fazla güvenilirlik sağlamak için işleri yeni kümeler üzerinde çalıştırmaya öneriyor. Databricks çalışma alanı-> kümeleri-> Etkileşimli küme adı-> Yapılandırma-> etiketleri üzerinde etkileşimli bir kümenin küme KIMLIĞINI bulabilirsiniz. [Daha fazla ayrıntı](https://docs.databricks.com/user-guide/clusters/tags.html) | Hayır 
| instancePoolId    | Databricks çalışma alanında var olan bir havuzun örnek havuzu KIMLIĞI.  | Hayır  |
| newClusterVersion    | Kümenin Spark sürümü. Databricks içinde bir iş kümesi oluşturacaktır. | Hayır  |
| newClusterNumOfWorker| Bu kümenin sahip olması gereken çalışan düğüm sayısı. Bir kümede toplam num_workers + 1 Spark düğümü için bir Spark sürücüsü ve num_workers Yürüticileri vardır. "1" gibi bir Int32 dizesi, numOfWorker 'ın 1 veya "1:10" olması anlamına gelir.  | Hayır                |
| newClusterNodeType   | Bu alan tek bir değer ile, bu kümedeki Spark düğümlerinin her biri için kullanılabilir kaynakları kodluyor. Örneğin, Spark düğümleri bellek veya işlem yoğunluğu yoğun iş yükleri için sağlanabilir ve iyileştirilebilir. Bu alan yeni küme için gereklidir                | Hayır               |
| Newclusterparlak conf  | isteğe bağlı, Kullanıcı tarafından belirtilen Spark yapılandırması anahtar-değer çiftleri kümesi. Kullanıcılar ayrıca, sırasıyla spark. Driver. extraJavaOptions ve Spark. yürütücü. extraJavaOptions aracılığıyla sürücüye ve yürüticilere ek JVM seçenekleri dizesi geçirebilir. | Hayır  |
| Newclusterınitscripts| Yeni küme için isteğe bağlı, Kullanıcı tanımlı başlatma betikleri kümesi. İnit betikleri için DBFS yolunu belirtme. | Hayır  |


## <a name="azure-sql-database-linked-service"></a>Azure SQL Veritabanı bağlı hizmeti
Bir Azure SQL bağlı hizmeti oluşturur ve bir Data Factory işlem hattından saklı yordam çağırmak için [saklı yordam etkinliğiyle](transform-data-using-stored-procedure.md) birlikte kullanırsınız. Bu bağlı hizmet hakkındaki ayrıntılar için bkz. [Azure SQL Bağlayıcısı](connector-azure-sql-database.md#linked-service-properties) makalesi.

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL veri ambarı bağlı hizmeti
Bir Azure SQL veri ambarı bağlı hizmeti oluşturun ve bir Data Factory işlem hattından saklı yordam çağırmak için [saklı yordam etkinliğini](transform-data-using-stored-procedure.md) kullanın. Bu bağlı hizmet hakkındaki ayrıntılar için bkz. [Azure SQL veri ambarı Bağlayıcısı](connector-azure-sql-data-warehouse.md#linked-service-properties) makalesi.

## <a name="sql-server-linked-service"></a>Bağlı hizmet SQL Server
SQL Server bağlı bir hizmet oluşturur ve bir Data Factory işlem hattından saklı yordam çağırmak için [saklı yordam etkinliğiyle](transform-data-using-stored-procedure.md) birlikte kullanırsınız. Bu bağlı hizmet hakkındaki ayrıntılar için bkz. [SQL Server Bağlayıcısı](connector-sql-server.md#linked-service-properties) makalesi.

## <a name="azure-function-linked-service"></a>Azure Işlevi bağlı hizmeti
Azure Işlev bağlı hizmeti oluşturup Azure Işlevleri 'ni bir Data Factory işlem hattında çalıştırmak için [Azure işlevi etkinliğiyle](control-flow-azure-function-activity.md) birlikte kullanabilirsiniz. Azure işlevinin dönüş türü geçerli bir `JObject`olmalıdır. ( [Jarray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) 'in bir `JObject`*değil* olduğunu aklınızda bulundurun.) `JObject` dışındaki herhangi bir dönüş türü başarısız olur ve Kullanıcı hata *yanıtı içeriğini başlatır geçerli bir JObject değildir*.

| **Özellik** | **Açıklama** | **Gerekli** |
| --- | --- | --- |
| type   | Type özelliği: **AzureFunction** olarak ayarlanmalıdır | evet |
| function app url | Azure İşlev Uygulaması URL 'SI. Biçim `https://<accountname>.azurewebsites.net`. Bu URL, Azure portal İşlev Uygulaması görüntülenirken **URL** bölümündeki değerdir  | evet |
| function key | Azure Işlevi için erişim anahtarı. İlgili işlevin **Yönet** bölümüne tıklayın ve **işlev anahtarını** ya da **ana bilgisayar anahtarını**kopyalayın. Buradan daha fazla bilgi edinebilirsiniz: [Azure IŞLEVLERI http Tetikleyicileri ve bağlamaları](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | evet |
|   |   |   |

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory tarafından desteklenen dönüştürme etkinliklerinin listesi için bkz. [verileri dönüştürme](transform-data.md).
