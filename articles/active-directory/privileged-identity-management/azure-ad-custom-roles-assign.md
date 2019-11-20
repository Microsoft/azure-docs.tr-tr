---
title: Privileged Identity Management bir Azure AD özel rolü atama (PıM) | Microsoft Docs
description: Atama Privileged Identity Management için bir Azure AD özel rolü atama (PıM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
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
ms.openlocfilehash: 1c4963e6563db166566cff41d381295a8a9e576b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756352"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Privileged Identity Management bir Azure AD özel rolü atama

Bu makalede, Azure Active Directory (Azure AD) yönetim deneyiminde uygulamaları yönetmek için oluşturulan özel rollere tam zamanında ve zamana bağlanacak bir atama oluşturmak üzere Privileged Identity Management (PıM) ' nin nasıl kullanılacağı açıklanır.

- Azure AD 'de uygulama yönetimi için özel roller oluşturma hakkında daha fazla bilgi için, bkz. [Azure Active Directory (Önizleme) Içinde özel yönetici rolleri](../users-groups-roles/roles-custom-overview.md).
- Privileged Identity Management henüz kullanmadıysanız [Privileged Identity Management kullanmaya başlama](pim-getting-started.md)hakkında daha fazla bilgi alın.
- Privileged Identity Management yönetmek için başka bir yöneticiye erişim verme hakkında daha fazla bilgi için bkz. [Privileged Identity Management yönetmek için diğer yöneticilere erişim verme](pim-how-to-give-access-to-pim.md).

> [!NOTE]
> Azure AD özel rolleri, önizleme sırasında yerleşik Dizin rolleriyle tümleştirilmiştir. Yetenek genel kullanıma sunulduğunda, rol yönetimi yerleşik roller deneyiminde gerçekleşmeyecektir.

## <a name="assign-a-role"></a>Rol atama

Privileged Identity Management, Azure Active Directory (Azure AD) uygulama yönetiminde oluşturabileceğiniz özel rolleri yönetebilir.  Aşağıdaki adımlar özel bir dizin rolüne uygun bir atama yapar.

1. Azure portal [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) Için, ayrıcalıklı rol yöneticisi rolüne atanan bir kullanıcı hesabıyla oturum açın.
1. **Azure AD özel rollerini (Önizleme)** seçin.

    ![Uygun rol atamalarını görmek için Azure AD özel rolleri Önizlemesi ' ni seçin](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Azure AD uygulamalarına yönelik özel rollerin listesini görmek için **Roller** ' i seçin.

    ![Rolleri seçin uygun rol atamaları listesini görüntüleyin](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Atama sayfasını açmak için **üye Ekle** ' yi seçin.
1. Rol atamasının kapsamını tek bir uygulamayla kısıtlamak için, **kapsam** ' ı seçerek bir uygulama kapsamı belirtin.

    ![Azure AD 'de uygun rol atamalarının kapsamını kısıtlama](./media/azure-ad-custom-roles-assign/set-scope.png)

1. **Rol seç listesini** **açmak için bir rol seçin** öğesini seçin.

    ![kullanıcıya atanacak uygun rolü seçin](./media/azure-ad-custom-roles-assign/select-role.png)

1. Atamak istediğiniz bir rol seçin ve ardından **Seç**' e tıklayın. **Üye seçin** listesi açılır.

    ![rolü atadığınız kullanıcıyı seçin](./media/azure-ad-custom-roles-assign/select-member.png)

1. Role atamak istediğiniz kullanıcıyı seçin ve ardından **Seç**' e tıklayın. **Üyelik ayarları** listesi açılır.

    ![Rol atama türünü uygun veya etkin olarak ayarla](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. **Üyelik ayarları** sayfasında **uygun** veya **etkin**' i seçin:

    - **Uygun** atamalar, rolü kullanabilmesi için önce rolün atandığı kullanıcının bir eylem gerçekleştirmesini gerektirir. Eylemler, bir Multi-Factor Authentication denetimini geçirmeyi, iş gerekçesini sağlamayı veya belirlenen onaylayanlardan onay istemeyi içerebilir.
    - **Etkin** atamalar, atanan kullanıcının rolü kullanmak için herhangi bir eylem gerçekleştirmesini gerektirmez. Etkin kullanıcılar role her zaman atanmış ayrıcalıklara sahiptir.

1. **Kalıcı** onay kutusu varsa ve kullanılabiliyorsa (rol ayarlarına bağlı olarak), atamanın kalıcı olup olmadığını belirtebilirsiniz. Atamanın kalıcı olarak uygun veya kalıcı olarak atanmasını sağlamak için onay kutusunu seçin. Atama süresi belirtmek için onay kutusunu temizleyin.
1. Yeni rol atamasını oluşturmak için **Kaydet** ' e ve ardından **Ekle**' ye tıklayın. Atama işlemi durumu bildirimi görüntülenir.

Rol atamasını doğrulamak için, açık bir rolde **atamalar**  > **ata** ' yı seçin ve rol atamalarınızın uygun veya etkin olarak doğru şekilde tanımlandığını doğrulayın.

 ![Rol atamasının uygun veya etkin olarak görünür olup olmadığını denetleyin](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD özel rolünü etkinleştirme](azure-ad-custom-roles-assign.md)
- [Azure AD özel rol atamasını kaldırma veya güncelleştirme](azure-ad-custom-roles-update-remove.md)
- [Azure AD özel rol ataması yapılandırma](azure-ad-custom-roles-configure.md)
- [Azure AD 'de rol tanımları](../users-groups-roles/directory-assign-admin-roles.md)
