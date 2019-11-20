---
title: Azure Service Fabric durum bilgisi olan hizmetlerde güvenilir koleksiyonlara giriş | Microsoft Docs
description: Durum bilgisi olan Service Fabric hizmetler, yüksek oranda kullanılabilir, ölçeklenebilir ve düşük gecikmeli bulut uygulamaları yazmanıza olanak tanıyan güvenilir koleksiyonlar sağlar.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 1/3/2019
ms.author: atsenthi
ms.openlocfilehash: a7b30003fd02f8ab2e367311cdb3f56c80dbb4b2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599272"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Azure Service Fabric durum bilgisi olan hizmetlerde güvenilir koleksiyonlara giriş

Güvenilir koleksiyonlar, tek bir bilgisayar uygulaması yazıyor olsanız da yüksek oranda kullanılabilir, ölçeklenebilir ve düşük gecikmeli bulut uygulamaları yazmanızı sağlar. **Microsoft. ServiceFabric. Data. Collections** ad alanındaki sınıflar, durumu yüksek oranda kullanılabilir hale getirmek için otomatik olarak bir koleksiyon kümesi sağlar. Geliştiricilerin yalnızca güvenilir koleksiyon API 'Leri ile programlama yapması ve güvenilir koleksiyonların çoğaltılan ve yerel durumu yönetmesine izin vermek gerekir.

Güvenilir koleksiyonlar ve diğer yüksek kullanılabilirlik teknolojileri (Redsıs, Azure Tablo hizmeti ve Azure Kuyruk hizmeti gibi) arasındaki önemli fark, durumun hizmet örneğinde yerel olarak tutulması ve yüksek oranda kullanılabilir yapılmalarıdır. Bunun anlamı:

* Tüm okumalar yereldir, bu da düşük gecikme süresi ve yüksek aktarım hızı okumalarına neden olur.
* Tüm yazma işlemleri, düşük gecikme süresi ve yüksek aktarım hızı yazmaları ile sonuçlanan en az ağ IOs sayısına tabi olur.

![Koleksiyonların gelişini görüntüsü.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Güvenilir koleksiyonlar, **sistem. Collections** sınıflarının doğal evrimi olarak düşünülebilir: geliştirici için karmaşıklığı arttırmadan bulut ve çoklu bilgisayar uygulamaları için tasarlanan yeni bir koleksiyonlar kümesi. Bu nedenle, güvenilir koleksiyonlar şunlardır:

* Çoğaltılamaz Durum değişiklikleri yüksek kullanılabilirlik için çoğaltılır.
* Olamıyor Veriler, büyük ölçekli kesintiler için disk üzerinde kalıcı hale getirilir (örneğin, bir veri merkezi güç kesintisi).
* Yazmaları kalıcı ve yinelenmiş olduğundan, geçici bir ReliableDictionary, ReliableQueue veya yalnızca bellekte verileri sürdüren diğer güvenilir koleksiyonları oluşturamazsınız.
* En GÇ 'Leri yüklerken iş parçacıklarının engellenmediğinden emin olmak için API 'Ler zaman uyumsuzdur.
* İşlem API 'Ler, bir hizmet içinde birden çok güvenilir koleksiyonu kolayca yönetebilmeniz için işlem soyutlamasını kullanır.

Güvenilir koleksiyonlar, uygulama durumunu daha kolay hale getirmek için önemli tutarlılık sağlar.
Kesin tutarlılık, işlem yürütmelerinin yalnızca, birincil dahil olmak üzere bir çok çoğaltmalarda tüm işlem günlüğe kaydedildikten sonra bitmesini sağlamak tarafından gerçekleştirilir.
Daha zayıf tutarlılık elde etmek için, uygulamalar zaman uyumsuz tamamlama döndürülmesinden önce istemci/istek sahibine geri bildirim alabilir.

Güvenilir koleksiyonlar API 'Leri, eşzamanlı koleksiyonlar API 'lerinin bir gelişmidir ( **System. Collections. eşzamanlı** ad alanında bulunur):

* En Eşzamanlı koleksiyonların aksine, işlemler çoğaltılmakta ve kalıcı hale getirilir, bu yana bir görevi döndürür.
* Out parametresi yok: Out `ConditionalValue<T>` parametreleri yerine bir `bool` ve değeri döndürmek için kullanır. `ConditionalValue<T>`gibidir `Nullable<T>` , ancak T 'nin bir struct olması gerekmez.
* Hareket Kullanıcının işlemleri bir işlemde birden çok güvenilir koleksiyonda grupkullanmasını sağlamak için bir işlem nesnesi kullanır.

Günümüzde **Microsoft. ServiceFabric. Data. Collections** üç koleksiyon vardır:

* [Güvenilir sözlük](https://msdn.microsoft.com/library/azure/dn971511.aspx): Anahtar/değer çiftlerinin çoğaltılan, işlemsel ve zaman uyumsuz koleksiyonunu temsil eder. **ConcurrentDictionary**ile benzer şekilde, hem anahtar hem de değer herhangi bir türde olabilir.
* [Güvenilir sıra](https://msdn.microsoft.com/library/azure/dn971527.aspx): Çoğaltılan, işlemsel ve zaman uyumsuz katı, ilk çıkar (FıFO) kuyruğunu temsil eder. **ConcurrentQueue**ile benzer şekilde, değer herhangi bir türde olabilir.
* [Güvenilir eşzamanlı sıra](service-fabric-reliable-services-reliable-concurrent-queue.md): Yüksek aktarım hızı için çoğaltılan, işlemsel ve zaman uyumsuz en iyi iş akış sırasını temsil eder. **ConcurrentQueue**ile benzer şekilde, değer herhangi bir türde olabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Güvenilir koleksiyon yönergeleri & önerileri](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Güvenilir Koleksiyonlar ile çalışma](service-fabric-work-with-reliable-collections.md)
* [İşlemler ve kilitler](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Verileri yönetme
  * [Yedekleme ve Geri Yükleme](service-fabric-reliable-services-backup-restore.md)
  * [Bildirimleri](service-fabric-reliable-services-notifications.md)
  * [Güvenilir Koleksiyonu serileştirme](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serileştirme ve yükseltme](service-fabric-application-upgrade-data-serialization.md)
  * [Güvenilir durum Yöneticisi yapılandırması](service-fabric-reliable-services-configuration.md)
* Diğerleri
  * [Reliable Services hızlı başlangıç](service-fabric-reliable-services-quick-start.md)
  * [Güvenilir koleksiyonlar için geliştirici başvurusu](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
