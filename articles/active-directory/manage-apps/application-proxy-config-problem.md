---
title: Uygulama proxy 'Si uygulaması oluşturma sorunu | Microsoft Docs
description: Azure AD yönetim portalında uygulama proxy 'Si uygulamaları oluşturma sorunlarını giderme
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
ms.openlocfilehash: c0c88b90d0caa2f723921d76b5f9dc036895f14e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258023"
---
# <a name="problem-creating-an-application-proxy-application"></a>Uygulama proxy 'Si uygulaması oluşturma sorunu 

Aşağıda, yeni bir uygulama proxy uygulaması oluştururken insanların karşılaştığı yaygın sorunlardan bazıları verilmiştir.

## <a name="recommended-documents"></a>Önerilen belgeler 

Yönetici portalı aracılığıyla uygulama proxy 'Si uygulaması oluşturma hakkında daha fazla bilgi edinmek için bkz. [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](application-proxy-add-on-premises-application.md).

Bu belgedeki adımları takip ediyorsanız ve uygulamayı oluştururken hata alıyorsanız, uygulamanın nasıl düzeltileceğini gösteren bilgi ve öneriler için hata ayrıntılarına bakın. Çoğu hata iletisi önerilen bir çözümü içerir. 

## <a name="specific-things-to-check"></a>Denetlenecek belirli şeyler

Yaygın hatalardan kaçınmak için şunları doğrulayın:

-   Uygulama proxy 'Si uygulaması oluşturma izni olan bir yöneticiniz

-   İç URL benzersizdir

-   Dış URL benzersizdir

-   URL 'Ler http veya https ile başlar ve bir "/" ile biter

-   URL, bir etki alanı adı olmalı ve bir IP adresi olmamalıdır

Hata iletisi, uygulamayı oluştururken sağ üst köşede görüntülenmelidir. Hata iletilerini görmek için bildirim simgesini de seçebilirsiniz.

   ![Bildirim istemi](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Sonraki adımlar
[Azure portalında Uygulama Ara Sunucusunu etkinleştirme](application-proxy-add-on-premises-application.md)
