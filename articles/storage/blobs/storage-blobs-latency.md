---
title: Blob depolamada gecikme-Azure depolama
description: BLOB depolama işlemleri için gecikme süresini anlayın ve ölçer ve düşük gecikme süresine sahip BLOB depolama uygulamalarınızı nasıl tasarlayacağınızı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: d4fae87d999bd0f6b0b388613098a17c181dae0c
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70392145"
---
# <a name="latency-in-blob-storage"></a>Blob depolamada gecikme süresi

Bazen yanıt süresi olarak başvurulan gecikme süresi, bir uygulamanın bir isteğin tamamlanmasını beklemesi gereken süredir. Gecikme süresi, bir uygulamanın performansını doğrudan etkileyebilir. Düşük gecikme süresi genellikle döngüdeki insanlardan (kredi kartı işlemleri veya Web sayfaları yükleme gibi) senaryolar için önemlidir. Telemetri günlüğü veya IoT olayları gibi yüksek hızlarda gelen olayları işlemesi gereken sistemler için de düşük gecikme süresi gerekir. Bu makalede, blok Blobları üzerinde işlemler için gecikme süresini ve ölçüyü nasıl anlayacağınızı ve uygulamalarınızın düşük gecikme süresine nasıl tasarlanacağını açıklanmaktadır.

Azure depolama, blok Blobları için iki farklı performans seçeneği sunar: Premium ve standart. Premium blok blob 'ları, yüksek performanslı SSD diskleri aracılığıyla standart blok Bloblarından önemli ölçüde daha düşük ve daha tutarlı gecikme sunar. Daha fazla bilgi için bkz. Azure Blob depolamada **Premium performans bloğu BLOB depolama** [: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](storage-blob-storage-tiers.md).

## <a name="about-azure-storage-latency"></a>Azure depolama gecikmesi hakkında

Azure depolama gecikmesi, Azure depolama işlemlerine yönelik istek ücretleri ile ilgilidir. İstek ücretleri, saniye başına giriş/çıkış işlemi (ıOPS) olarak da bilinir.

İstek hızını hesaplamak için, ilk olarak her bir isteğin tamamlanma süresini belirleyip, saniye başına kaç istek işleneceğini hesaplayın. Örneğin, bir isteğin tamamlanma 50 milisaniye (MS) aldığını varsayalım. Bir bekleyen okuma veya yazma işlemine sahip bir iş parçacığı kullanan bir uygulama, 20 ıOPS elde etmelidir (istek başına 1 saniye veya 1000 ms/50 MS). Teorik olarak, iş parçacığı sayısı iki katına çıkardıysanız, uygulama 40 ıOPS elde edebilmelidir. Her iş parçacığı için bekleyen zaman uyumsuz okuma veya yazma işlemleri iki katına çıkardıysanız, uygulama 80 ıOPS elde edebilmelidir.

Uygulamada, istek hızları her zaman daha erken ölçeklendirmez. Bu, istemcideki ek yükün görev zamanlamasıyla, bağlam geçişinden ve benzer şekilde ölçeklendirilmesine neden olur. Hizmet tarafında, Azure depolama sisteminde basınç, kullanılan depolama ortamındaki farklılıklar, diğer iş yüklerindeki gürültü, bakım görevleri ve diğer faktörlere bağlı olarak değişkenlik, gecikme süresi olabilir. Son olarak, istemci ile sunucu arasındaki ağ bağlantısı, sıkışıklık, yeniden yönlendirme veya diğer kesintiler nedeniyle Azure depolama gecikmesini etkileyebilir.

Aktarım hızı olarak da adlandırılan Azure depolama bant genişliği, istek oranı ile ilgilidir ve istek boyutu (ıOPS) istek boyutuyla çarpılarak hesaplanabilir. Örneğin, saniyede 160 istek varsayılarak, her 256 kıb veri, saniyede 40.960 KiB veya 40 MiB işleme ile sonuçlanır.

## <a name="latency-metrics-for-block-blobs"></a>Blok Blobları için gecikme ölçümleri

Azure depolama, blok Blobları için iki gecikme süresi sağlar. Bu ölçümler Azure portal görüntülenebilir:

- **Uçtan uca (e2e) gecikme süresi** , Azure depolama 'nın, yanıtın son paketinde bir istemci bildirimi aldığından isteğin ilk paketini aldığı zaman aralığını ölçer.

- **Sunucu gecikmesi** , Azure depolama 'nın, yanıtın Ilk paketi Azure Storage 'dan döndürülünceye kadar isteğin son paketini aldığı zaman aralığını ölçer.

