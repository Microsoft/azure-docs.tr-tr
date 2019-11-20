---
title: Azure Cosmos DB aktarım hızını en iyi duruma getirme
description: Bu makalede, Azure Cosmos DB depolanan veriler için üretilen iş maliyetlerinin nasıl iyileştirileceği açıklanır.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 4bdf842ae24d90850280a5a19038dbd00168ff2c
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053359"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Azure Cosmos DB sağlanan üretilen iş maliyetini iyileştirin

Sağlanan aktarım hızı modeli sunarak Azure Cosmos DB, herhangi bir ölçekte tahmin edilebilir performans sağlar. Zaman ayırarak üretilen iş hacmi, performansınızda "gürültülü komşu" etkisini ortadan kaldırır. İhtiyacınız olan aktarım hızını tam olarak belirtirsiniz ve Azure Cosmos DB, SLA tarafından desteklenen, yapılandırılan üretilen işi garanti eder.

En az 400 RU/sn aktarım hızı ile başlayabilir ve saniyede on milyonlarca istek ve daha fazlasını ölçeklendirebilirsiniz. Azure Cosmos kapsayıcınızda veya bir okuma isteği, yazma isteği, sorgu isteği, saklı yordamların, sizin sağladığınız iş maliyetinizden kesilen karşılık gelen bir maliyeti olan her bir istek için sorun. 400 RU/sn temin edebilir ve maliyeti 40 RU olan bir sorgu verirseniz, saniye başına 10 tür sorgu gönderebilirsiniz. Bundan sonraki tüm istekler hız sınırlı alır ve isteği yeniden denemeniz gerekir. İstemci sürücüleri kullanıyorsanız, otomatik yeniden deneme mantığını destekler.

Veritabanları veya kapsayıcılar üzerinde üretilen iş sağlayabilirsiniz ve her strateji, senaryoya bağlı olarak maliyetlerde tasarruf etmenize yardımcı olabilir.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Farklı düzeylerde sağlama verimini sağlayarak iyileştirin

* Bir veritabanında üretilen iş temin ediyorsanız, bu veritabanındaki koleksiyonlar/tablolar/grafikler için tüm kapsayıcılar yük temelinde aktarım hızını paylaşabilir. Veritabanı düzeyinde ayrılan aktarım hızı, belirli bir kapsayıcı kümesindeki iş yüküne bağlı olarak eşit olarak paylaşılır.

* Bir kapsayıcıda üretilen iş temin ediyorsanız, bu kapsayıcı için SLA tarafından desteklenen verimlilik garanti edilir. Mantıksal bölüm anahtarı seçimi, bir kapsayıcının tüm mantıksal bölümlerinde yük dağıtımı için çok önemlidir. Daha fazla ayrıntı için bkz. [bölümlendirme](partitioning-overview.md) ve [yatay ölçeklendirme](partition-data.md) makaleleri.

Aşağıda, sağlanan bir verimlilik stratejisine karar vermek için bazı yönergeler verilmiştir:

Şu **durumlarda bir Azure Cosmos veritabanında (bir kapsayıcı kümesi içeren) üretilen iş sağlamayı düşünün**:

1. Birkaç düzine Azure Cosmos Kapsayıcınız var ve bunların bazıları veya tümünde üretilen işi paylaşmak istiyorsunuz. 

2. IaaS barındırılan VM 'lerde veya şirket içinde çalışacak şekilde tasarlanan tek kiracılı bir veritabanından geçiş yapıyorsanız, örneğin, NoSQL veya ilişkisel veritabanları Azure Cosmos DB. Birden çok koleksiyon/tablo/grafik varsa ve veri modelinizde herhangi bir değişiklik yapmak istemiyorsanız. Şirket içi bir veritabanından geçiş yaparken veri modelinizi güncelleştirmeiyorsanız Azure Cosmos DB tarafından sunulan avantajlardan bazılarının güvenliğini tehlikeye atabilir. Performans açısından en iyi şekilde yararlanmak ve ayrıca maliyetleri iyileştirmek için veri modelinize her zaman yeniden erişmeniz önerilir. 

3. İş yüklerindeki planlanmamış ani artışları artışlarını devralarak, veritabanı düzeyinde tabi üretilen iş yükünü, iş yükünde beklenmedik şekilde ani bir biçimde sanallaştırarak etmek istiyorsunuz. 

