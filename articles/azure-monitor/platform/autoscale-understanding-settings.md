---
title: Azure İzleyici otomatik ölçeklendirme ayarlarını anlama
description: Otomatik ölçeklendirme ayarları ve nasıl çalıştıkları ayrıntılı bir dökümü. Sanal makineler, bulut Hizmetleri, Web uygulamaları için geçerlidir
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 02840b8a909f46c37130bdb7162674c694a0ff96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60787504"
---
# <a name="understand-autoscale-settings"></a>Otomatik Ölçeklendirme ayarlarını anlama
Otomatik ölçeklendirme ayarları, uygulamanızın dalgalı yükü işlemek için çalışan kaynakları doğru miktarda sahip olduğunuzdan emin olun yardımcı olur. Yük veya performans ölçümleri temelinde veya Tetiklenmiş bir zamanlanan tarih ve saatte tetiklenmesi için otomatik ölçeklendirme ayarlarını yapılandırabilirsiniz. Bu makalede, bir otomatik ölçeklendirme ayarı anatomisi ayrıntılı bilgi alır. Makale bir ayarın özelliklerini ve şema ile başlar ve yapılandırılabilir farklı profil türleri aracılığıyla size yol gösterir. Son olarak, bu makalede, azure'da otomatik ölçeklendirme özelliği belirli bir zamanda yürütmek için hangi profilin nasıl değerlendirir ele alınmaktadır.

## <a name="autoscale-setting-schema"></a>Otomatik ölçeklendirme ayarı şeması
Otomatik ölçeklendirme ayarı şema göstermek için aşağıdaki otomatik ölçeklendirme ayarı kullanılır. Bu otomatik ölçeklendirme ayarı olduğuna dikkat edin önemlidir:
- Bir profili. 
- Bu profildeki iki ölçüm kuralları: biri ölçek genişletme için ve biri ölçeğini daraltma.
  - Sanal makine ölçek kümesinin ortalama yüzdesi CPU ölçüm son 10 dakika için yüzde 85'den büyük olduğunda ölçek genişletme kuralı tetiklenir.
  - Ölçek daraltma kuralı, sanal makine ölçek kümesinin ortalama yüzde 60'den az olduğunda tetiklenir son dakika.

