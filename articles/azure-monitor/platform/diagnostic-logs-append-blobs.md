---
title: Azure Izleyici tanılama günlüklerine biçim değişikliğine hazırlanma
description: Azure tanılama günlükleri, 1 Kasım 2018 ' de Append blob 'ları kullanacak şekilde taşınacak.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: a5589828570455c61f857dbeadc896e8fef27178
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258393"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>Azure Izleyici tanılama günlüklerinde bir depolama hesabına arşivlenen biçim değişikliğine hazırlanma

> [!WARNING]
> [Azure Kaynak tanılama günlüklerini veya ölçümlerini](./../../azure-monitor/platform/archive-diagnostic-logs.md) , [günlük profillerini kullanarak bir depolama hesabına](./../../azure-monitor/platform/archive-activity-log.md)kaynak tanılama ayarlarını ya da etkinlik günlüklerini kullanarak bir depolama hesabına gönderiyorsanız, depolama hesabındaki VERILERIN biçimi, üzerinde JSON satırları olarak değişir Kas. 1, 2018. Aşağıdaki yönergeler etkisini ve yeni biçimi işlemek için araçlarınızın nasıl güncelleşmesini açıklamaktadır. 
>
> 

## <a name="what-is-changing"></a>Yapılacak değişiklikler

Azure Izleyici, Azure Izleyici 'de bir Azure depolama hesabına, Event Hubs ad alanına veya bir Log Analytics çalışma alanına kaynak Tanılama verileri ve etkinlik günlüğü verileri göndermenizi sağlayan bir özellik sunar. Bir sistem performansı sorununa yönelik olarak, **1 kasım 2018 ' de 12:00 gece yarısı UTC** , blob depolamaya gönderilen günlük verilerinin biçimi değişecektir. Blob depolamadan veri okuyan bir araç varsa, yeni veri biçimini anlamak için araç araçlarınızı güncelleştirmeniz gerekir.

* Salı günü, 1 Kasım 2018, 12:00 gece UTC 'de, blob biçimi de [JSON satırları](http://jsonlines.org/)olacak şekilde değişir. Bu, her kaydın bir yeni satır tarafından sınırlandırıldığından, dış kayıt dizisi olmadan ve JSON kayıtları arasında virgül olmaması anlamına gelir.
* Blob biçimi tüm abonelikler genelinde tüm Tanılama ayarları için aynı anda değişir. 1 Kasım 1 için yayılan ilk PT1H. JSON dosyası bu yeni biçimi kullanacaktır. Blob ve kapsayıcı adları aynı kalır.
* Şu an ve 1 Kasım arasında bir tanılama ayarı ayarlanması, verileri geçerli biçimde 1 Kasım 'a kadar yaymaya devam eder.
* Bu değişiklik, tüm genel bulut bölgelerinde bir kez gerçekleşir. Bu değişiklik, 21Vianet tarafından çalıştırılan Microsoft Azure, Azure Almanya veya Azure Kamu bulutlarında henüz gerçekleşmeyecektir.
* Bu değişiklik aşağıdaki veri türlerini etkiler:
  * [Azure Kaynak tanılama günlükleri](archive-diagnostic-logs.md) ([kaynakların listesini buraya bakın](diagnostic-logs-schema.md))
  * [Tanılama ayarları tarafından aktarılan Azure Kaynak ölçümleri](diagnostic-settings.md)
  * [Azure etkinlik günlüğü verileri, günlük profilleri tarafından veriliyor](archive-activity-log.md)
* Bu değişiklik etkilenmez:
  * Ağ akışı günlükleri
  * Azure hizmet günlükleri henüz Azure Izleyici üzerinden kullanılabilir değil (örneğin, tanılama günlükleri, depolama analiz günlükleri Azure App Service)
  * Azure tanılama günlükleri ve etkinlik günlüklerinin diğer hedeflere yönlendirilmesi (Event Hubs Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Etkilenip etkilenmediğinizi görme

Bu değişiklikten yalnızca şunları yaptıysanız etkilenmiş olursunuz:
1. , Bir kaynak tanılama ayarı kullanarak bir Azure depolama hesabına günlük verileri gönderiyor ve
2. Depolama alanındaki bu günlüklerin JSON yapısına bağlı olan araçları izleyin.
 
Bir Azure depolama hesabına veri gönderen kaynak tanılama ayarlarınıza sahip olup olmadığını belirlemek için portalın **izleyici** bölümüne gidebilir, **Tanılama ayarları**' na tıklayabilir ve tanısı olan tüm kaynakları belirleyebilirsiniz  **Durum** **etkin**olarak ayarlandı:

![Azure Izleyici Tanılama ayarları dikey penceresi](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Tanı durumu etkin olarak ayarlanırsa, bu kaynak üzerinde etkin bir tanılama ayarınız vardır. Bir depolama hesabına veri gönderip göndermeyen tanılama ayarlarının olup olmadığını görmek için kaynağa tıklayın:

![Depolama hesabı etkin](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Bu kaynak tanılama ayarlarını kullanarak bir depolama hesabına veri gönderen kaynaklarınız varsa, bu depolama hesabındaki verilerin biçimi bu değişiklikten etkilenir. Bu depolama hesaplarının dışında çalışan özel bir araç yoksa, biçim değişikliği sizi etkilemez.

### <a name="details-of-the-format-change"></a>Biçim değişikliğinin ayrıntıları

Azure Blob depolama 'daki PT1H. json dosyasının geçerli biçimi, bir JSON dizi kayıt kullanır. İşte şu anda bir Keykasası günlük dosyası örneği:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

Yeni biçim, her olayın bir satır olduğu ve yeni bir olayı gösterdiği yeni bir olayı gösteren [JSON hatlarını](http://jsonlines.org/)kullanır. Yukarıdaki örnek, PT1H. json dosyasında değişiklikten sonra şöyle görünür:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Bu yeni biçim, Azure Izleyici 'nin, yeni olay verilerini sürekli olarak eklemek için daha verimli olan [ekleme Blobları](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)kullanarak günlük dosyalarını gönderebilmesine olanak sağlar.

## <a name="how-to-update"></a>Güncelleştirme

Yalnızca bu günlük dosyalarını daha fazla işlemeye yönelik bir özel araç varsa, güncelleştirmeler yapmanız gerekir. Bir dış Log Analytics veya SıEM aracı kullanıyorsanız, [bunun yerine bu verileri almak için Olay Hub 'larını kullanmanızı](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)öneririz. Olay Hub 'ları tümleştirmesi, belirli bir günlükteki birçok hizmet ve yer işareti konumundan günlükleri işleme açısından daha kolay hale getirir.

Özel araçlar, yukarıda açıklanan hem geçerli biçimi hem de JSON satırları biçimini işleyecek şekilde güncellenecektir. Bu, verilerin yeni biçimde görüntülenmesinden sonra araçlarınızın kesintiye uğramadığından emin olur.

## <a name="next-steps"></a>Sonraki adımlar

* [Kaynak tanılama günlüklerini bir depolama hesabına arşivleme](./../../azure-monitor/platform/archive-diagnostic-logs.md) hakkında bilgi edinin
* [Etkinlik günlüğü verilerini bir depolama hesabına arşivleme](./../../azure-monitor/platform/archive-activity-log.md) hakkında bilgi edinin

