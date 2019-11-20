---
title: Azure Advisor 'ı kullanarak hizmet maliyetlerini azaltma | Microsoft Docs
description: Azure dağıtımlarınızın maliyetini iyileştirmek için Azure Advisor 'ı kullanın.
services: advisor
documentationcenter: NA
author: saket-ms
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: sagupt
ms.openlocfilehash: cae16430ce933b8d071696377b22f10c15b5eae5
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145396"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Azure Advisor 'ı kullanarak hizmet maliyetlerini azaltma

Danışman, boşta ve az kullanılan kaynakları tanımlayarak Genel Azure harcamalarınızı iyileştirmenize ve azaltmanıza yardımcı olur. Danışman panosundaki **Maliyet** sekmesinden maliyet önerileri alabilirsiniz.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Az kullanılan örnekleri yeniden boyutlandırarak veya kapatarak sanal makineyi harcamayı iyileştirin 

Bazı uygulama senaryoları tasarım tarafından düşük kullanıma yol açabilir, ancak sanal makinelerinizin boyutunu ve sayısını yöneterek paradan tasarruf edebilirsiniz. Danışman gelişmiş değerlendirme modelleri, en yüksek CPU kullanımı değeri olan P95th ' in en fazla %3 ' ten az olduğu ve ağ kullanımı 7 günlük bir dönemde %2 ' den az olduğunda sanal makineleri kapatma için kabul eder. Sanal makineler, geçerli yükün daha küçük bir SKU 'da (aynı SKU ailesi içinde) sığması mümkün olduğunda doğru boyut olarak kabul edilir veya geçerli yükün Kullanıcı ile ilgili olmayan iş yükleri üzerinde %80 ' un üzerinde olmadığı ve Kullanıcı tarafından kullanıma sunulmadığı zaman %40 ' nin üzerinde olmadığı bir daha küçük sayı olarak değerlendirilir. Burada iş yükünün türü, iş yükünün CPU kullanım özelliklerinin analiz edilmesine göre belirlenir.

Önerilen Eylemler, için önerilen kaynağa özgü olarak kapatılır veya yeniden boyutlandırılır. Danışman, önerilen eylemler için tahmini maliyet tasarrufu gösterir-yeniden boyutlandır veya kapat. Ayrıca, önerilen eylemi yeniden boyutlandır için, Advisor geçerli ve hedef SKU bilgilerini sağlar. 

Aşırı kullanılan sanal makineleri tanımlamaya daha Agresif olmak istiyorsanız, CPU kullanım kuralını abonelik başına temelinde ayarlayabilirsiniz.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Sağlaması kaldırılan ExpressRoute devreleri ortadan kaldırarak maliyetleri düşürün

Danışman, sağlayıcı durumunda bir aydan daha fazla sağlanmamış olan ExpressRoute devreleri tanımlar ve bağlantı sağlayıcınızla devreyi sağlamayı *planlamadıysanız* devreyi silmeyi önerir.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Boştaki Sanal ağ geçitlerini silerek veya yeniden yapılandırarak maliyetleri azaltın

Advisor 90 gün boyunca boşta olan sanal ağ geçitlerini tanımlar. Bu ağ geçitleri saatlik olarak faturalandırıladıklarından, bunları artık kullanmayı düşünmüyorsanız onları yeniden yapılandırmanızı veya silmeyi düşünmelisiniz. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Kullandıkça Öde maliyetlerinden tasarruf etmek için ayrılmış sanal makine örnekleri satın alın

Danışman, son 30 gün içinde sanal makine kullanımınızı inceleyerek bir Azure ayırması satın alarak para tasarrufu sağlayabilmeniz gerektiğini belirlemektir. Danışman size en fazla tasarruf ettiğiniz bölgeleri ve boyutları gösterir ve satın alma rezervasyonlarından Tahmini tasarruf sağlar. Azure ayırmaları sayesinde, sanal makinelerinizin temel maliyetlerini önceden satın alabilirsiniz. İndirimler, ayırmalarıyla aynı boyuta ve bölgeye sahip yeni veya mevcut VM 'lere otomatik olarak uygulanacaktır. [Azure ayrılmış VM örnekleri hakkında daha fazla bilgi edinin.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Danışman Ayrıca, sonraki 30 gün içinde sona erecektir olan ayrılmış örnekleri size bildirir. Kullandıkça Öde fiyatlandırmasını önlemeyi önlemek için yeni ayrılmış örnekler satın almanızı öneririz.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Paradan tasarruf etmek için ilişkilendirilmemiş genel IP adreslerini silin

Danışman, şu anda yük dengeleyiciler veya VM 'Ler gibi Azure kaynaklarıyla ilişkili olmayan genel IP adreslerini tanımlar. Bu genel IP adresleri nominal bir ücret ile gelir. Bunları kullanmayı planlamıyorsanız, bunların silinmesi maliyet tasarruflarına yol açabilir.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Başarısız olan Azure Data Factory işlem hatlarını silme

Azure Advisor, tekrar tekrar başarısız olan Azure Data Factory işlem hatlarını algılar ve artık gerekli değilse sorunları çözmenize veya başarısız olan işlem hatlarını silmeye önerilir. Bu işlem hatları için, başarısız olduklarında hizmet vermese bile faturalandırılırsınız. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Yönetilen diskler için standart anlık görüntüleri kullanma
Maliyetin %60 ' i kaydetmek için, üst diskin depolama türünden bağımsız olarak anlık görüntülerinizi standart depolamada depolamanızı öneririz. Bu, yönetilen diskler anlık görüntüleri için varsayılan seçenektir. Azure Danışmanı, Premium Depolama depolanan anlık görüntüleri belirler ve anlık görüntü ortamınızı Premium 'dan standart depolamaya geçirmeyi öneririz. [Yönetilen disk fiyatlandırması hakkında daha fazla bilgi edinin](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Azure Danışmanı 'nda maliyet önerilerine erişme

1. [Azure Portal](https://portal.azure.com)oturum açın ve [danışman](https://aka.ms/azureadvisordashboard)' yi açın.

2.  Danışman panosunda **Maliyet** sekmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Danışman önerileri hakkında daha fazla bilgi edinmek için bkz.:
* [Advisor 'a giriş](advisor-overview.md)
* [Kullanmaya Başlayın](advisor-get-started.md)
* [Advisor performans önerileri](advisor-cost-recommendations.md)
* [Danışman yüksek kullanılabilirlik önerileri](advisor-cost-recommendations.md)
* [Danışman güvenlik önerileri](advisor-cost-recommendations.md)
* [Danışman Işletimsel üstün öneriler](advisor-operational-excellence-recommendations.md)
