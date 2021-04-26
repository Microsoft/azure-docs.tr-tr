---
title: Azure portal kullanarak yönetilen kimlikleri Yapılandırma-Azure AD
description: Azure portal kullanarak bir Azure VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırmaya yönelik adım adım yönergeler.
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
ms.openlocfilehash: 78c973537e958b7199c137c81e72091f70b84ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590996"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Azure portal kullanarak bir VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen bir kimlikle Azure hizmetleri sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz. 

Bu makalede, Azure portal kullanarak bir Azure sanal makinesi (VM) için sistem ve Kullanıcı tarafından atanan yönetilen kimliklerin nasıl etkinleştirileceğini ve devre dışı bırakılacağını öğreneceksiniz. 

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için Yönetilen kimlikler hakkında bilginiz varsa [genel bakış bölümüne](overview.md)bakın.
- Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).

## <a name="system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik

Bu bölümde, Azure portal kullanarak VM için sistem tarafından atanan yönetilen kimliği etkinleştirmeyi ve devre dışı bırakmayı öğreneceksiniz.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>VM oluşturma sırasında sistem tarafından yönetilen kimliği etkinleştirme

Oluşturma sırasında bir VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirmek için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

- **Kimlik** bölümündeki **Yönetim** sekmesinde, **yönetilen hizmet kimliğini** **Açık** olarak değiştirin.  

![VM oluşturma sırasında sistem tarafından atanan kimliği etkinleştirme](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

VM oluşturmak için aşağıdaki hızlı başlangıçlara bakın: 

- [Azure portal ile Windows sanal makinesi oluşturma](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Azure portalı ile Linux sanal makinesi oluşturma](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Var olan bir VM'de sistem tarafından yönetilen kimliği etkinleştirme

Başlangıçta sağlanan bir VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirmek için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

1. VM 'yi içeren Azure aboneliğiyle ilişkili bir hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.

2. İstediğiniz sanal makineye gidin ve **kimlik**' i seçin.

3. **Sistem atandı** altında **durum**' un altında **, seçin ve** ardından **Kaydet**' e tıklayın:

   !["Sistem atanmış" durumunun "açık" olarak ayarlandığı "kimlik (Önizleme)" sayfasını gösteren ekran görüntüsü.](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Bir VM 'den sistem tarafından atanan yönetilen kimliği kaldırma

Bir VM 'den sistem tarafından atanan yönetilen kimliği kaldırmak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

Artık sistem tarafından atanan yönetilen kimliğe ihtiyaç olmayan bir sanal makineniz varsa:

1. VM 'yi içeren Azure aboneliğiyle ilişkili bir hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın. 

2. İstediğiniz sanal makineye gidin ve **kimlik**' i seçin.

3. **Sistem atandı**, **durum**' un altında **kapalı** ' yı seçin ve **Kaydet**' e tıklayın:

   ![Yapılandırma sayfası ekran görüntüsü](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik

 Bu bölümde, Azure portal kullanarak bir VM 'den Kullanıcı tarafından atanan yönetilen kimlik ekleme ve kaldırma hakkında bilgi edineceksiniz.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>VM oluşturma sırasında Kullanıcı tarafından atanan bir kimlik atama

Bir VM 'ye Kullanıcı tarafından atanan bir kimlik atamak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ve [yönetilen kimlik işleci](../../role-based-access-control/built-in-roles.md#managed-identity-operator) rol atamalarına ihtiyacı vardır. Ek Azure AD dizin rolü ataması gerekli değildir.

Şu anda Azure portal, bir VM oluşturulurken Kullanıcı tarafından atanan yönetilen kimliğin atanmasını desteklemez. Bunun yerine, aşağıdaki VM oluşturma hızlı başlangıç makalelerinden birine başvurarak bir VM oluşturun ve ardından VM 'ye Kullanıcı tarafından atanan bir yönetilen kimlik atama hakkında ayrıntılı bilgi edinmek için sonraki bölüme ilerleyin:

- [Azure portal ile Windows sanal makinesi oluşturma](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Azure portalı ile Linux sanal makinesi oluşturma](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Mevcut bir VM 'ye Kullanıcı tarafından atanan bir yönetilen kimlik atama

Bir VM 'ye Kullanıcı tarafından atanan bir kimlik atamak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ve [yönetilen kimlik işleci](../../role-based-access-control/built-in-roles.md#managed-identity-operator) rol atamalarına ihtiyacı vardır. Ek Azure AD dizin rolü ataması gerekli değildir.

1. VM 'yi içeren Azure aboneliğiyle ilişkili bir hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
2. İstediğiniz sanal makineye gidin ve **kimlik**, **Kullanıcı atandı** ve ardından **\+ Ekle**' ye tıklayın.

   !["Kullanıcı atandı" seçiliyken ve "Ekle" düğmesinin vurgulandığı "kimlik" sayfasını gösteren ekran görüntüsü.](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. VM 'ye eklemek istediğiniz kullanıcı tarafından atanan kimliğe tıklayın ve ardından **Ekle**' ye tıklayın.

    ![VM 'ye Kullanıcı tarafından atanan yönetilen kimlik ekleme](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Kullanıcı tarafından atanan yönetilen kimliği bir VM 'den kaldırma

Kullanıcı tarafından atanan kimliği bir VM 'den kaldırmak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir. Ek Azure AD dizin rolü ataması gerekli değildir.

1. VM 'yi içeren Azure aboneliğiyle ilişkili bir hesabı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
2. İstediğiniz sanal makineye gidin ve **kimlik**, **Kullanıcı atandı**, silmek istediğiniz kullanıcı tarafından atanan yönetilen kimliğin adı ' na tıklayın ve ardından **Kaldır** ' a tıklayın (onay bölmesinde **Evet** ' e tıklayın).

   ![Kullanıcı tarafından atanan yönetilen kimliği bir VM 'den kaldırma](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Sonraki adımlar

- Azure portal kullanarak, bir Azure VM 'nin yönetilen kimlik [erişimini başka bir Azure kaynağına](howto-assign-access-portal.md)verin.