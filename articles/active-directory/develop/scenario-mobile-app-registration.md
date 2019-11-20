---
title: Web API 'Lerini çağıran mobil uygulama-uygulamanın kod yapılandırması
titleSuffix: Microsoft identity platform
description: Web API 'Lerini (uygulamanın kod yapılandırması) çağıran bir mobil uygulama oluşturmayı öğrenin
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f55e73fa1a73908d7e77bacc6af24ea1a40ba92
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803728"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Web API 'Lerini çağıran mobil uygulama-uygulama kaydı

Bu makale, bir mobil uygulama oluşturmaya yönelik uygulama kayıt yönergelerini içerir.

## <a name="supported-accounts-types"></a>Desteklenen hesap türleri

Mobil uygulamalarda desteklenen hesap türleri, etkinleştirmek istediğiniz deneyime ve kullanmak istediğiniz akışlara bağlıdır.

### <a name="audience-for-interactive-token-acquisition"></a>Etkileşimli belirteç alımı için hedef kitle

Çoğu mobil uygulama etkileşimli kimlik doğrulaması kullanır. Bu durumda, kullanıcıların herhangi bir [Hesap türünden](quickstart-register-app.md#register-a-new-application-using-the-azure-portal) oturum açmasını sağlayabilirsiniz

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>Tümleşik kimlik doğrulaması, Kullanıcı adı/parola ve B2C için hedef kitle

- Tümleşik Windows kimlik doğrulamasını (UWP uygulamalarında mümkündür) veya Kullanıcı adı/parola kullanmak istiyorsanız, uygulamanızın kendi kiracınızda (LOB Geliştirici) veya Azure Active Directory kuruluşları 'nda (ISV senaryosu) Kullanıcı oturum açması gerekir. Bu kimlik doğrulama akışları, Microsoft kişisel hesapları için desteklenmez
- Sosyal kimlik ve ilke geçirimli kullanıcılar oturum açarsanız, yalnızca etkileşimli ve Kullanıcı adı-parola kimlik doğrulamasını kullanabilirsiniz. Kullanıcı adı-parola Şu anda yalnızca Xamarin. iOS, Xamarin. Android ve UWP üzerinde destekleniyor.

Büyük resim için bkz. [senaryolar ve desteklenen kimlik doğrulaması akışları](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) ve [senaryolar ve desteklenen platformlar ve diller](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>Platform yapılandırma ve yeniden yönlendirme URI 'Leri  

### <a name="interactive-authentication"></a>Etkileşimli kimlik doğrulaması

Etkileşimli kimlik doğrulaması kullanarak bir mobil uygulama oluştururken, en kritik kayıt adımı yeniden yönlendirme URI 'sidir. Bu, [kimlik doğrulama dikey penceresindeki platform yapılandırması](https://aka.ms/MobileAppReg)aracılığıyla ayarlanabilir.

Bu deneyim, uygulamanızın Microsoft Authenticator (ve Android 'de Intune Şirket Portalı) aracılığıyla çoklu oturum açma (SSO) ve cihaz yönetim ilkelerini destekleme olanağı sağlar.

İOS ve Android uygulamaları için aracılı yanıt URI 'sini hesaplamanıza yardımcı olmak üzere uygulama kayıt portalında bir önizleme deneyimi olduğunu unutmayın:

1. Uygulama kaydı ' nda, **kimlik doğrulama** ve seçim ' i seçin
   **yeni deneyim**![görüntüsünü deneyin](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. 
   **platform**![görüntü Ekle ' yi seçin](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Platformların listesi desteklenmiş olduğunda **iOS**
   ![görüntüsü ' nü seçin](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Paket KIMLIĞINIZI istenen şekilde girin ve ardından
   ![görüntüsüne **Kaydet** ' e basın](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. Yeniden yönlendirme URI 'SI sizin için hesaplanır.
   ![resim](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Yeniden yönlendirme URI 'sini el ile yapılandırmayı tercih ediyorsanız, bunu uygulama bildirimi aracılığıyla yapabilirsiniz. Önerilen biçim aşağıda verilmiştir:

- ***iOS***: `msauth.<BUNDLE_ID>://auth` (örneğin, "msauth. com. yourcompany. AppName:/Auth")
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Android imza karması, anahtar aracı komutu aracılığıyla sürüm veya hata ayıklama anahtarları kullanılarak oluşturulabilir.

### <a name="username-password"></a>Kullanıcı adı parolası

Uygulamanız yalnızca Kullanıcı adı/parola kullanıyorsa, uygulamanız için bir yeniden yönlendirme URI 'SI kaydetmeniz gerekmez. Gerçekten, bu akış Microsoft Identity platform v 2.0 uç noktasına gidiş dönüş yapar ve uygulamanız belirli bir URI üzerinde geri çağrılmayacaktır. Ancak, uygulamanızın ortak bir istemci uygulaması olduğunu ifade etmeniz gerekir. Bu yapılandırma, uygulamanızın **kimlik doğrulama** bölümüne giderek elde edilir ve **Gelişmiş ayarlar** alt bölümünde **Evet**' i seçin. Bu, soruya **ortak bir istemci olarak davran** ( **varsayılan istemci türü** paragraf)

## <a name="api-permissions"></a>API izinleri

Mobil uygulamalar, oturum açmış kullanıcı adına API 'Leri çağırır. Uygulamanızın, kapsam olarak da adlandırılan, temsilci izinleri istemesi gerekir. İstenen deneyime bağlı olarak, bu Azure portal statik olarak veya çalışma zamanında dinamik olarak yapılabilir. Statik olarak kayıt izinleri, yöneticilerin uygulamanızı kolayca onaylamasını sağlar ve önerilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kod yapılandırması](scenario-mobile-app-configuration.md)
