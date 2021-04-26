---
title: Azure DevTest Labs 'de müşteri tarafından yönetilen anahtarları kullanarak işletim sistemi disklerini şifreleyin
description: Azure DevTest Labs 'de müşteri tarafından yönetilen anahtarları kullanarak işletim sistemi (OS) disklerini şifrelemeyi öğrenin.
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 26ef4ff1529483da9956c6dcc43807af0ffd6463
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96341229"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Azure DevTest Labs 'de müşteri tarafından yönetilen anahtarları kullanarak işletim sistemi (OS) disklerini şifreleyin
Sunucu tarafı şifreleme (SSE) verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur. SSE, Azure 'da (işletim sistemi ve veri diskleri) yönetilen disklerde depolanan verilerinizi otomatik olarak buluta kalıcı hale getirerek varsayılan olarak şifreler. Azure 'da [disk şifrelemesi](../virtual-machines/disk-encryption.md) hakkında daha fazla bilgi edinin. 

DevTest Labs içinde, bir laboratuvarın parçası olarak oluşturulan tüm işletim sistemi diskleri ve veri diskleri, platform tarafından yönetilen anahtarlar kullanılarak şifrelenir. Ancak, bir laboratuar sahibi olarak, kendi anahtarlarınızı kullanarak laboratuvar sanal makine işletim sistemi disklerini şifrelemeyi tercih edebilirsiniz. Şifrelemeyi kendi anahtarlarınız ile yönetmeyi seçerseniz, laboratuvar işletim sistemi disklerindeki verileri şifrelemek için kullanılacak **müşteri tarafından yönetilen bir anahtar** belirtebilirsiniz. Müşteri tarafından yönetilen anahtarlarla sunucu tarafı şifreleme (SSE) hakkında daha fazla bilgi edinmek ve diğer yönetilen disk şifreleme türleri hakkında daha fazla bilgi için bkz. [müşteri tarafından yönetilen anahtarlar](../virtual-machines/disk-encryption.md#customer-managed-keys). Ayrıca bkz. [müşteri tarafından yönetilen anahtarları kullanmayla ilgili kısıtlamalar](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions).

> [!NOTE]
> - Şu anda, müşteri tarafından yönetilen bir anahtarla disk şifrelemesi yalnızca DevTest Labs içindeki işletim sistemi diskleri için desteklenir. 
> 
> - Bu ayar, laboratuvardaki yeni oluşturulan işletim sistemi diskleri için geçerlidir. Bir noktada disk şifrelemesi kümesini değiştirmeyi seçerseniz, laboratuvardaki eski diskler önceki disk şifreleme kümesi kullanılarak şifreli olmaya devam edecektir. 

Aşağıdaki bölümde, bir laboratuar sahibinin, müşteri tarafından yönetilen anahtar kullanarak şifrelemeyi nasıl ayarlayagösterdiği gösterilmektedir.

## <a name="pre-requisites"></a>Ön koşullar

1. Bir disk şifreleme ayarladıysanız, bir [Key Vault ve bir disk şifreleme kümesi ayarlamak](../virtual-machines/disks-enable-customer-managed-keys-portal.md)için bu makaleyi izleyin. Disk şifreleme kümesi için aşağıdaki gereksinimleri dikkate alın: 

    - Disk şifreleme kümesinin **laboratuvarınız ile aynı bölgede ve abonelikte** olması gerekir. 
    - Laboratuvar işletim sistemi disklerini şifrelemek için kullanılacak disk şifreleme kümesine en az bir **okuyucu düzeyinde erişime** sahip olduğunuzdan emin olun. 
1. 8/1/2020 ' den önce oluşturulan laboratuvarlar için laboratuvar sahibinin laboratuvar sistemi tarafından atanan kimliğin etkinleştirildiğinden emin olması gerekir. Bunu yapmak için, laboratuvar sahibi laboratuvarına gidebilir, **yapılandırma ve ilkeler**' e tıklayabilir, **kimlik (Önizleme)** dikey penceresine tıklayabilir, sistem tarafından atanan kimlik **durumunu** **Açık** olarak değiştirebilir ve **Kaydet**' e tıklayabilirsiniz. 8/1/2020 Laboratuvarı sistem tarafından atanan kimlik ile oluşturulan yeni laboratuvarlar için varsayılan olarak etkinleştirilir. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="Yönetilen Anahtarlar":::
1. Laboratuvarın tüm laboratuar işletim sistemi diskleri için şifrelemeyi işlemesi için, laboratuvar sahibinin, temel alınan Azure aboneliğinde sanal makine katılımcısı rolü ' ne ek olarak, laboratuvar sahibinin, disk şifreleme kümesi ve sanal makine katılımcısı rolü üzerinde **sistem tarafından atanan kimlik** okuyucusu rolünü açıkça vermesi gerekir. Laboratuvar sahibi bunu aşağıdaki adımları tamamlayarak yapabilir:

   
    1. Azure kaynaklarına Kullanıcı erişimini yönetebilmeniz için Azure abonelik düzeyinde [Kullanıcı erişimi Yöneticisi rolünün](../role-based-access-control/built-in-roles.md#user-access-administrator) bir üyesi olduğunuzdan emin olun. 
    1. **Disk şifreleme kümesi** sayfasında, Sol menüdeki **ERIŞIM denetimi (IAM)** seçeneğini belirleyin. 
    1. Araç çubuğunda **+ Ekle** ' yi seçin ve **rol ataması Ekle**' yi seçin.  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="Rol yönetimi ekleme-menü":::
    1. **Rol ataması Ekle** sayfasında, **okuyucu** rolü veya daha fazla erişime izin veren bir rol seçin. 
    1. Disk şifrelemesi kümesinin kullanılacağı laboratuvar adını yazın ve açılan listeden laboratuvar adını (Laboratuvar için sistem tarafından atanan kimlik) seçin. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="Laboratuvarın sistem tarafından yönetilen kimliğini seçin":::        
    1. Araç çubuğunda **Kaydet**’i seçin. 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="Rol atamasını Kaydet":::
3. **Abonelik** erişim denetimi (IAM) sayfasını kullanarak, laboratuvarın **sistem tarafından atanan kimliğini** **sanal makine katılımcısı** rolüne ekleyin  ->   . Adımlar önceki adımlarda olanlarla benzerdir. 

    
    1. Azure portal **abonelik** sayfasına gidin. 
    1. **Erişim denetimi (IAM)** öğesini seçin. 
    1. Araç çubuğunda **+ Ekle** ' yi seçin ve **rol ataması Ekle**' yi seçin. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="Abonelik-> Access Control (ıAM) sayfası":::
    1. **Rol ataması Ekle** sayfasında, rol Için **sanal makine katılımcısı** ' nı seçin.
    1. Laboratuvar adını yazın ve açılan listeden **Laboratuvar adını** (Laboratuvar için sistem tarafından atanan kimlik) seçin. 
    1. Araç çubuğunda **Kaydet**’i seçin. 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>Müşteri tarafından yönetilen bir anahtarla laboratuar işletim sistemi disklerini şifreleme 

1. Laboratuvarınızın giriş sayfasında, Azure portal Sol menüdeki **yapılandırma ve ilkeler** ' i seçin. 
1. **Yapılandırma ve ilkeler** sayfasında, **şifreleme** bölümünde **diskler (Önizleme)** öğesini seçin. Varsayılan olarak, **şifreleme türü** **Platform yönetilen anahtarıyla bekleyen olarak şifreleme** olarak ayarlanır.

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="Yapılandırma ve ilkeler sayfasının diskler sekmesi":::
1. **Şifreleme türü** için, açılan listeden **müşteri tarafından yönetilen bir anahtarla bekleyen şifreleme** ' yi seçin. 
1. **Disk şifreleme kümesi** için, daha önce oluşturduğunuz disk şifreleme kümesini seçin. Bu, laboratuvar için sistem tarafından atanan kimliğin erişebileceği disk şifrelemesi kümesidir.
1. Araç çubuğunda **Kaydet**’i seçin. 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="Müşteri tarafından yönetilen anahtarla şifrelemeyi etkinleştir":::
1. İleti kutusunda aşağıdaki metinle: *Bu ayar, laboratuvardaki yeni oluşturulan makineler için geçerlidir. Eski işletim sistemi diski eski disk şifrelemesi kümesiyle şifreli kalacak*, **Tamam**' ı seçin. 

    Bir kez yapılandırıldıktan sonra, laboratuvar işletim sistemi diskleri disk şifreleme kümesi kullanılarak sunulan müşteri tarafından yönetilen anahtarla şifrelenir. 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>Disklerin şifrelenip şifrelenmediğini doğrulama

1. Laboratuvar üzerinde müşteri tarafından yönetilen bir anahtarla disk şifrelemeyi etkinleştirdikten sonra oluşturulan bir laboratuvar sanal makinesine gidin.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="Etkin disk şifrelemesi olan VM":::
1. VM 'nin kaynak grubuna tıklayın ve işletim sistemi diskine tıklayın.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="VM kaynak grubu":::
1. Şifreleme ' ye gidin ve şifrelemenin, seçtiğiniz disk şifrelemesi kümesiyle müşterinin yönettiği anahtar olarak ayarlandığını doğrulayın.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="Şifrelemeyi doğrula":::
  
## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelere bakın: 

- [Azure disk şifrelemesi](../virtual-machines/disk-encryption.md). 
- [Müşteri tarafından yönetilen anahtarlar](../virtual-machines/disk-encryption.md#customer-managed-keys)