4. Tek tek kapsayıcılarda belirli aktarım hızı ayarlamak yerine, veritabanının içindeki bir kapsayıcı kümesi boyunca toplam aktarım hızını alma konusunda dikkatli olursunuz.

**Şu durumlarda tek bir kapsayıcıda üretilen işi sağlamayı düşünün:**

1. Birkaç Azure Cosmos Kapsayıcınız vardır. Azure Cosmos DB şema belirsiz olduğundan, bir kapsayıcı heterojen şemaları olan öğeleri içerebilir ve müşterilerin her varlık için bir tane olmak üzere birden çok kapsayıcı türü oluşturmasını gerektirmez. Bu her zaman göz önünde bulundurmanız 10-20 gereken tek bir seçenektir. Kapsayıcılar için en az 400 ru ile, tüm 10-20 kapsayıcıları tek bir havuza, daha uygun maliyetli olabilir. 

2. Belirli bir kapsayıcıdaki aktarım hızını denetlemek ve SLA tarafından desteklenen belirli bir kapsayıcıda garantili aktarım hızını almak istiyorsunuz.

**Yukarıdaki iki stratejinin karma öğesini düşünün:**

1. Daha önce belirtildiği gibi Azure Cosmos DB, yukarıdaki iki stratejiyi karıştırabileceğiniz ve eşleştirecek ve artık Azure Cosmos veritabanı dahilinde bazı kapsayıcılara sahip olabilirsiniz. bu sayede, veritabanında sağlanan aktarım hızını ve ayrıca aynı veritabanı içindeki bazı kapsayıcıları paylaşabilir , özel olarak sağlanan üretilen iş miktarlarına sahip olabilir. 

2. Yukarıdaki stratejileri, bir karma yapılandırma ile birlikte çalışmak için uygulayabilirsiniz. burada, belirli bir iş hacmi olan bazı kapsayıcılarda veritabanı düzeyinde sağlanan aktarım hızına sahip olursunuz.

Aşağıdaki tabloda gösterildiği gibi, API seçimine bağlı olarak, farklı bir ayırluluya aktarım hızı sağlayabilirsiniz.

|eklentisi|**Paylaşılan** verimlilik için yapılandırma |**Adanmış** aktarım hızı için yapılandırma |
|----|----|----|
|SQL API’si|Database|Kapsayıcı|
|MongoDB için Azure Cosmos DB API'si|Database|Koleksiyon|
|Cassandra API’si|anahtar alanı|Tablo|
|Gremlin API|Veritabanı hesabı|Graph|
|Tablo API’si|Veritabanı hesabı|Tablo|

Farklı düzeylerde üretilen iş yükünü sağlarken, iş yükünüzün özelliklerine göre maliyetlerinizi iyileştirebilirsiniz. Daha önce belirtildiği gibi, programlama yoluyla ve herhangi bir zamanda tek tek kapsayıcılar veya bir kapsayıcı kümesi genelinde sağlanan aktarım hızını artırabilir ya da azaltabilirsiniz. İş yükünüz değiştikçe ölçek işleme esnek göre yalnızca yapılandırdığınız aktarım hızı için ödeme yaparsınız. Kapsayıcınız veya bir kapsayıcı kümesi birden çok bölgeye dağıtılmışsa, kapsayıcıda yapılandırdığınız üretilen iş veya bir kapsayıcı kümesi tüm bölgelerde kullanılabilir hale getirilir.

## <a name="optimize-with-rate-limiting-your-requests"></a>İsteklerin hız sınırlaması ile en iyileştirin

Gecikme süresine duyarlı olmayan iş yükleri için daha az üretilen iş sağlayabilir ve gerçek aktarım hızı sağlanan aktarım hızını aştığında uygulamanın hız sınırlaması olmasını sağlayabilirsiniz. Sunucu, isteği `RequestRateTooLarge` (HTTP durum kodu 429) ile sona erpreemptively ve kullanıcının isteği yeniden denemeden önce beklemesi gereken süreyi milisaniye olarak belirten `x-ms-retry-after-ms` üst bilgisini döndürür. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>SDK 'larda yeniden deneme mantığı 

