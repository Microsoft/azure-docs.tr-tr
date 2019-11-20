---
title: Çok kiracılı uygulamanız için SSO'yu etkinleştirin
description: Azure active Directory ile tümleştirme, bağımsız yazılım satıcıları için yönergeler
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
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795175"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Çok kiracılı uygulama için çoklu oturum açmayı etkinleştir  

Diğer şirketlerin aracılığıyla satın alma veya abonelik tarafından kullanılmak üzere uygulamanızı satışa, uygulamanızın kullanılabilir müşteriler Azure kiracıları içinde yaptığınız. Bu, çok kiracılı bir uygulama oluşturma olarak bilinir. Bu kavramı genel bakış için bkz. [azure'da çok Kiracılı uygulamalar](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) ve [Azure Active Directory'de kiralama](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Çoklu oturum açma nedir

Çoklu oturum açma (SSO) Azure Active Directory ve diğer kimlikleri kullanarak uygulamalar için kullanıcıların oturum açma, güvenlik ve kolaylık ekler. Uygulamaya SSO etkin olduğunda, kullanıcıların bu uygulamaya erişmek için farklı kimlik bilgileri girmeniz gerekmez. Çoklu oturum açma ilişkin tam açıklama için. [Azure Active Directory'de uygulamalar için çoklu oturum açma bkz](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Uygulamanızda çoklu oturum açmayı neden etkinleştirilsin mi?

Çok kiracılı uygulamada SSO etkinleştirme için birçok avantaj vardır. Ne zaman uygulamanız için SSO'yu etkinleştirin:

* Uygulamanız, uygulamanızın Azure Active Directory kullanan kuruluşlar milyonlarca tarafından keşfedilebilir olduğu Azure Marketi'nde listelenebilir.
  * Uygulamayı Azure AD'ye hızlı bir şekilde yapılandırmak müşterilerin sağlar.

* Uygulamanızı Office 365 uygulama galerisinde, Office 365 uygulama başlatıcısında ve Microsoft Search Office.com'daki içinde bulunabilir olabilir

* Uygulamanızı Microsoft Graph'teki kullanılabilir olan kullanıcı üretkenliği artıran verilere erişmek için Microsoft Graph REST API'sini kullanabilirsiniz.

* Müşterileriniz için kolaylaştırarak, destek maliyetlerini azaltır.
  * Uygulamaya özel belgelere karşılıklı müşteriler hızları bağlılığımızın için Azure AD ekiple coproduced.
  * Tek tıklamayla SSO etkinleştirilirse, müşterilerinizin BT yöneticilerinin kuruluşlarında kullanmak için uygulamanızı yapılandırma konusunda bilgi gerekmez.

* Müşterilerinizin tamamen çalışan ve Konuk kimlikleri kimlik doğrulama ve yetkilendirmeyi yönetme olanağı sağlar.

  * Bu kimlikler müşteri sahibi olan tüm hesap yönetimi ve uyumluluk sorumluluk yerleştirebilirsiniz.

  * Etkinleştirme veya devre dışı SSO için özel kimlik sağlayıcıları, gruplar veya kullanıcılar iş gereksinimlerini karşılamak sağlama yeteneği.

* Marketability ve adoptability artırırsınız. Çoğu büyük kuruluş gerektiren (veya uymaya) çalışanlarına tüm uygulamaları arasında sorunsuz SSO deneyimleri sahip. SSO kolaylaştırarak önemlidir.

* Son kullanıcı kullanım artırmak ve Cebinizde artırmak son kullanıcı uyuşmazlıkları azaltın.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Yayımlanan uygulamanızda çoklu oturum açmayı etkinleştirme

1. [Çok kiracılı uygulamanız için doğru Federasyon protokol](isv-choose-multi-tenant-federation.md).
1. Uygulamanızdaki SSO gerçekleştir
   - Bkz: [kimlik doğrulaması desenleri hakkında rehberlik](../develop/v2-app-types.md)
   - Bkz: [Azure active Directory kod örnekleri](../develop/sample-v2-code.md) OIDC/OAuth protokolleri için
1. [Azure Kiracınızı oluşturmak](isv-tenant-multi-tenant-app.md) ve uygulamanızı test edin
1. [Oluşturma ve SSO belgeleri sitenizde yayımlama](isv-create-sso-documentation.md).
1. [Uygulama listenizi gönderme](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) ve Microsoft'un site belgeleri oluşturmak için Microsoft ile iş ortağı.
1. [Microsoft iş ortağı ağı (ücretsiz) katılın ve planı pazara Git oluşturma](https://partner.microsoft.com/en-us/explore/commercial#gtm).
