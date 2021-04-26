---
title: Azure yay bulutu 'nda Application Insights Java In-Process Aracısı kullanma
description: Azure yay bulutu 'nda Application Insights Java In-Process Aracısı kullanarak uygulamaları ve mikro hizmetleri izleme.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b704d2cf8e2cc8e6cf5d8049290379dd45e26737
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870346"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Azure yay bulutu 'nda Application Insights Java In-Process Aracısı (Önizleme)

Bu belgede, Azure yay bulutu 'nda Application Insights Java Aracısı kullanılarak uygulamaların ve mikro hizmetlerin nasıl izleneceği açıklanmaktadır. 

Bu özellikle şunları yapabilirsiniz:

* Farklı filtrelerle izleme verilerinde arama yapın.
* Mikro hizmetlerin bağımlılık haritasını görüntüleyin.
* İstek performansını denetleyin.
* Gerçek zamanlı canlı ölçümleri izleyin.
* İstek başarısızlıklarını denetleyin.
* Uygulama ölçümlerini denetleyin.

Application Insights, aşağıdakiler dahil olmak üzere birçok observable perspektifi sağlar:

* Uygulama haritası
* Performans
* Hatalar
* Ölçümler
* Canlı Ölçümler
* Kullanılabilirlik

## <a name="enable-java-in-process-agent-for-application-insights"></a>Application Insights için Java In-Process Aracısını Etkinleştir

Aşağıdaki yordamı kullanarak Java In-Process Aracısı önizleme özelliğini etkinleştirin.

1. Hizmet örneğinizin hizmete genel bakış sayfasına gidin.
2. İzleme dikey penceresinde **Application Insights** girişi ' ne tıklayın.
3. **Application Insights** tümleştirmesini etkinleştirmek Için **Application Insights etkinleştir** düğmesine tıklayın.
4. Application Insights var olan bir örneğini seçin ya da yeni bir tane oluşturun.
5. Chick, Java işlem içi aracı özelliğini etkinleştirmek için **Java işlem içi Aracısı 'Nı etkinleştirir** . Örnekleme hızını 0 ' dan 100 ' e de özelleştirebilirsiniz.
6.  Değişikliği kaydetmek için **Kaydet**’e tıklayın.

## <a name="portal"></a>Portal

1. **Hizmete git | Genel Bakış sayfasını inceleyin** ve **izleme** bölümünde **Application Insights** ' yi seçin. 
2. Azure yay bulutu 'nda Application Insights etkinleştirmek için **Application Insights etkinleştir** ' e tıklayın.
3. Java IPA Preview özelliğini etkinleştirmek için **işlem Içi Java Aracısını Etkinleştir** ' e tıklayın. Bir IPA Preview özelliği etkinleştirildiğinde, bir isteğe bağlı örnekleme hızı yapılandırabilirsiniz (varsayılan% 10,0).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Application Insights özelliğini kullanma

**Application Insights** özelliği etkinleştirildiğinde şunları yapabilirsiniz:

Sol gezinti bölmesinde, Application Insights **genel bakış** sayfasına gitmek için **Application Insights** ' a tıklayın. 

* Uygulamalar arasındaki çağrıların durumunu görmek için **uygulama Haritası** ' na tıklayın.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Müşteriler-hizmet arasındaki bağlantıya tıklayın ve `petclinic` SQL 'deki bir sorgu gibi daha fazla ayrıntı görmek için.

* Sol gezinti bölmesinde, tüm uygulamaların işlemlerinin ve bağımlılıkların ve rollerinin performans verilerini görmek için **performans** ' a tıklayın.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* Uygulamalarınızdan beklenmeyen bir şeyin olup olmadığını görmek için sol gezinti bölmesinde **hata** ' a tıklayın.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* Sol gezinti bölmesinde **ölçümler** ' e tıklayın ve ad alanını seçin, varsa her Iki yay önyükleme ölçümlerini ve özel ölçümleri görürsünüz.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* Sol gezinti bölmesinde, farklı boyutlarda gerçek zamanlı ölçümleri görmek için **canlı ölçümler** ' e tıklayın.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* [Application Insights ' de kullanılabilirlik testleri](../azure-monitor/app/monitor-web-app-availability.md)oluşturarak, sol gezinti bölmesinde, Web uygulamalarının kullanılabilirliğini ve yanıt hızını Izlemek için **kullanılabilirlik** ' e tıklayın.

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM Şablonu
Azure Resource Manager şablonunu kullanmak için aşağıdaki içeriği öğesine kopyalayın `azuredeploy.json` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>CLI
CLı komutuyla ARM şablonu uygulayın:

* Mevcut bir Azure yay bulutu örneği için:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" â€“name "assignedName" â€“resource-group "resourceGroupName"
```
* Yeni oluşturulan bir Azure yay bulutu örneği için:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" â€“resource-group "resourceGroupName"
```
* Uygulama öngörülerini devre dışı bırakmak için:

```azurecli
az spring-cloud app-insights update --disable â€“name "assignedName" â€“resource-group "resourceGroupName"

```

## <a name="see-also"></a>Ayrıca bkz.
* [Azure Spring Cloud ile dağıtılmış izleme kullanma](spring-cloud-howto-distributed-tracing.md)
* [Günlükleri ve ölçümleri analiz etme](diagnostic-services.md)
* [Gerçek zamanlı olarak günlük akışı yapma](spring-cloud-howto-log-streaming.md)
