---
title: Azure purview 'ı izleme
description: Azure Izleyici 'yi kullanarak Azure purview ölçümlerini, uyarılarını ve tanılama ayarlarını yapılandırmayı öğrenin.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 4cb3965d359980856c238cd563ed8b761754660b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667740"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Azure Izleyici 'de Azure purview ölçümleri

Bu makalede Azure Izleyici kullanarak Azure purview için ölçüm, uyarı ve tanılama ayarlarının nasıl yapılandırılacağı açıklanır.

## <a name="monitor-azure-purview"></a>Azure takip görünümünü izleme

Azure purview yöneticileri, purview hesabının işletimsel durumunu izlemek için Azure Izleyici 'yi kullanabilir. Ölçümler, olası sorunları izlemenize, sorun gidermenize ve purview hesabının güvenilirliğini iyileştirmenize yönelik veri noktaları sağlamak için toplanır. Ölçümler, Azure takip görünümünde oluşan olaylar için Azure izleyici 'ye gönderilir.

## <a name="aggregated-metrics"></a>Toplu ölçümler

Ölçümlere, bir purview hesabı için Azure portal erişilebilir. Ölçümlere erişim, purview hesabının rol ataması tarafından denetlenir. Ölçümleri görmek için kullanıcıların Azure purview 'daki "Izleme okuyucu" rolünün bir parçası olması gerekir. Rol erişim düzeyleri hakkında daha fazla bilgi edinmek için [Izleme okuyucu rolü izinlerine](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) göz atın.

Purview hesabını oluşturan kişi, ölçümleri görüntüleme izinlerini otomatik olarak alır. Başka herkes ölçümleri görmek istiyorsa, aşağıdaki adımları izleyerek bunları **Izleme okuyucu** rolüne ekleyin:

### <a name="add-a-user-to-the-monitoring-reader-role"></a>Izleme okuyucusu rolüne kullanıcı ekleme

**Izleme okuyucusu** rolüne bir kullanıcı eklemek Için, purview hesabının sahibi veya abonelik sahibi aşağıdaki adımları izleyebilir:

1. [Azure Portal](https://portal.azure.com) gidin ve Azure purview hesap adını arayın.

2. **Erişim denetimi (IAM)** öğesini seçin.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="IAM 'e nasıl erişileceğini gösteren ekran görüntüsü.":::

3. **Rol ataması Ekle**' yi seçin.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="Rol atamasının nasıl ekleneceğini gösteren ekran görüntüsü.":::

4. Rol **Izleme okuyucusunu** seçin ve **Azure AD kullanıcısına, grubuna veya hizmet sorumlusuna** ata erişimi ayarlayın. Ve ölçümlere erişmek için AAD hesabını atayın.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="İzleme okuyucusu rolünün nasıl ekleneceğini gösteren ekran görüntüsü.":::

## <a name="metrics-visualization"></a>Ölçüm görselleştirme

**Izleme okuyucusu** rolündeki kullanıcılar, Azure izleyici 'ye gönderilen toplu ölçümleri ve tanılama günlüklerini görebilir. Ölçümler, ilgili purview hesabı için Azure portal listelenir. Azure portal, tüm kullanılabilir ölçümlerin listesini görmek için ölçümler bölümünü seçin.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="Kullanılabilir purview ölçümleri bölümünü gösteren ekran görüntüsü." lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Azure purview kullanıcıları ayrıca ölçümler sayfasına doğrudan Azure purview hesabının yönetim merkezinden de erişebilirler. Azure portal başlatmak için purview Yönetim Merkezi 'nin ana sayfasında Azure Izleyicisi ' ni seçin.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="Yönetim Merkezi 'nden purview ölçümlerini başlatmak için ekran görüntüsü." lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>Mevcut ölçümler

Azure portal ölçüm bölümünün nasıl kullanılacağına ilişkin familiarized almak için aşağıdaki iki belgeyi önceden okuyun. Ölçüm Gezgini ve [Gelişmiş Özellikler](../azure-monitor/essentials/metrics-charts.md)Ölçüm Gezgini [ile çalışmaya](../azure-monitor/essentials/metrics-getting-started.md) başlama.

Aşağıdaki tablo, Azure portal araştırmak için kullanılabilen ölçümlerin listesini içerir:

| Ölçüm Adı | Ölçüm Ad Alanı | Toplama türü | Description |
| ------------------- | ------------------- | ------------------- | ----------------- |
| Tarama Iptal edildi | Otomatik tarama | Sum <br> Count | Zaman aralığı için iptal edilen veri kaynağı taramalarının toplamı |
| Tarama tamamlandı | Otomatik tarama | Sum <br> Count | Zaman aralığı boyunca tamamlanan veri kaynağı taramalarının toplamı |
| Tarama başarısız oldu | Otomatik tarama | Sum <br> Count | Zaman aralığı boyunca başarısız veri kaynağı taramalarının toplamı |
| Harcanan Tarama süresi | Otomatik tarama | Min <br> En yüksek değer <br> Sum <br> Ort | Zaman aralığı içinde taramalar tarafından alınan toplam süreyi toplama |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Azure depolama hesabına yönelik tanılama günlükleri

Ham telemetri olayları Azure Izleyici 'ye dağıtılır. Olaylar, daha fazla analiz için tercih edilen bir müşteri depolama hesabına kaydedilebilir. Günlüklerin dışarı aktarılması, Azure portal purview hesabının Tanılama ayarları aracılığıyla yapılır.

Azure purview hesabınız için bir tanılama ayarı oluşturma adımlarını izleyin.

1. Şu makaleyi izleyerek platform günlüklerini ve ölçümlerini toplamak için yeni bir tanılama ayarı oluşturun: [Platform günlüklerini ve ölçümlerini farklı hedeflere göndermek için Tanılama ayarları oluşturma](../azure-monitor/essentials/diagnostic-settings.md). Hedefi yalnızca Azure Storage hesabı olarak seçin.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="Tanılama günlüğü oluşturmayı gösteren ekran görüntüsü." lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. Olayları bir depolama hesabına kaydedin. Tanılama günlüklerini arşivlemek için ayrılmış bir depolama hesabı önerilir. [Bir depolama hesabı oluşturmak](../storage/common/storage-account-create.md?tabs=azure-portal)için bu makaleyi takip edin.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="Tanılama günlüğü için depolama hesabı atamayı gösteren ekran görüntüsü." lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

Yeni oluşturulan depolama hesabında günlük almaya başlamak için 15 dakikaya kadar izin verin. [Bkz. Azure Storage hesabındaki kaynak günlüklerinin veri saklama ve şema](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Tanılama günlükleri yapılandırıldıktan sonra olaylar depolama hesabına akar.

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

Olay, tarama yaşam döngüsünü izler. Bir tarama işlemi, sıraya alınan, çalışan ve son olarak bir Terminal durumundan başarıyla bir durum sırası aracılığıyla ilerleme izler | Başarısız oldu | Edilmesi. Her durum geçişi için bir olay günlüğe kaydedilir ve olay şeması aşağıdaki özelliklere sahip olur.

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

Olay örneği için örnek günlük aşağıdaki bölümde gösterilmiştir.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>Sonraki adımlar

[Varlık öngörülerini görüntüle](asset-insights.md)