---
title: Tanılama günlükleri
titleSuffix: Azure Content Delivery Network
description: Azure tanılama günlüklerini kullanarak Azure Content Delivery Network uç noktanıza kullanım ölçümlerini dışarı aktarabilirsiniz.
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: 96e80de5b8b5ab0a046913ce40ca2d7254dd0133
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100573231"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>Tanılama günlükleri-Azure Content Delivery Network

Azure tanılama günlükleri ile çekirdek analizlerini görüntüleyebilir ve bunları aşağıdakiler dahil olmak üzere bir veya daha fazla hedefe kaydedebilirsiniz:

* Azure Storage hesabı
* Log Analytics çalışma alanı
* Azure Event Hubs

Bu özellik tüm fiyatlandırma katmanları için CDN uç noktalarında kullanılabilir. 

Tanılama günlükleri, temel kullanım ölçümlerini CDN uç noktanıza farklı tür kaynaklarına dışarı aktarıp bunları özelleştirilmiş bir şekilde kullanabilmeniz için izin verir. Aşağıdaki veri dışa aktarma türlerini yapabilirsiniz:

* Verileri blob depolamaya aktarın, CSV 'ye aktarın ve Excel 'de grafikler oluşturun.
* Event Hubs ve diğer Azure hizmetlerinden verilerle bağıntılı verileri dışarı aktarın.
* Azure Izleyici günlüklerine veri aktarma ve kendi Log Analytics çalışma alanınızdaki verileri görüntüleme

Aşağıdaki adımlar için bir Azure CDN profili gereklidir. Devam etmeden önce [Azure CDN profili ve uç noktası oluşturma](cdn-create-new-endpoint.md) bölümüne bakın.

## <a name="enable-logging-with-the-azure-portal"></a>Azure portalıyla günlüğe kaydetmeyi etkinleştirme

Azure CDN uç noktanıza yönelik günlüğü etkinleştirmek için şu adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın. 

2. Azure Portal, **tüm kaynaklar**  ->  **-CDN-profile** ' e gidin

2. Tanılama günlüklerini etkinleştirmek istediğiniz CDN uç noktasını seçin:

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="CDN uç noktası seçin." border="true":::

3. **İzleme** bölümünde **tanılama günlükleri** ' ni seçin:

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="Tanılama günlükleri ' ni seçin." border="true":::

### <a name="enable-logging-with-azure-storage"></a>Azure depolama ile günlüğe kaydetmeyi etkinleştirme

Günlükleri depolamak üzere bir depolama hesabı kullanmak için şu adımları izleyin:

 >[!NOTE] 
 >Bu adımları gerçekleştirmek için bir depolama hesabı gereklidir. Daha fazla bilgi için bkz. **[Azure depolama hesabı oluşturma](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)** .
    
1. **Tanılama ayarı adı** için tanılama günlük ayarlarınız için bir ad girin.
 
2. **Bir depolama hesabına arşiv**' i seçin ve ardından **coreanalytics**' i seçin. 

3. **Bekletme (gün)** için bekletme günü sayısını seçin. Sıfır günlük bir bekletme, günlükleri süresiz olarak depolar. 

4. Günlükler için abonelik ve depolama hesabı seçin.

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="Tanılama günlükleri-depolama." border="true":::

3. **Kaydet**’i seçin.

### <a name="send-to-log-analytics"></a>Log Analytics’e gönderme

Günlükler için Log Analytics kullanmak için şu adımları izleyin:

>[!NOTE] 
>Bu adımları gerçekleştirmek için bir Log Analytics çalışma alanı gereklidir. Daha fazla bilgi için bkz. **[Azure portal Log Analytics çalışma alanı oluşturma](../azure-monitor/logs/quick-create-workspace.md)** .
    
1. **Tanılama ayarı adı** için tanılama günlük ayarlarınız için bir ad girin.

2. **Log Analytics gönder**' i seçin ve ardından **coreanalytics**' i seçin. 

3. Günlükler için aboneliği ve Log Analytics çalışma alanını seçin.

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="Tanılama günlükleri-Log Analytics." border="true":::

4. **Kaydet**’i seçin.

### <a name="stream-to-an-event-hub"></a>Bir olay hub'ına akış yap

Günlükler için bir olay hub 'ı kullanmak için şu adımları izleyin:

>[!NOTE] 
>Bu adımları gerçekleştirmek için bir olay hub 'ı gerekir. Bkz. hızlı başlangıç: daha fazla bilgi için **[Azure Portal kullanarak bir olay hub 'ı oluşturun](../event-hubs/event-hubs-create.md)** .
    
