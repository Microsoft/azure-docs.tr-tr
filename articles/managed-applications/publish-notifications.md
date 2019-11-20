---
title: Bildirimleri olan Azure yönetilen uygulamalar
description: Yönetilen uygulama örneklerinde oluşturulan, güncelleştirme, silme ve hatalar hakkında bildirim almak için, Web kancası uç noktaları ile yönetilen uygulamayı yapılandırın.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: a00e5be4493b8c8116e2925e88a3ce4bf8cfb722
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085323"
---
# <a name="azure-managed-applications-with-notifications"></a>Bildirimleri olan Azure yönetilen uygulamalar

Azure yönetilen uygulama bildirimleri, yayımcıların yönetilen uygulama örneklerinin yaşam döngüsü olaylarına göre eylemleri otomatikleştirmesine olanak tanır. Yayımcılar, yeni ve mevcut yönetilen uygulama örnekleri hakkında olay bildirimleri almak için özel bildirim Web kancası uç noktaları belirtebilir. Yayımcının uygulama sağlama, güncelleştirme ve silme sırasında özel iş akışlarını ayarlamasına olanak tanır.

## <a name="getting-started"></a>Başlarken
Yönetilen uygulamaları almaya başlamak için genel bir HTTPS uç noktası başlatın ve hizmet kataloğu uygulama tanımını ya da Market Teklifini yayımlarken belirtin.

Hızlıca çalışmaya başlamanıza yönelik önerilen adımlar dizisi aşağıda verilmiştir:
1. Gelen POST isteklerini günlüğe kaydeden ve `200 OK`döndüren bir genel HTTPS uç noktası çalıştırın.
2. Aşağıda açıklandığı gibi, uç noktayı Service Catalog uygulama tanımına veya Market teklifine ekleyin.
3. Uygulama tanımına veya Market teklifine başvuran bir yönetilen uygulama örneği oluşturun.
4. Bildirimlerin başarıyla alındığını doğrulayın.
5. Aşağıdaki **uç nokta kimlik doğrulaması** bölümünde açıklandığı şekilde yetkilendirmeyi etkinleştirin.
6. Bildirim isteklerini ayrıştırmak ve bildirime göre iş mantığınızı uygulamak için aşağıdaki **bildirim şeması** belgelerini izleyin.

## <a name="adding-service-catalog-application-definition-notifications"></a>Hizmet kataloğu uygulama tanımı bildirimleri ekleniyor
#### <a name="azure-portal"></a>Azure portal
Kullanmaya başlamak için lütfen [Azure Portal aracılığıyla bir hizmet kataloğu uygulaması yayımlayın](./publish-portal.md) makalesini okuyun.

![Portalda Hizmet kataloğu uygulama tanımı bildirimleri](./media/publish-notifications/service-catalog-notifications.png)
#### <a name="rest-api"></a>REST API

