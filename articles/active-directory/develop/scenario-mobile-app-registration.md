---
title: Web API 'Lerini çağıran mobil uygulamaları kaydetme | Mavisi
titleSuffix: Microsoft identity platform
description: Web API 'Leri (uygulamanın kaydı) çağıran bir mobil uygulama oluşturmayı öğrenin
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 6385f03556d155941139b77333d6f4a25081fe67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103167"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Web API 'Lerini çağıran mobil uygulamaları kaydetme

Bu makalede, oluşturmakta olduğunuz bir mobil uygulamayı kaydetmenize yardımcı olacak yönergeler yer almaktadır.

## <a name="supported-account-types"></a>Desteklenen hesap türleri

Mobil uygulamalarınızın desteklediği hesap türleri, etkinleştirmek istediğiniz deneyime ve kullanmak istediğiniz akışlara bağlıdır.

### <a name="audience-for-interactive-token-acquisition"></a>Etkileşimli belirteç alımı için hedef kitle

Çoğu mobil uygulama etkileşimli kimlik doğrulaması kullanır. Uygulamanız bu kimlik doğrulama biçimini kullanıyorsa, kullanıcıların herhangi bir [Hesap türünden](quickstart-register-app.md)oturum açmasını sağlayabilirsiniz.

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Tümleşik Windows kimlik doğrulaması, Kullanıcı adı-parola ve B2C için hedef kitle

Evrensel Windows Platformu (UWP) uygulamanız varsa, kullanıcıların oturum açması için tümleşik Windows kimlik doğrulaması kullanabilirsiniz. Tümleşik Windows kimlik doğrulaması veya Kullanıcı adı-parola kimlik doğrulamasını kullanmak için, uygulamanızın kendi iş kolu (LOB) geliştirici kiracınızdaki kullanıcılar oturum açması gerekir. Bağımsız bir yazılım satıcısı (ISV) senaryosunda, uygulamanız Azure Active Directory kuruluşlardaki kullanıcıları oturum açabilir. Bu kimlik doğrulama akışları, Microsoft kişisel hesapları için desteklenmez.

Ayrıca, B2C yetkilisini ve ilkesini geçiren sosyal kimlikleri kullanarak kullanıcılara oturum açabilirsiniz. Bu yöntemi kullanmak için yalnızca etkileşimli kimlik doğrulaması ve Kullanıcı adı-parola kimlik doğrulaması kullanabilirsiniz. Kullanıcı adı-parola kimlik doğrulaması şu anda yalnızca Xamarin. iOS, Xamarin. Android ve UWP üzerinde desteklenmektedir.

Daha fazla bilgi için bkz. [senaryolar ve desteklenen kimlik doğrulaması akışları](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) ve [senaryolar ve desteklenen platformlar ve diller](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages).

## <a name="platform-configuration-and-redirect-uris"></a>Platform yapılandırma ve yeniden yönlendirme URI 'Leri

### <a name="interactive-authentication"></a>Etkileşimli kimlik doğrulaması

Etkileşimli kimlik doğrulaması kullanan bir mobil uygulama yapılandırdığınızda, en kritik kayıt adımı yeniden yönlendirme URI 'sidir. Etkileşimli kimlik doğrulamasını, [ **kimlik doğrulama** dikey penceresinde platform yapılandırması](https://aka.ms/MobileAppReg)aracılığıyla ayarlayabilirsiniz.

Bu deneyim, uygulamanızın Microsoft Authenticator (ve Android 'de Intune Şirket Portalı) aracılığıyla çoklu oturum açma (SSO) almasını sağlar. Ayrıca cihaz yönetim ilkelerini de destekleyecektir.

Uygulama kayıt portalı, iOS ve Android uygulamaları için aracılı yanıt URI 'sini hesaplamanıza yardımcı olmak için bir önizleme deneyimi sağlar:

1. Uygulama kayıt portalında **kimlik doğrulama**' yı seçin,  >  **Yeni deneyimi deneyin**.

   ![Yeni bir deneyim seçtiğiniz kimlik doğrulama dikey penceresi](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. **Platform Ekle**' yi seçin.

   ![Platform ekleme](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Platformların listesi desteklenmiş olduğunda **iOS**' ı seçin.

   ![Bir mobil uygulama seçin](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Paket KIMLIĞINIZI girip **Kaydet**' i seçin.

   ![Paket KIMLIĞINIZI girin](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Adımları tamamladığınızda, aşağıdaki görüntüde olduğu gibi yeniden yönlendirme URI 'SI sizin için hesaplanır.

![Elde edilen yeniden yönlendirme URI 'SI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Yeniden yönlendirme URI 'sini el ile yapılandırmayı tercih ediyorsanız, bunu uygulama bildirimi aracılığıyla yapabilirsiniz. Bildirim için önerilen biçim aşağıda verilmiştir:

- **iOS**: `msauth.<BUNDLE_ID>://auth`
  - Örneğin, şunu girin `msauth.com.yourcompany.appName://auth`
- **Android**: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Anahtar araç komutu aracılığıyla sürüm anahtarını veya hata ayıklama anahtarını kullanarak Android imza karmasını oluşturabilirsiniz.

### <a name="username-password-authentication"></a>Kullanıcı adı-parola kimlik doğrulaması

Uygulamanız yalnızca Kullanıcı adı-parola kimlik doğrulaması kullanıyorsa, uygulamanız için bir yeniden yönlendirme URI 'SI kaydetmeniz gerekmez. Bu akış, Microsoft Identity platformu 'na gidiş dönüş yapar. Uygulamanız belirli bir URI üzerinde geri çağrılmayacaktır.

Ancak, uygulamanızı ortak bir istemci uygulaması olarak belirleyebilirsiniz. Bunun için:

1. Hala <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>, uygulamanızı **uygulama kayıtları** ve ardından **kimlik doğrulaması**' nı seçin.
1. **Gelişmiş ayarlar**' da  >  **ortak istemci akışlarının**  >  **aşağıdaki mobil ve Masaüstü akışlarını etkinleştirmesine** izin ver:, **Evet**' i seçin.

   :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Azure portal 'de kimlik doğrulama bölmesinde ortak istemci ayarını etkinleştir":::

## <a name="api-permissions"></a>API izinleri

Mobil uygulamalar, oturum açmış kullanıcı adına API 'Leri çağırır. Uygulamanızın temsilci izinleri istemesi gerekiyor. Bu izinler Ayrıca kapsamlar olarak adlandırılır. İstediğiniz deneyime bağlı olarak, Azure portal aracılığıyla statik olarak temsilci izinleri isteyebilirsiniz. Ya da çalışma zamanında dinamik olarak istek yapabilirsiniz.

İzinleri statik olarak kaydederek, yöneticilerin uygulamanızı kolayca onaylamasını sağlayabilirsiniz. Statik kayıt önerilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryonun [uygulama kodu yapılandırması](scenario-mobile-app-configuration.md)olan bir sonraki makaleye geçin.
