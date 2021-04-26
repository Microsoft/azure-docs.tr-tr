---
title: MSAL.NET 'de AD FS desteği | Mavisi
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı 'nda (MSAL.NET) Active Directory Federasyon Hizmetleri (AD FS) (AD FS) desteği hakkında bilgi edinin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: a146b310e6056954ac2655ff2fd99e1e3d7c694f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063646"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>MSAL.NET içinde destek Active Directory Federasyon Hizmetleri (AD FS)
Windows Server 'daki Active Directory Federasyon Hizmetleri (AD FS) (AD FS), geliştirmekte olduğunuz uygulamalara OpenID Connect ve OAuth 2,0 tabanlı kimlik doğrulaması ve yetkilendirme eklemenize olanak sağlar. Bu uygulamalar daha sonra kullanıcıların kimliğini doğrudan AD FS karşı doğrulayabilir. Daha fazla bilgi için, [geliştiriciler için AD FS senaryoları](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)makalesini okuyun.

.NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET), AD FS kimlik doğrulama için iki senaryoyu destekler:

- MSAL.NET, kendisini AD FS *Federal* olan Azure Active Directory konuşur.
- MSAL.NET **doğrudan** bir ADFS yetkilisine konuşur. Bu yalnızca AD FS 2019 ve üzeri sürümlerde desteklenir. Bu önemli senaryolardan biri [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) desteklenir


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL, AD FS Federal olan Azure AD 'ye bağlanır
MSAL.NET, yönetilen kullanıcılar (Azure AD 'de yönetilen kullanıcılar) veya Federasyon kullanıcıları (AD FS gibi başka bir kimlik sağlayıcısı tarafından yönetilen kullanıcılar) üzerinde oturum açan Azure AD 'ye bağlanmayı destekler. MSAL.NET, kullanıcıların federe olduğu gerçeğini bilmez. Söz konusu olduğu kadar, Azure AD ile iletişim kuran bir.

Bu durumda kullandığınız [yetkili](msal-client-application-configuration.md#authority) , her zamanki yetkilinin (yetkili ana bilgisayar adı + kiracı, ortak veya kuruluşlar).

### <a name="acquiring-a-token-interactively"></a>Bir belirteci etkileşimli olarak alma
`AcquireTokenInteractive`Yöntemini çağırdığınızda, Kullanıcı deneyimi genellikle şu şekilde olur:

1. Kullanıcı hesap KIMLIĞINI girer.
2. Azure AD, "kuruluşunuzun sayfasına yönlendirilme" iletisini kısaca görüntüler.
3. Kullanıcı, kimlik sağlayıcısının oturum açma sayfasına yönlendirilir. Oturum açma sayfası genellikle kuruluşun logosu ile özelleştirilir.

Bu Federasyon senaryosunda desteklenen AD FS sürümleri AD FS v2, AD FS v3 (Windows Server 2012 R2) ve AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Acquiretokenbyıntegratedaduthentication veya AcquireTokenByUsernamePassword kullanarak bir belirteç alınıyor
Veya yöntemlerini kullanarak bir belirteç edinilirken `AcquireTokenByIntegratedAuthentication` `AcquireTokenByUsernamePassword` , msal.NET Kullanıcı adına göre iletişim kurulacak kimlik sağlayıcısını alır.  MSAL.NET, kimlik sağlayıcısıyla iletişim kurduktan sonra bir [SAML 1,1 belirteci](reference-saml-tokens.md) alır.  MSAL.NET ardından, SAML belirtecini bir JWT geri almak için Kullanıcı Onayı (örneğin, [akışa](msal-authentication-flows.md#on-behalf-of)benzer) olarak Azure AD 'ye sağlar.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL AD FS doğrudan bağlanır
MSAL.NET, açık KIMLIK bağlantısı olan AD FS 2019 ' ye bağlanmayı destekler ve PKI ve kapsamları anlamıştır. Bu destek, Windows Server 'a bir hizmet paketi [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) uygulanmasını gerektirir. AD FS doğrudan bağlanırken, uygulamanızı derlemek için kullanmak istediğiniz yetkili buna benzer `https://mysite.contoso.com/adfs/` .

Şu anda, ile doğrudan bağlantıyı desteklemeye yönelik bir plan yoktur:

- AD FS, PCE 'yi desteklemediğinden ve yine de kaynakları kullandığından (kapsam değil) 16.
- OıDC uyumlu olmayan AD FS v2.

 AD FS 2016 'e doğrudan bağlantı gerektiren senaryoları desteklemeniz gerekiyorsa [Azure Active Directory kimlik doğrulama kitaplığının](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries)en son sürümünü kullanın. Şirket içi sisteminizi AD FS 2019 ' e yükseltmişseniz, MSAL.NET kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Federasyon durumu için bkz. [ana bölge bulma ilkesi kullanarak bir uygulama için Azure Active Directory oturum açma davranışını yapılandırma](../manage-apps/configure-authentication-for-federated-users-portal.md)