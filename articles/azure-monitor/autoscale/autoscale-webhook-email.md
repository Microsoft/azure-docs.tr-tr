---
title: E-posta ve Web kancası uyarı bildirimlerini göndermek için otomatik ölçeklendirmeyi kullanın
description: Otomatik ölçeklendirme eylemlerini kullanarak Web URL 'Lerini çağırma veya Azure Izleyici 'de e-posta bildirimleri gönderme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: 3b1f13fd1ce8bedcbe58385d4cee321f1d1405df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100623791"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Azure Izleyici 'de e-posta ve Web kancası uyarı bildirimleri göndermek için otomatik ölçeklendirme eylemlerini kullanma
Bu makalede, belirli Web URL 'Lerini çağırabilmeniz veya Azure 'daki otomatik ölçeklendirme eylemlerine göre e-posta gönderebilmek için Tetikleyicileri ayarlama yöntemi gösterilmektedir.  

## <a name="webhooks"></a>Web Kancaları
Web kancaları, Azure uyarı bildirimlerini, işleme sonrası veya özel bildirimler için diğer sistemlere yönlendirmenize olanak tanır. Örneğin, uyarıyı SMS göndermek, hataları günlüğe kaydetmek, sohbet veya mesajlaşma hizmetleri kullanarak bir takıma bildirimde bulunmak için gelen bir web isteğini işleyebilen hizmetlere yönlendirme. Web kancası URI 'SI geçerli bir HTTP veya HTTPS uç noktası olmalıdır.

## <a name="email"></a>E-posta
E-posta, geçerli herhangi bir e-posta adresine gönderilebilir. Kuralın çalıştığı aboneliğin yöneticileri ve ortak yöneticileri de bilgilendirilir.

## <a name="cloud-services-and-app-services"></a>Cloud Services ve uygulama hizmetleri
Cloud Services ve sunucu grupları (App Services) için Azure portal tercih edebilirsiniz.

* Ölçüyü ölçmeye **göre** seçin.

![ölçek ölçütü](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Sanal Makine ölçek kümeleri
Kaynak Yöneticisi (sanal makine ölçek kümeleri) ile oluşturulan daha yeni sanal makineler için, bunu REST API, Kaynak Yöneticisi şablonları, PowerShell ve CLı kullanarak yapılandırabilirsiniz. Portal arabirimi henüz kullanılabilir değil.
REST API veya Kaynak Yöneticisi şablonunu kullanırken, aşağıdaki seçeneklerle birlikte, bir sonraki [Ölçek ayarlarınıza](/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) bildirim öğesi ekleyin.

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

| Alan | Girilmesi? | Description |
| --- | --- | --- |
| operation |evet |değer "Scale" olmalıdır |
| sendToSubscriptionAdministrator |evet |değer "true" veya "false" olmalıdır |
| sendToSubscriptionCoAdministrators |evet |değer "true" veya "false" olmalıdır |
| Customepostaları |evet |değer null [] veya e-postaların dize dizisi olabilir |
| Web kancaları |evet |değer null veya geçerli bir URI olabilir |
| serviceUri |evet |geçerli bir HTTPS URI 'Si |
| properties |evet |değer boş olmalı {} veya anahtar-değer çiftleri içerebilir |

## <a name="authentication-in-webhooks"></a>Web kancalarında kimlik doğrulama
Web kancası, belirteç kimliği URI 'sini bir sorgu parametresi olarak bir belirteç kimliğiyle kaydettiğiniz belirteç tabanlı kimlik doğrulaması kullanarak kimlik doğrulaması yapabilir. Örneğin, https: \/ /mysamplealert/webcallback? tokenId = sometokenıd&someparameter = someValue

## <a name="autoscale-notification-webhook-payload-schema"></a>Otomatik ölçeklendirme bildirimi Web kancası yük şeması
Otomatik ölçeklendirme bildirimi oluşturulduğunda aşağıdaki meta veriler Web kancası yüküne dahil edilir:

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


| Alan | Girilmesi? | Description |
| --- | --- | --- |
| durum |evet |Bir otomatik ölçeklendirme eyleminin oluşturulduğunu gösteren durum |
| operation |evet |Örneklerin artması için, "ölçeği genişletme" ve örneklerde bir azalma için "ölçeklendirilmesi" olacaktır. |
| bağlam |evet |Otomatik ölçeklendirme eylemi bağlamı |
| timestamp |evet |Otomatik ölçeklendirme eyleminin tetiklendiği zaman damgası |
| kimlik |Yes |Otomatik ölçeklendirme ayarının Kaynak Yöneticisi KIMLIĞI |
| name |Yes |Otomatik ölçeklendirme ayarının adı |
| bilgileri |Yes |Otomatik ölçeklendirme hizmetinin aldığı eylemin açıklaması ve örnek sayısında değişiklik |
| subscriptionId |Yes |Ölçeklendirilen hedef kaynağın abonelik KIMLIĞI |
| resourceGroupName |Yes |Ölçeklendirilen hedef kaynağın kaynak grubu adı |
| resourceName |Yes |Ölçeklendirilen hedef kaynağın adı |
| resourceType |Yes |Desteklenen üç değer: "Microsoft. classiccompute/DomainNames/yuvalar/roller"-bulut hizmeti rolleri, "Microsoft. COMPUTE/virtualmachinescalesets"-sanal makine ölçek kümeleri ve "Microsoft. Web/sunucugrupları"-Web uygulaması |
| resourceId |Yes |Ölçeklendirilen hedef kaynağın Kaynak Yöneticisi KIMLIĞI |
| Portal bağlantısı |Yes |Hedef kaynağın özet sayfasına Azure portal bağlantı |
| oldCapacity |Yes |Otomatik ölçeklendirme bir ölçeklendirme eylemi gerçekleştirirse geçerli (eski) örnek sayısı |
| newCapacity |Yes |Otomatik olarak kaynağı ölçeklendirilen yeni örnek sayısı |
| properties |No |İsteğe bağlı. <anahtar, değer> çiftleri (örneğin, sözlük <dizesi, dize>) kümesi. Özellikler alanı isteğe bağlıdır. Özel bir kullanıcı arabiriminde veya mantıksal uygulama tabanlı iş akışında, yük kullanılarak geçirilebilecek anahtar ve değerleri girebilirsiniz. Özel özellikleri giden Web kancası çağrısına geri geçirmenin alternatif bir yolu, Web kancası URI 'sinin kendisini kullanmaktır (sorgu parametreleri olarak) |
