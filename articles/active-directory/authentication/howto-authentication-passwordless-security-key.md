---
title: Azure AD (Önizleme) için passwordless güvenlik anahtarı oturum açma özelliğini etkinleştirme-Azure Active Directory
description: FIDO2 güvenlik anahtarlarını (Önizleme) kullanarak Azure AD 'de passwordless güvenlik anahtarı oturum açma özelliğini etkinleştirme
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50af82e79e7ba8b979ab28a1b3f608ec7e41bfb2
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603443"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Passwordless güvenlik anahtarı oturum açma özelliğini etkinleştir (Önizleme)

Bugün parola kullanan ve paylaşılan bir BILGISAYAR ortamına sahip olan kuruluşlar için güvenlik anahtarları, çalışanların Kullanıcı adı veya parola girmeden kimlik doğrulamasının sorunsuz bir yolunu sağlar. Güvenlik anahtarları, çalışanlar için geliştirilmiş üretkenlik sağlar ve daha iyi güvenliğe sahiptir.

Bu belge güvenlik anahtarı tabanlı passwordless kimlik doğrulamasını etkinleştirmeye odaklanır. Bu makalenin sonunda, FIDO2 güvenlik anahtarı kullanarak Azure AD hesabınızla Web tabanlı uygulamalarda oturum açabilirsiniz.

|     |
| --- |
| FIDO2 güvenlik anahtarları Azure Active Directory genel önizleme özelliğidir. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Gereksinimler

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Birleşik güvenlik bilgileri kayıt önizlemesi](concept-registration-mfa-sspr-combined.md)
- Uyumlu [FIDO2 güvenlik anahtarları](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN, Windows 10 sürüm 1809 veya üstünü gerektirir * *

Web Apps ve hizmetlerinde oturum açmak için güvenlik anahtarlarını kullanmak üzere, WebAuthN protokolünü destekleyen bir tarayıcıya sahip olmanız gerekir. Bunlara Microsoft Edge, Chrome, Firefox ve Safari dahildir.

## <a name="prepare-devices-for-preview"></a>Cihazları önizleme için hazırlama

İle pillendirilecektir, Windows 10 sürüm 1809 veya üstünü çalıştırıyor olmalıdır. En iyi deneyim Windows 10 sürüm 1903 veya daha yüksektir.

## <a name="enable-passwordless-authentication-method"></a>Passwordless kimlik doğrulama yöntemini Etkinleştir

### <a name="enable-the-combined-registration-experience"></a>Birleşik kayıt deneyimini etkinleştir

Passwordless kimlik doğrulama yöntemlerinin kayıt özellikleri, Birleşik kayıt önizlemesine bağımlıdır. Birleşik kayıt önizlemesini etkinleştirmek için [Birleşik güvenlik bilgileri kaydını (Önizleme) etkinleştirme](howto-registration-mfa-sspr-combined.md)makalesindeki adımları izleyin.

### <a name="enable-fido2-security-key-method"></a>FIDO2 güvenlik anahtarı yöntemini Etkinleştir

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Kimlik doğrulama yöntemi ilkesi (Önizleme)**  > **Azure Active Directory** > **güvenlik** > **kimlik doğrulama yöntemlerini** inceleyin.
1. Method **FIDO2 Security Key**altında aşağıdaki seçenekleri belirleyin:
   1. **Etkinleştir** -Evet veya Hayır
   1. **Hedef** -tüm kullanıcılar veya kullanıcıları seçin
1. Yapılandırmayı **kaydedin** .

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 güvenlik anahtarlarının Kullanıcı kaydı ve yönetimi

1. [https://myprofile.microsoft.com](https://myprofile.microsoft.com)gidin.
1. Henüz yoksa oturum açın.
1. **Güvenlik bilgileri**' ne tıklayın.
   1. Kullanıcının kayıtlı en az bir Azure Multi-Factor Authentication yöntemi zaten varsa, bir FIDO2 güvenlik anahtarını hemen kaydedebilirler.
   1. Kayıtlı en az bir Azure Multi-Factor Authentication yöntemi yoksa, bir tane eklemesi gerekir.
1. **Yöntem Ekle** ' ye tıklayıp **güvenlik anahtarı**' nı seçerek bir FIDO2 güvenlik anahtarı ekleyin.
1. **USB cihazı** veya **NFC cihazını**seçin.
1. Anahtarınızı hazırlayın ve **İleri ' yi**seçin.
1. Bir kutu görünür ve kullanıcıdan güvenlik anahtarınız için bir PIN oluşturmasını/girmesini ister ve ardından anahtar için Biyometri ya da Touch için gerekli hareketi gerçekleştirir.
1. Kullanıcı, Birleşik kayıt deneyimine döndürülür ve kullanıcının birden çok tane varsa bunu belirleyebilmesi için anahtar için anlamlı bir ad sağlaması istenir. **İleri**’ye tıklayın.
1. İşlemi gerçekleştirmek için **bitti** ' ye tıklayın.

## <a name="sign-in-with-passwordless-credential"></a>Passwordless kimlik bilgileriyle oturum açın

Aşağıdaki örnekte, bir Kullanıcı FIDO2 güvenlik anahtarını zaten sağladı. Kullanıcı, Windows 10 sürüm 1809 veya üzeri sürümlerde desteklenen bir tarayıcı içinde FIDO2 güvenlik anahtarı ile Web 'de oturum açmayı tercih edebilir.

![Güvenlik anahtarı oturum açma Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Sorun giderme ve geri bildirim

Bu özelliğin önizlemesini yaparken geri bildirimde bulunmak veya sorun yaşarsanız, lütfen Windows Geri Bildirim Hub 'ı uygulaması aracılığıyla paylaşabilirsiniz.

1. **Geri Bildirim Hub 'ını** başlatın ve oturum açtığınızdan emin olun.
1. Aşağıdaki kategoriye göre geri bildirim gönderin:
   1. Kategori: güvenlik ve Gizlilik
   1. Alt Kategori: FıDO
1. Günlükleri yakalamak için, şu seçeneği kullanın: **sorunum yeniden oluştur**

## <a name="known-issues"></a>Bilinen sorunlar

### <a name="security-key-provisioning"></a>Güvenlik anahtarı sağlama

Güvenlik anahtarlarının yönetici tarafından sağlanması ve devre dışı sağlanması genel önizlemede bulunmamaktadır.

### <a name="upn-changes"></a>UPN değişiklikleri

Bir kullanıcının UPN 'si değişirse değişiklik için FIDO2 güvenlik anahtarlarını artık değiştiremezsiniz. Çözüm, cihazı sıfırlamadır ve Kullanıcı FIDO2 güvenlik anahtarlarını yeniden kaydetmek zorunda olur.

## <a name="next-steps"></a>Sonraki adımlar

[FIDO2 güvenlik anahtarı Windows 10 oturum açma](howto-authentication-passwordless-security-key-windows.md)

[Şirket içi kaynaklarda FIDO2 kimlik doğrulamasını etkinleştirme](howto-authentication-passwordless-security-key-on-premises.md)

[Cihaz kaydı hakkında daha fazla bilgi edinin](../devices/overview.md)

[Azure Multi-Factor Authentication hakkında daha fazla bilgi](../authentication/howto-mfa-getstarted.md)
