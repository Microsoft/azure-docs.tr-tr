---
title: Azure Active Directory B2C 'de Android uygulaması kullanarak belirteç alma | Microsoft Docs
description: Bu makalede, Kullanıcı kimliklerini yönetmek ve kullanıcıların kimliğini doğrulamak için Azure Active Directory B2C ile AppAuth kullanan bir Android uygulamasının nasıl oluşturulacağı gösterilir.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 28b1c3622ca449b0ce539937369fe43bd1d508ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468979"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C Android uygulaması kullanarak oturum açma

Microsoft kimlik platformu OAuth2 ve OpenID Connect gibi açık standartlar kullanır. Bu standartlar, Azure Active Directory B2C tümleştirmesini istediğiniz herhangi bir kitaplığı kullanmanıza olanak sağlar. Diğer kitaplıkları kullanmanıza yardımcı olması için, bu, üçüncü taraf kitaplıklarının Microsoft Identity platformuna bağlanmak üzere nasıl yapılandırılacağını göstermek için buna benzer bir yol kullanabilirsiniz. [RFC6749 OAuth2 belirtimini](https://tools.ietf.org/html/rfc6749) uygulayan çoğu kitaplık Microsoft Identity platformu 'na bağlanabilir.

> [!WARNING]
> Microsoft üçüncü taraf kitaplıklar için düzeltmeler sağlamaz ve bu kitaplıkların bir gözden geçirilmesini gerçekleştirmez. Bu örnek, Azure AD B2C ile temel senaryolarda uyumluluk için test edilmiş AppAuth adlı bir 3. taraf kitaplığı kullanmaktır. Sorunlar ve özellik istekleri kitaplığın açık kaynaklı projesine yönlendirilmelidir. Daha fazla bilgi için lütfen [Bu makaleye](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) bakın.
>
>

OAuth2 veya OpenID Connect kullanmaya yeni başladıysanız bu örnek yapılandırmanın büyük bölümü sizin için çok anlamlı olmayabilir. [burada belgelediğimiz protokolün genel bakışına](active-directory-b2c-reference-protocols.md) kısaca bakmanız önerilir.

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C dizini alma

Azure AD B2C'yi kullanabilmek için önce dizin veya kiracı oluşturmanız gerekir. Dizin; tüm kullanıcılarınız, uygulamalarınız, gruplarınız ve daha fazlası için bir kapsayıcıdır. Henüz yoksa devam etmeden önce [bir B2C dizini oluşturun](tutorial-create-tenant.md).

## <a name="create-an-application"></a>Uygulama oluşturma

Sonra, Azure AD B2C kiracınıza bir uygulamayı kaydedin. Bu, Azure AD 'nin uygulamanızla güvenli bir şekilde iletişim kurması için gereken bilgileri sağlar.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.

Ayrıca, daha sonraki bir adımda kullanmak üzere özel yeniden yönlendirme URI 'nizi kaydedin. Örneğin, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Kullanıcı akışlarınızı oluşturun

Azure AD B2C, her kullanıcı deneyimi, Azure AD 'nin davranışını denetleyen bir ilke kümesi olan bir [Kullanıcı akışı](active-directory-b2c-reference-policies.md)tarafından tanımlanır. Bu uygulama, oturum açma ve kaydolma Kullanıcı akışı gerektirir. Kullanıcı akışını oluştururken şunları yaptığınızdan emin olun:

* Kullanıcı akışındaki bir kaydolma özniteliği olarak **görünen adı** seçin.
* Her Kullanıcı akışında **görünen ad** ve **nesne kimliği** uygulama taleplerini seçin. Diğer talepleri de seçebilirsiniz.
* Her Kullanıcı akışının **adını** oluşturduktan sonra kopyalayın. `b2c_1_` önekine sahip olmalıdır.  Kullanıcı akış adı daha sonra gerekecektir.

Kullanıcı akışlarınızı oluşturduktan sonra uygulamanızı oluşturmaya hazırsınız demektir.

## <a name="download-the-sample-code"></a>Örnek kodu indirin

[GitHub üzerinde](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c)Azure AD B2C AppAuth kullanan bir çalışan örnek sağladık. Kodu indirebilir ve çalıştırabilirsiniz. [README.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md)içindeki yönergeleri izleyerek kendi Azure AD B2C yapılandırmanızı kullanarak kendi uygulamanızı hızlı bir şekilde kullanmaya başlamanızı sağlayabilirsiniz.

Örnek, [Appauth](https://openid.github.io/AppAuth-Android/)tarafından sunulan örneğin bir değişimdir. AppAuth ve özellikleri hakkında daha fazla bilgi edinmek için lütfen sayfasını ziyaret edin.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Uygulamanızı AppAuth ile Azure AD B2C kullanmak üzere değiştirme

> [!NOTE]
> AppAuth, Android API 16 (Jellybean) ve üstünü destekler. API 23 ve üstünü kullanmanızı öneririz.
>

### <a name="configuration"></a>Yapılandırma

Azure AD B2C ile iletişimi, bulma URI 'sini belirterek veya hem yetkilendirme uç noktası hem de belirteç uç noktası URI 'Lerini belirterek yapılandırabilirsiniz. Her iki durumda da aşağıdaki bilgilere ihtiyacınız olacaktır:

* Kiracı KIMLIĞI (ör. contoso.onmicrosoft.com)
* Kullanıcı akış adı (ör. B2C\_1\_SignUpIn)

Yetkilendirme ve belirteç uç noktası URI 'Lerini otomatik olarak bulmayı seçerseniz, bulma URI 'sinden bilgi almanız gerekir. Aşağıdaki URL 'deki kiracı\_KIMLIĞI ve Ilke\_adı değiştirilerek bulma URI 'SI oluşturulabilir:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Daha sonra yetkilendirme ve belirteç uç noktası URI 'Lerini alabilir ve aşağıdakileri çalıştırarak bir AuthorizationServiceConfiguration nesnesi oluşturabilirsiniz:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

Yetkilendirme ve belirteç uç noktası URI 'Lerini elde etmek için bulma kullanmak yerine, kiracı\_KIMLIĞINI ve aşağıdaki URL 'deki Ilke\_adını değiştirerek ayrıca bunları açıkça belirtebilirsiniz:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

AuthorizationServiceConfiguration nesneniz oluşturmak için aşağıdaki kodu çalıştırın:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>İşlemidir

Yetkilendirme hizmeti yapılandırmasını yapılandırdıktan veya aldıktan sonra bir yetkilendirme isteği oluşturulabilir. İsteği oluşturmak için aşağıdaki bilgilere ihtiyacınız olacaktır:

* Daha önce kaydettiğiniz istemci KIMLIĞI (uygulama KIMLIĞI). Örneğin, `00000000-0000-0000-0000-000000000000`.
* Daha önce kaydettiğiniz özel yeniden yönlendirme URI 'SI. Örneğin, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

[Uygulamanızı kaydederken](#create-an-application)her iki öğe de kaydedilmiş olmalıdır.

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Lütfen işlemin geri kalanını nasıl tamamlayacağından ilgili [Appauth kılavuzuna](https://openid.github.io/AppAuth-Android/) bakın. Çalışan bir uygulamayı hızlıca kullanmaya başlamak için [örneğimize](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c)göz atın. Kendi Azure AD B2C yapılandırmanızı girmek için [README.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) içindeki adımları izleyin.
