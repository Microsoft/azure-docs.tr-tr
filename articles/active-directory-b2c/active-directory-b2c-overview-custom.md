---
title: Özel ilkeleri Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C özel ilkeler hakkında bilgi edinin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 335b6c1a12f3786d7c0f1083f5b052aaac4beccb
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065785"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C özel ilkeler

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Özel ilkeler, Azure Active Directory B2C (Azure AD B2C) kiracınızın davranışını tanımlayan yapılandırma dosyalarıdır. Kullanıcı akışları, en yaygın kimlik görevlerinin Azure AD B2C portalında önceden tanımlanmıştır. Özel ilkeler, bir kimlik geliştiricisi tarafından birçok farklı görevi tamamlayabilecek şekilde tamamen düzenlenebilir.

## <a name="comparing-user-flows-and-custom-policies"></a>Kullanıcı akışlarını ve özel ilkeleri karşılaştırma

| | Kullanıcı akışları | Özel ilkeler |
|-|-------------------|-----------------|
| Hedef kullanıcılar | Kimlik uzmanlığına sahip veya olmayan tüm uygulama geliştiricileri. | Kimlik uzmanları, sistem tümleştiricileri, danışmanları ve şirket içi kimlik takımları. OpenID Connect akışlarıyla rahat ve kimlik sağlayıcılarını ve talep tabanlı kimlik doğrulamasını anlayın. |
| Yapılandırma yöntemi | Kullanıcı dostu Kullanıcı arabirimi (UI) ile Azure portal. | XML dosyalarını doğrudan düzenleyip Azure portal karşıya yükleyin. |
| UI özelleştirmesi | HTML, CSS ve JavaScript gibi tam UI özelleştirmesi.<br><br>Özel dizelerle çok dilli destek. | Naklettiğiniz |
| Öznitelik özelleştirmesi | Standart ve özel öznitelikler. | Naklettiğiniz |
| Belirteç ve oturum yönetimi | Özel belirteç ve çoklu oturum seçenekleri. | Naklettiğiniz |
| Kimlik Sağlayıcıları | Önceden tanımlanmış yerel veya sosyal sağlayıcı ve Azure Active Directory kiracılar ile Federasyon gibi çoğu OıDC kimlik sağlayıcısı. | Standartlara dayalı OıDC, OAUTH ve SAML.  REST API 'lerle tümleştirme kullanılarak da kimlik doğrulaması mümkündür. |
| Kimlik görevleri | Yerel veya çok sayıda sosyal hesapla kaydolma veya oturum açma.<br><br>Self servis parola sıfırlama.<br><br>Profil düzenleme.<br><br>Multi-Factor Authentication.<br><br>Belirteçleri ve oturumları özelleştirin.<br><br>Belirteç akışlarına erişin. | Özel kimlik sağlayıcılarını kullanarak Kullanıcı akışlarıyla aynı görevleri tamamlayıp özel kapsamlar kullanın.<br><br>Kayıt sırasında başka bir sistemde bir kullanıcı hesabı sağlayın.<br><br>Kendi e-posta hizmet sağlayıcınızı kullanarak bir hoş geldiniz e-postası gönderin.<br><br>Azure AD B2C dışında bir kullanıcı deposu kullanın.<br><br>Bir API kullanarak güvenilir bir sistemle Kullanıcı tarafından sağlanmış bilgileri doğrulayın. |

## <a name="policy-files"></a>İlke dosyaları

Bu üç tür ilke dosyası kullanılır:

- **Temel dosya** -tanımların çoğunu içerir. Sorun gidermenize yardımcı olması için bu dosyada en az sayıda değişiklik yapmanız ve ilkeleriniz için uzun süreli bakım yapmanız önerilir.
- **Uzantılar dosyası** -kiracınız için benzersiz yapılandırma değişiklikleri tutar.
- **Bağlı olan taraf (RP) dosyası** -uygulama veya hizmet tarafından doğrudan çağrılan tek görev odaklı dosya (Ayrıca, bağlı olan taraf olarak da bilinir). Her benzersiz görev kendi RP gerektirir ve marka gereksinimlerine bağlı olarak, sayı "Toplam uygulama sayısı x Toplam kullanım örneği sayısı" olabilir.

