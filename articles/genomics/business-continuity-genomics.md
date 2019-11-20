---
title: İş sürekliliği 'ne genel bakış
titleSuffix: Microsoft Genomics
description: Bu genel bakışta, Microsoft Genomiks 'nin iş sürekliliği ve olağanüstü durum kurtarma için sağladığı yetenekler açıklanır.
keywords: iş sürekliliği, olağanüstü durum kurtarma
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 28a4a53851155c56e8d34981862bf52a3a2cf15b
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249186"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Microsoft Genomiks ile iş sürekliliği 'ne genel bakış
Bu genel bakışta, Microsoft Genomiks 'nin iş sürekliliği ve olağanüstü durum kurtarma için sağladığı yetenekler açıklanır. Azure bölge kesintisi gibi, veri kaybına neden olabilecek, kesintiye uğratan olayları kurtarmaya yönelik seçenekler hakkında bilgi edinin. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>İş devamlılığını destekleyen Microsoft Genomiks özellikleri 
Nadir olarak bir Azure veri merkezi kesintiye neden olabilir. Bu, birkaç dakika içinde birkaç saate kadar geçen bir iş kesintiine neden olabilir. Bir kesinti oluştuğunda, şu anda veri merkezinde çalışan tüm işler başarısız olur ve Microsoft Genomiks hizmeti işleri bir ikincil bölgeye otomatik olarak yeniden göndermez. 

* Bir seçenek, veri merkezi kesintisi olduğunda veri merkezinin tekrar çevrimiçi gelmesini bekleyeyöneliktir. Kesinti kısaysa, Microsoft Genomiks hizmeti başarısız işleri otomatik olarak algılar ve iş akışı otomatik olarak yeniden başlatılır.

* Başka bir seçenek de iş akışını başka bir veri bölgesine teslim etmek olur. Microsoft Genomiks, örnekleri birkaç [Azure](https://azure.microsoft.com/regions/services/)bölgesinde dağıtır ve bölgeye özgü her örnek bağımsızdır. Microsoft Genomiks örneklerinden biri bölge yerel hatası yaşlarsa, Microsoft Genomiks örneklerini çalıştıran diğer bölgeler işleri işlemeye devam eder. Alternatif bir bölgeye aktarım, kullanıcının denetimi altında ve istediğiniz zaman kullanılabilir.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Microsoft Genomiks iş akışlarını başka bir bölgeye el ile yük devretme
Bölgesel bir veri merkezi kesintisi oluşursa, Microsoft Genomiks iş akışlarını, bireysel verilerinize ve iş sürekliliği gereksinimlerine bağlı olarak ikincil bir bölgeye göndermeyi tercih edebilirsiniz. Microsoft Genomiks iş akışlarının el ile yük devretmesi için, farklı bir bölgeye özel kullanırsınız. Genomiks hesabı ve işi uygun bölgeye özgü Genomikler ve depolama hesabı kimlik bilgileriyle birlikte gönder.

Özellikle şunları yapmanız gerekir:
* Azure portal kullanarak ikincil bölgede Genomiks hesabı oluşturun. 
* Giriş verilerinizi ikincil bölgedeki bir depolama hesabına geçirin ve ikincil bölgede bir çıkış klasörü ayarlayın.
* İş akışını ikincil bölgeye gönder.

Özgün bölge geri yüklendiğinde, Microsoft Genomiks hizmeti verileri ikincil bölgeden özgün bölgeye geri geçirmez. Giriş ve çıkış dosyalarını ikincil bölgeden özgün bölgeye geri taşımayı tercih edebilirsiniz.  Verilerini taşımayı tercih ederseniz bu, Genomiks hizmetinin dışındadır ve veri hareketiyle ilgili tüm ücretler sizin sorumluluğunuzdadır. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Bölgeye özgü olası kesinti için hazırlanma
Veri merkezi kesintisi durumunda daha hızlı kurtarma konusunda endişeleriniz varsa, Microsoft Genomiks iş akışlarınızı ikincil bir bölgeye el ile yeniden göndermeniz için gereken süreyi azaltmak üzere birkaç adım daha vardır:

* Uygun bir ikincil bölge ve Pro 'yu etkin bir şekilde bu bölgede bir Genomiks hesabı oluşturun
* Verilerinizin ikincil bölgede hemen kullanılabilmesini sağlamak için verilerinizi birincil ve ikincil bölgede çoğaltın. Bu, el ile veya Azure depolamada bulunan [coğrafi olarak yedekli depolama](https://docs.microsoft.com/azure/storage/common/storage-redundancy) özelliği kullanılarak yapılabilir. 

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Microsoft Genomiks hizmetini kullanırken iş sürekliliği ve olağanüstü durum kurtarma seçenekleriniz hakkında bilgi edindiniz. Azure 'da genel olarak iş sürekliliği ve olağanüstü durum kurtarma hakkında daha fazla bilgi için bkz [. Azure dayanıklılık Teknik Kılavuzu.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 