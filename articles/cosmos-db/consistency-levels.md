---
title: Azure Cosmos DB'deki tutarlılık düzeyleri
description: Azure Cosmos DB Bakiye nihai tutarlılık, kullanılabilirlik ve gecikme süresi dengelemeler yardımcı olmak üzere beş tutarlılık düzeyi vardır.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 395b7bc31377fd771549a399032bad9d951ec804
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384928"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB'deki tutarlılık düzeyleri

Yüksek kullanılabilirlik, düşük gecikme süresi veya her ikisi de çoğaltma kullanan dağıtılmış veritabanları kullanılabilirlik, gecikme süresi ve aktarım hızı ve okuma tutarlılığı temel etmekten olun. Ticari olarak kullanılabilen en çok dağıtılmış veritabanları, geliştiricilerin iki üstün tutarlılık modeli arasından seçim yapmasını ister: *güçlü* tutarlılık ve *nihai* tutarlılık. Doğrizlebilirlik veya güçlü tutarlılık modeli, veri programlamasına yönelik altın standarttır. Ancak, daha yüksek gecikme süresi (düzenli durumda) ve düşük kullanılabilirlik (hatalarda) sağlar. Öte yandan, nihai tutarlılık daha yüksek kullanılabilirlik ve daha iyi performans sunar, ancak uygulamaların program tarafından kullanılabilmesini sağlar. 

Azure Cosmos DB olarak içeren iki uç nokta yerine seçimleri, veri tutarlılığını yaklaşıyor. Güçlü tutarlılık ve nihai tutarlılık, tayfın uçlarından, ancak SPI üzerinde birçok tutarlılık seçeneği vardır. Geliştiriciler, yüksek kullanılabilirlik ve performans açısından kesin seçimler ve ayrıntılı bir denge sağlamak için bu seçenekleri kullanabilir. 

Azure Cosmos DB ile geliştiriciler beş iyi tanımlanmış tutarlılık modeli üzerinde tutarlılık spektrumu arasından seçim yapabilirsiniz. En güçlü ve daha gevşek bir şekilde modeller, *güçlü*, *sınırlanmış Eskime durumu*, *oturum*, *tutarlı ön ek*ve *nihai* tutarlılık içerir. Modeller iyi tanımlanmış ve sezgisel olur ve belirli gerçek dünyada senaryolar için kullanılabilir. Her model [kullanılabilirlik ve performans avantajları](consistency-levels-tradeoffs.md) sağlar ve SLA 'lar tarafından desteklenir. Aşağıdaki görüntüde, farklı tutarlılık düzeyleri bir speklik olarak gösterilmiştir.

![Tutarlılık spektrumu olarak](./media/consistency-levels/five-consistency-levels.png)

Tutarlılık düzeyleri bölge açısından belirsizdir ve okuma ve yazma işlemleri, Azure Cosmos hesabınızla ilişkili bölge sayısı veya hesabınızın tek bir ile yapılandırılıp yapılandırılmadığını dikkate almaksızın tüm işlemler için garanti edilir. veya birden fazla yazma bölgesi.

## <a name="scope-of-the-read-consistency"></a>Okuma tutarlılığı kapsamı

Bölüm anahtar aralığı veya bir mantıksal bölüm içinde kapsamlı bir tek okuma işlemi okuma tutarlılığı uygular. Okuma işlemi, uzak bir istemci veya saklı yordam tarafından verilebilir.

## <a name="configure-the-default-consistency-level"></a>Varsayılan tutarlılık düzeyini yapılandırma

