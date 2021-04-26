---
title: Azure Cosmos DB denetim düzlemi işlemlerini denetleme
description: Bölge ekleme, aktarım hızını güncelleştirme, bölge yük devretme, VNet ekleme, sanal ağ vb. gibi denetim düzlemi işlemlerini nasıl denetleyeceğinizi öğrenin. Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/05/2020
ms.author: sngun
ms.openlocfilehash: 6f3e408343fc75d6587d1a67a0179edf13d56e36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658257"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Azure Cosmos DB denetim düzlemi işlemlerini denetleme
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB denetim düzlemi, Azure Cosmos hesabında farklı bir işlem kümesi gerçekleştirmenize olanak tanıyan bir Reston hizmettir. Ortak bir kaynak modeli (örneğin, veritabanı, hesap) ve son kullanıcılara kaynak modelinde işlem gerçekleştirmeye yönelik çeşitli işlemler sunar. Denetim düzlemi işlemleri Azure Cosmos hesabında veya kapsayıcısında yapılan değişiklikleri içerir. Örneğin, bir Azure Cosmos hesabı oluşturma, bölge ekleme, aktarım hızını güncelleştirme, bölge yük devretme, VNet ekleme vb. gibi işlemler, bazı denetim düzlemi işlemlerdir. Bu makalede Azure Cosmos DB içindeki denetim düzlemi işlemlerini denetleme açıklanmaktadır. Azure CLı, PowerShell veya Azure portal kullanarak Azure Cosmos hesaplarında denetim düzlemi işlemlerini çalıştırabilirsiniz, ancak kapsayıcılar için Azure CLı veya PowerShell kullanın.

Denetim denetim düzlemi işlemlerinin yararlı olduğu bazı örnek senaryolar aşağıda verilmiştir:

* Azure Cosmos hesabınız için güvenlik duvarı kuralları değiştirildiğinde bir uyarı almak istiyorsunuz. Azure Cosmos hesabınızın ağ güvenliğini yöneten ve hızlı bir şekilde gerçekleştirilecek kurallara yönelik yetkisiz değişiklikler bulmak için uyarı gereklidir.

* Azure Cosmos hesabınızdan yeni bir bölge eklenirse veya buradan bir uyarı almak istiyorsanız uyarı almak isteyebilirsiniz. Bölge ekleme veya kaldırma, faturalandırma ve veri egemenlik gereksinimlerini etkiler. Bu uyarı, hesabınızda yanlışlıkla ekleme veya bölge kaldırma işlemini tespit etmenize yardımcı olur.

* Değişiklik yapılan tanılama günlüklerinden daha fazla ayrıntı almak istiyorsunuz. Örneğin, bir sanal ağ değiştirildi.

## <a name="disable-key-based-metadata-write-access"></a>Anahtar tabanlı meta veri yazma erişimini devre dışı bırak