Azure AD B2C Kullanıcı akışları yukarıda gösterilen üç dosya modelini izler, ancak geliştirici RP dosyasını yalnızca, Azure portal arka planda uzantılar dosyasına değiştirir.

## <a name="custom-policy-core-concepts"></a>Özel ilke temel kavramları

Azure 'daki müşteri kimliği ve erişim yönetimi (CıHAR) hizmeti şunları içerir:

- Microsoft Graph kullanılarak erişilebilen ve hem yerel hesaplar hem de Federasyon hesapları için Kullanıcı verilerini tutan bir kullanıcı dizini.
- Kullanıcılar ve varlıklar arasında güveni düzenleyen ve kimlik veya erişim yönetimi görevini tamamlayan talepler arasındaki talepleri ileten **kimlik deneyimi çerçevesine** erişin.
- KIMLIK belirteçleri veren bir güvenlik belirteci hizmeti (STS), belirteçleri yeniler ve belirteçleri (ve eşdeğer SAML onayları) ve kaynakları koruyacak şekilde doğrular.

Kimlik sağlayıcıları, kullanıcılar, diğer sistemlerle ve bir kimlik görevi elde etmek için sırasıyla Yerel Kullanıcı diziniyle etkileşime Azure AD B2C. Örneğin, bir kullanıcı için oturum açın, yeni bir Kullanıcı kaydedin veya parolayı sıfırlayın. Kimlik deneyimi çerçevesi ve bir ilke (Kullanıcı yolculuğu veya güven çerçevesi ilkesi olarak da bilinir), çok taraflara sahip güven oluşturur ve açıkça aktörleri, eylemleri, protokolleri ve tamamlanacak adımların sırasını tanımlar.

Kimlik deneyimi çerçevesi, OpenID Connect, OAuth, SAML ve standart olmayan birkaç standart protokol biçiminde varlıklar arasında güveni düzenleyen, örneğin REST gibi standart bir yapılandırılabilir, ilke odaklı, bulut tabanlı bir Azure platformudur. API tabanlı sistemden sisteme talep değişimler. Framework, HTML ve CSS 'yi destekleyen, Kullanıcı dostu, beyaz etiketli deneyimler oluşturur.

Özel ilke bir veya hiyerarşi zincirinde birbirine başvuran daha fazla XML biçimindeki dosyadan oluşabilir. XML öğeleri, diğer öğelerin yanı sıra talep şemasını, talep dönüştürmelerini, içerik tanımlarını, talep sağlayıcılarını, teknik profilleri ve Kullanıcı yolculuğu düzenleme adımlarını tanımlar. Özel bir ilke, bağlı olan taraf tarafından çağrıldığında kimlik deneyimi çerçevesi tarafından yürütülen bir veya birkaç XML dosyası olarak erişilebilir. Özel ilkeleri yapılandıran geliştiriciler, her bir kimlik sağlayıcısı için gerektiğinde meta veri uç noktalarını, tam talep istekleri Exchange tanımlarını ve parolaları, anahtarları ve sertifikaları yapılandırmak için güvenilir ilişkileri dikkatle tanımlamalıdır.

### <a name="inheritance-model"></a>Devralma modeli

Bir uygulama RP ilke dosyasını çağırdığında, Azure AD B2C ' deki kimlik deneyimi çerçevesi temel dosyadaki tüm öğeleri uzantı dosyasından, sonra da geçerli ilkeyi uygulamak için RP ilke dosyasından ekler.  RP dosyasındaki aynı tür ve ad öğeleri Uzantılardaki geçersiz kılınır ve uzantılar temeli geçersiz kılar.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel ilkeleri kullanmaya başlama](active-directory-b2c-get-started-custom.md)
