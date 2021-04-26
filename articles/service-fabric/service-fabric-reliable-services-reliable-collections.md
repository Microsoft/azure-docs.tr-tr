---
title: Güvenilir koleksiyonlara giriş
description: Durum bilgisi olan Service Fabric hizmetler, yüksek oranda kullanılabilir, ölçeklenebilir ve düşük gecikmeli bulut uygulamaları yazmanıza olanak tanıyan güvenilir koleksiyonlar sağlar.
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 7d705f81b4ad31559886e43226febcd4cf1d345d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784385"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Azure Service Fabric durum bilgisi olan hizmetlerde güvenilir koleksiyonlara giriş

Güvenilir koleksiyonlar, tek bir bilgisayar uygulaması yazıyor olsanız da yüksek oranda kullanılabilir, ölçeklenebilir ve düşük gecikmeli bulut uygulamaları yazmanızı sağlar. **Microsoft. ServiceFabric. Data. Collections** ad alanındaki sınıflar, durumu yüksek oranda kullanılabilir hale getirmek için otomatik olarak bir koleksiyon kümesi sağlar. Geliştiricilerin yalnızca güvenilir koleksiyon API 'Leri ile programlama yapması ve güvenilir koleksiyonların çoğaltılan ve yerel durumu yönetmesine izin vermek gerekir.

Güvenilir koleksiyonlar ve diğer yüksek kullanılabilirlik teknolojileri (Redsıs, Azure Tablo hizmeti ve Azure Kuyruk hizmeti gibi) arasındaki önemli fark, durumun hizmet örneğinde yerel olarak tutulması ve yüksek oranda kullanılabilir yapılmalarıdır. Bunun anlamı:

* Tüm okumalar yereldir, bu da düşük gecikme süresi ve yüksek aktarım hızı okumalarına neden olur.
* Tüm yazma işlemleri, düşük gecikme süresi ve yüksek aktarım hızı yazmaları ile sonuçlanan en az ağ IOs sayısına tabi olur.

![Koleksiyonların gelişini görüntüsü.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Güvenilir koleksiyonlar, **sistem. Collections** sınıflarının doğal evrimi olarak düşünülebilir: geliştirici için karmaşıklığı arttırmadan bulut ve çoklu bilgisayar uygulamaları için tasarlanan yeni bir koleksiyonlar kümesi. Bu nedenle, güvenilir koleksiyonlar şunlardır:

* Çoğaltılan: durum değişiklikleri yüksek kullanılabilirlik için çoğaltılır.
* Zaman uyumsuz: GÇ 'Leri yüklerken iş parçacıklarının engellenmediğinden emin olmak için API 'Ler zaman uyumsuzdur.
* İşlem: API 'Ler, bir hizmette birden çok güvenilir koleksiyonu kolayca yönetebilmeniz için işlem soyutlamasını kullanır.
* Kalıcı veya geçici: veriler, büyük ölçekli kesintilere karşı dayanıklılık için diske (örneğin, bir veri merkezi güç kesintisi) kalıcı olarak devam edebilir. Bazı güvenilir Koleksiyonlar ayrıca, çoğaltılan bellek içi önbellek gibi tüm verilerin bellekte tutulduğu geçici modu ( [Uyarılar](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)ile) destekler.

Güvenilir koleksiyonlar, uygulama durumunu daha kolay hale getirmek için önemli tutarlılık sağlar.
Kesin tutarlılık, işlem yürütmelerinin yalnızca, birincil dahil olmak üzere bir çok çoğaltmalarda tüm işlem günlüğe kaydedildikten sonra bitmesini sağlamak tarafından gerçekleştirilir.
Daha zayıf tutarlılık elde etmek için, uygulamalar zaman uyumsuz tamamlama döndürülmesinden önce istemci/istek sahibine geri bildirim alabilir.

Güvenilir koleksiyonlar API 'Leri, eşzamanlı koleksiyonlar API 'lerinin bir gelişmidir ( **System. Collections. eşzamanlı** ad alanında bulunur):

* Zaman uyumsuz: eşzamanlı koleksiyonların aksine, işlemler çoğaltılmakta ve kalıcı hale getirilir.
* Out parametresi yok: `ConditionalValue<T>` `bool` out parametreleri yerine bir ve değeri döndürmek için kullanılır. `ConditionalValue<T>` gibidir, `Nullable<T>` ancak T 'nin bir struct olması gerekmez.
* İşlemler: kullanıcının eylemleri bir işlemde birden çok güvenilir koleksiyonda grupkullanmasını sağlamak için bir işlem nesnesi kullanır.

Günümüzde **Microsoft. ServiceFabric. Data. Collections** üç koleksiyon vardır:

* [Güvenilir sözlük](/dotnet/api/microsoft.servicefabric.data.collections.ireliabledictionary-2#microsoft_servicefabric_data_collections_ireliabledictionary_2): anahtar/değer çiftlerinin çoğaltılan, işlemsel ve zaman uyumsuz koleksiyonunu temsil eder. **ConcurrentDictionary** ile benzer şekilde, hem anahtar hem de değer herhangi bir türde olabilir.
* [Güvenilir sıra](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1): çoğaltılan, işlemsel ve zaman uyumsuz katı, ilk çıkar (FIFO) kuyruğunu temsil eder. **ConcurrentQueue** ile benzer şekilde, değer herhangi bir türde olabilir.
* [Güvenilir eşzamanlı kuyruk](service-fabric-reliable-services-reliable-concurrent-queue.md): yüksek aktarım hızı için çoğaltılan, işlemsel ve zaman uyumsuz en iyi çaba sırası kuyruğunu temsil eder. **ConcurrentQueue** ile benzer şekilde, değer herhangi bir türde olabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Güvenilir koleksiyon yönergeleri & önerileri](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Güvenilir Koleksiyonlar ile çalışma](service-fabric-work-with-reliable-collections.md)
* [İşlemler ve kilitler](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Verileri yönetme
  * [Yedekleme ve geri yükleme](service-fabric-reliable-services-backup-restore.md)
  * [Bildirimler](service-fabric-reliable-services-notifications.md)
  * [Güvenilir Koleksiyonu serileştirme](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serileştirme ve yükseltme](service-fabric-application-upgrade-data-serialization.md)
  * [Güvenilir durum Yöneticisi yapılandırması](service-fabric-reliable-services-configuration.md)
* Diğer
  * [Hızlı başlangıç Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Güvenilir koleksiyonlar için geliştirici başvurusu](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
