---
title: Çok kiracılı uygulamanız için doğru Federasyon protokolünü seçin
description: Azure Active Directory tümleştirilirken bağımsız yazılım satıcıları için rehberlik
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: ede458e7d4c1cb1a8d7e3f2e2c9df54d5925d6d8
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175975"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Çok kiracılı uygulamanız için doğru Federasyon protokolünü seçin

Hizmet olarak yazılım (SaaS) uygulaması geliştirirken, ve müşterilerinizin ihtiyaçlarını en iyi karşılayan Federasyon protokolünü seçmeniz gerekir. Bu karar, geliştirme platformunuzu temel alır ve müşterilerinizin Office 365 ve Azure AD ekosistemi dahilinde bulunan verilerle tümleştirme yapmanız gerekir.

Azure Active Directory [SSO tümleştirmeleri için kullanılabilen protokollerin](what-is-single-sign-on.md) tüm listesine bakın.
Aşağıdaki tabloda karşılaştırılmaktadır 
* Açık kimlik doğrulaması 2,0 (OAuth 2,0)
* Açık KIMLIK bağlantısı (OıDC)
* Security Assertion Markup Language (SAML)
* Web Hizmetleri Federasyonu (Wsbeslenir)

| Özellik| OAuth/OıDC| SAML/wsbes |
| - |-|-|
| Web tabanlı çoklu oturum açma| √| √ |
| Web tabanlı çoklu oturum kapatma| √| √ |
| Mobil tabanlı çoklu oturum açma| √| √ |
| Mobil tabanlı çoklu oturum açma| √| √ |
| Mobil uygulamalar için koşullu erişim ilkeleri| √| X |
| Mobil uygulamalar için sorunsuz MFA deneyimi| √| X |
| Erişim Microsoft Graph| √| X |

\* Olası, ancak Microsoft örnek veya rehberlik sağlamıyor.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2,0 ve açık KIMLIK Connect