Azure Cosmos DB ' de denetim düzlemi işlemlerini denetetmeden önce, hesabınızda anahtar tabanlı meta veri yazma erişimini devre dışı bırakın. Anahtar tabanlı meta veri yazma erişimi devre dışı bırakıldığında, hesap anahtarları üzerinden Azure Cosmos hesabına bağlanan istemcilerin hesaba erişmesi engellenir. Özelliği true olarak ayarlayarak yazma erişimini devre dışı bırakabilirsiniz `disableKeyBasedMetadataWriteAccess` . Bu özelliği ayarladıktan sonra, herhangi bir kaynakta yapılan değişiklikler, doğru Azure rolüne ve kimlik bilgilerine sahip bir kullanıcıdan meydana gelebilir. Bu özelliği ayarlama hakkında daha fazla bilgi için bkz. SDK 'larda [değişiklik](role-based-access-control.md#prevent-sdk-changes) yapma makalesi. 

Etkinleştirildikten sonra `disableKeyBasedMetadataWriteAccess` , SDK tabanlı istemciler oluşturma veya güncelleştirme işlemleri çalıştırdıysa, *' ContainerNameorDatabaseName ' kaynağındaki "Işlem ' Post ' öğesine Azure Cosmos DB uç noktası üzerinden izin verilmez* . Hesabınız için bu tür işlemlere erişimi açmanız veya Azure Resource Manager, Azure CLı veya Azure PowerShell aracılığıyla oluşturma/güncelleştirme işlemleri gerçekleştirmeniz gerekir. Geri dönmek için, [Cosmos SDK 'dan gelen değişiklikleri](role-based-access-control.md#prevent-sdk-changes) kısıtlama makalesinde açıklandığı gibi, Azure CLI kullanarak disableKeyBasedMetadataWriteAccess 'i **false** olarak ayarlayın. Değerini `disableKeyBasedMetadataWriteAccess` doğru yerine false olarak değiştirdiğinizden emin olun.

Meta veri yazma erişimini kapatırken aşağıdaki noktaları göz önünde bulundurun:

* Uygulamalarınızın, SDK veya hesap anahtarlarını kullanarak yukarıdaki kaynakları (örneğin, koleksiyon oluşturma, iş üretimini güncelleştirme,...) değiştiren meta veri çağrıları oluşturmadığından emin olun.

* Şu anda Azure portal, meta veri işlemleri için hesap anahtarlarını kullanır, bu nedenle bu işlemler engellenir. Alternatif olarak, bu tür işlemleri gerçekleştirmek için Azure CLı, SDK 'lar veya Kaynak Yöneticisi Şablon dağıtımlarını kullanın.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Denetim düzlemi işlemleri için tanılama günlüklerini etkinleştirme

Azure portal kullanarak denetim düzlemi işlemleri için tanılama günlüklerini etkinleştirebilirsiniz. Etkinleştirildikten sonra, tanılama günlükleri işlemi, ilgili ayrıntılarla birlikte bir dizi başlangıç ve tamamlanmış olay olarak kaydeder. Örneğin, *Regionfailoverstart* ve *regionfailovertamamlanı* bölge yük devretme olayını tamamlar.

Denetim düzlemi işlemlerinde günlüğe kaydetmeyi etkinleştirmek için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com) açın ve Azure Cosmos hesabınıza gidin.

1. **Tanılama ayarları** bölmesini açın, oluşturulacak Günlükler Için bir **ad** sağlayın.

1. Günlük türü için **Controlplanerequests** ' yi seçin ve **Log Analytics gönder** seçeneğini belirleyin.

Günlükleri bir depolama hesabında veya bir olay hub 'ına de kaydedebilirsiniz. Bu makalede, Log Analytics 'e günlüklerin nasıl gönderileceği ve daha sonra sorgulanacağını gösterir. Etkinleştirildikten sonra, tanılama günlüklerinin etkili olması birkaç dakika sürer. Bu noktadan sonra gerçekleştirilen tüm denetim düzlemi işlemleri izlenir. Aşağıdaki ekran görüntüsünde denetim düzlemi günlüklerinin nasıl etkinleştirileceği gösterilmektedir:

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="Denetim düzlemi isteklerini günlüğe kaydetmeyi etkinleştir":::

## <a name="view-the-control-plane-operations"></a>Denetim düzlemi işlemlerini görüntüleme

Günlüğe kaydetmeyi etkinleştirdikten sonra, belirli bir hesap için işlemleri izlemek üzere aşağıdaki adımları kullanın:

