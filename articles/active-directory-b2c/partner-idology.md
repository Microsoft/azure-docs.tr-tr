---
title: Azure Active Directory B2C ile tümleştirme IDology
titleSuffix: Azure AD B2C
description: Örnek bir çevrimiçi ödeme uygulamasını IDology ile Azure AD B2C tümleştirme hakkında bilgi edinin. IDology, birden çok çözüm içeren bir kimlik doğrulama ve sağlama sağlayıcıdır.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: df0fcaf7987e30f9c2599346aaef5fcabb4f04e9
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256659"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>Azure Active Directory B2C ile IDology yapılandırma öğreticisi 

Bu örnek öğreticide, [IDology](https://www.idology.com/solutions/)ile Azure AD B2C tümleştirme hakkında rehberlik sağlıyoruz. IDology, birden çok çözüm içeren bir kimlik doğrulama ve sağlama sağlayıcıdır. Bu örnekte, ExpectID çözümünü IDology tarafından ele alınacaktır.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Azure aboneliğinize bağlı [bir Azure AD B2C kiracısı](tutorial-create-tenant.md) .

## <a name="scenario-description"></a>Senaryo açıklaması

IDology tümleştirmesi aşağıdaki bileşenleri içerir:

- Azure AD B2C: kullanıcının kimlik bilgilerini doğrulamaktan sorumlu yetkilendirme sunucusu. Kimlik sağlayıcısı olarak da bilinir.
- IDology – IDology hizmeti kullanıcı tarafından sağlanmış girişi alır ve kullanıcının kimliğini doğrular.
- Özel REST API 'SI – bu API, Azure AD ile IDology hizmeti arasındaki tümleştirmeyi uygular.

Aşağıdaki mimari diyagram uygulamayı gösterir.

![IDology mimari diyagramı](media/partner-idology/idology-architecture-diagram.png)

| Adım | Açıklama |
|------|------|
|1     | Kullanıcı oturum açma sayfasına ulaşır. |
|2     | Kullanıcı, yeni bir hesap oluşturmak ve sayfaya bilgi girmek için kaydolma seçeneğini seçer. Azure AD B2C Kullanıcı özniteliklerini toplar. |
|3     | Azure AD B2C orta katman API 'sini çağırır ve Kullanıcı özniteliklerine geçirir. |
|4     | Orta katman API 'SI Kullanıcı özniteliklerini toplar ve bunları IDOlogy API 'sinin tüketebileceği bir biçime dönüştürür. Daha sonra bilgileri IDology 'e gönderir. |
|5     | IDology, bilgileri kullanır ve işler, ardından sonucu orta katman API 'sine döndürür. |
|6     | Orta katman API 'SI bilgileri işler ve ilgili bilgileri Azure AD B2C 'e geri gönderir. |
|7     | Azure AD B2C, bilgileri ortadaki katman API 'sinden geri alır. **Hata yanıtı gösteriyorsa** , kullanıcıya bir hata iletisi görüntülenir. **Başarılı** bir yanıt gösteriyorsa, kullanıcının kimliği doğrulanır ve dizine yazılır. |
|      |      |

> [!NOTE]
> Azure AD B2C ayrıca müşterinin adım adım kimlik doğrulaması gerçekleştirmesini isteyebilir, ancak bu senaryo Bu öğreticinin kapsamı dışındadır.

## <a name="onboard-with-idology"></a>IDology ile ekleme

1. IDology, [burada](https://www.idology.com/solutions/)bulabileceğiniz çeşitli çözümler sunar. Bu örnek için ExpectID kullanacağız.

2. IDology hesabı oluşturmak için [IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/)başvurun.

3. Bir hesap oluşturulduktan sonra, API yapılandırması için gereken bilgileri alacaksınız. Aşağıdaki bölümlerde işlem açıklanır.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C ile tümleştirme

### <a name="part-1---deploy-the-api"></a>1. Bölüm-API 'YI dağıtma

Belirtilen [API kodunu](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api) bir Azure hizmetine dağıtın. Kod, bu [yönergeleri](/visualstudio/deployment/quickstart-deploy-to-azure)Izleyerek Visual Studio 'dan yayımlanabilir.

Azure AD 'yi gerekli ayarlarla yapılandırmak için dağıtılan hizmetin URL 'sine ihtiyacınız vardır.

### <a name="part-2---configure-the-api"></a>Bölüm 2-API 'YI yapılandırma 

Uygulama ayarları, [Azure 'da App Service yapılandırılabilir](../app-service/configure-common.md#configure-app-settings). Bu yöntemde, ayarlar bir depoya denetlenmeden güvenle yapılandırılabilir. REST API için aşağıdaki ayarları sağlamanız gerekir:

| Uygulama ayarları | Kaynak | Notlar |
| :-------- | :------------| :-----------|
|Idologysettings: ApiUsername | IDology hesabı yapılandırması |     |
|Idologysettings: ApiPassword | IDology hesabı yapılandırması |     |
|WebApiSettings: ApiUsername |API için Kullanıcı adı tanımlama| Extıd yapılandırmasında kullanılır |
|WebApiSettings: ApiPassword | API için bir parola tanımlayın | Extıd yapılandırmasında kullanılır

### <a name="part-3---create-api-policy-keys"></a>3. kısım-API Ilkesi anahtarları oluşturma

İki ilke anahtarı oluşturmak için bu [belgeyi](secure-rest-api.md#add-rest-api-username-and-password-policy-keys) izleyin: biri API Kullanıcı adı, diğeri ise yukarıda tanımladığınız API parolası için.

Örnek ilke şu anahtar adlarını kullanır:

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Bölüm 4-Azure AD B2C Ilkesini yapılandırma

1. [LocalAccounts başlangıç paketini](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) indirmek ve Azure AD B2C kiracısı için ilkeyi yapılandırmak üzere bu [belgeyi](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) izleyin. **Özel Ilkeyi test** etme bölümünü tamamlayana kadar yönergeleri izleyin.

2. Bu iki örnek ilkeyi [buraya](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy)indirin.

3. İki örnek ilkeyi güncelleştirin:

   1. Her iki ilkeyi de aç:

      1. Bölümünde `Idology-ExpectId-API` , `ServiceUrl` meta veri öğesini yukarıda dağıtılan API 'nin konumuyla güncelleştirin.

      1. `yourtenant`Azure AD B2C kiracınızın adıyla değiştirin.
      Örneğin, Azure AD B2C kiracınızın adı ise  `contosotenant` , öğesinin tüm örneklerini  `yourtenant.onmicrosoft.com`   ile değiştirin `contosotenant.onmicrosoft.com` .

   1. Dosya TrustFrameworkExtensions.xml açın:

      1. Öğesini bulun  `<TechnicalProfile Id="login-NonInteractive">` . Her iki örneğini,  `IdentityExperienceFrameworkAppId`   daha önce oluşturduğunuz IdentityExperienceFramework UYGULAMASıNıN uygulama kimliğiyle değiştirin.

      1. Her iki örneğini,  `ProxyIdentityExperienceFrameworkAppId`   daha önce oluşturduğunuz ProxyIdentityExperienceFramework UYGULAMASıNıN uygulama kimliğiyle değiştirin.

4. SignInorSignUp.xml değiştirin ve daha önce, 1. adımdaki Azure AD B2C 'e yüklenmiş iki örnek ilke ile TrustFrameworkExtensions.xml.

> [!NOTE]
> En iyi uygulama olarak, müşterilerin öznitelik koleksiyonu sayfasına onay bildirimi eklemesini öneririz. Kimlik doğrulaması için üçüncü taraf hizmetlere bilgi gönderileceğini kullanıcılara bildirin.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Azure AD B2C kiracıyı açın ve **ilkeler** altında **Kullanıcı akışları**' nı seçin.

2. Daha önce oluşturduğunuz **Kullanıcı akışınızı** seçin.

3. **Kullanıcı akışını Çalıştır** ' ı seçin ve ayarları seçin:

   1. **Uygulama** -kayıtlı uygulamayı seçin (örnek JWT).

   1. **Yanıt URL 'si** - **yeniden yönlendirme URL**'sini seçin.

   1. **Kullanıcı akışını Çalıştır**' ı seçin.

4. Kaydolma akışı ' na gidip bir hesap oluşturun.

5. Oturumunuzu kapatın.

6. Oturum açma akışına gidin.

7. IDology bulmaca, **devam et** girdikten sonra görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [Azure AD B2C'deki özel ilkeler](custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
