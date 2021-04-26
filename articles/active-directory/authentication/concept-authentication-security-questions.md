---
title: Güvenlik soruları kimlik doğrulama yöntemi-Azure Active Directory
description: Oturum açma olaylarını geliştirmek ve güvenli hale getirmek için Azure Active Directory güvenlik sorularını kullanma hakkında bilgi edinin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 841391778e0fb8c00f503aa0cc79b5562661e309
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530355"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Azure Active Directory güvenlik sorularında kimlik doğrulama yöntemleri

Güvenlik soruları, oturum açma olayında kimlik doğrulama yöntemi olarak kullanılmaz. Bunun yerine, güvenlik soruları, kim olduğunu onaylamak için self servis parola sıfırlama (SSPR) işlemi sırasında kullanılabilir. Yönetici hesapları, SSPR ile doğrulama yöntemi olarak güvenlik sorularını kullanamaz.

Kullanıcılar SSPR 'ye kaydolduklarında, kullanılacak kimlik doğrulama yöntemlerini seçmeleri istenir. Güvenlik sorularını kullanmayı tercih ederseniz, kendi yanıtlarını isteyecek ve sonra kendi yanıtlarını sağlamaları için bir soru kümesinden seçim yapılır.

![Güvenlik soruları için kimlik doğrulama yöntemlerini ve seçeneklerini gösteren Azure portal ekran görüntüsü](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Güvenlik soruları, dizindeki bir kullanıcı nesnesi üzerinde özel olarak ve güvenli bir şekilde depolanır ve yalnızca kayıt sırasında kullanıcılar tarafından yanıtlanabilecek. Bir yöneticinin bir kullanıcının sorularını veya yanıtlarını okuması veya değiştirmesi için bir yol yoktur.

Bazı kullanıcılar başka bir Kullanıcı sorusunun yanıtlarını bilebileceğinden, güvenlik soruları diğer yöntemlerden daha az güvenli olabilir. SSPR ile güvenlik soruları kullanıyorsanız, bunları başka bir yöntemle birlikte kullanmanız önerilir. Bir kullanıcıdan SSPR işlemi sırasında kimliklerini doğrulamak için Microsoft Authenticator uygulama veya telefon kimlik doğrulamasını kullanması istenebilir ve yalnızca telefon veya kayıtlı cihazları yoksa güvenlik soruları ' nı seçin.

## <a name="predefined-questions"></a>Önceden tanımlanmış sorular

Aşağıdaki önceden tanımlanmış güvenlik soruları, SSPR ile doğrulama yöntemi olarak kullanılabilir. Bu güvenlik sorularının tümü, kullanıcının tarayıcı yerel ayarlarına bağlı olarak Microsoft 365 dillerin tam kümesine çevrilir ve yerelleştirilir:

* İlk eşiniz/iş ortağınızı hangi şehirle karşıladınız?
* Ebeveynleriniz hangi şehirle buluşmış?
* En yakın eşdüzey ağınız hangi şehirde etkin?
* Babalar hangi şehirde doğdu?
* İlk işiniz hangi şehirdir?
* Anneniz hangi şehirde doğdu?
* Yeni yıl 2000 ' de hangi şehirdeydiniz?
* Yüksek okulda en sevdiğiniz öğretmenin soyadı nedir?
* Uyguladığınız bir üniversite adı nedir, ancak katılmadınız mı?
* İlk evlilik alıyeninizi dağıttığınız yerin adı nedir?
* Babalar ikinci adı nedir?
* En sevdiğiniz yemek nedir?
* Anneannenizin en Annenizin adı ve soyadı nedir?
* Annenizin ikinci adı nedir?
* En eski eşdüzey Doğum günün ayı ve yılı nedir? (örneğin, Kasım 1985)
* En eski eşdüzey öğenin ikinci adı nedir?
* Babanızın babalar adı ve soyadı nedir?
* Kardeşinizin eşdüzey ortaınızın ikinci adı nedir?
* Altıncı sınıf için hangi okula katıldınız?
* Çocukluğunuzdaki en iyi arkadaşınızın adı ve soyadı neydi?
* İlk önemli olan ilk ve soyadı neydi?
* En sevdiğiniz sınıf okul öğretmeninizin soyadı neydi?
* İlk arabalarınızın veya otodöngünüzün marka ve modeli neydi?
* Katıldığınız ilk okulunuzun adı neydi?
* Doğmakta olduğunuz hastanın adı neydi?
* İlk çocukluk evin sokak adı neydi?
* Çocukluğunuzdaki kahraman 'ın adı neydi?
* En sevdiğiniz peluş ya da bir hayvan adı neydi?
* İlk evcil hayvanınızın adı neydi?
* Çocukluk takma ad nedir?
* Yüksek okulda en sevdiğiniz spor nedir?
* İlk işiniz neydi?
* Çocukluk telefon numaranız için son dört basamak neydi?
* Küçükken, büyüyken ne yapmak istiyorsunuz?
* Şimdiye kadar tanıştığınız en ünlü kişi kim?

## <a name="custom-security-questions"></a>Özel güvenlik soruları

Ek esneklik için kendi özel güvenlik sorularınızı tanımlayabilirsiniz. Özel bir güvenlik sorusunun en fazla uzunluğu 200 karakterdir.

Özel güvenlik soruları, varsayılan güvenlik soruları gibi otomatik olarak yerelleştirilmez. Kullanıcının tarayıcı yerel ayarı farklı olsa bile, tüm özel sorular yönetim kullanıcı arabirimine girildikleri dilde görüntülenir. Yerelleştirilmiş sorulara ihtiyacınız varsa, önceden tanımlanmış soruları kullanmanız gerekir.

## <a name="security-question-requirements"></a>Güvenlik sorusu gereksinimleri

Hem varsayılan hem de özel güvenlik soruları için aşağıdaki gereksinimler ve sınırlamalar geçerlidir:

* En küçük yanıt karakter sınırı üç karakterdir.
* En fazla yanıt karakter sınırı 40 karakterdir.
* Kullanıcılar aynı sorudan birden çok kez yanıt vermez.
* Kullanıcılar, birden fazla soruya aynı yanıtı sağlayamaz.
* Herhangi bir karakter kümesi, Unicode karakterler de dahil olmak üzere soruları ve yanıtları tanımlamak için kullanılabilir.
* Tanımlanan soruların sayısı, kaydolmak için gereken soru sayısına eşit veya ondan büyük olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için, [self servis parola sıfırlama (SSPR) öğreticisine][tutorial-sspr]bakın.

SSPR kavramları hakkında daha fazla bilgi edinmek için bkz. [Azure AD self servis parola sıfırlamasının nasıl çalıştığı][concept-sspr].

[Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview)kullanarak kimlik doğrulama yöntemlerini yapılandırma hakkında daha fazla bilgi edinin.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