1. [Azure portalda](https://portal.azure.com) oturum açın.

1. Sol taraftaki gezinmede **izleyici** sekmesini açın ve ardından **Günlükler** bölmesini seçin. Bu, kapsamdaki belirli hesapla kolayca sorguları çalıştırabileceğiniz bir kullanıcı arabirimi açar. Denetim düzlemi günlüklerini görüntülemek için aşağıdaki sorguyu çalıştırın:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

Aşağıdaki ekran görüntüleri, bir Azure Cosmos hesabı için tutarlılık düzeyi değiştiğinde günlükleri yakalar:

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="VNet eklendiğinde denetim düzlemi günlükleri":::

Aşağıdaki ekran görüntüleri, bir Cassandra hesabının anahtar alanı veya tablosu oluşturulduğunda ve üretilen iş güncelleştirileceği zaman günlükleri yakalar. Veritabanı ve kapsayıcı oluşturma ve güncelleştirme işlemleri için denetim düzlemi günlükleri, aşağıdaki ekran görüntüsünde gösterildiği gibi ayrı olarak kaydedilir:

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="Verimlilik güncelleniyorsa denetim düzlemi günlükleri":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Belirli bir işlemle ilişkili kimliği tanımlama

Daha fazla hata ayıklamak isterseniz, etkinlik **günlüğünde** etkinlik kimliğini veya işlemin zaman damgasını kullanarak belirli bir işlemi tanımlayabilirsiniz. Zaman damgası, etkinlik KIMLIĞININ açıkça geçirildiği bazı Kaynak Yöneticisi istemcileri için kullanılır. Etkinlik günlüğü, işlemin başlatıldığı kimliğin ayrıntılarını verir. Aşağıdaki ekran görüntüsünde etkinlik KIMLIĞINI kullanma ve etkinlik günlüğünde onunla ilişkili işlemleri bulma işlemi gösterilmektedir:

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="Etkinlik KIMLIĞINI kullanın ve işlemleri bulun":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Azure Cosmos hesabı için denetim düzlemi işlemleri

Hesap düzeyinde kullanılabilen Denetim düzlemi işlemleri aşağıda verilmiştir. İşlemlerin çoğu hesap düzeyinde izlenir. Bu işlemler, Azure izleyici 'de ölçümler olarak kullanılabilir:

* Eklenen bölge
* Bölge kaldırıldı
* Hesap silindi
* Yük devredilen bölge
* Hesap oluşturuldu
* Sanal ağ silindi
* Hesap Ağ ayarları güncelleştirildi
* Hesap çoğaltma ayarları güncelleştirildi
* Hesap anahtarları güncelleştirildi
* Hesap yedekleme ayarları güncelleştirildi
* Hesap Tanılama ayarları güncelleştirildi

## <a name="control-plane-operations-for-database-or-containers"></a>Veritabanı veya kapsayıcılar için denetim düzlemi işlemleri

Aşağıda, veritabanı ve kapsayıcı düzeyinde kullanılabilen Denetim düzlemi işlemleri verilmiştir. Bu işlemler, Azure izleyici 'de ölçümler olarak kullanılabilir:

* SQL veritabanı oluşturuldu
* SQL veritabanı güncelleştirildi
* SQL veritabanı verimlilik güncelleştirildi
* SQL veritabanı silindi
* Oluşturulan SQL kapsayıcısı
* SQL kapsayıcısı güncelleştirildi
* SQL kapsayıcısı üretilen Işi güncelleştirildi
* SQL kapsayıcısı silindi
* Cassandra keyspace oluşturuldu
* Cassandra keyspace güncelleştirildi
* Cassandra keyspace üretilen Işi güncelleştirildi
* Cassandra keyspace silindi
* Cassandra tablosu oluşturuldu
* Cassandra tablosu güncelleştirildi
* Cassandra tablosu üretilen Işi güncelleştirildi
* Cassandra tablosu silindi
* Gremlin veritabanı oluşturuldu
* Gremlin veritabanı güncelleştirildi
* Gremlin veritabanı performansı güncelleştirildi
* Gremlin veritabanı silindi
* Gremlin grafiği oluşturuldu
* Gremlin grafiği güncelleştirildi
* Gremlin Graf üretilen Işi güncelleştirildi
* Gremlin grafiği silindi
* Mongo veritabanı oluşturuldu
* Mongo veritabanı güncelleştirildi
* Mongo veritabanı üretilen Işi güncelleştirildi
* Mongo veritabanı silindi
* Mongo koleksiyonu oluşturuldu
* Mongo koleksiyonu güncelleştirildi
* Mongo koleksiyon üretilen Işi güncelleştirildi
* Mongo koleksiyonu silindi
* AzureTable tablo oluşturuldu
* AzureTable tablo güncelleştirildi
* AzureTable tablo üretilen Işi güncelleştirildi
* AzureTable tablo silindi

## <a name="diagnostic-log-operations"></a>Tanılama günlüğü işlemleri

Farklı işlemler için tanılama günlüklerindeki işlem adları aşağıda verilmiştir:

* RegionAddStart, Regionaddtamamlanmıştır
* RegionRemoveStart, Regionremovetamamlanmıştır
* AccountDeleteStart, Accountdeletetamamlanmıştır
* RegionFailoverStart, Regionfailovertamamlanmıştır
* AccountCreateStart, Accountcreatetamamlandı
* AccountUpdateStart, Accountupdatetamamlanmıştır
* VirtualNetworkDeleteStart, Virtualnetworkdeletetamamlanmıştır
* DiagnosticLogUpdateStart, Diagnosticlogupdatetamamlanmıştır

API 'ye özgü işlemler için, işlem aşağıdaki biçimde adlandırılır:

* Apıkind + Apıkindresourcetype + OperationType
* ApiKind + Apıkindresourcetype + "aktarım hızı" + operationType

**Örnek** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

*Resourcedetails* özelliği, kaynak gövdesinin tamamını bir istek yükü olarak içerir ve güncelleştirmek için istenen tüm özellikleri içerir

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>Denetim düzlemi işlemleri için tanılama günlüğü sorguları

Aşağıda denetim düzlemi işlemlerine yönelik tanılama günlüklerini almak için bazı örnekler verilmiştir:

```kusto
AzureDiagnostics 
| where Category startswith "ControlPlane"
| where OperationName contains "Update"
| project httpstatusCode_s, statusCode_s, OperationName, resourceDetails_s, activityId_g
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where TimeGenerated >= todatetime('2020-05-14T17:37:09.563Z')
| project TimeGenerated, OperationName, apiKind_s, apiKindResourceType_s, operationType_s, resourceDetails_s
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersUpdate"
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersThroughputUpdate"
```

ActivityID ve kapsayıcı silme işlemini başlatan çağıranı almak için sorgu:

```kusto
(AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName == "SqlContainersDelete"
| where TimeGenerated >= todatetime('9/3/2020, 5:30:29.300 PM')
| summarize by activityId_g )
| join (
AzureActivity
| parse HTTPRequest with * "clientRequestId\": \"" activityId_g "\"" * 
| summarize by Caller, HTTPRequest, activityId_g)
on activityId_g
| project Caller, activityId_g
```

Dizin veya TTL güncelleştirmelerini almak için sorgu. Daha sonra, dizin veya TTL değişikliğini görmek için bu sorgunun çıkışını daha önceki bir güncelleştirmeyle karşılaştırabilirsiniz.

```Kusto
AzureDiagnostics
| where Category =="ControlPlaneRequests"
| where  OperationName == "SqlContainersUpdate"
| project resourceDetails_s
```

**çıktıların**

```json
{id:skewed,indexingPolicy:{automatic:true,indexingMode:consistent,includedPaths:[{path:/*,indexes:[]}],excludedPaths:[{path:/_etag/?}],compositeIndexes:[],spatialIndexes:[]},partitionKey:{paths:[/pk],kind:Hash},defaultTtl:1000000,uniqueKeyPolicy:{uniqueKeys:[]},conflictResolutionPolicy:{mode:LastWriterWins,conflictResolutionPath:/_ts,conflictResolutionProcedure:}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB için Azure Izleyicisini keşfet](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Azure Cosmos DB ölçümlerle izleme ve hata ayıklama](use-metrics.md)