İstediğiniz zaman Azure Cosmos hesabınızdaki varsayılan tutarlılık düzeyini yapılandırabilirsiniz. Hesabınızda yapılandırılan varsayılan tutarlılık düzeyi, bu hesap altındaki tüm Azure Cosmos veritabanları ve kapsayıcıları için geçerlidir. Tüm okuma ve verilen bir kapsayıcı veya bir veritabanına karşı sorgular belirtilen tutarlılık düzeyi varsayılan olarak kullanır. Daha fazla bilgi için bkz. nasıl [varsayılan tutarlılık düzeyini yapılandırma](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Tutarlılık düzeyleri ile ilişkili garanti eder

Yüzde 100 okuma isteklerinin seçtiğiniz herhangi bir tutarlılık düzeyi için tutarlılık garantisini karşılayan Azure Cosmos DB garantisi tarafından sağlanan kapsamlı SLA'lar. Tutarlılık düzeyi ile ilişkili tüm tutarlılık garantileri sağlanırsa Okuma isteği tutarlılık SLA karşılar. [Azure-Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) GitHub deposunda Azure Cosmos DB içindeki beş tutarlılık DÜZEYININ, TLA + belirtim dilini kullanarak kesin tanımları sunulmaktadır.

Beş tutarlılık düzeyi semantiği aşağıda açıklanmıştır:

- **Güçlü**: Güçlü tutarlılık, bir doğrizlebilirlik garantisi sunar. Doğrizlebilirlik istekleri aynı anda sunma anlamına gelir. Okuma işlemleri, bir öğe işlenen en son sürümünü döndürmek için garanti edilir. Bir istemci hiçbir zaman işlenmemiş ya da kısmi bir yazma görür. Kullanıcıların her zaman en son kabul edilen yazma okumak için garanti edilir.

- **Sınırlanmış eskime**durumu: Okumaların tutarlı ön ek garantisi kabul edilmesi garanti edilir. Okumalar, bir öğenin en çok *"K"* sürümünde (yani "Updates") veya *"T"* zaman aralığına göre yazma işlemlerinin arkasında kalabilir. Diğer bir deyişle, sınırlanmış Eskime durumu ' nu seçtiğinizde, "stalet" iki şekilde yapılandırılabilir: 

  * Öğenin sürüm sayısı (*K*)
  * Okumaların yazma işlemlerinin arkasında gecikme olabileceği zaman aralığı (*T*) 

  Sınırlanmış eskime durumu teklifler toplam genel sıra dışında "eskime durumu penceresi içinde." Monoton okuma garantisi, hem Azure içindeki hem eskime durumu penceresi dışında bir bölge içinde mevcut. Güçlü tutarlılık, sınırlı stalet tarafından sunulan ile aynı semantiğe sahiptir. Eskime durumu penceresi sıfıra eşittir. Sınırlanmış eskime durumu, doğrusallaştırılabilme zaman Gecikmeli da bilinir. Bir istemci, yazmaları kabul eden bir bölgede okuma işlemleri gerçekleştirdiğinde, sınırlı stalet tutarlılığı tarafından sunulan garantiler, güçlü tutarlılık açısından bu garantilerle aynıdır.

- **Oturum**:  Tek bir istemci oturumu okumalarının içinde, tutarlı ön eki (tek bir "yazıcı" oturumu varsayılıyor), monoton okuma, monoton yazmaları, okuma-yazma ve yazma işlemleri gibi bir şekilde kabul etmek garanti edilir. Oturum yazma işlemi dışındaki istemciler nihai tutarlılığı görür.

- **Tutarlı ön ek**: Döndürülen güncelleştirmeler, tüm güncelleştirmelerin, boşluk olmadan bazı ön eklerini içerir. Tutarlı ön ek tutarlılık düzeyi, okumaların hiçbir şekilde sıra dışı yazmaları görmeme garantisi sağlar.

- Son: Okuma için sıralama garantisi yoktur. Başka yazma işlemlerinin olmaması durumunda, yineleme sonunda birbirine yaklaşır.

## <a name="consistency-levels-explained-through-baseball"></a>Beyzbol örneği ile açıklanan tutarlılık düzeyleri

Beyzbol oyun senaryoya örnek olarak alalım. Beyzbol oyunu baştan puanı temsil eden bir yazma dizisi düşünün. İnning tarafından inning satır puanı açıklanan [Beyzbol aracılığıyla veri tutarlılığı çoğaltılan](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) kağıt. Şu anda yedinci inning ortasında bu kuramsal Beyzbol oyundur. Bu, yedinci – inning esnetme olur. Ziyaretçiler aşağıda gösterildiği gibi 2-5 puanı ile geride bulunur:

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Çalıştırmalar** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Ziyaretçiler** | 0 | 0 | 1\. | 0 | 1\. | 0 | 0 |  |  | 2 |
| **Giriş** | 1 | 0 | 1\. | 1 | 0 | 2 |  |  |  | 5 |

Azure Cosmos kapsayıcısı, ziyaretçi ve ev ekiplerinin çalışma toplamlarını tutar. Garanti farklı puanları okuma istemcilerle sonuçlanabilir oyun devam ederken, farklı okuyun. Aşağıdaki tabloda her beş tutarlılık garantileri giriş puanları ve ziyaretçilerinizin okuyarak döndürülebilir puanları tam kümesini listeler. Ziyaretçilerinizin puanı önce listelenir. Farklı olası dönüş değerleri virgülle ayrılır.

| **Tutarlılık düzeyi** | **Puanlar (ziyaretçiler, ev)** |
| - | - |
| **Tanımlayıcı** | 2-5 |
| **Sınırlanmış eskime durumu** | En fazla bir tanesi güncel olmayan Puanlar: 2-3, 2-4, 2-5 |
| **Oturumu** | <ul><li>Yazıcı için: 2-5</li><li> Yazıcı dışındaki herkes için: 0-0, 0-1, 0-2, 0-3, 4 0, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5</li><li>1-3 okumadan sonra: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Tutarlı ön ek** | 0-0, 0-1, 1-1, 1-2, 1-3, 2-3, 2-4, 2-5 |
| **Nihai** | 0-0, 0-1, 0-2, 0-3, 4 0, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5 |

## <a name="additional-reading"></a>Ek okuma

Tutarlılık kavramları hakkında daha fazla bilgi edinmek için bu makaleleri okuyun:

- [Azure Cosmos DB tarafından sunulan beş tutarlılık düzeyi için üst düzey TLA + belirtimleri](https://github.com/Azure/azure-cosmos-tla)
- [Çoğaltılan verilerin tutarlılık açıklandığı aracılığıyla Beyzbol (video) Doug Terry tarafından](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Çoğaltılan verilerin tutarlılık açıklandığı aracılığıyla Beyzbol (Teknik İnceleme) Doug Terry tarafından](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Oturum, tutarlı zayıf çoğaltılan veriler için garanti eder](https://dl.acm.org/citation.cfm?id=383631)
- [Modern dağıtılmış veritabanı sistemleri tasarımında tutarlılık avantajları: CAP yalnızca hikayenin bir parçasıdır](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Olasılığa dayalı sınırlanmış eskime durumu (PBS) için pratik kısmi çekirdekleri](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Sonunda tutarlı - Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB'deki tutarlılık düzeyleri hakkında daha fazla bilgi edinmek için bu makaleleri okuyun:

* [Uygulamanız için doğru tutarlılık düzeyi seçme](consistency-levels-choosing.md)
* [Azure Cosmos DB API'leri arasında tutarlılık düzeyleri](consistency-levels-across-apis.md)
* [Çeşitli tutarlılık düzeyleri için kullanılabilirlik ve performans seçenekleri](consistency-levels-tradeoffs.md)
* [Varsayılan tutarlılık düzeyini yapılandırma](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Varsayılan tutarlılık düzeyi geçersiz kıl](how-to-manage-consistency.md#override-the-default-consistency-level)

