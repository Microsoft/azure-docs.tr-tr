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
ms.topic: how-to
ms.subservice: pim
ms.date: 04/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d0428f73c4df3d97002def03823ff8cced1d647
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552640"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Privileged Identity Management kullanılacak lisans gereksinimleri

Azure Active Directory (Azure AD) Privileged Identity Management (PıM) kullanmak için, bir dizinin geçerli bir lisansı olması gerekir. Ayrıca, lisansların Yöneticiler ve ilgili kullanıcılara atanması gerekir. Bu makalede Privileged Identity Management kullanmak için lisans gereksinimleri açıklanmaktadır.

## <a name="valid-licenses"></a>Geçerli lisanslar

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]PIM ve tüm ayarlarını kullanmanız gerekir. Şu anda, kiracınızda etkin bir Azure Active Directory Premium P2 sürümü ile Azure AD ve Azure Kaynak rolleri 'ne (Önizleme) erişimi olan hizmet sorumlularına yönelik bir erişim incelemesi tanımlayabilirsiniz. Hizmet sorumluları için lisanslama modeli, bu özelliğin genel kullanıma sunulmasıyla sonlandırılır ve ek lisanslar gerekebilir.

## <a name="licenses-you-must-have"></a>Sahip olmanız gereken lisanslar

Aşağıdaki görevleri yerine getirmek istediğiniz çalışanlara sahip olduğunuz için, dizininizde en az sayıda Azure AD Premium P2 lisansa sahip olduğundan emin olun:

- Azure AD veya PıM kullanılarak yönetilen Azure rollerine uygun olarak atanan kullanıcılar
- Uygun Üyeler veya ayrıcalıklı erişim gruplarının sahipleri olarak atanan kullanıcılar
- PıM 'de etkinleştirme isteklerini onaylayabilecek veya reddedebilen kullanıcılar
- Erişim gözden geçirmesi için atanan kullanıcılar
- Erişim gözden geçirmeleri gerçekleştiren kullanıcılar

Aşağıdaki görevler için Azure AD Premium P2 lisansları gerekli **değildir** :

- PıM 'yi ayarlama, ilkeleri yapılandırma, uyarıları alma ve erişim incelemelerini ayarlama kullanıcıları için herhangi bir lisans gerekmez.

Lisanslar hakkında daha fazla bilgi için bkz. [Azure Active Directory portalını kullanarak lisans atama veya kaldırma](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Örnek lisans senaryoları

Sahip olmanız gereken lisans sayısını belirlemenize yardımcı olacak bazı örnek lisans senaryoları aşağıda verilmiştir.

| Senaryo | Hesaplama | Lisans sayısı |
| --- | --- | --- |
| Woodgrove Bank, farklı departmanlar için 10 yönetici ve PıM 'yi yapılandıran ve yöneten 2 genel yönetici vardır. Bunlar beş yönetici için uygun hale getirir. | Uygun yöneticiler için beş lisans | 5 |
| Grafik tasarımı Enstitüsü, 14 ' ün PıM aracılığıyla yönettiği 25 yöneticileridir. Rol etkinleştirme onay gerektirir ve kuruluşta etkinleştirmeleri onaylayabilecek üç farklı kullanıcı vardır. | uygun roller ve üç onaylayan için 14 lisans | 17 |
| Contoso, 50 'e 42 ait, PıM aracılığıyla yönetilmekte olan yöneticilerine sahiptir. Rol etkinleştirme onay gerektirir ve kuruluşta etkinleştirmeleri onaylayabilecek beş farklı kullanıcı vardır. Contoso Ayrıca, yönetici rollerine atanan kullanıcılara yönelik aylık incelemeler ve gözden geçirenler, kullanıcının PıM tarafından yönetilen yönetici rollerde bulunmayan Kullanıcı yöneticilerinde de bulunur. | uygun rollere yönelik 42 lisans + beş onaylayan + altı gözden geçiren | 53 |

## <a name="when-a-license-expires"></a>Bir Lisansın süresi dolarsa

Bir Azure AD Premium P2, EMS E5 veya deneme lisansının süresi dolarsa Privileged Identity Management özellikler artık dizininizde kullanılamaz:

- Azure AD rollerine yönelik kalıcı rol atamaları bundan etkilenmeyecektir.
- Azure portal Privileged Identity Management hizmetinin yanı sıra Privileged Identity Management Graph API cmdlet 'leri ve PowerShell arabirimleri artık kullanıcıların ayrıcalıklı rolleri etkinleştirmesine, ayrıcalıklı erişimi yönetmesine veya ayrıcalıklı rollere yönelik erişim gözden geçirmeleri gerçekleştirmesine izin vermez.
- Azure AD rollerinin uygun rol atamaları kaldırılacak, çünkü kullanıcılar artık ayrıcalıklı rolleri etkinleştiremeyecektir.
- Azure AD rollerinin devam eden tüm erişim İncelemeleri sona acaktır ve Privileged Identity Management yapılandırma ayarları kaldırılır.
- Privileged Identity Management, artık rol atama değişikliklerinde e-posta göndermeyecektir.

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management'ı dağıtma](pim-deployment-plan.md)
- [Privileged Identity Management'ı kullanmaya başlama](pim-getting-started.md)
- [Privileged Identity Management içinde yönetileyemiyorum roller](pim-roles.md)
- [PıM 'de Azure Kaynak rolleri için erişim gözden geçirmesi oluşturma](pim-resource-roles-start-access-review.md)
- [PıM 'de Azure AD rolleri için erişim gözden geçirmesi oluşturma](pim-how-to-start-security-review.md)
