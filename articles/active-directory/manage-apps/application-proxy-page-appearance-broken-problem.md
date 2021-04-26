---
title: Uygulama sayfası uygulama proxy 'Si uygulaması için doğru görüntülenmez | Microsoft Docs
description: Azure AD ile Tümleştirdiğiniz bir uygulama proxy uygulamasında sayfanın doğru görüntülenmediği kılavuz
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99b09e7b15427eb33e1e85edd89f4c8a37c4a3eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254663"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Uygulama sayfası bir uygulama proxy 'Si uygulaması için doğru görüntülenmez

Bu makale, sayfaya gittiğinizde Azure Active Directory Uygulama Ara Sunucusu uygulamalarla ilgili sorunları gidermenize yardımcı olur, ancak sayfadaki bir şey doğru görünmüyor.

## <a name="overview"></a>Genel Bakış
Bir uygulama proxy 'Si uygulaması yayımladığınızda, uygulamaya erişirken yalnızca köklerinizin altındaki sayfalara erişilebilir. Sayfa doğru görüntülenmiyorsa, uygulama için kullanılan kök iç URL 'nin bazı sayfa Kaynakları eksik olabilir. Sorunu çözmek için, sayfanın *Tüm* kaynaklarını uygulamanızın bir parçası olarak yayımladığınızdan emin olun.

Ağ izleyicinizi (ör. Fiddler veya Internet Explorer/Microsoft Edge 'de F12 araçları) açarak, sayfayı yükleyerek ve 404 hata arayarak sorunu eksik kaynaklarda doğrulayabilirsiniz. Bu, şu an sayfaların bulunamadığını ve bunları yayımlamanız gerektiğini gösterir.

Bu durumda bir örnek olarak, iç URL 'yi kullanarak bir giderler uygulaması yayımladığınızı varsayın `http://myapps/expenses` , ancak uygulama stil sayfasını kullanır `http://myapps/style.css` . Bu durumda, stil sayfası uygulamanızda yayımlanmaz, böylece giderler uygulamasının yüklenmesi, Style. css ' yi yüklemeye çalışırken 404 hatası oluşturur. Bu örnekte, bir iç URL ile uygulama yayımlayarak sorun çözülür `http://myapp/` .

## <a name="problems-with-publishing-as-one-application"></a>Tek bir uygulama olarak yayımlama sorunları

Aynı uygulamadaki tüm kaynakları yayımlamak mümkün değilse, birden fazla uygulama yayımlamanız ve aralarında bağlantıları etkinleştirmeniz gerekir.

Bunu yapmak için [özel etki alanları](application-proxy-configure-custom-domain.md) çözümünü kullanmanızı öneririz. Ancak bu çözüm, etki alanınız için sertifikaya sahip olmanızı gerektirir ve uygulamalarınız tam etki alanı adlarını (FQDN) kullanır. Diğer seçenekler için bkz. [bozuk bağlantıları sorun giderme belgeleri](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Sonraki adımlar
[Azure AD Uygulama Ara Sunucusu ile uygulama yayımlama](application-proxy-add-on-premises-application.md)
