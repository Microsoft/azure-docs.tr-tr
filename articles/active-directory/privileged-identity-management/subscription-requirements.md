---
title: Privileged Identity Management kullanılacak lisans gereksinimleri-Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) kullanmak için lisans gereksinimlerini açıklar.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6e336ec40ba2b9f6d3018e6a4f5b2ac721077aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895109"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Privileged Identity Management kullanılacak lisans gereksinimleri

Azure Active Directory (Azure AD) Privileged Identity Management (PıM) kullanmak için, bir dizinin geçerli bir lisansı olması gerekir. Ayrıca, lisansların Yöneticiler ve ilgili kullanıcılara atanması gerekir. Bu makalede Privileged Identity Management kullanmak için lisans gereksinimleri açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Privileged Identity Management kullanmak için, dizininiz aşağıdaki ücretli veya deneme lisanslarından birine sahip olmalıdır:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Daha fazla bilgi için bkz. [Azure Active Directory nedir?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Hangi kullanıcıların Lisansı olmalıdır?

Privileged Identity Management avantajlarından sorumlu olan veya bir avantajdan yararlanan her yönetici veya Kullanıcı bir lisansa sahip olmalıdır. Örneğin:

- Azure AD rollerine yönelik PıM kullanılarak yönetilen Yöneticiler
- Azure Kaynak rolleri ile PıM kullanılarak yönetilen Yöneticiler
- Ayrıcalıklı rol yöneticisi rolüne atanan yöneticiler
- PıM kullanılarak yönetilen Azure AD rollerine uygun olarak atanan kullanıcılar
- Kullanıcı PıM 'de istekleri onaylayabilir/reddedebiliyor
- Tam zamanında veya doğrudan (zaman tabanlı) atamalarla bir Azure Kaynak rolüne atanan kullanıcılar  
- Erişim gözden geçirmesi için atanan kullanıcılar
- Erişim gözden geçirmeleri gerçekleştiren kullanıcılar

Kullanım için lisansları atama hakkında daha fazla bilgi için, bkz. [Azure Active Directory portalını kullanarak lisans atama veya kaldırma](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>Lisansın süresi dolarsa ne olur?

Bir Azure AD Premium P2, EMS E5 veya deneme lisansının süresi dolarsa Privileged Identity Management özellikler artık dizininizde kullanılamaz:

- Azure AD rollerine yönelik kalıcı rol atamaları bundan etkilenmeyecektir.
- Azure portal Privileged Identity Management hizmetinin yanı sıra Privileged Identity Management Graph API cmdlet 'leri ve PowerShell arabirimleri artık kullanıcıların ayrıcalıklı rolleri etkinleştirmesine, ayrıcalıklı erişimi yönetmesine veya gerçekleştirmesine izin vermez ayrıcalıklı rollerin erişim gözden geçirmeleri.
- Azure AD rollerinin uygun rol atamaları kaldırılacak, çünkü kullanıcılar artık ayrıcalıklı rolleri etkinleştiremeyecektir.
- Azure AD rollerinin devam eden tüm erişim İncelemeleri sona acaktır ve Privileged Identity Management yapılandırma ayarları kaldırılır.
- Privileged Identity Management, artık rol atama değişikliklerinde e-posta göndermeyecektir.

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management dağıt](pim-deployment-plan.md)
- [Privileged Identity Management kullanmaya başlayın](pim-getting-started.md)
- [Privileged Identity Management içinde yönetemezsiniz roller](pim-roles.md)
