---
title: Azure Güvenlik Duvarı günlüklerine ve ölçümlere genel bakış
description: Güvenlik duvarı günlüklerini kullanarak Azure Güvenlik Duvarı'nı izleyebilirsiniz. Ayrıca etkinlik günlüklerini kullanarak Azure Güvenlik Duvarı kaynaklarıyla ilgili işlemleri denetleyebilirsiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/02/2021
ms.author: victorh
ms.openlocfilehash: 4514717274cfb66fbfc0eee25b09aa9e1234ffad
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280335"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure Güvenlik Duvarı günlükleri ve ölçümleri

Güvenlik duvarı günlüklerini kullanarak Azure Güvenlik Duvarı'nı izleyebilirsiniz. Ayrıca etkinlik günlüklerini kullanarak Azure Güvenlik Duvarı kaynaklarıyla ilgili işlemleri denetleyebilirsiniz.

Bu günlüklerden bazılarına portaldan erişebilirsiniz. Günlükler [Azure izleyici](../azure-monitor/insights/azure-networking-analytics.md)günlüklerine, depolamaya ve Event Hubs gönderilebilir ve Azure izleyici günlüklerinde veya Excel ve Power BI gibi farklı araçlarla analiz edilebilir.

Ölçümler hafif ve uyarı ve hızlı sorun algılama için yararlı hale getirmek üzere neredeyse gerçek zamanlı senaryolara destek sağlayabilir.

## <a name="diagnostic-logs"></a>Tanılama günlükleri

 Azure Güvenlik Duvarı'nda aşağıdaki tanılama günlükleri mevcuttur:

* **Uygulama kuralı günlüğü**

   Uygulama kuralı günlüğü, Olay Hub 'larına akışa kaydedilir ve/veya yalnızca her bir Azure Güvenlik Duvarı için etkinleştirdiyseniz Azure Izleyici günlüklerine gönderilir. Yapılandırdığınız uygulama kurallarınızla eşleşen yeni bağlantılar kabul edilen/reddedilen bağlantı için bir günlük oluşturur. Aşağıdaki örneklerde gösterildiği gibi, veriler JSON biçiminde günlüğe kaydedilir:

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

   ```json
   {
     "category": "AzureFirewallApplicationRule",
     "time": "2018-04-16T23:45:04.8295030Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallApplicationRuleLog",
     "properties": {
         "msg": "HTTPS request from 10.11.2.4:53344 to www.bing.com:443. Action: Allow. Rule Collection: ExampleRuleCollection. Rule: ExampleRule. Web Category: SearchEnginesAndPortals"
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

* **DNS proxy günlüğü**

   DNS proxy günlüğü, bir depolama hesabına kaydedilir, Olay Hub 'larına kaydedilir ve/veya Azure Izleyici günlüklerine yalnızca her bir Azure Güvenlik Duvarı için etkinleştirdiyseniz bu günlüklere gönderilir. Bu günlük, DNS proxy kullanılarak yapılandırılmış bir DNS sunucusuna DNS iletilerini izler. Aşağıdaki örneklerde gösterildiği gibi, veriler JSON biçiminde günlüğe kaydedilir:


   ```
   Category: DNS proxy logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   Başarılı
   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": "DNS Request: 11.5.0.7:48197 – 15676 AAA IN md-l1l1pg5lcmkq.blob.core.windows.net. udp 55 false 512 NOERROR - 0 2.000301956s"
     }
   }
   ```

   Başaramadı

   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": " Error: 2 time.windows.com.reddog.microsoft.com. A: read udp 10.0.1.5:49126->168.63.129.160:53: i/o timeout”
     }
   }
   ```

   ileti biçimi:

   `[client’s IP address]:[client’s port] – [query ID] [type of the request] [class of the request] [name of the request] [protocol used] [request size in bytes] [EDNS0 DO (DNSSEC OK) bit set in the query] [EDNS0 buffer size advertised in the query] [response CODE] [response flags] [response size] [response duration]`

Günlüklerinizi depolamak için kullanabileceğiniz üç seçenek vardır:

* **Depolama hesabı**: Depolama hesaplarının en iyi kullanım amacı, günlüklerin uzun süre depolanması ve ihtiyaç duyulduğunda gözden geçirilmesi durumlarıdır.
* **Event Hubs**: Event Hubs, kaynaklarınızla ilgili uyarılar almak için diğer güvenlik bilgisi ve olay yönetimi (SEIM) araçlarıyla tümleştirmek için idealdir.
* **Azure izleyici günlükleri**: Azure izleyici günlükleri, uygulamanızın genel gerçek zamanlı izleme için en iyi şekilde kullanılır veya eğilimleri göz yormaktadır.

