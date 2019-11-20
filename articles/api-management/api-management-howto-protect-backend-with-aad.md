---
title: Azure Active Directory ve API Management ile OAuth 2,0 kullanarak API 'YI koruma | Microsoft Docs
description: Azure Active Directory ve API Management ile Web API arka ucunu nasıl koruyacağınızı öğrenin.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 653089042c87b3223b3de048b6f12056d04b0f3c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806333"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Azure Active Directory ve API Management ile OAuth 2,0 kullanarak API 'YI koruma

Bu kılavuzda, Azure Active Directory (Azure AD) ile OAuth 2,0 protokolünü kullanarak bir API 'yi korumak üzere Azure API Management örneğinizi nasıl yapılandırabileceğiniz gösterilmektedir. 

> [!NOTE]
> Bu özellik, API Management **Geliştirici**, **Standart** ve **Premium** katmanlarında kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar
Bu makaledeki adımları izlemek için, şunları yapmanız gerekir:
* Bir API Management örneği
* API Management örneğini kullanan yayımlanmış bir API
* Bir Azure AD kiracısı

## <a name="overview"></a>Genel Bakış

Adımlara hızlı bir genel bakış aşağıda verilmiştir:

1. API 'yi göstermek için Azure AD 'de bir uygulamayı (arka uç uygulaması) kaydedin.
2. API 'yi çağırması gereken bir istemci uygulamasını göstermek için Azure AD 'de başka bir uygulamayı (istemci-uygulama) kaydedin.
3. Azure AD 'de, istemci uygulamanın arka uç uygulamasını çağırmasını sağlamak için izin verin.
4. API 'yi OAuth 2,0 kullanıcı yetkilendirmesi kullanarak çağırmak için geliştirici konsolunu yapılandırın.
5. Her gelen istek için OAuth belirtecini doğrulamak üzere **Validate-JWT** ilkesini ekleyin.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>API 'YI göstermek için bir uygulamayı Azure AD 'ye kaydetme

Bir API 'yi Azure AD ile korumak için ilk adım, API 'YI temsil eden bir uygulamayı Azure AD 'ye kaydettirebilir. 

