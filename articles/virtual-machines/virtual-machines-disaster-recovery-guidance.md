---
title: Azure VM 'Leri için olağanüstü durum kurtarma senaryoları | Microsoft Docs
description: Azure hizmet çalışmasının Azure sanal makinelerini etkilediği olayda ne yapılacağını öğrenin.
services: virtual-machines
documentationcenter: ''
author: kmouss
manager: gwallace
editor: ''
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: gwallace
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b59343fca5e14d211864a1354001819c918bd1ca
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891512"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Azure hizmeti 'nin kesintiye uğramaması durumunda Azure VM 'Leri etkileyen olay
Microsoft 'ta, hizmetlerimizin ihtiyacınız olduğunda her zaman sizin için kullanılabilir olduğundan emin olmak için çok çalıştık. Denetiimizin ötesine geçmeye, planlanmamış hizmet kesintilerine neden olacak şekilde bizi etkilemekte yarar vardır.

Microsoft, hizmet için çalışma süresi ve bağlantı taahhüdünde bir Hizmet Düzeyi Sözleşmesi (SLA) sağlar. Bireysel Azure hizmetleri için SLA, [Azure hizmet düzeyi sözleşmeleri](https://azure.microsoft.com/support/legal/sla/)' nde bulunabilir.

Azure 'da, yüksek oranda kullanılabilir uygulamaları destekleyen birçok yerleşik platform özelliği zaten var. Bu hizmetler hakkında daha fazla bilgi edinmek için bkz. [Azure uygulamaları Için olağanüstü durum kurtarma ve yüksek kullanılabilirlik](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Bu makalede, bir bütün bölge ana doğal olağanüstü durum veya geniş çaplı hizmet kesintisi nedeniyle bir kesinti yaşandığında, doğru bir olağanüstü durum kurtarma senaryosu ele alınmaktadır. Bunlar nadir oluşumlardır, ancak bir bölgenin tamamı için bir kesinti olması olasılığa hazırlanmanız gerekir. Bir bölgenin tamamı bir hizmet kesintisi yaşıyorsa, verilerinizin yerel olarak yedekli kopyaları geçici olarak devre dışı olur. Coğrafi çoğaltmayı etkinleştirdiyseniz, Azure Storage bloblarınızın ve tablolarının üç ek kopyası farklı bir bölgede depolanır. Tam bir bölgesel kesinti veya birincil bölgenin kurtarılabilir olmadığı bir olağanüstü durum durumunda Azure, tüm DNS girdilerini coğrafi olarak çoğaltılan bölgeye yeniden eşler.

Bu nadir oluşumları işleyebilmeniz için, Azure sanal makine uygulamanızın dağıtıldığı bölgenin tamamına yönelik bir hizmet kesintisi olması durumunda Azure sanal makineleri için aşağıdaki kılavuza sağlıyoruz.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Seçenek 1: Azure Site Recovery kullanarak yük devretme başlatma
Uygulamanızı birkaç dakika içinde tek bir tıklama ile kurtarabilmeniz için sanal makinelerinize yönelik Azure Site Recovery yapılandırabilirsiniz. Tercih ettiğiniz Azure bölgesine çoğaltabilir ve eşleştirilmiş bölgelerle sınırlı değildir. [Sanal makinelerinizi çoğaltarak](https://aka.ms/a2a-getting-started)çalışmaya başlamanızı sağlayabilirsiniz. Uygulamanız için tüm yük devretme sürecini otomatikleştirebilmek üzere [bir kurtarma planı oluşturabilirsiniz](../site-recovery/site-recovery-create-recovery-plans.md) . Üretim uygulamasını veya devam eden Çoğaltmayı etkilemeden yük devretmeleri önceden [Test](../site-recovery/site-recovery-test-failover-to-azure.md) edebilirsiniz. Birincil bölge kesintisi durumunda, yalnızca [bir yük devretme başlatın](../site-recovery/site-recovery-failover.md) ve uygulamanızı hedef bölgeye getirin.


## <a name="option-2-wait-for-recovery"></a>Seçenek 2: kurtarma için bekle
Bu durumda, sizin bölüminizdeki hiçbir işlem yapmanız gerekmez. Hizmet kullanılabilirliğini geri yüklemek için çalıştık bir çalışmamız olduğunu öğrenin. Geçerli hizmet durumunu [Azure hizmet durumu panoımızda](https://azure.microsoft.com/status/)görebilirsiniz.

Bu, kesintiye uğramadan önce Azure Site Recovery, Okuma Erişimli Coğrafi olarak yedekli depolama veya coğrafi olarak yedekli depolama alanı ayarlamadıysanız en iyi seçenektir. VM sanal sabit sürücülerinizin (VHD) depolandığı depolama hesabı için coğrafi olarak yedekli depolama veya Okuma Erişimli Coğrafi olarak yedekli depolama ayarladıysanız, temel görüntü VHD 'sini kurtarmaya ve bundan sonra yeni bir VM sağlamaya çalışırsanız bakabilirsiniz. Verilerin eşitlenmesi garantisi olmadığından, bu tercih edilen bir seçenek değildir. Sonuç olarak, bu seçeneğin çalışması garanti edilmez.


> [!NOTE]
> Bu işlem üzerinde herhangi bir denetiminiz olmadığı ve yalnızca bölge genelinde hizmet kesintileri için gerçekleşmeyeceği hakkında dikkat edin. Bu nedenle, en yüksek kullanılabilirlik düzeyini elde etmek için uygulamaya özgü diğer yedekleme stratejilerine de güvenmelidir. Daha fazla bilgi için [olağanüstü durum kurtarma Için veri stratejileri](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)bölümüne bakın.
>
>

## <a name="next-steps"></a>Sonraki adımlar

- Azure Site Recovery kullanarak [Azure sanal makinelerinde çalışan uygulamalarınızı korumaya](https://aka.ms/a2a-getting-started) başlayın

- Olağanüstü durum kurtarma ve yüksek kullanılabilirlik stratejisi uygulama hakkında daha fazla bilgi edinmek için bkz. [Azure uygulamaları Için olağanüstü durum kurtarma ve yüksek kullanılabilirlik](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

- Bulut platformunun yeteneklerini ayrıntılı bir şekilde anlamak için bkz. [Azure dayanıklılığı teknik kılavuzu](/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).


- Yönergeler net değilse veya Microsoft 'un sizin adınıza işlem yapması istiyorsanız [müşteri desteği](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)'ne başvurun.