OAuth 2,0, yetkilendirme için [sektör standardı](https://oauth.net/2/) bir protokoldür. OıDC (OpenID Connect), oath 2,0 protokolünün üstünde oluşturulmuş bir [sektör standart](https://openid.net/connect/) kimlik doğrulama katmanıdır.

### <a name="benefits"></a>Avantajlar

Microsoft, protokollerde yerleşik olarak bulunan kimlik doğrulaması ve yetkilendirmeler için OıDC/OAuth 2,0 kullanılmasını önerir. SAML ile ayrıca yetkilendirme uygulamanız gerekir.

Bu protokollerde bulunan yetkilendirme, uygulamanızın Microsoft Graph API aracılığıyla zengin Kullanıcı ve kurumsal verilere erişmesini ve bunları tümleştirmesini sağlar.

OAuth 2,0 ve OıDC kullanmak, uygulamanız için SSO 'yu benimsediği zaman müşterilerinizin Son Kullanıcı deneyimini basitleştirir. Gerekli olan izin kümelerini kolayca tanımlayabilir, bu, yönetici veya son kullanıcı tarafından otomatik olarak temsil edilir.

Ayrıca, bu protokollerin kullanılması, müşterilerinizin uygulamalara erişimi denetlemek için koşullu erişim ve MFA ilkelerini kullanmasına olanak sağlar. Microsoft, geliştirmeye yardımcı olmak için [birden çok teknoloji platformunda kitaplıklar ve kod örnekleri](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) sunar.  

### <a name="implementation"></a>Uygulama

Uygulamanızı bir OAuth 2,0 sağlayıcısı olan Microsoft Identity ile kaydedersiniz. Ayrıca, OAuth 2,0 tabanlı uygulamanızı, tümleştirmek istediğiniz diğer kimlik sağlayıcıları ile de kaydedebilirsiniz. 

Uygulamanızı kaydetme ve Web uygulamalarına SSO için bu protokolleri uygulama hakkında daha fazla bilgi için bkz. [OpenID Connect ve Azure Active Directory kullanarak Web uygulamalarına erişim yetkisi verme](../develop/sample-v2-code.md).  Bu protokollerin mobil uygulamalarda SSO için nasıl uygulanacağı hakkında daha fazla bilgi için aşağıdakilere bakın: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Evrensel Windows Platformu](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2,0 ve wsbes

Security Assertion Markup Language (SAML) genellikle Web uygulamaları için kullanılır. Azure 'un genel bakış için [SAML protokolünü nasıl kullandığını](../develop/active-directory-saml-protocol-reference.md) görün. 

Web Hizmetleri Federasyonu (Wsbeslenir), .NET platformu kullanılarak geliştirilen Web uygulamaları için genellikle kullanılan bir [sektör standardıdır](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) .

### <a name="benefits"></a>Avantajlar

SAML 2,0, yetişkinlere yönelik bir standarttır ve çoğu teknoloji platformu SAML 2,0 için açık kaynaklı kitaplıkları destekler. Müşterilerinizin SAML SSO 'yu yapılandırmak için bir yönetim arabirimi sağlayabilirsiniz. Microsoft Azure AD için SAML SSO ve SAML 2 ' yi destekleyen diğer kimlik sağlayıcıları yapılandırabilir

### <a name="trade-offs"></a>Denge

Mobil uygulamalar için SAML 2,0 veya Wsbesli protokoller kullanırken, çok faktörlü kimlik doğrulaması (MFA) dahil olmak üzere bazı koşullu erişim ilkelerinin düzeyi düşürülmüş bir deneyimle karşılaşacaktır. Ayrıca, Microsoft Graph erişmek istiyorsanız, gerekli belirteçleri oluşturmak için OAuth 2,0 aracılığıyla yetkilendirmeyi uygulamanız gerekir. 

### <a name="implementation"></a>Uygulama

Microsoft, SAML uygulamasına yönelik kitaplıklar sağlamaz veya belirli kitaplıkları önermez. Kullanılabilir çok sayıda açık kaynak kitaplığı vardır.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO ve Microsoft Graph REST API 'SI kullanma 

Microsoft Graph, Office 365, Windows 10 ve Enterprise Mobility ve Security gibi tüm Microsoft 365 ve Dynamics 365 gibi ek ürünleri kapsayan veri dokudır. Bu, Kullanıcı üretkenliğini hedefleyen kullanıcılar, gruplar, takvim, posta, dosyalar ve daha fazlası gibi varlıkların temel şemalarını içerir. Microsoft Graph geliştiricilere, geliştiricilerin kendi verilerini Microsoft Graph eklemesine izin veren bir REST tabanlı API, Microsoft Graph veri bağlantısı ve bağlayıcılar için üç arabirim sunar.  

SSO için yukarıdaki protokollerden herhangi birini kullanmak, uygulamanızın Microsoft Graph REST API aracılığıyla erişilebilen zengin verilere erişmesini sağlar. Bu, müşterilerinizin Microsoft 365 yatırımlarından daha fazla değer almasını sağlar. Örneğin, uygulamanız, müşterilerinizin Office 365 örneği ve Surface Microsoft Office kullanıcıları tarafından uygulamanızdaki SharePoint öğeleriyle tümleştirilebilen Microsoft Graph API 'sini çağırabilir. 

Kimlik doğrulamak için açık KIMLIK bağlantısı kullanıyorsanız, OAuth2 kullanabilmeniz için geliştirme deneyiminizi sorunsuz bir şekilde kullanabilirsiniz. Bu durumda, belirteçleri almak için açık KIMLIK Connect Foundation Microsoft Graph API 'Leri çağırmak için kullanılabilir. Uygulamanız SAML veya Wsbeslenir kullanıyorsa, Microsoft Graph API 'Leri çağırmak için gereken belirteçleri elde etmek üzere bu OAuth2 almak için uygulamanız içine ek kod eklemeniz gerekir. 

## <a name="next-steps"></a>Sonraki Adımlar

[Çok kiracılı uygulamanız için SSO 'yu etkinleştirme](isv-sso-content.md)

[Çok kiracılı uygulamanız için belge oluşturma](isv-create-sso-documentation.md)
