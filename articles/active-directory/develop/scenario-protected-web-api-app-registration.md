---
title: Korumalı Web API 'SI-uygulama kaydı
titleSuffix: Microsoft identity platform
description: Korumalı bir Web API 'SI oluşturmayı ve uygulamayı kaydettirmek için gereken bilgileri oluşturmayı öğrenin.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c905dfd86fd80b9e55aa7bd5a9b9b03f277570c
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802551"
---
# <a name="protected-web-api-app-registration"></a>Korumalı Web API 'SI: uygulama kaydı

Bu makalede, korumalı bir Web API 'SI için uygulama kaydının ayrıntıları açıklanmaktadır.

Bkz. hızlı başlangıç: uygulamayı kaydetmeye yönelik ortak adımlar için [Microsoft Identity platformu ile uygulama kaydetme](quickstart-register-app.md) .

## <a name="accepted-token-version"></a>Kabul edilen belirteç sürümü

Microsoft Identity platform uç noktası iki tür belirteç verebilir: v 1.0 belirteçleri ve v 2.0 belirteçleri. Bu belirteçler hakkında daha fazla bilgi için bkz. [erişim belirteçleri](access-tokens.md). Kabul edilen belirteç sürümü, uygulamanızı oluştururken seçtiğiniz **Desteklenen hesap türlerine** bağlıdır:

- **Desteklenen hesap türlerinin** değeri **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında (örn. Skype, Xbox, Outlook.com) hesaplardır**, kabul edilen belirteç sürümü v 2.0 olur.
- Aksi takdirde, kabul edilen belirteç sürümü v 1.0 olur.

Uygulamayı oluşturduktan sonra, aşağıdaki adımları izleyerek kabul edilen belirteç sürümünü belirleyebilir veya değiştirebilirsiniz:

1. Azure portal, uygulamanızı seçin ve ardından uygulamanızın **bildirimini** seçin.
2. Bildirimde, **"Accesstokenacceptedversion"** ifadesini aratın. Değerinin **2**olduğunu unutmayın. Bu özellik, Web API 'sinin v 2.0 belirteçlerini kabul ettiğini Azure Active Directory (Azure AD) belirtir. Değer **null**ise, kabul edilen belirteç sürümü v 1.0 olur.
3. Belirteç sürümünü değiştirdiyseniz **Kaydet**' i seçin.

> [!NOTE]
> Web API 'SI, kabul ettiği belirteç sürümünü (v 1.0 veya v 2.0) belirtir. İstemcileri, Web API 'niz için Microsoft Identity platform (v 2.0) uç noktasından bir belirteç talep ettiğinde, Web API 'SI tarafından hangi sürümün kabul edildiğini belirten bir belirteç alırlar.

## <a name="no-redirect-uri"></a>Yeniden yönlendirme URI 'SI yok

Kullanıcı etkileşimli olarak oturum açmamış olduğundan Web API 'Lerinin yeniden yönlendirme URI 'SI kaydetmesi gerekmez.

## <a name="expose-an-api"></a>API 'YI kullanıma sunma

Web API 'Lerine özgü başka bir ayar, sunulan API ve sunulan kapsamdır.

### <a name="resource-uri-and-scopes"></a>Kaynak URI 'SI ve kapsamları

Kapsamlar genellikle `resourceURI/scopeName`biçiminde olur. Microsoft Graph için kapsamlar `User.Read`gibi kısayollara sahiptir. Bu dize, `https://graph.microsoft.com/user.read`için bir kısayoldur.

Uygulama kaydı sırasında şu parametreleri tanımlamanız gerekir:

