---
title: Azure Izleyici 'de otomatik ölçeklendirme ayarlarını anlama
description: Otomatik ölçeklendirme ayarlarının ve nasıl çalıştıkları hakkında ayrıntılı bir döküm. Sanal makineler için geçerlidir, Cloud Services Web Apps
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: a914f6d71c013acea8dfde0f6578985bc009bb26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100623809"
---
# <a name="understand-autoscale-settings"></a>Otomatik Ölçeklendirme ayarlarını anlama
Otomatik ölçeklendirme ayarları, uygulamanızın dalgalanma yükünü işlemek için çalışan doğru kaynak miktarına sahip olmanızı sağlar. Otomatik ölçeklendirme ayarlarını, yük veya performansı belirten veya zamanlanan bir tarih ve saatte tetiklenen ölçümlere göre tetiklenecek şekilde yapılandırabilirsiniz. Bu makalede, bir otomatik ölçeklendirme ayarının anatomtoize ayrıntılı bir bakış ele alır. Makale, bir ayarın şeması ve özellikleriyle başlar ve yapılandırılabilecek farklı profil türlerini gösterir. Son olarak, makalede, Azure 'daki otomatik ölçeklendirme özelliğinin, belirli bir zamanda hangi profilin yürütüleceğini nasıl değerlendirdiği açıklanmaktadır.

## <a name="autoscale-setting-schema"></a>Otomatik ölçeklendirme ayarı şeması
Otomatik ölçeklendirme ayarı şemasını göstermek için aşağıdaki otomatik ölçeklendirme ayarı kullanılır. Bu otomatik ölçeklendirme ayarının şunları olduğunu unutmayın:
- Bir profil. 
- Bu profilde iki ölçüm kuralı vardır: biri ölçeği genişletme için, diğeri de ölçekleme için.
  - Ölçek genişletme kuralı, sanal makine ölçek kümesinin ortalama yüzde 85 ' unun, son 10 dakika boyunca yüzde ' den büyük olduğunda tetiklenir.
  - Sanal makine ölçek kümesinin ortalaması, son dakika boyunca yüzde 60 ' den az olduğunda, ölçek-ın kuralı tetiklenir.

