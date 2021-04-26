---
title: Yüksek kullanılabilirlik ve olağanüstü durum kurtarma
titleSuffix: Azure Digital Twins
description: Olağanüstü durum kurtarma özellikleriyle yüksek düzeyde kullanılabilir Azure IoT çözümleri oluşturmanıza yardımcı olan Azure ve Azure dijital TWINS özelliklerini açıklar.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 41edef58910fe2b772831ef083e5aca8bb52a321
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482277"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure dijital TWINS yüksek kullanılabilirlik ve olağanüstü durum kurtarma

Dayanıklı IoT çözümleri için önemli bir alan olan iş sürekliliği ve olağanüstü durum kurtarma. **Yüksek kullanılabilirlik (ha)** ve **olağanüstü durum kurtarma (Dr)** için tasarlama, çözümünüz için uygun çalışma süresi hedeflerini tanımlamanıza ve elde etmenize yardımcı olabilir.

Bu makalede, Azure Digital TWINS hizmeti tarafından özellikle sunulan HA ve DR özellikleri ele alınmaktadır.

Azure dijital TWINS bu özellik seçeneklerini destekler:
* *Bölge ıçı ha* – hizmetin çalışma süresini sunmaya yönelik yerleşik yedeklilik
* *Çapraz bölge Dr* – beklenmedik bir veri merkezi hatası durumunda coğrafi eşlenmiş bir Azure bölgesine yük devretme