Yerel SDK 'lar (.NET/.NET Core, Java, Node. js ve Python), bu yanıtı dolaylı olarak yakalayıp sunucu tarafından belirtilen yeniden deneme üst bilgisine göre yakalar ve isteği yeniden dener. Hesabınız birden çok istemci tarafından aynı anda erişilmediği takdirde, sonraki yeniden deneme başarılı olur.

İstek hızının sürekli olarak birden fazla istemciniz varsa, o anda 9 ' a ayarlanmış olan varsayılan yeniden deneme sayısı yeterli olmayabilir. Bu gibi durumlarda, istemci uygulamaya 429 durum kodu ile bir `RequestRateTooLargeException` oluşturur. Varsayılan yeniden deneme sayısı, ConnectionPolicy örneğindeki `RetryOptions` ayarlanarak değiştirilebilir. Varsayılan olarak, istek hızının üzerinde çalışmaya devam ederse, 429 durum koduna sahip `RequestRateTooLargeException`, 30 saniyelik birikmeli bir bekleme süresi dolduktan sonra döndürülür. Bu durum, geçerli yeniden deneme sayısı en fazla yeniden deneme sayısından az olduğunda bile, varsayılan olarak 9 veya Kullanıcı tanımlı bir değer olmalıdır. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet) 3 olarak ayarlanır. bu nedenle, bir istek işlemi, kapsayıcının ayrılmış aktarım hızını aşarak sınırlı olursa istek işlemi, uygulamaya özel durumu oluşturmadan önce üç kez yeniden dener. [Maxretrywaittimeınseconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) 60 olarak ayarlanır. bu nedenle, ilk istek 60 saniye değerini aştığından kümülatif yeniden deneme bekleme süresi saniye cinsinden, özel durum atılır.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Bölümlendirme stratejisi ve sağlanan üretilen iş maliyetleri

Azure Cosmos DB maliyetleri iyileştirmek için iyi bölümleme stratejisi önemlidir. Depolama ölçümleri aracılığıyla sunulan bölümlerin eğriliği olmadığından emin olun. Üretilen iş ölçümleriyle birlikte sunulan bir bölüm için işleme eğriliği olmadığından emin olun. Belirli bölüm anahtarlarını doğru bir şekilde eğmek olmadığından emin olun. Depolama alanındaki baskın anahtarlar ölçümler aracılığıyla sunulur ancak anahtar, uygulama erişim öründile göre değişir. Doğru mantıksal bölüm anahtarı hakkında düşünmek en iyisidir. İyi bir bölüm anahtarının aşağıdaki özelliklere sahip olması beklenir:

* İş yükünü tüm bölümler arasında eşit olarak ve zaman içinde eşit olarak yayılan bir bölüm anahtarı seçin. Diğer bir deyişle, verilerin büyük çoğunluğuna ve bazı anahtarlara sahip olmayan bir anahtara sahip olmanız gerekmez. 

* Erişim desenlerinin mantıksal bölümler arasında eşit olarak yayılmasını sağlayan bir bölüm anahtarı seçin. İş yükü tüm anahtarlar arasında eşit olarak kabul edilir. Diğer bir deyişle, iş yükünün çoğu belirli bir anahtara odaklanmamalıdır. 

* Geniş bir değer aralığına sahip bir bölüm anahtarı seçin. 

Temel düşünce, veri depolama ve aktarım kaynakları için kaynakların mantıksal bölümler arasında dağıtılabilecek şekilde, verileri ve etkinliklerinizi kapsayıcıda mantıksal bölümler kümesi genelinde yaymaya yöneliktir. Bölüm anahtarları adayları, sorgularda filtre olarak sık görüntülenen özellikleri içerebilir. Filtre koşuluna bölüm anahtarı eklenerek sorgular etkin bir şekilde yönlendirilebilir. Bu tür bir bölümleme stratejisiyle sağlanan üretilen işi iyileştirmek çok daha kolay olacaktır. 

### <a name="design-smaller-items-for-higher-throughput"></a>Daha yüksek aktarım hızı için daha küçük öğeler tasarlama 

Belirli bir işlemin istek ücreti veya istek işleme maliyeti, öğenin boyutuyla doğrudan bağıntılı olur. Büyük öğelerdeki işlemler, daha küçük öğeler üzerinde işlemlerden daha fazlasını ücretlendirilecektir. 

## <a name="data-access-patterns"></a>Veri erişimi desenleri 

