---
title: Kimlik doğrulama yöntemleri etkinliği-Azure Active Directory
description: Kullanıcıların oturum açıp parolalarını sıfırlamasına yönelik kimlik doğrulama yöntemlerine genel bakış.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/16/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 699ff88e4181dada5eacaa3f13469722cdf7ceaa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530456"
---
# <a name="authentication-methods-activity"></a>Kimlik Doğrulama Yöntemleri Etkinliği 

Yeni kimlik doğrulama yöntemleri etkinlik panosu, yöneticilerin kuruluş genelinde kimlik doğrulama yöntemi kaydını ve kullanımını izlemesini sağlar. Bu raporlama özelliği, kuruluşunuzda hangi yöntemlerin kaydedildiğini ve bunların nasıl kullanıldığını anlamak için gereken yolları sağlar.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>İzinler ve lisanslar

Aşağıdaki izinlerle yerleşik ve özel roller, kimlik doğrulama yöntemleri etkinlik dikey penceresine ve API 'Lerine erişebilir:

- Microsoft. Directory/auditLogs/allProperties/Read
- Microsoft. Directory/Signınreports/allProperties/Read

Aşağıdaki roller gerekli izinlere sahiptir:

- Rapor okuyucu
- Güvenlik okuyucusu
- Genel okuyucu
- Güvenlik operatörü
- Güvenlik Yöneticisi
- Genel Yönetici

 Kullanım ve öngörülere erişmek için bir Azure AD Premium P1 veya P2 lisansı gerekir. Azure AD Multi-Factor Authentication ve self servis parola sıfırlama (SSPR) lisanslama bilgileri [Azure Active Directory fiyatlandırma sitesinde](https://azure.microsoft.com/pricing/details/active-directory/)bulunabilir.

## <a name="how-it-works"></a>Nasıl çalışır?

Kimlik doğrulama yöntemi kullanımı ve öngörülerine erişmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Azure Active Directory**  >  **güvenliği**  >  **kimlik doğrulama yöntemleri**  >  **etkinliği**' ne tıklayın.
1. Raporda iki sekme vardır: **kayıt** ve **kullanım**.

   ![Kimlik doğrulama yöntemleri etkinliğine genel bakış](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Kayıt ayrıntıları

Birden çok faktörlü kimlik doğrulaması, passowordless kimlik doğrulaması ve self servis parola sıfırlama özellikli Kullanıcı sayısını göstermek için [**Kayıt sekmesine**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) erişebilirsiniz. 

Kullanıcı kaydı ayrıntılarının listesini önceden filtrelemek için aşağıdaki seçeneklerden birine tıklayın:

- **Azure Multi-Factor Authentication özellikli kullanıcılar** , her ikisi de olan kullanıcıların dökümünü gösterir:
  - Güçlü bir kimlik doğrulama yöntemi için kayıtlı 
  - MFA için bu yöntemi kullanmak üzere ilke tarafından etkinleştirildi 
  
  Bu sayı, Azure AD dışında MFA için kaydedilmiş kullanıcıları yansıtmaz. 
- **Passwordless kimlik doğrulaması özelliğine sahip kullanıcılar** , MICROSOFT AUTHENTICATOR uygulamayla FIDO2, Iş Için Windows Hello veya passwordless telefon oturum açma kullanarak oturum açmak üzere kayıtlı kullanıcıların dökümünü gösterir. 
- **Self servis parola sıfırlama özelliğine sahip kullanıcılar** parolalarını sıfırlayabilecek kullanıcıların dökümünü gösterir. Kullanıcılar, her ikisi de varsa parolalarını sıfırlayabilir:
  - Self servis parola sıfırlama için kuruluşun ilkesini karşılamak üzere yeterli yöntemler için kayıtlı 
  - Parolasını sıfırlamak için etkinleştirildi 

  ![Kaydolabilen kullanıcıların ekran görüntüsü](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Kimlik doğrulama yöntemi tarafından kaydedilen kullanıcılar** , her bir kimlik doğrulama yöntemi için kaç kullanıcının kaydedildiğini gösterir. Bu yöntem için kimin kayıtlı olduğunu görmek için bir kimlik doğrulama yöntemine tıklayın.

![Kayıtlı kullanıcıların ekran görüntüsü](media/how-to-authentication-methods-usage-insights/users-registered.png)

**Kimlik doğrulama yöntemine göre son kayıt** , kaç kayıt başarılı ve başarısız olduğunu ve kimlik doğrulama yöntemine göre sıralanmış olduğunu gösterir. Bu yöntem için en son kayıt olaylarını görmek üzere bir kimlik doğrulama yöntemine tıklayın.

![Son kaydedilen ekran görüntüsü](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Kullanım ayrıntıları

**Kullanım** raporu, oturum açmak ve parolaları sıfırlamak için hangi kimlik doğrulama yöntemlerinin kullanıldığını gösterir.

![Kullanım sayfasının ekran görüntüsü](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Kimlik doğrulama gereksinimine göre oturum açma** Işlemleri, Azure AD 'de tek etmenle karşı çok faktörlü kimlik doğrulaması için gerekli olan başarılı kullanıcı etkileşimli oturum açma sayısını gösterir. MFA 'nın bir üçüncü taraf MFA sağlayıcısı tarafından zorlanan oturum açma işlemleri dahil değildir.

![Kimlik doğrulama gereksinimine göre oturum açma işlemlerinin ekran görüntüsü](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**Kimlik doğrulama yöntemine göre oturum açma** işlemleri, kullanılan kimlik doğrulama yöntemine göre kullanıcı etkileşimli oturum açma işlemlerinin (başarı ve başarısızlık) sayısını gösterir. Bu, kimlik doğrulama gereksiniminin belirteçteki bir talep tarafından karşılanan oturum açma işlemlerini içermez.

![Yönteme göre oturum açma işlemlerinin ekran görüntüsü](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**Parola sıfırlama ve hesap kilidini açma sayısı** , zaman içinde başarılı parola değişikliği ve parola sıfırlama (self servis ve yönetici) sayısını gösterir.

![Sıfırlama ve kilit açma ekran görüntüsü](media/how-to-authentication-methods-usage-insights/password-changes.png)

**Kimlik doğrulama yöntemine göre parola** sıfırlama, kimlik doğrulama yöntemine göre parola sıfırlama akışı sırasında başarılı ve başarısız kimlik doğrulama sayısını gösterir.

![Yönteme göre sıfırlama ekran görüntüsü](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Kullanıcı kaydı ayrıntıları 

Listenin en üstündeki denetimleri kullanarak bir Kullanıcı arayabilir ve gösterilen sütunlara göre Kullanıcı listesini filtreleyebilirsiniz.

Kayıt ayrıntıları raporu, her kullanıcı için aşağıdaki bilgileri gösterir:

- Kullanıcı asıl adı
- Name
- MFA özellikli (uyumlu değil)
- Passwordless özellikli (uyumlu değil)
- SSPR kaydedildi (kayıtlı, kayıtlı değil)
- SSPR etkin (etkin, etkin değil)
- SSPR özellikli (uyumlu değil) 
- Kaydedilen Yöntemler (e-posta, cep telefonu, alternatif cep telefonu, ofis telefonu, Microsoft Authenticator gönderimi, yazılım bir kerelik geçiş kodu, FIDO2, güvenlik anahtarı, güvenlik soruları)

  ![Kullanıcı kaydı ayrıntılarının ekran görüntüsü](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Olayları kaydetme ve sıfırlama 

**Kayıt ve sıfırlama olayları** , son 24 saat, son yedi gün veya son 30 gün içinde olayları kayıt ve sıfırlama olaylarını gösterir:

- Tarih
- Kullanıcı adı
- Kullanıcı 
- Özellik (kayıt, sıfırlama)
- Kullanılan Yöntem (uygulama bildirimi, uygulama kodu, telefon görüşmesi, ofis çağrısı, alternatif mobil çağrı, SMS, e-posta, güvenlik soruları)
- Durum (başarı, hata)
- Hatanın nedeni (Açıklama)

  ![Kayıt ve sıfırlama olaylarının ekran görüntüsü](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Sınırlamalar

- Rapordaki veriler gerçek zamanlı olarak güncellenmez ve birkaç saate kadar gecikme süresini yansıtabilir.
- Geçici erişim geçiş kayıtları, yalnızca kısa bir süre için geçerli olduklarından raporun kayıt sekmesine yansıtılmaz.
- Kullanıcının yapılandırdığı **Phoneappnotification** veya **Phoneappotp** yöntemleri panoda gösterilmez. 

## <a name="next-steps"></a>Sonraki adımlar

- [Kimlik doğrulama yöntemleri kullanım raporu API 'SI ile çalışma](/graph/api/resources/authenticationmethods-usage-insights-overview)
- [Kuruluşunuz için kimlik doğrulama yöntemlerini seçme](concept-authentication-methods.md)
- [Birleşik kayıt deneyimi](concept-registration-mfa-sspr-combined.md)
