---
title: Ekipteki proxy uygulamalarına Azure AD Uygulaması erişim | Microsoft Docs
description: Microsoft ekipleri aracılığıyla şirket içi uygulamanıza erişmek için Azure AD Uygulama Ara Sunucusu kullanın.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/05/2017
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c44716f045340022c871501609cf582015ba20f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256618"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Şirket içi uygulamalarınıza Microsoft ekipleri aracılığıyla erişin

Azure Active Directory Uygulama Ara Sunucusu nerede olursanız olsun, şirket içi uygulamalarda çoklu oturum açma olanağı sunar. Microsoft ekipleri işbirliğine dayalı çabalarınızı tek bir yerde kolaylaştırır. İki birlikte tümleştirme, kullanıcılarınızın herhangi bir durumda ekiplerle üretken olabileceği anlamına gelir.

Kullanıcılarınız, [sekmeleri kullanarak](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US)kendi takımlarına bulut uygulamaları ekleyebilir, ancak şirket Içinde barındırılan SharePoint siteleri veya planlama aracı hakkında ne tür olabilir? Uygulama proxy 'Si çözümdür. Uygulama proxy 'Si aracılığıyla yayımlanan uygulamaları, her zaman uygulamalarına uzaktan erişmek için kullandıkları aynı dış URL 'Ler kullanılarak kanallara ekleyebilirler. Uygulama proxy 'Si Azure Active Directory aracılığıyla kimlik doğrulaması yaptığından, kullanıcılarınız çoklu oturum açma deneyimi alırlar.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Uygulama proxy bağlayıcısını yükleyip uygulamanızı yayımlayın

Henüz yapmadıysanız, [kiracınız Için uygulama proxy 'sini yapılandırın ve bağlayıcıyı yükleyebilirsiniz](application-proxy-add-on-premises-application.md). Ardından, Şirket [içi uygulamanızı](application-proxy-add-on-premises-application.md) uzaktan erişim için yayımlayın. Uygulamayı yayımlarken, uygulamayı takımlara eklemek için kullanıldığından dış URL 'yi unutmayın.

Uygulamalarınız zaten yayımladıysanız, ancak dış URL 'Lerini anımsamıyorsanız, [Azure Portal](https://portal.azure.com)bakın. Oturum açın ve **Azure Active Directory**  >  **Kurumsal uygulamalar**  >  **tüm uygulamalar** ' a gidin > **uygulama proxy 'si**> uygulamanızı seçin.

## <a name="add-your-app-to-teams"></a>Uygulamanızı takımlara ekleyin

Uygulamayı uygulama proxy 'Si aracılığıyla yayımladıktan sonra, kullanıcılarınıza onu doğrudan takımlar kanallarında bir sekme olarak ekleyebileceklerini ve sonra da ekibin kullanabilmesi için uygulamanın kullanılabilir olduğunu bilmesini sağlayın. Bu üç adımı takip edin:

1. Bu uygulamayı eklemek istediğiniz takımlar kanalına gidin ve **+** sekme eklemeyi seçin.

   ![Takımlara sekme eklemek için + seçeneğini belirleyin](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Sekme seçeneklerinden **Web sitesi** ' ni seçin.

   ![Sekme Ekle ekranından Web sitesi ' ni seçin](./media/application-proxy-integrate-with-teams/website.png)

1. Sekmeye bir ad verin ve URL 'YI uygulama proxy 'si dış URL 'si olarak ayarlayın.

   ![Sekmeyi adlandırın ve dış URL 'YI ekleyin](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Bir takımın bir üyesi sekmeyi eklediğinde, Kanaldaki herkes için görüntülenir. Uygulamaya erişimi olan tüm kullanıcılar, Microsoft ekipleri için kullandıkları kimlik bilgileriyle çoklu oturum açma erişimi sağlar. Uygulamaya erişimi olmayan tüm kullanıcılar ekiplerde sekmeyi görebilir, ancak kullanıcılara şirket içi uygulamaya ve uygulamanın yayımlanmış sürümüne Azure portal izin verene kadar engellenmiştir.

## <a name="next-steps"></a>Sonraki adımlar

- [Şirket Içi SharePoint sitelerini](application-proxy-integrate-with-sharepoint-server.md) uygulama proxy 'si ile yayımlamayı öğrenin.
- Uygulamalarınızı dış URL 'ler için [özel etki alanları](application-proxy-configure-custom-domain.md) kullanacak şekilde yapılandırın.
