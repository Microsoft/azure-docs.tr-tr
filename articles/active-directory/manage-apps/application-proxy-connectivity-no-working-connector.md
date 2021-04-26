---
title: Uygulama ara sunucusu uygulaması için çalışan bağlayıcı grubu bulunamadı
description: Azure AD Uygulama Ara Sunucusu, uygulamanız için bir bağlayıcı grubunda hiç çalışma Bağlayıcısı olmadığında karşılaşabileceğiniz sorunları ele alabilirsiniz
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
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
ms.openlocfilehash: cec028dec2bf2e6295f59aae9c2e243da2209827
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257043"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Uygulama ara sunucusu uygulaması için çalışan bağlayıcı grubu bulunamadı

Bu makale, Azure Active Directory ile tümleştirilmiş bir uygulama proxy uygulaması için algılanan bir bağlayıcı olmadığında ortaya çıkan yaygın sorunları gidermeye yardımcı olur.

## <a name="overview-of-steps"></a>Adımlara genel bakış
Uygulamanız için bir bağlayıcı grubunda çalışma Bağlayıcısı yoksa, sorunu çözmek için birkaç yol vardır:

-   Grupta hiç bağlayıcı yoksa şunları yapabilirsiniz:

    -   Sağ taraftaki Şirket sunucusunda yeni bir bağlayıcı indirin ve bu gruba atayın

    -   Etkin bir bağlayıcıyı gruba taşıma

-   Grupta etkin bağlayıcı yoksa şunları yapabilirsiniz:

    -   Bağlayıcının etkin olmaması ve çözümlenmesi nedenini belirlemek

    -   Etkin bir bağlayıcıyı gruba taşıma

Sorunu anlamak için uygulamanızdaki "uygulama proxy 'Si" menüsünü açın ve bağlayıcı grubu uyarı iletisine bakın. Grupta hiç bağlayıcı yoksa, uyarı iletisi grubun en az bir bağlayıcıya ihtiyacı olduğunu belirtir. Etkin bağlayıcılar yoksa, uyarı iletisi bunu açıklar. Etkin olmayan bağlayıcılar olması yaygındır. 

   ![Azure portal bağlayıcı grubu seçimi](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Bu seçeneklerin her biri hakkında ayrıntılı bilgi için aşağıdaki ilgili bölüme bakın. Yönergeler bağlayıcı Yönetim sayfasından başlattığınızı varsayar. Yukarıdaki hata iletisine bakıyorsanız, uyarı iletisine tıklayarak bu sayfaya gidebilirsiniz. Ayrıca, **Azure Active Directory** gidip **Kurumsal uygulamalar**' a ve ardından **uygulama proxy 'si** ' ne tıklayarak da sayfaya ulaşabilirsiniz.

   ![Azure portal 'de bağlayıcı grubu yönetimi](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Yeni bir bağlayıcı indirin

Yeni bir bağlayıcıyı indirmek için sayfanın üst kısmındaki "bağlayıcıyı Indir" düğmesini kullanın.

Bağlayıcıyı, arka uç uygulamasına doğrudan görüş satırı olan bir makineye yükler. Genellikle bağlayıcı, uygulamayla aynı sunucuya yüklenir. İndirmeden sonra bağlayıcının bu menüde görünmesi gerekir. Bağlayıcıya tıklayın ve sağ gruba ait olduğundan emin olmak için "bağlayıcı grubu" açılan simgesini kullanın. Yaptığınız değişikliği kaydedin.

   ![Azure portal bağlayıcıyı indirin](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Etkin Bağlayıcıyı taşıma

Gruba ait olması gereken ve hedef arka uç uygulamasına bir görüş satırı içeren etkin bir bağlayıcınız varsa, bağlayıcıyı atanan gruba taşıyabilirsiniz. Bunu yapmak için bağlayıcıya tıklayın. "Bağlayıcı grubu" alanında, doğru grubu seçmek için açılan eklentiyi kullanın ve Kaydet ' e tıklayın.

## <a name="resolve-an-inactive-connector"></a>Etkin olmayan bir bağlayıcıyı çözümle

Gruptaki tek bağlayıcılar devre dışı bırakılırsa, büyük olasılıkla gerekli olan tüm bağlantı noktalarının engeli kaldırılmış bir makinedir.

Bu sorunu araştırmaya ilişkin ayrıntılar için bkz. belge sorunlarını giderme bilgileri.

## <a name="next-steps"></a>Sonraki adımlar
[Azure AD Uygulama Ara Sunucusu bağlayıcılarını anlama](application-proxy-connectors.md)


