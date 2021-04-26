---
title: Azure hizmet kesintileri
description: Azure hizmet çalışmasının Azure sanal makinelerini etkilediği olayda ne yapılacağını öğrenin.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/31/2017
ms.author: cynthn
ms.reviewer: ''
ms.openlocfilehash: 5c6036a65fa449986f5c085202fefa752e3a414a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98701042"
---
# <a name="what-if-an-azure-service-disruption-impacts-azure-vms"></a>Azure hizmet kesintisi, Azure sanal makinelerini etkiler

Microsoft 'ta, hizmetlerimizin ihtiyacınız olduğunda her zaman sizin için kullanılabilir olduğundan emin olmak için çok çalıştık. Denetiimizin ötesine geçmeye, planlanmamış hizmet kesintilerine neden olacak şekilde bizi etkilemekte yarar vardır.

Microsoft, hizmet için çalışma süresi ve bağlantı taahhüdünde bir Hizmet Düzeyi Sözleşmesi (SLA) sağlar. Bireysel Azure hizmetleri için SLA, [Azure hizmet düzeyi sözleşmeleri](https://azure.microsoft.com/support/legal/sla/)' nde bulunabilir.

Azure 'da, yüksek oranda kullanılabilir uygulamaları destekleyen birçok yerleşik platform özelliği zaten var. Bu hizmetler hakkında daha fazla bilgi edinmek için bkz. [Azure uygulamaları Için olağanüstü durum kurtarma ve yüksek kullanılabilirlik](/azure/architecture/framework/resiliency/backup-and-recovery).

Bu makalede, bir bütün bölge ana doğal olağanüstü durum veya geniş çaplı hizmet kesintisi nedeniyle bir kesinti yaşandığında, doğru bir olağanüstü durum kurtarma senaryosu ele alınmaktadır. Bunlar nadir oluşumlardır, ancak bir bölgenin tamamı için bir kesinti olması olasılığa hazırlanmanız gerekir. Bir bölgenin tamamı bir hizmet kesintisi yaşıyorsa, verilerinizin yerel olarak yedekli kopyaları geçici olarak devre dışı olur. Coğrafi çoğaltmayı etkinleştirdiyseniz, Azure Storage bloblarınızın ve tablolarının üç ek kopyası farklı bir bölgede depolanır. Tam bir bölgesel kesinti veya birincil bölgenin kurtarılabilir olmadığı bir olağanüstü durum durumunda Azure, tüm DNS girdilerini coğrafi olarak çoğaltılan bölgeye yeniden eşler.

Bu nadir oluşumları işleyebilmeniz için, Azure sanal makine uygulamanızın dağıtıldığı bölgenin tamamına yönelik bir hizmet kesintisi olması durumunda Azure sanal makineleri için aşağıdaki kılavuza sağlıyoruz.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Seçenek 1: Azure Site Recovery kullanarak yük devretme başlatma
Uygulamanızı birkaç dakika içinde tek bir tıklama ile kurtarabilmeniz için sanal makinelerinize yönelik Azure Site Recovery yapılandırabilirsiniz. Tercih ettiğiniz Azure bölgesine çoğaltabilir ve eşleştirilmiş bölgelerle sınırlı değildir. [Sanal makinelerinizi çoğaltarak](../site-recovery/azure-to-azure-quickstart.md)çalışmaya başlamanızı sağlayabilirsiniz. Uygulamanız için tüm yük devretme sürecini otomatikleştirebilmek üzere [bir kurtarma planı oluşturabilirsiniz](../site-recovery/site-recovery-create-recovery-plans.md) . Üretim uygulamasını veya devam eden Çoğaltmayı etkilemeden yük devretmeleri önceden [Test](../site-recovery/site-recovery-test-failover-to-azure.md) edebilirsiniz. Birincil bölge kesintisi durumunda, yalnızca [bir yük devretme başlatın](../site-recovery/site-recovery-failover.md) ve uygulamanızı hedef bölgeye getirin.


## <a name="option-2-wait-for-recovery"></a>Seçenek 2: kurtarma için bekle
Bu durumda, sizin bölüminizdeki hiçbir işlem yapmanız gerekmez. Hizmet kullanılabilirliğini geri yüklemek için çalıştık bir çalışmamız olduğunu öğrenin. Geçerli hizmet durumunu [Azure hizmet durumu panoımızda](https://azure.microsoft.com/status/)görebilirsiniz.

Bu, kesintiye uğramadan önce Azure Site Recovery, Okuma Erişimli Coğrafi olarak yedekli depolama veya coğrafi olarak yedekli depolama alanı ayarlamadıysanız en iyi seçenektir. VM sanal sabit sürücülerinizin (VHD) depolandığı depolama hesabı için coğrafi olarak yedekli depolama veya Okuma Erişimli Coğrafi olarak yedekli depolama ayarladıysanız, temel görüntü VHD 'sini kurtarmaya ve bundan sonra yeni bir VM sağlamaya çalışırsanız bakabilirsiniz. Verilerin eşitlenmesi garantisi olmadığından, bu tercih edilen bir seçenek değildir. Sonuç olarak, bu seçeneğin çalışması garanti edilmez.


> [!NOTE]
> Bu işlem üzerinde herhangi bir denetiminiz olmadığı ve yalnızca bölge genelinde hizmet kesintileri için gerçekleşmeyeceği hakkında dikkat edin. Bu nedenle, en yüksek kullanılabilirlik düzeyini elde etmek için uygulamaya özgü diğer yedekleme stratejilerine de güvenmelidir. Daha fazla bilgi için [olağanüstü durum kurtarma Için veri stratejileri](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)bölümüne bakın.
>
>

## <a name="next-steps"></a>Sonraki adımlar

- Azure Site Recovery kullanarak [Azure sanal makinelerinde çalışan uygulamalarınızı korumaya](../site-recovery/azure-to-azure-quickstart.md) başlayın

- Olağanüstü durum kurtarma ve yüksek kullanılabilirlik stratejisi uygulama hakkında daha fazla bilgi edinmek için bkz. [Azure uygulamaları Için olağanüstü durum kurtarma ve yüksek kullanılabilirlik](/azure/architecture/framework/resiliency/backup-and-recovery).

- Bulut platformunun yeteneklerini ayrıntılı bir şekilde anlamak için bkz. [Azure dayanıklılığı teknik kılavuzu](../data-lake-store/data-lake-store-disaster-recovery-guidance.md).


- Yönergeler net değilse veya Microsoft 'un sizin adınıza işlem yapması istiyorsanız [müşteri desteği](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)'ne başvurun.