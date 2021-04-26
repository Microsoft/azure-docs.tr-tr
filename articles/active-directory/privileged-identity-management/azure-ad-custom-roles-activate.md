---
title: Azure AD özel rolünü etkinleştirin-Privileged Identity Management (PıM)
description: Atama Privileged Identity Management için Azure AD özel rolünü etkinleştirme (PıM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c0d98641f8e2040de8350b7dd0231c2e7c889c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371622"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Privileged Identity Management bir Azure AD özel rolünü etkinleştirin

Azure Active Directory (Azure AD) Privileged Identity Management artık kimlik ve erişim yönetimi Yönetim deneyiminde uygulama yönetimi için oluşturulan özel rollere tam zamanında ve zamana bağlanacak Atamayı desteklemektedir. Azure AD 'de uygulama yönetimi için özel roller oluşturma hakkında daha fazla bilgi için, bkz. [Azure Active Directory (Önizleme) Içinde özel yönetici rolleri](../roles/custom-overview.md).

> [!NOTE]
> Azure AD özel rolleri, önizleme sırasında yerleşik Dizin rolleriyle tümleştirilmiştir. Yetenek genel kullanıma sunulduğunda, rol yönetimi yerleşik roller deneyiminde gerçekleşmeyecektir. Aşağıdaki başlığı görürseniz, bu rollerin [yerleşik roller deneyiminde](pim-how-to-activate-role.md) yönetilmesi gerekir ve bu makale uygulanmaz:
>
> :::image type="content" source="media/pim-how-to-add-role-to-user/pim-new-version.png" alt-text="Azure AD 'de Privileged Identity Management ' yi seçin." lightbox="media/pim-how-to-add-role-to-user/pim-new-version.png":::

## <a name="activate-a-role"></a>Rol etkinleştirme

Bir Azure AD özel rolünü etkinleştirmeniz gerektiğinde, Privileged Identity Management ' de rollerim gezinti seçeneğini belirleyerek etkinleştirme isteyin.

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Azure AD [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)açın.

1. Uygun Azure AD özel rol atamalarınızın listesini görmek için **Azure AD özel rolleri** ' ni seçin.

   ![Uygun Azure AD özel rol atamalarının listesini görüntüleyin](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
> Rol atamadan önce bir rol oluşturmanız/yapılandırmanız gerekir. AAD özel rollerini yapılandırma hakkında daha fazla bilgi için bkz. [Privileged Identity Management Azure AD özel rollerini yapılandırma](azure-ad-custom-roles-configure.md).

1. **Azure AD özel roller (Önizleme)** sayfasında, ihtiyacınız olan atamayı bulun.
1. **Etkinleştir** sayfasını açmak için **rolünüzü etkinleştir** ' i seçin.
1. Rolünüz çok faktörlü kimlik doğrulaması gerektiriyorsa, **devam etmeden önce kimliğinizi doğrula**' yı seçin. Oturum başına yalnızca bir kez kimlik doğrulaması yapmanız gerekir.
1. **Kimliğimi doğrula** ' yı seçin ve ek güvenlik doğrulaması sağlamak için yönergeleri izleyin.
1. Özel bir uygulama kapsamı belirtmek için **kapsam** ' ı seçerek filtre bölmesini açın. Gerekli en düşük kapsamda bir role erişim istemeniz gerekir. Atamanız bir uygulama kapsamınise, yalnızca o kapsamda etkinleştirebilirsiniz.

   ![Rol atamasına bir Azure AD kaynak kapsamı atama](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. Gerekirse, özel bir etkinleştirme başlangıç saati belirtin. Kullanıldığında, rol üyesi belirtilen zamanda etkinleştirilir.
1. **Neden** kutusunda, etkinleştirme isteğinin nedenini girin. Bu, rol ayarında gerekli hale getirilebilir veya bu şekilde ayarlanamaz.
1. **Etkinleştir**' i seçin.

Rol onay gerektirmiyorsa, ayarlarınıza göre etkinleştirilir ve etkin roller listesine eklenir. Etkinleştirilen rolü kullanmak istiyorsanız, [Privileged Identity Management bir Azure AD özel rolü atama](azure-ad-custom-roles-assign.md)bölümündeki adımlarla başlayın.

Rolün etkinleştirilmesi için onay gerekiyorsa, isteğin onay bekliyor olduğunu bildiren bir Azure bildirimi alırsınız.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD özel rolü atama](azure-ad-custom-roles-assign.md)
- [Azure AD özel rol atamasını kaldırma veya güncelleştirme](azure-ad-custom-roles-update-remove.md)
- [Azure AD özel rol ataması yapılandırma](azure-ad-custom-roles-configure.md)
- [Azure AD 'de rol tanımları](../roles/permissions-reference.md)