1. [Azure portal uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasına gidin. 

1. **Yeni kayıt**seçeneğini belirleyin. 

1. **Uygulama kaydet** sayfası göründüğünde uygulamanızın kayıt bilgilerini girin: 
    - **Ad** alanına uygulama kullanıcılarına gösterilecek anlamlı bir uygulama adı girin, örneğin `backend-app`. 
    - **Desteklenen hesap türleri** bölümünde, senaryonuza uygun bir seçenek belirleyin. 

1. **Yeniden yönlendirme URI 'si** bölümünü boş bırakın.

1. Uygulamayı kaydetmek için **Kaydet**'i seçin. 

1. Uygulamaya **genel bakış** sayfasında, **uygulama (istemci) kimlik** değerini bulun ve daha sonra için kaydedin.

Uygulama oluşturulduğunda, sonraki adımda kullanmak üzere **uygulama kimliği**' ni bir yere getirin. 

1. **BIR API 'Yi kullanıma** sunma ' yı seçin ve **Kaydet** ' e tıklayıp bir uygulama kimliği URI 'si oluşturun.

1. **Kapsam Ekle** SAYFASıNDA, API tarafından desteklenen yeni bir kapsam oluşturun. (örn., okuma) ve kapsamı oluşturmak için *Kapsam Ekle* ' ye tıklayın. API 'niz tarafından desteklenen tüm kapsamları eklemek için bu adımı tekrarlayın.

1. Kapsam oluşturulduğunda, sonraki adımda kullanmak üzere bunu bir yere unutmayın. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Bir istemci uygulamasını göstermek için Azure AD 'de başka bir uygulamayı kaydetme

API 'YI çağıran her istemci uygulamasının, Azure AD 'de bir uygulama olarak kaydedilmesi gerekir. Bu örnekte, istemci uygulaması API Management geliştirici portalındaki geliştirici konsoludur. Geliştirici konsolunu göstermek için Azure AD 'de başka bir uygulamanın nasıl kaydedileceği aşağıda açıklanmaktadır.

1. [Azure portal uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasına gidin. 

1. **Yeni kayıt**seçeneğini belirleyin.

1. **Uygulama kaydet** sayfası göründüğünde uygulamanızın kayıt bilgilerini girin: 
    - **Ad** alanına uygulama kullanıcılarına gösterilecek anlamlı bir uygulama adı girin, örneğin `client-app`. 
    - **Desteklenen hesap türleri** bölümünde, **herhangi bir kuruluş dizininde hesaplar**' ı seçin. 

1. **Yeniden yönlendirme URI** 'si bölümünde URL 'yi `Web` seçip girin`https://contoso5.portal.azure-api.net/signin`

1. Uygulamayı kaydetmek için **Kaydet**'i seçin. 

1. Uygulamaya **genel bakış** sayfasında, **uygulama (istemci) kimlik** değerini bulun ve daha sonra için kaydedin.

Şimdi bu uygulama için bir sonraki adımda kullanmak üzere bir istemci gizli anahtarı oluşturun.

1. İstemci uygulamanızın sayfa listesinden **sertifikalar & parolaları**' nı seçin ve **yeni istemci parolası**' nı seçin.

1. **İstemci parolası Ekle**altına bir **Açıklama**girin. Anahtarın ne zaman sona ereceğini seçin ve **Ekle**' yi seçin.

Gizli dizi oluşturulduğunda, sonraki adımda kullanmak üzere anahtar değerini bir yere getirin. 

## <a name="grant-permissions-in-azure-ad"></a>Azure AD 'de izin verme

API 'yi ve geliştirici konsolunu temsil etmek üzere iki uygulama kaydettirdiğiniz için, istemci uygulamanın arka uç uygulamasını çağırmasını sağlamak için izinler vermeniz gerekir.  

1. **Uygulama kayıtları**gidin. 

1. Uygulama `client-app`için sayfa listesinde ve ' ı seçin, **API izinleri**' ne gidin.

1. **Izin Ekle**' yi seçin.

1. **BIR API seçin**altında bulun ve seçin `backend-app`.

1. **Temsilci izinleri**altında, uygun izinleri `backend-app` seçin ve ardından **izin Ekle**' ye tıklayın.

1. İsteğe bağlı olarak, **API izinleri** sayfasında, bu dizindeki tüm kullanıcılar adına izin vermek için sayfanın altındaki **<-kiracı adı > Için yönetici izni ver** ' e tıklayın. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Geliştirici konsolunda OAuth 2,0 Kullanıcı yetkilendirmesini etkinleştirme

Bu noktada, uygulamalarınızı Azure AD 'de oluşturdunuz ve istemci uygulamanın arka uç uygulamasını çağırmasını sağlamak için uygun izinler vermiş olursunuz. 

Bu örnekte, Geliştirici Konsolu istemci-uygulama ' dır. Aşağıdaki adımlarda, geliştirici konsolunda OAuth 2,0 Kullanıcı yetkilendirmesinin nasıl etkinleştirileceği açıklanır. 

1. Azure portal, API Management örneğinize gidin.

1. **OAuth 2,0** > **Ekle**' yi seçin.

1. Bir **görünen ad** ve **Açıklama**sağlayın.

1. **İstemci kayıt sayfası URL 'si**için, gibi bir yer tutucu değeri `http://localhost`girin. **İstemci kayıt sayfası URL 'si** , kullanıcıların bunu destekleyen OAuth 2,0 sağlayıcıları için kendi hesaplarını oluşturmak ve yapılandırmak üzere kullanabileceği bir sayfaya işaret eder. Bu örnekte, kullanıcılar kendi hesaplarını oluşturmaz ve yapılandırmadıkları için bunun yerine bir yer tutucu kullanırsınız.

1. **Yetkilendirme verme türleri**için **yetkilendirme kodu**' nu seçin.

1. **Yetkilendirme uç noktası URL 'sini** ve **belirteç uç noktası URL 'sini**belirtin. Azure AD kiracınızdaki **uç noktalar** sayfasından bu değerleri alın. **Uygulama kayıtları** sayfasına tekrar gidin ve **uç noktalar**' ı seçin.


1. **OAuth 2,0 yetkilendirme uç noktasını**kopyalayın ve **Yetkilendirme uç noktası URL 'si** metin kutusuna yapıştırın. Yetkilendirme isteği yöntemi altında **gönderi** ' ı seçin.

1. **OAuth 2,0 belirteç uç noktasını**kopyalayın ve **token Endpoint URL** metin kutusuna yapıştırın. 

    >[!IMPORTANT]
    > **V1** veya **v2** uç noktalarını kullanabilirsiniz. Ancak, seçtiğiniz sürüme bağlı olarak aşağıdaki adım farklı olacaktır. V2 uç noktaları kullanmanızı öneririz. 

1. **V1** uç noktaları kullanırsanız, **kaynak**adlı bir gövde parametresi ekleyin. Bu parametrenin değeri için arka uç uygulamasının **uygulama kimliği** ' ni kullanın. 

1. **V2** uç noktaları kullanıyorsanız, **varsayılan kapsam** alanındaki arka uç uygulaması için oluşturduğunuz kapsamı kullanın.

1. Ardından, istemci kimlik bilgilerini belirtin. Bunlar, istemci uygulaması için kimlik bilgileridir.

1. **ISTEMCI kimliği**için, Istemci UYGULAMANıN **uygulama kimliğini** kullanın.

1. **İstemci parolası**için, daha önce istemci uygulaması için oluşturduğunuz anahtarı kullanın. 

1. İstemci gizliliğini hemen takip eden, yetkilendirme kodu verme türü için **redirect_url** . Bu URL 'YI bir yere getirin.

1. **Oluştur**’u seçin.

1. İstemci uygulamanızın **Ayarlar** sayfasına dönün.

1. **Yanıt URL 'leri**' ni seçin ve **redirect_url** ilk satıra yapıştırın. Bu örnekte, ilk satırdaki URL `https://localhost` ile değiştirdik.  

Bir OAuth 2,0 yetkilendirme sunucusu yapılandırdığınıza göre, Geliştirici Konsolu Azure AD 'den erişim belirteçleri alabilir. 

Sonraki adım, API 'niz için OAuth 2,0 Kullanıcı yetkilendirmesini etkinleştirmektir. Bu, API 'nize çağrı yapmadan önce geliştirici konsolunun Kullanıcı adına bir erişim belirteci alması gerektiğini bilmesini sağlar.

1. API Management örneğinizi inceleyin ve **API**'lere gidin.

2. Korumak istediğiniz API 'YI seçin. Örneğin, kullanabilirsiniz `Echo API`.

3. Git **ayarları**.

4. **Güvenlik**altında **OAuth 2,0**' ı seçin ve daha önce yapılandırdığınız OAuth 2,0 sunucusunu seçin. 

5. **Kaydet**’i seçin.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>API 'yi geliştirici portalından başarıyla çağırma

> [!NOTE]
> Bu bölüm, geliştirici portalını desteklemeyen **Tüketim** katmanına uygulanmaz.

Artık OAuth 2,0 kullanıcı yetkilendirmesi API 'niz üzerinde etkin olduğuna göre, Geliştirici Konsolu API 'yi çağırmadan önce Kullanıcı adına bir erişim belirteci alır.

1. Geliştirici portalındaki API altında herhangi bir işleme gidin ve **deneyin**' i seçin. Bu, sizi geliştirici konsoluna getirir.

2. Yeni eklediğiniz yetkilendirme sunucusuna karşılık gelen **Yetkilendirme** bölümünde yeni bir öğe olduğunu aklınızda edin.

3. Yetkilendirme açılan listesinden **yetkilendirme kodu** ' nu seçin ve Azure AD kiracısında oturum açmanız istenir. Zaten hesapla oturum açtıysanız, bu durum istenmez.

4. Başarılı oturum açma işleminden sonra, Azure `Authorization` ad 'den bir erişim belirteciyle isteğe bir üst bilgi eklenir. Örnek belirteç (Base64 kodlamalı) aşağıda verilmiştir:

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. **Gönder**' i seçin ve API 'yi başarıyla çağırabilirsiniz.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>İstekleri önceden yetkilendirmek için bir JWT doğrulama ilkesi yapılandırma

Bu noktada, bir Kullanıcı geliştirici konsolundan bir çağrı yapmaya çalıştığında, kullanıcıdan oturum açması istenir. Geliştirici Konsolu kullanıcı adına bir erişim belirteci edinir ve API 'ye yapılan istekteki belirteci içerir.

Ancak, API 'nizi belirteç olmadan veya geçersiz bir belirteçle çağırırsa ne olacak? Örneğin, `Authorization` üst bilgi olmadan API 'yi çağırmayı deneyin, çağrı devam edecektir. Bunun nedeni API Management, bu noktada erişim belirtecini doğrulamaktır. Yalnızca `Authorization` üstbilgiyi arka uç API 'sine geçirir.

Her gelen isteğin erişim belirteçlerini doğrulayarak, API Management istekleri önceden yetkilendirmek için [JWT Ilkesini doğrula](api-management-access-restriction-policies.md#ValidateJWT) ' yı kullanabilirsiniz. Bir istekte geçerli bir belirteç yoksa API Management engeller. Örneğin, aşağıdaki ilkeyi `<inbound>` konusunun ilke bölümüne `Echo API`ekleyin. Bir erişim belirtecindeki hedef kitle talebini denetler ve belirteç geçerli değilse bir hata mesajı döndürür. İlkeleri yapılandırma hakkında daha fazla bilgi için bkz. [Ilkeleri ayarlama veya düzenleme](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="build-an-application-to-call-the-api"></a>API 'YI çağırmak için bir uygulama oluşturma

Bu kılavuzda, OAuth 2,0 tarafından `Echo API` korunan örneği çağırmak için örnek istemci uygulaması olarak API Management geliştirici konsolunu kullandınız. Uygulama oluşturma ve OAuth 2,0 uygulama hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory Code Samples](../active-directory/develop/sample-v1-code.md).

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Active Directory ve OAuth 2.0](../active-directory/develop/authentication-scenarios.md)hakkında daha fazla bilgi edinin.
* API Management hakkında daha fazla [videoya](https://azure.microsoft.com/documentation/videos/index/?services=api-management) göz atın.
* Arka uç hizmetinizi güvenli hale getirmeye yönelik diğer yollar için bkz. [Karşılıklı sertifika kimlik doğrulaması](api-management-howto-mutual-certificates.md).

* [API Management hizmet örneği oluşturun](get-started-create-service-instance.md).

* [Ilk API 'Nizi yönetin](import-and-publish.md).
