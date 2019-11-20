---
title: Privileged Identity Management (PıM) içinde bir Azure AD özel rolünü etkinleştirme | Microsoft Docs
description: Atama Privileged Identity Management için Azure AD özel rolünü etkinleştirme (PıM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92ea116414e12cb231fdbaa5ec9ad375d2c0eea9
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145004"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Privileged Identity Management bir Azure AD özel rolünü etkinleştirin

Azure Active Directory (Azure AD) Privileged Identity Management artık kimlik ve erişim yönetimi Yönetim deneyiminde uygulama yönetimi için oluşturulan özel rollere tam zamanında ve zamana bağlanacak Atamayı desteklemektedir. Azure AD 'de uygulama yönetimi için özel roller oluşturma hakkında daha fazla bilgi için, bkz. [Azure Active Directory (Önizleme) Içinde özel yönetici rolleri](../users-groups-roles/roles-custom-overview.md).

> [!NOTE]
> Azure AD özel rolleri, önizleme sırasında yerleşik Dizin rolleriyle tümleştirilmiştir. Yetenek genel kullanıma sunulduğunda, rol yönetimi yerleşik roller deneyiminde gerçekleşmeyecektir.

## <a name="activate-a-role"></a>Rol etkinleştirme

Bir Azure AD özel rolünü etkinleştirmeniz gerektiğinde, Privileged Identity Management ' de rollerim gezinti seçeneğini belirleyerek etkinleştirme isteyin.

1. Oturum [Azure portalında](https://portal.azure.com).
1. Azure AD [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)açın.

1. Uygun Azure AD özel rol atamalarınızın listesini görmek için **Azure AD özel rolleri** ' ni seçin.

   ![Uygun Azure AD özel rol atamalarının listesini görüntüleyin](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
>  Rol atamadan önce bir rol oluşturmanız/yapılandırmanız gerekir. AAD özel rollerini yapılandırma hakkında daha fazla bilgi için bkz. [buraya] (https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-ad-custom-roles-configure)

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
- [Azure AD 'de rol tanımları](../users-groups-roles/directory-assign-admin-roles.md)
