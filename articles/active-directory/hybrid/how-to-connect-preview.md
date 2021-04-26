---
title: 'Azure AD Connect: Önizlemedeki Özellikler | Microsoft Docs'
description: Bu konuda, Azure AD Connect önizleme aşamasında olan daha ayrıntılı özellikler açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d48cd8f95ecaf3e537a5221d766af150a51aa31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517693"
---
# <a name="more-details-about-features-in-preview"></a>Önizlemedeki özellikler hakkında daha fazla ayrıntı
Bu konuda, şu anda önizleme aşamasında olan özelliklerin nasıl kullanılacağı açıklanmaktadır.

## <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Azure AD Connect eşitleme v2 uç nokta API’si

Azure Active Directory için eşitleme hizmeti işlemlerinin performansını geliştiren Azure AD Connect için yeni bir uç nokta (API) dağıttık. Yeni v2 uç noktasından yararlanarak, Azure AD 'ye dışarı ve içeri aktarma sırasında dikkat çekici performans kazanımları yaşarsınız. Bu yeni uç nokta, en fazla 250 k üye içeren grupları eşitlemeyi da destekler. Bu uç noktanın kullanılması Ayrıca, grup geri yazma özelliği etkinken, şirket içi Active Directory en fazla üyelik sınırı olmadan Microsoft 365 birleştirilmiş gruplar yazmanızı sağlar. Daha fazla bilgi için bkz. [Sync v2 ENDPOINT apı Azure AD Connect](how-to-connect-sync-endpoint-api-v2.md).

## <a name="user-writeback"></a>Kullanıcı geri yazma
> [!IMPORTANT]
> Kullanıcı geri yazma önizleme özelliği Azure AD Connect için Ağustos 2015 güncelleştirmesinde kaldırılmıştır. Bunu etkinleştirdiyseniz, bu özelliği devre dışı bırakmanız gerekir.
>
>

## <a name="next-steps"></a>Sonraki adımlar
[Azure AD Connect özel yüklemenize](how-to-connect-install-custom.md)devam edin.

[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
