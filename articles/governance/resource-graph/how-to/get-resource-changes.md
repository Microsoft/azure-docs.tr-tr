---
title: Kaynak değişikliklerini alma
description: Bir kaynağın ne zaman değiştirildiğini bulmayı ve değiştirilen özelliklerin bir listesini almayı anlayın.
ms.date: 10/09/2019
ms.topic: conceptual
ms.openlocfilehash: 25d900276518e51e80021d1fdb1ac80945eb2d5c
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958754"
---
# <a name="get-resource-changes"></a>Kaynak değişikliklerini alma

Günlük kullanım sırasında, yeniden yapılandırma ve hatta yeniden dağıtım yapıldığında kaynaklar değiştirilir.
Değişiklik, bir bireden veya otomatikleştirilmiş bir işlemden gelebilir. Çoğu değişiklik tasarıma göre yapılır, ancak bazen değildir. Değişiklik geçmişinin son 14 günü ile Azure Kaynak Grafiği şunları yapmanızı sağlar:

- Azure Resource Manager özelliğinde değişikliklerin ne zaman algılandığını bulma
- Her kaynak değişikliği için bkz. özellik değişiklik ayrıntıları
- Algılanan değişiklikten önce ve sonra kaynağın tam karşılaştırmasına bakın

Değişiklik algılama ve Ayrıntılar aşağıdaki örnek senaryolar için değerlidir:

- Olay yönetimi sırasında, _potansiyel_ olarak ilgili değişiklikleri anlayın. Belirli bir zaman penceresi sırasında değişiklik olayları için sorgulama yapın ve değişiklik ayrıntılarını değerlendirin.
- CMDB olarak bilinen bir yapılandırma yönetimi veritabanının tutulması, güncel. Tüm kaynakları ve bunların tam özellik kümelerini zamanlanan bir sıklıkta yenilemek yerine yalnızca nelerin değiştiğini alın.
- Bir kaynağın uyumluluk durumu değiştiğinde başka hangi özelliklerin değiştirildiğini anlama. Bu ek özelliklerin değerlendirilmesi, bir Azure Ilke tanımıyla yönetilmesi gerekebilecek diğer özelliklerle ilgili öngörüler sağlayabilir.

Bu makalede, kaynak grafiğinin SDK 'Sı aracılığıyla bu bilgilerin nasıl toplanacağı gösterilmektedir. Bu bilgileri Azure portal görmek için bkz. Azure Ilkesinin [değişiklik geçmişi](../../policy/how-to/determine-non-compliance.md#change-history-preview) veya Azure etkinlik günlüğü [değişiklik geçmişi](../../../azure-monitor/platform/activity-log-view.md#azure-portal).
Altyapı katmanından uygulama dağıtımına kadar olan uygulamalardaki değişiklikler hakkında ayrıntılı bilgi için bkz. Azure Izleyici 'de [uygulama değişiklik analizini (Önizleme) kullanma](../../../azure-monitor/app/change-analysis.md) .

> [!NOTE]
> Kaynak grafiğinde değişiklik ayrıntıları Kaynak Yöneticisi özellikleri içindir. Bir sanal makine içindeki değişiklikleri izlemek için bkz. Azure Otomasyonu 'nun [VM 'ler Için](../../policy/concepts/guest-configuration.md) [değişiklik Izleme](../../../automation/automation-change-tracking.md) veya Azure ilkesinin Konuk yapılandırması.

> [!IMPORTANT]
> Azure Kaynak grafiğindeki değişiklik geçmişi genel önizlemede.

## <a name="find-detected-change-events-and-view-change-details"></a>Algılanan değişiklik olaylarını bul ve değişiklik ayrıntılarını görüntüle

Bir kaynakta nelerin değiştiğini görmekte olan ilk adım, bu kaynakla ilgili değişiklik olaylarını bir zaman penceresi içinde bulmadır. Her değişiklik olayı, kaynakta nelerin değiştirildiklerin ayrıntılarını da içerir. Bu adım, **Resourcechanges** REST uç noktası aracılığıyla yapılır.

**Resourcechanges** uç noktası, istek gövdesinde aşağıdaki parametreleri kabul eder:

