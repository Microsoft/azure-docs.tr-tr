---
title: Azure Cosmos DB'deki tutarlılık düzeyleri
description: Azure Cosmos DB nihai tutarlılık, kullanılabilirlik ve gecikme süreleri dengelemeye yardımcı olmak için beş tutarlılık düzeyi vardır.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0a203531e026d00b274ac98784076d33b22666d8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800151"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB'deki tutarlılık düzeyleri
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Yüksek kullanılabilirlik, düşük gecikme süresi veya her ikisi için çoğaltmaya dayanan dağıtılmış veritabanları, [paclc teoreminin](https://en.wikipedia.org/wiki/PACELC_theorem)tarafından tanımlanan okuma tutarlılığı, kullanılabilirliği, gecikme süresi ve aktarım hızı arasında temel bir zorunluluğunu getirir olmalıdır. Güçlü tutarlılık modelinin doğrizlebilirlik, veri programlamasına yönelik altın standarttır. Ancak büyük uzaklıklarda çoğaltmak ve yürütmek zorunda olması nedeniyle daha yüksek yazma gecikmeleri olan bir içerse fiyatı ekler. Veriler her bölgede çoğaltılamadığından ve işlemeden, güçlü tutarlılık de azalabilir (başarısızlık sırasında). Nihai tutarlılık daha yüksek kullanılabilirlik ve daha iyi performans sunar, ancak veriler tüm bölgelerde tamamen tutarlı olmayabilir.

Günümüzde piyasada bulunan ticari olarak kullanılabilen dağıtılmış NoSQL veritabanlarının çoğu yalnızca güçlü ve nihai tutarlılık sağlar. Azure Cosmos DB, iyi tanımlanmış beş düzeyi sunmaktadır. En güçlü ve en zayıf olan düzeyler şunlardır:

- *Güçlü*
- *Sınırlanmış Eskime durumu*
- *Oturumuna*
- *Tutarlı ön ek*
- *Son*

Her düzey kullanılabilirlik ve performans avantajları sağlar. Aşağıdaki görüntüde, farklı tutarlılık düzeyleri bir speklik olarak gösterilmiştir.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Bir spekme olarak tutarlılık" border="false" :::

Tutarlılık düzeyleri bölge açısından belirsizdir ve okuma ve yazma işlemleri, Azure Cosmos hesabınızla ilişkili bölge sayısı veya hesabınızın tek veya birden çok yazma bölgesiyle yapılandırılıp yapılandırılmadığını bağımsız olarak tüm işlemler için garanti edilir.

## <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Tutarlılık düzeyleri ve Azure Cosmos DB API’leri

Azure Cosmos DB popüler veritabanları için kablolu protokol ile uyumlu API 'Ler için yerel destek sağlar. Bunlar MongoDB, Apache Cassandra, Gremlin ve Azure Tablo depolama 'yı içerir. Gremlin API ve Tablo API'si kullanılırken, Azure Cosmos hesabında yapılandırılan varsayılan tutarlılık düzeyi kullanılır. Cassandra API veya MongoDB için API ve Azure Cosmos DB tutarlılık düzeyleri arasındaki tutarlılık düzeyi eşleme hakkında ayrıntılar için bkz. [MongoDB tutarlılık eşlemesi için](mongodb-consistency.md) [tutarlılık eşleme](cassandra-consistency.md) ve API 'yi Cassandra API.

## <a name="scope-of-the-read-consistency"></a>Okuma tutarlılığı kapsamı

Okuma tutarlılığı, mantıksal bir bölüm kapsamındaki tek bir okuma işlemi için geçerlidir. Okuma işlemi bir uzak istemci veya saklı yordam tarafından verilebilir.

## <a name="configure-the-default-consistency-level"></a>Varsayılan tutarlılık düzeyini yapılandırma

Azure Cosmos hesabınızda varsayılan tutarlılık düzeyini dilediğiniz zaman yapılandırabilirsiniz. Hesabınızda yapılandırılan varsayılan tutarlılık düzeyi, bu hesap altındaki tüm Azure Cosmos veritabanları ve kapsayıcıları için geçerlidir. Bir kapsayıcıya veya bir veritabanına karşı verilen tüm okuma ve sorgular varsayılan olarak belirtilen tutarlılık düzeyini kullanır. Daha fazla bilgi için bkz. [varsayılan tutarlılık düzeyini yapılandırma](how-to-manage-consistency.md#configure-the-default-consistency-level). Ayrıca, belirli bir istek için varsayılan tutarlılık düzeyini geçersiz kılabilirsiniz, daha fazla bilgi edinmek için bkz. [varsayılan tutarlılık düzeyi makalesini geçersiz kılma](how-to-manage-consistency.md?#override-the-default-consistency-level) .

> [!IMPORTANT]
> Varsayılan tutarlılık düzeyini değiştirdikten sonra herhangi bir SDK örneğinin yeniden oluşturulması gerekir. Bu, uygulama yeniden başlatılarak yapılabilir. Bu, SDK 'nın yeni varsayılan tutarlılık düzeyini kullanmasını sağlar.

## <a name="guarantees-associated-with-consistency-levels"></a>Tutarlılık düzeyleriyle ilişkili garanti garantisi

Azure Cosmos DB, okuma isteklerinin yüzde 100 ' unun seçilen tutarlılık düzeyi için tutarlılık garantisi karşıladığını garanti eder. [Azure-Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) GitHub deposunda Azure Cosmos DB içindeki beş tutarlılık DÜZEYININ, TLA + belirtim dilini kullanarak kesin tanımları sunulmaktadır.

Beş tutarlılık düzeyinin semantiği aşağıdaki bölümlerde açıklanmıştır.

### <a name="strong-consistency"></a>Güçlü tutarlılık

Güçlü tutarlılık, doğrusallaştırma garantisi sunar. Doğrizlebilirlik istekleri aynı anda sunma anlamına gelir. Okumaların, bir öğenin en son kaydedilmiş sürümünü döndürmesi garanti edilir. İstemci hiçbir şekilde işlenmemiş veya kısmi yazma görmez. Kullanıcıların her zaman en son yürütülen yazma okuma garantisi vardır.

  Aşağıdaki grafikte, müzik notlarıyla güçlü tutarlılık gösterilmektedir. Veriler "Batı ABD 2" bölgesine yazıldıktan sonra, diğer bölgelerden gelen verileri okurken en son değeri alırsınız:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="Güçlü tutarlılık düzeyinin çizimi":::

### <a name="bounded-staleness-consistency"></a>Sınırlanmış eskime durumu tutarlılığı

Sınırlı şekilde tutarlılık açısından, okuma tutarlılığı, tutarlı ön ek garantisi sağlamak açısından garanti edilir. Okumalar, bir öğenin (yani "Güncelleştirmeler"  ) veya *"T"* zaman aralığına göre yazma işlemlerinin arkasında, hangisi önce erişilerek gecikme gösterebilir. Diğer bir deyişle, sınırlanmış Eskime durumu ' nu seçtiğinizde, "stalet" iki şekilde yapılandırılabilir:

- Öğenin sürüm sayısı (*K*)
- Zaman aralığı (*T*) okuma işlemleri, yazma işlemlerinin arkasında kalabilir

Tek bir bölge hesabında, *K* ve *T* en küçük değeri 10 yazma işlemi veya 5 saniyedir. Çok bölgeli hesaplar için en küçük *K* ve *T* değeri 100.000 yazma işlemleri veya 300 saniyedir.

Sınırlanmış stalet, "stalet penceresi" dışında toplam genel sıra sunar. Bir istemci, yazmaları kabul eden bir bölgede okuma işlemleri gerçekleştirdiğinde, sınırlı stalet tutarlılığı tarafından sunulan garantiler, güçlü tutarlılık açısından bu garantilerle aynıdır. Bu pencere, zaman veya güncelleştirme için yaklaşısa da, ne kadar yaklaştığı için hizmet, çoğaltmanın tutarlı olmasını sağlamak için yeni yazma işlemlerini ortadan kaldırabilir.

Stalet penceresinin içinde, sınırlanmış Eskime durumu aşağıdaki tutarlılık garantisi sağlar:

- Tek bir yazma bölgesi olan bir hesap için aynı bölgedeki istemciler için tutarlılık = Strong
- Tek bir yazma bölgesi = tutarlı ön ek içeren bir hesap için farklı bölgelerdeki istemciler için tutarlılık
- Birden çok yazma bölgesi olan bir hesap için tek bir bölgeye yazan istemciler için tutarlılık = tutarlı ön ek
- Birden çok yazma bölgesi olan bir hesap için farklı bölgelere yazan istemciler için tutarlılık = son

  Sınırlı Eskime durumu, genellikle düşük yazma gecikmeleri bekleyen ancak toplam genel sipariş garantisi gerektiren küresel olarak dağıtılan uygulamalar tarafından seçilir. Sınırlı stalet, grup işbirliği ve paylaşım, stok şeridi, yayımlama-abonelik/sıraya alma gibi uygulamalar için harika bir yöntemdir. Aşağıdaki grafik, müzik notları ile sınırlı stalet tutarlılığını gösterir. Veriler "Batı ABD 2" bölgesine yazıldıktan sonra, "Doğu ABD 2" ve "Avustralya Doğu" bölgeleri, yapılandırılan en fazla gecikme süresine veya en yüksek işlemlere göre yazılan değeri okur:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="Sınırlanmış stalet tutarlılık düzeyinin çizimi":::

### <a name="session-consistency"></a>Oturum tutarlılığı

Oturum tutarlılığı ' nda, tek bir istemci oturumu okuma, tutarlı ön ek, monoton okuma, monoton yazma, okuma-yazma ve yazma ile okuma garantisi garanti edilir. Bu, tek bir "yazıcı" oturumunun olduğunu varsayar veya birden çok yazıcı için oturum belirtecini paylaşıyor.

Oturum yazma işlemi dışındaki istemciler aşağıdaki garantilere sahip olur:

- Tek bir yazma bölgesi = tutarlı ön ek içeren bir hesap için aynı bölgedeki istemciler için tutarlılık
- Tek bir yazma bölgesi = tutarlı ön ek içeren bir hesap için farklı bölgelerdeki istemciler için tutarlılık
- Birden çok yazma bölgesi olan bir hesap için tek bir bölgeye yazan istemciler için tutarlılık = tutarlı ön ek
- Birden fazla yazma bölgesi olan bir hesap için birden çok bölgeye yazan istemciler için tutarlılık = son

  Oturum tutarlılığı, hem tek bölge hem de küresel olarak dağıtılan uygulamalar için en yaygın olarak kullanılan tutarlılık düzeyidir. Nihai tutarlılık ile karşılaştırılabilir yazma gecikmeleri, kullanılabilirlik ve okuma üretilen işi sağlar, ancak aynı zamanda bir kullanıcı bağlamında çalışmak üzere yazılmış uygulamaların ihtiyaçlarına uygun tutarlılık garantisi sağlar. Aşağıdaki grafik, müzik notları ile oturum tutarlılığını göstermektedir. "Batı ABD 2 yazıcısı" ve "Batı ABD 2 okuyucu" aynı oturumu (oturum A) kullanıyor, böylece her ikisi de aynı anda aynı verileri okur. "Avustralya Doğu" bölgesi "oturum B" kullandığından, verileri daha sonra ve yazma sırasıyla aynı sırada alır.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="Oturum tutarlılığı düzeyi çizimi":::

### <a name="consistent-prefix-consistency"></a>Tutarlı ön ek tutarlılığı

Tutarlı ön ek seçeneğinde, döndürülen güncelleştirmeler boşluklar olmadan tüm güncelleştirmelerin bazı ön eklerini içerir. Tutarlı ön ek tutarlılık düzeyi, okumaların hiçbir şekilde sıra dışı yazmaları görmeme garantisi sağlar.

Sıralamada yazma işlemleri gerçekleştirildiyse, istemci, veya `A, B, C` `A` `A,B` `A,B,C` gibi hiçbir bir sıra dışı permütasyon görür `A,C` `B,A,C` . Tutarlı ön ek, nihai tutarlılık ile karşılaştırılabilir yazma gecikmeleri, kullanılabilirlik ve okuma üretilen işi sağlar, ancak aynı zamanda siparişin önemli olduğu senaryoların ihtiyaçlarına uygun sıra garantisi sağlar.

Tutarlı ön ek için tutarlılık garantisi aşağıda verilmiştir:

- Tek bir yazma bölgesi = tutarlı ön ek içeren bir hesap için aynı bölgedeki istemciler için tutarlılık
- Tek bir yazma bölgesi = tutarlı ön ek içeren bir hesap için farklı bölgelerdeki istemciler için tutarlılık
- Birden çok yazma bölgesi = tutarlı ön ek içeren bir hesap için tek bir bölgeye yazan istemciler için tutarlılık
- Birden fazla yazma bölgesi olan bir hesap için birden çok bölgeye yazan istemciler için tutarlılık = son

Aşağıdaki grafikte, müzik notlarıyla tutarlılık ön eki tutarlılığı gösterilmektedir. Tüm bölgelerde okumalar hiçbir şey yazma sırası görmez:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="Tutarlı ön ek çizimi":::

### <a name="eventual-consistency"></a>Nihai tutarlılık

Nihai tutarlılık bölümünde, okuma için sıralama garantisi yoktur. Yazma işlemleri kesildiğinde çoğaltmalar nihai tutarlılığa ulaşacaktır.  
Son tutarlılık, bir istemci, daha önce okuduğundan daha eski olan değerleri okuyabileceğinden en zayıf tutarlılık biçimidir. Nihai tutarlılık, uygulamanın herhangi bir sıralama garantisi gerektirmediğinden idealdir. Örnek olarak yeniden doldurulabilir, beğeni veya iş parçacıklı yorumların sayısını verilebilir. Aşağıdaki grafikte, müzik notlarıyla nihai tutarlılık gösterilmektedir.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="nihai tutarlılık için Vinillüstrasyon":::

## <a name="consistency-guarantees-in-practice"></a>Uygulamada tutarlılık garantisi

Uygulamada genellikle daha güçlü tutarlılık garantisi alabilirsiniz. Bir okuma işleminin tutarlılık garantisi, istediğiniz veritabanı durumunun yeniliği ve sıralamasına karşılık gelir. Okuma tutarlılığı, yazma/güncelleştirme işlemlerinin sıralaması ve yayılması ile bağlantılıdır.  

Veritabanında yazma işlemi yoksa, son, **oturum** veya **tutarlı ön ek** tutarlılık düzeylerine sahip bir okuma **işlemi, güçlü** tutarlılık düzeyiyle bir okuma işlemiyle aynı sonuçları elde etmek olasıdır.

Azure Cosmos hesabınız, güçlü tutarlılık dışında bir tutarlılık düzeyiyle yapılandırıldıysa, olasılık açısından *olasılığa sınırlı bir Stalet* (PBS) ölçüsüne bakarak istemcilerinizin iş yükleriniz için güçlü ve tutarlı okumalar alabilir olasılığını fark edebilirsiniz. Bu ölçüm Azure portal kullanıma sunulur, daha fazla bilgi edinmek için bkz. [Izleme olasılığa yönelik sınırlı Stalet (PBS) ölçümü](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Dayalı sınırlanmış stalet, nihai tutarlılığın ne kadar olduğunu gösterir. Bu ölçüm, Azure Cosmos hesabınızda halen yapılandırdığınız tutarlılık düzeyinden daha güçlü bir tutarlılık elde etme konusunda bir fikir sağlar. Diğer bir deyişle, yazma ve okuma bölgelerinin bir birleşimi için kesin olarak tutarlı okuma elde etme olasılığını (milisaniye olarak ölçülür) görebilirsiniz.

## <a name="consistency-levels-and-latency"></a>Tutarlılık düzeyleri ve gecikme

Tüm tutarlılık düzeyleri için okuma gecikmesi her zaman 99 ' luk yüzdede 10 milisaniyeden az olacak şekilde garanti edilir. 50. yüzdede ortalama okuma gecikmesi, genellikle 4 milisaniyedir veya küçüktür.

Tüm tutarlılık düzeyleri için yazma gecikmesi her zaman 99 ' luk yüzdede 10 milisaniyeden az olacak şekilde garanti edilir. 50. yüzdede ortalama yazma gecikmesi genellikle 5 milisaniyedir veya küçüktür. Çeşitli bölgelere yayılan ve güçlü tutarlılık ile yapılandırılmış Azure Cosmos hesapları, bu garanti için bir özel durumdur.

### <a name="write-latency-and-strong-consistency"></a>Yazma gecikmesi ve güçlü tutarlılık

Birden fazla bölgeyle güçlü tutarlılık ile yapılandırılmış Azure Cosmos hesaplarında, yazma gecikmesi iki en uzak bölge arasında iki kat gidiş dönüş süresine (RTT) ve 99. yüzdede 10 milisaniyeye eşittir. Güçlü tutarlılık bir işlemi yalnızca bir hesap içindeki tüm bölgelere taahhüt ettikten sonra tamamladıktan sonra, bölgeler arasındaki yüksek ağ RTT, Cosmos DB isteklerinde daha yüksek gecikme süresine çevrilecek.

Tam RTT gecikmesi, hafif bir mesafe ve Azure ağ topolojisi 'nin bir işlevidir. Azure ağ, iki Azure bölgesi arasında RTT için herhangi bir gecikme süresi sağlamaz, ancak [Azure ağ gidiş dönüş gecikme süresi istatistiklerini](../networking/azure-network-latency.md)yayımlamaz. Azure Cosmos hesabınız için çoğaltma gecikmeleri Azure portal görüntülenir. Azure Cosmos hesabınızla ilişkili çeşitli bölgeler arasındaki çoğaltma gecikme sürelerini izlemek için Azure portal (ölçümler dikey penceresine gidin, tutarlılık sekmesi ' ni seçin).

> [!IMPORTANT]
> Yüksek yazma gecikmesi nedeniyle 5000 mil 'tan fazla (8000 kiloters) yayılan bölgelere sahip hesaplar için güçlü tutarlılık, varsayılan olarak engellenir. Bu özelliği etkinleştirmek için lütfen desteğe başvurun.

## <a name="consistency-levels-and-throughput"></a>Tutarlılık düzeyleri ve verimlilik

- Güçlü ve sınırlı bir uyumluluk için, okuma, tutarlılık garantisi sağlamak üzere dört çoğaltma kümesindeki (minınlık çekirdeği) iki kopyaya karşı yapılır. Oturum, tutarlı ön ek ve nihai tek çoğaltma okumaları. Sonuç olarak, aynı sayıda istek birimi için, güçlü ve sınırlanmış eskime için okuma aktarım hızı diğer tutarlılık seviyelerinin yarısıdır.

- INSERT, Replace, upsert ve DELETE gibi belirli bir yazma işlemi türü için, istek birimleri için yazma üretilen işi tüm tutarlılık düzeyleri için aynıdır.

|**Tutarlılık Düzeyi**|**Çekirdek okuma**|**Çekirdek yazmaları**|
|--|--|--|
|**Güçlü**|Yerel Minınlık|Küresel çoğunluk|
|**Sınırlanmış Eskime durumu**|Yerel Minınlık|Yerel çoğunluk|
|**Oturumuna**|Tek çoğaltma (oturum belirtecini kullanarak)|Yerel çoğunluk|
|**Tutarlı ön ek**|Tek çoğaltma|Yerel çoğunluk|
|**Son**|Tek çoğaltma|Yerel çoğunluk|

> [!NOTE]
> Yerel Minalallık okumaları için okunan RU/sn, güçlü ve sınırlanmış bir şekilde tutarlılık garantisi sağlamak üzere iki çoğaltmadan okuma yapıldığından daha zayıf tutarlılık seviyelerinin iki katına daha güvenlidir.

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Tutarlılık düzeyleri ve veri dayanıklılığı

Küresel olarak dağıtılmış bir veritabanı ortamında, bölge genelinde bir kesinti olması durumunda tutarlılık düzeyi ve veri dayanıklılığı arasında doğrudan bir ilişki vardır. İş sürekliliği planınızı geliştirirken, kesintiye uğratan bir olaydan sonra uygulamanın tam olarak kurtarmadan önce kabul edilebilir en uzun süreyi anlamanız gerekir. Uygulamanın tam olarak kurtarılması için gereken süre, **kurtarma zamanı hedefi** (**RTO**) olarak bilinir. Ayrıca, uygulamanın, kesintiye uğratan bir olaydan sonra kurtarılırken kabul edebildiği en son veri güncelleştirme süresini de anlamanız gerekir. Kaybetmemek için uygun olan güncelleştirmelerin zaman dilimi **Kurtarma noktası hedefi** (**RPO**) olarak bilinir.

Aşağıdaki tabloda, bölge genelinde kesinti olması durumunda tutarlılık modeli ve veri dayanıklılığı arasındaki ilişki tanımlanmaktadır. Dağıtılmış bir sistemde, güçlü tutarlılıkla bile, bir RPO ile dağıtılmış bir veritabanına sahip olma imkanını ve üst [sınır](https://en.wikipedia.org/wiki/CAP_theorem)nedeniyle 0 ' dan sıfır olmasını unutmamak önemlidir.

|**Bölge (ler)**|**Çoğaltma modu**|**Tutarlılık düzeyi**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Tek veya birden çok yazma bölgesi|Herhangi bir tutarlılık düzeyi|< 240 dakika|<1 hafta|
|>1|Tek bir yazma bölgesi|Oturum, tutarlı ön ek, nihai|< 15 dakika|< 15 dakika|
|>1|Tek bir yazma bölgesi|Sınırlanmış Eskime Durumu|*K*  &  *T*|< 15 dakika|
|>1|Tek bir yazma bölgesi|Güçlü|0|< 15 dakika|
|>1|Birden çok yazma bölgesi|Oturum, tutarlı ön ek, nihai|< 15 dakika|0|
|>1|Birden çok yazma bölgesi|Sınırlanmış Eskime Durumu|*K*  &  *T*|0|

*K* = bir öğenin *"K"* sürümlerinin (yani Updates) sayısı.

*T* = son güncelleştirmeden bu yana *"t"* zaman aralığı.

Tek bir bölge hesabında, *K* ve *T* en küçük değeri 10 yazma işlemi veya 5 saniyedir. Çok bölgeli hesaplar için en küçük *K* ve *T* değeri 100.000 yazma işlemleri veya 300 saniyedir. Bu, sınırlı Stalet kullanılırken veri için minimum RPO 'yu tanımlar.

## <a name="strong-consistency-and-multiple-write-regions"></a>Güçlü tutarlılık ve birden çok yazma bölgesi

Birden çok yazma bölgesi ile yapılandırılan Cosmos hesapları, dağıtılmış bir sistem için sıfır ve 0 ' ın bir RPO sağlaması mümkün olmadığından, güçlü tutarlılık için yapılandırılamaz. Ayrıca, birden fazla yazma bölgesiyle güçlü tutarlılık kullanılmasına yönelik yazma gecikme avantajları yoktur, çünkü herhangi bir bölgeye yazma çoğaltılmalı ve hesaptaki tüm yapılandırılmış bölgelere atanmalıdır. Bu, tek bir yazma bölgesi hesabıyla aynı yazma gecikme süresine neden olur.

## <a name="additional-reading"></a>Ek okuma

Tutarlılık kavramları hakkında daha fazla bilgi edinmek için aşağıdaki makaleleri okuyun:

- [Azure Cosmos DB tarafından sunulan beş tutarlılık düzeyi için üst düzey TLA + belirtimleri](https://github.com/Azure/azure-cosmos-tla)
- [Doug Terry tarafından bebey(video) Ile açıklanan çoğaltılan veri tutarlılığı](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Doug Terry tarafından bebey(Teknik İnceleme) Ile açıklanan çoğaltılan veri tutarlılığı](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Zayıf tutarlı çoğaltılan veriler için oturum garantisi](https://dl.acm.org/citation.cfm?id=383631)
- [Modern dağıtılmış veritabanı sistemleri tasarımında tutarlılık avantajları: CAP yalnızca hikayenin bir parçasıdır](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Pratik kısmi çekirdeklerine için dayalı sınırlanmış Stalet (PBS)](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Sonuçta tutarlı yeniden ziyaret edildi](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB 'deki tutarlılık düzeyleri hakkında daha fazla bilgi edinmek için aşağıdaki makaleleri okuyun:

- [Varsayılan tutarlılık düzeyini yapılandırma](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Varsayılan tutarlılık düzeyini geçersiz kılma](how-to-manage-consistency.md#override-the-default-consistency-level)
- [SLA Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)