1. **Tanılama ayarı adı** için tanılama günlük ayarlarınız için bir ad girin.

2. **Bir olay hub 'ına akış**' ı seçin ve ardından **coreanalytics**' i seçin. 

3. Günlükler için abonelik ve Olay Hub 'ı ad alanını seçin.

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="Tanılama günlükleri-Olay Hub 'ı." border="true":::

4. **Kaydet**’i seçin.


## <a name="enable-logging-with-powershell"></a>PowerShell ile günlüğe kaydetmeyi etkinleştirme

Aşağıdaki örnek, Azure PowerShell cmdlet 'Leri aracılığıyla tanılama günlüklerinin nasıl etkinleştirileceğini gösterir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Depolama hesabında tanılama günlüklerini etkinleştirme

1. Azure PowerShell oturum açın:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Bir depolama hesabında tanılama günlüklerini etkinleştirmek için, bu komutları girin. Değişkenleri değerlerinizle değiştirin:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Log Analytics çalışma alanı için tanılama günlüklerini etkinleştirme

1. Azure PowerShell oturum açın:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Bir Log Analytics çalışma alanı için tanılama günlüklerini etkinleştirmek üzere, bu komutları girin. Değişkenleri değerlerinizle değiştirin:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Olay Hub 'ı ad alanı için tanılama günlüklerini etkinleştirme

