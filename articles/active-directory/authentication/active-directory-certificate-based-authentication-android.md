---
title: Android sertifika tabanlı kimlik doğrulaması-Azure Active Directory
description: Android cihazlarıyla çözümlerinde desteklenen senaryolar ve sertifika tabanlı kimlik doğrulaması yapılandırma gereksinimleri hakkında bilgi edinin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1001d5524fe99783cda4d5b77bdaceacc6791848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861383"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Android 'de sertifika tabanlı kimlik doğrulamayı Azure Active Directory

Android cihazlar, bağlantısı sırasında cihazlarındaki istemci sertifikası kullanarak Azure Active Directory kimlik doğrulaması yapmak için sertifika tabanlı kimlik doğrulaması (CBA) kullanabilir:

* Microsoft Outlook ve Microsoft Word gibi Office mobil uygulamaları
* Exchange ActiveSync (EAS) istemcileri

Bu özelliği yapılandırmak, mobil cihazınızda belirli e-posta ve Microsoft Office uygulamalarına bir Kullanıcı adı ve parola birleşimi girme gereksinimini ortadan kaldırır.

Bu konu, Office 365 Kurumsal, Iş, eğitim, ABD Kamu, Çin ve Almanya planlarındaki kiracıların kullanıcıları için bir Android cihazında CBA 'yi yapılandırmaya yönelik gereksinimler ve desteklenen senaryolar sağlar.

Bu özellik, Office 365 ABD kamu savunması ve Federal planlarında önizlemede sunulmaktadır.

## <a name="microsoft-mobile-applications-support"></a>Microsoft mobil uygulamalar desteği

| Uygulamalar | Destek |
| --- | --- |
| Azure Information Protection uygulaması |![Bu uygulama için desteği belirtir onay işareti][1] |
| Intune Şirket Portalı |![Bu uygulama için desteği belirtir onay işareti][1] |
| Microsoft Teams |![Bu uygulama için desteği belirtir onay işareti][1] |
| OneNote |![Bu uygulama için desteği belirtir onay işareti][1] |
| OneDrive |![Bu uygulama için desteği belirtir onay işareti][1] |
| Outlook |![Bu uygulama için desteği belirtir onay işareti][1] |
| Power BI |![Bu uygulama için desteği belirtir onay işareti][1] |
| Skype Kurumsal |![Bu uygulama için desteği belirtir onay işareti][1] |
| Word/Excel/PowerPoint |![Bu uygulama için desteği belirtir onay işareti][1] |
| Yammer |![Bu uygulama için desteği belirtir onay işareti][1] |

### <a name="implementation-requirements"></a>Uygulama gereksinimleri

Cihazın işletim sistemi sürümü, Android 5,0 (Lollipop) ve üzeri olmalıdır.

Federasyon sunucusunun yapılandırılması gerekir.

İstemci sertifikasını iptal etmek için Azure Active Directory için, ADFS belirtecinin aşağıdaki talepleri olması gerekir:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (İstemci sertifikasının seri numarası)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (İstemci sertifikasını veren için dize)

Azure Active Directory, bu talepleri ADFS belirtecinde (veya başka bir SAML belirteci) varsa yenileme belirtecine ekler. Yenileme belirtecinin doğrulanması gerektiğinde, bu bilgiler iptali denetlemek için kullanılır.

En iyi uygulama olarak, kuruluşunuzun ADFS hata sayfalarını aşağıdaki bilgilerle güncelleştirmeniz gerekir:

* Android 'e Microsoft Authenticator yükleme gereksinimi.
* Kullanıcı sertifikası alma yönergeleri.

Daha fazla bilgi için bkz. [AD FS oturum açma sayfalarını özelleştirme](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

Bazı Office uygulamaları (modern kimlik doğrulaması etkinleştirilmiş), istekleri için Azure AD 'ye '*Prompt = Login*' gönderin. Varsayılan olarak, Azure AD '*wauth = usernamepassworduri*' olarak ADFS 'e istekte bulunan '*Prompt = Login*' değerini çevirir (ADFS 'Nin U/P kimlik doğrulamasını yapması ister) ve '*wyeni = 0*' (ADFS 'in SSO durumunu yok saymasını ve yeni bir kimlik doğrulaması yapması istenir). Bu uygulamalar için sertifika tabanlı kimlik doğrulamasını etkinleştirmek istiyorsanız, varsayılan Azure AD davranışını değiştirmeniz gerekir. Federal etki alanı ayarlarınızda '*Promptloginbehavior*' ayarını '*Disabled*' olarak ayarlayın.
Bu görevi gerçekleştirmek için [Msoldomainfederationsettings](/powershell/module/msonline/set-msoldomainfederationsettings) cmdlet 'ini kullanabilirsiniz:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync istemcileri desteği

Android 5,0 (Lollipop) veya sonraki sürümlerde belirli Exchange ActiveSync uygulamaları desteklenir. E-posta uygulamanızın bu özelliği desteklediğini öğrenmek için uygulama geliştiricinize başvurun.

## <a name="next-steps"></a>Sonraki adımlar

Ortamınızda sertifika tabanlı kimlik doğrulaması yapılandırmak istiyorsanız, yönergeler için bkz. [Android 'de sertifika tabanlı kimlik doğrulamayı kullanmaya başlama](active-directory-certificate-based-authentication-get-started.md) .

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
