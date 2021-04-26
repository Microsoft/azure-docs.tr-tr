---
title: Azure sanal makinelerini kullanarak gelişmiş otomatik ölçeklendirme
description: ", E-posta gönderen ve ölçek eylemleriyle Web kancası URL 'Lerini çağıran birden çok kural ve profille Kaynak Yöneticisi ve VM Ölçek Kümeleri kullanır."
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 26cfdf05847b01d28a8574483acc89cfeced0ffa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717749"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>VM Ölçek Kümeleri için Kaynak Yöneticisi şablonları kullanarak gelişmiş otomatik ölçeklendirme yapılandırması
Performans ölçümü eşiklerine, yinelenen bir zamanlamaya göre veya belirli bir tarihe göre sanal makine ölçek kümelerinde ölçeklendirebilir ve genişleme yapabilirsiniz. Ayrıca, ölçek eylemleri için e-posta ve Web kancası bildirimleri de yapılandırabilirsiniz. Bu izlenecek yol, bir VM Ölçek kümesindeki bir Kaynak Yöneticisi şablonu kullanarak tüm bu nesneleri yapılandırmaya ilişkin bir örnek gösterir.

> [!NOTE]
> Bu kılavuzda VM Ölçek Kümeleri için adımlar açıklanmakta ancak aynı bilgiler, CPU gibi basit bir performans ölçüsüne bağlı olarak bir VM Ölçek kümesindeki basit bir ölçek genişletme/genişletme ayarı için otomatik ölçeklendirme [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)ve [API Management Hizmetleri](../../api-management/api-management-key-concepts.md) Için de geçerlidir. [Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) ve [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) belgelerine bakın
>
>

