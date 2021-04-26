---
title: Uygulamalar için hizmet koşulları ve gizlilik bildirimi | Mavisi
description: Azure AD 'yi kullanmak üzere kayıtlı uygulamalar için hizmet koşulları 'nı ve gizlilik bildirimi 'ni nasıl yapılandırabileceğinizi öğrenin.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 311dfd976610c392909a0ec3d91fecaa4d733539
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104204"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Nasıl yapılır: bir uygulama için hizmet koşullarını ve gizlilik bildirimini yapılandırma

Azure Active Directory (Azure AD) ve Microsoft hesaplarıyla tümleştirilen uygulamaları oluşturan ve yöneten geliştiriciler, uygulamanın hizmet koşulları ve gizlilik bildirimi bağlantılarını içermelidir. Hizmet koşulları ve gizlilik bildirimi, kullanıcılar tarafından Kullanıcı onay deneyimi aracılığıyla ortaya çıkmış. Kullanıcılarınıza, uygulamanıza güvenebileceklerini bilmeleri için yardımcı olurlar. Hizmet koşulları ve gizlilik bildirimi, birden çok dizin tarafından kullanılan veya tüm Microsoft hesabı kullanılabilen çok kiracılı uygulamalar için özellikle önemlidir.

Uygulamanız için hizmet koşulları ve gizlilik bildirimi belgelerinin oluşturulması ve bu belgelere URL 'Ler sağlanması sizin sorumluluğunuzdadır. Bu bağlantıları sağlamayan çok kiracılı uygulamalarda, uygulamanız için Kullanıcı onay deneyimi bir uyarı gösterir ve bu da kullanıcıların uygulamanıza uyum sağlamasını engelleyebilir.

> [!NOTE]
> * Tek kiracılı uygulamalar, bir uyarı göstermez.
> * İki bağlantılardan biri veya her ikisi de eksikse, uygulamanız bir uyarı gösterir.

## <a name="user-consent-experience"></a>Kullanıcı onay deneyimi

Aşağıdaki örneklerde, hizmet koşulları ve gizlilik bildirimi yapılandırıldığında ve bu bağlantılar yapılandırılmadığında Kullanıcı onay deneyimi gösterilmektedir.

![Gizlilik bildirimi ve hizmet koşulları ile ve olmadan ekran görüntüleri](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Hizmet koşulları ve gizlilik bildirimi belgeleri için bağlantıları biçimlendirme

Uygulamanızın hizmet koşulları ve gizlilik bildirimi belgeleri için bağlantıları eklemeden önce, URL 'Lerin bu yönergeleri izlediğinden emin olun.

| Yönerge     | Açıklama                           |
|---------------|---------------------------------------|
| Biçim        | Geçerli URL                             |
| Geçerli şemalar | HTTP ve HTTPS<br/>HTTPS önerilir |
| Uzunluk üst sınırı    | 2048 karakter                       |

Örnekler: `https://myapp.com/terms-of-service` ve `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Hizmet koşullarına ve gizlilik bildirimine bağlantılar ekleme

Hizmet koşulları ve gizlilik bildirimi hazırlandığı zaman, aşağıdaki yöntemlerden birini kullanarak uygulamanıza bu belgelere bağlantılar ekleyebilirsiniz:

* [Azure portal aracılığıyla](#azure-portal)
* [JSON uygulama nesnesini kullanma](#app-object-json)
* [Microsoft Graph API 'sini kullanma](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>Azure portalını kullanma
Azure portal aşağıdaki adımları izleyin.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> oturum açın ve doğru Azure AD kiracısını (B2C değil) seçin.
2. **Uygulama kayıtları** bölümüne gidin ve uygulamanızı seçin.
3. **Yönet** altında **marka**' i seçin.
4. **Hizmet koşulları URL 'sini** ve **Gizlilik bildirimi URL 'si** alanlarını doldurun.
5. **Kaydet**’i seçin.

    ![Uygulama özellikleri, hizmet koşullarını ve gizlilik bildirimi URL 'Lerini içerir](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>JSON uygulama nesnesini kullanma

JSON uygulama nesnesini doğrudan değiştirmeyi tercih ediyorsanız, uygulamanızın hizmet koşullarına ve gizlilik bildirimine yönelik bağlantıları dahil etmek için Azure portal veya uygulama kayıt portalındaki bildirim düzenleyicisini kullanabilirsiniz.

1. **Uygulama kayıtları** bölümüne gidip uygulamanızı seçin.
2. **Bildirim** bölmesini açın.
3. CTRL + F, "ınformationalurls" araması yapın. Bilgileri girin.
4. Yaptığınız değişiklikleri kaydedin.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>Microsoft Graph API 'sini kullanma

Tüm uygulamalarınızı program aracılığıyla güncelleştirmek için, tüm uygulamalarınızı hizmet koşulları ve gizlilik bildirimi belgelerinin bağlantılarını içerecek şekilde güncelleştirmek üzere Microsoft Graph API 'sini kullanabilirsiniz.

```
PATCH https://graph.microsoft.com/v1.0/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Bu alanlardan birine atadığınız önceden varolan değerlerin üzerine yazılmamaya dikkat edin: `supportUrl` , `marketingUrl` ve `logoUrl`
> * Microsoft Graph API yalnızca bir Azure AD hesabıyla oturum açtığınızda işe yarar. Kişisel Microsoft hesapları desteklenmez.