- **resourceıd** \[gerekli\]: üzerinde değişiklik aranacak Azure kaynağı.
- **aralık** \[gerekli\]: **Zulu saat dilimi (Z)** kullanılarak bir değişiklik olayının ne zaman denetleneceği için _Başlangıç_ ve _bitiş_ tarihleri içeren bir özellik.
- **Fetchpropertychanges** (isteğe bağlı): Response nesnesi özellik değişikliklerini içeriyorsa ayarlayan Boolean özellik.

Örnek istek gövdesi:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

Yukarıdaki istek gövdesiyle, **Resourcechanges** için REST API URI 'si şu şekilde olur:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Yanıt aşağıdaki örneğe benzer şekilde görünür:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

**RESOURCEID** için algılanan her değişiklik olayı aşağıdaki özelliklere sahiptir:

- **Changeıd** -bu değer bu kaynak için benzersizdir. **Changeıd** dizesi bazen başka özellikler de içerebilirken, yalnızca benzersiz olması garanti edilir.
- **Beforesnapshot** -bir değişiklik algılanmadan önce alınan kaynak anlık görüntüsünün **anlık görüntü kimliğini** ve **zaman damgasını** içerir.
- **Aftersnapshot** -bir değişiklik algılandıktan sonra gerçekleştirilen kaynak anlık görüntüsünün **anlık görüntü kimliğini** ve **zaman damgasını** içerir.
- **ChangeType** - **Beforesnapshot** ve **aftersnapshot**arasındaki değişiklik kaydının tamamı için algılanan değişikliğin türünü açıklar. Değerler şunlardır: _Oluştur_, _Güncelleştir_ve _Sil_. **PropertyChanges** Özellik dizisi yalnızca **ChangeType** _güncelleştirme_olduğunda dahil edilir.
- **PropertyChanges** -bu özellik dizisi, **Beforesnapshot** ve **aftersnapshot**arasında güncelleştirilmiş kaynak özelliklerinin tümünü ayrıntılardır:
  - **PropertyName** -değiştirilen kaynak özelliğinin adı.
  - **Changecategory** -değişikliğin ne yaptığını açıklar. Değerler şunlardır: _sistem_ ve _Kullanıcı_.
  - **ChangeType** -tek kaynak özelliği için algılanan değişikliğin türünü açıklar.
    Değerler şunlardır: _Insert_, _Update_, _Remove_.
  - **Beforevalue** - **Beforesnapshot**içindeki Resource özelliğinin değeri. **ChangeType** _eklendiğinde gösterilmez._
  - **aftervalue** - **aftersnapshot**içindeki Resource özelliğinin değeri. **ChangeType** 'ı _kaldırırken_gösterilmez.

## <a name="compare-resource-changes"></a>Kaynak değişikliklerini karşılaştırın

**Resourcechanges** uç noktasındaki **Changeıd** Ile, **resourcechangedetails** REST uç noktası daha sonra değiştirilen kaynağın önceki ve sonraki anlık görüntülerini almak için kullanılır.

**Resourcechangedetails** uç noktası, istek gövdesinde iki parametre gerektirir:

- **RESOURCEID**: değişiklikleri karşılaştırmak için Azure kaynağı.
- **Changeıd**: **resourcechanges**'ten toplanan **RESOURCEID** için benzersiz değişiklik olayı.

Örnek istek gövdesi:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Yukarıdaki istek gövdesiyle, **Resourcechangedetails** için REST API URI 'si:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Yanıt aşağıdaki örneğe benzer şekilde görünür:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**Beforesnapshot** ve **aftersnapshot** her biri anlık görüntünün alındığı zamanı ve o anda özellikleri sağlar. Değişiklik, bu anlık görüntüler arasındaki bir noktada gerçekleşti. Yukarıdaki örneğe bakarak, değiştirilen özelliğin **supportsHttpsTrafficOnly**olduğunu görebiliriz.

Sonuçları karşılaştırmak için, **Resourcechanges** içindeki **Changes** özelliğini kullanın veya her bir anlık görüntünün **içerik** bölümünü, bu farkı öğrenmek için **resourcechangedetails** ' de değerlendirin. Anlık görüntüleri karşılaştırırsanız, zaman **damgası** beklenmesine rağmen her zaman fark olarak gösterilir.

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Başlangıç sorgularında](../samples/starter.md)kullanılan dil.
- Gelişmiş [sorgularda](../samples/advanced.md)gelişmiş kullanımlar bölümüne bakın.
- [Kaynakları araştırma](../concepts/explore-resources.md)hakkında daha fazla bilgi edinin.