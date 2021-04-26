---
title: Telefon kimlik doğrulama yöntemleri-Azure Active Directory
description: Oturum açma olaylarını geliştirmek ve güvenli hale getirmek için Azure Active Directory 'de telefon kimlik doğrulama yöntemlerini kullanma hakkında bilgi edinin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a5e8b933f617d767f017f73fb6778a45b5a1ce3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725598"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Azure Active Directory-telefon seçeneklerinde kimlik doğrulama yöntemleri

Metin iletisini kullanarak doğrudan kimlik doğrulama için, [SMS tabanlı kimlik doğrulaması için kullanıcıları yapılandırabilir ve etkinleştirebilirsiniz](howto-authentication-sms-signin.md). SMS tabanlı oturum açma, Frontline çalışanları için harika. SMS tabanlı oturum açma sayesinde, kullanıcıların uygulama ve hizmetlere erişmek için bir Kullanıcı adı ve parola bilmeleri gerekmez. Bunun yerine Kullanıcı kayıtlı cep telefonu numarasını girer, doğrulama kodu içeren bir kısa mesaj alır ve oturum açma arabirimine girer.

Kullanıcılar ayrıca, Azure AD Multi-Factor Authentication veya self servis parola sıfırlama (SSPR) sırasında kullanılan ikincil kimlik doğrulama biçimi olarak bir cep telefonu veya ofis telefonu kullanarak kendilerini doğrulayabilirler.

Doğru şekilde çalışmak için telefon numaralarının *+ CountryCode PhoneNumber* biçiminde olması gerekir, örneğin *+ 1 4251234567*.

> [!NOTE]
> Ülke/bölge kodu ve telefon numarası arasında bir boşluk olması gerekir.
>
> Parola sıfırlama, telefon uzantılarını desteklemez. *+ 1 4251234567X12345* biçiminde bile, çağrı yerleştirilmadan önce uzantılar kaldırılır.

## <a name="mobile-phone-verification"></a>Cep telefonu doğrulama

Azure AD Multi-Factor Authentication veya SSPR için, kullanıcılar oturum açma arabirimine girmek üzere doğrulama kodu içeren bir kısa mesaj almayı seçebilir veya bir telefon araması alabilir.

