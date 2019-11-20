---
title: Azure Site Recovery veri şifreleme özelliğinin kullanımdan kaldırılması | Microsoft Docs
description: Ayrıntılar Azure Site Recovery veri şifreleme özelliği
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135002"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Site Recovery veri şifreleme özelliğinin kullanımdan kaldırılması

Bu belge, Hyper-V sanal makinelerini Azure 'a olağanüstü durum kurtarmayı yapılandırırken Site Recovery veri şifreleme özelliğini kullanıyorsanız uygulamanız gereken kullanımdan kaldırma ayrıntılarını ve düzeltme eylemini açıklar. 

## <a name="deprecation-information"></a>Kullanımdan kaldırma bilgileri


Site Recovery veri şifreleme özelliği, çoğaltılan verilerin güvenlik tehditlerine karşı korunmasını sağlamak üzere Hyper-V VM 'lerini koruyan müşteriler için kullanılabilir. Bu özellik **30 aralık 2019 ' de**kullanım dışı bırakılacak. [Depolama hizmeti şifrelemesi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE) kullanan Rest özelliğinde daha gelişmiş [şifreleme](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) ile değiştiriliyor. SSE ile, veriler depolama alanı kalıcı hale gelmeden ve geri alınmadan önce şifrelenir ve Azure 'a yük devretmeden VM 'niz, geliştirilmiş kurtarma süresi hedefi (RTO) için şifrelenmiş depolama hesaplarından çalıştırılır.

Bu özelliği kullanan mevcut bir müşteriyseniz, kullanımdan kaldırma ayrıntıları ve düzeltme adımlarıyla iletişim aldığınızı lütfen unutmayın. 


## <a name="what-are-the-implications"></a>Etkileri nelerdir?

**30 aralık 2019**' den sonra, kullanımdan kaldırılan şifreleme özelliğini kullanmaya devam eden tüm VM 'lerin yük devretme yapmasına izin verilmez. 

## <a name="required-action"></a>Gerekli eylem
Başarılı yük devretme işlemlerine devam etmek için, çoğaltmalar aşağıda bahsedilen adımları izler:

Her VM için aşağıdaki adımları izleyin: 
1.  [Çoğaltmayı devre dışı bırakın](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Yeni bir çoğaltma Ilkesi oluşturun](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy).
3.  [Çoğaltmayı etkinleştirin](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) ve SSE etkin bir depolama hesabı seçin.

SSE etkinken depolama hesaplarına ilk çoğaltmayı tamamladıktan sonra, VM 'niz Azure Site Recovery ile bekleyen şifrelemeyi kullanacaktır.


## <a name="next-steps"></a>Sonraki adımlar
Düzeltme adımlarını gerçekleştirmeyi planlayın ve bunları en erken yürütün. Bu kullanımdan kaldırma ile ilgili herhangi bir sorgunuz varsa, lütfen Microsoft Desteği ulaşın. Hyper-V ' d e Azure senaryosu hakkında daha fazla bilgi edinmek için [buraya](hyper-v-vmm-architecture.md)bakın.

