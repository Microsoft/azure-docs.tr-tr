---
title: RBAC ve Azure portal kullanarak Azure kaynaklarına erişimi yönetme | Microsoft Docs
description: Rol tabanlı erişim denetimi (RBAC) ve Azure portal kullanarak kullanıcılar, gruplar, hizmet sorumluları ve yönetilen kimlikler için Azure kaynaklarına erişimi yönetmeyi öğrenin. Buna erişimi listeleme, erişim verme ve erişimi kaldırma işlemleri dahildir.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e9a53c41535c17de2d56227012160c7f6eb25c6
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337612"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>RBAC ve Azure portal kullanarak Azure kaynaklarına erişimi yönetme

[Rol tabanlı erişim denetimi (RBAC)](overview.md) , Azure kaynaklarına erişimi yönetme yöntemidir. Bu makalede, Azure portal kullanarak erişimi nasıl yöneteceğiniz açıklanmaktadır. Azure Active Directory erişimini yönetmeniz gerekiyorsa, bkz. [Azure Active Directory yönetici rollerini görüntüleme ve atama](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Önkoşullar

Rol atamaları eklemek ve kaldırmak için şunları yapmanız gerekir:

- `Microsoft.Authorization/roleAssignments/write` ve @no__t- [Kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator) veya [sahibi](built-in-roles.md#owner) gibi-1 izinleri

## <a name="overview-of-access-control-iam"></a>Erişim denetimine genel bakış (ıAM)

**Erişim denetimi (IAM)** , Azure kaynaklarına erişimi yönetmek için kullandığınız dikey pencere. Kimlik ve erişim yönetimi olarak da bilinir ve Azure portal çeşitli konumlarda görüntülenir. Aşağıda bir abonelik için erişim denetimi (ıAM) dikey penceresi örneği gösterilmektedir.

![Abonelik için erişim denetimi (ıAM) dikey penceresi](./media/role-assignments-portal/access-control-numbers.png)

Aşağıdaki tabloda bazı öğelerin ne için kullanıldığı açıklanmaktadır:

| # | Öğe | Ne kullanıyorsunuz? |
| --- | --- | --- |
| 1\. | Erişim denetimi (ıAM) açıldığı kaynak | Kapsamı tanımla (Bu örnekteki abonelik) |
| 2 | Düğme **Ekle** | Rol atamaları ekleme |
| 3 | **Erişim sekmesini denetle** | Tek bir kullanıcı için rol atamalarını görüntüleme |
| 4 | **Rol atamaları** sekmesi | Rol atamalarını geçerli kapsamda görüntüle |
| 5 | **Roller** sekmesi | Tüm rolleri ve izinleri görüntüleme |

Erişim denetimi (ıAM) dikey penceresi ile en etkili olması için, erişimi yönetmeye çalışırken aşağıdaki üç soruyu yanıtlayıp yanıtlayabilmeniz yardımcı olur:

1. **Kimin erişmesi gerekiyor?**

    Bir kullanıcıya, gruba, hizmet sorumlusuna veya yönetilen kimliğe başvurur. Buna *güvenlik sorumlusu*da denir.

1. **Hangi izinleri gerekir?**

    İzinler, rollerle birlikte gruplandırılır. Birkaç yerleşik rol listesinden seçim yapabilirsiniz.

1. **Bunlara erişmesi gereken durumlar var mı?**

    Burada erişimin uygulandığı kaynak kümesini ifade eder. Nerede bir yönetim grubu, abonelik, kaynak grubu veya depolama hesabı gibi tek bir kaynak olabilir. Buna *kapsam*denir.

## <a name="open-access-control-iam"></a>Erişim denetimini Aç (ıAM)

Karar vermeniz gereken ilk şey, erişim denetimi (ıAM) dikey penceresinin açılacağı yerdir. Bu, erişimini yönetmek istediğiniz kaynaklara bağlıdır. Bir yönetim grubundaki her şeyin, bir abonelikteki her şeyin, bir kaynak grubundaki her şeyin veya tek bir kaynaktaki erişimini yönetmek istiyor musunuz?

1. Azure portal, **tüm hizmetler** ' e tıklayın ve kapsamı seçin. Örneğin, **Yönetim grupları**, **abonelikler**, **kaynak grupları**veya bir kaynak seçebilirsiniz.

1. Belirli kaynağa tıklayın.

1. Tıklayın **erişim denetimi (IAM)** .

    Aşağıda bir abonelik için erişim denetimi (ıAM) dikey penceresi örneği gösterilmektedir. Burada herhangi bir erişim denetimi değişikliği yaparsanız, bu değişiklikler tüm abonelik için geçerlidir.

    ![Abonelik için erişim denetimi (ıAM) dikey penceresi](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Rolleri ve izinleri görüntüleyin

Rol tanımı, rol atamaları için kullandığınız izin koleksiyonudur. Azure ['Da Azure kaynakları için 70 yerleşik rol](built-in-roles.md)vardır. Kullanılabilir rolleri ve izinleri görüntülemek için bu adımları izleyin.

1. **Erişim denetimini (IAM)** herhangi bir kapsamda açın.

1. Tüm yerleşik ve özel rollerin listesini görmek için **Roller** sekmesine tıklayın.

   Geçerli kapsamdaki her bir role atanan kullanıcı ve grup sayısını görebilirsiniz.

   ![Rolleri listesi](./media/role-assignments-portal/roles-list.png)

1. Bu rolün kim tarafından atandığını görmek için tek bir role tıklayın ve ayrıca rolün izinlerini görüntüleyin.

   ![Rol atamaları](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Rol atamalarını görüntüle

Erişimi yönetirken kimlerin erişebileceğini, izinleri ne olduğunu ve hangi kapsamda olduğunu bilmeniz gerekir. Bir kullanıcının, grubun, hizmet sorumlusunun veya yönetilen kimliğin erişimini listelemek için, rol atamalarını görüntüleyebilirsiniz.

### <a name="view-role-assignments-for-a-single-user"></a>Tek bir kullanıcı için rol atamalarını görüntüleme

Belirli bir kapsamdaki tek bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimlik erişimini görüntülemek için bu adımları izleyin.

1. Erişim **denetimi 'ni (IAM)** , erişimi görüntülemek istediğiniz yönetim grubu, abonelik, kaynak grubu veya kaynak gibi bir kapsamda açın.

1. **Erişimi denetle** sekmesine tıklayın.

    ![Erişim denetimi-erişim sekmesine göz atın](./media/role-assignments-portal/access-control-check-access.png)

1. **Bul** listesinde, erişimini denetlemek istediğiniz güvenlik sorumlusu türünü seçin.

1. Arama kutusuna, görünen adlar, e-posta adresleri veya nesne tanımlayıcıları için dizinde arama yapmak üzere bir dize girin.

    ![Erişim denetimi seçim listesi](./media/role-assignments-portal/check-access-select.png)

1. **Atamalar** bölmesini açmak için güvenlik sorumlusuna tıklayın.

    ![Atamalar bölmesi](./media/role-assignments-portal/check-access-assignments.png)

    Bu bölmede, seçilen güvenlik sorumlusuna ve kapsamına atanan rolleri görebilirsiniz. Bu kapsamda herhangi bir reddetme ataması varsa veya bu kapsama devralınmışsa, bunlar listelenecektir.

### <a name="view-all-role-assignments-at-a-scope"></a>Tüm rol atamalarını bir kapsamda görüntüle

1. Erişim **denetimi 'ni (IAM)** , erişimi görüntülemek istediğiniz yönetim grubu, abonelik, kaynak grubu veya kaynak gibi bir kapsamda açın.

1. Bu kapsamdaki tüm rol atamalarını görüntülemek için **rol atamaları** sekmesine tıklayın.

   ![Erişim denetimi-rol atamaları sekmesi](./media/role-assignments-portal/access-control-role-assignments.png)

   Rol atamaları sekmesinde kimlerin bu kapsamda erişimi olduğunu görebilirsiniz. Bazı rollerin kapsamı **Bu kaynak** olarak belirlenmişken diğerlerinin başka bir kapsamdan **(Devralınmış)** olduğuna dikkat edin. Erişim özellikle bu kaynağa atanır veya bir atamadan üst kapsama devralınır.

## <a name="add-a-role-assignment"></a>Rol ataması ekleme

RBAC 'de, erişim izni vermek için, bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimliğe bir rol atarsınız. Farklı kapsamlardan erişim sağlamak için aşağıdaki adımları izleyin.

### <a name="assign-a-role-at-a-scope"></a>Bir kapsamda rol atama

1. Erişim **denetimi 'ni (IAM)** , erişim vermek istediğiniz yönetim grubu, abonelik, kaynak grubu veya kaynak gibi bir kapsamda açın.

1. Bu kapsamdaki tüm rol atamalarını görüntülemek için **rol atamaları** sekmesine tıklayın.

1. Rol **ataması Ekle bölmesini**açmak için**rol ataması** Ekle ' ye tıklayın. > 

   Rol atama izniniz yoksa rol ataması Ekle seçeneği devre dışı bırakılır.

   ![Menü ekle](./media/role-assignments-portal/add-menu.png)

   ![Rol atama bölmesi Ekle](./media/role-assignments-portal/add-role-assignment.png)

1. **Rol** açılan listesinde **Sanal Makine Katılımcısı** gibi bir rol seçin.

1. **Seç** listesinde, bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimlik seçin. Listede güvenlik sorumlusunu görmüyorsanız **Seç** kutusuna giriş yaparak dizinde görünen ad, e-posta adresi ve nesne tanımlayıcısı arayabilirsiniz.

1. Rolü atamak için **Kaydet**’e tıklayın.

   Birkaç dakika sonra, güvenlik sorumlusu seçili kapsamda role atanır.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Bir kullanıcıyı, aboneliğin yöneticisi olarak atama

Bir kullanıcıyı bir Azure aboneliğinin Yöneticisi yapmak için, abonelik kapsamında bu rolü [sahip](built-in-roles.md#owner) rolüne atayın. Sahip rolü, kullanıcıya, başkalarına erişim yetkisi devretme hakkı da dahil olmak üzere abonelikteki tüm kaynaklara tam erişim izni verir. Bu adımlar diğer rol atamasıyla aynıdır.

1. Azure portal, **tüm hizmetler** ve ardından **abonelikler**' e tıklayın.

1. Erişim izni vermek istediğiniz aboneliğe tıklayın.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

1. Bu aboneliğin tüm rol atamalarını görüntülemek için **Rol atamaları** sekmesine tıklayın.

1. Rol **ataması Ekle bölmesini**açmak için**rol ataması** Ekle ' ye tıklayın. > 

   Rol atama izniniz yoksa rol ataması Ekle seçeneği devre dışı bırakılır.

   ![Menü ekle](./media/role-assignments-portal/add-menu.png)

   ![Rol atama bölmesi Ekle](./media/role-assignments-portal/add-role-assignment.png)

1. **Rol** açılan listesinde **Sahip** rolünü seçin.

1. **Seç** listesinde bir kullanıcı seçin. Listede kullanıcıyı görmüyorsanız **Seç** kutusuna giriş yaparak dizinde görünen ad ve e-posta adresi arayabilirsiniz.

1. Rolü atamak için **Kaydet**’e tıklayın.

   Birkaç dakika sonra kullanıcıya abonelik kapsamında Sahip rolü atanmış olur.

## <a name="remove-role-assignments"></a>Rol atamalarını kaldırın

RBAC'de erişimi kaldırmak için rol atamasını kaldırmanız gerekir. Erişimi kaldırmak için aşağıdaki adımları izleyin.

1. Erişim **denetimi 'ni (IAM)** , erişimi kaldırmak istediğiniz yönetim grubu, abonelik, kaynak grubu veya kaynak gibi bir kapsamda açın.

1. Bu aboneliğin tüm rol atamalarını görüntülemek için **Rol atamaları** sekmesine tıklayın.

1. Rol ataması listesinde kaldırmak istediğiniz rol atamasına sahip güvenlik sorumlusunun yanına onay işareti ekleyin.

   ![Rol atamasını kaldırma iletisi](./media/role-assignments-portal/remove-role-assignment-select.png)

1. **Kaldır**' a tıklayın.

   ![Rol atamasını kaldırma iletisi](./media/role-assignments-portal/remove-role-assignment.png)

1. Görüntülenen rol atamasını Kaldır iletisinde **Evet**' e tıklayın.

    Devralınmış rol atamaları kaldırılamaz. Devralınmış bir rol atamasını kaldırmanız gerekiyorsa bu işlemi rol atamasının oluşturulduğu kapsamda gerçekleştirmeniz gerekir. **Kapsam** sütununda, **(devralınmış)** seçeneğinin yanında, size bu rolün atandığı kapsama götüren bir bağlantı vardır. Rol atamasını kaldırmak için orada listelenen kapsama gidin.

   ![Rol atamasını kaldırma iletisi](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Öğretici: RBAC ve Azure portal @ no__t-0 kullanarak Azure kaynaklarına Kullanıcı erişimi verme
* [Öğretici: RBAC kullanarak Azure kaynaklarına Kullanıcı erişimi verme ve Azure PowerShell @ no__t-0
* [Azure kaynakları için RBAC sorunlarını giderme](troubleshooting.md)
* [Kaynaklarınızı Azure Yönetim grupları ile düzenleme](../governance/management-groups/overview.md)