Verilere ne sıklıkla erişdiklerinizi temel alarak verilerinizi mantıksal kategoriler halinde mantıksal olarak ayırmak her zaman iyi bir uygulamadır. Sık erişimli, orta veya soğuk veriler olarak kategorilere ayırarak, tüketilen depolamayı ve gereken aktarım hızını hassas bir şekilde ayarlayabilirsiniz. Erişim sıklığına bağlı olarak, verileri ayrı kapsayıcılara (örneğin, tablolar, grafikler ve koleksiyonlar) yerleştirebilir ve bu veri segmentinin ihtiyaçlarına uyum sağlamak için bunların üzerinde sağlanan aktarım hızını hassas bir şekilde ayarlayabilirsiniz. 

Ayrıca, Azure Cosmos DB kullanıyorsanız ve belirli veri değerlerine göre arama yapabildiğinizi veya nadiren erişebileceğinizi biliyorsanız, bu özniteliklerin sıkıştırılmış değerlerini depolamanız gerekir. Bu yöntemle depolama alanı, Dizin alanı ve sağlanan aktarım hızı ile tasarruf edersiniz ve daha düşük maliyetlerle sonuçlanır.

## <a name="optimize-by-changing-indexing-policy"></a>Dizin oluşturma ilkesini değiştirerek iyileştirin 

Varsayılan olarak, Azure Cosmos DB her kaydın her bir özelliğini otomatik olarak dizine ekler. Bu, geliştirmeyi kolaylaştırmak ve birçok farklı türde geçici sorgu üzerinde mükemmel performans sağlamak için tasarlanmıştır. Binlerce özellik içeren büyük kayıtlarınız varsa, özellikle bu özelliklerden yalnızca 10 veya 20 ' ye göre sorgulama yaparsanız, her özelliğin dizinleme için üretilen iş maliyetini ödemesine yardımcı olabilirsiniz. Belirli iş yükünüze yönelik bir tanıtıcı elde etmeniz daha da sonra, kılavuzumuz Dizin ilkenizi ayarlamanıza yardımcı olur. Azure Cosmos DB Dizin oluşturma ilkesi hakkında tam Ayrıntılar [burada](indexing-policies.md)bulunabilir. 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Sağlanan ve tüketilen verimlilik izleniyor 

Sağlanan toplam RUs sayısını, hız sınırlı isteklerin sayısını ve Azure portal kullandığınız ru sayısını izleyebilirsiniz. Aşağıdaki görüntüde örnek kullanım ölçümü gösterilmektedir:

![Azure portal istek birimlerini izleme](./media/optimize-cost-throughput/monitoring.png)

Ayrıca, hız sınırlı isteklerin sayısının belirli bir eşiği aşıp aşmadığını denetlemek için uyarılar da ayarlayabilirsiniz. Daha fazla bilgi için bkz. [Azure Cosmos DB makalesini izleme](use-metrics.md) . Bu uyarılar, sağlanan aktarım hızını otomatik olarak artırmak için hesap yöneticilerine e-posta gönderebilir veya özel bir HTTP Web kancası veya bir Azure Işlevi çağırabilir. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Aktarım hızını esnek ve isteğe bağlı olarak ölçeklendirin 

Sağlanan aktarım hızı için faturalandırılırsınız, sağlanan aktarım hızını gereksinimlerinize göre eşleştirmek, kullanılmayan aktarım hızına karşı ücretlendirmeden kaçınmanıza yardımcı olabilir. Sağlanan aktarım hızını dilediğiniz zaman gerektiği gibi ölçeklendirebilir veya azaltabilirsiniz.  

* Ru 'nizin tüketimini izlemek ve hız sınırlı isteklerin oranı, her gün veya hafta boyunca sağlanan sabitten haberdar olmanız gerekmediğini açığa çıkabilir. Gece veya hafta sonu sırasında daha az trafik alabilirsiniz. Azure portal veya Azure Cosmos DB yerel SDK 'Ları veya REST API kullanarak, sağlanan aktarım hızını dilediğiniz zaman ölçeklendirebilirsiniz. Azure Cosmos DB REST API, kapsayıcılarınızın performans düzeyini programlı bir şekilde güncelleştirmek için uç noktalar sağlar. bu sayede, bir günün saatine veya haftanın gününe bağlı olarak kodunuzda üretilen işi ayarlayabilirsiniz. İşlem herhangi bir kesinti olmadan gerçekleştirilir ve genellikle bir dakikadan kısa bir süre içinde devreye girer. 

