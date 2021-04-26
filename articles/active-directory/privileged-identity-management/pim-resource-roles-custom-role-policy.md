---
title: PıM-Azure AD 'de Azure özel rolleri kullanma | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde Azure özel rollerini nasıl kullanacağınızı öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24b7845ec66a85e6ced4f1df9caec409a94016bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88782609"
---
# <a name="use-azure-custom-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure özel rollerini kullanma

Bağımsız çalışma sınırı (Azure AD) kuruluşunuzda ayrıcalıklı bir Azure Active Directory roldeki bazı kullanıcılara katı Privileged Identity Management (PıM) ayarları uygulamanız gerekebilir, diğerleri için daha fazla sağlayabilirsiniz. Örneğin, kuruluşunuzun çeşitli sözleşmeleri bir Azure aboneliğinde çalışacak bir uygulama geliştirmede yardımcı olacak şekilde ilişkilendiğini göz önünde bulundurun.

Bir kaynak yöneticisi olarak, çalışanların onaya gerek duymadan erişim için uygun olmasını istersiniz. Ancak, kuruluşun kaynaklarına erişim istediklerinde tüm sözleşme ilişkilendirmeleri onaylanmalıdır.

Azure Kaynak rollerinin hedeflenen Privileged Identity Management ayarlarını yapmak için sonraki bölümde özetlenen adımları izleyin.

## <a name="create-the-custom-role"></a>Özel rol oluşturma

Bir kaynak için özel bir rol oluşturmak üzere [Azure özel rolleri](../../role-based-access-control/custom-roles.md)' nde açıklanan adımları izleyin.

Özel rol oluşturduğunuzda, yinelemek istediğiniz yerleşik rolü kolayca hatırlayabilmeniz için açıklayıcı bir ad ekleyin.

> [!NOTE]
> Özel rolün, çoğaltmak istediğiniz yerleşik rolün bir yinelemesi olduğundan ve kapsamının yerleşik rolle eşleştiğinden emin olun.

## <a name="apply-pim-settings"></a>PıM ayarlarını uygula

Rol Azure AD kuruluşunuzda oluşturulduktan sonra, Azure portal **Privileged Identity Management-Azure kaynakları** sayfasına gidin. Rolün uygulandığı kaynağı seçin.

!["Privileged Identity Management-Azure kaynakları" bölmesi](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

Rolün bu üyeleri için uygulanması gereken [Privileged Identity Management rol ayarlarını yapılandırın](pim-resource-roles-configure-role-settings.md) .

Son olarak, bu ayarlarla hedeflemek istediğiniz farklı üye grubuna [Roller atayın](pim-resource-roles-assign-roles.md) .

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure Kaynak rolü ayarlarını yapılandırma](pim-resource-roles-configure-role-settings.md)
- [Azure'da özel roller](../../role-based-access-control/custom-roles.md)