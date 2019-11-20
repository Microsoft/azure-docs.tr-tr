---
title: Privileged Identity Management Azure Active Directory Azure AD rolleri atama | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde Azure AD rolleri atamayı öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad03e69df4ca03d58bba98b912ade354f90042e
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809205"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure AD rolleri atama

Azure Active Directory (Azure AD) ile genel yönetici **kalıcı** Azure AD yönetici rolü atamaları yapabilir. Bu rol atamaları [Azure Portal](../users-groups-roles/directory-assign-admin-roles.md) veya [PowerShell komutları](/powershell/module/azuread#directory_roles)kullanılarak oluşturulabilir.

Azure AD Privileged Identity Management (PıM) hizmeti, ayrıcalıklı rol yöneticilerinin kalıcı yönetici rolü atamaları yapmasına de olanak tanır. Ayrıca, ayrıcalıklı rol yöneticileri, kullanıcıların Azure AD yönetici rollerine **uygun** olmasını sağlayabilir. Uygun bir yönetici, bu rolü gerektiğinde etkinleştirebilir ve sonra izinlerinin süresi bittiğinde sona erer.

## <a name="make-a-user-eligible-for-a-role"></a>Bir kullanıcıyı bir rol için uygun hale getirme

Bir kullanıcıyı Azure AD yöneticisi rolüne uygun hale getirmek için bu adımları izleyin.

1. [Ayrıcalıklı rol yöneticisi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) rolünün üyesi olan bir kullanıcıyla [Azure Portal](https://portal.azure.com/) oturum açın.

    Privileged Identity Management yönetmek için başka bir yöneticiye erişim verme hakkında daha fazla bilgi için bkz. [Privileged Identity Management yönetmek için diğer yöneticilere erişim verme](pim-how-to-give-access-to-pim.md).

1. **Azure AD Privileged Identity Management**açın.

    Henüz Azure portal Privileged Identity Management başlatmadıysanız [Privileged Identity Management kullanmaya başlayın](pim-getting-started.md)' a gidin.

1. **Azure AD rolleri**' ni seçin.

1. **Rolleri** veya **üyeleri**seçin.

    ![Roller ve Üyeler menü seçenekleri vurgulanmış olan Azure AD rolleri](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Yönetilen üyeleri Ekle ' yi açmak için **üye Ekle** ' yi seçin.

1. **Rol Seç**' i seçin, yönetmek istediğiniz rolü seçin ve ardından **Seç**' i seçin.

    ![Azure AD rollerini listeleme rol bölmesini seçin](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. **Üyeleri Seç**' i seçin, role atamak istediğiniz kullanıcıları seçin ve ardından **Seç**' i seçin.

    ![Bir kullanıcı seçebileceğiniz Üyeler bölmesini seçin](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Yönetilen Üyeler Ekle ' de, kullanıcıyı role eklemek için **Tamam** ' ı seçin.

1. Rol listesinde, üye listesini görmek için yeni atadığınız rolü seçin.

     Rol atandığında, seçtiğiniz Kullanıcı, rol için **uygun** olan Üyeler listesinde görünür.

    ![Rolün üyeleri, etkinleştirme durumuyla birlikte listelenir](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Artık Kullanıcı role uygun olduğuna göre, [Privileged Identity Management ' de Azure AD rollerimi etkinleştirme](pim-how-to-activate-role.md)' deki yönergelere göre etkinleştirebileceklerini bilmesini sağlar.

    Uygun yöneticilerin etkinleştirme sırasında Azure Multi-Factor Authentication (MFA) için kaydolduğu sorulur. Bir Kullanıcı MFA için kayıt yapamaz veya bir Microsoft hesabı (genellikle @outlook.com) kullanıyorsa, bunların tüm rollerinde kalıcı hale getirmeniz gerekir.

## <a name="make-a-role-assignment-permanent"></a>Rol atamasını kalıcı hale getirme

Varsayılan olarak, yeni kullanıcılar yalnızca bir Azure AD yönetici rolü için uygundur. Rol atamasını kalıcı hale getirmek istiyorsanız bu adımları izleyin.

1. **Azure AD Privileged Identity Management**açın.

1. **Azure AD rolleri**' ni seçin.

1. **Üyeler**' i seçin.

    ![Azure AD rolleri-rol ve etkinleştirme durumunu gösteren üye listesi](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Kalıcı hale getirmek istediğiniz **uygun** bir rol seçin.

1. **Diğer** ' i seçin ve ardından **izin oluştur**' u seçin.

    ![Daha fazla menü seçeneği açık olan bir rol için uygun olan bir Kullanıcı listeleme bölmesi](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Rol artık **kalıcı**olarak listelendi.

    ![Artık kalıcı olan rol ve etkinleştirme durumunu gösteren üye listesi](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Bir rolden Kullanıcı kaldırma

Kullanıcıları rol atamalarından kaldırabilirsiniz, ancak kalıcı bir genel yönetici olan en az bir kullanıcı olduğundan emin olabilirsiniz. Hangi kullanıcıların rol atamalarına ihtiyacı olduğundan emin değilseniz, [rol için bir erişim incelemesi başlatabilirsiniz](pim-how-to-start-security-review.md).

Belirli bir kullanıcıyı Azure AD yönetici rolünden kaldırmak için aşağıdaki adımları izleyin.

1. **Azure AD Privileged Identity Management**açın.

1. **Azure AD rolleri**' ni seçin.

1. **Üyeler**' i seçin.

    ![Azure AD rolleri-rol ve etkinleştirme stat 'yi gösteren üye listesi](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Kaldırmak istediğiniz rol atamasını seçin.

1. **Daha fazla** öğesini seçin ve **kaldırın**.

    ![Daha fazla menü seçeneği açık olan kalıcı rolüne sahip bir kullanıcının listelendiği bölme](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Eylemi onaylamanız istendiğinde **Evet**' i seçin.

    ![Rolden üye kaldırmak isteyip istemediğinizi soran ileti](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Rol ataması kaldırılır.

## <a name="authorization-error-when-assigning-roles"></a>Rol atarken yetkilendirme hatası

Senaryo: bir Azure kaynağı için etkin bir sahip veya Kullanıcı erişimi Yöneticisi olarak, kaynağınızı Privileged Identity Management içinde görebilir, ancak uygun bir atama yapma ya da bir rol atamaları listesini kaynağa genel bakış sayfası. Bu eylemlerin herhangi biri bir yetkilendirme hatasına neden olur.

Rol atamak için, MS-PıM hizmet sorumlusu, Azure kaynak erişimi için Azure rol tabanlı erişim denetimi 'nde (Azure AD Yönetim rollerinin aksine) [Kullanıcı erişimi yönetici rolü](../../role-based-access-control/built-in-roles.md#user-access-administrator) atanmalıdır. MS-PıM ' y i Kullanıcı erişimi Yöneticisi rolüne atanıncaya kadar beklemek yerine el ile atayabilirsiniz.

Aşağıdaki adımlar, Kullanıcı erişimi yönetici rolünü bir abonelik için MS-PıM hizmet sorumlusuna atar.

1. [Azure Portal](https://portal.azure.com) Azure AD kuruluşunuzda genel yönetici olarak oturum açın.

1. **Tüm hizmetler** ' i ve ardından **abonelikler**' i seçin.

1. Aboneliğinizi seçin.

1. **Erişim denetimi (IAM)** öğesini seçin.

1. Abonelik kapsamındaki rol atamalarının geçerli listesini görmek için **rol atamaları** ' nı seçin.

   ![Abonelik için erişim denetimi (ıAM) dikey penceresi](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. **MS-PIM** hizmet sorumlusuna **Kullanıcı erişimi yönetici** rolü atanıp atanmadığını denetleyin.

1. Aksi takdirde, rol **ataması Ekle ' yi seçerek** rol ataması **Ekle** bölmesini açın.

1. **Rol** açılan listesinde **Kullanıcı erişimi yönetici** rolü ' nü seçin.

1. **Seç** listesinde, **MS-PIM** hizmet sorumlusu ' nı bulup seçin.

   ![Rol atama bölmesi ekleme-MS-PıM hizmet sorumlusu için izin ekleme](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Rolü atamak için **Kaydet** ' i seçin.

   Birkaç dakika sonra MS-PıM hizmet sorumlusu, abonelik kapsamında Kullanıcı erişimi Yöneticisi rolüne atanır.

   ![MS-PıM için Kullanıcı erişimi yönetici rolü atamasını gösteren erişim denetimi (ıAM) dikey penceresi](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management 'de Azure AD yönetici rolü ayarlarını yapılandırma](pim-how-to-change-default-settings.md)
- [Privileged Identity Management Azure Kaynak rolleri atama](pim-resource-roles-assign-roles.md)