## <a name="walkthrough"></a>Kılavuz
Bu kılavuzda, bir ölçek kümesi için otomatik ölçeklendirme ayarını yapılandırmak ve güncellemek üzere [Azure Kaynak Gezgini](https://resources.azure.com/) kullanırız. Azure Kaynak Gezgini, Azure kaynaklarını Kaynak Yöneticisi şablonları aracılığıyla yönetmenin kolay bir yoludur. Azure Kaynak Gezgini araç ' i yeni kullanıyorsanız, [Bu tanıtımı](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/)okuyun.

1. Temel bir otomatik ölçeklendirme ayarıyla yeni bir ölçek kümesi dağıtın. Bu makalede, temel bir otomatik ölçeklendirme şablonuyla Windows ölçek kümesine sahip olan Azure hızlı başlangıç galerisindeki bir tane kullanılmaktadır. Linux ölçek kümeleri aynı şekilde çalışır.
2. Ölçek kümesi oluşturulduktan sonra, Azure Kaynak Gezgini ölçek kümesi kaynağına gidin. Microsoft. Insights düğümü altında aşağıdakileri görürsünüz.

    ![Azure Gezgini](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    Şablon yürütme, **' otomatik ölçeklendirme '** adlı varsayılan bir otomatik ölçek ayarı oluşturdu. Sağ tarafta, bu otomatik ölçeklendirme ayarının tam tanımını görebilirsiniz. Bu durumda, varsayılan otomatik ölçeklendirme ayarı CPU% tabanlı genişleme ve ölçek genişletme kuralıyla birlikte gelir.  

3. Artık zamanlamaya veya belirli gereksinimlere göre daha fazla profil ve kural ekleyebilirsiniz. Üç profille bir otomatik ölçeklendirme ayarı oluşturacağız. Otomatik ölçeklendirme profilleri ve kurallarını anlamak için [Otomatik ölçek En Iyi yöntemlerini](autoscale-best-practices.md)gözden geçirin.  

    | Profiller & kuralları | Description |
    |--- | --- |
    | **Profil** |**Performans/ölçüm tabanlı** |
    | Kural |Service Bus kuyruk Iletisi sayısı > x |
    | Kural |Service Bus kuyruğu Ileti sayısı < y |
    | Kural |CPU% > n |
    | Kural |CPU% < p |
    | **Profil** |**Hafta içi sabah saatleri (kural yok)** |
    | **Profil** |**Ürün başlatma günü (kural yok)** |

4. Bu izlenecek yol için kullandığımız kuramsal bir ölçeklendirme senaryosu aşağıda verilmiştir.

   * **Yük temelinde** , ölçek kümesinde barındırılan uygulamamdaki yüküne göre veya genişletmek istiyorum. *
   * **Ileti kuyruğu boyutu** -uygulamama gelen iletiler Için Service Bus kuyruğu kullanıyorum. Kuyruğun ileti sayısını ve% CPU 'sunu kullanıyorum ve ileti sayısı veya CPU herhangi biri eşiğin üzerinde olursa bir ölçeklendirme eylemi tetiklemek için varsayılan bir profil yapılandırın.\*
   * **Haftanın saati ve günü** -gün içinde ' günü sabah saat ' olarak adlandırılan bir haftalık yinelenen ' saat ' ı istiyorum. Geçmiş verilere bağlı olarak, bu süre boyunca uygulamamın yükünü işlemek için belirli sayıda sanal makine örneği sağlamak daha iyi olduğunu biliyorum.\*
   * **Özel tarihler** -' ürün başlatma günü ' profili ekledim. Uygulamamın yük son pazarlama bildirilerini işlemeye ve uygulamaya yeni bir ürün yerleştirdiğimiz zaman belirli tarihlere göre planlıyorum.\*
   * *Son iki profil, bunlar içinde diğer performans ölçümü tabanlı kurallara da sahip olabilir. Bu durumda, varsayılan performans ölçümü tabanlı kurallara göre değil, bir tane olmamaya karar verdim. Kurallar, yinelenen ve Tarih tabanlı profiller için isteğe bağlıdır.*

     Otomatik ölçeklendirme motorunun profillerin ve kuralların öncelik belirlemesi, [Otomatik ölçeklendirme en iyi uygulamalar](autoscale-best-practices.md) makalesinde de yakalanır.
     Otomatik ölçeklendirme için ortak ölçümlerin bir listesi için, [Otomatik ölçeklendirme Için ortak ölçümleri](autoscale-common-metrics.md) inceleyin

5. Kaynak Gezgini **okuma/yazma** modunda olduğunuzdan emin olun

    ![Otomatik ölçek wad, varsayılan otomatik ölçeklendirme ayarı](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Düzenle’ye tıklayın. Otomatik ölçeklendirme ayarında bulunan ' profiles ' öğesini aşağıdaki yapılandırmayla **değiştirin** :

    ![Ekran görüntüsü Profiller öğesini gösterir.](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
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
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
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
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
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
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    Desteklenen alanlar ve değerleri için bkz. [Otomatik ölçeklendirme REST API belgeleri](/rest/api/monitor/autoscalesettings). Artık otomatik ölçeklendirme ayarınız, daha önce açıklanan üç profili içerir.

7. Son olarak, otomatik ölçeklendirme **bildirimi** bölümüne bakın. Otomatik ölçeklendirme bildirimleri, bir genişleme veya bir işlem sırasında başarıyla tetiklendiğinde üç şey yapmanızı sağlar.
   - Aboneliğinizin yöneticisine ve ortak yöneticilerine bildirim gönderin
   - Bir kullanıcı kümesini e-postayla gönderin
   - Web kancası çağrısını tetikleyin. Bu Web kancası tetiklendiğinde, otomatik ölçeklendirme koşulunun ve ölçek kümesi kaynağıyla ilgili meta verileri gönderir. Otomatik ölçeklendirme Web kancasının yükü hakkında daha fazla bilgi için bkz. [Otomatik ölçeklendirme Için Web kancası & e-posta bildirimleri](autoscale-webhook-email.md)

   Değer null olan **bildirim** öğesini değiştiren otomatik ölçeklendirme ayarına aşağıdakini ekleyin

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Otomatik ölçeklendirme ayarını güncelleştirmek için Kaynak Gezgini ' de **PUT** düğmesine basın.

Bir VM Ölçek kümesinde birden çok ölçek profili ve ölçek bildirimleri içerecek şekilde bir otomatik ölçeklendirme ayarını güncelleştirmiş olursunuz.

## <a name="next-steps"></a>Sonraki Adımlar
Otomatik ölçeklendirme hakkında daha fazla bilgi edinmek için bu bağlantıları kullanın.

[Sanal makine ölçek kümeleriyle otomatik ölçeklendirme sorunlarını giderme](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Otomatik ölçeklendirme için genel ölçümler](autoscale-common-metrics.md)

[Azure otomatik ölçeklendirme için en iyi uygulamalar](autoscale-best-practices.md)

[PowerShell kullanarak otomatik ölçeklendirmeyi yönetme](../powershell-samples.md#create-and-manage-autoscale-settings)

[CLı kullanarak otomatik ölçeklendirmeyi yönetme](../cli-samples.md#autoscale)

[Otomatik ölçeklendirme için Web kancası & e-posta bildirimlerini yapılandırma](autoscale-webhook-email.md)

[Microsoft. Insights/oto scalesettings](/azure/templates/microsoft.insights/autoscalesettings) şablon başvurusu