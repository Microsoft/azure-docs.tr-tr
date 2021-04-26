---
title: Bildirimleri olan yönetilen uygulamalar
description: Yönetilen uygulama örneklerinde oluşturulan, güncelleştirme, silme ve hatalar hakkında bildirim almak için Web kancası uç noktaları ile yönetilen uygulamaları yapılandırın.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: 2a2e9d429d494c35c49a5b0a3e10b291fd8f24a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633946"
---
# <a name="azure-managed-applications-with-notifications"></a>Bildirimleri olan Azure yönetilen uygulamalar

Azure yönetilen uygulama bildirimleri, yayımcıların yönetilen uygulama örneklerinin yaşam döngüsü olaylarına göre eylemleri otomatikleştirmesine olanak tanır. Yayımcılar, yeni ve mevcut yönetilen uygulama örnekleri hakkında olay bildirimleri almak için özel bildirim Web kancası uç noktaları belirtebilir. Yayımcılar, uygulama sağlama, güncelleştirme ve silme sırasında özel iş akışlarını ayarlayabilir.

## <a name="getting-started"></a>Kullanmaya başlama
Yönetilen uygulamaları almaya başlamak için genel bir HTTPS uç noktası başlatın ve hizmet kataloğu uygulama tanımı veya Azure Market Teklifini yayımladığınızda bunu belirtin.

Hızlıca kullanmaya başlamak için önerilen adımlar aşağıda verilmiştir:
1. Gelen POST isteklerini günlüğe kaydeden ve döndüren bir genel HTTPS uç noktası çalıştırın `200 OK` .
2. Bu makalenin ilerleyen kısımlarında açıklandığı gibi, uç noktayı Service Catalog uygulama tanımına veya Azure Marketi teklifine ekleyin.
3. Uygulama tanımına veya Azure Market teklifine başvuran bir yönetilen uygulama örneği oluşturun.
4. Bildirimlerin alındığını doğrulayın.
5. Bu makalenin **uç nokta kimlik doğrulaması** bölümünde açıklandığı gibi yetkilendirmeyi etkinleştirin.
6. Bildirim isteklerini ayrıştırmak ve bildirime göre iş mantığınızı uygulamak için bu makalenin **bildirim şeması** bölümündeki yönergeleri izleyin.

## <a name="add-service-catalog-application-definition-notifications"></a>Hizmet kataloğu uygulama tanımı bildirimleri ekleme
#### <a name="azure-portal"></a>Azure portalı
Başlamak için bkz. [Azure Portal bir hizmet kataloğu uygulaması yayımlama](./publish-portal.md).

