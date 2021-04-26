---
title: include dosyası
description: include dosyası
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/15/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1dd78ba3799573e05e4ebbf55887bae3d9674b7c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310092"
---
Event Hubs bir olay hub 'ına bir veya daha fazla bölüme gönderilen olay dizilerini düzenler. Daha yeni olaylar geldikçe, bu sıranın sonuna eklenir. 

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Bir bölüm, "kaydetme günlüğü" olarak düşünülebilir. Bölümler, olayın gövdesini, olayı tanımlayan Kullanıcı tanımlı bir özellik paketini, bölümdeki boşluğu gibi meta verileri, akış dizisindeki numarasını ve kabul edildiği hizmet tarafı zaman damgasını içeren olay verilerini tutar.

![Daha eski olay sırasını görüntüleyen diyagram.](./media/event-hubs-partitions/partition.png)

### <a name="advantages-of-using-partitions"></a>Bölüm kullanmanın avantajları
Event Hubs, büyük hacimden olayların işlenmesine yardımcı olmak üzere tasarlanmıştır ve bölümleme iki şekilde bu şekilde yardımcı olur:

- Event Hubs PaaS hizmeti olsa da, altında fiziksel bir gerçeklik vardır ve olayların sırasını koruyan bir günlüğün tutulması, bu olayların temel depolama ve çoğaltmalarda birlikte tutulması ve bu durumda bir günlük için üretilen iş yükü elde edilmesi gerekir. Bölümlendirme, aynı Olay Hub 'ı için birden çok paralel günlüklerin kullanılmasına izin verir ve bu nedenle kullanılabilir ham GÇ üretilen iş kapasitesini çarpar.
- Kendi uygulamalarınız, bir olay hub 'ına gönderilen olayların hacminin işlenmesine devam edebilmelidir. Karmaşık olabilir ve önemli, ölçeği genişletilmiş ve paralel işleme kapasitesi gerektirir. Olayları işlemek için tek bir işlemin kapasitesi sınırlıdır, bu nedenle birkaç işlem yapmanız gerekir. Bölümler, çözümünüzün bu işlemlere yönelik akışlardır ve her olayın açık bir işleme sahibi olmasını sağlar. 

### <a name="number-of-partitions"></a>Bölüm sayısı
Bölüm sayısı, oluşturma sırasında belirtilmiştir ve Event Hubs standardında 1 ile 32 arasında olmalıdır. Bölüm sayısı Event Hubs Ayrılmış Kapasite birimi başına en fazla 2000 bölüm olabilir. 

Bu belirli olay hub 'ı için uygulamanızın en yoğun yükü sırasında sürekli [üretilen iş birimleri (tu)](../articles/event-hubs/event-hubs-faq.yml#what-are-event-hubs-throughput-units-) için gereken en az sayıda bölüm seçmenizi öneririz. 1 TU işleme kapasitesine sahip tek bir bölümle hesaplamanız gerekir (1 Mbde, 2 Mbtu çıkış). Ad alanınızı veya kümenizin kapasite birimlerini bölüm sayısından bağımsız olarak ölçeklendirebilirsiniz. 32 bölümden oluşan bir olay hub 'ı veya 1 bölümlü bir olay hub 'ı, ad alanı 1 TU kapasitesine ayarlandığında tam olarak aynı maliyette olur. 

[Ayrılmış bir Event Hubs kümesindeki](../articles/event-hubs/event-hubs-dedicated-overview.md) bir olay hub 'ının bölüm sayısı, Olay Hub 'ı oluşturulduktan sonra [artırılabilir](../articles/event-hubs/dynamically-add-partitions.md) , ancak bölümler arasında akış dağıtımı, Bölüm anahtarlarının bölüm değişikliklerinin eşlenmesiyle yapıldığında, bu değişikliklerden kaçınmaya devam etmeniz gerekir. bu nedenle, olayların göreli sırası uygulamanızda önemli olduğunda bu değişikliklere engel olmak için katı çalışmanız gerekir.

Bölüm sayısının izin verilen maksimum değere ayarlanması, ancak her zaman birden çok bölümden faydalanmak için olay akışlarınızın yapılandırılması gerektiğini aklınızda bulundurun. Tüm olaylarda veya yalnızca bir alt akışlarda mutlak sipariş koruması gerekiyorsa, birçok bölümden faydalanabilmeyebilirsiniz. Ayrıca, birçok bölüm işleme tarafını daha karmaşık hale getirir. 


### <a name="mapping-of-events-to-partitions"></a>Olayları bölümlerle eşleme
Gelen olay verilerini veri düzenleme amacıyla belirli bölümlere eşlemek için bölüm anahtarı kullanabilirsiniz. Bölüm anahtarı, gönderen tarafından belirtilip bir olay hub'ına geçirilen değerdir. Statik karma işlevi ile işlenir ve sonuçta bölüm ataması oluşturulur. Bir olayı yayımlarken bölüm anahtarı belirtmezseniz hepsini bir kez deneme ataması kullanılır.

Olay yayımcısı yalnızca bölüm anahtarını bilir, olayların yayımlandığı bölümü bilmez. Anahtar ile bölümün bu şekilde ayrılması göndereni aşağı akış işleme hakkında çok fazla bilgi sahibi olma gereksiniminden kurtarır. Cihaz veya kullanıcı başına benzersiz bir kimlik iyi bir bölüm anahtarı oluşturur, ancak ilgili olayları tek bir bölümde gruplandırmak için coğrafi bölge gibi diğer öznitelikler de kullanılabilir.

Bir bölüm anahtarı belirtilmesi, ilgili olayları aynı bölümde ve gönderildikleri kesin sırayla birlikte tutmaya izin vermez. Bölüm anahtarı, uygulama bağlamınızdan türetilen ve olayların ilişkilerini tanımlayan bir dizedir. Bölüm anahtarı tarafından tanımlanan olaylar dizisi bir *akıştır*. Bölüm, birçok sayıda akış için çoğullanmış bir günlük deposudur. 

> [!NOTE]
> Olayları doğrudan bölümlere gönderebilmeniz mümkün olsa da, özellikle yüksek kullanılabilirlik sizin için önemli olduğunda bunu önermiyoruz. Bir olay hub 'ının kullanılabilirliği bölüm düzeyinde daha eski olur. Daha fazla bilgi için bkz. [kullanılabilirlik ve tutarlılık](../articles/event-hubs/event-hubs-availability-and-consistency.md).

