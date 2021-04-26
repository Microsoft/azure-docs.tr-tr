---
title: Sanal makine ölçek kümesi 'nde yönetilen kimlikleri Yapılandırma-Azure AD
description: Azure portal kullanarak bir sanal makine ölçek kümesinde Azure kaynakları için Yönetilen kimlikler yapılandırmaya yönelik adım adım yönergeler.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73c2f4167d6ce5465a879f6b2ef7e96d91065c4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590931"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-vmss-using-the-azure-portal"></a>Azure portal kullanarak bir sanal makine ölçek kümesindeki (VMSS) Azure kaynakları için Yönetilen kimlikler yapılandırma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen bir kimlikle Azure hizmetleri sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz. 

Bu makalede, PowerShell kullanarak, bir sanal makine ölçek kümesindeki Azure kaynakları işlemleri için aşağıdaki yönetilen kimliklerin nasıl gerçekleştirileceğini öğreneceksiniz:

- Azure kaynakları için Yönetilen kimlikler hakkında bilginiz varsa [genel bakış bölümüne](overview.md)bakın.
- Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).
- Bu makaledeki yönetim işlemlerini gerçekleştirmek için, hesabınız aşağıdaki Azure rolü atamalarına ihtiyaç duyuyor:

    > [!NOTE]
    > Ek Azure AD dizin rolü ataması gerekli değildir.

    - Bir sanal makine ölçek kümesinden sistem tarafından atanan yönetilen kimliği etkinleştirmek ve kaldırmak için [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .

## <a name="system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik

Bu bölümde, Azure portal kullanarak sistem tarafından atanan yönetilen kimliği etkinleştirmeyi ve devre dışı bırakmayı öğreneceksiniz.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesi oluştururken sistem tarafından atanan yönetilen kimliği etkinleştir

Şu anda Azure portal, bir sanal makine ölçek kümesinin oluşturulması sırasında sistem tarafından atanan yönetilen kimliğin etkinleştirilmesini desteklemez. Bunun yerine, ilk olarak bir sanal makine ölçek kümesi oluşturmak için aşağıdaki sanal makine ölçek kümesi oluşturma hızlı başlangıcı makalesine başvurun ve sonra bir sanal makine ölçek kümesinde sistem tarafından atanan yönetilen kimliği etkinleştirme hakkında ayrıntılı bilgi edinmek için sonraki bölüme ilerleyin:

- [Azure portal bir sanal makine ölçek kümesi oluşturma](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Mevcut bir sanal makine ölçek kümesi üzerinde sistem tarafından atanan yönetilen kimliği etkinleştir

Başlangıçta sağlanan bir sanal makine ölçek kümesinde sistem tarafından atanan yönetilen kimliği etkinleştirmek için:

1. Sanal makine ölçek kümesini içeren Azure aboneliğiyle ilişkili bir hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.

2. İstenen sanal makine ölçek kümesine gidin.

3. **Sistem atandı** altında **durum**' un altında **, seçin ve** ardından **Kaydet**' e tıklayın:

   ![Ekran görüntüsü "sistem atandı" seçiliyken "kimlik (Önizleme)" sayfasını, "açık" durumunu ve "Kaydet" düğmesinin vurgulanmasını gösterir.](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesinden sistem tarafından atanan yönetilen kimliği kaldır

Artık sistem tarafından atanan yönetilen kimliğe ihtiyacı olmayan bir sanal makine ölçek kümesine sahipseniz:

1. Sanal makine ölçek kümesini içeren Azure aboneliğiyle ilişkili bir hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın. Ayrıca, hesabınızın, sanal makine ölçek kümesi üzerinde yazma izinleri veren bir role ait olduğundan emin olun.

2. İstenen sanal makine ölçek kümesine gidin.

3. **Sistem atandı**, **durum**' un altında **kapalı** ' yı seçin ve **Kaydet**' e tıklayın:

   ![Yapılandırma sayfası ekran görüntüsü](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik

Bu bölümde, Azure portal kullanarak bir sanal makine ölçek kümesinden Kullanıcı tarafından atanan yönetilen kimlik ekleme ve kaldırma hakkında bilgi edineceksiniz.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesi oluşturulurken Kullanıcı tarafından atanan yönetilen kimlik atama

Şu anda Azure portal, bir sanal makine ölçek kümesi oluşturulurken Kullanıcı tarafından atanan yönetilen kimliğin atanmasını desteklemez. Bunun yerine, önce bir sanal makine ölçek kümesi oluşturmak için aşağıdaki sanal makine ölçek kümesi oluşturma hızlı başlangıcı makalesine başvurun ve ardından bir sonraki bölüme giderek Kullanıcı tarafından atanan bir yönetilen kimliği atamaya yönelik ayrıntılar için bir sonraki bölüme ilerleyin:

- [Azure portal bir sanal makine ölçek kümesi oluşturma](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Mevcut bir sanal makine ölçek kümesine Kullanıcı tarafından atanan bir yönetilen kimlik atama

1. Sanal makine ölçek kümesini içeren Azure aboneliğiyle ilişkili bir hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
2. İstenen sanal makine ölçek kümesine gidin ve **kimlik**, **Kullanıcı atandı** ve ardından **\+ Ekle**' ye tıklayın.

   !["Kullanıcı atandı" seçiliyken ve "Ekle" düğmesinin vurgulandığı "kimlik" sayfasını gösteren ekran görüntüsü.](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Sanal makine ölçek kümesine eklemek istediğiniz kullanıcı tarafından atanan kimliğe tıklayın ve ardından **Ekle**' ye tıklayın.
   
   ![VMSS 'ye Kullanıcı tarafından atanan kimlik Ekle](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Kullanıcı tarafından atanan yönetilen kimliği bir sanal makine ölçek kümesinden kaldırma

1. VM 'yi içeren Azure aboneliğiyle ilişkili bir hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
2. İstenen sanal makine ölçek kümesine gidin ve **kimlik**, **Kullanıcı atandı**, silmek istediğiniz kullanıcı tarafından atanan yönetilen kimliğin adı ' na tıklayın ve ardından **Kaldır** ' a tıklayın (onay bölmesinde **Evet** ' e tıklayın).

   ![Kullanıcı tarafından atanan kimliği bir VMSS 'den kaldır](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Sonraki adımlar

- Azure portal kullanarak, [başka bir Azure kaynağına](howto-assign-access-portal.md)Azure sanal makine ölçek kümesi yönetilen kimlik erişimi verin.