![Azure portal Hizmet kataloğu uygulama tanımı bildirimleri](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> Şu anda, uygulama tanımı özelliklerinde yalnızca bir uç nokta sağlayabilirsiniz `notificationEndpoints` .

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
## <a name="add-azure-marketplace-managed-application-notifications"></a>Azure Marketi yönetilen uygulama bildirimleri ekleme
Daha fazla bilgi için bkz. [Azure Uygulama teklifi oluşturma](../../marketplace/create-new-azure-apps-offer.md).

![Azure portal Azure Marketi yönetilen uygulama bildirimleri](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Olay tetikleyicileri
Aşağıdaki tabloda, EventType ve ProvisioningState 'in tüm olası birleşimleri ve Tetikleyicileri açıklanmaktadır:

Olay türü | ProvisioningState | Bildirim için Tetikle
---|---|---
PUT | Kabul edildi | Yönetilen kaynak grubu, uygulama KONDUKTAN sonra (yönetilen kaynak grubundaki dağıtıma konmadan önce) başarıyla oluşturuldu ve yansıtılmıştır.
PUT | Başarılı | Yönetilen uygulamanın tam olarak sağlanması, bir KONDUKTAN sonra başarılı oldu.
PUT | Başarısız | Herhangi bir noktada uygulama örneği sağlamayı YERLEŞTIRME hatası.
DÜZELTMESI | Başarılı | Etiketler, JıT erişim ilkesi veya yönetilen kimlik güncelleştirmek için yönetilen uygulama örneğindeki başarılı bir düzeltme ekiyle sonra.
DELETE | Siliniyor | Kullanıcı, yönetilen uygulama örneğinin bir SILME işlemini başlatır.
DELETE | Silindi | Yönetilen uygulamanın tam ve başarılı bir şekilde silinmesinden sonra.
DELETE | Başarısız | Silme işlemini engelleyen kaldırma işlemi sırasında herhangi bir hatadan sonra.
## <a name="notification-schema"></a>Bildirim şeması
Bildirimleri işlemek için Web kancası uç noktanızı başlattığınızda, daha sonra bildirim üzerine işlem yapmak için önemli özellikleri almak üzere yükü ayrıştırmalıdır. Hizmet kataloğu ve Azure Marketi yönetilen uygulama bildirimleri, aynı özelliklerin çoğunu sağlar. İki küçük fark, örnekleri takip eden tabloda özetlenmiştir.

#### <a name="service-catalog-application-notification-schema"></a>Hizmet kataloğu uygulaması bildirim şeması
Yönetilen bir uygulama örneğinin başarıyla sağlanmasından sonra örnek bir hizmet kataloğu bildirimi aşağıda verilmiştir:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
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
    "applicationDefinitionId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
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

#### <a name="azure-marketplace-application-notification-schema"></a>Azure Market uygulaması bildirim şeması

Yönetilen bir uygulama örneğinin başarıyla sağlanmasından sonra örnek bir hizmet kataloğu bildirimi aşağıda verilmiştir:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
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
    "applicationId": "/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
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
eventType | Bildirimi tetikleyen olay türü. (Örneğin, PUT, PATCH, DELETE.)
applicationId | Bildirimin tetiklendiği yönetilen uygulamanın tam kaynak tanımlayıcısı.
eventTime | Bildirimi tetikleyen olayın zaman damgası. (UTC ISO 8601 biçiminde tarih ve saat.)
provisioningState | Yönetilen uygulama örneğinin sağlama durumu. (Örneğin, başarılı, başarısız, silme, silindi.)
error | *Yalnızca provisioningState başarısız olduğunda belirtilir*. Hata kodunu, iletisini ve hataya neden olan sorunun ayrıntılarını içerir.
Applicationdefinitionıd | *Yalnızca hizmet kataloğu yönetilen uygulamaları Için belirtilir*. Yönetilen uygulama örneğinin sağlandığı uygulama tanımının tam kaynak tanımlayıcısını temsil eder.
plan | *Yalnızca Azure Marketi tarafından yönetilen uygulamalar Için belirtilir*. Yönetilen uygulama örneğinin yayımcısını, teklifini, SKU 'sunu ve sürümünü temsil eder.
billingDetails | *Yalnızca Azure Marketi tarafından yönetilen uygulamalar için belirtilir.* Yönetilen uygulama örneğinin fatura ayrıntıları. Kullanım ayrıntıları için Azure Marketi 'ni sorgulamak üzere kullanabileceğiniz Resourceusageıd 'yi içerir.

## <a name="endpoint-authentication"></a>Uç nokta kimlik doğrulaması
Web kancası uç noktasını güvenli hale getirmek ve bildirimin orijinalliğini sağlamak için:
1. Web kancası URI 'sinin üstünde şu şekilde bir sorgu parametresi sağlayın: https \: //Your-Endpoint.com? SIG = GUID. Her bildirimle, sorgu parametresinin `sig` beklenen değere sahip olup olmadığını denetleyin `Guid` .
2. ApplicationId kullanarak yönetilen uygulama örneğinde bir GET verme. Tutarlılık sağlamak için provisioningState 'in bildirimin provisioningState ile eşleştiğini doğrulayın.

## <a name="notification-retries"></a>Bildirim yeniden denemeleri

Yönetilen uygulama bildirim hizmeti, `200 OK` Web kancası uç noktasından bildirime yanıt bekliyor. Web kancası uç 500 noktası, 429 hata kodu döndürürse veya uç nokta geçici olarak ulaşılamaz durumdaysa, bildirim hizmeti yeniden dener. Web kancası uç noktası 10 saat içinde kullanılabilir olmaz, bildirim iletisi bırakılır ve yeniden denemeler durdurulur.