Aşağıdaki görüntüde, `Get Blob` işlemi çağıran örnek bir iş yükü için **Ortalama başarılı e2e gecikme süresi** ve **ortalama başarı sunucusu gecikmesi** gösterilmektedir:

![Blob alma işleminin gecikme ölçümlerini gösteren ekran görüntüsü](media/storage-blobs-latency/latency-metrics-get-blob.png)

Normal koşullar altında, uçtan uca gecikme ve sunucu gecikmesi arasında çok az boşluk vardır. Bu, görüntünün örnek iş yükü için gösterdiği şeydir.

Uçtan uca ve sunucu gecikme ölçümlerinizi gözden geçirdikten sonra uçtan uca gecikme süresinin sunucu gecikmesinden önemli ölçüde daha yüksek olduğunu fark ederseniz, ek gecikme süresinin kaynağını araştırın ve ele geçirin.

Uçtan uca ve sunucu gecikme süresi benzerdir, ancak daha düşük gecikme süresine ihtiyacınız varsa Premium Blok Blob depolama alanına geçiş yapmayı düşünün.

## <a name="factors-influencing-latency"></a>Gecikme süresini etkileyen faktörler

Ana faktör etkileyen gecikme süresi işlem boyutudur. Ağ üzerinden aktarılan ve Azure depolama tarafından işlenen veri miktarı nedeniyle daha büyük işlemleri tamamlaması daha uzun sürer.

Aşağıdaki diyagramda, çeşitli boyutlardaki işlemler için toplam süre gösterilmektedir. Küçük miktarlarda veri için, gecikme aralığı, verileri aktarmak yerine, isteği işlemek için ağırlıklı. Gecikme aralığı, işlem boyutu arttıkça biraz artar (Aşağıdaki diyagramda 1 işaretlenir). İşlem boyutu arttıkça, veri aktarımı sırasında daha fazla zaman harcanarak toplam gecikme aralığı, istek işleme ve veri aktarımı arasında bölünür (Aşağıdaki diyagramda 2 işaretli). Daha büyük işlem boyutları ile, gecikme aralığı veri aktarımında neredeyse özellikle harcanmıştır ve istek işleme büyük ölçüde çok önemlidir (Aşağıdaki diyagramda 3 işaretlenir).

![İşlem boyutuna göre toplam işlem süresini gösteren ekran görüntüsü](media/storage-blobs-latency/operation-time-size-chart.png)

Eşzamanlılık ve iş parçacığı gibi istemci yapılandırma faktörleri de gecikme süresini etkiler. Genel aktarım hızı, belirli bir zamanda ve uygulamanızın iş parçacığı işleme nasıl işlediğini gösteren depolama isteklerinin kaç tane üzerinde olduğuna bağlıdır. CPU, bellek, yerel depolama ve ağ arabirimleri dahil istemci kaynakları da gecikme süresini etkileyebilir.

Azure depolama isteklerini işlemek için istemci CPU 'SU ve bellek kaynakları gerekir. İstemci, düşük güç kullanan bir sanal makine veya sistemdeki bazı ard bir işlem nedeniyle basınç altındaysa Azure Storage isteklerini işlemek için daha az kaynak bulunur. İstemci kaynaklarının herhangi bir çekişmesi veya olmaması, sunucu gecikmede bir artış olmadan uçtan uca gecikme süresi artarak, iki ölçüm arasındaki boşluğu arttırmaya neden olur.

Eşit oranda önemli olan ağ arabirimi ve istemci ile Azure depolama arasındaki ağ kanaldır. Tek başına fiziksel uzaklık önemli bir faktör olabilir. Örneğin, bir istemci VM 'si farklı bir Azure bölgedeyse veya şirket içinde olabilir. Ağ sıçramaları, ISS yönlendirme ve internet durumu gibi diğer faktörler, genel depolama gecikmesini etkileyebilir.

Gecikme süresini değerlendirmek için öncelikle senaryonuz için temel ölçümleri oluşturun. Temel ölçümler, iş yükü profilinize, uygulama yapılandırma ayarlarınıza, istemci kaynaklarına, ağ kanala ve diğer faktörlere bağlı olarak, uygulama ortamınız bağlamında beklenen uçtan uca ve sunucu gecikme süresi sağlar. Temel ölçümleriniz olduğunda, olağan dışı ve normal koşulları daha kolay bir şekilde tanımlayabilirsiniz. Temel ölçümler Ayrıca, uygulama yapılandırması veya VM boyutları gibi değiştirilen parametrelerin etkilerini gözlemlemeye olanak tanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Depolama hesapları için Azure depolama ölçeklenebilirlik ve performans hedefleri](../common/storage-scalability-targets.md)
- [Azure depolama performansı ve ölçeklenebilirlik denetim listesi](../common/storage-performance-checklist.md)
