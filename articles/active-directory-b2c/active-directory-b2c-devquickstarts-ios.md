---
title: Azure Active Directory B2C 'de bir iOS uygulamasında AppAuth kullanma | Microsoft Docs
description: Bu makalede, Kullanıcı kimliklerini yönetmek ve kullanıcıların kimliğini doğrulamak için Azure Active Directory B2C ile AppAuth kullanan bir iOS uygulamasının nasıl oluşturulacağı gösterilmektedir.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0fb5341c2e7ee55391cb38251b0ea66b55b93301
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469147"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: iOS uygulaması kullanarak oturum açma

Microsoft kimlik platformu OAuth2 ve OpenID Connect gibi açık standartlar kullanır. Açık bir standart protokol kullanmak, hizmetlerimizle tümleştirilecek bir kitaplığı seçerken daha fazla geliştirici seçeneği sunar. Geliştiricilere Microsoft Identity platformu 'na bağlanan uygulamalar yazma konusunda yardımcı olmak için bu izlenecek yolu ve diğer diğerlerini sağladık. [RFC6749 OAuth2 spec](https://tools.ietf.org/html/rfc6749) uygulayan çoğu kitaplık Microsoft Identity platformu 'na bağlanabilir.

> [!WARNING]
> Microsoft üçüncü taraf kitaplıklar için düzeltmeler sağlamaz ve bu kitaplıkların gözden geçirilmesini yapılmamış demektir. Bu örnek, Azure AD B2C ile temel senaryolarda uyumluluk için test edilmiş AppAuth adlı bir üçüncü taraf kitaplığı kullanmaktır. Sorunlar ve özellik istekleri kitaplığın açık kaynaklı projesine yönlendirilmelidir. Daha fazla bilgi için [bu makaleye](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) bakın.
>
>

OAuth2 veya OpenID Connect ' i yeni başladıysanız, bu örnek yapılandırmanın büyük bölümü sizin için çok anlamlı olmayabilir. [burada belgelediğimiz protokolün genel bakışına](active-directory-b2c-reference-protocols.md) kısaca bakmanız önerilir.

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C dizini alma
Azure AD B2C'yi kullanabilmek için önce dizin veya kiracı oluşturmanız gerekir. Dizin, tüm kullanıcılarınızın, uygulamalarınızın, grupların ve daha fazlası için bir kapsayıcıdır. Henüz yoksa devam etmeden önce [bir B2C dizini oluşturun](tutorial-create-tenant.md).

## <a name="create-an-application"></a>Uygulama oluşturma

Sonra, Azure AD B2C kiracınıza bir uygulamayı kaydedin. Bu, Azure AD 'nin uygulamanızla güvenli bir şekilde iletişim kurması için gereken bilgileri sağlar.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.

Ayrıca, daha sonraki bir adımda kullanmak üzere özel yeniden yönlendirme URI 'nizi kaydedin. Örneğin, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Kullanıcı akışlarınızı oluşturun
Azure AD B2C, her kullanıcı deneyimi bir [Kullanıcı akışı](active-directory-b2c-reference-policies.md)tarafından tanımlanır. Bu uygulama bir kimlik deneyimi içerir: Birleşik bir oturum açma ve kaydolma. Kullanıcı akışını oluştururken şunları yaptığınızdan emin olun:

* **Kaydolma öznitelikleri**altında, öznitelik **görünen adını**seçin.  Başka öznitelikler de seçebilirsiniz.
* **Uygulama talepleri**bölümünde, talep **görünen adını** ve **kullanıcının nesne kimliğini**seçin. Diğer talepler ' i de seçebilirsiniz.
* Her Kullanıcı akışının **adını** oluşturduktan sonra kopyalayın. Kullanıcı akışını kaydettiğinizde Kullanıcı akış adınızın önüne `b2c_1_` eklenir.  Kullanıcı akış adının daha sonra olması gerekir.

Kullanıcı akışlarınızı oluşturduktan sonra uygulamanızı oluşturmaya hazırsınız demektir.

## <a name="download-the-sample-code"></a>Örnek kodu indirin
[GitHub üzerinde](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c)Azure AD B2C AppAuth kullanan bir çalışan örnek sağladık. Kodu indirebilir ve çalıştırabilirsiniz. Kendi Azure AD B2C kiracınızı kullanmak için [README.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md)içindeki yönergeleri izleyin.

