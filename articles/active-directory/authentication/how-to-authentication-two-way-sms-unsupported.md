---
title: İki yönlü SMS artık desteklenmiyor-Azure Active Directory
description: Hala iki yönlü SMS kullanan kullanıcılar için başka bir yöntemin nasıl etkinleştirileceğini açıklar.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/02/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d25ed1e46823ec6d820addf3944c96c97fcabcb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689037"
---
# <a name="two-way-sms-unsupported"></a>İki yönlü SMS desteklenmez

Azure AD Multi-Factor Authentication (MFA) sunucusu için iki yönlü SMS, başlangıçta 2018 ' de kullanımdan kaldırılmıştır ve 24 Şubat 2021 ' den sonra artık desteklenmemektedir. Yöneticiler, hala iki yönlü SMS kullanan kullanıcılar için başka bir yöntemi etkinleştirmelidir.

E-posta bildirimleri ve Azure portal Service Health bildirimleri (portal Toler), 8 Aralık 2020 ve 28 Ocak 2021 tarihinde etkilenen yöneticilere gönderilmiştir. Uyarılar, aboneliğe bağlı olan sahip, Ikincil sahip, yönetici ve hizmet yöneticisi RBAC rollerine gitti. Aşağıdaki adımları zaten tamamladıysanız, herhangi bir eylemde bulunmanıza gerek yoktur.

## <a name="required-actions"></a>Gerekli eylemler

1. Daha önce yapmadıysanız, kullanıcılarınız için mobil uygulamayı etkinleştirin. Daha fazla bilgi için bkz. [MFA sunucusu ile mobil uygulama kimlik doğrulamasını etkinleştirme](howto-mfaserver-deploy-mobileapp.md).
1. Mobil uygulamayı etkinleştirmek için son kullanıcılarınıza MFA sunucusu [Kullanıcı portalınızı](howto-mfaserver-deploy-userportal.md) ziyaret etmeyi bildirin. [Microsoft Authenticator uygulaması](https://www.microsoft.com/en-us/account/authenticator) , ıkı yönlü SMS 'den daha güvenli olduğundan önerilen doğrulama seçeneğidir. Daha fazla bilgi için lütfen bkz. [kimlik doğrulaması Için telefon aktarımlarında asılı kalma süresi](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752).
1. İki yönlü metin iletisinden Kullanıcı ayarlarını, varsayılan yöntem olarak mobil uygulama olarak değiştirin.

## <a name="faq"></a>SSS

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>İki yönlü SMS 'den mobil uygulamaya varsayılan yöntemi değiştirdiğimde ne yapmalıyım?
24 Şubat 2021 ' den sonra iki yönlü SMS başarısız oluyor. Kullanıcılar, oturum açmayı ve MFA 'yı geçirmeye çalıştıklarında bir hata görür.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>Nasıl yaparım? iki yönlü metin iletisinden Kullanıcı ayarlarını mobil uygulama olarak değiştirmek mi istiyorsunuz?

Aşağıdaki adımları izleyerek Kullanıcı ayarlarını değiştirmelisiniz:

1. MFA sunucusu ' nda, iki yönlü kısa mesaj için kullanıcı listesine filtre uygulayın.
1. Tüm kullanıcılar ' ı seçin.
1. Kullanıcıları Düzenle iletişim kutusunu açın.
1. Kullanıcıları metin iletisinden mobil uygulamaya değiştirme.

   ![Son kullanıcıların ekran görüntüsü](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>Kullanıcılarım herhangi bir işlem yapması gerekiyor mu? Yanıt Evet ise nasıl?
Evet. Son kullanıcılarınızın, daha önce yapmadıysanız, mobil uygulamayı etkinleştirmek için belirli MFA sunucusu Kullanıcı portalınızı ziyaret etmeniz gerekir. Adım 3 ' ü tamamladıktan sonra, mobil uygulamayı ayarlamak için Kullanıcı portalını ziyaret etmediğiniz tüm kullanıcılar, yeniden kaydolmak için Kullanıcı portalını ziyaret edene kadar oturum açma işlemi başlar.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>Kullanıcılarım mobil uygulamayı yükleyediğimde ne olacak? Diğer seçenekler nelerdir?
İki yönlü SMS veya mobil uygulama için alternatif bir telefon çağrıdır. Ancak, Microsoft Authenticator uygulaması önerilen doğrulama yöntemidir.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>Tek yönlü SMS de kullanım dışı olacaktır mi?
Hayır, yalnızca iki yönlü SMS kullanım dışı bırakılıyor. MFA sunucusu için tek yönlü bir SMS, senaryoların bir alt kümesi için kullanılır:

- AD FS bağdaştırıcısı
- IIS kimlik doğrulaması (Kullanıcı Portalı ve yapılandırma gerektirir)
- RADIUS (RADIUS istemcilerinin erişim sınamasını desteklemesini ve PAP protokolünün kullanıldığını gerektirir)

Tek yönlü SMS 'nin, doğrulama kodu istemi gerektirmediğinden, mobil uygulamayı daha iyi bir alternatif haline getirmek için kullanılabilecek sınırlamalar vardır.
Bazı senaryolarda hala tek yönlü bir SMS kullanmak istiyorsanız, bunları denetlenmeye devam edebilir, ancak **genel** sekmesi **Kullanıcı Varsayılanları metin iletisini** **iki yönlü** yerine **tek yönlü** olarak değiştirebilirsiniz.  Son olarak, SMS için varsayılan olarak dizin eşitlemesi kullanıyorsanız, bunu Iki yönlü yerine One-Way değiştirmeniz gerekir.

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>Hangi kullanıcıların hala iki yönlü SMS kullandığını nasıl denetabilirim?
Bu kullanıcıları listelemek için **MFA sunucusu**' nu başlatın, **Kullanıcılar** bölümünü seçin, **Kullanıcı listesini filtrele**' yi tıklatın ve **metin iletisi iki yönlü** olarak filtreleyin.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>Kullanıcıların gelecekte seçmesini engellemek için MFA portalında bir seçenek olarak iki yönlü SMS 'yi nasıl gizliyoruz?
MFA sunucusu Kullanıcı Portalı ' nda, **Ayarlar**' a tıklayın, kullanılabilir olmaması Için **metin iletisini** temizleyebilirsiniz. Kullanıcı kaydı için AD FS kullanıyorsanız, **AD FS** bölümünde aynı değer geçerlidir.

