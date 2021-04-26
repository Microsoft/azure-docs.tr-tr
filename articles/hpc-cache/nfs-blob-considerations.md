---
title: Azure HPC Cache ile NFS BLOB depolama kullanma
description: Azure HPC Cache ile ADLS-NFS BLOB depolama kullanılırken yordamları ve sınırlamaları açıklar
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: d861a41d8cdeac548729ff341b3ffe27c0aa8152
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260011"
---
# <a name="use-nfs-mounted-blob-storage-preview-with-azure-hpc-cache"></a>Azure HPC Cache ile NFS bağlı BLOB depolama (ÖNIZLEME) kullanma

Mart 2021 ' den itibaren, Azure HPC Cache ile NFS bağlı blob kapsayıcıları kullanabilirsiniz. BLOB depolama belgeleri sitesindeki [Azure Blob depolamada NFS 3,0 protokol desteği](../storage/blobs/network-file-system-protocol-support.md) hakkında daha fazla bilgi edinin.

> [!NOTE]
> Azure Blob depolamada NFS 3,0 protokol desteği önizlemededir ve üretim ortamlarında kullanılmamalıdır. [NFS protokol desteği belgelerinde](../storage/blobs/network-file-system-protocol-support.md)güncelleştirmeleri ve diğer ayrıntıları denetleyin.

Azure HPC Cache, ADLS-NFS depolama hedef türünde NFS özellikli BLOB depolama alanını kullanır. Bu depolama hedefleri, normal NFS depolama hedeflerine benzerdir, ancak normal Azure Blob hedefleri ile de bazı örtüşmeye sahiptir.

Bu makalede, ADLS-NFS depolama hedeflerini kullanırken anlamanız gereken stratejiler ve sınırlamalar açıklanmaktadır.

NFS blob belgelerini, özellikle de uyumlu ve uyumsuz senaryoları tanımlayan bu bölümleri de okumalısınız:

* [Özelliklere genel bakış](../storage/blobs/network-file-system-protocol-support.md#applications-and-workloads-suited-for-this-feature)
* [Henüz desteklenmeyen özellikler](../storage/blobs/network-file-system-protocol-support.md#azure-storage-features-not-yet-supported)
* [Performansla ilgili önemli noktalar](../storage/blobs/network-file-system-protocol-support-performance.md)

## <a name="understand-consistency-requirements"></a>Tutarlılık gereksinimlerini anlama

HPC Cache, ADLS-NFS depolama hedefleri için güçlü tutarlılık gerektirir. Varsayılan olarak, NFS etkin blob depolaması dosya meta verilerini tamamen güncelleştirmez, bu da HPC önbelleğinin dosya sürümlerini doğru bir şekilde karşılaştırmasını önler.

Bu farka geçici bir çözüm için, Azure HPC Cache, depolama hedefi olarak kullanılan herhangi bir NFS etkin blob kapsayıcısında NFS öznitelik önbelleğini otomatik olarak devre dışı bırakır.

Bu ayar, önbellekten kaldırsanız bile kapsayıcının ömrü boyunca devam ettirir.

## <a name="preload-data-with-nfs-protocol"></a>NFS protokolü ile verileri önyükleme

NFS özellikli bir blob kapsayıcısında, *bir dosya yalnızca oluşturulduğu sırada kullanılan aynı protokol tarafından düzenlenebilir*. Diğer bir deyişle, bir kapsayıcıyı doldurmak için Azure REST API kullanıyorsanız, bu dosyaları güncelleştirmek için NFS kullanamazsınız. Azure HPC Cache yalnızca NFS kullandığından, Azure REST API ile oluşturulmuş herhangi bir dosyayı düzenleyemez.

Kapsayıcınız boşsa veya dosyalar NFS kullanılarak oluşturulduysa önbellek için bir sorun değildir.

Kapsayıcıdaki dosyalar NFS yerine Azure Blob 'un REST API oluşturulduysa, Azure HPC Cache özgün dosyalardaki bu eylemlerle kısıtlıdır:

* Dosyayı bir dizinde listeleyin.
* Dosyayı okuyun (ve sonraki okumalar için önbellekte tutun).
* Dosyayı silin.
* Dosyayı boşaltın (0 ' dan kesin).
* Dosyanın bir kopyasını kaydedin. Kopya, NFS tarafından oluşturulan bir dosya olarak işaretlenir ve NFS kullanılarak düzenlenebilir.

Azure **HPC Cache,** Rest kullanılarak oluşturulan bir dosyanın içeriğini düzenleyemez. Bu, değiştirilen bir dosyayı bir istemciden depolama hedefine geri kaydedemediği anlamına gelir.

NFS ile oluşturulmamış dosyalarda okuma/yazma önbelleği kullanım modelleri kullanıyorsanız, veri bütünlüğü sorunlarına neden olabileceğinden, bu sınırlamayı anlamak önemlidir.

> [!TIP]
> Okuma ve yazma önbelleğe alma hakkında daha fazla bilgi için bkz. [önbellek kullanım modellerini anlama](cache-usage-models.md).

### <a name="write-caching-scenarios"></a>Yazma önbelleği senaryoları

Bu önbellek kullanım modelleri yazma önbelleği içerir:

* **%15 yazma boyutundan büyük**
* **%15 ' ten fazla yazma, her 30 saniyede bir yedekleme sunucusunda değişiklikler denetleniyor**
* **%15 ' ten fazla yazma, her 60 saniyede bir yedekleme sunucusunu değişiklikler için denetleme**
* **%15 ' ten fazla yazma, her 30 saniyede bir sunucuya geri yazma**

Bu kullanım modellerini yalnızca NFS ile oluşturulan dosyaları düzenlemek için kullanın.

REST tarafından oluşturulan dosyalarda yazma önbelleği kullanmayı denerseniz, dosyanızın değişiklikleri kaybolabilir. Bunun nedeni, önbelleğin depolama kapsayıcısına hemen dosya düzenlemelerini kaydetmeye çalışmayın.

Burada, REST tarafından oluşturulan dosyalar için yazma işlemlerini önbelleğe alma girişimi, verileri riske koyar:

1. Önbellek, istemcilerden yapılan düzenlemeleri kabul eder ve her değişiklik üzerinde bir başarı iletisi döndürür.
1. Önbellek değiştirilen dosyayı deposunda tutar ve ek değişiklikler bekler.
1. Bir süre geçtikten sonra önbellek, değiştirilen dosyayı arka uç kapsayıcısına kaydetmeye çalışır. Bu noktada, NFS ile REST tarafından oluşturulan bir dosyaya yazmaya çalıştığı için bir hata iletisi alır.

   İstemci makinesine değişikliklerinin kabul edilmediğini ve önbelleğin orijinal dosyayı güncelleştirme yolu olmadığını söylemek için çok geç. Bu nedenle, istemcilerdeki değişiklikler kaybedilir.

### <a name="read-caching-scenarios"></a>Okuma önbelleği senaryoları

Okuma önbelleği senaryoları, NFS ya da Azure Blob REST API oluşturulan dosyalar için uygundur.

Bu kullanım modelleri yalnızca okuma önbelleği kullanır:

* **Yoğun, seyrek okunan yazma işlemleri**
* **İstemciler, önbelleği atlayarak NFS hedefine yazar**
* **Yoğun okuma, her 3 saatte bir yedekleme sunucusu denetleniyor**

Bu kullanım modellerini, REST API veya NFS tarafından oluşturulan dosyalarla birlikte kullanabilirsiniz. İstemciden arka uç kapsayıcısına gönderilen tüm NFS yazmaları yine de başarısız olur, ancak anında başarısız olur ve istemciye bir hata iletisi döndürür.

Okuma önbelleği iş akışı, önbelleğe alınmayan sürece dosya değişikliklerini de içerebilir. Örneğin, istemciler kapsayıcıdaki dosyalara erişebilir, ancak değişikliklerini yeni bir dosya olarak geri yazabilir veya değiştirilmiş dosyaları farklı bir konuma kaydedebilirler.

## <a name="recognize-network-lock-manager-nlm-limitations"></a>Ağ kilitleme Yöneticisi (NLM) sınırlamalarını tanıma

NFS özellikli blob kapsayıcıları, dosyaları çakışmaların korunmasını sağlayan, yaygın olarak kullanılan bir NFS protokolü olan ağ kilitleme Yöneticisi 'Ni (NLM) desteklemez.

NFS iş akışınız ilk olarak donanım depolama sistemleri için yazılmışsa, istemci uygulamalarınız NLM istekleri içerebilir. İşleminizi NFS özellikli blob depolamaya taşırken bu sınırlamaya geçici bir çözüm bulmak için, istemcileri, önbelleği bağlandıklarında NLM 'yi devre dışı bıraktığından emin olun.

NLM 'yi devre dışı bırakmak için ``-o nolock`` istemcileriniz ' komutundaki seçeneğini kullanın ``mount`` . Bu seçenek, istemcilerin, yanıt olarak NLM kilitleri ve hata alma isteği almasını engeller.

Birkaç durumda, Azure HPC önbelleğinin kendisi istemciden NLM isteklerini bildirir. **Okuma ağır, seyrek kullanılan yazma** adlı önbellek kullanım modeli, kendi arka uç DEPOLAMASı adına NLM isteklerini bildiren bir yazar. Bu sistem, istemcideki hataları engeller, ancak aslında ADLS-NFS kapsayıcısında bir kilit oluşturmaz.

Bir ADLS-NFS depolama hedefinin kullanım modelini, **okuma ağır, sık olmayan yazmaları** olarak değiştirirseniz, seçeneğini kullanarak tüm istemcileri yeniden kesmeniz gerekir ``nolock`` .

NLM, HPC önbelleği ve kullanım modelleri hakkında daha fazla bilgi için bkz. [önbellek kullanım modellerini anlama](cache-usage-models.md#know-when-to-remount-clients-for-nlm).

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>HPC Cache ile NFS özellikli kapsayıcılara yazma işlemlerini kolaylaştırma

Azure HPC önbelleği, bir ADLS-NFS depolama hedefinde değişiklik yazmayı içeren bir iş yükünde performansı artırmaya yardımcı olabilir.

> [!NOTE]
> Azure HPC Cache aracılığıyla dosyalarını değiştirmek istiyorsanız, ADLS-NFS depolama kapsayıcınızı doldurmak için NFS kullanmanız gerekir.

NFS etkin blob [performans konuları makalesinde](../storage/blobs/network-file-system-protocol-support-performance.md) özetlenen kısıtlamalardan bırı, ADLS-NFS depolaması 'nın var olan dosyaların üzerine yazılması için çok etkili olmaması şeklindedir. Azure HPC Cache 'i NFS bağlı BLOB depolama ile kullanırsanız, istemciler etkin bir dosyayı değiştirirken zaman aralıklı yeniden yazar gerçekleştirir. Arka uç kapsayıcısına bir dosya yazmanın gecikmesi istemcilerden gizlenir.

Yukarıda açıklanan sınırlamaları, [NFS protokolüyle verileri önyükleme](#preload-data-with-nfs-protocol)bölümünde aklınızda bulundurun.

## <a name="next-steps"></a>Sonraki adımlar

* [ADLS-NFS depolama hedefi önkoşullarını](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview) öğrenin
* [NFS etkin BLOB depolama hesabı](../storage/blobs/network-file-system-protocol-support-how-to.md) oluşturma