- Kaynak URI 'SI. Varsayılan olarak, uygulama kayıt portalı `api://{clientId}`kullanmanızı önerir. Bu kaynak URI 'SI benzersizdir, ancak insanlar okunabilir değildir. Bunu değiştirebilirsiniz, ancak yeni değerin benzersiz olduğundan emin olabilirsiniz.
- Bir veya daha fazla *kapsam*. (İstemci uygulamalarına, Web API 'niz için *temsilci izinleri* olarak gösterilir.)
- Bir veya daha fazla *uygulama rolü*. (İstemci uygulamalarına, Web API 'niz için *Uygulama izinleri* olarak gösterilir.)

Kapsamlar Ayrıca, uygulamanızın son kullanıcılarına sunulan onay ekranında da görüntülenir. Bu nedenle, kapsamı tanımlayan karşılık gelen dizeleri sağlamanız gerekir:

- Son Kullanıcı tarafından görüldüğü gibi.
- Kiracı Yöneticisi tarafından görüldüğü gibi, yönetici onayı de verebilir.

### <a name="exposing-delegated-permissions-scopes"></a>Temsilci izinleri gösterme (kapsamlar)

1. Uygulama kaydında **BIR API 'Yi kullanıma** sunma bölümünü seçin.
1. **Kapsam ekle**’yi seçin.
1. İstenirse, **Kaydet ve devam et**' i seçerek ÖNERILEN uygulama kimliği URI 'sini (`api://{clientId}`) kabul edin.
1. Şu parametreleri girin:
      - **Kapsam adı**için **access_as_user**kullanın.
      - **Kimler izin verebilir** **yönetici ve kullanıcıların** seçili olduğundan emin olun.
      - **Yönetici onayı görünen adı**' nda, **Kullanıcı olarak erişim TodoListService**girin.
      - **Yönetici onayı açıklaması**' nda, **Kullanıcı olarak TodoListService Web API 'sine erişir**yazın.
      - **Kullanıcı izni görünen adı**alanına **Kullanıcı olarak erişim TodoListService**girin.
      - **Kullanıcı onay açıklaması**' nda, **Kullanıcı olarak TodoListService Web API 'sine erişir**yazın.
      - **Durumu** **etkin**olarak ayarlayın.
      - **Kapsam Ekle**' yi seçin.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Web API 'niz bir Daemon uygulaması tarafından çağrılırsa

Bu bölümde, korumalı Web API 'nizi, Daemon uygulamaları tarafından güvenli bir şekilde çağrılabilecek şekilde nasıl kaydedeceğinizi öğreneceksiniz.

- *Uygulama izinlerini*kullanıma sunacaksınız. Yalnızca uygulama izinlerini bildirebilirsiniz çünkü Daemon uygulamaları kullanıcılarla etkileşime girmediğinden, temsilci izinleri anlamlı olmaz.
- Kiracı yöneticileri, Web API 'niz için belirteçleri yalnızca Web API 'sinin uygulama izinlerinden birine erişmek üzere kayıtlı olan uygulamalara vermek için Azure Active Directory (Azure AD) gerektirebilir.

#### <a name="exposing-application-permissions-app-roles"></a>Uygulama izinlerini gösterme (uygulama rolleri)

Uygulama izinlerini göstermek için bildirimi düzenlemeniz gerekir.

1. Uygulamanız için uygulama kaydında, **bildirim**' ı seçin.
1. `appRoles` ayarını bularak ve bir veya daha fazla uygulama rolü ekleyerek bildirimi düzenleyin. Rol tanımı aşağıdaki örnek JSON bloğunda verilmiştir. `allowedMemberTypes` yalnızca `"Application"` olarak ayarlı bırakın. `id` benzersiz bir GUID olduğundan ve `displayName` ve `value` boşluk içermediğinden emin olun.
1. Bildirimi kaydedin.

Aşağıdaki örnek `appRoles`içeriğini gösterir. (`id` herhangi bir benzersiz GUID olabilir.)

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Azure AD 'nin, Web API 'niz için belirteçleri yalnızca izin verilen istemcilere verdiği doğrulanıyor

Web API 'SI, uygulama rolünü denetler. (Uygulama izinlerini kullanıma sunma geliştirici yöntemidir.) Ancak, Azure AD 'yi, Web API 'niz için yalnızca kiracı yöneticisinin API 'niz tarafından onaylanan uygulamalara bir belirteç vermek üzere de yapılandırabilirsiniz. Bu artırılmış güvenliği eklemek için:

1. Uygulama kaydınız için uygulamaya **genel bakış** sayfasında, **yerel dizinde yönetilen uygulama**altında uygulamanızın adıyla bağlantıyı seçin. Bu alanın başlığı kesilmiş olabilir. Örneğin, **Içindeki yönetilen uygulama** ' da bkz....

   > [!NOTE]
   >
   > Bu bağlantıyı seçtiğinizde, oluşturduğunuz kiracıda uygulamanız için hizmet sorumlusu ile ilişkili **Kurumsal uygulamaya genel bakış** sayfasına gidebilirsiniz. Tarayıcınızın geri düğmesini kullanarak uygulama kayıt sayfasına geri gidebilirsiniz.

1. Kurumsal uygulama sayfalarının **Yönet** bölümünde **Özellikler** sayfasını seçin.
1. Azure AD 'nin yalnızca belirli istemcilerden Web API 'nize erişmesine izin vermek istiyorsanız, **gereken Kullanıcı atamasını** **Evet**olarak ayarlayın.

   > [!IMPORTANT]
   >
   > **Gerekli Kullanıcı atamasını** **AYARLARSANıZ, Azure AD, Web**API 'si için bir erişim belirteci istediklerinde, istemcilerin uygulama rolü atamalarını denetlecektir. İstemci herhangi bir uygulama rolüne atanmamışsa, Azure AD `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`hata döndürür.
   >
   > **Kullanıcı atamasını gerekli** tutarsanız, **Hayır**olarak AYARLARSANıZ, *istemci Web API 'niz için bir ERIŞIM belirteci istediğinde, Azure AD uygulama rolü atamalarını denetlemez*. Tüm Daemon istemcileri (yani, istemci kimlik bilgileri akışını kullanan tüm istemciler), hedef kitini belirterek API için bir erişim belirteci elde edebilecektir. Herhangi bir uygulama, için izin istemek zorunda kalmadan API 'ye erişebilecektir. Ancak Web API 'niz her zaman, önceki bölümde açıklandığı gibi, uygulamanın doğru rolün (kiracı yöneticisi tarafından yetkilendirilmiş) olduğunu doğrulayın. API, erişim belirtecinin bir rol talebine sahip olduğunu ve bu talebin değerinin doğru olduğunu doğrulayarak bu doğrulamayı gerçekleştirir. (Bu durumda, değer `access_as_application`.)

1. **Kaydet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulamanın kod yapılandırması](scenario-protected-web-api-app-configuration.md)
