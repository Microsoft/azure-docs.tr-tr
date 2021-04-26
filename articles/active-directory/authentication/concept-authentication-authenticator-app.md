---
title: Microsoft Authenticator uygulama kimlik doğrulama yöntemi-Azure Active Directory
description: Oturum açma olaylarını geliştirmek ve güvenli hale getirmek için Azure Active Directory Microsoft Authenticator uygulamayı kullanma hakkında bilgi edinin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3175b1292a7e69506b9193d1182e184e257ebda3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530493"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Azure Active Directory Microsoft Authenticator uygulamasındaki kimlik doğrulama yöntemleri

Microsoft Authenticator uygulaması, Azure AD iş veya okul hesabınız veya Microsoft hesabı için ek bir güvenlik düzeyi sağlar ve [Android](https://go.microsoft.com/fwlink/?linkid=866594) ve [iOS](https://go.microsoft.com/fwlink/?linkid=866594)için kullanılabilir. Microsoft Authenticator uygulamayla, kullanıcılar oturum açma sırasında daha az bir şekilde veya self servis parola sıfırlama (SSPR) veya Azure AD Multi-Factor Authentication olayları sırasında ek bir doğrulama seçeneği olarak kimlik doğrulaması yapabilir.

Kullanıcılar, bir oturum açma arabirimine girilebilecek bir OAUTH doğrulama kodu oluşturmak için mobil uygulama aracılığıyla kendileri için bir bildirim alabilir veya bu uygulamayı kullanabilir. Hem bildirim hem de doğrulama kodunu etkinleştirirseniz, Authenticator uygulamasını kaydeden kullanıcılar kimliklerini doğrulamak için her iki yöntemi de kullanabilir.

Kimlik doğrulayıcı uygulamasını Kullanıcı adı ve parola birleşimi yerine bir oturum açma isteminde kullanmak için bkz. [Microsoft Authenticator App ile passwordless oturum açmayı etkinleştirme](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Kullanıcılar SSPR 'yi etkinleştirdiklerinde mobil uygulamalarını kaydetme seçeneğine sahip değildir. Bunun yerine, kullanıcılar mobil uygulamalarını [https://aka.ms/mfasetup](https://aka.ms/mfasetup) konumundaki Birleşik güvenlik bilgileri kaydının bir parçası olarak veya ' de kaydedebilir [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

## <a name="passwordless-sign-in"></a>Parolasız oturum açma

Kullanıcı adı girdikten sonra parola istemi görmek yerine, Microsoft Authenticator uygulamadan telefonla oturum açmayı etkinleştirmiş bir Kullanıcı, uygulamasındaki bir sayıya dokunmak üzere bir ileti görür. Doğru sayı seçildiğinde, oturum açma işlemi tamamlanmıştır.

![Kullanıcının oturum açma onayını onaylamasını isteyen tarayıcı oturum açma örneği](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Bu kimlik doğrulama yöntemi, yüksek düzeyde güvenlik sağlar ve kullanıcının oturum açma sırasında parola sağlaması gereksinimini ortadan kaldırır. 

Parolasız oturum açma 'yı kullanmaya başlamak için bkz. [Microsoft Authenticator App ile parolasız oturum açmayı etkinleştirme](howto-authentication-passwordless-phone.md).

## <a name="notification-through-mobile-app"></a>Mobil uygulama aracılığıyla bildirim

Authenticator uygulaması, Smartphone 'a veya tabletinize bir bildirim göndererek, hesaplara yetkisiz erişimin önlenmesine ve sahte işlemleri durdurmasına yardımcı olabilir. Kullanıcılar bildirimi görüntüler ve meşru ise **Doğrula**' yı seçin. Aksi takdirde, **Reddet**' i seçebilir.

![Oturum açma işlemini tamamlamaya yönelik kimlik doğrulayıcı uygulama bildirimi için Web tarayıcı istemi örnek ekran görüntüsü](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Kuruluşunuzda Çin 'de çalışan veya Çin 'e geçiş yapan bir personel varsa, Android cihazlarda *mobil uygulama yöntemi aracılığıyla bildirim* , Google Play hizmetleri (anında iletme bildirimleri dahil) bölgede engellenmeye karşı, bu ülkede/bölgede çalışmaz. Ancak iOS bildirimi çalışır. Android cihazlarda, bu kullanıcılar için alternatif kimlik doğrulama yöntemlerinin kullanılabilir hale getirilmelidir.

## <a name="verification-code-from-mobile-app"></a>Mobil uygulamadaki doğrulama kodu

Kimlik doğrulayıcı uygulaması bir OATH doğrulama kodu oluşturmak için yazılım belirteci olarak kullanılabilir. Kullanıcı adınızı ve parolanızı girdikten sonra, kimlik doğrulayıcı uygulaması tarafından belirtilen kodu oturum açma arabirimine girersiniz. Doğrulama kodu, ikinci bir kimlik doğrulama biçimi sağlar.

Kullanıcılar, her zaman kullanılmak üzere yapılandırılmış Microsoft Authenticator uygulaması gibi beş OATH donanım belirtecinin veya Authenticator uygulamasının bir birleşimine sahip olabilir.

> [!WARNING]
> Yalnızca bir yöntem sıfırlama için gerekli olduğunda self servis parola sıfırlamasının en yüksek düzeyde güvenlik sağlamak için, kullanıcılara sunulan tek seçenek bir doğrulama kodu olur.
>
> İki yöntem gerektiğinde, kullanıcılar diğer etkin yöntemlerin yanı sıra bir bildirim ya da doğrulama kodu kullanarak sıfırlayabilir.

## <a name="next-steps"></a>Sonraki adımlar

Parolasız oturum açma 'yı kullanmaya başlamak için bkz. [Microsoft Authenticator App ile parolasız oturum açmayı etkinleştirme](howto-authentication-passwordless-phone.md).

[Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview)kullanarak kimlik doğrulama yöntemlerini yapılandırma hakkında daha fazla bilgi edinin.