* Aktarım hızını ölçeklendirmeniz gereken alanlardan biri, örneğin veri geçişi sırasında Azure Cosmos DB veri alma. Geçişi tamamladıktan sonra çözümün kararlı durumunu işlemek için sağlanan aktarım hızını azaltabilirsiniz.  

* Faturalandırma, bir saatten fazla ayrıntı düzeyinde olduğundan, sağlanan aktarım hızını aynı anda bir saatten fazla sıklıkta değiştirirseniz herhangi bir para kazanmayacaksınız.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Yeni bir iş yükü için gereken aktarım hızını belirleme 

Yeni bir iş yükünün sağlanan verimini öğrenmek için aşağıdaki adımları kullanabilirsiniz: 

1. Kapasite planlayıcısını kullanarak ilk ve kaba bir değerlendirme gerçekleştirin ve Azure portal Azure Cosmos Explorer yardımıyla tahminlerinizi ayarlayın. 

2. Kapsayıcıları beklenenden daha yüksek aktarım hızı ile oluşturmanız ve ardından gerektikçe ölçeklendirilmesi önerilir. 

3. İsteklerin hız sınırlı olduğunda otomatik yeniden denemelerden yararlanmak için yerel Azure Cosmos DB SDK 'Lardan birini kullanmanız önerilir. Desteklenmeyen bir platformda çalışıyorsanız Cosmos DB REST API kullanın `x-ms-retry-after-ms` üst bilgisini kullanarak kendi yeniden deneme ilkenizi uygulayın. 

4. Tüm yeniden denemeler başarısız olduğunda uygulama kodunuzun düzgün şekilde desteklediğinden emin olun. 

5. Fiyat sınırlama bildirimleri almak için Azure portal uyarıları yapılandırabilirsiniz. Son 15 dakika boyunca 10 oranlık sınırlı istek gibi koruyucu limitlerle başlayabilir ve gerçek tüketiminizi belirledikten sonra daha fazla Eager kuralına geçiş yapabilirsiniz. Zaman zaman hız limitlerinin hassas olması, ayarlamış olduğunuz limitlerle oynadığınızı ve tam olarak ne yapmak istediğinizi gösterir. 

6. Trafik modelinizi anlamak için izlemeyi kullanın. bu sayede, gün veya hafta boyunca aktarım hızını dinamik olarak ayarlama ihtiyacını göz önünde bulundurmanız yeterlidir. 

7. Gereken sayıda kapsayıcı ve veritabanından daha fazla kaynak sağladığınızdan emin olmak için, sağlanan ve tüketilen üretilen iş oranını düzenli olarak izleyin. Daha fazla sağlanmış verimlilik olması iyi bir güvenlik denetimi olur.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Sağlanan aktarım hızını iyileştirmek için en iyi uygulamalar 

Aşağıdaki adımlar Azure Cosmos DB kullanırken çözümlerinizi yüksek düzeyde ölçeklenebilir ve ekonomik hale getirmenize yardımcı olur.  

1. Kapsayıcılar ve veritabanları genelinde sağlanan aktarım hızına önemli ölçüde sahipseniz, RUs ile sağlanan ru 'ları gözden geçirmeniz ve iş yüklerini ince ayar yapmanız gerekir.  

2. Uygulamanız için gerekli olan ayrılmış aktarım hızı miktarını tahmin etmek için bir yöntem, tipik işlemleri çalıştıran, uygulamanız tarafından kullanılan bir Azure Cosmos kapsayıcısına veya veritabanına karşı normal işlemlerle ilişkili istek birimi RU ücreti kaydetme yöntemidir. ardından, her saniye gerçekleştirmeyi tahmin ettiğiniz işlem sayısını tahmin edin. Tipik sorguları ve bunların kullanımlarını de ölçdiğinizden ve dahil ettiğinizden emin olun. Program aracılığıyla veya Portal kullanarak sorguların RU maliyetlerini nasıl tahmin edebileceğiniz hakkında bilgi edinmek için bkz. [sorguların maliyetini En Iyi duruma getirme](online-backup-and-restore.md). 

