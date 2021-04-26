---
title: Privileged Identity Management 'da ayrıcalıklı Azure AD gruplarını yönetme (PıM) | Microsoft Docs
description: Privileged Identity Management (PıM) içinde ayrıcalıklı erişim gruplarının üyelerini ve sahiplerini yönetme
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759781fd61cd42d05f2823d390e99d128dd2fcac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512401"
---
# <a name="management-capabilities-for-privileged-access-azure-ad-groups-preview"></a>Ayrıcalıklı erişim için yönetim özellikleri Azure AD grupları (Önizleme)

Privileged Identity Management (PıM) ' de, artık ayrıcalıklı erişim gruplarının üyeliği veya sahipliği için uygunluk atayabilirsiniz. Bu önizlemeden başlayarak bulut gruplarına Azure Active Directory (Azure AD) yerleşik roller atayabilir ve grup üyesini ve sahip uygunluk ve etkinleştirmeyi yönetmek için PıM kullanabilirsiniz. Azure AD 'de rol atanabilir gruplar hakkında daha fazla bilgi için bkz. [Azure Active Directory (Önizleme) içinde rol atamalarını yönetmek için bulut gruplarını kullanma](../roles/groups-concept.md).

>[!Important]
> Exchange, güvenlik ve Uyumluluk Merkezi veya SharePoint 'e yönetim erişimi sağlamak üzere bir role ayrıcalıklı erişim grubu atamak için, Kullanıcı veya grubun etkinleştirmesine uygun hale getirmek için ayrıcalıklı erişim grupları deneyiminizde değil, Azure AD portalı **rolleri ve yöneticiler** deneyimini kullanın.

## <a name="require-different-policies-for-each-role-assignable-group"></a>Atanabilir her bir rol için farklı ilkeler gerektir

Bazı kuruluşlar, iş ortaklarını Azure AD kuruluşlarına Konuk olarak davet etmek için Azure AD işletmeden işletmeye (B2B) işbirliği gibi araçlar kullanır. Ayrıcalıklı bir role yönelik tüm atamalar için tek bir tam zamanında ilke yerine, kendi ilkeleriyle iki farklı ayrıcalıklı erişim grubu oluşturabilirsiniz. Güvenilen çalışanlarınız için daha az katı gereksinimler ve kendi atanan grubuna etkinleştirme istediklerinde iş ortaklarınız için onay iş akışı gibi daha sıkı gereksinimlere zorlayabilirsiniz.

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>Tek bir istekte birden çok rol atamasını etkinleştirme

Ayrıcalıklı erişim grupları önizlemesi sayesinde, tek bir tam zamanında isteği olan birden çok role iş yüküne özgü yöneticilere hızlı erişim sağlayabilirsiniz. Örneğin, katman 3 Office yöneticilerinin, olayları günlük olarak araştırmak için Exchange Yöneticisi, Office uygulamaları Yöneticisi, takımlar Yöneticisi ve arama yönetici rollerine tam zamanında erişimi olması gerekebilir. Bugünden önce dört ardışık istek gerektirir ve bu işlem biraz zaman alır. Bunun yerine, "Katman 3 Office yöneticileri" adlı bir rol atanabilir grup oluşturabilir, daha önce bahsedilen dört rolün (veya Azure AD yerleşik rollerinin) her birine atayabilir ve grubun etkinlik bölümünde ayrıcalıklı erişim için etkinleştirebilirsiniz. Ayrıcalıklı erişim için etkinleştirildikten sonra, grubun üyeleri için tam zamanında ayarları yapılandırabilir ve yönetici ve sahiplerinizi uygun şekilde atayabilirsiniz. Yöneticiler gruba yükseltirse, tüm dört Azure AD rollerinin üyesi olurlar.

## <a name="extend-and-renew-group-assignments"></a>Grup atamalarını genişletme ve yenileme

Zamana bağlama sahip veya üye atamalarınızı ayarladıktan sonra, bir atamanın süresi dolarsa sorabileceğiniz ilk soru ne olur? Bu yeni sürümde, bu senaryo için iki seçenek sunuyoruz:

- Uzat – bir rol ataması süre sonu yaklaştığında Kullanıcı, rol ataması için bir uzantı istemek üzere Privileged Identity Management kullanabilir
- Yenile – bir rol atamasının süresi dolduğunda Kullanıcı, rol ataması için bir yenileme isteğinde bulunan Privileged Identity Management kullanabilir

Kullanıcı tarafından başlatılan eylemlerin her ikisi de genel yönetici veya ayrıcalıklı rol yöneticisinden onay gerektirir. Yöneticilerin artık bu süre sonlarını yönetme işinde ihtiyacı olmayacaktır. Yalnızca uzantı veya yenileme isteklerini bekleyebilir, istek geçerliyse bunları onaylayabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Ayrıcalıklı erişim grubu sahibini veya üyesini atama](groups-assign-member-owner.md)
- [Ayrıcalıklı erişim grubu üyeleri ve sahipleri için etkinleştirme isteklerini onaylama veya reddetme](groups-approval-workflow.md)
