---
title: 'Nasıl yapılır: bir uygulama tarafından desteklenen hesap türlerini değiştirme | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu nasıl yapılır, Microsoft Identity platformu ile kayıtlı olan bir uygulamayı, uygulamayı veya hangi hesapların erişebileceğini değiştirmek üzere yapılandırırsınız.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 3ae6616263de605d5910f244423b9e7ffc036c5d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080598"
---
# <a name="how-to-modify-the-accounts-supported-by-an-application"></a>Bir uygulama tarafından desteklenen hesapları değiştirme

Uygulamanızı Microsoft Identity platformu ile kaydettiğinizde, hangi hesap türlerinin erişebileceğini belirtdiniz. Örneğin, yalnızca kuruluşunuzda *tek kiracılı* bir uygulama olan hesapları belirtmiş olabilirsiniz. Ya da, *çok kiracılı* bir uygulama olan herhangi bir kuruluşta (sizinkiler dahil) hesaplar belirtmiş olabilirsiniz.

Aşağıdaki bölümlerde, uygulamaya hangi tür hesapların erişebileceğini veya hangilerinin erişebileceğini değiştirmek için Azure portal uygulamanızın kaydını nasıl değiştireceğiniz hakkında bilgi edineceksiniz.

## <a name="prerequisites"></a>Önkoşullar

* [Azure AD kiracınızda kayıtlı bir uygulama](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>Uygulama kaydını farklı hesapları destekleyecek şekilde değiştirme

Var olan bir uygulama kaydı tarafından desteklenen hesap türleri için farklı bir ayar belirtmek için:

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet**' in altında **uygulama kayıtları**' yi seçin ve ardından uygulamanızı seçin.
1. Şimdi, bazen *oturum açma seyirci* olarak adlandırılan, uygulamayı kullanabilecek kişileri belirtin.

    | Desteklenen hesap türleri | Description |
    |-------------------------|-------------|
    | **Yalnızca bu kuruluş dizinindeki hesaplar** | *Kiracınızda* yalnızca kullanıcılar (veya konuklar) tarafından kullanılacak bir uygulama oluşturuyorsanız bu seçeneği belirleyin.<br><br>Genellikle, *iş kolu* (LOB) uygulaması olarak adlandırılan bu, Microsoft Identity platformunda **tek kiracılı** bir uygulamadır. |
    | **Herhangi bir kuruluş dizinindeki hesaplar** | *Herhangi bir* Azure AD kiracısındaki kullanıcıların uygulamanızı kullanabilmemizi istiyorsanız bu seçeneği belirleyin. Örneğin, birden çok kuruluşa sağlamak istediğiniz bir hizmet olarak yazılım (SaaS) uygulaması oluşturuyorsanız, bu seçenek uygundur.<br><br>Bu, Microsoft Identity platformunda **çok kiracılı** bir uygulama olarak bilinir. |
1. **Kaydet**’i seçin.

### <a name="why-changing-to-multi-tenant-can-fail"></a>Çok kiracılı olarak değiştirilmesi neden başarısız olabilir

Uygulama KIMLIĞI URI 'SI (uygulama KIMLIĞI URI 'SI) ad çakışmaları nedeniyle, bir uygulama kaydını tek-çok kiracıdan değiştirmek bazen başarısız olabilir. Örnek bir uygulama KIMLIĞI URI 'SI `https://contoso.onmicrosoft.com/myapp` .

Uygulama Kimliği URI'si, uygulamanın protokol iletileri içinde tanımlanması için kullanılan yollardan biridir. Tek kiracılı bir uygulama için, uygulama KIMLIĞI URI 'SI yalnızca o kiracı içinde benzersiz olmalıdır. Çok kiracılı bir uygulama için Azure AD 'nin uygulamayı tüm kiracılarda bulabilmesi için genel olarak benzersiz olması gerekir. Genel benzersizlik, uygulama KIMLIĞI URI 'sinin ana bilgisayar adının Azure AD kiracının [doğrulanmış yayımcı etki alanlarından](howto-configure-publisher-domain.md)biriyle eşleşmesini gerektirerek zorlanır.

Örneğin, kiracınızın adı *contoso.onmicrosoft.com* ise `https://contoso.onmicrosoft.com/myapp` GEÇERLI bir uygulama kimliği URI 'si olur. Kiracınızda *contoso.com* etki alanı doğrulanmışsa, geçerli BIR uygulama kimliği URI 'si de olur `https://contoso.com/myapp` . Uygulama KIMLIĞI URI 'SI ikinci kalıbı izlemezse, `https://contoso.com/myapp` uygulama kaydını çok kiracılı hale dönüştürmek başarısız olur.

Doğrulanmış bir yayımcı etki alanını yapılandırma hakkında daha fazla bilgi için bkz. [doğrulanan etki alanını yapılandırma](howto-configure-publisher-domain.md).

## <a name="next-steps"></a>Sonraki adımlar

[Bir uygulamayı tek veya çok kiracılı olarak dönüştürme](howto-convert-app-to-be-multi-tenant.md)gereksinimleri hakkında daha fazla bilgi edinin.