HA/DR için tasarlama hakkında genel Azure Kılavuzu için [*en iyi uygulamalar*](#best-practices) bölümünü de görebilirsiniz.

## <a name="intra-region-ha"></a>Bölge içi HA
 
Azure dijital TWINS, hizmet içinde artıklıkları uygulayarak bölge içi HA sağlar. Bu, çalışma süresi için [SERVICE SLA](https://azure.microsoft.com/support/legal/sla/digital-twins) 'da yansıtılır. **Bu HA özelliklerinden faydalanmak için bir Azure dijital TWINS çözümünün geliştiricilerine ek bir iş gerekmez.** Azure dijital TWINS, makul bir çalışma süresi garantisi sunmakla birlikte, herhangi bir dağıtılmış bilgi işlem platformunda olduğu gibi geçici sorunlar da beklenmeye devam edebilir. Uygun yeniden deneme ilkeleri, geçici hatalarla başa çıkmak için bir bulut uygulamasıyla etkileşime geçen bileşenlere yerleşik olmalıdır.

## <a name="cross-region-dr"></a>Çapraz bölge DR

Veri Merkezi, bölgedeki güç arızalarına veya diğer olaylara karşı genişletilmiş kesintiler yaşadığında bazı nadir durumlar olabilir. Bu tür olaylar nadir ve bu tür hatalarda, yukarıda açıklanan bölge içi HA özelliği de yardımcı olmayabilir. Azure dijital TWINS bunu Microsoft tarafından başlatılan yük devretme ile gidermektedir.

**Microsoft tarafından başlatılan yük devretme** , Microsoft 'un, etkilenen bir bölgeden gelen tüm Azure dijital TWINS örneklerinin, ilgili coğrafi eşleştirilmiş bölgeye yük devretmesine olanak sağlar. Bu işlem, varsayılan bir seçenektir (kullanıcıların herhangi bir şekilde kabul etmesidir) ve kullanıcıdan müdahale gerektirmez. Microsoft bu seçeneğin ne zaman belirlendiğini belirleme hakkını saklı tutar. Bu mekanizma, kullanıcının örneğinin yük devretme öncesinde Kullanıcı onayı içermez.

>[!NOTE]
> Bazı Azure Hizmetleri, **müşteri tarafından başlatılan yük devretme** adlı ek bir seçenek de sağlar. Bu, müşterilerin yalnızca kendi örnekleri için bir yük devretme başlatmasına olanak sağlar. ÖRNEĞIN, Dr detaylarını çalıştırmak gibi. Bu mekanizma Şu anda Azure dijital TWINS tarafından **desteklenmiyor** . 

## <a name="monitor-service-health"></a>Hizmet durumunu izleme

Azure dijital TWINS örnekleri yük devretdiği ve kurtarıldığı için, [Azure hizmet durumu](../service-health/service-health-overview.md) aracını kullanarak işlemi izleyebilirsiniz. Hizmet durumu, farklı bölgelerde ve aboneliklerde Azure hizmetlerinizin sistem durumunu izler ve kesintiler ve kaymalar hakkında hizmet tarafından etkileyen iletişimleri paylaşır.

Bir yük devretme olayı sırasında hizmet durumu, hizmetinizin ne zaman ve ne zaman yedekleneceği hakkında bir gösterge sağlayabilir.

Hizmet durumu olaylarını görüntülemek için...
1. Azure portal [hizmet durumu](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) ' na gidin (Bu bağlantıyı kullanabilir veya Portal arama çubuğunu kullanarak arama yapabilirsiniz).
1. *Sistem durumu geçmişi* sayfasına geçmek için sol menüyü kullanın.
1. **Azure dijital TWINS** ile başlayan bir *sorun adı* olup olmadığına bakın ve seçin.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="Sistem durumu geçmişi sayfasını gösteren Azure portal ekran görüntüsü. Son birkaç gündeki birkaç sorun listesi bulunur ve ' Azure Digital TWINS-Batı Avrupa-azaltıldı ' adlı bir sorun vurgulanır." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. Kesinti hakkında genel bilgi için *Özet* sekmesini görüntüleyin.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="Durum geçmişi sayfasında, Özet sekmesi vurgulanır. Sekme, etkilenen kaynak, bölgesi ve aboneliği gibi genel bilgileri görüntüler." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. Zamana göre sorun hakkında daha fazla bilgi ve güncelleştirme için, *sorun güncelleştirmeleri* sekmesini görüntüleyin.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="Durum geçmişi sayfasında, sorun güncelleştirmeleri sekmesi vurgulanır. Bu sekmede, geçerli durumu bir günden önce gösteren çeşitli girişler görüntülenir." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


Bu araçta görüntülenen bilgilerin bir Azure dijital örneğine özgü olmadığını unutmayın. Belirli bir bölgede veya abonelikte Azure dijital TWINS hizmeti ile neler olduğunu anlamak için hizmet durumunu kullandıktan sonra, belirli örneklerde ayrıntıya gitmek ve etkilenip etkilenmediğini görmek için [kaynak sistem durumu aracını](troubleshoot-resource-health.md) kullanarak bir adım daha fazla işlem yapabilirsiniz.

## <a name="best-practices"></a>En iyi uygulamalar

HA/DR üzerinde en iyi yöntemler için, bu konuda aşağıdaki Azure yönergelerine bakın: 
* [*Azure Iş sürekliliği teknik kılavuzu*](/azure/architecture/framework/resiliency/overview) makalesi, iş sürekliliği ve olağanüstü durum kurtarma hakkında düşünmenize yardımcı olacak genel bir çatı açıklamaktadır. 
* [*Azure uygulamaları Için olağanüstü durum kurtarma ve yüksek kullanılabilirlik*](/azure/architecture/framework/resiliency/backup-and-recovery) sayfası, yüksek KULLANıLABILIRLIK (ha) ve olağanüstü durum kurtarma (Dr) sağlamak için Azure uygulamalarına yönelik stratejiler hakkında mimari yönergeler sağlar.

## <a name="next-steps"></a>Sonraki adımlar 

Azure dijital TWINS çözümlerini kullanmaya başlama hakkında daha fazla bilgi edinin:
 
* [*Azure Digital Twins nedir?*](overview.md)
* [*Hızlı başlangıç: örnek bir senaryoyu araştırma*](quickstart-azure-digital-twins-explorer.md)