1. Azure PowerShell oturum açın:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Bir Log Analytics çalışma alanı için tanılama günlüklerini etkinleştirmek üzere, bu komutları girin. Değişkenleri değerlerinizle değiştirin:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Azure Depolama'dan tanılama günlüklerini kullanma
Bu bölümde, bir Azure depolama hesabında bulunan ve bir CSV dosyasındaki günlükleri indirmek için örnek kod sağlayan CDN Core Analytics şeması açıklanmaktadır.

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure Depolama Gezgini kullanma
Aracı indirmek için bkz. [Azure Depolama Gezgini](https://storageexplorer.com/). Yazılımı indirip yükledikten sonra, CDN tanılama günlüklerine hedef olarak yapılandırılmış aynı Azure Depolama hesabını kullanacak şekilde yapılandırın.

1.  **Microsoft Azure Depolama Gezgini** açın
2.  Depolama hesabını bulma
3.  Bu depolama hesabı altındaki **BLOB kapsayıcıları** düğümünü genişletin.
4.  *Öngörüler-logs-coreanalytics* adlı kapsayıcıyı seçin.
5.  Sonuçlar, *RESOURCEID =* olarak ilk düzeyden başlayarak sağ bölmedeki görünür. *ÜzerindePT1H.js* dosyayı bulana kadar her bir düzeyi seçmeye devam edin. Yolun açıklaması için bkz. [BLOB yol biçimi](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Her blob *PT1H.js* dosyasında, belırlı bir CDN uç noktası veya özel etki alanı için bir saat için analiz günlükleri temsil eder.
7.  Bu JSON dosyasının içeriğinin şeması, çekirdek analiz günlüklerinin bölüm şemasında açıklanmaktadır.


#### <a name="blob-path-format"></a>Blob yol biçimi

Çekirdek analizi günlükleri saatte bir oluşturulur ve veriler toplanır ve bir JSON yükü olarak tek bir Azure blobunun içinde depolanır. Depolama Gezgini Aracı, '/' öğesini dizin ayırıcı olarak yorumlar ve hiyerarşiyi gösterir. Azure Blob 'un yolu, hiyerarşik bir yapı gibi görünür ve BLOB adını temsil eder. Blobun adı aşağıdaki adlandırma kuralına uyar:    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Alanların açıklaması:**

|Değer|Açıklama|
|-------|---------|
|Abonelik Kimliği    |GUID biçiminde Azure aboneliğinin KIMLIĞI.|
|Kaynak Grubu Adı |CDN kaynaklarının ait olduğu kaynak grubunun adı.|
|Profil Adı |CDN profilinin adı|
|Uç nokta adı |CDN uç noktasının adı|
|Yıl|  Yılın dört basamaklı temsili, örneğin, 2017|
|Ay| Ay numarasının iki basamaklı temsili. 01 = Ocak... 12 = Aralık|
|Gün|   Ayın gününün iki basamaklı temsili|
|Üzerinde PT1H.js| Analiz verilerinin depolandığı gerçek JSON dosyası|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Temel analiz verilerini bir CSV dosyasına aktarma

Çekirdek analizine erişmek için, bir araç için örnek kod sağlanır. Bu araç, JSON dosyalarını grafik veya diğer toplamaları oluşturmak için kullanılabilecek düz bir virgülle ayrılmış dosya biçimine indirmeyi sağlar.

Aracı şu şekilde kullanabilirsiniz:

1.  GitHub bağlantısını ziyaret edin: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Kodu indirin.
3.  Derlemek ve yapılandırmak için yönergeleri izleyin.
4.  Aracı çalıştırın.
5.  Elde edilen CSV dosyası, analiz verilerini basit bir düz hiyerarşide gösterir.

## <a name="log-data-delays"></a>Günlük verileri gecikmeleri

Aşağıdaki tabloda **Microsoft 'tan Azure CDN Standart** için günlük verisi gecikmeleri **Azure CDN, Akamai 'Ten standart** ve **Verizon 'den Standart/Premium Azure CDN** gösterilmektedir.

Microsoft günlük veri gecikmeleri | Verizon günlük verileri gecikmeleri | Akamai günlük verileri gecikmeleri
--- | --- | ---
1 saat gecikiyor. | 1 saat gecikmeli ve uç nokta yayma tamamlandıktan sonra görüntülenecek 2 saate kadar zaman alabilir. | 24 saat gecikiyor; 24 saatten uzun bir süre önce oluşturulduysa, görünün başlaması 2 saate kadar sürer. Yakın zamanda oluşturulduysa, günlüklerin görünmeye başlaması 25 saate kadar sürebilir.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>CDN çekirdek analizi için tanılama günlüğü türleri

Microsoft şu anda yalnızca CDN pop 'Ları/kenarlarından görüldüğü gibi HTTP yanıt istatistiklerini ve çıkış istatistiklerini gösteren ölçümleri içeren temel analiz günlükleri sunmaktadır.

### <a name="core-analytics-metrics-details"></a>Çekirdek analizi ölçüm ayrıntıları
Aşağıdaki tabloda, için çekirdek analiz günlüklerinde bulunan ölçümlerin bir listesi gösterilmektedir:

* **Microsoft 'tan Azure CDN Standart**
* **Akamai 'ten standart Azure CDN**
* **Verizon 'ten Standart/Premium Azure CDN**

Tüm ölçümler tüm sağlayıcılardan kullanılamaz, ancak bu farklar en az düzeydedir. Tablo aynı zamanda belirli bir metriğin sağlayıcıdan kullanılabilir olup olmadığını gösterir. Ölçümler yalnızca üzerinde trafiği bulunan CDN uç noktaları için kullanılabilir.


|Metric                     | Açıklama | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Bu süre boyunca toplam istek isabet sayısı. | Yes | Yes |Yes |
| RequestCountHttpStatus2xx | 2xx HTTP kodu ile sonuçlanan tüm isteklerin sayısı (örneğin, 200, 202). | Yes | Yes |Yes |
| RequestCountHttpStatus3xx | 3xx HTTP kodu ile sonuçlanan tüm isteklerin sayısı (örneğin, 300, 302). | Yes | Yes |Yes |
| RequestCountHttpStatus4xx | 4xx HTTP kodu ile sonuçlanan tüm isteklerin sayısı (örneğin, 400, 404). | Yes | Yes |Yes |
| RequestCountHttpStatus5xx | 5xx HTTP kodu ile sonuçlanan tüm isteklerin sayısı (örneğin, 500, 504). | Yes | Yes |Yes |
| RequestCountHttpStatusOthers | Diğer tüm HTTP kodlarının sayısı (2xx-5xx dışında). | Yes | Yes |Yes |
| RequestCountHttpStatus200 | 200 HTTP kod yanıtıyla sonuçlanan tüm isteklerin sayısı. | Yes | Hayır  |Yes |
| RequestCountHttpStatus206 | 206 HTTP kod yanıtıyla sonuçlanan tüm isteklerin sayısı. | Yes | Hayır  |Yes |
| RequestCountHttpStatus302 | 302 HTTP kod yanıtıyla sonuçlanan tüm isteklerin sayısı. | Yes | Hayır  |Yes |
| RequestCountHttpStatus304 | 304 HTTP kod yanıtıyla sonuçlanan tüm isteklerin sayısı. | Yes | Hayır  |Yes |
| RequestCountHttpStatus404 | 404 HTTP kod yanıtıyla sonuçlanan tüm isteklerin sayısı. | Yes | Hayır  |Yes |
| RequestCountCacheHit | Önbellek okuması ile sonuçlanan tüm isteklerin sayısı. Varlık doğrudan POP 'tan istemciye sunulur. | Yes | Yes | Hayır  |
| Requestcountcacheisabetsizlik | Önbellek isabetsizliği ile sonuçlanan tüm isteklerin sayısı. Bir önbellek isabetsizlik, varlığın istemciye en yakın POP üzerinde bulunamadığını ve kaynaktan alındığını gösterir. | Yes | Yes | Hayır |
| RequestCountCacheNoCache | Bir varlık için, uçta Kullanıcı yapılandırması nedeniyle önbelleğe alınması engellenen tüm isteklerin sayısı. | Yes | Yes | Hayır |
| RequestCountCacheUncacheable | Varlık Cache-Control ve süre sonu üstbilgileri tarafından önbelleğe alınması engellenen varlıkların tüm isteklerinin sayısı. Bu sayı, bir POP veya HTTP istemcisi tarafından önbelleğe alınmaması gerektiğini gösterir. | Yes | Yes | Hayır |
| Requestcountcachediğerleri | Yukarıda yer almayan önbellek durumuna sahip tüm isteklerin sayısı. | Hayır | Yes | Hayır  |
| Yumurresstotal | GB cinsinden giden veri aktarımı | Yes |Yes |Yes |
| EgressHttpStatus2xx | GB cinsinden 2xx HTTP durum koduna sahip yanıtlar için giden veri aktarımı *. | Yes | Yes | Hayır  |
| EgressHttpStatus3xx | GB olarak 3xx HTTP durum koduna sahip yanıtlar için giden veri aktarımı. | Yes | Yes | Hayır  |
| EgressHttpStatus4xx | GB cinsinden 4xx HTTP durum koduna sahip yanıtlar için giden veri aktarımı. | Yes | Yes | Hayır  |
| EgressHttpStatus5xx | GB olarak 5 xx HTTP durum koduna sahip yanıtlar için giden veri aktarımı. | Yes | Yes | Hayır |
| EgressHttpStatusOthers | GB olarak diğer HTTP durum kodlarına sahip yanıtlar için giden veri aktarımı. | Yes | Yes | Hayır  |
| Yumurresscachehit | CDN pop 'Ları/kenarları üzerindeki CDN önbelleğinden doğrudan teslim edilen yanıtlar için giden veri aktarımı. | Yes | Yes | Hayır |
| Yumurresscacheisabetsizlik. | En yakın POP sunucusunda bulunmayan ve kaynak sunucudan alınan yanıtlar için giden veri aktarımı. | Yes | Yes | Hayır |
| Yumurresscachenocache | Kenarda Kullanıcı yapılandırması nedeniyle önbelleğe alınması engellenen varlıkların giden veri aktarımı. | Yes | Yes | Hayır |
| Yumurresscacheuncacheable | Varlığın Cache-Control ve veya süre sonu üstbilgileri tarafından önbelleğe alınması engellenen varlıkların giden veri aktarımı. Bir POP veya HTTP istemcisi tarafından önbelleğe alınmaması gerektiğini gösterir. | Yes | Yes | Hayır |
| Yumurresscachediğerleri | Diğer önbellek senaryoları için giden veri aktarımları. | Hayır | Yes | Hayır |

* Giden veri aktarımı, CDN POP sunucularından istemciye teslim edilen trafiğin anlamına gelir.


### <a name="schema-of-the-core-analytics-logs"></a>Çekirdek analiz günlüklerinin şeması 

Tüm Günlükler JSON biçiminde depolanır ve her girdinin aşağıdaki şemaya göre dize alanları vardır:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Burada *saat* , istatistiklerin bildirildiği saat sınırının başlangıç saatini gösterir. Bir CDN sağlayıcısı tarafından bir Double veya integer değeri yerine desteklenmeyen bir ölçüm, null değer değeriyle sonuçlanır. Bu null değer, bir ölçümün yokluğunu gösterir ve 0 değerinden farklıdır. Her etki alanı için bu ölçümler kümesi uç noktada yapılandırılır.

Örnek Özellikler:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure tanılama günlükleri](../azure-monitor/essentials/platform-logs-overview.md)
* [Azure CDN ek Portal aracılığıyla temel analiz](./cdn-analyze-usage-patterns.md)
* [Azure İzleyici günlükleri](../azure-monitor/logs/log-query-overview.md)
* [Azure Log Analytics REST API](/rest/api/loganalytics)