---
title: Azure Güvenlik Duvarı günlüklerine ve ölçümlere genel bakış
description: Güvenlik duvarı günlüklerini kullanarak Azure Güvenlik Duvarı'nı izleyebilirsiniz. Ayrıca etkinlik günlüklerini kullanarak Azure Güvenlik Duvarı kaynaklarıyla ilgili işlemleri denetleyebilirsiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: f233b1a60202b440abf34edd1c56eebaecba18e2
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166993"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure Güvenlik Duvarı günlükleri ve ölçümleri

Güvenlik duvarı günlüklerini kullanarak Azure Güvenlik Duvarı'nı izleyebilirsiniz. Ayrıca etkinlik günlüklerini kullanarak Azure Güvenlik Duvarı kaynaklarıyla ilgili işlemleri denetleyebilirsiniz.

Bu günlüklerden bazılarına portaldan erişebilirsiniz. Günlükler [Azure izleyici](../azure-monitor/insights/azure-networking-analytics.md)günlüklerine, depolamaya ve Event Hubs gönderilebilir ve Azure izleyici günlüklerinde veya Excel ve Power BI gibi farklı araçlarla analiz edilebilir.

Ölçümler hafif ve uyarı ve hızlı sorun algılama için yararlı hale getirmek üzere neredeyse gerçek zamanlı senaryolara destek sağlayabilir. 

## <a name="diagnostic-logs"></a>Tanılama günlükleri

 Azure Güvenlik Duvarı'nda aşağıdaki tanılama günlükleri mevcuttur:

* **Uygulama kuralı günlüğü**

   Uygulama kuralı günlüğü, Olay Hub 'larına akışa kaydedilir ve/veya yalnızca her bir Azure Güvenlik Duvarı için etkinleştirdiyseniz Azure Izleyici günlüklerine gönderilir. Yapılandırdığınız uygulama kurallarınızla eşleşen yeni bağlantılar kabul edilen/reddedilen bağlantı için bir günlük oluşturur. Veriler aşağıdaki örnekte gösterildiği gibi JSON biçiminde günlüğe kaydedilir:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Ağ kuralı günlüğü**

   Ağ kuralı günlüğü, Olay Hub 'larına akışa kaydedilir ve/veya yalnızca her bir Azure Güvenlik Duvarı için etkinleştirdiyseniz Azure Izleyici günlüklerine gönderilir. Yapılandırdığınız ağ kurallarınızla eşleşen yeni bağlantılar kabul edilen/reddedilen bağlantı için bir günlük oluşturur. Veriler aşağıdaki örnekte gösterildiği gibi JSON biçiminde günlüğe kaydedilir:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Günlüklerinizi depolamak için kullanabileceğiniz üç seçenek vardır:

* **Depolama hesabı**: Depolama hesaplarının en iyi kullanım amacı, günlüklerin uzun süre depolanması ve ihtiyaç duyulduğunda gözden geçirilmesi durumlarıdır.
* **Event Hubs**: Event Hubs, kaynaklarınızla ilgili uyarılar almak için diğer güvenlik bilgisi ve olay yönetimi (SEIM) araçlarıyla tümleştirmek için idealdir.
* **Azure izleyici günlükleri**: Azure izleyici günlükleri, uygulamanızın genel gerçek zamanlı izleme için en iyi şekilde kullanılır veya eğilimleri göz yormaktadır.

## <a name="activity-logs"></a>Etkinlik günlükleri

   Etkinlik günlüğü girişleri varsayılan olarak toplanır ve bunları Azure portalda görüntüleyebilirsiniz.

   Azure aboneliğinize gönderilen tüm işlemleri görüntülemek için [Azure etkinlik günlüklerini](../azure-resource-manager/resource-group-audit.md) (eski adıyla işlemsel Günlükler ve denetim günlükleri olarak bilinir) kullanabilirsiniz.

## <a name="metrics"></a>Ölçümler

Azure Izleyici ölçümleri, belirli bir zamanda sistemin bazı yönlerini tanımlayan sayısal değerlerdir. Ölçümler her dakika toplanır ve sıklıkla örneklenebilir olduğundan uyarma için faydalıdır. Bir uyarı görece basit mantık ile hızlı bir şekilde tetiklenebilir.

Aşağıdaki ölçümler Azure Güvenlik Duvarı için kullanılabilir:

- **Uygulama kuralları isabet sayısı** -bir uygulama kuralının isabet sayısı.

    Birim: sayım

- **Ağ kuralları isabet sayısı** -bir ağ kuralının isabet sayısı.

    Birim: sayım

- **Işlenen veri** -güvenlik duvarından geçen veri miktarı.

    Birim: bayt

- **Güvenlik duvarı sistem** durumu-güvenlik duvarının sistem durumunu gösterir.

    Birim: yüzde

   Bu ölçümün iki boyutu vardır:
  - **Durum**: olası değerler *sağlıklı*, *düşürülmüş*, *sağlıksız*.
  - **Neden**: güvenlik duvarının ilgili durumunun nedenini gösterir. Örneğin, güvenlik duvarı durumu düşürüldü veya sağlıksız ise, bu, *SNAT bağlantı noktalarını* belirtebilir.





- **SNAT bağlantı noktası kullanımı** -güvenlik duvarı tarafından kullanılan SNAT bağlantı noktalarının yüzdesi.

    Birim: yüzde

   Güvenlik duvarınızdan daha fazla genel IP adresi eklediğinizde, daha fazla SNAT bağlantı noktası kullanılabilir ve bu, SNAT bağlantı noktası kullanımını azaltır. Ayrıca, güvenlik duvarı farklı nedenlerle (örneğin, CPU veya aktarım hızı) ek SNAT bağlantı noktaları da kullanılabilir hale gelir. Bu nedenle, belirli bir SNAT bağlantı noktası kullanımı yüzdesi, yalnızca hizmetin ölçeği değiştiğinden, genel IP adresi eklemeden kapatılabilir. Güvenlik duvarınızdaki bağlantı noktalarını artırmak için kullanılabilen genel IP adresi sayısını doğrudan kontrol edebilirsiniz. Ancak, güvenlik duvarı ölçeklendirmeyi doğrudan kontrol edebilirsiniz. Şu anda, SNAT bağlantı noktaları yalnızca ilk beş genel IP adresi için eklenmiştir.   


## <a name="next-steps"></a>Sonraki adımlar

- Azure Güvenlik Duvarı günlüklerini ve ölçümlerini izlemeyi öğrenmek için bkz. [öğretici: Azure Güvenlik Duvarı günlüklerini izleme](tutorial-diagnostics.md).

- Azure Izleyici 'de ölçümler hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de ölçümler](../azure-monitor/platform/data-platform-metrics.md).