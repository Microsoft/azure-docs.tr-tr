---
title: İstemci uygulama yapılandırması (MSAL) | Mavisi
titleSuffix: Microsoft identity platform
description: Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanarak genel istemci ve gizli istemci uygulamaları için yapılandırma seçenekleri hakkında bilgi edinin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/20/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 00768f363d08bc476350e57a8eac69eafd9c3589
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99580947"
---
# <a name="application-configuration-options"></a>Uygulama yapılandırma seçenekleri

Belirteçlerin kimliğini doğrulamak ve almak için kodunuzda yeni bir ortak veya gizli istemci uygulaması başlatın. İstemci uygulamasını Microsoft kimlik doğrulama kitaplığı 'nda (MSAL) başlatırken birçok yapılandırma seçeneği belirleyebilirsiniz. Bu seçenekler iki gruba ayrılır:

- Kayıt seçenekleri, şunlar dahildir:
  - Yetkili (kimlik sağlayıcı [örneğinden](#cloud-instance) ve uygulama için oturum açma [izleyicisiyle](#authority) ve muhtemelen kiracı kimliğinde) oluşur [](#application-audience)
  - [İstemci Kimliği](#client-id)
  - [Yönlendirme URI'si](#redirect-uri)
  - [İstemci gizli](#client-secret) dizisi (gizli istemci uygulamaları için)
- Günlük düzeyi, kişisel verilerin denetimi ve kitaplığı kullanarak bileşen adı dahil olmak üzere [günlüğe kaydetme seçenekleri](#logging)

## <a name="authority"></a>Yetkili

Yetkili, MSAL tarafından belirteçleri isteyebileceğini bir dizini gösteren bir URL 'dir.

Ortak yetkililer şunlardır:

| Ortak yetkili URL 'Leri | Kullanılması gereken durumlar |
|--|--|
| `https://login.microsoftonline.com/<tenant>/` | Yalnızca belirli bir kuruluşun kullanıcıları oturum açın. `<tenant>`URL 'de, Azure Active Directory (Azure AD) kiracının (bır GUID) veya kiracı etki alanının KIRACı kimliğidir. |
| `https://login.microsoftonline.com/common/` | Kullanıcıları iş ve okul hesaplarıyla veya kişisel Microsoft hesaplarıyla oturum açın. |
| `https://login.microsoftonline.com/organizations/` | İş ve okul hesaplarıyla kullanıcıları oturum açın. |
| `https://login.microsoftonline.com/consumers/` | Kullanıcılara yalnızca kişisel Microsoft hesaplarıyla (MSA) oturum açın. |

Kodunuzda belirttiğiniz yetkilinin, Azure portal **uygulama kayıtları** uygulama Için belirttiğiniz **Desteklenen hesap türleriyle** tutarlı olması gerekir.

Yetkili şu olabilir:

- Bir Azure AD bulut yetkilisi.
- Azure AD B2C yetkilisi. [B2C özelliklerine](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)bakın.
- Active Directory Federasyon Hizmetleri (AD FS) (AD FS) yetkilisi. Bkz. [AD FS desteği](https://aka.ms/msal-net-adfs-support).

Azure AD bulut yetkilileri iki bölümden oluşur:

- Kimlik sağlayıcısı *örneği*
- Uygulama için oturum açma *hedef kitlesi*

Örnek ve hedef kitle, yetkili URL 'SI olarak birleştirilebilir ve belirtilebilir. Bu diyagram, yetkili URL 'sinin nasıl oluşturulduğunu gösterir:

![Yetkili URL 'SI nasıl oluşturulur?](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Bulut örneği

*Örnek* , uygulamanızın Azure genel bulutundaki veya ulusal bulutlardan Kullanıcı imzalanmasını belirtmek için kullanılır. Kodunuzda MSAL kullanarak, bir sabit listesi kullanarak veya URL 'YI (bildiğiniz takdirde) [Ulusal bulut örneğine](authentication-national-cloud.md#azure-ad-authentication-endpoints) geçirerek Azure bulut örneğini ayarlayabilirsiniz `Instance` .

Her ikisi de belirtilirse, MSAL.NET açık bir özel durum oluşturur `Instance` `AzureCloudInstance` .

Bir örnek belirtmezseniz, uygulamanız Azure genel bulut örneğini (URL 'nin örneği) hedefleyecek `https://login.onmicrosoftonline.com` .

## <a name="application-audience"></a>Uygulama hedef kitlesi

Oturum açma hedef kitlesi, uygulamanız için iş ihtiyaçlarına bağlıdır:

- İş kolu (LOB) geliştiricisiyseniz, muhtemelen yalnızca kuruluşunuzda kullanılacak tek kiracılı bir uygulama oluşturacaksınız. Bu durumda, organizasyonu kiracı KIMLIĞI (Azure AD örneğinizin KIMLIĞI) veya Azure AD örneğiyle ilişkili bir etki alanı adı ile belirtin.
- ISV iseniz, kullanıcıların herhangi bir kuruluşta veya bazı kuruluşlarda (çok kiracılı uygulama) iş ve okul hesaplarıyla oturum açmak isteyebilirsiniz. Ancak kullanıcıların kişisel Microsoft hesaplarıyla oturum açmasını da isteyebilirsiniz.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Kodunuzda/yapılandırmanızda hedef kitleyi belirtme

Kodunuzda MSAL kullanarak, aşağıdaki değerlerden birini kullanarak hedef kitleyi belirtirsiniz:

- Azure AD yetkilisi hedef kitlesi numaralandırması
- Kiracı KIMLIĞI:
  - Tek kiracılı uygulamalar için GUID (Azure AD örneğinizin KIMLIĞI)
  - Azure AD örneğiniz ile ilişkili bir etki alanı adı (tek kiracılı uygulamalar için de)
- Azure AD yetkilisi hedef kitlesi numaralandırması yerine bir kiracı KIMLIĞI olarak bu yer tutuculardan biri:
  - `organizations` çok kiracılı bir uygulama için
  - `consumers` kullanıcıları yalnızca kendi kişisel hesaplarıyla oturum açmak için
  - `common` kullanıcılara iş ve okul hesaplarıyla veya kendi kişisel Microsoft hesaplarıyla oturum açmak için

Hem Azure AD yetkilisi kitleyi hem de kiracı KIMLIĞINI belirtirseniz MSAL anlamlı bir özel durum oluşturur.

Hedef kitle belirtmezseniz, uygulamanız Azure AD ve kişisel Microsoft hesaplarını bir hedef kitle olarak hedefleyecek. (Yani, belirtildiği gibi davranır `common` .)

### <a name="effective-audience"></a>Etkin hedef kitle

Uygulamanız için geçerli hedef kitle, uygulamanızda belirlediğiniz hedef kitlesinin ve uygulama kaydında belirtilen hedef kitlesinin en az (bir kesişmesi varsa) olacaktır. Aslında [uygulama kayıtları](https://aka.ms/appregistrations) deneyimi, uygulama için hedef kitleyi (desteklenen hesap türleri) belirtmenize olanak tanır. Daha fazla bilgi için bkz. [hızlı başlangıç: Microsoft Identity platformu ile uygulama kaydetme](quickstart-register-app.md).

Şu anda, yalnızca kişisel Microsoft hesaplarıyla kullanıcıların oturum açması için bir uygulama almanın tek yolu bu ayarların her ikisini de yapılandırmaktır:

- Uygulama kaydı kitlesini olarak ayarlayın `Work and school accounts and personal accounts` .
- Kodunuzda/yapılandırmanızda bulunan hedef kitleyi `AadAuthorityAudience.PersonalMicrosoftAccount` (veya `TenantID` = "tüketiciler") olarak ayarlayın.

## <a name="client-id"></a>İstemci Kimliği

İstemci KIMLIĞI, uygulama kaydedildiğinde Azure AD tarafından uygulamanıza atanan benzersiz uygulama (istemci) KIMLIĞIDIR.

## <a name="redirect-uri"></a>Yeniden Yönlendirme URI'si

Yeniden yönlendirme URI 'si, kimlik sağlayıcısı 'nın güvenlik belirteçlerini uygulamasına geri göndereceği URI 'dir.

### <a name="redirect-uri-for-public-client-apps"></a>Ortak istemci uygulamaları için yeniden yönlendirme URI 'SI

MSAL kullanan ortak bir istemci uygulama geliştiricisiyseniz:

- `.WithDefaultRedirectUri()`Masaüstü veya UWP uygulamalarında kullanmak istersiniz (msal.NET 4.1 +). Bu yöntem, ortak istemci uygulamasının yeniden yönlendirme URI özelliğini genel istemci uygulamaları için önerilen varsayılan yeniden yönlendirme URI 'si olarak ayarlar.

  | Platform | Yeniden Yönlendirme URI'si |
  |--|--|
  | Masaüstü uygulaması (.NET ILT) | `https://login.microsoftonline.com/common/oauth2/nativeclient` |
  | UWP | değeri `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` . Bu, kaydolmanız gereken WebAuthenticationBroker. GetCurrentApplicationCallbackUri () sonucuna değer ayarlayarak, SSO 'yu tarayıcıda sağlar |
  | .NET Core | `https://localhost`. Bu, .NET Core 'un katıştırılmış Web görünümü için bir kullanıcı arabirimine sahip olmadığından kullanıcının etkileşimli kimlik doğrulaması için sistem tarayıcısını kullanmasına olanak sağlar. |

- Aracı yeniden yönlendirme URI 'sini desteklemeyen bir Xamarin Android ve iOS uygulaması oluşturuyorsanız yeniden yönlendirme URI 'SI eklemeniz gerekmez. `msal{ClientId}://auth`Xamarin Android ve iOS için otomatik olarak ayarlanır.

- [Uygulama kayıtları](https://aka.ms/appregistrations)YENIDEN yönlendirme URI 'sini yapılandırın:

   ![Uygulama kayıtları yeniden yönlendirme URI 'SI](media/msal-client-application-configuration/redirect-uri.png)

Özelliğini kullanarak yeniden yönlendirme URI 'sini geçersiz kılabilirsiniz `RedirectUri` (örneğin, aracılar kullanıyorsanız). Bu senaryo için yeniden yönlendirme URI 'Leri örnekleri aşağıda verilmiştir:

- `RedirectUriOnAndroid` = "msauth-5a434691-CCB2-4fd1-b97b-b64bcfbc03fc://com.Microsoft.identity.Client.Sample";
- `RedirectUriOnIos` = $ "msauth. {Demeti. ID}://kimlik doğrulaması ";

Ek iOS ayrıntıları için bkz. [adal.net 'den msal.net 'ye Microsoft Authenticator kullanan iOS uygulamalarını geçirme](msal-net-migration-ios-broker.md) ve [iOS üzerinde aracı](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)kullanma.
Ek Android ayrıntıları için bkz. [Android 'de aracılı kimlik doğrulaması](msal-android-single-sign-on.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>Gizli istemci uygulamaları için yeniden yönlendirme URI 'SI

Web Apps için, yeniden yönlendirme URI 'si (veya yanıt URL 'SI), Azure AD 'nin belirteci uygulamaya geri göndermek için kullanacağı URI 'dir. Gizli uygulama bunlardan biri ise, bu URI Web uygulaması/Web API 'sinin URL 'si olabilir. Yeniden yönlendirme URI 'sinin uygulama kaydında kayıtlı olması gerekir. Başlangıçta yerel olarak test ettiğiniz bir uygulamayı dağıtırken bu kayıt özellikle önemlidir. Ardından, uygulama kayıt portalı 'nda dağıtılan uygulamanın yanıt URL 'sini eklemeniz gerekir.

Daemon uygulamaları için bir yeniden yönlendirme URI 'SI belirtmeniz gerekmez.

## <a name="client-secret"></a>Gizli anahtar

Bu seçenek, gizli istemci uygulaması için istemci parolasını belirtir. Bu gizli dizi (uygulama parolası) uygulama kayıt portalı tarafından sağlanır veya PowerShell AzureAD, PowerShell Azurerd veya Azure CLı ile uygulama kaydı sırasında Azure AD 'ye sağlanır.

## <a name="logging"></a>Günlüğe Kaydetme
Hata ayıklama ve kimlik doğrulama sırasında hata giderme senaryolarında, Microsoft kimlik doğrulama kitaplığı yerleşik günlük desteği sağlar. Günlüğe kaydetme her bir kitaplık aşağıdaki makalelerde ele alınmıştır:

:::row:::
    :::column:::
        - [MSAL.NET’te oturum açma](msal-logging-dotnet.md)
        - [Android için MSAL’de oturum açma](msal-logging-android.md)
        - [MSAL.js’de oturum açma](msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [iOS/macOS için MSAL’de oturum açma](msal-logging-ios.md)
        - [Java için MSAL’de oturum açma](msal-logging-java.md)
        - [Python için MSAL’de oturum açma](msal-logging-python.md)
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Sonraki adımlar

[Msal.NET kullanarak istemci uygulamalarının örneğini](msal-net-initializing-client-applications.md) oluşturma ve [MSAL.jskullanarak istemci uygulamaları örneğini ](msal-js-initializing-client-applications.md)oluşturma hakkında bilgi edinin.
