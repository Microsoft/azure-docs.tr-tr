---
title: Azure Active Directory B2C ile Keyless yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Passwordless kimlik doğrulaması için Azure Active Directory B2C ile Keyless yapılandırma öğreticisi
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 006bcc6c0cffe6f7c08b16057325a499e44f06be
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257917"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>Öğretici: Azure Active Directory B2C ile Keyless yapılandırma

Bu örnek öğreticide, [Keyless](https://keyless.io/)ile Azure ACTIVE DIRECTORY (ad) B2C 'yi yapılandırma hakkında rehberlik sağlıyoruz. Azure AD B2C, bir kimlik sağlayıcısı olarak, kullanıcılarınıza doğru parolasız kimlik doğrulama sağlamak için herhangi bir müşteri uygulamalarınızla Keyless tümleştirebilirsiniz.

Keyless 'in çözüm **keyless Zero-Knowledge Biyometri (ZKB™)** , müşteri deneyimini geliştirerek ve gizliliğini koruyarak, sahtekarlık, kimlik avı ve kimlik bilgisi yeniden kullanımını ortadan kaldıran, passwordless çok faktörlü kimlik doğrulaması sağlar.

## <a name="pre-requisites"></a>Ön koşullar

Başlamak için şunlar gerekir:

- Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

- [Azure AD B2C kiracısı](./tutorial-create-tenant.md). Kiracının Azure aboneliğinize bağlı olması gerekir.

- Keyless bir bulut kiracısı, ücretsiz bir [deneme hesabı](https://keyless.io/go)alın.

- Kullanıcının cihazında yüklü olan Keyless Authenticator uygulaması.

## <a name="scenario-description"></a>Senaryo açıklaması

Keyless tümleştirmesi aşağıdaki bileşenleri içerir:

- Azure AD B2C – kimlik sağlayıcısı olarak da bilinen kullanıcının kimlik bilgilerini doğrulamadan sorumlu yetkilendirme sunucusu.

- Web ve mobil uygulamalar: Keyless ve Azure AD B2C ile korumayı seçtiğiniz mobil veya Web uygulamalarınız.

- Keyless mobil uygulama: daha seyrek erişimli mobil uygulama Azure AD B2C etkinleştirilmiş uygulamalarda kimlik doğrulaması için kullanılacaktır.

Aşağıdaki mimari diyagram uygulamayı gösterir.

![Görüntüde Keyless mimari diyagramı gösteriliyor](./media/partner-keyless/keyless-architecture-diagram.png)

|Adım | Description |
|:-----| :-----------|
| 1. | Kullanıcı bir oturum açma sayfasına ulaştı. Kullanıcılar oturum açma/kaydolma seçer ve Kullanıcı adını girer
| 2. | Uygulama, kimlik doğrulaması için Kullanıcı özniteliklerini Azure AD B2C gönderir.
| 3. | Azure AD B2C Kullanıcı özniteliklerini toplar ve Keyless mobil uygulama aracılığıyla kullanıcının kimliğini doğrulamak için öznitelikleri Keyless 'a gönderir.
| 4. | Keyless, bir yüz Biyometrik tarama biçiminde bir gizlilik-koruma kimlik doğrulaması için kayıtlı kullanıcının mobil cihazına anında iletme bildirimi gönderir.
| 5. | Kullanıcı, anında iletme bildirimine yanıt verdikten sonra, doğrulama sonuçlarına göre müşteri uygulamasına erişim izni verilir veya reddedilir.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C ile tümleştirme

### <a name="add-a-new-identity-provider"></a>Yeni bir kimlik sağlayıcısı ekleyin

Yeni bir kimlik sağlayıcısı eklemek için aşağıdaki adımları izleyin:

1. **[Azure portal](https://portal.azure.com/#home)** Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.

2. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.

3. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.

4. **Pano**  >  **Azure Active Directory B2C**  >   **kimlik sağlayıcılarına** git

5. **Kimlik sağlayıcıları**' nı seçin.

6. **Add (Ekle)** seçeneğini belirleyin.

### <a name="configure-an-identity-provider"></a>Kimlik sağlayıcısı yapılandırma

Bir kimlik sağlayıcısını yapılandırmak için aşağıdaki adımları izleyin:

1. **Kimlik sağlayıcısı türünü** seçin  >  **OpenID Connect (Önizleme)**
2. Kimlik sağlayıcısını ayarlamak için formu doldurun:

   |Özellik | Değer |
   |:-----| :-----------|
   | Ad   | Anahtarsız |
   | Meta veri URL 'SI | Barındırılan Keyless kimlik doğrulama uygulamasının URI 'sini, ardından ' ' gibi belirli bir yolu ekleyin https://keyless.auth/.well-known/openid-configuration |
   | İstemci Gizli Anahtarı | Keyless kimlik doğrulama örneğiyle ilişkili gizli dizi, daha önce yapılandırılmış bir ile aynı değildir. İstediğiniz karmaşık bir dize ekleyin. Bu gizli anahtar daha sonra Keyless kapsayıcı yapılandırmasında kullanılacaktır.|
   | İstemci Kimliği | İstemcinin KIMLIĞI. Bu KIMLIK daha sonra Keyless kapsayıcı yapılandırmasında kullanılacaktır.|
   | Kapsam | OpenID |
   | Yanıt türü | id_token |
   | Yanıt modu | form_post|

3. **Tamam**’ı seçin.

4. **Bu kimlik sağlayıcısının taleplerini eşle**' yi seçin.

5. Kimlik sağlayıcısını eşlemek için formu doldurun:

   |Özellik | Değer |
   |:-----| :-----------|
   | UserID    | Abonelikten |
   | Görünen ad | Abonelikten |
   | Yanıt modu | Abonelikten |

6. Yeni açık KIMLIK Connect (OıDC) kimlik sağlayıcınızın kurulumunu gerçekleştirmek için **Kaydet** ' i seçin.

### <a name="create-a-user-flow-policy"></a>Kullanıcı akış ilkesi oluşturma

Artık B2C kimlik sağlayıcılarınızda listelenen yeni bir OıDC kimlik sağlayıcısı olarak Keyless görmeniz gerekir.

1. Azure AD B2C kiracınızda **ilkeler** altında **Kullanıcı akışları**' nı seçin.

2. **Yeni** Kullanıcı akışı ' nı seçin.

3. **Kaydolun ve oturum açın**' ı seçin, bir **Sürüm** seçin ve ardından **Oluştur**' u seçin.

4. İlkeniz için bir **ad** girin.

5. Kimlik sağlayıcıları bölümünde yeni oluşturduğunuz Keyless kimlik sağlayıcınızı seçin.

6. Kullanıcı akışlarınızın parametrelerini ayarlayın. Bir ad ekleyin ve oluşturduğunuz kimlik sağlayıcısını seçin. E-posta adresi de ekleyebilirsiniz. Bu durumda, Azure oturum açma yordamını doğrudan Keyless 'e yönlendirmez, bunun yerine kullanıcının kullanmak istedikleri seçeneği seçebileceği bir ekran görüntülenir.

7. **Multi-Factor Authentication** alanını olduğu gibi bırakın.

8. **Koşullu erişim Ilkelerini zorla** ' yı seçin

9. **Kullanıcı öznitelikleri ve belirteç talepleri** altında, topla özniteliği seçeneğinde **e-posta adresi** ' ni seçin. Azure Active Directory, Kullanıcı hakkında Azure AD B2C istemci uygulamasına döndürebileceği taleplerle birlikte toplayabileceği tüm öznitelikleri ekleyebilirsiniz.

10. **Oluştur**’u seçin.

11. Başarılı bir oluşturma işleminden sonra yeni **Kullanıcı akışınızı** seçin.

12. Sol panelde **uygulama talepleri**' ı seçin. Seçenekler altında, **e-posta** onay kutusunu Işaretleyin ve **Kaydet**' i seçin.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Azure AD B2C kiracıyı açın ve Ilkeler altında kimlik deneyimi çerçevesi ' ni seçin.

2. Daha önce oluşturduğunuz Signupsignın ' ı seçin.

3. Kullanıcı akışını Çalıştır ' ı seçin ve ayarları seçin:

   a. Uygulama: kayıtlı uygulamayı seçin (örnek JWT)

   b. Yanıt URL 'SI: yeniden yönlendirme URL 'sini seçin

   c. Kullanıcı akışını Çalıştır ' ı seçin.

4. Kaydolma akışına gidin ve hesap oluşturun

5. Kullanıcı özniteliği oluşturulduktan sonra akış sırasında Keyless çağrılacaktır. Akış tamamlanmamışsa, kullanıcının dizine kaydedilip kaydedilmediğini kontrol edin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)