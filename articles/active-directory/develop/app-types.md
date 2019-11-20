---
title: V 1.0 'daki uygulama türleri | Mavisi
description: Azure Active Directory v 2.0 uç noktası tarafından desteklenen uygulama ve senaryoların türlerini açıklar.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72fab8a51f1ea1b9c7bda14d825dad538f96cde6
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374057"
---
# <a name="application-types-in-v10"></a>V 1.0 'daki uygulama türleri

Azure Active Directory (Azure AD), endüstri standardı protokoller OAuth 2,0 veya OpenID Connect temelinde çeşitli modern uygulama mimarilerinin kimlik doğrulamasını destekler.

Aşağıdaki diyagramda senaryolar ve uygulama türleri ve farklı bileşenlerin nasıl eklenebileceği gösterilmektedir:

![Uygulama Türleri ve senaryolar](./media/authentication-scenarios/application_types_and_scenarios.png)

Azure AD tarafından desteklenen beş birincil uygulama senaryosu şunlardır:

- **[Tek sayfalı uygulama (Spa)](single-page-application.md)** : kullanıcının Azure AD tarafından güvenliği sağlanmış tek sayfalı bir uygulamada oturum açması gerekir.
- Web **[uygulamasına Web tarayıcısı](web-app.md)** : bir kullanıcının Azure AD tarafından güvenliği sağlanmış bir Web uygulamasında oturum açması gerekir.
- **[Yerel uygulama Web API 'sine](native-app.md)** : bir telefonda, tablette veya bilgisayar üzerinde çalışan yerel bir uygulamanın, Azure AD tarafından güvenliği sağlanmış BIR Web API 'sinden kaynak alması için bir kullanıcının kimliğini doğrulaması gerekir.
- Web **[uygulaması Web API 'si](web-api.md)** : bir Web UYGULAMASıNıN Azure AD ile güvenliği sağlanmış BIR Web API 'sinden kaynak alması gerekir.
- **[Web API 'sine yönelik Daemon veya sunucu uygulaması](service-to-service.md)** : Web Kullanıcı arabirimi olmayan bir Daemon uygulamasının veya bir sunucu UYGULAMASıNıN Azure AD tarafından güvenliği sağlanmış BIR Web API 'sinden kaynak alması gerekir.

Her uygulama türü hakkında daha fazla bilgi edinmek ve kodla çalışmaya başlamadan önce üst düzey senaryoları anlamak için bağlantıları izleyin. Ayrıca, v 1.0 Endpoint veya v 2.0 uç noktası ile birlikte çalışarak belirli bir uygulamayı yazarken bilmeniz gereken farklılıklar hakkında bilgi edinebilirsiniz.

> [!NOTE]
> V 2.0 uç noktası tüm Azure AD senaryolarını ve özelliklerini desteklemez. V 2.0 uç noktasını kullanmanız gerekip gerekmediğini öğrenmek için, [v 2.0 sınırlamalarını](active-directory-v2-limitations.md)okuyun.

Çeşitli diller ve platformlar kullanılarak burada açıklanan uygulama ve senaryolardan herhangi birini geliştirebilirsiniz. Bunlar, kod örnekleri kılavuzunda bulunan tüm kod örnekleri tarafından desteklenir, senaryoya göre [v 1.0 kod örnekleri](sample-v1-code.md) ve [senaryoya göre v 2.0 kod örnekleri](sample-v2-code.md). Kod örneklerini doğrudan ilgili [GitHub örnek depolarından](https://github.com/Azure-Samples?q=active-directory)da indirebilirsiniz.

Ayrıca, uygulamanız bir uçtan uca senaryonun belirli bir parçasına veya kesimine ihtiyacı varsa, çoğu durumda işlevsellik bağımsız olarak eklenebilirler. Örneğin, bir Web API 'SI çağıran yerel bir uygulamanız varsa, Web API 'sini de çağıran bir Web uygulamasını kolayca ekleyebilirsiniz.

## <a name="app-registration"></a>Uygulama kaydı

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Azure AD v 1.0 uç noktasını kullanan bir uygulamayı kaydetme

Azure AD 'de kimlik doğrulaması yapan herhangi bir uygulamanın bir dizine kaydedilmesi gerekir. Bu adım, Azure AD 'nin bulunduğu URL 'yi, kimlik doğrulamasından sonra yanıtları göndermek için URL 'yi, uygulamanızı tanımlayacak URI 'yi ve daha fazlasını içeren, uygulamanız hakkında bilgi ister. Bu bilgiler, bazı önemli nedenlerle gereklidir:

* Azure AD 'de oturum açma veya belirteçleri değiştirme sırasında uygulamayla iletişim kurması gerekir. Azure AD ile uygulama arasında geçirilen bilgiler şunları içerir:
  
  * **Uygulama kimliği URI 'si** -bir uygulama için tanımlayıcı. Bu değer, arayanın hangi uygulamayı bir belirteç istediğini göstermek için kimlik doğrulama sırasında Azure AD 'ye gönderilir. Ayrıca, uygulamanın amaçlanan hedef olduğunu bilmesi için bu değer belirtece dahil edilir.
  * **Yanıt URL 'si** ve **yeniden yönlendirme URI 'si** -bir Web API 'si veya Web uygulaması için, kimlik doğrulaması başarılı olursa belirteç de dahIl olmak üzere Azure AD 'nin kimlik doğrulama yanıtını göndereceği konumdur. Yerel bir uygulama için, yeniden yönlendirme URI 'SI, Azure AD 'nin bir OAuth 2,0 isteğindeki Kullanıcı aracısını yeniden yönlendireceği benzersiz bir tanımlayıcıdır.
  * **Uygulama kimliği** -uygulama KAYDEDILDIĞINDE Azure AD tarafından oluşturulan BIR uygulamanın kimliği. Bir yetkilendirme kodu veya belirteci istenirken, kimlik doğrulama sırasında Azure AD 'ye uygulama KIMLIĞI ve anahtarı gönderilir.
  * **Anahtar** -BIR Web API 'sini çağırmak IÇIN Azure AD kimlik doğrulaması yapılırken BIR uygulama kimliğiyle birlikte gönderilen anahtar.
* Azure AD 'nin, uygulamanın dizin verilerinize, kuruluşunuzdaki diğer uygulamalara ve benzeri bir erişim için gerekli izinlere sahip olduğundan emin olması gerekir.

Ayrıntılar için, [bir uygulamayı nasıl kaydedeceğinizi](quickstart-register-app.md)öğrenin.

## <a name="single-tenant-and-multi-tenant-apps"></a>Tek kiracılı ve çok kiracılı uygulamalar

Azure AD ile geliştirilebilen ve tümleştirilebilen iki uygulama kategorisi olduğunu anladığınızda, sağlama daha anlaşılır hale gelir:

* **Tek kiracılı uygulama** -tek bir kiracı uygulaması tek bir kuruluşta kullanılmak üzere tasarlanmıştır. Bunlar, genellikle kurumsal bir geliştirici tarafından yazılan iş kolu (LoB) uygulamalardır. Tek bir kiracı uygulamasına yalnızca bir dizindeki kullanıcılar tarafından erişilmesi gerekir ve sonuç olarak yalnızca bir dizinde sağlanması gerekir. Bu uygulamalar genellikle kuruluştaki bir geliştirici tarafından kaydedilir.
* **Çok kiracılı uygulama** -çok kiracılı bir uygulama, yalnızca bir kuruluş değil birçok kuruluşta kullanılmak üzere tasarlanmıştır. Bunlar, genellikle bağımsız bir yazılım satıcısı (ISV) tarafından yazılan hizmet olarak yazılım (SaaS) uygulamalardır. Çok kiracılı uygulamaların kullanılacağı her dizinde sağlanması gerekir, bu da Kullanıcı veya yönetici tarafından kaydolmak için onay gerektirir. Bu onay işlemi, bir uygulama dizine kaydedildiğinde başlar ve Graph API veya belki başka bir Web API 'sine erişim izni verilir. Farklı bir kuruluştan Kullanıcı veya yönetici uygulamayı kullanmak üzere kaydolduğunda, uygulamanın gerektirdiği izinleri görüntüleyen bir iletişim kutusu görüntülenir. Kullanıcı veya yönetici daha sonra uygulamaya izin verebilir, bu da uygulamanın belirtilen verilere erişmesini sağlar ve son olarak uygulamayı kendi dizinine kaydeder. Daha fazla bilgi için bkz. [onay çerçevesine genel bakış](consent-framework.md).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Tek kiracılı veya çok kiracılı uygulamalar geliştirirken dikkat edilecek ek noktalar

Tek bir kiracı uygulaması yerine çok kiracılı bir uygulama geliştirilirken bazı ek konular ortaya çıkar. Örneğin, uygulamanızı birden çok dizindeki kullanıcılar için kullanılabilir hale getiriyorsanız, hangi kiracının bulunduğunu belirlemeniz için bir mekanizmaya ihtiyacınız vardır. Tek bir kiracı uygulamasının bir kullanıcı için kendi dizinine bakması gerekir, ancak çok kiracılı bir uygulamanın Azure AD 'deki tüm dizinlerden belirli bir kullanıcıyı tanımlaması gerekir. Bu görevi gerçekleştirmek için Azure AD, herhangi bir çok kiracılı uygulamanın kiracıya özgü bir uç nokta yerine, oturum açma isteklerini doğrudan yönlendirbildiği ortak bir kimlik doğrulama uç noktası sağlar. Bu uç nokta, Azure AD 'deki tüm dizinler için https://login.microsoftonline.com/common ' dır, ancak kiracıya özgü bir uç nokta https://login.microsoftonline.com/contoso.onmicrosoft.com olabilir. Genellikle, oturum açma, oturum kapatma ve belirteç doğrulama sırasında birden çok kiracıyı işlemek için gerekli mantığın olması gerekeceğinden, genel uç nokta, uygulamanızı geliştirirken göz önünde bulundurmanız önemlidir.

Şu anda tek bir kiracı uygulaması geliştirmekte, ancak bunu birçok kuruluşta kullanılabilir hale getirmek istiyorsanız, Azure AD 'de uygulamada ve yapılandırmada kolayca değişiklikler yapabilir ve bunu çok kiracılı hale getirebilirsiniz. Ayrıca, Azure AD, tek bir kiracıda veya çok kiracılı bir uygulamada kimlik doğrulaması sağlayıp sağlamaktan bağımsız olarak tüm dizinlerdeki tüm belirteçler için aynı imza anahtarını kullanır.

Bu belgede listelenen her senaryo, sağlama gereksinimlerini açıklayan bir alt bölüm içerir. Azure AD 'de bir uygulama sağlama ve tek ve çok kiracılı uygulamalar arasındaki farklılıklar hakkında daha ayrıntılı bilgi için bkz. daha fazla bilgi için [uygulamaları Azure Active Directory tümleştirme](quickstart-v1-integrate-apps-with-azure-ad.md) . Azure AD 'deki yaygın uygulama senaryolarını anlamak için okumaya devam edin.

## <a name="next-steps"></a>Sonraki adımlar

- Diğer Azure AD [kimlik doğrulaması temelleri](v1-authentication-scenarios.md) hakkında daha fazla bilgi edinin