## <a name="activity-logs"></a>Etkinlik günlükleri

   Etkinlik günlüğü girişleri varsayılan olarak toplanır ve bunları Azure portalda görüntüleyebilirsiniz.

   Azure aboneliğinize gönderilen tüm işlemleri görüntülemek için [Azure etkinlik günlüklerini](../azure-resource-manager/management/view-activity-logs.md) (eski adıyla işlemsel Günlükler ve denetim günlükleri olarak bilinir) kullanabilirsiniz.

## <a name="metrics"></a>Ölçümler

Azure Izleyici ölçümleri, belirli bir zamanda sistemin bazı yönlerini tanımlayan sayısal değerlerdir. Ölçümler her dakika toplanır ve sıklıkla örneklenebilir olduğundan uyarma için faydalıdır. Bir uyarı görece basit mantık ile hızlı bir şekilde tetiklenebilir.

Aşağıdaki ölçümler Azure Güvenlik Duvarı için kullanılabilir:

- **Uygulama kuralları isabet sayısı** -bir uygulama kuralının isabet sayısı.

    Birim: sayım

- **Ağ kuralları isabet sayısı** -bir ağ kuralının isabet sayısı.

    Birim: sayım

- **Işlenen veri** -belirli bir zaman penceresinde güvenlik duvarından geçen verilerin toplamı.

    Birim: bayt

- **Aktarım** hızı-güvenlik duvarından geçen verilerin saniye başına oranı.

    Birim: saniye başına bit

- **Güvenlik duvarı sistem** durumu-güvenlik DUVARıNıN, SNAT bağlantı noktası kullanılabilirliğine göre sistem durumunu gösterir.

    Birim: yüzde

   Bu ölçümün iki boyutu vardır:
  - Durum: olası değerler *sağlıklı*, *düşürülmüş*, *sağlıksız*.
  - Neden: güvenlik duvarının ilgili durumunun nedenini gösterir. 

     SNAT bağlantı noktaları %95 > kullanılırsa, tükendikleri kabul edilir ve durum =**düşürülmüş** ve Reason =**SNAT bağlantı noktası** ile sistem durumu %50 olur. Güvenlik duvarı, trafiği işlemeye devam eder ve var olan bağlantılar bu durumdan etkilenmez. Ancak yeni bağlantılar aralıklı olarak kurulamayabilir.

     SNAT bağlantı noktaları %95 < kullanılırsa, güvenlik duvarı sağlıklı olarak değerlendirilir ve sistem durumu %100 olarak gösterilir.

     SNAT bağlantı noktası kullanımı bildirilmezse sistem durumu %0 olarak gösterilir. 

- **SNAT bağlantı noktası kullanımı** -güvenlik duvarı tarafından kullanılan SNAT bağlantı noktalarının yüzdesi.

    Birim: yüzde

   Güvenlik duvarınıza daha fazla genel IP adresi eklediğinizde kullanıma sunulan SNAT bağlantı noktası sayısı artar ve SNAT bağlantı noktası kullanımı azalır. Ayrıca farklı nedenlerden dolayı (CPU veya aktarım hızı gibi) güvenlik duvarının ölçeği genişletildiğinde de ek SNAT bağlantı noktaları kullanıma sunulur. Bu nedenle, belirli bir SNAT bağlantı noktası kullanımı yüzdesi, yalnızca hizmetin ölçeği değiştiğinden, genel IP adresi eklemeden kapatılabilir. Güvenlik duvarınızdaki bağlantı noktalarını artırmak için kullanılabilen genel IP adresi sayısını doğrudan kontrol edebilirsiniz. Ancak, güvenlik duvarı ölçeklendirmeyi doğrudan kontrol edebilirsiniz.

   Güvenlik duvarınız SNAT bağlantı noktası tükenmesi olarak çalışıyorsa, en az beş genel IP adresi eklemeniz gerekir. Bu, kullanılabilir SNAT bağlantı noktası sayısını artırır. Daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Özellikleri](features.md#multiple-public-ip-addresses).


## <a name="next-steps"></a>Sonraki adımlar

- Azure Güvenlik Duvarı günlüklerini ve ölçümlerini izlemeyi öğrenmek için bkz. [öğretici: Azure Güvenlik Duvarı günlüklerini izleme](./firewall-diagnostics.md).

- Azure Izleyici 'de ölçümler hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de ölçümler](../azure-monitor/essentials/data-platform-metrics.md).