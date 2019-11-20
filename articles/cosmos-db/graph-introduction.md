---
title: Azure Cosmos DB Gremlin API'sine giriş
description: Düşük gecikme süresine sahip yoğun grafikleri Apache TinkerPop’un Gremlin grafik sorgu dilini kullanarak depolamak, sorgulamak ve dolaştırmak için Azure Cosmos DB’yi nasıl kullanabileceğinizi öğrenin.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/18/2019
ms.author: lbosq
ms.openlocfilehash: 1f46eb1995e2e7cb098098ebd22eedbd194dc6a6
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310247"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB’ye giriş: Gremlin API

[Azure Cosmos DB, Microsoft](introduction.md)'un görev açısından kritik uygulamalar için genel olarak dağıtılmış, çok modelli bir veritabanı hizmetidir.  Bu, çok modelli bir veritabanıdır ve belge, anahtar-değer, grafik ve sütun ailesi veri modellerini destekler. Azure Cosmos DB Gremlin API, herhangi bir ölçek için tasarlanan tam olarak yönetilen bir veritabanı hizmetinde grafik verilerini depolamak ve bunlarla çalışmak için kullanılır.  

![Azure Cosmos DB grafik mimarisi](./media/graph-introduction/cosmosdb-graph-architecture.png)

Bu makale, Azure Cosmos DB Gremlin API'ye genel bir bakış sağlar ve milyarlarca köşesi ve kenarı olan yoğun grafikleri depolamak için bunu nasıl kullanabileceğinizi açıklar. Daha fazla milisaniyelik gecikme süresiyle grafikleri sorgulayabilir ve Graf yapısını kolayca geliştirebilirsiniz. Azure Cosmos DB Gremlin API 'si [Apache tinkerpop](https://tinkerpop.apache.org) Graph veritabanı standardını temel alır ve Gremlin sorgu dilini kullanır. 

Azure Cosmos DB Gremlin API 'SI, grafik veritabanı algoritmalarının gücünü yüksek düzeyde ölçeklenebilir, yönetilen altyapıyla birleştirerek esneklik ve ilişkisel yaklaşımlarla ilişkili en yaygın veri sorunlarına yönelik benzersiz ve esnek bir çözüm sunar. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Azure Cosmos DB grafik veritabanının özellikleri
 
Azure Cosmos DB; genel dağıtım, depolama ve aktarım hızında esnek ölçeklendirme, otomatik dizinleme ve sorgu, ayarlanabilir tutarlılık düzeyleri ve TinkerPop standardı desteği sunan, tam olarak yönetilen bir grafik veritabanıdır. 

Azure Cosmos DB Gremlin API 'nin sunduğu farklı özellikler şunlardır:

* **Esnek ölçeklenebilir işleme ve depolama**

  Gerçek dünyadaki grafiklerin, tek bir sunucunun kapasitesinin yetmeyeceği biçimde ölçeklendirilmesi gerekir. Azure Cosmos DB, depolama ve sağlanan aktarım hızı bakımından neredeyse sınırsız bir boyuta sahip olan yatay olarak ölçeklenebilir grafik veritabanlarını destekler. Grafik veritabanı ölçeği büyüdükçe, veriler [grafik bölümlendirme](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning)kullanılarak otomatik olarak dağıtılacaktır.

* **Çok bölgeli çoğaltma**

  Azure Cosmos DB, grafik verilerinizi dünyanın her yerindeki Azure bölgesine otomatik olarak çoğaltabilir. Genel çoğaltma, verilere genel erişim gerektiren uygulamaların geliştirilmesini basitleştirir. Azure Cosmos DB, dünyanın her yerindeki okuma ve yazma gecikmesini en aza indirmenin yanı sıra, bir bölgedeki hizmet kesintisi durumunda uygulamanızın sürekliliği için otomatik bölgesel yük devretme mekanizması sağlar. 

* **En yaygın benimseyen Graph sorgu standardı ile hızlı sorgular ve traversals**

  Heterojen köşeleri ve kenarları depolayın ve bunları tanıdık Gremlin sözdizimi aracılığıyla sorgulayın. Gremlin, yaygın grafik algoritmaları uygulamak için zengin bir arabirim sağlayan, zorunlu, işlevsel bir sorgu dilidir. 
  
  Azure Cosmos DB, şema ipuçları, ikincil dizinler veya görünümler belirtmeye gerek olmadan zengin gerçek zamanlı sorguları ve traversals sağlar. Daha fazla bilgi için bkz. [Gremlin kullanarak sorgu grafikleri](gremlin-support.md).

* **Tam yönetilen grafik veritabanı**

  Azure Cosmos DB, veritabanı ve makine kaynaklarını yönetme ihtiyacını ortadan kaldırır. Birçok mevcut grafik veritabanı platformu, altyapısının kısıtlamalarına bağlanır ve genellikle çalışmasını sağlamak için yüksek düzeyde bakım gerektirir. 
  
  Tam olarak yönetilen bir hizmet olarak, Cosmos DB sanal makineleri yönetme, çalışma zamanı yazılımlarını güncelleştirme, parçalama veya çoğaltmayı yönetme veya karmaşık veri katmanı yükseltmeleriyle işleme gereksinimini ortadan kaldırır. Tüm grafikler otomatik olarak yedeklenir ve bölgesel arızalara karşı korunur. Bu garanti, geliştiricilerin grafik veritabanlarını çalıştırmak ve yönetmek yerine uygulama değeri sunmaya odaklanmasını sağlar. 

* **Otomatik Dizin oluşturma**

  Azure Cosmos DB, varsayılan olarak grafiğin düğümlerindeki ve kenarlarındaki tüm özelliklerin otomatik olarak dizinini oluşturur ve herhangi bir şemayı ya da ikincil dizinlerin oluşturulmasını beklemez veya gerektirmez. [Azure Cosmos DB 'de dizin oluşturma](https://docs.microsoft.com/azure/cosmos-db/index-overview)hakkında daha fazla bilgi edinin. 

* **Apache TinkerPop ile uyumluluk**

  Azure Cosmos DB, [Açık kaynaklı Apache TinkerPop standardını](http://tinkerpop.apache.org/)destekler. Tinkerpop Standard, Azure Cosmos DB Gremlin API 'siyle kolayca tümleştirilebilen, uygulama ve kitaplıkların örnek bir ekosistemine sahiptir. 

* **Ayarlanabilir tutarlılık düzeyleri**

  Azure Cosmos DB, uygulamanız için tutarlılık ve performans arasında doğru zorunluluğunu getirir elde etmek üzere beş iyi tanımlanmış tutarlılık düzeyi sağlar. Azure Cosmos DB sorgular ve okuma işlemleri için beş farklı tutarlılık düzeyi sunar: güçlü, sınırlanmış eskime durumu, oturum, tutarlı ön ek ve son. Bu ayrıntılı ve iyi tanımlanmış tutarlılık düzeyleri tutarlılık, kullanılabilirlik ve gecikme süresi arasında sağlam bir denge kurmanıza olanak sağlar. [Azure Cosmos DB'deki ayarlanabilir tutarlılık düzeyleri](consistency-levels.md) sayfasına giderek daha fazla bilgi edinin.

## <a name="scenarios-that-can-use-gremlin-api"></a>Gremlin API kullanabilen senaryolar
Grafik desteğinin Azure Cosmos DB yararlı olabilecek bazı senaryolar aşağıda verilmiştir:

* **Sosyal ağlar/müşteri 365**

  Müşterileriniz ve diğer kişilerle etkileşimleri hakkındaki verileri birleştirerek kişiselleştirilmiş deneyimleri geliştirebilir, müşteri davranışını tahmin edebilir veya kişileri benzer ilgi alanlarına sahip başkalarına bağlayabilirsiniz. Azure Cosmos DB, sosyal ağları yönetmek ve müşteri tercihlerini ve verilerini izlemek için kullanılabilir.

* **Öneri motorları**

  Bu senaryo, perakende sektöründe yaygın olarak kullanılır. Ürünler, kullanıcılar ve satın alma, gözatma veya bir öğeyi derecelendirme gibi kullanıcı etkileşimleri hakkındaki bilgileri birleştirerek özelleştirilmiş öneriler oluşturabilirsiniz. Düşük gecikme süresi, elastik ölçek ve Azure Cosmos DB yerel grafik desteği Bu senaryolar için idealdir.

* **Uzamsal**

  Telekomünikasyon, lojistik ve seyahat planlama alanlarındaki birçok uygulamanın, bir bölgede ilgi çekici bir konum bulması veya iki konum arasındaki en kısa/en uygun yolu belirlemesi gerekir. Azure Cosmos DB, bu sorunları için idealdir.

* **Nesnelerin İnterneti**

  IOT cihazları arasındaki ağ ve bağlantılar bir grafik olarak modellendiğinde, cihazlarınızın ve varlıklarınızın durumunu daha iyi kavrayabilirsiniz. Ayrıca ağın bir bölümündeki değişikliklerin başka bir bölümü nasıl etkileyebileceğini de öğrenebilirsiniz.

## <a name="introduction-to-graph-databases"></a>Grafik veritabanlarına giriş
Gerçek dünyada görünen veriler doğal olarak bağlıdır. Geleneksel veri modelleme, varlıkları ayrı olarak tanımlamaya ve çalışma zamanında ilişkilerini hesaplamaya odaklanır. Bu modelin avantajları olsa da, yüksek oranda bağlantılı verilerin kısıtlamaları altında yönetilmesi zor olabilir.  

Grafik veritabanı yaklaşımı, depolama katmanındaki kalıcı ilişkileri temel alır. bunun yerine, son derece verimli grafik alma işlemlerine yol açar. Azure Cosmos DB Gremlin API 'SI, [özellik grafik modelini](https://tinkerpop.apache.org/docs/current/reference/#intro)destekler.

### <a name="property-graph-objects"></a>Özellik grafiği nesneleri

Özellik [grafiği](http://mathworld.wolfram.com/Graph.html) [köşeler](http://mathworld.wolfram.com/GraphEdge.html)ve kenarlardan oluşan bir yapıdır. [](http://mathworld.wolfram.com/GraphVertex.html) Her iki nesne de özellik olarak rastgele sayıda anahtar-değer çiftine sahip olabilir. 

* Köşeler **-köşeler** , bir kişi, yer veya olay gibi ayrı varlıkları gösterir.

* **Kenarlar** - Kenarlar, köşeler arasındaki ilişkileri gösterir. Bir kişinin başka bir kişiyi tanıması, bir etkinliğe katılması veya kısa süre önce bir konumda bulunması bağlantılar buna örnek gösterilebilir. 

* **Özellikler** - Özellikler, köşeler ve kenarlar hakkında bilgi ifade eder. Köşelerin ya da kenarlarda herhangi bir sayıda özellik olabilir ve bir sorgudaki nesneleri anlatmak ve filtrelemek için kullanılabilirler. Örnek özellikler, ad ve yaş içeren bir köşeyi ya da bir zaman damgası ve/veya ağırlığı olabilen bir kenarı içerir. 

Grafik veritabanları genellikle NoSQL veya ilişkisel olmayan veritabanı kategorisinde bulunur, çünkü bir şemaya veya kısıtlanmış veri modeline bağımlılık yoktur. Bu şema eksikliği, bağlantılı yapıların doğal ve verimli şekilde modellenmesi ve depolanmasına olanak tanır. 

### <a name="gremlin-by-example"></a>Örneğe göre Gremlin
Sorguların Gremlin’de nasıl ifade edildiğini anlamak için örnek bir grafik kullanalım. Aşağıdaki şekilde kullanıcılar, ilgi alanları ve cihazlar hakkındaki verileri yöneten bir iş uygulaması grafik biçiminde gösterilir.  

![Kişileri, cihazları ve ilgi alanlarını gösteren örnek grafik](./media/gremlin-support/sample-graph.png) 

Bu grafik, aşağıdaki *köşe* türlerine sahiptir (Gremlin içinde "etiket" adı verilir):

- **Kişiler**: Grafikte üç kişi, deneme, Thomas ve ben bulunur
- **Ilgi alanları**: Bu örnekteki ilgi alanları, bu örnekte futbol oyunu
- **Cihazlar**: Kişilerin kullandığı cihazlar
- **Işletim sistemleri**: Cihazların üzerinde çalıştığı işletim sistemleri

Aşağıdaki *uç* türleri/Etiketler aracılığıyla bu varlıklar arasındaki ilişkileri temsil ediyoruz:

- **Biliyor**: Örneğin, "Thomas biliyor"
- **Ilgileniyor**: Grafımız kişilerin ilgi alanlarınızı göstermek için örneğin, "Ben futbol ile ilgileniyor"
- **Runsos**: Dizüstü bilgisayar Windows işletim sistemini çalıştırır
- Şunu **kullanır**: Bir kişinin hangi cihaza kullandığını göstermek için. Örneğin Robin, seri numarası 77 olan bir Motorola telefon kullanır

Şimdi [Gremlin Console](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console)’u kullanarak bu grafiğe yönelik birkaç işlem yapalım. Dilerseniz bu işlemleri, tercih ettiğiniz platformdaki (Java, Node.js, Python veya .NET) Gremlin sürücülerini kullanarak da gerçekleştirebilirsiniz.  Azure Cosmos DB’de nelerin desteklendiğine bakmadan önce söz dizimine hakkında bilgi edinmek için birkaç örneğe bakalım.

İlk olarak CRUD’a bakalım. Aşağıdaki Gremlin deyimi “Thomas” köşesini grafiğe ekler:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Daha sonra aşağıdaki Gremlin deyimi, Thomas ve Robin arasına bir “Tanıma” kenarı ekler.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Aşağıdaki sorgu, “kişiler” köşesini ilk adlarına göre azalan sırada döndürür:
```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Grafiklerin asıl iyi olduğu kısımlar, “Thomas’ın arkadaşları hangi işletim sistemini kullanıyor?” gibi sorular sorduğunuzda ortaya çıkıyor. Bu bilgileri grafikten almak için bu Gremlin çapraz geçişini çalıştırabilirsiniz:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Sonraki adımlar
Azure Cosmos DB’de grafik desteği hakkında daha fazla bilgi için bkz.

* [Azure Cosmos DB grafik öğreticisi](create-graph-dotnet.md) ile çalışmaya başlayın.
* [Azure Cosmos DB’de Gremlin kullanarak grafikleri sorgulamayı](gremlin-support.md) öğrenin.