> [!NOTE]
> Bir ayarın birden çok profili olabilir. Daha fazla bilgi edinmek için [profiller](#autoscale-profiles) bölümüne bakın. Bir profilde aynı zamanda birden fazla genişleme kuralı ve ölçek kuralları tanımlanmış olabilir. Nasıl değerlendirildiğini görmek için [değerlendirme](#autoscale-evaluation) bölümüne bakın.

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

| Section | Öğe adı | Açıklama |
| --- | --- | --- |
| Ayar | ID | Otomatik ölçeklendirme ayarının kaynak KIMLIĞI. Otomatik ölçeklendirme ayarları bir Azure Resource Manager kaynağıdır. |
| Ayar | name | Otomatik ölçeklendirme ayarı adı. |
| Ayar | location | Otomatik ölçeklendirme ayarının konumu. Bu konum, ölçeklendirilen kaynağın konumundan farklı olabilir. |
| properties | targetResourceUri | Ölçeklendirilen kaynağın kaynak KIMLIĞI. Kaynak başına yalnızca bir otomatik ölçeklendirme ayarınız olabilir. |
| properties | lerinize | Otomatik ölçeklendirme ayarı bir veya daha fazla profilden oluşur. Otomatik ölçeklendirme altyapısının her çalıştırılışında, bir profili yürütür. |
| profil | name | Profilin adı. Profili tanımanıza yardımcı olacak herhangi bir ad seçebilirsiniz. |
| profil | Kapasite. maksimum | İzin verilen maksimum kapasite. Otomatik ölçeklendirme, bu profili yürütürken kaynağı bu sayının üzerinde ölçeklendirmez olmasını sağlar. |
| profil | Kapasite. en az | İzin verilen en düşük kapasite. Otomatik ölçeklendirme, bu profili yürütürken kaynağı bu sayının altında ölçeklendirmez olmasını sağlar. |
| profil | Kapasite. varsayılan | Kaynak ölçüsünü (Bu durumda, "vmss1" CPU 'SU) okurken bir sorun varsa ve geçerli kapasite varsayılan değer altında olursa otomatik ölçeklendirme varsayılan olarak ölçeği ölçeklendirir. Bu, kaynağın kullanılabilirliğini sağlamaktır. Geçerli kapasite varsayılan kapasiteden daha yüksekse, otomatik ölçeklendirme ölçeklendirme yapmaz. |
| profil | rules | Otomatik ölçeklendirme, profildeki kuralları kullanarak maksimum ve en düşük kapasite arasında otomatik olarak ölçeklendirilir. Bir profilde birden çok kurala sahip olabilirsiniz. Genellikle iki kural vardır: bir tanesi ne zaman ölçeklendirileceğini ve diğeri ne zaman ölçeklendirileceğini belirlemektir. |
| kurallar | metricTrigger | Kuralın ölçüm koşulunu tanımlar. |
| metricTrigger | metricName | Ölçümün adı. |
| metricTrigger |  metricResourceUri | Ölçüyü veren kaynağın kaynak KIMLIĞI. Çoğu durumda, ölçeklendirmekte olan kaynakla aynı olur. Bazı durumlarda, farklı olabilir. Örneğin, bir sanal makine ölçek kümesini bir depolama kuyruğundaki ileti sayısına göre ölçeklendirebilirsiniz. |
| metricTrigger | zamandilimi | Ölçüm örnekleme süresi. Örneğin, **Timegrex "PT1M"** , ölçümlerin, istatistik öğesinde belirtilen toplama yöntemi kullanılarak 1 dakikada bir toplanması gerektiği anlamına gelir. |
| metricTrigger | istatistik | Zaman çizgisi dönemi içindeki toplama yöntemi. Örneğin, **istatistik = "Average"** ve **TIMEGREN = "PT1M"** , ölçümlerin ortalama alınarak her 1 dakikada toplanmasının gerektiği anlamına gelir. Bu özellik, ölçümün nasıl örnekleneceğini belirler. |
| metricTrigger | timeWindow | Ölçümler için geri aranacak zaman miktarı. Örneğin, **timeWindow = "PT10M"** , otomatik ölçeklendirme her çalıştığında, son 10 dakika için ölçümleri sorgular. Zaman penceresi, ölçümlerinizin normalleştirilmesine izin verir ve geçici ani artışlar için yeniden hareket etmenizi önler. |
| metricTrigger | timeAggregation | Örneklenmiş ölçümleri toplamak için kullanılan toplama yöntemi. Örneğin, **timeaggregate = "Average"** , ortalama değeri alarak örneklenmiş ölçümleri toplamalıdır. Önceki durumda, 10 1 dakikalık örnekleri alın ve bunları ortalama yapın. |
| kurallar | scaleAction | Kuralın metricTrigger değeri tetiklendiğinde gerçekleştirilecek eylem. |
| scaleAction | yön | Ölçeği genişletmek için "Artır" veya "azalt".|
| scaleAction | değer | Kaynağın kapasitesini ne kadar artırabilir veya azaltamazsınız. |
| scaleAction | cooldown | Ölçeklendirmeden önce bir ölçek işleminden sonra beklenecek süre. Örneğin, **coolI = "PT10M"** ise otomatik ölçeklendirme, başka 10 dakika boyunca yeniden ölçeklendirmeye çalışmaz. Coolaşağı, örneklerin ekleme veya kaldırma işleminden sonra ölçümlerin sabitlerine izin vermektedir. |

## <a name="autoscale-profiles"></a>Otomatik ölçeklendirme profilleri

Üç tür otomatik ölçeklendirme profili vardır:

- **Normal profil:** En yaygın profil. Kaynağı haftanın gününe veya belirli bir güne göre ölçeklendirmeniz gerekmiyorsa, normal bir profil kullanabilirsiniz. Bu profil daha sonra, ne zaman ölçeklenmesi ve ne zaman Ölçeklendirilecek hakkında bilgi veren ölçüm kurallarıyla yapılandırılabilir. Yalnızca bir normal profiliniz tanımlanmış olmalıdır.

    Bu makalede daha önce kullanılan örnek profil, normal bir profile örnektir. Bir profili, kaynağınız için statik örnek sayısına ölçeklendirecek şekilde ayarlamak da mümkün olduğunu unutmayın.

- **Sabit tarih profili:** Bu profil özel durumlar içindir. Örneğin, 26 Aralık 2017 (PST) tarihinde önemli bir olayınızın olduğunu varsayalım. Kaynağınızın en düşük ve en büyük kapasitelerinin o gün için farklı olmasını istiyorsunuz, ancak yine de aynı ölçümleri ölçeklendirin. Bu durumda, ayarınızın profil listesine sabit bir tarih profili eklemeniz gerekir. Profil, yalnızca olayın gününde çalışacak şekilde yapılandırılmıştır. Herhangi bir gün için otomatik ölçeklendirme normal profili kullanır.

    ```json
    "profiles": [
        {
            "name": " regularProfile",
            "capacity": {
                ...
            },
            "rules": [
                {
                ...
                },
                {
                ...
                }
            ]
        },
        {
            "name": "eventProfile",
            "capacity": {
            ...
            },
            "rules": [
                {
                ...
                }, 
                {
                ...
                }
            ],
            "fixedDate": {
                "timeZone": "Pacific Standard Time",
                "start": "2017-12-26T00:00:00",
                "end": "2017-12-26T23:59:00"
            }
        }
    ]
    ```
    
- **Yinelenme profili:** Bu profil türü, bu profilin her zaman haftanın belirli bir gününde kullanılmasını sağlamanıza olanak sağlar. Yineleme profillerinin yalnızca bir başlangıç saati vardır. Sonraki yinelenme profili veya sabit tarih profili başlangıç olarak ayarlanana kadar çalışır. Aynı ayarda tanımlanmış bir normal profil olsa bile, yalnızca bir yinelenme profiline sahip bir otomatik ölçeklendirme ayarı bu profili çalıştırır. Aşağıdaki iki örnek, bu profilin nasıl kullanıldığını göstermektedir:

    **Örnek 1: hafta Içi ve hafta sonları**
    
    Hafta sonları için maksimum kapasitenin 4 olmasını istediğinizi varsayalım. Hafta içi, daha fazla yük beklemeniz için maksimum kapasitenizin 10 olmasını istiyorsunuz. Bu durumda, ayarınız hafta içinde ve diğeri de hafta içinde çalışmak üzere iki yinelenme profili içerir.
    Bu ayar şöyle görünür:

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

    Yukarıdaki ayar, her bir yinelenme profilinin bir zamanlamaya sahip olduğunu gösterir. Bu zamanlama, profilin ne zaman çalışmaya başlayacağını belirler. Profil, başka bir profil çalıştırmak istediğinizde durduruluyor.

    Örneğin, yukarıdaki ayarda, "weekdayProfile" Pazartesi günü 12:00 ' de başlayacak şekilde ayarlanır. Bu, bu profilin 12:00 ' de Pazartesi günü çalışmaya başladığı anlamına gelir. "WeekendProfile" çalışmaya başlamak üzere zamanlandığında, Cumartesi 12:00 ' ye kadar devam eder.

    **Örnek 2: Iş saatleri**
    
    İş saatleri (9:00-5:00 PM) sırasında tek bir ölçüm eşiğine sahip olmak istediğinizi ve diğer tüm zamanlarda farklı bir ölçü olduğunu varsayalım. Bu ayar şöyle görünür:
    
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
    
    Yukarıdaki ayarda, "businessHoursProfile" ın Pazartesi günü 9:00 ' de çalışmaya başladığı ve 5:00 PM ile devam ettiği gösterilmektedir. Bu, "nonBusinessHoursProfile" çalışmaya başladığında yapılır. "NonBusinessHoursProfile", 9:00 Salı kadar çalışır ve "businessHoursProfile" yeniden sürer. Bu, 5:00 saat ' ye kadar yinelenir. Bu noktada, "nonBusinessHoursProfile", Pazartesi günü 9:00 ile aynı şekilde çalışır.
    
> [!Note]
> Azure portal otomatik ölçeklendirme Kullanıcı arabirimi, yineleme profillerinin bitiş zamanlarını zorlar ve yineleme profilleri arasında otomatik ölçeklendirme ayarının varsayılan profilini çalıştırmaya başlar.
    
## <a name="autoscale-evaluation"></a>Otomatik ölçeklendirme değerlendirmesi
Otomatik ölçeklendirme ayarlarının birden çok profili olabilir ve her profilde birden çok ölçüm kuralı bulunabilir ve bu, bir otomatik ölçeklendirme ayarının nasıl değerlendirildiğini anlamak önemlidir. Otomatik ölçeklendirme işi her çalıştığında, uygulanabilir profili seçerek başlar. Sonra otomatik ölçeklendirme, en düşük ve en yüksek değerleri ve profildeki ölçüm kurallarını değerlendirir ve bir ölçeklendirme eylemi gerekli olup olmadığını belirler.

### <a name="which-profile-will-autoscale-pick"></a>Hangi profil otomatik ölçeklendirme seçer?

Otomatik ölçeklendirme, profili seçmek için aşağıdaki sırayı kullanır:
1. İlk olarak, şimdi çalışacak şekilde yapılandırılmış herhangi bir sabit tarih profilini arar. Varsa, otomatik ölçeklendirme onu çalıştırır. Çalışması beklenen birden çok sabit tarih profili varsa, otomatik ölçeklendirme ilk olanı seçer.
2. Sabit bir tarih profili yoksa, otomatik ölçeklendirme yineleme profillerine bakar. Bir yinelenme profili bulunursa, onu çalıştırır.
3. Sabit bir tarih veya yinelenme profilleri yoksa otomatik ölçeklendirme normal profili çalıştırır.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Otomatik ölçeklendirme birden çok kuralı nasıl değerlendirir?

Otomatik ölçeklendirme hangi profilin çalıştırılacağını belirledikten sonra, profildeki tüm genişleme kurallarını değerlendirir (Bu kurallar **Direction = "Artır"**).

Bir veya daha fazla genişleme kuralı tetikleniyorsa, otomatik ölçeklendirme, bu kuralların her birinin **ScaleAction** tarafından belirlenen yeni kapasiteyi hesaplar. Daha sonra hizmet kullanılabilirliğine olanak sağlamak için bu kapasitelerin en üst sınırına göre ölçeklendirin.

Örneğin, geçerli 10 kapasiteye sahip bir sanal makine ölçek kümesi olduğunu varsayalım. İki genişleme kuralı vardır: kapasiteyi yüzde 10 ' luk bir artırır ve kapasiteyi 3 saya artırır. İlk kural yeni bir 11 kapasiteye neden olur ve ikinci kural 13 kapasiteye neden olur. Hizmet kullanılabilirliğini sağlamak için, otomatik ölçeklendirme, en yüksek kapasiteye neden olan eylemi seçer, bu nedenle ikinci kural seçilir.

Ölçek Genişletme kuralları tetiklenmez, otomatik ölçeklendirme tüm ölçek genişletme kurallarını değerlendirir ( **Direction = "azalt"** ile kurallar). Otomatik ölçeklendirme yalnızca tüm ölçek genişletme kuralları tetikleniyorsa bir ölçeklendirme eylemi alır.

Otomatik ölçeklendirme, bu kuralların her birinin **ScaleAction** tarafından belirlenen yeni kapasiteyi hesaplar. Ardından, hizmet kullanılabilirliğini sağlamak için bu kapasitelerin maksimum sayısına neden olan ölçekleme eylemini seçer.

Örneğin, geçerli 10 kapasiteye sahip bir sanal makine ölçek kümesi olduğunu varsayalım. İki ölçeklendirme kuralı vardır: kapasiteyi yüzde 50 oranında düşürür ve kapasiteyi 3 sayan azaltır. İlk kural yeni 5 kapasiteye neden olur ve ikinci kural 7 kapasiteye neden olur. Hizmet kullanılabilirliğini sağlamak için, otomatik ölçeklendirme, en yüksek kapasiteye neden olan eylemi seçer, bu nedenle ikinci kural seçilir.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki öğesine başvurarak otomatik ölçeklendirme hakkında daha fazla bilgi edinin:

* [Otomatik ölçeklendirmeye genel bakış](./autoscale-overview.md)
* [Azure Izleyici otomatik ölçeklendirme ortak ölçümleri](./autoscale-common-metrics.md)
* [En iyi Azure İzleyici otomatik ölçeklendirme yöntemleri](./autoscale-best-practices.md)
* [E-posta ve Web kancası uyarı bildirimleri göndermek için otomatik ölçeklendirme eylemlerini kullanma](./autoscale-webhook-email.md)
* [Otomatik ölçeklendirme REST API](/rest/api/monitor/autoscalesettings)

