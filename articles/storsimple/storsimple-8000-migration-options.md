---
title: StorSimple 5000-7000 Serisi cihazlarından veri geçiş seçenekleri
description: StorSimple 5000-7000 serisinden veri geçirme seçeneklerine genel bakış sağlar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: cc67b678fabeb2a7878490a4ea713acb5709cdbc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643786"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>StorSimple 5000-7000 serisindeki verileri geçirme seçenekleri 

> [!IMPORTANT]
> 9 Temmuz 2019 ' de, StorSimple 5000/7000 Serisi destek (EOS) durumunun sonuna ulaşacaktır. StorSimple 5000/7000 Serisi müşterilerinin belgede açıklanan alternatifden birine geçiş yapmanızı öneririz.

StorSimple 5000-7000 Serisi, Haziran 2019 ' de [destek sonuna](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) ulaşıyor. StorSimple 5000-7000 serisini çalıştıran müşterilerin diğer Azure birinci taraf karma hizmetlerine yükseltme seçeneği vardır. Bu makalede, verileri geçirmek için kullanılabilen Azure hibrit seçenekleri açıklanmaktadır. 

## <a name="migration-options"></a>Geçiş seçenekleri

StorSimple 5000-7000 serisini kullanan müşterilerin Azure veya üçüncü taraf seçenekleri vardır.

### <a name="azure-options"></a>Azure seçenekleri

#### <a name="upgrade-to-storsimple-8000-series"></a>StorSimple 8000 serisine yükseltme

StorSimple 8000 serisine yükseltin ve bu nedenle StorSimple platformunda devam edin.  Bu yükseltme yolu, müşterilerin 5000-7000 Serisi cihazlarını bir 8000 serisi ile değiştirmesini gerektirir. Veriler, geçiş aracı kullanılarak 5000-7000 Serisi cihazdan geçirilir. Geçiş başarıyla tamamlandıktan sonra, StorSimple 8000 serisi cihazlar verileri Azure Blob depolama alanına taşımaya devam edecektir. 

StorSimple 8000 serisini kullanarak verileri geçirme hakkında daha fazla bilgi için, [storsimple 5000-7000 serisinden verileri 8000 serisi cihaza geçirme](storsimple-8000-migrate-from-5000-7000.md)bölümüne gidin.

#### <a name="migrate-to-azure-file-sync"></a>Azure Dosya Eşitleme’ye Geçiş

Bu yepyeni geçiş seçeneği, müşterilerin kuruluşunuzun dosya paylaşımlarını Azure dosyalarında depolamasına olanak sağlar. Bu dosya paylaşımları daha sonra Azure Dosya Eşitleme (AFS) kullanarak şirket içi erişim için merkezi hale getirilmiş. AFS, bir Windows Server konağında dağıtılabilir. Gerçek veri geçişi daha sonra bir konak kopyası olarak veya geçiş aracı kullanılarak gerçekleştirilir.

Azure Dosya Eşitleme verileri geçirme hakkında daha fazla bilgi için, [StorSimple 5000-7000 serisinden verileri Azure dosya eşitleme geçirme](../storage/files/storage-files-migration-storsimple-8000.md)bölümüne gidin.

### <a name="third-party-options"></a>Üçüncü taraf seçenekleri

#### <a name="migrate-to-panzura-freedom-nas"></a>Panzura özgürlük 'e geçiş

StorSimple 5000-7000 müşterileri, verilerini Azure 'da tutmak için Panzura özgürlüğü 'e geçiş yapmak için seçim yapabilir. Panzura özgürlük çözümü, veri merkezlerini, ofislerini, ortak ve özel bulutları kapsayan bir NAS çözümü sağlar. Bu çözüm, NFS, SMB ve mobil istemciler için yerel, karma ve bulut içi veri iş akışlarını mümkün bir şekilde sunar. 