Kullanıcılar cep telefonu numarasının dizinde görünmesini istemiyor, ancak parola sıfırlama için kullanmak istiyorsanız, Yöneticiler dizindeki telefon numarasını doldurmamalıdır. Bunun yerine, kullanıcılar, **kimlik doğrulama telefonu** özniteliğini, Birleşik güvenlik bilgileri kaydı aracılığıyla doldurmalıdır [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . Yöneticiler bu bilgileri kullanıcının profilinde görebilir, ancak başka bir yerde yayımlanmaz.

:::image type="content" source="media/concept-authentication-methods/user-authentication-methods.png" alt-text="Bir telefon numarası doldurulmuş kimlik doğrulama yöntemlerini gösteren Azure portal ekran görüntüsü":::

Microsoft, tutarlı SMS veya ses tabanlı Azure AD Multi-Factor Authentication istemi teslimini aynı numarayla garanti etmez. Kullanıcılarımıza ilişkin olarak, SMS teslimat yeteneğini geliştirmek üzere rota ayarlamaları yaptığımız için istediğiniz zaman kısa kodlar ekleyebilir veya kaldırabiliriz. Microsoft, Birleşik Devletler ve Kanada yanı sıra ülkeler/bölgeler için kısa kodları desteklemez.

### <a name="text-message-verification"></a>Kısa mesaj doğrulaması

SSPR veya Azure AD Multi-Factor Authentication sırasında kısa mesaj doğrulaması ile bir SMS, bir doğrulama kodu içeren cep telefonu numarasına gönderilir. Oturum açma işlemini gerçekleştirmek için, girilen doğrulama kodu oturum açma arabirimine girilir.

### <a name="phone-call-verification"></a>Telefon araması doğrulaması

SSPR veya Azure AD Multi-Factor Authentication sırasında telefon araması doğrulaması ile, Kullanıcı tarafından kaydedilen telefon numarasına otomatik bir sesli çağrı yapılır. Oturum açma işlemini tamamlamaya yönelik olarak, kullanıcıdan kendi tuş takımında # ' a basması istenir.

## <a name="office-phone-verification"></a>Office telefon doğrulaması

SSPR veya Azure AD Multi-Factor Authentication sırasında telefon araması doğrulaması ile, Kullanıcı tarafından kaydedilen telefon numarasına otomatik bir sesli çağrı yapılır. Oturum açma işlemini tamamlamaya yönelik olarak, kullanıcıdan kendi tuş takımında # ' a basması istenir.

## <a name="troubleshooting-phone-options"></a>Telefon seçenekleriyle ilgili sorunları giderme

Azure AD için telefon kimlik doğrulamasıyla ilgili sorunlar yaşıyorsanız, aşağıdaki sorun giderme adımlarını gözden geçirin:

* "Doğrulama çağrılarında limitimize ulaştınız" veya "oturum açma sırasında" metin Doğrulama kodlarıyla sınırımız "hata iletileriyle karşılaştık
   * Microsoft, aynı kullanıcı tarafından kısa bir süre içinde yapılan yinelenen kimlik doğrulama girişimlerini sınırlayabilir. Bu sınırlama Microsoft Authenticator veya doğrulama kodu için geçerlidir. Bu sınırlara ulaşırsanız, doğrulayıcı uygulamasını, doğrulama kodunu kullanabilir veya birkaç dakika içinde yeniden oturum açmayı deneyebilirsiniz.
* "Üzgünüz, oturum açma sırasında hesabınızı doğrularken sorun yaşıyoruz" hata iletisi
   * Microsoft, yüksek sayıda başarısız sesli veya SMS kimlik doğrulama girişimi nedeniyle aynı kullanıcı, telefon numarası veya kuruluş tarafından gerçekleştirilen sesli veya SMS kimlik doğrulama girişimlerini sınırlayabilir veya engelleyebilir. Bu hatayla karşılaşıyorsanız, kimlik doğrulayıcı uygulaması veya doğrulama kodu gibi başka bir yöntemi deneyebilir veya destek için yöneticinize erişebilirsiniz.
* Tek bir cihazda engellenen arayan KIMLIĞI.
   * Cihazda yapılandırılan tüm engellenen numaraları gözden geçirin.
* Yanlış telefon numarası veya yanlış ülke/bölge kodu ya da kişisel telefon numarası ile iş telefonu numarası arasında karışıklık.
   * Kullanıcı nesnesi ve yapılandırılmış kimlik doğrulama yöntemleri sorunlarını giderin. Doğru telefon numaralarının kaydedildiğinden emin olun.
* Yanlış PIN girildi.
   * Kullanıcının hesabı için kayıtlı doğru PIN 'ı kullandığını onaylayın (yalnızca MFA sunucu kullanıcıları).
* Sesli mesaj ile iletilen çağrı.
   * Kullanıcının telefonu açık olduğundan ve bu hizmetin kendi alanında kullanılabilir olduğundan emin olun veya alternatif yöntemi kullanın.
* Kullanıcı engellendi
   * Azure portal kullanıcının engellemesini kaldırmak için bir Azure AD yöneticisi vardır.
* Cihazda SMS abone değil.
   * Kullanıcının bir yöntemi değiştirmesini veya cihazda SMS 'yi etkinleştirmesini sağlamak.
* Telefon girişi yok, eksik DTMF tonları sorunları, birden çok cihazda engellenen çağıran KIMLIĞI veya birden çok cihazda SMS tarafından engellenen hatalı Telekom sağlayıcıları.
   * Microsoft, kimlik doğrulaması için telefon çağrılarını ve SMS iletilerini yönlendirmek üzere birden çok Telekom sağlayıcısı kullanır Yukarıdaki sorunlardan herhangi birini görürseniz, bir kullanıcının yöntemini 5 dakika içinde en az beş kez kullanmayı denemesi ve Microsoft desteği ile iletişim kurulurken kullanıcının bilgilerinin kullanılabilir olması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için [self servis parola sıfırlama (SSPR)][tutorial-sspr] ve [Azure AD Multi-Factor Authentication][tutorial-azure-mfa]öğreticisine bakın.

SSPR kavramları hakkında daha fazla bilgi edinmek için bkz. [Azure AD self servis parola sıfırlamasının nasıl çalıştığı][concept-sspr].

MFA kavramları hakkında daha fazla bilgi edinmek için bkz. [Azure AD Multi-Factor Authentication nasıl çalıştığı][concept-mfa].

[Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)kullanarak kimlik doğrulama yöntemlerini yapılandırma hakkında daha fazla bilgi edinin.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
