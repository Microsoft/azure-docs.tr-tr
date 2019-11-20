---
title: Azure VM 'lerini yeni aboneliğe veya kaynak grubuna taşıma
description: Sanal makineleri yeni bir kaynak grubuna veya aboneliğe taşımak için Azure Resource Manager kullanın.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: b03e9618e621216f74cb02828183df7ee6b502ea
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150924"
---
# <a name="move-guidance-for-virtual-machines"></a>Sanal makineler için taşıma Kılavuzu

Bu makalede, şu anda desteklenmeyen senaryolar ve sanal makineleri yedekleme ile taşıma adımları açıklanır.

## <a name="scenarios-not-supported"></a>Senaryolar desteklenmez

Aşağıdaki senaryolar henüz desteklenmemektedir:

* Kullanılabilirlik Alanları yönetilen diskler farklı bir aboneliğe taşınamaz.
* Standart SKU 'SU Load Balancer veya standart SKU genel IP 'si olan sanal makine ölçek kümeleri taşınamaz.
* Market kaynaklardan bağlı planlar ile oluşturulan sanal makineler, kaynak grubu veya abonelik arasında taşınamaz. Sanal makinenin geçerli abonelikte sağlamasını kaldırın ve yeni abonelikte yeniden dağıtın.
* Sanal ağdaki tüm kaynakları taşımadan, var olan bir sanal ağdaki sanal makineler yeni bir aboneliğe taşınamaz.
* Düşük öncelikli sanal makineler ve düşük öncelikli sanal makine ölçek kümeleri kaynak grupları veya abonelikler arasında taşınamaz.
* Bir kullanılabilirlik kümesindeki sanal makineler tek tek taşınamaz.

## <a name="virtual-machines-with-azure-backup"></a>Azure Backup olan sanal makineler

Azure Backup ile yapılandırılmış sanal makineleri taşımak için aşağıdaki geçici çözümü kullanın:

* Sanal makinenizin konumunu bulun.
* Şu adlandırma düzenine sahip bir kaynak grubu bulun: `AzureBackupRG_<location of your VM>_1` Örneğin, AzureBackupRG_westus2_1
* Azure portalında, ardından onay "gizli türleri Göster ise"
* PowerShell'de varsa, kullanmak `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet'i
* CLI, kullanın `az resource list -g AzureBackupRG_<location of your VM>_1`
* Adlandırma düzenine sahip `Microsoft.Compute/restorePointCollections` türündeki kaynağı bulun `AzureBackup_<name of your VM that you're trying to move>_###########`
* Bu kaynağı silin. Bu işlem, kasadaki yedeklenen verileri değil yalnızca anlık kurtarma noktalarını siler.
* Silme işlemi tamamlandıktan sonra, kasayı ve sanal makineyi hedef aboneliğe taşıyabilirsiniz. Taşıma işleminden sonra verilerde kayıp olmadan yedeklemeye devam edebilirsiniz.
* Kurtarma hizmeti kasalarını yedekleme için taşıma hakkında daha fazla bilgi için bkz. [Kurtarma Hizmetleri sınırlamaları](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Sonraki adımlar

Kaynakları taşıma komutları için bkz. [kaynakları yeni kaynak grubuna veya aboneliğe taşıma](../resource-group-move-resources.md).