Bu geçiş, Panzura tarafından desteklenir ve müşteriler [panzura Web sitesinden](https://panzura.com/storsimple-migration/)geçiş desteği isteyerek çalışmaya başlayabilir.

#### <a name="migrate-to-cohesity"></a>Cohesity 'e geçiş

Cohesity, geçerli StorSimple 5000 – 7000 ' ten verileri Azure 'daki Cohesity veri platformuna geçirmenize olanak sağlar. Cohesity veri platformu, dosyaları, yedeklemeleri, nesneleri ve VM 'Leri tek bir bulut Yerel çözümüne birleştiren yazılım tanımlı bir Web ölçeği çözümüdür. Veri platformuna geçişten sonra, tek bir cam bölmeden buluta veri ve uygulama yönetebilir, koruyabilir ve bunları çekirdek olarak sağlayabilirsiniz. Cohesity ile, üç düğüm kadar birkaç kez başlatın. 

[Cohesity veri platformuna geçiş](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html)hakkında daha fazla bilgi edinin.

#### <a name="migrate-to-nasuni"></a>Nasuni 'ya geçiş

Nasuni, StorSimple 5000-7000 müşterilerinin verileri Azure 'da geçirmesini ve tutmasını kolaylaştırır.  Nasuni, önde gelen Azure tabanlı bir NAS depolama çözümüdür ve müşterilere, şirket içi çözümlerden beklenen performans ve güvenlik, bulut ekonomiki ve ölçeklendirilmesine sahip olur.  Yüksek performanslı dosya depolama, Nasuni ve Azure 'ın yedekleme ve DR işleme yanı sıra, merkezi dosya depolama yönetimi sayesinde dünyanın dört bir yanındaki verilerinizi paylaşmanıza ve bunlarla işbirliği yapmanıza olanak sağlar. 

Nasuni sayesinde geçişinizi kolay bir şekilde kullanmaya başlayın: https://www.nasuni.com/blog-migrating-off-storsimple/

#### <a name="migrate-to-talon-fast"></a>HıZLı bir şekilde geçiş yapın

Talon, StorSimple 5000-7000 müşterilerinin çok daha büyük bir işlev ile StorSimple platformunda (sınırsız bulut kaynaklarıyla desteklenen küçük yerinde parmak izi) büyük bir avantajdan yararlanmaya devam etmesini kolaylaştırır.  HıZLı çözüm sayesinde, müşteriler Azure 'da verilerini geçirebilir ve koruyabilir, böylece artık daha küçük bir yazılıma sahip olan ve genel dosya kilitleme, genel ad alanı ve çok siteli işbirliği gibi avantajlar ekleyebilirsiniz.  Talon, önde gelen bir Azure ekosistem çözümüdür ve şirket içi dosya sunucusu iş yüklerini, Kullanıcı iş akışı veya deneyiminden ödün vermeden birleştirilmiş, Azure tabanlı bir ayak izine geçirmeye yönelik olarak çalışır.  

Buluta birleştirilmiş bir kuruluşa nasıl geliştireceğinizi öğrenin.

## <a name="migration---frequently-asked-questions"></a>Geçiş-sık sorulan sorular

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>S. StorSimple 5000 ve 7000 Serisi cihazlar hizmet sonuna ulaştığında ne zaman? 

A. StorSimple 5000-7000 serisi [hizmet sonu](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) 2019 ' de sona ulaşın. Hizmet sonu, Microsoft 'un artık 2019 Temmuz 'dan sonra bu cihazların donanımı ve yazılımları için destek sağlayamayacağı anlamına gelir. Şimdi cihazlarınızdan verileri geçirmek için bir planı formülle açmanızı öneririz.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>S. Azure 'da depoladığınız verilere ne olur?  

A. Verileri daha yeni bir hizmete geçirdikten sonra Azure 'da kullanmaya devam edebilirsiniz. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>S. StorSimple cihazım üzerinde yerel olarak depoladığınız verilere ne olur? 

A. Yerel cihazdaki veriler, geçiş belgelerinde açıklandığı gibi yeni hizmete kopyalanabilir.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>S. StorSimple 5000/7000 Serisi gerecimi korumak istersem ne olur? 

A. Hizmetler çalışmaya devam edebilirken, Microsoft artık donanım ve yazılım desteği sağlayamayacaktır. Geçiş, iş sürekliliği için önerilir.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>S. StorSimple 5000-7000 Serisi cihazlarından verileri geçirmek için hangi seçenekler kullanılabilir? 

A. StorSimple 5000-7000 Serisi kullanıcıları, senaryosuna bağlı olarak aşağıdaki geçiş seçeneklerine sahiptir. 

 - **8000 serisine Yükselt**: StorSimple platformunda devam etmek istediğinizde bu seçeneği kullanın. 
 - **Azure dosya eşitleme geçir**: Azure yerel biçimine geçmek istediğinizde bu seçeneği kullanın. Dosya paylaşımlarının merkezi yönetimi için Azure Dosya Eşitleme kullanabilirsiniz. 

Burada listelenmeyen geçiş seçeneklerini tartışmak için Microsoft Desteği ile iletişim sağlayabilirsiniz.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>S. Diğer depolama çözümlerine geçiş destekleniyor mu?

A. Evet. Verilerin ana bilgisayar kopyası kullanılarak diğer depolama çözümlerine geçiş desteklenir.

### <a name="q-is-migration-supported-by-microsoft"></a>S. Geçiş Microsoft tarafından destekleniyor mu? 

A. 5000 veya 7000 serisinden geçiş, tam olarak desteklenen bir işlemdir. Aslında, Microsoft, Geçişe başlamadan önce destek 'e ulaşmasını önerir. Geçiş Şu anda yardımlı bir işlemdir. StorSimple 5000-7000 Serisi cihazınızdan veri geçirmeyi planlıyorsanız [bir destek bileti açın](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>S. Geçiş seçeneklerinin her ikisi için de fiyatlandırma modeli nedir?

A. Geçiş maliyeti, belirlediğiniz seçeneğe bağlı olarak farklılık gösterir. Geçiş işlemi ücretsizdir, StorSimple 8000 serisine yükseltmeye karar verirseniz, donanım cihazının maliyeti olur. 

Benzer şekilde, Azure Dosya Eşitleme kullanırken hizmetin abonelik ücretleri de uygulanabilir. Her durumda, müşterilerin devam eden depolama maliyetlerini de ödeyecektir. Tahmin için aşağıdakilere bakın: 
- [StorSimple fiyatlandırması](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS fiyatlandırması]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>S.  Geçişin tamamlanması ne kadar sürer?

A. Verilerin geçirilmesi için geçen süre, veri miktarına ve yükseltme seçeneğinin seçili olmasına bağlıdır. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>S. StorSimple 8000 serisi için destek tarihinin sonu nedir?

A. StorSimple 8000 serisi için destek bitiş tarihi [burada](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)yayımlanır.


## <a name="next-steps"></a>Sonraki adımlar
 - [StorSimple 5000-7000 serisinden bir 8000 serisi cihaza veri geçirin](storsimple-8000-migrate-from-5000-7000.md).
 - [StorSimple 5000-7000 serilerinden verileri Azure Dosya Eşitleme 'e geçirme](../storage/files/storage-files-migration-storsimple-8000.md)
