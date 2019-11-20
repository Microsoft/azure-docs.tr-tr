---
title: Azure Cosmos DB için yaygın kullanım örnekleri ve senaryolar
description: 'Azure Cosmos DB için en iyi beş kullanım durumu hakkında bilgi edinin: Kullanıcı tarafından oluşturulan içerik, olay günlüğü, Katalog verileri, Kullanıcı tercihleri verileri ve Nesnelerin İnterneti (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: de2bc551547706fb820813e57996e77bf49148d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888928"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Ortak Azure Cosmos DB kullanım örnekleri
Bu makalede Azure Cosmos DB yönelik birkaç yaygın kullanım durumuna genel bakış sunulmaktadır.  Bu makaledeki öneriler, Cosmos DB uygulamanızı geliştirirken başlangıç noktası olarak görev yapar.   

Bu makaleyi okuduktan sonra aşağıdaki soruları cevaplayabilirsiniz: 

* Azure Cosmos DB için genel kullanım örnekleri nelerdir?
* Perakende uygulamaları için Azure Cosmos DB kullanmanın avantajları nelerdir?
* Nesnelerin İnterneti (IoT) sistemleri için veri deposu olarak Azure Cosmos DB kullanmanın avantajları nelerdir?
* Web ve mobil uygulamalar için Azure Cosmos DB kullanmanın avantajları nelerdir?

## <a name="introduction"></a>Giriş
[Azure Cosmos DB](../cosmos-db/introduction.md) , Microsoft 'un genel olarak dağıtılmış veritabanı hizmetidir. Hizmet, müşterilerin herhangi bir sayıda coğrafi bölgede esnek (ve bağımsız olarak) işleme ve depolamayı ölçeklendirmesine izin verecek şekilde tasarlanmıştır. Azure Cosmos DB, günümüzde Market, gecikme süresi, kullanılabilirlik ve tutarlılık dahil olmak üzere kapsamlı [hizmet düzeyi sözleşmeleri](https://azure.microsoft.com/support/legal/sla/cosmos-db/) sunan ilk genel olarak dağıtılmış veritabanı hizmetidir. 

