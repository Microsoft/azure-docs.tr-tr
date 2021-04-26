---
title: Azure AD kullanarak uçtan uca içerik koruma
description: Bu makalede içeriğinizi Azure Media Services ve Azure Active Directory nasıl koruyabileceğiniz öğretilir
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 9c81a9b48ff9fa305385c45266d88deb4047f70f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599494"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>Öğretici: Azure AD kullanarak uçtan uca içerik koruma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu öğretici ve sunulan oynatıcı örneği sayesinde, tüm AMS destekli DRM/AES-128, akış protokolleri, codec ve kapsayıcı biçimleriyle medya içeriğini akışa almak için Azure Media Services (AMS) ve Azure Active Directory (AAD) üzerinde uçtan uca bir medya içerik koruma alt sistemi ayarlayabilirsiniz. Örnek, OAuth 2 tarafından korunan REST API, kod değişimi (PKCE) düzeltme anahtarıyla yetkilendirme kodu akışı aracılığıyla güvenli erişim için yeterince geneldir. (Azure Media Services lisans teslim hizmeti bunlardan yalnızca biridir.) Ayrıca, OAuth 2 yetkilendirme kodu akışıyla güvenliği sağlanmış Microsoft Graph API veya özel geliştirilmiş REST API için de kullanılır. Bu, [örnek koda](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)eşlik eden bir belgedir.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
>
> * Kimlik doğrulama gereksinimlerini dikkate alın
> * Uygulamanın nasıl çalıştığını anlama
> * Arka uç kaynak uygulamasını kaydetme
> * İstemci uygulamasını kaydetme
> * Media Services hesabı içerik anahtarı ilkesini ve akış ilkelerini ayarlama
> * Oynatıcı uygulamasını ayarlama