> [!NOTE]
> Bir ayarı birden çok profil var. Daha fazla bilgi için bkz. [profilleri](#autoscale-profiles) bölümü. Bir profil, birden çok ölçek genişletme kuralları ve ölçeğini tanımlanan kuralları da olabilir. Nasıl değerlendirilir görmek için bkz: [değerlendirme](#autoscale-evaluation) bölümü.

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| `Section` | Öğe adı | Açıklama |
| --- | --- | --- |
| Ayar | id | Otomatik ölçeklendirme ayarının kaynak kimliği Otomatik ölçeklendirme, bir Azure Resource Manager kaynağı ayarlarıdır. |
| Ayar | name | Otomatik ölçeklendirme ayarı adı. |
| Ayar | location | Otomatik ölçeklendirme ayarının konumu. Bu konum ölçeği genişletilmiş kaynak konumundan farklı olabilir. |
| properties | targetResourceUri | Ölçeği genişletilmiş kaynak kaynak kimliği. Yalnızca bir otomatik ölçeklendirme ayarı, her bir kaynak olabilir. |
| properties | profiles | Bir otomatik ölçeklendirme ayarı, bir veya daha fazla profillerini oluşur. Otomatik ölçeklendirme altyapısı, her çalıştığında bir profil yürütür. |
| profile | name | Profil adı. Bu profili tanımlamanıza yardımcı olan herhangi bir ad seçebilirsiniz. |
| profile | Capacity.Maximum | İzin verilen kapasite üst sınırı. Bu profili yürütülürken otomatik ölçeklendirme, kaynağınızı bu sayı yukarıda ölçeklenmez sağlar. |
| profile | Capacity.minimum | İzin verilen kapasite alt sınırı. Bu profili yürütülürken otomatik ölçeklendirme, kaynağınızı bu sayının altına ölçeklenmez sağlar. |
| profile | Capacity.default | Kaynak ölçüm (Bu durumda, "vmss1" CPU) okunurken bir sorun yoktur ve geçerli kapasite varsayılandır, otomatik ölçeklendirme kullanıma varsayılan değere ölçeklendirir. Bu kaynak kullanılabilirliğini sağlamaktır. Geçerli kapasite, varsayılan kapasiteden zaten yüksektir, otomatik ölçeklendirme, ölçeklenmez. |
| profile | rules | Otomatik ölçeklendirme kuralları profilinde kullanarak maksimum ve minimum kapasiteler arasında otomatik olarak ölçeklendirir. Bir profili birden çok kural bulunabilir. Genellikle iki kural vardır: ne zaman ölçeğini belirlemek için diğeri ne zaman ölçeklendirme belirlemek için. |
| rule | metricTrigger | Ölçüm koşuluyla bir kural tanımlar. |
| metricTrigger | metricName | Ölçüm adı. |
| metricTrigger |  metricResourceUri | Ölçüm yayan kaynağının kaynak kimliği. Çoğu durumda, ölçeği genişletilmiş kaynak ile aynı olur. Bazı durumlarda, farklı olabilir. Örneğin, bir depolama kuyruğuna ileti sayısına dayalı olarak bir sanal makine ölçek kümesi ölçeklendirebilirsiniz. |
| metricTrigger | timeGrain | Ölçüm örnekleme süresi. Örneğin, **TimeGrain = "PT1M"** istatistiği öğesinde belirtilen toplama yöntemi kullanarak ölçümleri her 1 dakikada toplanması anlamına gelir. |
| metricTrigger | statistic | TimeGrain dönemi içindeki toplama yöntemidir. Örneğin, **istatistik = "Ortalama"** ve **timeGrain = "PT1M"** ortalamasını alarak ölçümleri her 1 dakikada toplanması anlamına gelir. Bu özellik, ölçüm nasıl örneklenir belirler. |
| metricTrigger | timeWindow | Geri ölçümlerini aramak için süre miktarı. Örneğin, **timeWindow "PT10M" =** otomatik ölçeklendirme her çalıştırıldığında, son 10 dakika ölçümlerini sorgular anlamına gelir. Ölçümlerinizin normalleştirilmiş, zaman penceresi sağlar ve geçici artışlara tepki verilmesini önler. |
| metricTrigger | timeAggregation | Örneklenen ölçümleri toplamak için kullanılan toplama yöntemidir. Örneğin, **TimeAggregation = "Ortalama"** örneklenen ölçümlerin ortalamasını alarak toplamak. Önceki örnekte, on 1 dakikalık örnekleri almak ve bunları ortalama. |
| rule | scaleAction | Kuralın metricTrigger tetiklendiğinde gerçekleştirilecek eylem. |
| scaleAction | direction | "Ölçeği ya da"Azaltmak"için ölçek artırma".|
| scaleAction | value | Artırmak veya kaynak kapasitesinin azaltmak için ne kadar. |
| scaleAction | cooldown | Sonra bir ölçeklendirme işlemi yeniden ölçeklendirmeden önce beklenecek süre miktarı. Örneğin, varsa **dakikaysa "PT10M" =** , başka bir 10 dakika için yeniden ölçeklendirmek otomatik ölçeklendirme denemez. Dakikaysa eklenmesi veya kaldırılmasını örnekleri sonra ölçümlerin sağlamaktır. |

## <a name="autoscale-profiles"></a>Otomatik ölçeklendirme profilleri

Otomatik ölçeklendirme profilleri üç tür vardır:

- **Normal profili:** En sık kullanılan profil. Kaynağınızı belirli bir gün veya haftanın gününe göre ölçeklendirmek gerekmiyorsa, normal bir profili kullanabilirsiniz. Bu profil, ardından ölçeğini genişletmek zaman ve ne zaman ölçeklendirme dikte ölçüm kuralları ile yapılandırılabilir. Yalnızca tanımlı bir normal profili olmalıdır.

    Bu makalenin önceki bölümlerinde kullanılan örnek profili, normal bir profili örneğidir. Kaynağınız için bir statik örnek sayısını ölçeklendirmek için bir profil ayarlamak mümkündür unutmayın.

- **Sabit tarih profili:** Özel durumlar için profilidir. Örneğin, 26 aralık 2017'de (Pasifik Saati) önce geldiğini önemli olaya sahip varsayalım. Bu günlük, ancak yine de ölçümleri aynı ölçekte farklı olacak şekilde minimum ve maksimum kapasite kaynağınızın kullanmanız gerekir. Bu durumda, bir sabit tarih profili, ayarın profiller listesine eklemeniz gerekir. Profil, yalnızca olay gününde çalıştırmak için yapılandırılır. Diğer herhangi bir gün için otomatik ölçeklendirme normal profilini kullanır.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
- **Yinelenme profili:** Bu profil türü, bu profili haftanın belirli bir günde her zaman kullanıldığından emin olmak sağlar. Yinelenme profilleri, yalnızca bir başlangıç saati gerekir. Sabit tarih profili başlamaya ayarlandıysa veya bunlar sonraki yinelenme profili kadar çalıştırın. Aynı ayarda tanımlanan normal bir profili olsa bile bu profili bir otomatik ölçeklendirme ayarı yalnızca bir yinelenme profili ile çalışır. Aşağıdaki iki örnek, bu profili nasıl kullanıldığını göstermektedir:

    **Örnek 1: Haftanın günü ve hafta sonları karşılaştırması**
    
    Sonralı, 4 için maksimum kapasite istediğinizi düşünelim. Daha fazla yük beklediğiniz çünkü günlerinde, 10 olması için en yüksek kapasite istersiniz. Bu durumda, ayarınızı iki yinelenme profili, bir hafta sonları ve diğer günlerinde çalıştırılacak içerecektir.
    Ayarı şöyle görünür:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    Önceki ayarı, her bir yinelenme profili bir zamanlamaya sahip olduğunu gösterir. Profil başladığında bu zamanlamanın belirler çalışıyor. Başka bir profile çalıştırma süresi olduğunda profili durdurur.

    Örneğin, önceki ayarında, 12: 00'da Pazartesi günü başlatmak için "weekdayProfile" ayarlanır. Bu profil, Pazartesi günü 12: 00'da çalışan başlar anlamına gelir. 12:00 "weekendProfile" çalıştırmaya başlamak için ne zaman zamanlanmış'da, Cumartesi kadar devam eder.

    **Örnek 2: iş saatleri**
    
    Bir ölçüm eşiği çalışma saatleri (09:00:00 ila 5:00) ve diğer tüm saatler için farklı bir sahip olmasını istediğiniz varsayalım. Ayarı şöyle görünür:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    Önceki ayar "businessHoursProfile" Pazartesi günü 9: 00'da çalışan başlar ve 17:00:00 için Devam'ı gösterir. Bu durumda "nonBusinessHoursProfile" çalıştıran başlatır. "nonBusinessHoursProfile" Salı günü 09:00:00 kadar çalışır ve ardından "businessHoursProfile" yeniden devreye girer. Bu, 17: 00'dan Cuma kadar yinelenir. Bu noktada, "nonBusinessHoursProfile" Pazartesi tüm 9: 00'da çalışır.
    
> [!Note]
> Azure portalında otomatik ölçeklendirme kullanıcı arabiriminde yinelenme profilleri için bitiş saatlerini uygular ve otomatik ölçeklendirme ayarının yineleme profilleri arasında varsayılan profili çalıştırır.
    
## <a name="autoscale-evaluation"></a>Otomatik ölçek değerlendirme
Otomatik ölçeklendirme ayarları birden çok profil olabilir ve her bir profil birden çok ölçüm kuralları olabilir düşünüldüğünde, bir otomatik ölçeklendirme ayarı nasıl değerlendirilir anlamak önemlidir. Otomatik ölçeklendirme işlemi her çalıştırıldığında, geçerli profili seçerek başlar. Daha sonra otomatik ölçeklendirme, en düşük ve en yüksek değerleri ve profilinde herhangi bir ölçüm kuralları değerlendirir ve bir ölçeklendirme eylemi gerekli olup olmadığını belirler.

### <a name="which-profile-will-autoscale-pick"></a>Otomatik ölçeklendirme profili hangi seçer?

Otomatik ölçeklendirme profilini seçmek için aşağıdaki sırayı kullanır:
1. Önce şimdi çalıştırmak üzere yapılandırılmış herhangi bir sabit tarih profil için arar. Varsa, otomatik ölçeklendirme, çalıştırır. Çalıştırmak için gereken birden çok sabit tarih profili varsa, otomatik ölçeklendirme İlkini seçer.
2. Hiçbir sabit tarih profili varsa, otomatik ölçeklendirme yinelenme profilleri arar. Yinelenme profili bulunursa, onu çalıştırır.
3. Otomatik ölçeklendirme, sabit tarih veya yineleme profilleri yoksa, normal profili çalıştırır.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Otomatik ölçeklendirme, birden çok kural nasıl değerlendirmek?

Otomatik ölçeklendirme çalıştırmak için hangi profilin belirledikten sonra bu profildeki tüm genişleme kuralları değerlendirir (kurallarıyla bunlar **yönü "Artır" =** ).

Bir veya daha fazla ölçek genişletme kuralı tetiklendi, otomatik ölçeklendirme tarafından belirlenen yeni kapasite hesaplar **scaleAction** bu kuralların her biri. Ardından, out hizmet kullanılabilirliğini sağlamak için bu kapasitelerin en ölçeklendirir.

Örneğin, burada 10 'un geçerli kapasite ile ayarlanmış bir sanal makine ölçek varsayalım. İki genişleme kuralları: 10 oranında kapasitesini artırır ve 3 sayılar kapasitesini artırır. İlk kural 11'in yeni bir kapasitede neden olur ve ikinci kural 13 bir kapasitede neden olur. Hizmetin kullanılabilir olmasını sağlamak için otomatik ölçeklendirme, bu kapasite üst sınırı, bu nedenle ikinci kuralı sonucunda oluşan eylemi seçilen seçer.

Ölçek genişletme kuralı yok tetiklenen, otomatik ölçeklendirme tüm ölçeğini kurallarını değerlendirir. (ile kurallar **yönü "Azaltmak" =** ). Tetiklenen tüm ölçeğini kuralları, otomatik ölçeklendirme, yalnızca bir ölçek daraltma eylemi alır.

Otomatik ölçeklendirme hesaplar tarafından belirlenen yeni kapasite **scaleAction** bu kuralların her biri. Ardından bu hizmetin kullanılabilir olmasını sağlamak için bu kapasiteler en fazla sonucunda oluşan ölçek eylemi seçer.

Örneğin, burada 10 'un geçerli kapasite ile ayarlanmış bir sanal makine ölçek varsayalım. İki ölçek kuralı vardır: yüzde 50 kapasite azalır ve kapasite tarafından 3 sayısını azaltır. İlk kural 5 yeni bir kapasite neden olur ve ikinci kural 7'in bir kapasitede neden olur. Hizmetin kullanılabilir olmasını sağlamak için otomatik ölçeklendirme, bu kapasite üst sınırı, bu nedenle ikinci kuralı sonucunda oluşan eylemi seçilen seçer.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki başvurarak otomatik ölçeklendirme hakkında daha fazla bilgi edinin:

* [Otomatik Ölçeklendirmeye Genel Bakış](../../azure-monitor/platform/autoscale-overview.md)
* [Azure İzleyici otomatik ölçeklendirme ortak ölçümleri](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Azure İzleyici otomatik ölçeklendirme için en iyi yöntemler](../../azure-monitor/platform/autoscale-best-practices.md)
* [E-posta ve Web kancası uyarı bildirimleri göndermek için otomatik ölçeklendirme eylemleri kullanın](../../azure-monitor/platform/autoscale-webhook-email.md)
* [Otomatik ölçeklendirme REST API](https://msdn.microsoft.com/library/dn931953.aspx)

