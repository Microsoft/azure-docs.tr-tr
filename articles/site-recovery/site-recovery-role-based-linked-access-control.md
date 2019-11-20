---
title: Azure Site Recovery 'de Azure rol tabanlı erişim denetimini yönetme
description: Bu makalede, Azure Site Recovery erişimini yönetmek için rol tabanlı erişim denetimi 'nin (RBAC) nasıl uygulanacağı açıklanır.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: ce389f9281b02662f87353f00c9bca92cdf86937
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083772"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Rol tabanlı erişim denetimi (RBAC) ile Site Recovery erişimini yönetme

Azure rol tabanlı erişim denetimi (RBAC), Azure için ayrıntılı erişim yönetimine izin vermez. RBAC kullanarak, takımlarınızın içindeki sorumlulukları ayırabilirsiniz ve belirli işleri gerçekleştirmek için gerektiğinde kullanıcılara yalnızca belirli erişim izinleri verebilirsiniz.

Azure Site Recovery, Site Recovery yönetim işlemlerini denetlemek için 3 yerleşik rol sağlar. [Azure RBAC yerleşik rolleri](../role-based-access-control/built-in-roles.md) hakkında daha fazla bilgi edinin

* [Site Recovery Katkıda Bulunanı](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - Bu rol, Kurtarma Hizmetleri kasasında Site Recovery işlemlerini yönetmek için gereken tüm izinlere sahiptir. Ancak bu role sahip kullanıcı, Kurtarma Hizmetleri kasasını oluşturamaz veya silemez ya da diğer kullanıcılara erişim hakkı atayamaz. Bu rol, büyük/küçük harf gibi durumlarda uygulamalar veya tüm kuruluşlar için olağanüstü durum kurtarmayı etkinleştirebilen ve yönetebilen olağanüstü durum kurtarma yöneticileri için idealdir.
* [Site Recovery Operatörü](../role-based-access-control/built-in-roles.md#site-recovery-operator) - Bu rol, Yük Devretme ve Yeniden Çalışma işlemlerini yürütme ve yönetme izinlerine sahiptir. Bu role sahip bir Kullanıcı çoğaltmayı etkinleştiremez veya devre dışı bırakamayabilir, kasa oluşturabilir veya silebilir, yeni altyapıyı kaydedebilir veya diğer kullanıcılara erişim hakları atayabilir. Bu rol, uygulama sahipleri ve BT yöneticileri tarafından bir DR detaya gitme gibi gerçek ya da sanal bir olağanüstü durum durumunda, sanal makinelerin veya uygulamaların yükünü önleyebilen bir olağanüstü durum kurtarma operatörü için en uygun seçenektir. Olağanüstü durum çözümlemesi sonrasında DR işletmeni, sanal makineleri yeniden koruyabilir ve yeniden gerçekleştirebilir.
* [Site Recovery Okuyucusu](../role-based-access-control/built-in-roles.md#site-recovery-reader) - Bu rol tüm Site Recovery yönetim işlemlerini görüntüleme iznine sahiptir. Bu rol, güncel koruma durumunu izleyebilen ve gerekirse destek biletleri veren BT izleme Yöneticisi için idealdir.

Daha da fazla denetim için kendi rollerinizi tanımlamak istiyorsanız bkz. Azure 'da [özel roller oluşturma](../role-based-access-control/custom-roles.md) .

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Yeni sanal makineler için çoğaltmayı etkinleştirmek için gereken izinler
Yeni bir sanal makine Azure Site Recovery kullanılarak Azure 'a çoğaltıldığında, kullanıcının Site Recovery için belirtilen Azure kaynaklarını kullanmak için gerekli izinlere sahip olduğundan emin olmak için ilişkili kullanıcının erişim düzeyleri onaylanır.

Yeni bir sanal makine için çoğaltmayı etkinleştirmek üzere bir Kullanıcı şunları içermelidir:
* Seçili kaynak grubunda sanal makine oluşturma izni
* Seçilen sanal ağda sanal makine oluşturma izni
* Seçilen depolama hesabına yazma izni

Bir kullanıcının yeni bir sanal makinenin çoğaltmasını tamamlaması için aşağıdaki izinlere ihtiyacı vardır.

> [!IMPORTANT]
>Kaynak dağıtımı için kullanılan dağıtım modeli (Kaynak Yöneticisi/klasik) başına ilgili izinlerin eklendiğinden emin olun.

> [!NOTE]
> Bir Azure VM için çoğaltmayı etkinleştirip Site Recovery güncelleştirmeleri yönetmesine izin vermek istiyorsanız, çoğaltmayı etkinleştirirken Ayrıca, aynı zamanda bir Otomasyon hesabı oluşturmak için izninizin olması gereken yeni bir Otomasyon hesabı oluşturmak isteyebilirsiniz. kasa olarak da abonelik.

| **Kaynak Türü** | **Dağıtım modeli** | **Yetkisi** |
| --- | --- | --- |
| Bilgi İşlem | Resource Manager | Microsoft. COMPUTE/kullanılabilirliği Bilitysets/Read |
|  |  | Microsoft. COMPUTE/virtualMachines/okuma |
|  |  | Microsoft. COMPUTE/virtualMachines/Write |
|  |  | Microsoft. COMPUTE/virtualMachines/Delete |
|  | Klasik | Microsoft. ClassicCompute/domainNames/Read |
|  |  | Microsoft. ClassicCompute/domainNames/Write |
|  |  | Microsoft. ClassicCompute/domainNames/Delete |
|  |  | Microsoft. ClassicCompute/virtualMachines/okuma |
|  |  | Microsoft. ClassicCompute/virtualMachines/Write |
|  |  | Microsoft. ClassicCompute/virtualMachines/Delete |
| Ağ | Resource Manager | Microsoft. Network/NetworkInterfaces/Read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft. Network/NetworkInterfaces/Delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft. Network/virtualNetworks/Read |
|  |  | Microsoft. Network/virtualNetworks/alt ağlar/okuma |
|  |  | Microsoft. Network/virtualNetworks/alt ağlar/JOIN/Action |
|  | Klasik | Microsoft. ClassicNetwork/virtualNetworks/Read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Depolama | Resource Manager | Microsoft. Storage/storageAccounts/Read |
|  |  | Microsoft. Storage/storageAccounts/ListKeys/Action |
|  | Klasik | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Kaynak Grubu | Resource Manager | Microsoft. resources/dağıtımlar/* |
|  |  | Microsoft. resources/abonelikler/resourceGroups/Read |

Sırasıyla Kaynak Yöneticisi ve klasik dağıtım modelleriyle ilgili ' sanal makine katılımcısı ' ve ' klasik sanal makine katılımcısı ' [yerleşik rollerini](../role-based-access-control/built-in-roles.md) kullanmayı göz önünde bulundurun.

## <a name="next-steps"></a>Sonraki adımlar
* [Rol tabanlı Access Control](../role-based-access-control/role-assignments-portal.md): Azure Portal RBAC ile çalışmaya başlayın.
* İle erişimin nasıl yönetileceğini öğrenin:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Rol tabanlı Access Control sorun giderme](../role-based-access-control/troubleshooting.md): yaygın sorunları gidermeye yönelik öneriler alın.
