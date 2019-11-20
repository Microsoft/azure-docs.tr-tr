---
title: Privileged Identity Management Azure AD özel rollerini yapılandırma (PıM) | Microsoft Docs
description: Privileged Identity Management (PıM) içinde Azure AD özel rollerini yapılandırma
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
ms.openlocfilehash: e7d5a7fe20a53baa86f43703c97fb8437fbb0fcd
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756407"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure AD özel rollerini yapılandırma

Ayrıcalıklı bir rol yöneticisi, bir kullanıcı için bir özel rol ve özel roller atayan diğer uygulama yöneticileri için kendilerine uygulanan rol ayarlarını değiştirebilir.

## <a name="open-role-settings"></a>Rol ayarlarını aç

Bir Azure AD rolünün ayarlarını açmak için aşağıdaki adımları izleyin.

1. Azure portal [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) Için, ayrıcalıklı rol yöneticisi rolüne atanan bir kullanıcı hesabıyla oturum açın.
1. **Azure AD özel rollerini (Önizleme)** seçin.

    ![Uygun rol atamalarını görmek için Azure AD özel rolleri Önizlemesi ' ni seçin](./media/azure-ad-custom-roles-configure/settings-list.png)

1. **Ayarlar** sayfasını açmak için **ayarı** seçin. Yapılandırmak istediğiniz ayarların rolünü seçin.
1. **Rol ayarları** sayfasını açmak için **Düzenle** ' yi seçin.

    ![Ayarları düzenlemek için Azure AD özel rolünü açın](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Rol ayarları

Yapılandırabileceğiniz çeşitli ayarlar vardır.

### <a name="assignment-duration"></a>Atama süresi

Bir rolün ayarlarını yapılandırırken her atama türü için iki atama süresi seçeneğinden birini seçebilirsiniz (uygun veya etkin). Bu seçenekler, Privileged Identity Management role bir üye atandığında varsayılan en uzun süre olur.

Bu *uygun* atama süresi seçeneklerinden birini belirleyebilirsiniz.

- **Kalıcı uygun atamaya Izin ver**: Yöneticiler kalıcı uygun üyelik atayabilir.
- Şu **tarihten sonra uygun atama süresi doluyor**: Yöneticiler, tüm uygun atamaların belirtilen başlangıç ve bitiş tarihine sahip olmasını gerektirebilir.

Ayrıca, şu *etkin* atama süresi seçeneklerinden birini belirleyebilirsiniz:

- **Kalıcı etkin atamaya Izin ver**: Yöneticiler kalıcı etkin üyelik atayabilir.
- **Etkin atamanın sona erme**tarihi: Yöneticiler, tüm etkin atamaların belirtilen başlangıç ve bitiş tarihine sahip olmasını gerektirebilir.

### <a name="require-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication gerektir

Privileged Identity Management, iki ayrı senaryo için isteğe bağlı Azure Multi-Factor Authentication zorlaması sağlar.

- **Etkin atama üzerinde Multi-Factor Authentication gerektir**

  Bir üyeyi yalnızca kısa bir süre (örneğin, bir gün) için atamak istiyorsanız, atanan üyelerin etkinleştirme istemesini gerektirmek çok yavaş olabilir. Bu senaryoda, Kullanıcı rol atamasını etkinleştiğinden Privileged Identity Management Multi-Factor Authentication 'ı zorlayamıyor, çünkü bu rol atandığı andan itibaren zaten etkin. Atamayı karşılayan yöneticinin bunları söyledikleri kim olduğundan emin olmak için, **etkin atama üzerinde Multi-Factor Authentication gerektir** kutusunu seçin.

- **Etkinleştirme üzerinde Multi-Factor Authentication gerektir**

  Bir role atanmış kullanıcıların etkinleştirebilmeleri için Azure Multi-Factor Authentication 'a kaydolmasını zorunlu kılabilirsiniz. Bu işlem, etkinleştirme isteyen kullanıcının makul belirsizlik sağlar. Bu seçeneğin zorunlu kılınması, Kullanıcı hesabının tehlikeye girdiği durumlarda kritik rolleri korur. Etkinleştirmeden önce uygun bir üyenin Azure Multi-Factor Authentication çalıştırmasını gerektirmek için, **etkinleştirme üzerinde Multi-Factor Authentication gerektir** kutusunu seçin.

Daha fazla bilgi için bkz. [Multi-Factor Authentication ve Privileged Identity Management](pim-how-to-require-mfa.md).

### <a name="activation-maximum-duration"></a>Etkinleştirme en uzun süre

Rolün süresi dolmadan önce etkin kalacağı en uzun süreyi saat cinsinden ayarlamak için **etkinleştirme en uzun süre** kaydırıcısını kullanın. Bu değer, 1 ile 24 saat arasında olabilir.

### <a name="require-justification"></a>Gerekçe gerektir

Üyelerin etkin atamaya veya etkinleştirildiklerinde bir gerekçe girmesini zorunlu kılabilirsiniz. Gerekçe gerektirmek için **etkin atamada gerekçe iste** onay kutusunu veya **etkinleştirme için gerekçe iste** kutusunu seçin.

### <a name="require-approval-to-activate"></a>Etkinleştirmek için onay gerektir

Bir rolü etkinleştirmek için onay gerektirmek isterseniz, aşağıdaki adımları izleyin.

1. **Etkinleştirmek için onay gerektir** onay kutusunu seçin.
1. **Bir üye veya Grup Seç** listesini açmak Için **onaylayanları Seç** ' i seçin.

    ![Ayarları düzenlemek için Azure AD özel rolünü açın](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. En az bir üye veya grup seçin ve ardından **Seç**' e tıklayın. En az bir onaylayan seçmelisiniz. Varsayılan onaylayanlar yok. Seçimleriniz seçili onaylayanlar listesinde görünür.
1. Rol ayarlarını belirttikten sonra, değişikliklerinizi kaydetmek için **Güncelleştir** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD özel rolünü etkinleştirme](azure-ad-custom-roles-assign.md)
- [Azure AD özel rolü atama](azure-ad-custom-roles-assign.md)
- [Azure AD özel rol atamasını kaldırma veya güncelleştirme](azure-ad-custom-roles-update-remove.md)
- [Azure AD 'de rol tanımları](../users-groups-roles/directory-assign-admin-roles.md)
