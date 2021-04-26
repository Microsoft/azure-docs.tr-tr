---
title: Kimlik doğrulaması ile yetkilendirme | Mavisi
titleSuffix: Microsoft identity platform
description: Microsoft Identity platformunda kimlik doğrulama ve yetkilendirmenin temelleri hakkında bilgi edinin.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5244a8e572abb56c00d87d0bdd7e8d1291af9b9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581916"
---
# <a name="authentication-vs-authorization"></a>Kimlik Doğrulaması ve yetkilendirme

Bu makalede kimlik doğrulama ve yetkilendirme tanımlanmaktadır. Ayrıca, Web uygulamalarınızda, Web API 'Lerinde veya korumalı Web API 'Lerini çağıran uygulamalarda kullanıcıların kimliğini doğrulamak ve yetkilendirmek için Microsoft Identity platformunu nasıl kullanabileceğinizi kısaca ele alınmaktadır. Bildiğiniz bir terimi görürseniz, temel kavramları kapsayan [Sözlüğümüzü](developer-glossary.md) veya [Microsoft Identity platform videolarımızı](identity-videos.md)deneyin.

## <a name="authentication"></a>Kimlik Doğrulaması

*Kimlik doğrulama* , sizin olduğunu söylediğinizi kanıtlama işlemidir. Bazen *AuthN* olarak kısaltıldı. Microsoft Identity platformu kimlik doğrulamasını işlemek için [OpenID Connect](https://openid.net/connect/) protokolünü kullanır.

## <a name="authorization"></a>Yetkilendirme

*Yetkilendirme* , kimliği doğrulanmış bir tarafın bir şeyi yapmak için izin verme işlemidir. Hangi verilerin erişebileceğini ve bu verilerle yapabileceklerinizi belirtir. Yetkilendirme bazen *Authz* olarak kısaltılır. Microsoft Identity platformu, yetkilendirmeyi işlemek için [OAuth 2,0](https://oauth.net/2/) protokolünü kullanır.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Microsoft Identity platformunu kullanarak kimlik doğrulaması ve yetkilendirme

Her birinin kendi Kullanıcı adı ve parola bilgilerini korumakta olduğu uygulamalar oluşturma, birden çok uygulama arasında kullanıcı eklerken veya kaldırırken yüksek bir yönetim yükü doğurur. Bunun yerine, uygulamalarınız bu sorumluluğu merkezi bir kimlik sağlayıcısına devredebilir.

Azure Active Directory (Azure AD), bulutta merkezi bir kimlik sağlayıcıdır. Kimlik doğrulama ve yetkilendirme için temsilci seçme, şu senaryolara olanak sağlar:

- Bir kullanıcının belirli bir konumda olmasını gerektiren koşullu erişim ilkeleri.
- Bazen iki öğeli kimlik doğrulama veya 2FA olarak adlandırılan [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)'ın kullanımı.
- Bir kullanıcının bir kez oturum açmasını sağlama ve ardından aynı merkezi dizini paylaşan tüm Web uygulamalarında otomatik olarak oturum açma. Bu özellik *Çoklu oturum açma (SSO)* olarak adlandırılır.

Microsoft Identity platformu, bir hizmet olarak kimlik sağlayarak uygulama geliştiricileri için Yetkilendirmeyi ve kimlik doğrulamasını basitleştirir. Kodlamaya hızlı bir şekilde başlamanıza yardımcı olması için endüstri standardı protokolleri ve farklı platformlar için açık kaynak kitaplıklarını destekler. Geliştiricilerin tüm Microsoft kimliklerinden oturum açmasını, [Microsoft Graph](https://developer.microsoft.com/graph/)çağırmak, Microsoft API 'lerine erişmek veya geliştiricilerin oluşturduğu diğer API 'lere erişmek için belirteçleri almasını sağlar.

Bu videoda, Microsoft Identity platformu ve modern kimlik doğrulamasının temelleri açıklanmaktadır: 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Microsoft Identity platformunun kullandığı protokollerin karşılaştırması aşağıda verilmiştir:

* **OAuth ve OpenID Connect**: platform yetkilendirme için OAuth ve kimlik doğrulaması Için OpenID Connect (OIDC) kullanır. OpenID Connect, OAuth 2,0 üzerine kurulmuştur, bu nedenle terminoloji ve akış ikisi arasında benzerdir. Her ikisi de bir kullanıcının kimliğini doğrulayabilir (OpenID Connect aracılığıyla) ve kullanıcının sahip olduğu korumalı bir kaynağa (OAuth 2,0 aracılığıyla) tek bir istekte erişim yetkisi verebilirsiniz. Daha fazla bilgi için bkz. [OAuth 2,0 ve OpenID Connect Protocols](active-directory-v2-protocols.md) ve [OpenID Connect Protocol](v2-protocols-oidc.md).
* **OAuth Ile SAML**: platform kimlik doğrulaması için, YETKILENDIRME ve SAML için OAuth 2,0 kullanır. Bir kullanıcının kimliğini doğrulamak ve korunan bir kaynağa erişmek için yetkilendirme almak üzere bu protokollerin birlikte kullanılması hakkında daha fazla bilgi için bkz. [Microsoft Identity platform ve OAuth 2,0 SAML taşıyıcı onaylama akışı](./scenario-token-exchange-saml-oauth.md).
* **OpenID Connect ve SAML**: platform bir kullanıcının kimliğini doğrulamak ve çoklu oturum açmayı etkinleştirmek Için hem OpenID Connect hem de SAML kullanır. SAML kimlik doğrulaması genellikle, Azure AD 'ye federe Active Directory Federasyon Hizmetleri (AD FS) (AD FS) gibi kimlik sağlayıcılarıyla kullanılır, bu nedenle genellikle kurumsal uygulamalarda kullanılır. OpenID Connect genellikle, mobil uygulamalar, Web siteleri ve Web API 'Leri gibi yalnızca bulutta bulunan uygulamalar için kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

Kimlik doğrulama ve yetkilendirme temellerini kapsayan diğer konular için:

* Erişim belirteçlerinin, yenileme belirteçlerinin ve kimlik belirteçlerinin yetkilendirme ve kimlik doğrulama için nasıl kullanıldığını öğrenmek için bkz. [güvenlik belirteçleri](security-tokens.md).
* Uygulamanızı Microsoft Identity platformu ile tümleştirilebilen şekilde kaydetme süreci hakkında bilgi edinmek için bkz. [uygulama modeli](application-model.md).