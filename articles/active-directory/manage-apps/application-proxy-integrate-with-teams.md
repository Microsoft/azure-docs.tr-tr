---
title: Azure AD uygulama ara sunucusu uygulamaları teams'de erişim | Microsoft Docs
description: Şirket içi uygulamanızı Microsoft Teams erişmek için Azure AD uygulama proxy'si kullanın.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 037e005993a54e525560571a6d893197af99b6a0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807768"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Şirket içi uygulamalarınızı, Microsoft Teams erişim

Azure Active Directory Uygulama Ara sunucusu, çoklu oturum açma nerede olursa olsun şirket içi uygulamalara sağlar. Microsoft Teams, tek bir yerde işbirliği çalışmalarınızı kolaylaştırır. İki birlikte tümleştirilmesi, kullanıcılarınızın üretken ekip herhangi bir durumda olabilir anlamına gelir.

Kullanıcılarınıza bulut uygulamaları için takımlar kanallarını da ekleyebilirsiniz [sekmeleri kullanarak](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), ancak ne şirket içi SharePoint siteleri veya olan planlama aracı barındırılan? Uygulama proxy'si çözümüdür. Bunlar her zaman uygulamalarını uzaktan erişmek için kullandıkları aynı dış URL'leri kullanarak kendi kanallara uygulama proxy'si aracılığıyla yayımlanan uygulamalar ekleyebilirsiniz. Ve uygulama proxy'si Azure Active Directory doğruladığından, kullanıcılarınızın çoklu oturum açma deneyimini elde edin.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Uygulama Ara sunucusu bağlayıcısını yüklemek ve uygulamanızı yayımlayın

Henüz kaydolmadıysanız [kiracınızda uygulama proxy'sini yapılandırmak ve bağlayıcıyı yükleyin](application-proxy-add-on-premises-application.md). Ardından, [şirket içi uygulamanızı yayımlayın](application-proxy-add-on-premises-application.md) uzaktan erişim için. Uygulamayı yayımlarken Teams'e uygulama ekleme için kullanıldığından dış URL'yi not edin.

Zaten varsa yayımlanan uygulamalarınızı ancak kendi URL'lere hatırlamıyorum bunları içinde aramak [Azure portalında](https://portal.azure.com). Oturum açın ve ardından gitmek **Azure Active Directory** > **kurumsal uygulamalar** > **tüm uygulamaları** > uygulamanızı seçin >  **Uygulama proxy'si**.

## <a name="add-your-app-to-teams"></a>Teams'e uygulama ekleme

Uygulama proxy'si aracılığıyla uygulama yayımladıktan sonra bunlar bir sekmeyi doğrudan kendi takımlar kanalları olarak ekleyebilir ve ardından uygulamayı herkes kullanmak için takım bilmeniz imkan sağlar. Şu üç adımı takip sağlayın:

1. Bu uygulama ekleme ve seçmek için istediğiniz Teams kanalına gidin **+** sekme eklemek için.

   ![Seçin + Teams'de sekme eklemek için](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Seçin **Web sitesi** sekmesi seçenekleri.

   ![Web sitesi Ekle sekmesi ekran seçin](./media/application-proxy-integrate-with-teams/website.png)

1. Sekme bir ad verin ve URL için uygulama proxy'si dış URL'yi ayarlayın.

   ![Sekme adını ve dış URL Ekle](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Bir takım üyesi sekmesi ekler. sonra bu kanal herkes için gösterilir. Uygulamaya sahip tüm kullanıcılar için Microsoft Teams kullandıkları kimlik bilgileri ile çoklu oturum açma erişimi alın. Uygulamaya sahip olmayan kullanıcılardan teams'de sekmesinde görebilirsiniz, ancak bunları izinleri şirket içi uygulama ve uygulamayı Azure portalı yayımlanmış sürümüne size kadar engellenir.

## <a name="next-steps"></a>Sonraki adımlar

- Bilgi edinmek için nasıl [şirket içi SharePoint siteleri yayımlama](application-proxy-integrate-with-sharepoint-server.md) uygulama ara sunucusu ile.
- Uygulamalarınızı kullanmak için yapılandırma [özel etki alanları](application-proxy-configure-custom-domain.md) , dış URL.
