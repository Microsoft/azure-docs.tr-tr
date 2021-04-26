---
title: Azure RBAC için en iyi uygulamalar
description: Azure rol tabanlı erişim denetimi (Azure RBAC) kullanmaya yönelik en iyi uygulamalar.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: d58398c42cdc6faed758e5dba3431e0841fc0b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555566"
---
# <a name="best-practices-for-azure-rbac"></a>Azure RBAC için en iyi uygulamalar

Bu makalede, Azure rol tabanlı erişim denetimi (Azure RBAC) kullanmaya yönelik bazı en iyi yöntemler açıklanmaktadır. Bu en iyi uygulamalar, Azure RBAC ile deneyimimizin yanı sıra sizin gibi müşterilerin deneyimlerini elde edilmiştir.

## <a name="only-grant-the-access-users-need"></a>Yalnızca kullanıcılara ihtiyacı olan erişime izin ver

Azure RBAC kullanarak ekibiniz içinde görevleri ayırabilir, bu işlere gerek duyan kişilere sadece erişim miktarını verebilirsiniz. Herkese Azure aboneliğiniz veya kaynaklarınızda sınırsız izin vermek yerine belirli eylemlere yalnızca belirli kapsamlarda izin verebilirsiniz.

Erişim denetimi stratejinizi planlarken en iyi yöntem, kullanıcılara yalnızca işlerini yapmak için gerekli olan ayrıcalığı tanımaktır. Daha geniş bir rolün daha kolay bir şekilde daha kolay görünse bile daha geniş kapsamlar atamaktan kaçının. Özel roller oluştururken, yalnızca kullanıcıların ihtiyacı olan izinleri ekleyin. Rolleri ve kapsamları sınırlayarak, güvenlik sorumlusu tehlikeye girerse, hangi kaynakların risk altında olduğunu sınırlayabilirsiniz.

Aşağıdaki diyagramda Azure RBAC kullanımı için önerilen bir model gösterilmektedir.

![Azure RBAC ve en düşük ayrıcalık](./media/best-practices/rbac-least-privilege.png)

Rol atama hakkında daha fazla bilgi için bkz. [Azure Portal kullanarak Azure rolleri atama](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Abonelik sahiplerinin sayısını sınırlayın

Güvenliği aşılmış bir sahibe göre ihlal olasılığını azaltmak için en fazla 3 abonelik sahibi olmanız gerekir. Bu öneri, Azure Güvenlik Merkezi ' nde izlenebilir. Güvenlik Merkezi 'ndeki diğer kimlik ve erişim önerileri için bkz. [güvenlik önerileri-bir başvuru kılavuzu](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management kullan

Ayrıcalıklı hesapları kötü niyetli Cyber saldırılarına karşı korumak için Azure Active Directory Privileged Identity Management (PıM) kullanarak ayrıcalıkların etkilenme süresini azaltır ve raporlar ve uyarılar aracılığıyla kullanım için görünürlüğünüzü artırabilirsiniz. PıM, Azure AD ve Azure kaynaklarına tam zamanında ayrıcalıklı erişim sağlayarak ayrıcalıklı hesapların korunmasına yardımcı olur. Erişim, otomatik olarak iptal edilen ayrıcalıkların zaman sınırı olabilir. 

Daha fazla bilgi için bkz. [Azure AD Privileged Identity Management nedir?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="assign-roles-to-groups-not-users"></a>Kullanıcılara değil gruplara roller atama

Rol atamalarını daha yönetilebilir hale getirmek için, rolleri doğrudan kullanıcılara atamaktan kaçının. Bunun yerine, gruplara roller atayın. Kullanıcılar yerine gruplara rol atama, [her abonelik için 2.000 rol ataması sınırlaması](troubleshooting.md#azure-role-assignments-limit)olan rol atamalarının sayısını en aza indirmenize de yardımcı olur. 

## <a name="next-steps"></a>Sonraki adımlar

- [Azure RBAC sorunlarını giderme](troubleshooting.md)
