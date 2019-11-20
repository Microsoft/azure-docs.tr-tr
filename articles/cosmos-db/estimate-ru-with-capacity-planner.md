---
title: Azure Cosmos DB kapasite planlayıcısını kullanarak maliyetleri tahmin etme
description: Azure Cosmos DB kapasite planlayıcısı, iş yükünüz için gereken aktarım hızını (RU/s) ve maliyeti tahmin etmenize olanak tanır. Bu makalede, gereken verimlilik ve maliyeti tahmin etmek için kapasite planlayıcısı 'nın yeni sürümünün nasıl kullanılacağı açıklanır.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707636"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Azure Cosmos DB kapasite planlayıcısını kullanarak RU/s 'yi tahmin etme

Azure Cosmos veritabanlarınızı ve Kapsayıcılarınızı, iş yükünüz için doğru sağlanan aktarım hızı veya [Istek birimleri (ru/s)](request-units.md)ile yapılandırarak maliyet ve performansı en iyi duruma getirme açısından önemlidir. Bu makalede, iş yükünüzün gerekli RU/sn ve maliyetinin bir tahminini almak için Azure Cosmos DB [Kapasite planlayıcısı](https://cosmos.azure.com/capacitycalculator/) 'nın nasıl kullanılacağı açıklanır. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Azure Cosmos DB kapasite planlayıcısı ile aktarım hızını ve maliyeti tahmin etme

Kapasite planlayıcısı iki modda kullanılabilir.

|**Modundaysa**  |**Açıklama**  |
|---------|---------|
|Temel|Hızlı, yüksek düzey RU/s ve maliyet tahmini sağlar. Bu mod, dizin oluşturma ilkesi, tutarlılık ve diğer parametrelerin varsayılan Azure Cosmos DB ayarlarını varsayar. <br/><br/>Azure Cosmos DB üzerinde çalışmak üzere olası bir iş yükünü değerlendirirken hızlı, yüksek düzey bir tahmin için temel modu kullanın.|
|Gelişmiş|Ek ayarları ayarlama (Dizin oluşturma ilkesi, tutarlılık düzeyi ve maliyeti ve aktarım hızını etkileyen diğer parametreler) sayesinde daha ayrıntılı bir RU/s ve maliyet tahmini sağlar. <br/><br/>Yeni bir proje için RU/s Tahmini yaparken veya daha ayrıntılı bir tahmin istediğinizde Gelişmiş modu kullanın. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Temel mod kullanarak sağlanan aktarım hızını ve maliyeti tahmin etme
Temel modu kullanarak iş yükünüz için hızlı bir tahmin sağlamak üzere [Kapasite planlayıcısı](https://cosmos.azure.com/capacitycalculator/)' na gidin. İş yükünüze göre aşağıdaki parametreleri girin: 

|**Girdi**  |**Açıklama**  |
|---------|---------|
|Bölge sayısı|Azure Cosmos DB tüm Azure bölgelerinde kullanılabilir. İş yükünüz için gereken bölge sayısını seçin. Cosmos hesabınızla istediğiniz sayıda bölgeyi ilişkilendirebilirsiniz. Daha fazla bilgi için bkz. Azure Cosmos DB [genel dağıtım](distribute-data-globally.md) .|
|Çok bölgeli yazma işlemleri|[Çok bölgeli yazmaları](distribute-data-globally.md#key-benefits-of-global-distribution)etkinleştirirseniz, uygulamanız herhangi bir Azure bölgesini okuyup yazabilir. Çok bölgeli yazmaları devre dışı bırakırsanız, uygulamanız tek bir bölgeye veri yazabilir. <br/><br/> Farklı bölgelerde düşük Gecikmeli yazma işlemleri gerektiren etkin-etkin bir iş yükünüz olmasını beklemeniz durumunda çok bölgeli yazmaları etkinleştirin. Örneğin, farklı bölgelerdeki yüksek hacimde veritabanına veri yazan bir ıOT iş yükü. <br/><br/> Çok bölgeli yazma% 99,999 okuma ve yazma kullanılabilirliğini garanti eder. Çok bölgeli yazma işlemleri, tek yazma bölgeleriyle karşılaştırıldığında daha fazla verimlilik gerektirir. Daha fazla bilgi edinmek için bkz. [Rus, tek ve birden çok yazma bölgesi için nasıl farklıdır](optimize-cost-regions.md) makalesi.|
|Depolanan toplam veri (bölge başına)|Tek bir bölgede GB cinsinden depolanan toplam tahmini veri.|
|Öğe boyutu|1 KB ile 2 MB arasında değişen veri öğesinin tahmini boyutu (örn. belge). |
|Bölge başına Okuma/sn|Saniye başına beklenen okuma sayısı. |
|Bölge başına yazma/sn|Saniye başına beklenen yazma sayısı. |

Gerekli ayrıntıları doldurduktan sonra **Calculate**' yi seçin. **Maliyet tahmini** sekmesi, depolama ve sağlanan aktarım hızı için toplam maliyeti gösterir. Okuma ve yazma istekleri için gereken verimlilik dökümünü almak için bu sekmedeki **Ayrıntıları göster** bağlantısını genişletebilirsiniz. Herhangi bir alanın değerini her değiştirişinizde, tahmini maliyeti yeniden hesaplamak için **Hesapla** ' yı seçin. 

![Kapasite planlayıcısı temel modu](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Gelişmiş modu kullanarak sağlanan aktarım hızını ve maliyeti tahmin etme

Gelişmiş mod, RU/s tahminini etkileyen daha fazla ayar sağlamanıza olanak tanır. Bu seçeneği kullanmak için, [Kapasite planlayıcısı](https://cosmos.azure.com/capacitycalculator/) ' na gidin ve Azure için kullandığınız bir hesapla araçta oturum açın. Oturum açma seçeneği sağ köşedeki kullanılabilir. 

Oturum açtıktan sonra, temel moddaki alanlarla karşılaştırılan ek alanlar görebilirsiniz. İş yükünüze göre ek parametreleri girin. 

|**Girdi**  |**Açıklama**  |
|---------|---------|
|API|Azure Cosmos DB, çok modelli ve çok API olan bir hizmettir. Yeni iş yükleri için SQL (çekirdek) API 'sini seçin. |
|Bölge sayısı|Azure Cosmos DB tüm Azure bölgelerinde kullanılabilir. İş yükünüz için gereken bölge sayısını seçin. Cosmos hesabınızla istediğiniz sayıda bölgeyi ilişkilendirebilirsiniz. Daha fazla bilgi için bkz. Azure Cosmos DB [genel dağıtım](distribute-data-globally.md) .|
|Çok bölgeli yazma işlemleri|[Çok bölgeli yazmaları](distribute-data-globally.md#key-benefits-of-global-distribution)etkinleştirirseniz, uygulamanız herhangi bir Azure bölgesini okuyup yazabilir. Çok bölgeli yazmaları devre dışı bırakırsanız, uygulamanız tek bir bölgeye veri yazabilir. <br/><br/> Farklı bölgelerde düşük Gecikmeli yazma işlemleri gerektiren etkin-etkin bir iş yükünüz olmasını beklemeniz durumunda çok bölgeli yazmaları etkinleştirin. Örneğin, farklı bölgelerdeki yüksek hacimde veritabanına veri yazan bir ıOT iş yükü. <br/><br/> Çok bölgeli yazma% 99,999 okuma ve yazma kullanılabilirliğini garanti eder. Çok bölgeli yazma işlemleri, tek yazma bölgeleriyle karşılaştırıldığında daha fazla verimlilik gerektirir. Daha fazla bilgi edinmek için bkz. [Rus, tek ve birden çok yazma bölgesi için nasıl farklıdır](optimize-cost-regions.md) makalesi.|
|Varsayılan tutarlılık|Azure Cosmos DB, geliştiricilerin tutarlılık, kullanılabilirlik ve gecikme süresi avantajları arasında zorunluluğunu getirir dengelamasına olanak tanımak için 5 tutarlılık düzeyini destekler. Daha fazla bilgi edinmek için [tutarlılık düzeyleri](consistency-levels.md) makalesine bakın. <br/><br/> Varsayılan olarak Azure Cosmos DB, oturum tutarlılığını kullanır ve bu, bir oturumdaki kendi yazma olanlarınızı okuyabilme olanağını garanti eder. <br/><br/> Güçlü veya sınırlandırılmış bir şekilde seçmek, oturum, tutarlı ön ek ve nihai tutarlılık ile karşılaştırıldığında, okuma için gereken RU/s 'yi gerektirir. Çok bölgeli yazmalarla güçlü tutarlılık desteklenmez ve otomatik olarak, güçlü tutarlılık içeren tek bölgeli yazmaları otomatik olarak varsayılan olarak kaydeder. |
|Dizin oluşturma ilkesi|Varsayılan olarak, Azure Cosmos DB tüm öğelerdeki tüm özellikleri esnek ve verimli sorgular ( **Otomatik** dizin oluşturma ilkesiyle eşlenir) için [dizine](index-policy.md) ekler. <br/><br/> **Kapalı**seçeneğini belirlerseniz, özelliklerin hiçbiri dizinlenemez. Bu, yazma işlemleri için en düşük RU ücretine neden olur. Yalnızca [nokta okuma](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) (anahtar değer aramaları) ve/veya yazma işlemleri ve sorgu yok olarak beklemeniz durumunda ilkeyi **Kapat** ' ı seçin. <br/><br/> Özel dizin oluşturma ilkesi, daha düşük yazma işleme ve depolama için dizinden belirli özellikleri dahil etmenize veya dışlayamazsınız. Daha fazla bilgi için bkz. [Dizin oluşturma ilkesi](index-overview.md) ve [örnek dizin oluşturma ilkeleri](how-to-manage-indexing-policy.md#indexing-policy-examples) makaleleri.|
|Depolanan toplam veri (bölge başına)|Tek bir bölgede GB cinsinden depolanan toplam tahmini veri.|
|İş yükü modu|İş yükü biriminiz sabit ise, sabit ' **i seçin.** <br/><br/> İş yükü biriminiz zaman içinde değişirse **değişken** ' i seçin.  Örneğin, belirli bir gün veya ay boyunca. <br/><br/> Değişken iş yükü seçeneğini belirlerseniz aşağıdaki ayarlar kullanılabilir:<ul><li>En yoğun zaman yüzdesi: İş yükünüzün en yüksek (en yüksek) aktarım hızı gerektirdiği bir ayda zaman yüzdesi. <br/><br/> Örneğin, 9:00:00 – 18:00 iş saatlerinde yüksek etkinlik içeren bir iş yükünüz varsa, yoğun sürenin yüzdesi şunlardır: 45 saat, en yüksek/730 saat/ay = ~ 6%.<br/><br/></li><li>Bölge başına en yoğun Okuma/sn-en yoğun saniye başına beklenen okuma sayısı.</li><li>Her bölge için en yoğun yazma/sn: en yüksek saniyede beklenen yazma sayısı.</li><li>Bölge başına en yoğun Okuma/sn – yoğun bir şekilde saniye başına beklenen okuma sayısı.</li><li>Bölge başına en yoğun yazma/sn: en yoğun olarak saniye başına beklenen yazma sayısı.</li></ul>Yoğun ve yoğun olmayan aralıklarda, [sağlanan aktarım hızını](set-throughput.md#update-throughput-on-a-database-or-a-container) uygun şekilde yukarı ve aşağı ölçeklendirerek maliyetinizi iyileştirebilmenizi sağlayabilirsiniz.|
|Öğe boyutu|1 KB ile 2 MB arasında değişen veri öğesi (ör. belge) boyutu. <br/><br/>**Örnek (JSON)** belgesini daha doğru bir tahmine göre de karşıya yükleyebilirsiniz.<br/><br/>İş yükünüz aynı kapsayıcıda birden çok türde öğe (farklı JSON içeriğiyle) varsa, birden fazla JSON belgesini karşıya yükleyebilir ve tahmini alabilirsiniz. Birden çok örnek JSON belgesi eklemek için **Yeni öğe Ekle** düğmesini kullanın.|

Geçerli tahmini içeren bir CSV dosyasını indirmek için **tahmin kaydet** düğmesini de kullanabilirsiniz. 

![Kapasite planlayıcısı Gelişmiş modu](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

Azure Cosmos DB kapasite planlayıcısı 'nda gösterilen fiyatlar, üretilen iş ve depolama için genel fiyatlandırma ücretlerine göre tahminlerdir. Tüm fiyatlar ABD Doları cinsinden gösterilmiştir. Bölgelere göre tüm oranları görmek için [Azure Cosmos DB fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) bakın.  

## <a name="estimating-throughput-for-queries"></a>Sorgular için üretilen iş tahmini

Azure Cosmos kapasite hesaplayıcı, işaret okumalarının (tek bir öğe, örneğin, KIMLIĞE ve bölüm anahtarı değerine göre) ve iş yükü için yazmaları olduğunu varsayar. Sorgular için gereken aktarım hızını tahmin etmek için sorgunuzu Cosmos kapsayıcısındaki temsili bir veri kümesi üzerinde çalıştırın ve [ru ücreti alın](find-request-unit-charge.md). İstenen toplam RU/sn sayısını almak için saniye başına çalıştırmayı tahmin ettiğiniz sorgu sayısıyla RU ücreti çarpın. 

Örneğin, iş yükünüz bir sorgu gerektiriyorsa, ``SELECT * FROM c WHERE c.id = 'Alice'`` Bu saniyede 100 kez çalıştırılır ve sorgunun ru ücreti 10 ru ise, bu isteklere yönelik olarak, toplam olarak 100 sorgu/sn * 10 ru/sorgu = 1000 ru/s gerekir. Bu RU/s 'leri, iş yükünüze yapılan tüm okuma ve yazma işlemleri için gereken RU/s 'ye ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB fiyatlandırma modeli](how-pricing-works.md)hakkında daha fazla bilgi edinin.
* Yeni bir [Cosmos hesabı, veritabanı ve kapsayıcısı](create-cosmosdb-resources-portal.md)oluşturun.
* [Sağlanan aktarım hızı maliyetini iyileştirmek](optimize-cost-throughput.md)hakkında bilgi edinin.
* [Ayrılmış kapasite ile maliyeti iyileştirmek](cosmos-db-reserved-capacity.md)hakkında bilgi edinin.