Azure Cosmos DB, çok çeşitli uygulamalarda ve kullanım durumlarında kullanılan küresel bir dağıtılmış, çok modelli veritabanıdır. En az milisaniyelik yanıt süreleriyle ihtiyacı olan ve hızlı ve küresel olarak ölçeklendirilmesi gereken [sunucusuz](https://azure.com/serverless) bir uygulama için iyi bir seçimdir. Birden çok veri modelini (anahtar-değer, belge, grafik ve sütun) ve veri erişimi için [Azure Cosmos DB, MongoDB](mongodb-introduction.md), [SQL API](documentdb-introduction.md), [Gremlin API](graph-introduction.md)ve [tablo API 'si](table-introduction.md) yerel olarak ve genişletilebilir bir şekilde bir çok API 'yi destekler. 

Aşağıda, genel Ambition ile yüksek performanslı uygulamalar için iyi uygun hale gelen Azure Cosmos DB öznitelikleri verilmiştir.

* Azure Cosmos DB yüksek kullanılabilirlik ve ölçeklenebilirlik için verilerinizi yerel olarak bölümlendirir. Azure Cosmos DB, kullanılabilirlik, aktarım hızı, düşük gecikme süresi ve tüm tek bölge hesapları ve gevşek tutarlılık ile tüm çok bölgeli hesaplar için% 99,99 garanti sağlar ve tüm çok bölgeli veritabanı hesaplarında% 99,999 okuma kullanılabilirliği sağlanır.
* Azure Cosmos DB, düşük Gecikmeli yanıt süresi sırasına sahip SSD özellikli depolamaya sahiptir.
* Azure Cosmos DB nihai, tutarlı ön ek, oturum ve sınırlı stalet gibi tutarlılık düzeylerine yönelik destek sayesinde tam esneklik ve düşük maliyetli performans oranı sağlanır. Bir veritabanı hizmeti, düzeyler tutarlılığı Azure Cosmos DB kadar esneklik sunar. 
* Azure Cosmos DB, depolama ve aktarım hızını bağımsız olarak kapsayan esnek, veri kullanımı kolay bir fiyatlandırma modeline sahiptir.
* Azure Cosmos DB için ayrılan aktarım hızı modeli, temel alınan donanımın CPU/bellek/IOPS 'si yerine okuma/yazma işlemlerinin sayısını düşünmenize olanak tanır.
* Azure Cosmos DB tasarımı, günde çok sayıda istek hacimlerde çok büyük istek birimlerine ölçeklendirmenize olanak tanır.

Bu öznitelikler, az yanıt süresi gerektiren Web, mobil, oyun ve IoT uygulamalarında faydalıdır ve çok büyük miktarlarda okuma ve yazma işlemlerini ele almanız gerekir.

## <a name="iot-and-telematics"></a>IoT ve telematikler
IoT kullanım örnekleri, verileri alma, işleme ve depolama gibi bazı desenleri yaygın olarak paylaşır.  İlk olarak, bu sistemlerin çeşitli yerel ayarlara ait cihaz sensörlerinden veri alımı gerekir. Ardından, bu sistemler gerçek zamanlı içgörüler elde etmek için akış verilerini işler ve analiz eder. Veriler daha sonra Batch Analytics için soğuk depolamaya arşivlenir. Microsoft Azure, Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight ve Power BI gibi IoT kullanım durumları için uygulanabilen zengin hizmetler sunar. 

![Azure Cosmos DB IoT başvuru mimarisi](./media/use-cases/iot.png)

Düşük gecikme süresine sahip yüksek aktarım hızı veri alımı sağladığından Azure Event Hubs veri kaynakları tarafından alınabilir. Gerçek zamanlı içgörüler için işlenmesi gereken veriler gerçek zamanlı analizler için Azure Stream Analytics. Veriler, geçici sorgulama için Azure Cosmos DB yüklenebilir. Veriler Azure Cosmos DB ' ye yüklendikten sonra, veriler sorgulanmaya uygulanabilir. Ayrıca, yeni veriler ve mevcut verilerde yapılan değişiklikler değişiklik akışında okunabilir. Değişiklik akışı, yalnızca Cosmos kapsayıcılarındaki değişiklikleri sıralı sırada depolayan kalıcı, yalnızca bir Append günlüğü. Tüm veriler veya yalnızca Azure Cosmos DB verilerde yapılan değişiklikler gerçek zamanlı analizler için başvuru verileri olarak kullanılabilir. Ayrıca, veriler, Pig, Hive veya eşleme/azaltma işleri için Azure Cosmos DB verileri HDInsight 'a bağlayarak daha sonra yeniden görüntülenebilir ve işlenebilir.  Daha sonra raporlama için Azure Cosmos DB arka arkaya veriler yüklenir.   

Azure Cosmos DB, EventHubs ve fırtınası kullanan örnek bir IoT çözümü için [GitHub 'da HDInsight-fırtınası-örnekler deposuna](https://github.com/hdinsight/hdinsight-storm-examples/)bakın.

IoT için Azure teklifleri hakkında daha fazla bilgi için bkz. [öğelerinizi Internet 'Te oluşturma](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Perakende ve pazarlama
Azure Cosmos DB, Microsoft 'un Windows Mağazası ve XBox Live çalıştıran kendi e-ticaret platformlarında yaygın olarak kullanılır. Ayrıca, katalog verilerini depolamak için perakende sektöründe ve işlem hatları sırasında olay kaynağını belirleme için de kullanılır.

Katalog veri kullanımı senaryoları, kişiler, Yerlerim ve ürünler gibi varlıklar için bir dizi özniteliğin depolanmasını ve sorgulanmasını içerir. Katalog verilerine ilişkin bazı örnekler Kullanıcı hesapları, ürün katalogları, IoT cihaz kayıt defterleri ve ürün reçeteleri sistemleridir. Bu verilerin öznitelikleri farklılık gösterebilir ve uygulama gereksinimlerine uyacak zaman içinde değişebilir.

Bir Oto için bölüm sağlayıcısı için bir ürün kataloğu örneği düşünün. Her parçanın, tüm parçaların paylaştığı ortak özniteliklere ek olarak kendi öznitelikleri olabilir. Ayrıca, belirli bir bölümün öznitelikleri, yeni bir model yayınlandığında aşağıdaki yılı değiştirebilir. Azure Cosmos DB esnek şemaları ve hiyerarşik verileri destekler ve bu nedenle ürün kataloğu verilerini depolamak için uygundur.

![Azure Cosmos DB Retail Catalog başvuru mimarisi](./media/use-cases/product-catalog.png)

Azure Cosmos DB, genellikle [değişiklik akışı](change-feed.md) işlevselliğini kullanarak güç olayı odaklı mimarilere yönelik olay kaynağı olarak kullanılır. Değişiklik akışı, aşağı akış mikro hizmetleri 'nin bir Azure Cosmos DB yapılan eklemeleri ve güncelleştirmeleri (örneğin, sipariş olayları) güvenilir ve artımlı olarak okumasına olanak sağlar. Bu işlevsellik, kalıcı bir olay mağazasının durum değiştirme olayları ve çok sayıda mikro hizmet arasında ( [sunucusuz Azure işlevleri](https://azure.com/serverless)olarak uygulanabilecek) sürücü sırası işleme iş akışını sağlamak için bir ileti Aracısı olarak yararlanılabilir olabilir.

![Azure Cosmos DB sıralama işlem hattı başvuru mimarisi](./media/use-cases/event-sourcing.png)

Ayrıca, Azure Cosmos DB depolanan veriler, Apache Spark işleri aracılığıyla büyük veri analizi için HDInsight ile tümleştirilebilir. Azure Cosmos DB Spark Bağlayıcısı hakkında daha fazla bilgi için bkz. [Cosmos DB ve HDInsight Ile Spark Işi çalıştırma](spark-connector.md).

## <a name="gaming"></a>Oyun
Veritabanı katmanı, oyun uygulamalarının önemli bir bileşenidir. Modern Oyunlar Mobil/konsol istemcilerinde grafik işleme gerçekleştirir, ancak oyun içi istatistikler, sosyal medya tümleştirme ve yüksek puanı olan öncü kartlar gibi özelleştirilmiş ve kişiselleştirilmiş içerikler sunmak için buluta bağımlıdır. Oyunlar, her zaman ilgi çekici bir oyun deneyimi sağlamak için okuma ve yazma işlemleri için tek milisaniyelik gecikme süreleri gerektirir. Bir oyun veritabanının hızlı olması ve yeni oyun başlatma ve özellik güncelleştirmeleri sırasında istek hızlarında büyük ani artışlar işleyebilmeleri gerekir.

Azure Cosmos DB, yürüyen ölü gibi Oyunlar tarafından kullanılır: [sonraki oyunlara](https://www.nextgames.com/)ve [Halo 5: velileri](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/)tarafından [hiçbir Man 'ın ARAI](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) . Azure Cosmos DB, oyun geliştiricileri için aşağıdaki avantajları sağlar:

* Azure Cosmos DB, performansının ölçeğini artırma veya azaltma esnek sağlar. Bu, oyunların tek bir API çağrısı yaparak düzinelerce ve milyonlarca eşzamanlı oyuncuya ait profil ve istatistikleri güncelleştirme işlemesini sağlar.
* Azure Cosmos DB, oyun yürütme sırasında herhangi bir lags kullanmaktan kaçınmak için milisaniyelik okuma ve yazma işlemlerini destekler.
* Azure Cosmos DB otomatik dizin oluşturma, birden çok farklı özelliğe karşı gerçek zamanlı olarak filtrelemeye olanak tanır. Örneğin, kendi iç oynatıcı kimliklerine ya da oyun merkezi, Facebook, Google kimliklerini veya bir Guild 'de oynatıcı üyeliğine bağlı olarak sorgu veya sorgu için oyuncuları bulun. Bu, karmaşık dizin oluşturma veya parçalama altyapısı oluşturmadan mümkündür.
* Oyun içi sohbet iletileri, oynatıcı Guld üyelikleri, tamamlanan sorunlar, yüksek puanı olan en yüksek puan ve sosyal grafikler, esnek bir şema ile daha kolay bir şekilde uygulanır.
* Yönetilen bir hizmet olarak platform (PaaS) olarak Azure Cosmos DB hızlı yineleme için en az kurulum ve yönetim çalışması gerekir ve pazara ulaşma süresini azaltır.

![Azure Cosmos DB Gaming Reference mimarisi](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Web uygulamaları ve mobil uygulamalar
Azure Cosmos DB genellikle Web ve mobil uygulamalarda kullanılır ve sosyal etkileşimleri modellemeye, üçüncü taraf hizmetlerle tümleştirilmesine ve zengin kişiselleştirilmiş deneyimler oluşturmaya yönelik oldukça uygundur. Cosmos DB SDK 'Ları, popüler [Xamarin çerçevesini](mobile-apps-with-xamarin.md)kullanarak zengin IOS ve Android uygulamaları oluşturmak için kullanılabilir.  

### <a name="social-applications"></a>Sosyal uygulamalar
Azure Cosmos DB için yaygın olarak kullanılan bir kullanım örneği, Web, mobil ve sosyal medya uygulamaları için Kullanıcı tarafından oluşturulan içeriği (UGC) depolamanıza ve sorgulayamaz. Bazı UGC örnekleri sohbet oturumları, çimler, blog gönderileri, derecelendirmeler ve açıklamalardır. Genellikle, sosyal medya uygulamalarında UıGC, ggıd yapısıyla sınırlı olmayan, serbest biçimli metin, özellikler, Etiketler ve ilişkilerin bir karışımdır. Sohbetleri gibi içerik, yorumları ve gönderileri Cosmos DB'de dönüşümlerini veya karmaşık nesne ilişkisel eşleme katmanlara gerek kalmadan depolanabilir.  Veri özellikleri, geliştiriciler uygulama kodu üzerinde yineleme yaparken gereksinimlere kolayca eklenebilir veya değiştirilebilir, böylece hızlı geliştirmeyi yükseltmektedir.  

Üçüncü taraf sosyal ağlarla tümleşen uygulamalar bu ağlardan değişen şemaları vermelidir. Veriler Cosmos DB varsayılan olarak otomatik olarak dizinlendiğinden, veriler istediğiniz zaman sorgulanmaya hazır olur. Bu nedenle, bu uygulamaların ilgili ihtiyaçlarına göre projeksiyonal esnekliği vardır.

Birçok sosyal uygulamalar küresel ölçekte çalışır ve tahmin edilemeyen kullanım desenleri oluşturabilir. Veri deposunun ölçeklendirilmesi, uygulama katmanı kullanım talebini eşleşecek şekilde ölçeklendirildiği için önemlidir.  Cosmos DB bir hesap altına ek veri bölümleri ekleyerek ölçeği değiştirebilirsiniz.  Ayrıca, birden çok bölgede daha fazla Cosmos DB hesap oluşturabilirsiniz. Cosmos DB hizmet bölgesi kullanılabilirliği için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/#services).

![Azure Cosmos DB Web uygulaması başvuru mimarisi](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Kişiselleştirme
Günümüzde, modern uygulamalar karmaşık görünümler ve deneyimlerle gelir. Bunlar genellikle dinamik, kullanıcı tercihlerine veya Moods ve marka ihtiyaçlarına göre yapılır. Bu nedenle, uygulamaların kullanıcı arabirimi öğelerini ve deneyimleri hızla işlemesini sağlamak için kişiselleştirilmiş ayarları etkili bir şekilde alabilmesi gerekir. 

Cosmos DB tarafından desteklenen bir biçim olan JSON, UI düzen verilerini yalnızca hafif olmadığından ve JavaScript tarafından kolayca yorumlanabilecek şekilde temsil edecek biçimde etkili bir biçimdir. Cosmos DB, düşük gecikmeli yazmalar ile hızlı okuma olanağı sağlayan, ayarlanabilir tutarlılık düzeyleri sunar. Bu nedenle, kişiselleştirilmiş ayarlar dahil olmak üzere Kullanıcı arabirimi düzen verilerini Cosmos DB JSON belgeleri olarak depolamak, bu verilerin hatta hatta olması için etkili bir araçtır.

![Azure Cosmos DB Web uygulaması başvuru mimarisi](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Sonraki adımlar

* Azure Cosmos DB kullanmaya başlamak için [hızlı başlangıç](create-sql-api-dotnet.md)deneyimimizi izleyin. Bu, bir hesap oluşturma ve Cosmos DB kullanmaya başlama konusunda size yol gösterir.

* Azure Cosmos DB kullanarak müşteriler hakkında daha fazla bilgi edinmek istiyorsanız [Müşteri örnek olay incelemeleri](https://azure.microsoft.com/case-studies/?service=cosmos-db) sayfasına bakın.
