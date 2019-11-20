---
title: Azure HPC önbelleğini yönetme ve güncelleştirme
description: Azure portal kullanarak Azure HPC önbelleğini yönetme ve güncelleştirme
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: rohogue
ms.openlocfilehash: 9cd5ad151c977838fea30f52c7d4a93b4663c8ff
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166737"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Azure portal önbelleğinizi yönetin

Azure portal önbellekte Genel Bakış sayfasında, önbelleğiniz için proje ayrıntıları, önbellek durumu ve temel istatistikler gösterilmektedir. Ayrıca, önbelleği silmek, uzun süreli depolamaya verileri temizlemek veya yazılımı güncelleştirmek için denetimler de vardır.

Genel Bakış sayfasını açmak için Azure portal ön belleği kaynağını seçin. Örneğin, **tüm kaynaklar** sayfasını yükleyin ve önbellek adına tıklayın.

![Azure HPC önbellek örneğinin genel bakış sayfasının ekran görüntüsü](media/hpc-cache-overview.png)

Sayfanın üst kısmındaki düğmeler, önbelleği yönetmenize yardımcı olabilir:

* [**Flush**](#flush-cached-data) -tüm önbelleğe alınmış verileri depolama hedeflerine yazar
* [**Yükseltme**](#upgrade-cache-software) -önbellek yazılımını güncelleştirir
* **Yenile** -genel bakış sayfasını yeniden yükler
* [**Sil**](#delete-the-cache) -önbelleği kalıcı olarak yok eder

Aşağıdaki seçenekler hakkında daha fazla bilgi edinin.

## <a name="flush-cached-data"></a>Önbelleğe alınmış verileri temizleme

Genel Bakış sayfasındaki **Temizleme** düğmesi önbelleğin önbellekte depolanan tüm değiştirilen verileri arka uç depolama hedeflerine hemen yazmasını söyler. Önbellek verileri düzenli aralıklarla depolama hedeflerine kaydeder, bu nedenle arka uç depolama sisteminin güncel olduğundan emin olmak istemediğiniz takdirde bunu el ile yapmak gerekli değildir. Örneğin, bir depolama anlık görüntüsü almadan veya veri kümesi boyutunu kontrol etmeden önce **temizlemeyi** kullanabilirsiniz.

> [!NOTE]
> Temizleme işlemi sırasında önbellek, istemci isteklerine yönelik hizmeti veremez. Önbellek erişimi askıya alınır ve işlem bittikten sonra devam eder.

![Flush vurgulanmış olan üst düğmelerin ve Temizleme eylemini açıklayan ve ' devam etmek istiyor musunuz? ' adlı bir açılan iletinin ekran görüntüsü Evet (varsayılan) ve düğme yok](media/hpc-cache-flush.png)

Önbellek temizleme işlemini başlattığınızda, önbellek istemci isteklerini kabul etmeyi durduruyor ve Genel Bakış sayfasındaki önbellek durumu **reçeteye göre**değişir.

Önbellekteki veriler uygun depolama hedeflerine kaydedilir. Ne kadar veri temizlenmesi gerektiğine bağlı olarak, işlem birkaç dakika sürebilir veya bir saat boyunca zaman alabilir.

Tüm veriler depolama hedeflerine kaydedildikten sonra, önbellek otomatik olarak istemci istekleri almaya başlar. Önbellek durumu **sağlıklı**olarak döner.

## <a name="upgrade-cache-software"></a>Yükseltme önbelleği yazılımı

Yeni bir yazılım sürümü varsa, **Yükselt** düğmesi etkin hale gelir. Ayrıca, sayfanın en üstünde yazılım güncelleştirme hakkında bir ileti de görmeniz gerekir.

![Yükseltme düğmesi etkin olan düğmelerin en üstteki satır görüntüsü](media/hpc-cache-upgrade-button.png)

İstemci erişimi, yazılım yükseltmesi sırasında kesintiye uğramaz, ancak önbellek performansı yavaşlar. Yoğun olmayan kullanım saatlerinde veya planlanan bir bakım döneminde yazılım yükseltmeyi planlayın.

Yazılım güncelleştirmesi birkaç saat sürebilir. Daha yüksek aktarım hızı ile yapılandırılan önbellekler, daha az yoğun üretilen iş değeri olan önbellekler daha uzun sürer

Bir yazılım yükseltmesi kullanılabilir olduğunda, bir hafta veya el ile uygulamanız gerekir. Son Tarih yükseltme iletisinde listelenir. Bu süre boyunca yükseltmezseniz Azure, güncelleştirmeyi otomatik olarak önbelleğe uygular. Otomatik yükseltmenin zamanlaması yapılandırılamaz. Önbellek performansı etkisi konusunda endişeleriniz varsa, zaman döneminin süresi dolmadan önce yazılımı kendiniz yükseltmeniz gerekir.

Yazılım güncelleştirmesini başlatmak için **Yükselt** düğmesine tıklayın. İşlem tamamlanana kadar önbellek durumu **yükseltme** olarak değişir.

## <a name="delete-the-cache"></a>Önbelleği silme

**Sil** düğmesi önbelleği yok eder. Bir önbelleği sildiğinizde, tüm kaynakları yok edilir ve artık hesap ücretlerine tabi değildir.

Önbelleği sildiğinizde depolama hedefleri etkilenmez. Daha sonra gelecekteki bir önbelleğe ekleyebilir veya onları ayrı olarak yetkisini alabilirsiniz.

Önbellek, son kapatılma işleminin bir parçası olarak, kaydedilmemiş verileri otomatik olarak depolama hedeflerine temizler.

## <a name="cache-metrics-and-monitoring"></a>Önbellek ölçümleri ve izleme

Genel Bakış sayfasında bazı temel önbellek istatistikleri için grafikler, önbellek aktarım hızı, saniye başına işlem ve gecikme gösterilmektedir.

![Örnek önbellek için yukarıda bahsedilen istatistikleri gösteren üç satırlık grafiklerin ekran görüntüsü](media/hpc-cache-overview-stats.png)

Bu grafikler, Azure 'un yerleşik izleme ve analiz araçlarının bir parçasıdır. Ek araçlar ve uyarılar, Portal kenar çubuğu 'ndaki **izleme** başlığı altındaki sayfalardan kullanılabilir. [Azure izleme belgelerinin](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)Portal bölümünde daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

<!-- * Learn more about metrics and statistics for hpc cache -->
* [Azure ölçümleri ve istatistik araçları](../azure-monitor/index.yml) hakkında daha fazla bilgi edinin
* [Azure HPC önbelleğiniz ile ilgili yardım](hpc-cache-support-ticket.md) alın
