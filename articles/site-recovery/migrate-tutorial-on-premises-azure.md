---
title: Azure geçişi ile şirket içi makineleri geçirme
description: Bu makalede, şirket içi makinelerin Azure 'a nasıl geçirileceği özetlenmektedir ve Azure geçişi önerilmektedir.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: 5f5a0f65bfbde5ef8513780933cc570a4b5737df
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581142"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Şirket içi makineleri Azure’a geçirme

Bu makalede, şirket içi makineleri Azure 'a geçirme seçenekleri açıklanmaktadır. 

## <a name="migrate-with-azure-migrate"></a>Azure geçişi ile geçiş

[Azure geçişi](../migrate/migrate-services-overview.md) hizmetini kullanarak makineleri Azure 'a geçirmeniz önerilir. Azure geçişi, sunucu geçişi için oluşturulmuştur. Azure geçişi, şirket içi makinelerin Azure 'a bulunması, değerlendirilmesi ve geçirilmesi için merkezi bir merkez sağlar.

Azure geçişi ile geçiş yapmak için bu bağlantıları izleyin:

- VMware VM 'Leri için geçiş seçenekleri [hakkında bilgi edinin](../migrate/server-migrate-overview.md) , ardından sanal makineleri [aracısız](../migrate/tutorial-migrate-vmware.md) veya [aracı tabanlı](../migrate/tutorial-migrate-vmware-agent.md) geçişle Azure 'a geçirin.
- [Hyper-V VM](../migrate/tutorial-migrate-hyper-v.md) 'lerini Azure 'a geçirin.
- [AWS örnekleri](../migrate/tutorial-migrate-aws-virtual-machines.md) de dahil olmak üzere [fiziksel sunucuları veya diğer VM 'leri](../migrate/tutorial-migrate-physical-virtual-machines.md)Azure 'a geçirin.

## <a name="migrate-with-site-recovery"></a>Site Recovery ile geçir
Site Recovery, yalnızca olağanüstü durum kurtarma için kullanılmalıdır, geçiş değildir.

Zaten Azure Site Recovery kullanıyorsanız ve geçiş için kullanmaya devam etmek istiyorsanız, olağanüstü durum kurtarma için kullandığınız adımların aynısını izleyin.

- VMware VM 'Leri: Azure ve [VMware](vmware-azure-tutorial-prepare-on-premises.md)'yi [hazırlayın](tutorial-prepare-azure.md) , [makineleri çoğaltmaya](vmware-azure-tutorial.md)başlayın, her şeyin çalıştığından emin [olun](tutorial-dr-drill-azure.md) ve [Yük devretme çalıştırın](vmware-azure-tutorial-failover-failback.md).
- Hyper-V VM 'Leri: [Azure](tutorial-prepare-azure-for-hyperv.md) ve [Hyper-v](hyper-v-prepare-on-premises-tutorial.md)' y i hazırlayın, [makineleri çoğaltmaya](hyper-v-azure-tutorial.md)başlayın, her şeyin çalıştığından emin [olun](tutorial-dr-drill-azure.md) ve [Yük devretme çalıştırın](hyper-v-azure-failover-failback-tutorial.md).
- Fiziksel sunucular: Azure 'u hazırlama, olağanüstü durum kurtarma için makineler hazırlama ve çoğaltmayı ayarlama hakkında [izlenecek yolu izleyin](physical-azure-disaster-recovery.md) .

> [!NOTE]
> Olağanüstü durum kurtarma için bir yük devretme işlemi çalıştırdığınızda, son bir adım olarak yük devretmeyi yürütmeniz gerekir. Şirket içi makineleri geçirdiğinizde, **COMMIT** seçeneği ilgili değildir. Bunun yerine, **geçişi Tamam** seçeneğini belirleyin. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> Azure geçişi ile ilgili [sık sorulan soruları gözden geçirin](../migrate/resources-faq.md) .

  
