---
title: Çok kiracılı bir uygulama için Azure kiracısı oluşturma
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
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812619"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Çok kiracılı bir uygulama için Azure kiracısı oluşturma  

Çok kiracılı uygulamanıza erişim sağlamak için, uygulamayı kaydetmek ve müşterinizin kimliklerinin federasyonunu etkinleştirmek üzere bir Azure Active Directory kiracı oluşturmanız gerekir. Bkz. [çok kiracılı uygulamanız için doğru Federasyon protokolünü seçme](isv-choose-multi-tenant-federation.md). Bu kiracı, müşterilerinizin Azure AD ortamlarına benzer bir ortamda uygulamanızı ve Federasyonu test etmeniz için size olanak sağlayacak.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Çok kiracılı bir uygulama barındırma maliyetleri

Azure Active Directory birden çok sürümde kullanılabilir. [Ayrıntılı Özellik Karşılaştırması bölümüne bakın](https://azure.microsoft.com/pricing/details/active-directory/).

Azure aboneliğinizi ve Azure Active Directory 'nizi ücretsiz olarak oluşturabilir ve temel özellikleri kullanabilirsiniz.

## <a name="create-your-tenant"></a>Kiracınızı oluşturun

1. Kiracınızı oluşturun. Bkz. [geliştirme ortamı ayarlama](../develop/quickstart-create-new-tenant.md).

2. Uygulamanıza çoklu oturum açma erişimini etkinleştirin ve test edin,

   a. **OıDC veya Oath uygulamaları için**uygulamanızı çok kiracılı bir uygulama olarak [kaydedin](../develop/quickstart-register-app.md) . Desteklenen hesap türlerinde herhangi bir kurumsal dizin ve kişisel Microsoft hesabı seçeneğinde hesapları seçin

   b. **SAML ve WS-Bessel tabanlı uygulamalar için**, Azure AD 'de genel bir SAML şablonu kullanarak [SAML tabanlı çoklu oturum açma uygulamalarını yapılandırırsınız](configure-single-sign-on-non-gallery-applications.md) .

Ayrıca, gerekirse [tek kiracılı bir uygulamayı birden çok kiracıya dönüştürebilirsiniz](../develop/howto-convert-app-to-be-multi-tenant.md) .

## <a name="next-steps"></a>Sonraki Adımlar

[Uygulamanızda SSO 'yu tümleştirme](isv-sso-content.md)
