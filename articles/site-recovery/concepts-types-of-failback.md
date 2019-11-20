---
title: Azure Site Recovery ile olağanüstü durum kurtarma sırasında yeniden çalışma | Microsoft Docs
description: Bu makalede, Azure Site Recovery hizmeti ile olağanüstü durum kurtarma sırasında şirket içi duruma geri dönirken dikkate alınacak farklı çalışma ve uyarılarla ilgili bir genel bakış sunulmaktadır.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: c0eaf28f9aeb4050fd35a6036a53e3e91d00f3eb
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847479"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>Azure 'a olağanüstü durum kurtarma sonrasında VMware VM 'Leri yeniden çalışma

Olağanüstü durum kurtarma işleminizin bir parçası olarak Azure 'a yük devretmeye başladıktan sonra şirket içi sitenize geri dönebilirsiniz. Azure Site Recovery için olası iki farklı yeniden çalışma türü vardır: 

- Özgün konuma geri dönme 
- Alternatif bir konuma geri dönme

Bir VMware sanal makinesi üzerinden yük devretseniz, hala varsa aynı kaynak şirket içi sanal makineye geri dönebilirsiniz. Bu senaryoda, yalnızca değişiklikler geri çoğaltılır. Bu senaryo **özgün konum kurtarma**olarak bilinir. Şirket içi sanal makine yoksa, senaryo **alternatif bir konum kurtarması**olur.

> [!NOTE]
> Yalnızca özgün vCenter ve Configuration sunucusuna geri dönebilirsiniz. Yeni bir yapılandırma sunucusu dağıtamazsınız ve kullanarak yeniden başarısız olabilirsiniz. Ayrıca, var olan yapılandırma sunucusuna yeni bir vCenter ekleyemez ve yeni vCenter 'da yeniden çalışma işlemi yapılamaz.

## <a name="original-location-recovery-olr"></a>Özgün konum kurtarma (OLR)
Özgün sanal makineye yeniden yük devri seçerseniz, aşağıdaki koşulların karşılanması gerekir:

* Sanal makine bir vCenter sunucusu tarafından yönetiliyorsa, ana hedefin ESX konağının sanal makinenin veri deposuna erişimi olmalıdır.
* Sanal makine bir ESX ana makineiyorsa ancak vCenter tarafından yönetilmiyorsa, sanal makinenin Sabit diski ana hedefin ana bilgisayarın erişebileceği bir veri deposunda olmalıdır.
* Sanal makineniz bir ESX ana makinedir ve vCenter kullanmıyorsa, yeniden korumadan önce ana hedefin ESX konağını bulmayı tamamlamalısınız. Bu, fiziksel sunucuları da geri yüklüyorsanız geçerlidir.
* Diskler zaten varsa ve şirket içi sanal makineye bağlıysa, sanal depolama alanı ağına (vSAN) veya ham cihaz eşlemesi (RDM) tabanlı bir diske geri dönebilirsiniz.

> [!IMPORTANT]
> Yeniden çalışma sırasında, Azure Site Recovery hizmetinin bekleyen değişikliklerin yazılacağı sanal makinede orijinal VMDK belirleyebilmesi için disk. Enableuuıd = TRUE ' ı etkinleştirmeniz önemlidir. Bu değer TRUE olarak ayarlanmamışsa hizmet, ilgili şirket içi VMDK 'yı en iyi çaba temelinde belirlemeyi dener. Sağ VMDK bulunamazsa, ek bir disk oluşturur ve verilerin üzerine yazılır.

## <a name="alternate-location-recovery-alr"></a>Alternatif Konum Kurtarma (ALR)
Sanal makineyi yeniden korumadan önce şirket içi sanal makine yoksa, senaryoya alternatif bir konum kurtarma denir. Yeniden koru iş akışı, şirket içi sanal makineyi yeniden oluşturur. Bu, tam veri indirmesine de neden olur.

* Alternatif bir konuma geri döndüğünüzde, sanal makine ana hedef sunucunun dağıtıldığı aynı ESX konağına kurtarılır. Diski oluşturmak için kullanılan veri deposu, sanal makine yeniden korunurken seçilen veri deposu olacaktır.
* Yalnızca bir sanal makine dosya sistemi (VMFS) veya vSAN veri deposuna yeniden yük devreedebilirsiniz. Bir RDM varsa, yeniden koruma ve yeniden çalışma işlemi çalışmaz.
* Yeniden koruma, daha sonra değişiklikler tarafından izlenen bir büyük ilk veri aktarımı içerir. Bu işlem, sanal makine şirket içinde bulunmadığından oluşur. Verilerin tamamı geri çoğaltılmalıdır. Bu yeniden koruma, orijinal bir konum kurtarmasından daha fazla zaman alır.
* RDM tabanlı disklere yeniden yük devredemezsiniz. Bir VMFS/vSAN veri deposunda yalnızca yeni sanal makine diskleri (VMDK) oluşturulabilir.

> [!NOTE]
> Azure 'a yük devretme yapıldığında bir fiziksel makine, yalnızca VMware sanal makinesi olarak yeniden başarısız olabilir. Bu, alternatif konum kurtarma ile aynı iş akışını izler. En az bir ana hedef sunucusu ve geri dönmesi gereken gerekli ESX/ESXi konaklarını bultığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Yeniden [çalışma işlemini](vmware-azure-failback.md)gerçekleştirmek için adımları izleyin.