Azure Media Services aboneliğiniz yoksa, bir Azure [ücretsiz deneme hesabı](https://azure.microsoft.com/free/) oluşturun ve ardından bir Media Services hesabı oluşturun.

### <a name="duration"></a>Süre
Önkoşul teknolojisini denemeye hazırladıktan sonra öğreticinin tamamlanması iki saat sürer.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki en son teknoloji sürümleri ve kavramları kullanılır. Bu öğreticiye başlamadan önce bunları tanımanız önerilir.

### <a name="prerequisite-knowledge"></a>Önkoşul bilgileri

Bu, isteğe bağlıdır, ancak Bu öğreticiye başlamadan önce aşağıdaki kavramları tanımanız önerilir:

* Dijital Rights Management (DRM)
* [Azure Media Services (AMS) v3](./media-services-overview.md)
* AMS API v3, Azure portal veya [Azure Media Services Explorer (AMSE) aracını](https://github.com/Azure/Azure-Media-Services-Explorer) kullanan AMS [içerik anahtarı ilkeleri](drm-content-key-policy-concept.md)
* [Microsoft Identity platformunda](../../active-directory/develop/index.yml) Azure AD v2 uç noktaları
* [OAuth 2,0 ve OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md) gibi modern bulut kimlik doğrulaması
  * [OAuth 2,0 'de yetkilendirme kodu akışı](../../active-directory/develop/v2-oauth2-auth-code-flow.md) ve PKI CE gerekli
  * [Temsilci izni vs uygulama izni](../../active-directory/develop/developer-glossary.md#permissions)
* [JWT belirteci](../../active-directory/develop/access-tokens.md), talepleri ve imzalama anahtarı geçişi (örneğe dahildir.)

### <a name="prerequisite-code-and-installations"></a>Önkoşul kodu ve yüklemeleri

* Örnek kod. [Örnek kodu](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)indirin.
* Visual Studio Code yüklemesi. Visual Studio Code buradan indirin [https://code.visualstudio.com/download](https://code.visualstudio.com/download) .
* Node.js yüklemesi. Node.js buradan indirin [https://nodejs.org](https://nodejs.org) . NPM, install ile birlikte gelir.
* Bir [Azure aboneliği](https://azure.microsoft.com/free/).
* Azure Media Services (AMS) hesabı.
* @azure/msal-browser farklı istemci platformları için [Microsoft kimlik doğrulama kitaplığı (msal)](../../active-directory/develop/msal-overview.md) SDK ailesindeki üyelerden biri olan v 2.0
* [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)en son sürümü (örneğe dahildir.)
* FairPlay DRM 'yi ve istemci tarafı JavaScript aracılığıyla erişilebilen CORS ile barındırılan uygulama sertifikasını eklemek istiyorsanız Apple 'dan kimlik bilgileri.

> [!IMPORTANT]
> Bu öğretici, içerik anahtarı ilke kısıtlaması oluşturmak için .NET kullanır.  .NET geliştiricisi değilseniz, ancak Azure Media Services bağlanmak için Node.js denemek istiyorsanız, [Media Services v3 API-Node.jsbağlantısını ](configure-connect-nodejs-howto.md)okuyun. Ayrıca, anahtar geçişi otomatik olarak işlemek için kullanılabilen bir Node.js modülü de vardır, bkz. Node.js [Passport-ad modülü](https://github.com/AzureAD/passport-azure-ad).

## <a name="consider-the-authentication-and-authorization-requirements"></a>Kimlik doğrulama ve yetkilendirme gereksinimlerini dikkate alın

Alt sistemi tasarlarken bazı sorunlar sunulmuştur. Birden çok hareketli parçaya sahiptir, her altı haftada bir gerçekleşen istemci uygulama kısıtlamaları ve Azure AD anahtar geçişi vardır.

Bu öğreticide kullanılan Single-Page App (SPA), kimlik doğrulama gereksinimlerine ve izleyen kısıtlamalara karşı sorunları ele alır. Şunları kullanır:

* Azure AD geliştirici platformu (v1 uç noktaları) olarak Azure AD v2 uç noktaları Microsoft Identity platform (v2 uç noktaları) olarak değiştiriliyor.
* OAuth 2 örtük verme akışı kullanım dışı bırakıldığından yetkilendirme kodu akışı.
* Aşağıdaki kısıtlamalara tabi olan bir uygulama:
    * Ortak istemci, istemci parolasını gizlemiyor.  Yalnızca yetkilendirme kodu akışı, istemci gizliliğini gizlemeyi gerektirir, bu nedenle PKCE ile yetkilendirme kodu akışı kullanılır.
    * Tarayıcı güvenlik korumalı alana (CORS/ön kontrol kısıtlaması) tabi olan tarayıcı tabanlı bir uygulama.
    * JavaScript güvenlik kısıtlamalarına tabi olan modern JavaScript kullanan tarayıcı tabanlı bir uygulama (özel üstbilgi erişilebilirliği, bağıntı kimliği).

## <a name="understand-the-subsystem-design"></a>Alt sistem tasarımını anlama

Alt sistemin tasarımı Aşağıdaki diyagramda gösterilmiştir.  Üç katmana sahiptir:

* İçerik anahtarı ilkesini yapılandırmak için arka ofis katmanı (siyah) ve akış için içerik yayımlama
* Kimlik doğrulama, yetkilendirme, DRM lisansı ve şifrelenmiş içerik sağlayan, Player/müşteriye yönelik uç noktalar olan genel uç noktalar (mavi)
* Tüm bileşenleri ve tüm bileşenlerini tümleştiren oynatıcı uygulaması (açık mavi)
    * Azure AD aracılığıyla Kullanıcı kimlik doğrulamasını işler.
    * Azure AD 'den access_token alımı işler.
    * AMS/CDN 'den bildirim ve şifrelenmiş içerik alır.
    * Azure Media Services 'ten DRM lisansı elde edin.
    * içerik şifre çözme, kodunu çözme ve görüntüleme işlemleri gerçekleştirir.

![JWT belirteçlerini ayrıştırma ekranı](media/aad-ams-content-protection/subsystem.svg)

Alt sistem hakkında daha fazla bilgi için [Access Control ile bir çoklu DRM içerik koruma sisteminin tasarımını](./architecture-design-multi-drm-system.md) okuyun.

## <a name="understand-the-single-page-app"></a>Tek sayfalı uygulamayı anlayın

Oynatıcı uygulaması, kullanarak Visual Studio Code geliştirilen tek sayfalı bir uygulamadır (SPA):

* ES 6 JavaScript ile Node.js
* @azure/msal-browser 2,0 Beta
* Azure Media Player SDK
* Azure AD v2 uç noktalarında OAuth 2 akışı (Microsoft Identity Platform)

SPA oynatıcı uygulaması aşağıdaki eylemleri tamamlar:

* Kiracıya yerel kullanıcılar için Kullanıcı kimlik doğrulaması ve diğer AAD kiracılarından veya MSA hesaplarından Konuk kullanıcılar. Kullanıcılar tarayıcı açılır veya yeniden yönlendirme aracılığıyla oturum açmayı seçebilir (Safari gibi açılan pencerelere izin vermeyerek tarayıcılar için).
* `access_token`PKI Ile yetkilendirme kodu akışını alma.
* Yenileme `access_token` (AAD tarafından verilen belirteçler 1 saat için geçerlidir), için `refresh_token` de elde edilir.
* `access_token`Test/İnceleme IÇIN JWT belirteçleri (hem hem de `id_token` ) ayrıştırılıyor.
* Üç DRMs veya AES-128 içerik anahtarı için DRM lisanslarının alımı.
* DRM ile akış protokolü vs kapsayıcı biçiminin çeşitli birleşimleri kapsamında içerik akışı. Doğru biçim dizesi her birleşim için oluşturulur.
* Şifre çözme, kodunu çözme ve görüntüleme.
* Sorun giderme amacıyla API çağrılarını Microsoft Graph. <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

Oturum açma, belirteç alma, belirteç yenileme ve belirteç görüntüleme ekranı:

 ![Oturum açma, belirteç alma, belirteç yenileme ve belirteç görüntüleme için ekran](media/aad-ams-content-protection/token-acquisition.png)

JWT belirteçlerini ayrıştırma ekranı (access_token veya id_token):

![J W T belirteçlerini ayrıştırmayı gösteren ekran görüntüsü.](media/aad-ams-content-protection/parsing-jwt-tokens.png)

Farklı DRM/AES vs akış protokolleriyle korumalı içeriği test etme ekranı, kapsayıcı biçimi:

![Farklı D R M veya A ve akış protokolleriyle karşı korumalı içeriği test eden, kapsayıcı biçimine karşı test eden ekran görüntüsü](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Bir Azure AD kiracısı seçin

> [!NOTE]
> Buradan, Azure portal oturum açmış olduğunuz ve en az bir Azure AD kiracısının bulunduğu varsayılır.

Uçtan uca örneğimiz için kullanılacak bir Azure AD kiracısı seçin. İki seçeneğiniz vardır:

* Mevcut bir Azure AD kiracısı. Herhangi bir Azure aboneliğinin bir Azure AD kiracısı olması gerekir, ancak Azure AD kiracısı birden çok Azure aboneliği tarafından kullanılabilir.
* Herhangi bir Azure aboneliği *tarafından kullanılmayan yeni* BIR Azure AD kiracısı. Yeni kiracı seçeneğini belirlerseniz, Media Service hesabı ve örnek oynatıcı uygulamasının ayrı bir Azure AD kiracısı kullanan bir Azure aboneliğinde olması gerekir. Bu, biraz esneklik sağlar. Örneğin, kendi Azure AD kiracınızı, müşterinin Azure aboneliğindeki müşterinin medya hizmeti hesabını da kullanabilirsiniz.

## <a name="register-the-backend-resource-app"></a>Arka uç kaynak uygulamasını kaydetme

1. Seçtiğiniz veya oluşturduğunuz Azure AD kiracısına gidin.
1. Menüden **Azure Active Directory** seçin.
1. Menüden **uygulama kayıtları** seçin.
1. **+ Yeni kayıt**' ye tıklayın.
1. Uygulamayı *LicenseDeliveryResource2* olarak adlandırın (burada 2 AAD v2 uç noktasını gösterir).
1. **Yalnızca bu kuruluş dizinindeki hesapları seçin ([yalnızca *kiracı adınız*]-tek kiracı)**. Birden çok kiracıya erişimi etkinleştirmek istiyorsanız, diğer çok kiracılı seçeneklerden birini seçin.
1. **Yeniden yönlendirme URI 'si** isteğe bağlıdır ve daha sonra değiştirilebilir.
1. **Kaydet**’e tıklayın. Uygulama kayıtları görünümü görüntülenir.
1. Menüden **bildirim** ' ı seçin. Bildirim görünümü görüntülenir.
1. Değerini `accessTokenAcceptedVersion` *2* (tırnak yok) olarak değiştirin.
1. Değerini `groupMembershipClaims` *"SecurityGroup"* (tırnak işareti) olarak değiştirin.
1. **Kaydet**’e tıklayın.
1. Menüden **BIR API 'Yi kullanıma** sunma ' yı seçin. Kapsam ekleme görünümü görüntülenir. (Azure bir uygulama KIMLIĞI URI 'SI sağlar, ancak bunu değiştirmek istiyorsanız, uygulama KIMLIĞI URI 'SI alanında düzenleyebilirsiniz.)
1. **Kaydet ve devam et**' e tıklayın. Görünüm değişecektir. Aşağıdaki tabloda bulunan ayar sütunundaki ayarların her biri için değer sütununa değeri girip **Kapsam Ekle**' ye tıklayın.

| Ayar | Değer | Açıklama |
| ------- | ----- | ----------- |
| Kapsam adı | *DRM. Lisans. Delivery* | Bu API 'ye erişim istendiğinde kapsam nasıl görünür ve bir istemci uygulamasına kapsam verildiğinde erişim belirteçlerinde. Bu uygulama genelinde benzersiz olmalıdır. En iyi yöntem, adı oluşturmak için bir model olarak "Resource. Operation. Constraint" kullanmaktır. |
| Kimler izin verebilir? | *Yöneticiler ve kullanıcılar* | Kullanıcıların, Kullanıcı izninin etkinleştirildiği dizinlerde bu kapsama izin verip veremeyeceğini belirler. |
| Yönetici onayı görünen adı | *DRM lisansı verme* | Yöneticiler bu kapsama kabul edildiğinde izin ekranında kapsam çağrılacaktır. |
| Yönetici onay açıklaması * * | *DRM lisans teslimi arka uç kaynak kapsamı* | Kiracı yöneticileri onay ekranındaki bir kapsamı genişlettikten sonra görüntülenen kapsamın ayrıntılı açıklaması. |
| Kullanıcı onayı görünen adı | *DRM. Lisans. Delivery* | Kullanıcılar bu kapsama kabul edildiğinde izin ekranında kapsam çağrılacaktır. |
| Kullanıcı onayı açıklaması | *DRM lisans teslimi arka uç kaynak kapsamı* | Bu, kullanıcılar onay ekranındaki bir kapsamı genişlettikten sonra görüntülenen kapsamın ayrıntılı bir açıklamasıdır. |
| Durum | *Etkin* | Bu kapsamın istemcilerin istemesi için kullanılabilir olup olmadığını belirler. İstemcilere görünür olmasını istemediğiniz kapsamlar için bunu "devre dışı" olarak ayarlayın. Yalnızca devre dışı olan kapsamlar silinebilir ve hiçbir istemcinin hala kullanılmadığından emin olmak için bir kapsamın silinmeden önce bir kapsamın devre dışı bırakıldıktan sonra en az bir hafta bekledikten sonra beklenmesi önerilir. |

## <a name="register-the-client-app"></a>İstemci uygulamasını kaydetme

1. Seçtiğiniz veya oluşturduğunuz Azure AD kiracısına gidin.
1. Menüden **Azure Active Directory** seçin.
1. Menüden **uygulama kayıtları** seçin.
1. **+ Yeni kayıt**' ye tıklayın.
1. İstemci uygulamaya bir ad verin (örneğin, *AMS AAD Content Protection*).
1. **Yalnızca bu kuruluş dizinindeki hesapları seçin ([yalnızca *kiracı adınız*]-tek kiracı)**. Birden çok kiracıya erişimi etkinleştirmek istiyorsanız, diğer çok kiracılı seçeneklerden birini seçin.
1. **Yeniden yönlendirme URI 'si** isteğe bağlıdır ve daha sonra değiştirilebilir.
1. **Kaydet**’e tıklayın.
1. Menüden **API izinleri** ' ni seçin.
1. **+ Izin Ekle**' ye tıklayın. Istek API 'SI izinleri görünümü açılır.
1. **API 'Me** sekmesine tıklayın ve son bölümde oluşturduğunuz *LicenseDeliveryResource2* uygulamasını seçin.
1. DRM okuna tıklayın ve DRM 'yi denetleyin *. Lisans. Delivery* izni.
1. **Izin Ekle**' ye tıklayın. İzin ekleme görünümü kapatılacak.
1. Menüden **bildirim** ' ı seçin. Bildirim görünümü görüntülenir.
1. Aşağıdaki değer çiftlerini bulun ve `replyUrlsWithType` özniteliğe ekleyin:

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > Bu noktada, Player uygulamanızın URL 'SI henüz yok.  Uygulamayı localhost Web sunucusu 'ınızdan çalıştırıyorsanız, yalnızca localhost değer çiftini kullanabilirsiniz. Oynatıcı Uygulamanızı dağıttıktan sonra, bu girdiyi dağıtılan URL ile buraya ekleyebilirsiniz.  Bunu unutursanız, Azure AD oturum açma bölümünde bir hata iletisi görürsünüz.

1. **Kaydet**’e tıklayın.
1. Son olarak yapılandırmanızın doğru olduğunu doğrulamak için **kimlik doğrulaması**' nı seçin.  Kimlik doğrulama görünümü görüntülenir. İstemci uygulamanız tek sayfalı uygulama (SPA) olarak listelenecektir, yeniden yönlendirme URI 'SI listelenir ve izin türü, yetkilendirme kodu akışını PKCE ile birlikte olacaktır.

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Media Services hesabı içerik anahtarı ilkesini ve akış ilkelerini ayarlama

**Bu bölümde, bir .NET geliştiricisi olduğunuz ve AMS v3 API 'sini kullanma hakkında bilgi sahibi olduğunuz varsayılır.**

> [!NOTE]
> Bu yazma itibariyle, OpenID-config ile asimetrik belirteç imzalama anahtarı kullanımını desteklemediği için Media Services hesap anahtarı ilke kurulumu için Azure portal kullanamazsınız.  Azure AD verilen belirteç asimetrik bir anahtar tarafından imzalandığından ve anahtar altı haftada bir daha fazla kaydolduğunda, kurulum 'un Azure AD anahtar rollover 'ı desteklemesi gerekir. Bu nedenle, bu öğretici .NET ve AMS v3 API kullanır.

DRM ve AES-128 için [içerik anahtarı ilkesinin](drm-content-key-policy-concept.md) ve [akış ilkelerinin](stream-streaming-policy-concept.md) yapılandırması geçerlidir.  `ContentKeyPolicyRestriction`İçerik anahtarı ilkesinde öğesini değiştirin.

Aşağıda içerik anahtarı ilke kısıtlaması oluşturmaya yönelik .NET kodu verilmiştir.

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

`ida_AADOpenIdDiscoveryDocument` `ida_audience` `ida_issuer` Yukarıdaki koddaki, ve değerlerini değiştirin. Azure portal bu öğelerin değerlerini bulmak için:

1. Daha önce kullandığınız AAD kiracısını seçin, menüdeki **uygulama kayıtları** ' a ve ardından **uç noktalar** bağlantısına tıklayın.
1. **Openıdconnect meta verileri belge** alanının değerini seçip kopyalayın ve değer olarak koda yapıştırın `ida_AADOpenIdDiscoveryDocument` .
1. `ida_audience`Değer, kayıtlı uygulamanın *LicenseDeliveryResource2* uygulama (istemci) kimliğidir.
1. `ida_issuer`Değer URL 'sidir `https://login.microsoftonline.com/[tenant_id]/v2.0` . [*Tenant_id*] DEĞERINI kiracı Kimliğinizle değiştirin.

## <a name="set-up-the-sample-player-app"></a>Örnek oynatıcı uygulamasını ayarlama

Henüz yapmadıysanız, uygulamayı kaynak deposundan kopyalayın veya indirin: [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad) .

Oynatıcı uygulamasını ayarlamak için iki seçeneğiniz vardır:

* Minimum özelleştirme (yalnızca client_id, tenant_id ve akış URL 'SI gibi bazı sabit dize değerlerini değiştirerek), ancak Visual Studio Code ve Node.js kullanmanız gerekir.
* ASP.NET Core gibi başka bir IDE ve web platformu kullanmayı tercih ediyorsanız, oynatıcı uygulamanın kendisi herhangi bir sunucu tarafı kodu kullanmadığından, Web sayfası dosyalarını, JavaScript dosyalarını ve CSS dosyasını projenize yerleştirebilirsiniz.

### <a name="option-1"></a>1\. Seçenek

1. Visual Studio Code’u başlatın.
1. Projeyi açmak için dosya > klasörü aç ' a tıklayın > klasöre gidin ve *package.jsdosya üzerinde* üst klasörü seçin.
1. *Public/JavaScript/constants.js* JavaScript dosyasını açın.
1. `OAUTH2_CONST.CLIENT_ID` `client_id` AAD kiracısındaki kayıtlı istemci uygulamanızın ile değiştirin.  `client_id`Azure Portal ' de kayıtlı uygulamanın genel bakış bölümünü bulabilirsiniz. Not: nesne KIMLIĞI değil, istemci KIMLIĞIDIR.
1. `OAUTH2_CONST.TENANT_ID` `tenant_id` Azure AD kiracınızın yerine koyun. `tenant_id`Azure Active Directory menüsüne tıklayarak hesabınızı bulabilirsiniz. Tenant_id genel bakış bölümünde görüntülenir.
1. `OAUTH2_CONST.SCOPE`Kayıtlı istemci uygulamanıza eklediğiniz kapsama göre değiştirin. Kapsamı, **uygulama kayıtları** menüsünden kayıtlı istemci uygulamasına giderek ve ardından istemci uygulamanızı seçerek bulabilirsiniz:
    1. İstemci uygulamanızı seçin, **API izinleri** menüsüne tıklayın ve ardından DRM kapsamını seçin *.* API Permission *LicenseDeliveryResource2* altında lisans. Delivery. İzin api://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM gibi biçimde olmalıdır *. Lisans. Delivery*. **Önemli**: alanı `offline_access` ' de önünde tutun `OAUTH2_CONST.SCOPE` .
1. Aşağıdaki iki sabit dizeyi `AMS_CONST` aşağıda gösterildiği gibi değiştirin. Biri, FairPlay test çalışmasını eklemek istiyorsanız, test varlığınızın korumalı akış URL 'si ve diğeri ise FPS uygulama sertifikası URL 'sidir. Aksi takdirde, için olduğu gibi bırakabilirsiniz `AMS_CONST.APP_CERT_URL` . Ardından **Kaydet**' e tıklayın.

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

Yerel olarak test etmek için:

1. Visual Studio Code (VSC) ' de, ana menüden **Görünüm** ' ü seçin ve ardından **Terminal**' i seçin.
1. NPM 'yi henüz yüklemediyseniz, komut isteminde yazın `npm install` .
1. `npm start`Komut istemine yazın. (NPM başlamazsa, komut istemine girerek dizinini olarak değiştirmeyi deneyin `npmweb` `cd npmweb` .)
1. Gezinmek için bir tarayıcı kullanın `http://localhost:3000` .

Kullandığınız tarayıcıya bağlı olarak, oturum açma işleminden (alma) sonra sınanacak şekilde DRM/AES vs akış protokolü vs kapsayıcı biçiminin doğru birleşimini seçin `access_token` . Safari 'de macOS 'ta test ediyorsanız, Safari 'nin açılan pencerelere izin vermediği için yeniden yönlendirme API 'SI seçeneğini işaretleyin. Diğer tarayıcıların çoğu, açılanları ve yeniden yönlendirme seçeneklerini sağlar.

### <a name="option-2"></a>2\. Seçenek

Geliştirme makinenizde çalışan IIS gibi başka bir IDE/web platformu ve/veya Web sunucusu kullanmayı planlıyorsanız, aşağıdaki dosyaları yerel Web ortamınızdaki yeni bir dizine kopyalayın. Aşağıdaki yollar, indirdiğiniz kodda bulacağınız yerdir.

* *views/Index. EJS* (son eki. html olarak değiştir)
* *views/JWT. EJS* (son eki. html olarak değiştir)
* *views/Info. EJS* (son eki HTML olarak değiştir)
* *Public/** (JavaScript dosyaları, CSS, resimler aşağıda gösterildiği gibi)

1. *Görünüm* klasöründe bulunan dosyaları yeni dizinin köküne kopyalayın.
1. *Ortak* klasörde bulunan *klasörleri* yeni dizinin köküne kopyalayın.
1. `.ejs`Dosyaların uzantılarını olarak değiştirin `.html` . (Herhangi bir sunucu tarafı değişkeni, güvenli bir şekilde değiştirebilmeniz için kullanılmaz.)
1. VSC 'de *index.html* ' i (veya diğer kod Düzenleyicisi) açın ve `<script>` ve `<link>` yollarını, dosyaların bulunduğu yeri yansıtacak şekilde değiştirin.  Önceki adımları izlediyseniz, yalnızca yolundaki öğesini silmeniz gerekir `\` .  Örneğin, `<script type="text/javascript" src="/javascript/constants.js"></script>` olur `<script type="text/javascript" src="javascript/constants.js"></script>` .
1. *JavaScript/constants.js* dosyasındaki sabitleri 1. seçenek olarak özelleştirin.

## <a name="common-customer-scenarios"></a>Ortak müşteri senaryoları

Artık öğreticiyi tamamlayıp çalışan bir alt sisteme sahip olduğunuza göre, aşağıdaki müşteri senaryolarında bunu değiştirmeyi deneyebilirsiniz:

### <a name="azure-role-based-access-control-azure-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Azure AD grup üyeliği aracılığıyla lisans teslimi için Azure rol tabanlı erişim denetimi (Azure RBAC)

Şimdiye kadar, sistem, geçerli bir lisans almak ve korunan içeriği yürütmek için oturum açabilen tüm kullanıcılara izin verir.

Diğer bir deyişle, kimliği doğrulanmış kullanıcıların bir alt kümesinin içeriği izleyebileceği, örneğin video içeriğine yönelik temel ve Premium abonelikler sunan bir müşteri olduğu yaygın bir müşteri gereksinimidir. Temel bir abonelik için ücretli olan müşterileri, Premium bir abonelik gerektiren içeriği izleyebilmelidir. Bu gereksinimi karşılamak için gereken ek adımlar aşağıda verilmiştir:

#### <a name="set-up-the-azure-ad-tenant"></a>Azure AD kiracısını ayarlama

1. Kiracınızda iki hesap ayarlayın. *Premium_user* ve *basic_user* adlandırılabilir.
1. Bir Kullanıcı grubu oluşturun ve *PremiumGroup* çağırın.
1. *Premium_user* bir üye olarak *PremiumGroup* ekleyin, ancak *basic_user* gruba eklemeyin.
1. *PremiumGroup* **nesne kimliğini** bir yere göz atın.

#### <a name="set-up-the-media-services-account"></a>Media Services hesabı ayarlama

`ContentKeyPolicyRestriction`(Media Service hesabındaki kurulum 'da gösterilen bölümde gösterildiği gibi), *gruplar* adlı bir talep ekleyerek ve `ida_EntitledGroupObjectId` DEĞERI olarak *PremiumGroup* nesne kimliğine sahip olan bir talep ekleyerek değiştirin:

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

*Gruplar* talebi, Azure AD 'de kısıtlı bir [talep kümesinin](../../active-directory/develop/reference-claims-mapping-policy-type.md#claim-sets) üyesidir.

#### <a name="test"></a>Test

1. *Premium_user* hesabıyla oturum açın. Korunan içeriği oynatabilmelisiniz.
1. *Basic_user* hesabıyla oturum açın. Videonun şifrelendiğini belirten bir hata almalısınız, ancak şifresini çözmek için bir anahtar yok. Player tanılama alt sürümünün altındaki açılan menüde olayları, hataları ve İndirmeleri görüntülediğinizde, hata iletisi, Azure AD belirteç uç noktası tarafından verilen JWT içindeki gruplar talebi için eksik talep değeri nedeniyle lisans alma başarısızlığını göstermelidir.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>Birden çok medya hizmeti hesabını destekleme (birden çok abonelik arasında)

Müşterinin tek veya birden çok Azure aboneliği üzerinde birden çok medya hizmeti hesabı olabilir. Örneğin, bir müşterinin üretim birincil olarak bir medya hizmeti hesabı, ikincil/yedek olarak diğeri ise geliştirme ve test için bir tane olabilir.

Tek yapmanız gereken, `ContentKeyPolicyRestriction` Tüm Media Service hesaplarında ' i oluştururken bölümünde kullandığınız aynı parametre kümesini (Media Service hesabı 'Nda Kurulum) kullandığınızdan emin olunması sağlamaktır.

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>Birden çok AAD kiracısının içindeki bir müşteriyi, satıcıları ve/veya yan kuruluşları destekleme

Çözümün kullanıcıları, bir müşterinin yan kuruluşları, satıcıları/ortakları, ve gibi farklı AAD kiracılarında bulunabilir `mycustomer.com` `mysubsidiary.com` `myparther.com` . Bu çözüm, gibi tek bir özel AAD kiracısında inşa edilirken `mycustomer.com` , diğer kiracılardan kullanıcılar için de iş yapabilirsiniz.

`mycustomer.com`Bu çözüm için kullanarak `mypartner.com` kiracıya Konuk Kullanıcı olarak bir kullanıcı ekleyin `mycustomer.com` . `mypartner.com`Kullanıcının Konuk hesabını etkinleştirdiğinizden emin olun. Konuk hesabı başka bir AAD kiracısından veya bir `outlook.com` hesaptan olabilir.

Konuk kullanıcıların, ' de etkinleştirildikten `mypartner.com` sonra, `mycustomer.com` kendi/orijinal AAD kiracısı aracılığıyla kimlik doğrulamasından geçdiğinin, `mypartner.com` ancak `access_token` tarafından verildiğine dikkat edin `mycustomer.com` .

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>Abonelik/kiracınızdaki bir kurulumla bir müşteri kiracısını/aboneliğini destekleme

Ayarlarınızı, müşterinizin medya hizmeti hesabındaki/aboneliğinizdeki korumalı içeriği test etmek için kullanabilirsiniz. Aynı abonelikte bir Azure AD kiracısı ve bir medya hizmeti hesabıyla ayarlanır. Müşterinin medya hizmeti hesabı kendi Azure AD kiracılarıyla Azure aboneliklerinde yer alırlar.

1. Müşterinizin hesabını kiracınıza bir Konuk hesabı olarak ekleyin.
1. Media Service hesabı 'nda kurulum bölümünde listelenen üç parametreyi belirterek müşterinizin medya hizmeti hesabındaki korumalı içeriği hazırlamak için müşterinizden çalışın.

Daha sonra müşteriniz kuruluma gidebilir, Konuk hesabıyla oturum açabilir ve kendi korumalı içeriğini test edebilir. Ayrıca kendi hesabınızla oturum açıp müşterinizin içeriğini test edebilirsiniz.

Örnek çözümünüz Microsoft aboneliği veya Microsoft aboneliği ile özel kiracı ile Microsoft kiracısında ayarlanabilir. Azure Media Service örneği, kiracısına sahip başka bir abonelikten olabilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

> [!WARNING]
> Bu uygulamayı kullanmaya devam etmeyecekecekseniz, bu öğreticiyi izleyerek oluşturduğunuz kaynakları silin. Aksi takdirde, bunlar için ücretlendirilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Hızlı başlangıç: içeriği şifreleyin](drm-encrypt-content-how-to.md)
