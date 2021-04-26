---
title: Windows Azure tanılama uzantısı 'ndan Azure Event Hubs veri gönderme
description: Azure Izleyici 'de tanılama uzantısı 'nı, Azure 'un dışındaki konumlara iletmek için Azure Olay Hub 'ına veri göndermek üzere yapılandırın.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 6aa960eb7aed7a579c3fc95748b6e32601201a61
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305132"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Windows Azure tanılama uzantısı 'ndan Azure Event Hubs veri gönderme
Azure tanılama uzantısı, Azure Izleyici 'de Konuk işletim sisteminden ve Azure sanal makinelerinin ve diğer işlem kaynaklarının iş yüklerinden izleme verilerini toplayan bir aracıdır. Bu makalede, Azure 'un dışındaki konumlara iletmek için Microsoft Azure tanılama uzantısı 'ndan (WAD) [azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) veri gönderme işleminin nasıl yapılacağı açıklanır.

## <a name="supported-data"></a>Desteklenen veriler

Event Hubs 'e gönderilebilecek Konuk işletim sisteminden toplanan veriler aşağıdakileri içerir. WAD tarafından toplanan ve IIS günlükleri ve kilitlenme dökümleri dahil diğer veri kaynakları Event Hubs gönderilemez.

* Windows için Olay İzleme (ETW) olayları
* Performans sayaçları
* Windows olay günlüğü 'nde uygulama günlükleri de dahil olmak üzere Windows olay günlükleri
* Azure Tanılama altyapısı günlükleri

## <a name="prerequisites"></a>Önkoşullar

* Windows Tanılama uzantısı 1,6 veya üzeri. Desteklenen kaynaklar için [Azure tanılama uzantısı yapılandırma şeması sürümleri ve geçmiş](diagnostics-extension-versions.md) bir sürüm geçmişi ve [Azure tanılama uzantısına genel bakış](diagnostics-extension-overview.md) bölümüne bakın.
* Event Hubs ad alanı her zaman sağlanmalıdır. Ayrıntılar için bkz. [Event Hubs ile çalışmaya başlama](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) .


## <a name="configuration-schema"></a>Yapılandırma şeması
Yapılandırma şemasının bir başvurusu için tanılama uzantısını ve [Azure tanılama yapılandırma şemasını](diagnostics-extension-schema-windows.md) etkinleştirmek ve yapılandırmak üzere farklı seçenekler için bkz. [Windows Azure tanılama uzantısı 'nı (wad) yükleyip](diagnostics-extension-windows-install.md) yapılandırma. Bu makalenin geri kalanında, bu yapılandırmanın bir olay hub 'ına veri göndermek için nasıl kullanılacağı açıklanır. 

Azure Tanılama her zaman günlükleri ve ölçümleri bir Azure depolama hesabına gönderir. Ek konumlara veri gönderen bir veya daha fazla *veri havuzları* yapılandırabilirsiniz. Her havuz, genel yapılandırmanın [Sinksconfig öğesinde](diagnostics-extension-schema-windows.md#sinksconfig-element) özel yapılandırmadaki hassas bilgilerle tanımlanır. Bu olay hub 'ları yapılandırması aşağıdaki tablodaki değerleri kullanır.

| Özellik | Açıklama |
|:---|:---|
| Ad | Havuz için tanımlayıcı ad. Yapılandırmada, havuza hangi veri kaynaklarının gönderileceğini belirtmek için kullanılır. |
| Url  | . Servicebus.windows.net/formundaki Olay Hub 'ının URL 'si \<event-hubs-namespace\> \<event-hub-name\> .          |
| SharedAccessKeyName | En az **gönderme** yetkisine sahip olay hub 'ı için paylaşılan erişim ilkesinin adı. |
| SharedAccessKey     | Olay Hub 'ı için paylaşılan erişim ilkesinden birincil veya ikincil anahtar. |

Örnek ortak ve özel konfigürasyonlar aşağıda gösterilmiştir. Bu, Olay Hub 'ı veri havuzunun nasıl yapılandırılacağını ve kullanılacağını göstermek için tek bir performans sayacı ve olay günlüğü ile en az bir yapılandırmadır. Daha karmaşık bir örnek için bkz. [Azure tanılama yapılandırma şeması](diagnostics-extension-schema-windows.md) .

### <a name="public-configuration"></a>Ortak yapılandırma

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Özel yapılandırma

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Yapılandırma seçenekleri
Veri havuzuna veri göndermek için, veri kaynağının düğümünde **havuzlar** özniteliğini belirtirsiniz. **Havuz** özniteliğini yerleştirdiğiniz yerdir, atamanın kapsamını belirler. Aşağıdaki örnekte, **havuzlar** özniteliği, tüm alt performans sayaçlarının Olay Hub 'ına gönderilmesine neden olacak **PerformanceCounters** düğümü olarak tanımlanmıştır.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


Aşağıdaki örnekte, **havuzlar** özniteliği yalnızca bu performans sayaçlarının Olay Hub 'ına gönderilmesine neden olacak üç sayaca doğrudan uygulanır. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Yapılandırma doğrulanıyor
Verilerin Olay Hub 'ına gönderildiğini doğrulamak için çeşitli yöntemler kullanabilirsiniz. basit yöntem, [Azure Blob depolamada veya Azure Data Lake Storage azure Event Hubs aracılığıyla olayları yakalama](../../event-hubs/event-hubs-capture-overview.md)bölümünde açıklandığı gibi Event Hubs yakalamayı kullanmaktır. 


## <a name="troubleshoot-event-hubs-sinks"></a>Event Hubs havuzları sorunlarını giderme

- Azure Tanılama yönelik Günlükler ve hatalar içeren Azure Storage Table **WADDiagnosticInfrastructureLogsTable** ' a bakın. Tek bir seçenek, bu depolama hesabına bağlanmak, bu tabloyu görüntülemek ve son 24 saat içinde zaman damgasına bir sorgu eklemek için [Azure Depolama Gezgini](https://www.storageexplorer.com) gibi bir araç kullanmaktır. Aracı, bir. csv dosyasını dışarı aktarmak ve Microsoft Excel gibi bir uygulamada açmak için kullanabilirsiniz. Excel, hangi hatanın raporlandığını görmek için **Eventhubs** gibi arama kartı dizelerini aramanızı kolaylaştırır.  

- Olay Hub 'ınızın başarıyla sağlandığını denetleyin. Yapılandırmanın **Privateconfig** bölümündeki tüm bağlantı bilgileri, portalda görüldüğü gibi kaynağınızın değerleriyle aynı olmalıdır. Portalda bir SAS ilkesinin tanımlanmış (örnekteki *Sendrule* ) olduğundan ve *gönderme* izninin verildiğinden emin olun.  

## <a name="next-steps"></a>Sonraki adımlar

* [Event Hubs genel bakış](../../event-hubs/event-hubs-about.md)
* [Olay Hub 'ı oluşturma](../../event-hubs/event-hubs-create.md)
* [Event Hubs ile ilgili SSS](../../event-hubs/event-hubs-faq.yml)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