> [!NOTE]
> Şu anda yalnızca bir uç nokta, uygulama tanımı özelliklerindeki **Notificationendpoints** kapsamında destekleniyor

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="adding-marketplace-managed-application-notifications"></a>Market yönetilen uygulama bildirimleri ekleniyor
Daha fazla bilgi için lütfen bkz. [Azure Uygulama teklifi oluşturma](../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Portalda Hizmet kataloğu uygulama tanımı bildirimleri](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Olay tetikleyicileri
Aşağıdaki tabloda, EventType + ProvisioningState ve tetikleyicilerinin tüm olası bileşimleri açıklanmaktadır:

Türü | ProvisioningState | Bildirim için Tetikle
---|---|---
PUT | Eden | Yönetilen kaynak grubu, uygulama gerçekleştirildikten sonra oluşturulmuştur ve başarıyla tahmin edildi. (Yönetilen RG içindeki dağıtımdan önce devre dışı bırakın.)
PUT | Başarılı oldu | Yönetilen uygulamanın tam olarak sağlanması, bir KONDUKTAN sonra başarılı oldu.
PUT | Başarısız | Herhangi bir noktada uygulama örneği sağlamayı YERLEŞTIRME hatası.
DÜZELTMESI | Başarılı oldu | Etiketlerin, JIT erişim ilkesinin veya yönetilen kimliğin güncelleştirilmesi için yönetilen uygulama örneğindeki başarılı düzeltme ekiyle sonra.
DELETE | Silinmesinden | Kullanıcı, yönetilen uygulama örneğinin bir SILME işlemini başlatır.
DELETE | Silme | Yönetilen uygulamanın tam ve başarılı bir şekilde silinmesinden sonra.
DELETE | Başarısız | Silme işlemini engelleyen kaldırma işlemi sırasında herhangi bir hatadan sonra.
## <a name="notification-schema"></a>Bildirim şeması
Bildirimleri işlemek için Web kancası uç noktanızı başlattığınızda, daha sonra bildirim üzerine işlem yapmak için önemli özellikleri almak üzere yükü ayrıştırmalıdır. Hem hizmet kataloğu hem de Market yönetilen uygulama bildirimleri aşağıda özetlenen küçük farkla aynı özelliklerden çoğunu sağlar.

#### <a name="service-catalog-application-notification-schema"></a>Hizmet kataloğu uygulaması bildirim şeması
Yönetilen bir uygulama örneğinin başarılı bir şekilde sağlanmasından sonra örnek bir hizmet kataloğu bildirimi aşağıda verilmiştir.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Sağlama başarısız olursa, belirtilen uç noktaya hata ayrıntılarına sahip bir bildirim gönderilir.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="marketplace-application-notification-schema"></a>Market uygulama bildirim şeması

Yönetilen bir uygulama örneğinin başarılı bir şekilde sağlanmasından sonra örnek bir hizmet kataloğu bildirimi aşağıda verilmiştir.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Sağlama başarısız olursa, belirtilen uç noktaya hata ayrıntılarına sahip bir bildirim gönderilir.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Parametre | Açıklama
---|---
eventType | Bildirimi tetikleyen olay türü. (ör. "PUT", "PATCH", "DELETE")
Uygulama | Bildirimin tetiklendiği yönetilen uygulamanın tam kaynak tanımlayıcısı. 
eventTime | Bildirimi tetikleyen olayın zaman damgası. (UTC ISO 8601 biçiminde tarih ve saat.)
ProvisioningState | Yönetilen uygulama örneğinin sağlama durumu. (örn. "başarılı", "başarısız", "silme", "silindi")
error | *Yalnızca provisioningState başarısız olduğunda belirtilir*. Hata kodunu, iletisini ve hataya neden olan sorunun ayrıntılarını içerir.
Applicationdefinitionıd | *Yalnızca hizmet kataloğu yönetilen uygulamalar için belirtilir*. Yönetilen uygulama örneğinin sağlandığı uygulama tanımının tam kaynak tanımlayıcısını temsil eder.
plan | *Yalnızca Market yönetilen uygulamalar için belirtilir*. Yönetilen uygulama örneğinin yayımcısını, teklifini, SKU 'sunu ve sürümünü temsil eder.

## <a name="endpoint-authentication"></a>Uç nokta kimlik doğrulaması
Web kancası uç noktasını güvenli hale getirmek ve bildirimin orijinalliğini sağlamak için:
- Web kancası URI 'sinin üst kısmında https://your-endpoint.com?sig=Guidgibi bir sorgu parametresi sağlayın. Her bildirimle, `sig` sorgu parametresinin beklenen değer `Guid`sahip olduğunu hızlı bir şekilde kontrol edin.
- ApplicationId ile yönetilen uygulama örneğinde bir GET verme. Tutarlılık sağlamak için provisioningState 'in bildirimin provisioningState ile eşleştiğini doğrulayın.

## <a name="notification-retries"></a>Bildirim yeniden denemeleri

Yönetilen uygulama bildirimi hizmeti, Web kancası uç noktasından bildirime `200 OK` bir yanıt bekler. Web kancası uç noktası bir HTTP hata kodu döndürürse > = 500, 429 veya uç nokta geçici olarak ulaşılamaz durumdaysa, bildirim hizmeti yeniden denenecek. Web kancası uç noktası 10 saat içinde kullanılabilir olmaz, bildirim iletisi bırakılır ve yeniden denemeler durdurulur.

