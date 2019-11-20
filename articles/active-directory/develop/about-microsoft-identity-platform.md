---
title: Microsoft kimlik platformu - Azure evrimi
description: Microsoft kimlik platformu, bir Azure Active Directory (Azure AD) kimlik hizmeti ve geliştirici platformu evrimi hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dc4c29d3a12902b3ce01f9505cba5ac02e921eb
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625356"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Microsoft Identity Platform'un gelişimi

Microsoft kimlik platformu, Azure Active Directory (Azure AD) geliştirici platformunun geliştirilmesiyle ortaya çıkmıştır. Kullanıcılar oturum, gibi Microsoft Graph API'leri veya geliştiricilerin geliştirdim API'leri çağırmak için belirteçleri almak uygulamalar oluşturmalarını sağlar. Bir kimlik doğrulama hizmeti, açık kaynak kitaplıkları, uygulama kaydı ve yapılandırması (aracılığıyla bir geliştirici portal ve API uygulaması) oluşan tam bir geliştirici belgeleri, hızlı başlangıç örnekleri, kod örnekleri, öğreticiler, nasıl yapılır kılavuzları, ve diğer Geliştirici içeriği. Microsoft Identity Platform OAuth 2.0 ve OpenID Connect gibi sektör standardı protokolleri destekler.

Şimdiye kadar Çoğu geliştirici iş kimliğini doğrulamak ve Okul hesapları (Azure AD tarafından sağlanan) için Azure AD v1.0 platformuyla Azure AD v1.0 uç noktasından belirteç talep ederek Azure AD Authentication Library (ADAL) için Azure portalı kullanarak çalıştı Uygulama kaydı ve yapılandırması ve programlı uygulama yapılandırması için Azure AD Graph API'si.

Microsoft kimlik platformu ile (v2.0) bu türde kullanıcılara kitlelere ulaşın:

- İş ve Okul hesaplarına (sağlanan Azure AD hesapları)
- Kişisel hesaplar (örneğin, Outlook.com veya Hotmail.com)
- Müşterilerinize kendi e-posta veya sosyal kimlik (örneğin, LinkedIn, Facebook, Google) aracılığıyla Azure AD B2C teklifi sunun

Birleştirilmiş Microsoft kimlik platformu ile bir kez kod yazın ve herhangi bir Microsoft kimliği uygulamanıza kimlik doğrulaması. Çeşitli platformlar için Microsoft kimlik doğrulama kitaplığı (MSAL) adlı tam olarak desteklenen bir açık kaynak kitaplığı yoktur. MSAL, kullanımı basit olan, yüksek güvenilirlik ve performans elde etmek ve Microsoft Güvenli geliştirme yaşam döngüsü (SDL) kullanarak geliştirilen yardımcı olur, kullanıcılarınız için harika çoklu oturum açma (SSO) deneyimleri sağlar. API'leri çağıran, uygulamanız için daha fazla bozucu kapsamları onay isteği, çalışma zamanında bu uygulamanın kullanımı gerektirdiğini kadar gecikme olanak tanıyan artımlı onay yararlanmak için yapılandırabilirsiniz.

Kaydetme ve uygulamanızı yapılandırmak için Azure portalını kullanın ve programlı uygulama yapılandırması için Microsoft Graph API'sini kullanın.

Uygulamanız kendi hızınızda güncelleştirin. ADAL kitaplıklarını ile oluşturulmuş uygulamaları desteklemeye devam eder. ADAL ve MSAL kitaplıkları ile oluşturulan uygulamalar ile oluşturulmuş uygulamalar oluşan karma uygulama Portföylerini de desteklenir. Bu, en son ADAL ve en son MSAL kullanarak uygulamaları bu kitaplıklar arasında paylaşılan belirteç önbelleği tarafından sağlanan Portföyü genelinde SSO verecektir anlamına gelir. Uygulamalar için MSAL İOS'tan güncelleştirildi, yükseltme sonrasında kullanıcı oturum açma durumunu korur.

## <a name="microsoft-identity-platform-experience"></a>Microsoft kimlik platformu deneyimi

Aşağıdaki diyagramda, uygulama kaydı deneyimi, SDK'lar, uç noktalar ve desteklenen kimlikler de dahil olmak üzere, üst düzeyde Microsoft kimlik deneyimi gösterilir.

![Bugün Microsoft kimlik platformu](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Uygulama kayıt deneyimi

Azure portalında **[uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908)** Microsoft kimlik platformu ile tümleşik olan tüm uygulamaları yönetmek için bir portal deneyimi deneyimidir. Uygulama kayıt portalı kullandıysanız, Azure Portalı Uygulama kaydı deneyimini kullanmayı başlatın.

(Sosyal ya da yerel kimlik doğrularken) Azure AD B2C ile tümleştirme için bir B2C kiracısında uygulamanızı kaydetmeniz gerekir. Bu deneyim ayrıca Azure portalında bir parçasıdır.

**Microsoft Graph API uygulaması** şu anda Önizleme aşamasındadır. Bu API, uygulamalarınızı herhangi bir Microsoft kimlik doğrulama için Microsoft kimlik platformu ile tümleşik programlı bir şekilde yapılandırmak için kullanın. Ancak, bu API, genel kullanılabilirlik ulaşana kadar Azure AD Graph 1.6 API'sini ve uygulama bildirimi kullanmanız gerekir.

### <a name="msal-libraries"></a>MSAL kitaplıkları

MSAL kitaplığı, tüm Microsoft kimlikleri doğrulamak uygulamalar oluşturmak için kullanabilirsiniz. .NET ve JavaScript MSAL kitaplıklarında genel kullanıma sunulmuştur. İOS ve Android için MSAL kitaplıkları vardır ve önizleme aşamasındadır ve bir üretim ortamında kullanım için uygundur. Genel kullanıma sunulan sürümleri MSAL ve ADAL yaptığımız gibi aynı üretim düzeyi desteğin MSAL Önizleme kitaplıklarında sağlıyoruz.

Ayrıca, uygulamanızı Azure AD B2C ile tümleştirmek için MSAL kitaplıkları da kullanabilirsiniz.

Web uygulamaları ve web API'leri oluşturmak için sunucu tarafı kitaplıklar genel kullanıma sunulmuştur: [ASP.NET](https://docs.microsoft.com/aspnet/overview) ve [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Microsoft kimlik platformu uç noktası

Microsoft kimlik Platformu (v2.0) uç noktası sertifikalı OIDC sunulmuştur. Microsoft kimlik doğrulama kitaplığı (MSAL) veya diğer standartlara uygun kitaplığı ile çalışır. İnsan tarafından okunabilir kapsamlar, endüstri standartlarına uygun olarak uygular.

## <a name="next-steps"></a>Sonraki adımlar

v1.0 ve v2.0 hakkında daha fazla bilgi edinin.

* [Microsoft kimlik Platformu (v2.0) genel bakış](v2-overview.md)
* [Azure Active Directory geliştiricilerin (v1.0) genel bakış](v1-overview.md)
