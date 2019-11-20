---
title: Azure Storage 'da çapraz bölgesel dayanıklılık için coğrafi olarak yedekli depolama (GRS) | Microsoft Docs
description: Coğrafi olarak yedekli depolama (GRS), verilerinizi yüzlerce mil olan iki bölge arasında çoğaltır. GRS, veri merkezindeki donanım arızalarına ve bölgesel felaketlere karşı koruma sağlar.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 16a5f214495025d16d10ee01a7b2a40b78f7a17a
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670797"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Coğrafi olarak yedekli depolama (GRS): Azure depolama için çapraz bölgesel çoğaltma
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Okuma Erişimli Coğrafi olarak yedekli depolama
Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS), depolama hesabınız için kullanılabilirliği en üst düzeye çıkarır. RA-GRS, iki bölge arasında coğrafi çoğaltmanın yanı sıra ikincil konumdaki verilere salt okuma erişimi sağlar.

İkincil bölgedeki verilerinize salt okuma erişimi etkinleştirdiğinizde, verileriniz ikincil bir uç noktada ve depolama hesabınızın birincil uç noktasında bulunur. İkincil uç nokta birincil uç noktaya benzerdir, ancak `–secondary` sonekini hesap adına ekler. Örneğin, blob hizmeti için birincil uç noktanız `myaccount.blob.core.windows.net` ise, ikincil uç noktanız `myaccount-secondary.blob.core.windows.net` ' dir. Depolama hesabınızın erişim anahtarları hem birincil hem de ikincil uç noktalar için aynıdır.

RA-GRS kullanırken göz önünde bulundurmanız gereken bazı noktalar:

* Uygulamanız, RA-GRS kullanırken hangi uç noktanın etkileşimde olduğunu yönetmiştir.
* Zaman uyumsuz çoğaltma bir gecikme içerdiğinden, veriler birincil bölgeden kurtarılamazsa ikincil bölgeye henüz çoğaltılmamış olan değişiklikler kaybolabilir.
* Depolama hesabınızın son eşitleme zamanını kontrol edebilirsiniz. Son eşitleme zamanı, GMT Tarih/saat değeridir. Son eşitleme zamanından önceki tüm birincil yazmaları ikincil konuma başarıyla yazıldı, yani ikincil konumdan okunmak üzere kullanılabilir. Son eşitleme zamanından sonraki birincil yazma işlemleri henüz okuma için kullanılamayabilir veya kullanılamıyor olabilir. [Azure Portal](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md)veya Azure Storage istemci kitaplıklarından birini kullanarak bu değeri sorgulayabilirsiniz.
* Bir GRS veya RA-GRS hesabının bir hesap yük devretmesini (Önizleme) ikinci bölgeye başlatırsanız, yük devretme tamamlandıktan sonra bu hesaba yazma erişimi geri yüklenir. Daha fazla bilgi için bkz. [olağanüstü durum kurtarma ve depolama hesabı yük devretme (Önizleme)](storage-disaster-recovery-guidance.md).
* RA-GRS, yüksek kullanılabilirlik amaçlarıyla tasarlanmıştır. Ölçeklenebilirlik Kılavuzu için [performans denetim listesini](storage-performance-checklist.md)gözden geçirin.
* RA-GRS ile yüksek kullanılabilirlik için tasarlanmasıyla ilgili öneriler için, bkz. [RA-GRS depolama kullanarak yüksek oranda kullanılabilir uygulamalar tasarlama](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>RPO ve RTO GRS nedir?

**Kurtarma noktası hedefi (RPO):** GRS ve RA-GRS 'de, depolama hizmeti verileri zaman uyumsuz olarak coğrafi olarak çoğaltarak ikincil konuma çoğaltır. Birincil bölgenin kullanılamaz hale geldiği olayda, ikincil bölgeye hesap yük devretmesi (Önizleme) gerçekleştirebilirsiniz. Bir yük devretme başlattığınızda, henüz coğrafi olarak çoğaltılan son değişiklikler kaybolabilir. Kaybolan olası verilerin dakika sayısı RPO olarak bilinir. RPO, verilerin kurtarılabileceği noktayı gösterir. Azure Storage genellikle 15 dakikadan daha kısa bir RPO 'ya sahiptir, ancak şu anda coğrafi çoğaltmanın ne kadar süre sürdüğüne ilişkin bir SLA yoktur.

**Kurtarma süresi hedefi (RTO):** RTO, yük devretmeyi gerçekleştirmeye ne kadar süreceğine ve depolama hesabını yeniden çevrimiçine almaya yönelik bir ölçüdür. Yük devretmeyi gerçekleştirme zamanı aşağıdaki eylemleri içerir:

   * Müşteri, depolama hesabının Birincilden ikincil bölgeye yük devretmesini başlatana kadar geçen süre.
   * Birincil DNS girdilerini ikincil konuma işaret etmek üzere değiştirerek yük devretmeyi gerçekleştirmek için Azure tarafından gereken süre.

## <a name="paired-regions"></a>Eşleştirilmiş bölgeler 
Bir depolama hesabı oluşturduğunuzda, hesabın birincil bölgesini seçersiniz. Eşleştirilmiş ikincil bölge, birincil bölgeye göre belirlenir ve değiştirilemez. Azure tarafından desteklenen bölgeler hakkında güncel bilgiler için, bkz. [Iş sürekliliği ve olağanüstü durum kurtarma (BCDR): Azure eşlenmiş bölgeler](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Ayrıca bkz.
- [Azure Depolama çoğaltması](storage-redundancy.md)
- [Yerel olarak yedekli depolama (LRS): Azure depolama için düşük maliyetli veri artıklığı](storage-redundancy-lrs.md)
- [Bölgesel olarak yedekli depolama (ZRS): Yüksek oranda kullanılabilir Azure depolama uygulamaları](storage-redundancy-zrs.md)
