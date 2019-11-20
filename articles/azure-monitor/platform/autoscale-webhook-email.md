---
title: E-posta ve Web kancası uyarı bildirimleri göndermek için otomatik ölçeklendirmeyi kullanma
description: "Otomatik ölçeklendirme eylemlerinin web URL'leri çağırma veya Azure İzleyici'de e-posta bildirimleri göndermek için nasıl kullanılacağı konusuna bakın. "
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: c1386f4058f9490bad0161b680005db6031bace1
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491520"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>E-posta ve Web kancası, Azure İzleyici'de uyarı bildirimleri göndermek için otomatik ölçeklendirme eylemleri kullanın
Bu makalede, böylece belirli web URL'lerini çağırma veya Azure otomatik ölçeklendirme eylemleri göre e-posta Gönder Tetikleyiciler nasıl kümesi gösterilmektedir.  

## <a name="webhooks"></a>Web Kancaları
Web kancaları, işlem sonrası veya özel bildirimleri için diğer sistemlere Azure uyarı bildirimleri yönlendirmek olanak sağlar. Örneğin, uyarı vb. sohbet kullanarak veya Mesajlaşma bir team services ile SMS, günlük hataları, bildirim göndermek için bir gelen web isteklerini işleyebilir Hizmetleri için yönlendirme. Web kancası URI geçerli bir HTTP veya HTTPS uç noktası olmalıdır.

## <a name="email"></a>Email
E-posta, herhangi bir geçerli e-posta adresine gönderilebilir. Kural çalıştığı aboneliğin Yöneticiler ve ortak de bildirim alırsınız.

## <a name="cloud-services-and-web-apps"></a>Bulut Hizmetleri ve Web uygulamaları
Azure portalından bulut Hizmetleri ve sunucu grupları (Web uygulamaları) için katılımı.

* Seçin **ölçeklendirilmesine** ölçümü.

![göre ölçeklendirin](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Sanal Makine ölçek kümeleri
Daha yeni Resource Manager (sanal makine ölçek kümeleri) ile oluşturulan sanal makineler için bunu REST API, Resource Manager şablonları, PowerShell ve CLI kullanarak yapılandırabilirsiniz. Bir portal arabirimi henüz kullanılamıyor.
REST API veya Resource Manager şablonu kullanarak, aşağıdaki seçeneklerle bildirimler öğesi ekleyin.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
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

| Alan | Zorunlu? | Açıklama |
| --- | --- | --- |
| operation |evet |"Ölçek" değeri olmalıdır |
| sendToSubscriptionAdministrator |evet |değer "true" veya "false" olmalıdır. |
| sendToSubscriptionCoAdministrators |evet |değer "true" veya "false" olmalıdır. |
| customEmails |evet |değer null [] veya e-postaları dize dizisi olabilir |
| webhooks |evet |değer null veya geçerli bir URI olabilir |
| serviceUri |evet |Geçerli bir https URI |
| properties |evet |Değer boş olmalıdır {} veya anahtar-değer çiftleri içerebilir |

## <a name="authentication-in-webhooks"></a>Web kancaları kimlik doğrulaması
Web kancası belirteci kimliği ile bir sorgu parametresi olarak Web kancası URI kaydetmek belirteç tabanlı kimlik doğrulamasını kullanarak kimlik doğrulaması yapabilir. Örneğin, https: \/ /mysamplealert/webcallback? tokenıd = sometokenid & someparameter in değeri birdeğer =

## <a name="autoscale-notification-webhook-payload-schema"></a>Otomatik ölçeklendirme bildirim Web kancası yükü şeması
Otomatik ölçeklendirme bildirim oluşturulduğunda, aşağıdaki meta verileri Web kancası yükteki dahildir:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Alan | Zorunlu? | Açıklama |
| --- | --- | --- |
| status |evet |Otomatik ölçeklendirme eylemi oluşturulduğunu gösteren durum |
| operation |evet |Bir artış örnekleri için "Ölçeği genişletme" olacaktır ve durumlarda bir azaltmak için "ölçek" olacaktır |
| context |evet |Otomatik ölçeklendirme eylem bağlamı |
| timestamp |evet |Otomatik ölçeklendirme eylemi tetiklendiğinde zaman damgası |
| id |Evet |Resource Manager Kimliğini otomatik ölçeklendirme ayarı |
| name |Evet |Otomatik ölçeklendirme ayarının adı |
| details |Evet |Otomatik ölçeklendirme hizmeti sürdü eylem ve örnek sayısını değişiklik açıklaması |
| subscriptionId |Evet |Abonelik kimliği ölçeklendirilir hedef kaynak |
| resourceGroupName |Evet |Ölçeklendirilen hedef kaynağın kaynak grubu adı |
| resourceName |Evet |Ölçeklendirilen hedef kaynağın adı |
| resourceType |Evet |Üç desteklenen değerler: "microsoft.classiccompute/domainnames/slots/roles" - bulut hizmeti rolleri, "microsoft.compute/virtualmachinescalesets" - sanal makine ölçek kümeleri ve "Microsoft.Web/serverfarms" - Web uygulaması |
| resourceId |Evet |Resource Manager Kimliğini ölçeklendirilir hedef kaynak |
| portalLink |Evet |Özet sayfasında, hedef kaynağı için Azure portal bağlantısı |
| oldCapacity |Evet |Bir ölçek eylemi otomatik ölçeklendirme geçen zaman geçerli (eski) örnek sayısı |
| newCapacity |Evet |Otomatik ölçeklendirme kaynağa ölçeği yeni bir örnek sayısı |
| properties |Hayır |İsteğe bağlı. < Anahtar değer > dizi çiftlerini (örneğin, sözlük < String, String >). Özellikler alanı isteğe bağlıdır. Özel kullanıcı arabirimi veya mantıksal uygulama temel iş akışı, anahtarları ve yükü kullanılarak geçirilebilir değerleri girebilirsiniz. Özel özellikler giden Web kancası çağrısı geçirmek için alternatif bir yolu Web kancası kendisi (olarak URI sorgu parametreleri) kullanmaktır. |