3. İşlem/süre ve istek ücreti dökümünü sunan Azure Izleyici günlüklerini etkinleştirerek, işlemleri ve bunların maliyetlerini bir diğer şekilde almanın bir başka yolu da vardır. Azure Cosmos DB her işlem için istek ücreti sağlar, bu nedenle her işlem ücreti yanıttan geri depolanabilir ve daha sonra analiz için kullanılır. 

4. İş yükünüzün gereksinimlerine uyum sağlamak için gereken üretilen iş verimini esnek ölçeği artırma ve azaltma sağlayabilirsiniz. 

5. İhtiyacınız olduğu için Azure Cosmos hesabınızla ilişkili bölgeler ekleyebilir ve kaldırabilirsiniz. 

6. Kapsayıcılarınızın mantıksal bölümlerinde verilerin ve iş yüklerinin eşit bir şekilde dağıtılmasını sağlayın. Düzensiz bölüm dağıtımı varsa, bu, gereken değerden daha yüksek miktarda aktarım hızı sağlamaya neden olabilir. Çarpıtılmış bir dağıtıma sahip olup olmadığını belirlerseniz, iş yükünü bölümler arasında eşit olarak yeniden dağıtmalarını veya verileri yeniden bölümlendirmenizi öneririz. 

7. Çok sayıda kapsayıcınız varsa ve bu kapsayıcılar SLA 'Lar gerektirmiyorsa, veritabanı tabanlı teklifi kapsayıcı üretilen iş başına SLA 'ların uygulanamadığı durumlar için kullanabilirsiniz. Veritabanı düzeyinde aktarım hızı için hangi Azure Cosmos kapsayıcılarından hangilerinin geçiş yapmak istediğinizi belirlemeniz ve ardından bunları bir değişiklik akışı tabanlı çözüm kullanarak geçirmeniz gerekir. 

8. "Cosmos DB ücretsiz katmanı" (bir yıl boyunca ücretsiz) kullanmayı düşünün, geliştirme ve test senaryoları için Cosmos DB (en fazla üç bölgeye kadar) veya indirilebilir Cosmos DB öykünücüyü deneyin. Test-dev için bu seçenekleri kullanarak maliyetlerinizi önemli ölçüde düşürebilirsiniz.  

9. Daha fazla iş yüküne özgü maliyet iyileştirmeleri gerçekleştirebilirsiniz. Örneğin, toplu iş boyutu, Yük Dengeleme okuma işlemlerini birden çok bölgede artırma ve varsa verileri çoğaltma.

10. Azure Cosmos DB ayrılmış kapasite sayesinde, üç yıl boyunca %65 ' e kadar önemli iskontolar elde edebilirsiniz. Azure Cosmos DB ayrılmış kapasite modeli, zaman içinde gereken istek birimleri hakkında ön taahhüt niteliğinde bir taahhütdir. İskontolar, daha uzun bir süre boyunca kullandığınız istek birimleri arttıkça, indiriminiz daha fazla olacaktır. Bu indirimler hemen uygulanır. Sağlanan değerlerinizin üzerinde kullanılan tüm ru 'lar, ayrılmamış kapasite maliyetine göre ücretlendirilir. Daha fazla ayrıntı için bkz. [Cosmos DB ayrılmış kapasite](cosmos-db-reserved-capacity.md)). Sağlanan verimlilik maliyetlerinizi daha düşük bir düzeye düşürmek için ayrılmış kapasite satın almayı düşünün.  

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra, aşağıdaki makalelerle Azure Cosmos DB maliyet iyileştirmesi hakkında daha fazla bilgi edinebilirsiniz:

* [Geliştirme ve test Için iyileştirme](optimize-dev-test.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB Faturanızı Anlama](understand-your-bill.md) hakkında daha fazla bilgi edinin
* [Depolama maliyetini iyileştirme](optimize-cost-storage.md) hakkında daha fazla bilgi edinin
* [Okuma ve yazma maliyetlerini iyileştirme](optimize-cost-reads-writes.md) hakkında daha fazla bilgi edinin
* [Sorguların maliyetini En Iyi duruma getirme](optimize-cost-queries.md) hakkında daha fazla bilgi edinin
* [Çok bölgeli Azure Cosmos hesaplarının maliyetini En Iyi duruma getirme](optimize-cost-regions.md) hakkında daha fazla bilgi edinin

