---
title: HYPR ile Azure Active Directory B2C yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Doğru passworddaha az güçlü müşteri kimlik doğrulaması için Hypr ile Azure Active Directory B2C yapılandırma öğreticisi
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 357c8d79259986e211dd04580b1d8205c25824da
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257985"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>Azure Active Directory B2C ile HYPR yapılandırma öğreticisi

Bu örnek öğreticide, [HYPR](https://get.hypr.com)ile Azure AD B2C yapılandırma hakkında rehberlik sağlıyoruz. Azure AD B2C bir kimlik sağlayıcısı olarak, kullanıcılarınıza doğru parolasız kimlik doğrulaması sağlamak için HYPR 'i müşteri uygulamalarınızla tümleştirebileceğinizi sağlayabilirsiniz. HYPR, parolaların, kimlik avı ve kimlik bilgilerinin yeniden kullanımını ortadan kaldıran ortak anahtar şifrelemeleri olan parolaları değiştirir.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

- Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

- [Azure AD B2C kiracısı](./tutorial-create-tenant.md). Kiracı, Azure aboneliğinize bağlı.

- Bir HYPR bulut kiracısı, ücretsiz bir [deneme hesabı](https://get.hypr.com/free-trial)alın.

- HYPR REST API 'Leri veya HYPR kiracınızdaki HYPR Device Manager kullanılarak kaydedilmiş bir kullanıcının mobil cihazı. Örneğin, bu görevi gerçekleştirmek için [HYPR Java SDK 'sını](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) kullanabilirsiniz.

## <a name="scenario-description"></a>Senaryo açıklaması

HYRP tümleştirmesi aşağıdaki bileşenleri içerir:

- Azure AD B2C – kimlik sağlayıcısı olarak da bilinen kullanıcının kimlik bilgilerini doğrulamadan sorumlu yetkilendirme sunucusu

- Web ve mobil uygulamalar-HYPR ve Azure AD B2C ile korumayı seçtiğiniz mobil veya Web uygulamalarınız. HYPR, Ayrıca iOS ve Android platformlarında, doğru passwordless kimlik doğrulaması yapmak için kullanabileceğiniz bir mobil uygulama sağlar.

- HYPR Mobile App-HYPR mobil uygulaması, mobil SDK 'Ları kendi mobil uygulamalarınızda kullanmayı tercih ediyorsanız bu örneği yürütmek için kullanılabilir.

- HYPR REST API 'Leri-Kullanıcı cihaz kaydı ve kimlik doğrulaması yapmak için HYPR API 'Lerini kullanabilirsiniz. Bu API 'Ler [burada](https://apidocs.hypr.com)bulunabilir.

Aşağıdaki mimari diyagram uygulamayı gösterir.

![Hypr mimarisi için ekran görüntüsü-diyagram](media/partner-hypr/hypr-architecture-diagram.png)

|Adım | Description |
|:-----| :-----------|
| 1. | Kullanıcı bir oturum açma sayfasına ulaştı. Kullanıcılar oturum açma/kaydolma ' yı seçip sayfaya Kullanıcı adı girin.
| 2. | Uygulama, doğrulama tanımlamak için Kullanıcı özniteliklerini Azure AD B2C gönderir.
| 3. | Azure AD B2C, Kullanıcı özniteliklerini toplar ve HYPR 'e, HYPR mobil uygulaması aracılığıyla kullanıcının kimliğini doğrulamak için öznitelikleri gönderir.
| 4. | HYPR, kayıtlı Kullanıcı mobil cihazına hızlı kimlik çevrimiçi (FIDO) sertifikalı kimlik doğrulaması için bir anında iletme bildirimi gönderir. Kullanıcı parmak izi, Biyometri veya merkezi olmayan PIN olabilir.  
| 5. | Kullanıcı, anında iletme bildirimini kabul ettikten sonra, doğrulama sonuçlarına göre müşteri uygulamasına erişim izni verilir veya reddedilir.

## <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C ilkesini yapılandırma

1. Ilke klasöründeki [Azure AD B2C HYPR ilkesine](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy) gidin.

2. [LocalAccounts başlangıç paketini](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) indirmek için bu [belgeyi](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) izleyin

3. Azure AD B2C kiracı için ilkeyi yapılandırın.

>[!NOTE]
>Belirli kiracınızla ilgili olarak belirtilen ilkeleri güncelleştirin.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Azure AD B2C kiracıyı açın ve Ilkeler altında **kimlik deneyimi çerçevesi**' ni seçin.

2. Daha önce oluşturduğunuz **Signupsignın**' ı seçin.

3. **Kullanıcı akışını Çalıştır** ' ı seçin ve ayarları seçin:

   a. **Uygulama**: kayıtlı uygulamayı seçin (örnek JWT)

   b. **Yanıt URL 'si**: **yeniden yönlendirme URL** 'sini seçin

   c. **Kullanıcı akışını Çalıştır**' ı seçin.

4. Kaydolma akışına gidin ve hesap oluşturun

5. HYPR, kullanıcı özniteliği oluşturulduktan sonra akış sırasında çağrılacaktır. Akış tamamlanmamışsa, kullanıcının dizine kaydedilip kaydedilmediğini kontrol edin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)