Bu örnek, [GitHub 'Daki IOS AppAuth projesi](https://github.com/openid/AppAuth-iOS)tarafından Benioku yönergelerini izleyerek oluşturulmuştur. Örnek ve kitaplığın nasıl çalıştığı hakkında daha fazla bilgi için GitHub 'da AppAuth Benioku dosyasına başvurun.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Uygulamanızı AppAuth ile Azure AD B2C kullanmak üzere değiştirme

> [!NOTE]
> AppAuth, iOS 7 ve üstünü destekler.  Ancak, Google üzerinde sosyal oturum açma işlemlerini desteklemek için iOS 9 veya üstünü gerektiren SFSafariViewController gerekir.
>

### <a name="configuration"></a>Yapılandırma

Yetkilendirme uç noktası ve belirteç uç noktası URI 'Lerini belirterek Azure AD B2C iletişim yapılandırabilirsiniz.  Bu URI 'Leri oluşturmak için aşağıdaki bilgilere ihtiyacınız vardır:
* Kiracı KIMLIĞI (örneğin, contoso.onmicrosoft.com)
* Kullanıcı akış adı (örneğin, B2C\_1\_SignUpIn)

Belirteç uç noktası URI 'SI, kiracı\_KIMLIĞI ve Ilke\_adı aşağıdaki URL 'de değiştirilerek oluşturulabilir:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Aşağıdaki URL 'deki kiracı\_KIMLIĞI ve Ilke\_adı değiştirilerek yetkilendirme uç noktası URI 'SI oluşturulabilir:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

AuthorizationServiceConfiguration nesneniz oluşturmak için aşağıdaki kodu çalıştırın:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>İşlemidir

Yetkilendirme hizmeti yapılandırmasını yapılandırdıktan veya aldıktan sonra bir yetkilendirme isteği oluşturulabilir. İsteği oluşturmak için aşağıdaki bilgilere ihtiyacınız vardır:

* Daha önce kaydettiğiniz istemci KIMLIĞI (uygulama KIMLIĞI). Örneğin, `00000000-0000-0000-0000-000000000000`.
* Daha önce kaydettiğiniz özel yeniden yönlendirme URI 'SI. Örneğin, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

[Uygulamanızı kaydederken](#create-an-application)her iki öğe de kaydedilmiş olmalıdır.

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Uygulamanızı özel şemayla URI 'ye yeniden yönlendirmeyi işleyecek şekilde ayarlamak için, Info. pList dosyanızın ' URL şemaları ' listesini güncelleştirmeniz gerekir:
* Info. pList öğesini açın.
* ' Paket işletim sistemi türü kodu ' gibi bir satırın üzerine gelin ve \+ simgesine tıklayın.
* Yeni ' URL türleri ' satırını yeniden adlandırın.
* Ağacı açmak için ' URL türleri ' sol tarafındaki oka tıklayın.
* Ağacı açmak için ' Item 0 ' solundaki oka tıklayın.
* 0 öğesinin altındaki ilk öğeyi ' URL şemaları ' olarak yeniden adlandırın.
* Ağacı açmak için ' URL şemaları ' sol tarafındaki oka tıklayın.
* ' Value ' sütununda, ' URL şemaları ' altında ' Item 0 ' solunda boş bir alan var.  Değeri uygulamanızın benzersiz düzenine ayarlayın.  Oıdaduthorizationrequest nesnesi oluşturulurken bu değerin redirectURL içinde kullanılan şemayla eşleşmesi gerekir.  Örnekte, ' com. onmicrosoft. fabrikamb2c. exampleapp ' şeması kullanılır.

İşlemin geri kalanını tamamlamaya yönelik [Appauth kılavuzuna](https://openid.github.io/AppAuth-iOS/) bakın. Çalışan bir uygulamayla hızlı bir şekilde çalışmaya başlamak için [örneğe](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c)göz atın. Kendi Azure AD B2C yapılandırmanızı girmek için [README.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) içindeki adımları izleyin.

Geri bildirim ve önerilere her zaman açıktır! Bu makaleyle ilgili zorluklar varsa veya bu içeriği geliştirmeye yönelik önerileriniz varsa, sayfanın en altında geri bildiriminizi beğeneceğiz. Özellik istekleri için bunları [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)'a ekleyin.
