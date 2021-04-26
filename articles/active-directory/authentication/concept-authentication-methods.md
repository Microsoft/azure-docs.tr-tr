---
title: Kimlik doğrulama yöntemleri ve özellikleri-Azure Active Directory
description: Oturum açma olaylarının artırılmasına ve güvenliğinin sağlanmasına yardımcı olmak için Azure Active Directory 'de bulunan farklı kimlik doğrulama yöntemleri ve özellikleri hakkında bilgi edinin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: b4d69157f4544daad962cca15e53802e7b912399
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530426"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Azure Active Directory'de mevcut olan kimlik doğrulaması ve doğrulama yöntemleri

Azure Active Directory (Azure AD) hesapları için oturum açma deneyiminin bir parçası olarak, bir kullanıcının kimliklerini doğrulayabilmesi için farklı yollar vardır. Kullanıcı adı ve parola, bir kullanıcının geçmişe ait kimlik bilgilerini sağlaması için en yaygın yoldur. Azure AD 'de Modern kimlik doğrulama ve güvenlik özellikleriyle, bu temel parolanın takıma eklenmesi veya daha güvenli kimlik doğrulama yöntemleriyle değiştirilmeleri gerekir.

![Azure AD 'de güçlü ve tercih edilen kimlik doğrulama yöntemlerinin tablosu](media/concept-authentication-methods/authentication-methods.png)

Windows Hello, FIDO2 güvenlik anahtarları ve Microsoft Authenticator uygulaması gibi parolasız kimlik doğrulama yöntemleri, en güvenli oturum açma olaylarını sağlar.

Azure AD Multi-Factor Authentication (MFA), yalnızca bir Kullanıcı oturum açtığında bir parola kullanarak ek güvenlik sağlar. Kullanıcıya bir anında iletme bildirimine yanıt vermek, bir yazılım veya donanım belirtecinden bir kod girmek ya da bir SMS veya telefon çağrısına yanıt vermek gibi ek kimlik doğrulama biçimleri istenebilir.

Kullanıcı çıkarma deneyimini basitleştirmek ve hem MFA hem de Self servis parola sıfırlama (SSPR) için kaydolmak üzere [Birleşik güvenlik bilgileri kaydını etkinleştirmenizi](howto-registration-mfa-sspr-combined.md)öneririz. Dayanıklılık için, kullanıcıların birden çok kimlik doğrulama yöntemi kaydetmesini gerektirmenizi öneririz. Oturum açma veya SSPR sırasında bir kullanıcı için bir yöntem kullanılamadığında, başka bir yöntemle kimlik doğrulamayı seçebilirler. Daha fazla bilgi için bkz. [Azure AD 'de esnek erişim denetimi yönetim stratejisi oluşturma](concept-resilient-controls.md).

Kuruluşunuzun güvenliğini sağlamak için en iyi kimlik doğrulama yöntemini seçmenize yardımcı olmak üzere oluşturduğumuz bir [video](https://www.youtube.com/watch?v=LB2yj4HSptc&feature=youtu.be) aşağıda verilmiştir.

## <a name="authentication-method-strength-and-security"></a>Kimlik doğrulama yöntemi gücü ve güvenliği

Kuruluşunuzda Azure AD Multi-Factor Authentication gibi özellikler dağıttığınızda, kullanılabilir kimlik doğrulama yöntemlerini gözden geçirin. Güvenlik, kullanılabilirlik ve kullanılabilirlik açısından gereksinimlerinizi karşılayan veya aşan yöntemleri seçin. Mümkün olduğunda, en yüksek güvenlik düzeyiyle kimlik doğrulama yöntemlerini kullanın.

Aşağıdaki tabloda, kullanılabilir kimlik doğrulama yöntemlerine yönelik güvenlik konuları özetlenmektedir. Kullanılabilirlik, kullanıcının Azure AD 'de hizmet kullanılabilirliğini değil, kimlik doğrulama yöntemini kullanabildiğinin bir göstergesidir:

| Kimlik doğrulama yöntemi          | Güvenlik | Kullanılabilirlik | Kullanılabilirlik |
|--------------------------------|:--------:|:---------:|:------------:|
| İş İçin Windows Hello     | Yüksek     | Yüksek      | Yüksek         |
| Microsoft Authenticator uygulaması    | Yüksek     | Yüksek      | Yüksek         |
| FIDO2 güvenlik anahtarı             | Yüksek     | Yüksek      | Yüksek         |
| OATH Donanım belirteçleri (Önizleme) | Orta   | Orta    | Yüksek         |
| OATH yazılım belirteçleri           | Orta   | Orta    | Yüksek         |
| SMS                            | Orta   | Yüksek      | Orta       |
| Ses                          | Orta   | Orta    | Orta       |
| Parola                       | Düşük      | Yüksek      | Yüksek         |

Güvenlikle ilgili en son bilgiler için blog gönderilerimize göz atın:

- [Kimlik doğrulaması için telefon aktarımlarında asılı kalma zamanı](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)
- [Kimlik doğrulama güvenlik açıkları ve saldırı vektörleri](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)

> [!TIP]
> Esneklik ve kullanılabilirlik için Microsoft Authenticator uygulamasını kullanmanızı öneririz. Bu kimlik doğrulama yöntemi, en iyi kullanıcı deneyimini ve passwordless, MFA anında iletme bildirimleri ve OATH kodları gibi birden çok modu sağlar.

## <a name="how-each-authentication-method-works"></a>Her kimlik doğrulama yönteminin nasıl çalıştığı

Bir uygulama veya cihazda oturum açtığınızda, bir FIDO2 güvenlik anahtarı ya da parola gibi bazı kimlik doğrulama yöntemleri birincil faktör olarak kullanılabilir. Diğer kimlik doğrulama yöntemleri yalnızca Azure AD Multi-Factor Authentication veya SSPR kullandığınızda ikincil bir faktör olarak kullanılabilir.

Aşağıdaki tabloda, bir oturum açma olayı sırasında bir kimlik doğrulama yöntemi kullanılabilir olduğunda özetlenmektedir:

| Yöntem                         | Birincil kimlik doğrulama | İkincil kimlik doğrulaması  |
|--------------------------------|:----------------------:|:-------------------------:|
| İş İçin Windows Hello     | Yes                    | Çok faktörlü kimlik doğrulaması                       |
| Microsoft Authenticator uygulaması    | Yes                    | MFA ve SSPR              |
| FIDO2 güvenlik anahtarı             | Yes                    | Çok faktörlü kimlik doğrulaması                       |
| OATH Donanım belirteçleri (Önizleme) | Hayır                     | Çok faktörlü kimlik doğrulaması                       |
| OATH yazılım belirteçleri           | Hayır                     | Çok faktörlü kimlik doğrulaması                       |
| SMS                            | Yes                    | MFA ve SSPR              |
| Sesli arama                     | Hayır                     | MFA ve SSPR              |
| Parola                       | Yes                    |                           |

Bu kimlik doğrulama yöntemlerinin tümü Azure portal yapılandırılabilir ve [Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview)giderek giderek daha da yönetilebilir.

Her bir kimlik doğrulama yönteminin nasıl çalıştığı hakkında daha fazla bilgi edinmek için aşağıdaki ayrı kavramsal makalelere göz atın:

* [İş İçin Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Microsoft Authenticator uygulaması](concept-authentication-authenticator-app.md)
* [FIDO2 güvenlik anahtarı](concept-authentication-passwordless.md#fido2-security-keys)
* [OATH Donanım belirteçleri (Önizleme)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [OATH yazılım belirteçleri](concept-authentication-oath-tokens.md#oath-software-tokens)
* [SMS oturum açma](howto-authentication-sms-signin.md) ve [doğrulama](concept-authentication-phone-options.md#mobile-phone-verification)
* [Sesli arama doğrulaması](concept-authentication-phone-options.md)
* Parola

> [!NOTE]
> Azure AD 'de, bir parola genellikle birincil kimlik doğrulama yöntemlerinden biridir. Parola kimlik doğrulama yöntemini devre dışı bırakamıyorum. Birincil kimlik doğrulama faktörü olarak bir parola kullanırsanız, Azure AD Multi-Factor Authentication kullanarak oturum açma olaylarının güvenliğini artırın.

Aşağıdaki ek doğrulama yöntemleri belirli senaryolarda kullanılabilir:

* [Uygulama parolaları](howto-mfa-app-passwords.md) -modern kimlik doğrulamayı desteklemeyen eski uygulamalar için kullanılır ve Kullanıcı BAŞıNA Azure AD Multi-Factor Authentication için yapılandırılabilir.
* [Güvenlik soruları](concept-authentication-security-questions.md) -yalnızca SSPR için kullanılır
* [E-posta adresi](concept-sspr-howitworks.md#authentication-methods) -yalnızca SSPR için kullanılır

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için [self servis parola sıfırlama (SSPR)][tutorial-sspr] ve [Azure AD Multi-Factor Authentication][tutorial-azure-mfa]öğreticisine bakın.

SSPR kavramları hakkında daha fazla bilgi edinmek için bkz. [Azure AD self servis parola sıfırlamasının nasıl çalıştığı][concept-sspr].

MFA kavramları hakkında daha fazla bilgi edinmek için bkz. [Azure AD Multi-Factor Authentication nasıl çalıştığı][concept-mfa].

[Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview)kullanarak kimlik doğrulama yöntemlerini yapılandırma hakkında daha fazla bilgi edinin.

Hangi kimlik doğrulama yöntemlerinin kullanımda olduğunu gözden geçirmek için bkz. [PowerShell Ile Azure AD Multi-Factor Authentication kimlik doğrulama yöntemi analizi](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
