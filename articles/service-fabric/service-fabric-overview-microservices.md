---
title: Azure üzerinde mikro hizmetlere giriş
description: Mikro hizmet yaklaşımına sahip bulut uygulamalarının neden oluşturulmasına ilişkin genel bakış, modern uygulama geliştirme ve Azure Service Fabric bunun için bir platform sağlar.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 46d4be7321057bd7a5ee19e0aca136c8c60d43bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044439"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Uygulamaları oluşturmak için neden mikro hizmetler yaklaşımı kullanılmalıdır

Yazılım geliştiricileri için bir uygulamayı bileşen bölümlerine düzenleme, hiçbir şey değildir. Genellikle, bir arka uç deposu, orta katman iş mantığı ve bir ön uç Kullanıcı arabirimi (UI) ile katmanlı bir yaklaşım kullanılır. Son birkaç *yılda ne kadar değişiklik yapılır, geliştiricilerin* bulut için dağıtılmış uygulamalar oluşturuyor olması.

İşte bazı değişen iş ihtiyaçları:

* Yeni coğrafi bölgelerdeki müşterilere ulaşmak için uygun ölçekte oluşturulup çalıştırılan bir hizmet.
* Çevik bir şekilde müşteri taleplerine yanıt vermeye yönelik özelliklerin ve yeteneklerin daha hızlı teslimi.
* Maliyetleri azaltmak için geliştirilmiş kaynak kullanımı.

Bu iş ihtiyaçları, uygulama oluşturma *şeklini* etkiliyor.

Mikro hizmetlere yönelik Azure yaklaşımı hakkında daha fazla bilgi için bkz. [mikro hizmetler: bulut tarafından desteklenen bir uygulama devrimi](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Tek parçalı ve mikro hizmetler tasarım yaklaşımı

Uygulamalar zaman içinde gelişdi. Başarılı uygulamalar, kişilere faydalı olacak şekilde geliştikçe. Başarısız uygulamalar geliştirmeyin ve sonunda kullanım dışı bırakılmıştır. Soru şu şekildedir: Şu anda gereksinimlerinize ne kadar haberdar olabilirsiniz ve gelecekte neler olacak? Örneğin, şirketinizdeki bir departman için bir raporlama uygulaması oluşturduğunuzu varsayalım. Uygulamanın yalnızca şirketinizin kapsamında geçerli olduğundan ve raporların uzun tutulmayacağından emin olursunuz. Farklı bir deyişle, her ne kadar milyonlarca müşteriye video içeriği sunan bir hizmet derleyebileceğiniz yaklaşımdan farklı olacaktır.

Bazen, bir kavram kanıtı olarak kapıdan bir şeyin alınması, itici faktördür. Uygulamanın daha sonra yeniden tasarlanması gerektiğini bilirsiniz. Daha fazla kullanılmayan bir şeyi aşırı bir noktada ele alır. Diğer taraftan, şirketler bulut için derleme yaparken beklentide büyüme ve kullanım kullanılır. Büyüme ve ölçek öngörülemez. Daha hızlı bir şekilde prototip eklemek istiyoruz. bu da, gelecekteki başarıyı işleyebildiğimiz bir yolda olduğunu bilmektir. Bu, yalın başlatma yaklaşımına sahiptir: derleme, ölçme, öğrenme ve yineleme.

İstemci/sunucu dönemi boyunca, her katmandaki belirli teknolojileri kullanarak katmanlı uygulamalar oluşturmaya odaklanıyoruz. *Tek parçalı* uygulama terimi, bu yaklaşımların tanımlanmasında ortaya çıktı. Katmanlar arasında yer alan arabirimler ve her katmandaki bileşenler arasında daha sıkı bir şekilde bağlanmış tasarım kullanılmıştır. Kitaplıklarda derlenen ve birkaç yürütülebilir dosya ve DLL 'de birbirine bağlanmış olan geliştiricilere tasarlanan geliştiriciler.

Tek parçalı tasarım yaklaşımının avantajları vardır. Tek parçalı uygulamalar genellikle tasarım için daha basittir ve bu çağrılar genellikle işlemler arası iletişim (IPC) üzerinde olduğundan bileşenler arasındaki çağrılar daha hızlıdır. Ayrıca herkes tek bir ürünü sınar ve bu da insan kaynakları 'nın daha verimli bir şekilde kullanılmasını sağlar. Altkenar, katmanlı katmanlar arasında sıkı bir bağ olduğundan tek tek bileşenleri ölçeklendiremez. Düzeltmeler veya yükseltmeler yapmanız gerekiyorsa başkalarının testlerini bitirmesini beklemeniz gerekir. Çevik olması daha zordur.

Mikro hizmetler bu yüzleri ele alarak önceki iş gereksinimleriyle daha yakından hizalanır. Ancak bunların avantajları ve yükümlüler de vardır. Mikro hizmetlerin avantajları, her birinin genellikle daha basit iş işlevlerini kapsüller, bu da bağımsız olarak, test, dağıtım ve yönetim özelliklerini birbirinden bağımsız olarak kapsar. Mikro hizmetler yaklaşımının önemli bir avantajı, ekiplerin teknolojiden daha fazla iş senaryosu tarafından daha fazla çalıştırılmalarıdır. Daha küçük takımlar, bir müşteri senaryosuna dayalı bir mikro hizmet geliştirir ve kullanmak istedikleri teknolojileri kullanır.

Diğer bir deyişle, kuruluşun mikro hizmet uygulamalarını sürdürmek için teknik standartlaştırma gereksinimi yoktur. Hizmet sahibi olan bireysel ekipler, ekip uzmanlığına göre veya sorunu çözmek için en uygun olan özellikler temelinde ne kadar anlamlı olduğunu yapabilir. Uygulamada, belirli bir NoSQL Mağazası veya Web uygulaması çerçevesi gibi önerilen teknolojiler kümesi tercih edilir.

Mikro hizmetlerin alt tarafı, daha fazla farklı varlık yönetmeniz ve daha karmaşık dağıtımlar ve sürüm oluşturma ile uğraşmanız gerekir. Mikro hizmetler arasındaki ağ trafiği, karşılık gelen ağ gecikmelerinin olduğu gibi artar. Çok sayıda geveze, ayrıntılı hizmet bir performansa neden olabilir. Bu bağımlılıkları görüntülemenize yardımcı olan araçlar olmadan sistemin tamamını görmek zordur.

Standartlar, mikro hizmet yaklaşımını, yalnızca bir hizmette bulunan ve rigıd sözleşmeleri yerine yalnızca ihtiyaç duyduğunuz şeyleri nasıl iletdiğinizi ve tolerans yapmayı belirleyerek çalışır. Hizmetler birbirinden bağımsız olarak güncelleştiğinden, bu sözleşmelerin tasarımın önüne tanımlanması önemlidir. Mikro hizmet yaklaşımı ile tasarlamaya yönelik başka bir açıklama, "ayrıntılı hizmet odaklı mimari (SOA)" dir.

***En basit, mikro hizmetler tasarımı yaklaşımı, iletişim için her biri ve üzerinde anlaşılan her bir hizmet için bağımsız değişiklikler içeren bir hizmet Federasyonu ile ilgilidir.***

Daha fazla bulut uygulaması üretildiğinde, kullanıcılar genel uygulamanın bu ayrışmına bağımsız olarak, senaryoya odaklanmış hizmetlerden daha uzun süreli bir yaklaşım olduğunu tespit edilmiştir.

## <a name="comparison-between-application-development-approaches"></a>Uygulama geliştirme yaklaşımları arasındaki karşılaştırma

![Service Fabric Platform uygulaması geliştirme][Image1]

1) Tek parçalı bir uygulama, etki alanına özgü işlevselliği içerir ve normalde Web, iş ve veri gibi işlevsel katmanlara bölünmüştür.

2) Tek parçalı bir uygulamayı birden çok sunucu/sanal makine/kapsayıcı üzerinde kopyalayarak ölçeklendirebilirsiniz.

3) Mikro hizmet uygulaması işlevselliği ayrı bir şekilde daha küçük hizmetlere ayırır.

4) Mikro hizmetler yaklaşımı, her bir hizmeti ayrı ayrı dağıtarak, sunucular/sanal makineler/kapsayıcılar arasında bu hizmetlerin örneklerini oluşturarak ölçeği ölçeklendirir.

Mikro hizmetler yaklaşımı ile tasarlamak tüm projelere uygun değildir, ancak daha önce açıklanan iş hedefleri ile daha yakından hizalanır. Tek parçalı bir yaklaşımla başlamak, daha sonra kodu daha sonra mikro hizmetler tasarımına yeniden çalışma fırsatına sahip olduğunuzu biliyorsanız anlamlı olabilir. Daha yaygın olarak, tek parçalı bir uygulamayla başlayıp daha ölçeklenebilir veya çevik olması gereken işlevsel alanlarla başlayarak bu uygulamayı yavaş bir şekilde kesebilirsiniz.

Mikro hizmetler yaklaşımını kullandığınızda, uygulamanızı birçok küçük hizmet için oluşturursunuz. Bu hizmetler, bir makine kümesi arasında dağıtılan kapsayıcılar içinde çalışır. Daha küçük takımlar, bir senaryoya odaklanan ve bağımsız olarak test, sürüm, dağıtım ve her bir hizmeti ölçeklendirerek tüm uygulamanın gelişmesine olanak tanıyan bir hizmet geliştirir.

## <a name="what-is-a-microservice"></a>Mikro hizmet nedir?

Mikro hizmetlerin farklı tanımları vardır. Ancak bu mikro hizmet özelliklerinin çoğu yaygın olarak kabul edilir:

* Müşteri veya iş senaryosunu yalıt. Hangi sorunu çözmenize?
* Küçük bir mühendislik ekibi tarafından geliştirilmiştir.
* Herhangi bir Framework kullanılarak herhangi bir programlama dilinde yazılır.
* Kodu ve isteğe bağlı olarak, her ikisi de bağımsız olarak sürümlü, dağıtılmış ve ölçeklenen durum oluşur.
* İyi tanımlanmış arabirimler ve protokoller üzerinden diğer mikro hizmetlerle etkileşime geçin.
* Konumlarını çözümlemek için kullanılan benzersiz adlara (URL 'Ler) sahiptir.
* Hatalarda tutarlı ve kullanılabilir durumda kalır.

Şunları toplamak için:

***Mikro hizmet uygulamaları, iyi tanımlanmış arabirimler sayesinde standart protokoller üzerinden birbirleriyle iletişim kuran küçük, bağımsız sürümlü ve ölçeklenebilir müşteri odaklı hizmetlerden oluşur.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Herhangi bir Framework kullanılarak herhangi bir programlama dilinde yazılmıştır

Geliştiriciler olarak, becerilerimize ve oluşturmakta olduğumuz hizmetin gereksinimlerine bağlı olarak bir dil veya çerçeve seçmek için ücretsiz olmak istiyoruz. Bazı hizmetlerde, C++ ' ın performans avantajlarından başka bir şeyin üzerinde değer sağlayabilirsiniz. Diğer bir deyişle, C# veya Java 'dan aldığınız yönetilen geliştirme kolaylığı daha önemli olabilir. Bazı durumlarda, hizmeti istemcilere göstermek için belirli bir iş ortağı kitaplığı, veri depolama teknolojisini veya yöntemini kullanmanız gerekebilir.

Bir teknoloji seçtikten sonra, hizmetin işletimsel veya yaşam döngüsü yönetimini ve ölçeğini göz önünde bulundurmanız gerekir.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Kodun ve durumun bağımsız olarak sürümlü, dağıtılmış ve ölçeklendirilmesine olanak tanır

Mikro hizmetlerinizi nasıl yazacağınız, kodun ve isteğe bağlı olarak durumun bağımsız olarak dağıtılması, yükseltilmesi ve ölçeklendirilmesi gerekir. Bu sorun, kendi tercih ettiğiniz teknolojilere doğru geldiği için çözülebileceğinden zor. Ölçeklendirme için, hem kodun hem de durumun zor olduğunu anlamak için. Kod ve durum bugün yaygın olarak kullanılan farklı teknolojiler kullanırken, mikro hizmetinizin dağıtım betiklerinin her ikisini de ölçeklendirmeleri gerekir. Bu ayrım, çevikliği ve esnekliği de sağlar; bu sayede tüm mikro hizmetlerden bazılarını aynı anda yükseltmek zorunda kalmadan yükseltebilirsiniz.

Tek parçalı ve mikro hizmet yaklaşımlarımızın bir süre karşılaştırılmıza geri dönelim. Bu diyagramda durum depolama yaklaşımının farkları gösterilmektedir:

#### <a name="state-storage-for-the-two-approaches"></a>İki yaklaşımdan oluşan durum depolaması

![Service Fabric platform durumu depolaması][Image2]

***Sol taraftaki tek parçalı yaklaşım, belirli teknolojilerin tek bir veritabanına ve katmanlarına sahiptir.***

***Sağdaki mikro hizmetler yaklaşımı, durumun genellikle mikro hizmet kapsamındaki ve çeşitli teknolojilerin kullanıldığı, birbirine bağlı mikro hizmetler grafiğine sahiptir.***

Tek parçalı bir yaklaşımda, uygulama genellikle tek bir veritabanı kullanır. Bir veritabanını kullanmanın avantajı tek bir konumda olduğundan, dağıtımını kolaylaştırır. Her bileşenin durumunu depolamak için tek bir tablosu olabilir. Ekipler, bir güçlük olan, tamamen ayrı bir durum olmalıdır. Kaçınılmaz, birisi mevcut bir müşteri tablosuna bir sütun eklemek, tablolar arasında bir birleşme ve depolama katmanında bağımlılıklar oluşturmak için kullanılır. Bu durumda, ayrı bileşenleri ölçeklendiremez.

Mikro hizmetler yaklaşımında her hizmet kendi durumunu yönetir ve depolar. Her hizmet, hizmet taleplerini karşılamak için hem kodu hem de durumu ölçeklendirmekten sorumludur. Daha aşağı bir örnek, uygulamanızın verilerinin görünümlerini veya sorgularını oluşturmanız gerektiğinde, birden çok durum mağazasında sorgulama yapmanız gerekir. Bu sorun genellikle, mikro hizmetler koleksiyonu genelinde bir görünüm oluşturan ayrı bir mikro hizmet tarafından çözülebilir. Veriler üzerinde birden çok ıpromptu sorgusu çalıştırmanız gerekiyorsa, her mikro hizmetin verisini çevrimdışı analiz için bir veri ambarı hizmetine yazmayı düşünmelisiniz.

Mikro hizmetler sürümlüdür. Mikro hizmetin farklı sürümlerinin yan yana çalışması mümkündür. Mikro hizmetin daha yeni bir sürümü yükseltme sırasında başarısız olabilir ve önceki bir sürüme geri alınması gerekir. Sürüm oluşturma işlemi, farklı kullanıcıların hizmetin farklı sürümlerini deneymesinden bir/B testi için de yardımcı olur. Örneğin, belirli bir müşteri kümesi için mikro hizmeti yükseltmek yaygın bir şekilde, daha yaygın bir şekilde kullanıma sunulmadan önce yeni işlevleri test etmek için ortaktır.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>İyi tanımlanmış arabirimler ve protokoller üzerinden diğer mikro hizmetlerle etkileşime girer

Son 10 yılda, hizmet odaklı mimarilerde iletişim desenlerini açıklayan kapsamlı bilgiler yayımlandı. Genellikle, hizmet iletişimi HTTP ve TCP protokolleri ve XML veya JSON ile serileştirme biçimi olarak bir REST yaklaşımı kullanır. Bir arabirim perspektifinden, web tasarımı yaklaşımını almak yaklaşık olur. Ancak hiçbir şey, ikili protokolleri veya kendi veri biçimlerinizi kullanmanızı durdurmamalıdır. Bu protokoller ve biçimler birlikte yoksa, kullanıcıların mikro hizmetlerinizi kullanarak daha zor bir zamana sahip olacağını unutmayın.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Konumunu çözümlemek için kullanılan benzersiz bir ada (URL) sahiptir

Mikro hizmetinizin çalıştığı her yerde adreslenebilir olması gerekir. Makineler hakkında bilgi alırsanız ve bunlardan biri belirli bir mikro hizmeti çalıştırıyorsa, bu şeyler hızla sorunsuz bir şekilde çalışabilir.

DNS 'nin belirli bir makineye belirli bir URL 'yi çözümlediği şekilde, mikro hizmetinizin geçerli konumunun bulunabilir olması için benzersiz bir adı olması gerekir. Mikro hizmetler üzerinde çalıştıkları altyapıdan bağımsız olarak adreslenebilir adlara sahip olmalıdır. Bu, hizmet kayıt defteri olması gerektiğinden hizmetinizin nasıl dağıtıldığı ve nasıl keşfedildiği arasında bir etkileşim olduğunu gösterir. Bir makine başarısız olduğunda, kayıt defteri hizmetinin, hizmetin nereye taşındığını söylemiş olması gerekir.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Hatalarda tutarlı ve kullanılabilir durumda kalır

Beklenmedik hatalarla ilgilenirken, özellikle dağıtılmış bir sistemde çözülecek en zor sorunlardan biridir. Geliştiriciler olarak yazdığımız kodun büyük bölümü özel durumları işlemeye yöneliktir. Sınama sırasında, özel durum işlemede de en fazla zaman harcadık. İşlem, hataların işlenmesi için kod yazmaya kıyasla daha fazla yer alır. Mikro hizmetin çalıştığı makine başarısız olduğunda ne olur? Sorunu tespit etmeniz gerekir, bu sorun kendi kendine ilgili bir sorundur. Ancak mikro hizmetinizi de yeniden başlatmanız gerekir.

Kullanılabilirlik için, mikro bir hizmetin hatalara karşı dayanıklı olması ve başka bir makinede yeniden başlatılması gerekir. Bu dayanıklılık gereksinimlerine ek olarak, Verilerin kaybedilmemesi ve verilerin tutarlı kalması gerekir.

Uygulama yükseltmesi sırasında oluşan başarısızlık oluştuğunda esneklik elde etmek zordur. Dağıtım sistemiyle çalışan mikro hizmetin kurtarılması gerekmez. Daha yeni bir sürüme taşımaya veya tutarlı bir durumu korumak için önceki bir sürüme geri dönerek devam edip etmediğini belirlemesi gerekir. İleri doğru ilerleyecek ve mikro hizmetin önceki sürümlerinin nasıl kurtarılacağı gibi yeterli makine olup olmadığı gibi birkaç soruyu göz önünde bulundurmanız gerekir. Bu kararları vermek için, mikro hizmetin sistem durumu bilgilerini yaymalıdır.

### <a name="reports-health-and-diagnostics"></a>Raporlar sistem durumu ve tanılama

Açık görünebilir ve genellikle çok daha fazla görünür, ancak mikro bir hizmetin sistem durumunu ve tanılamayı raporlamaları gerekir. Aksi takdirde, bir işlem perspektifinden sistem durumu hakkında daha fazla bilgi sahibi olursunuz. Bir bağımsız hizmet kümesi genelinde Tanılama olaylarını bağıntılandırın ve olay sırasını anlamlı hale getirmek için makine saati eğimiyle ilgilenirken, zor olur. Kabul edilen protokoller ve veri biçimleri üzerinde, bir mikro hizmetle etkileşimde bulunmanızın yanı sıra, sorgulama ve görüntüleme amacıyla bir olay deposunda nihai sistem durumu ve tanılama olaylarının nasıl günlüğe alınacağını standartlaştırmanız gerekir. Mikro hizmetler yaklaşımıyla, farklı takımların tek bir günlük biçiminde kabul etmesi gerekir. Tanılama olaylarını uygulamada bir bütün olarak görüntülemek için tutarlı bir yaklaşım olması gerekir.

Sağlık, tanılamalardan farklıdır. Sistem durumu, uygun işlemleri gerçekleştirmek için geçerli durumunu raporlayan mikro hizmet ile ilgilidir. İyi bir örnek, kullanılabilirliği sürdürmek için yükseltme ve dağıtım mekanizmalarıyla birlikte çalışıyor. Bir hizmet, işlem kilitlenmesi veya makinenin yeniden başlatılması nedeniyle şu anda sağlıksız olabilir, ancak hizmet hala çalışıyor olabilir. İhtiyacınız olan son şey, bir yükseltme başlatarak durumu daha kötüleşmelidir. En iyi yaklaşım, mikro hizmetin kurtarılması için önce araştırmanız ya da zaman için zaman vermaktır. Mikro bir hizmetten gelen sistem durumu olayları bilinçli kararlar almanıza ve etkili bir şekilde kendi kendine düzeltme hizmetleri oluşturmaya yardımcı olur.

## <a name="guidance-for-designing-microservices-on-azure"></a>Azure 'da mikro hizmetler tasarlama Kılavuzu

[Azure 'da mikro hizmetler tasarlama ve oluşturma](/azure/architecture/microservices/)hakkında rehberlik için Azure mimari merkezini ziyaret edin.

## <a name="service-fabric-as-a-microservices-platform"></a>Mikro hizmet platformu olarak Service Fabric

Azure Service Fabric, Microsoft, genellikle tek parçalı olarak hizmet sunmaya yönelik paketlenmiş ürünleri sunmaya çalıştığında ortaya çıktı. Azure SQL veritabanı ve Azure Cosmos DB gibi büyük hizmetleri oluşturma ve çalıştırma deneyimi (şekillendirilmiş Service Fabric). Platform, daha fazla hizmet benimsediği zaman içinde gelişmiştir. Service Fabric yalnızca Azure 'da değil, tek başına Windows Server dağıtımlarında de çalıştırdık.

***Service Fabric amacı, bir hizmet oluşturma ve çalıştırma ve altyapı kaynaklarını verimli bir şekilde kullanma ile ilgili sorunları çözmektir. bu sayede takımlar, mikro hizmetler yaklaşımını kullanarak iş sorunlarını çözebilir.***

Bu kısa videoda Service Fabric ve mikro hizmetler tanıtılır:
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

Service Fabric, mikro hizmetler yaklaşımını kullanan uygulamalar oluşturmanıza yardımcı olur:

* Başarısız hizmetleri dağıtmak, yükseltmek, algılamak ve yeniden başlatmak, hizmetleri bulmak, iletileri yönlendirmek, durumu yönetmek ve sistem durumunu izlemek için sistem hizmetleri sağlayan bir platform.
* Kapsayıcılarda veya işlem olarak çalışan uygulamalar dağıtma özelliği. Bir kapsayıcı ve işlem Orchestrator Service Fabric.
* Mikro hizmetler olarak uygulama oluşturmanıza yardımcı olmak için üretken programlama API 'Leri: [ASP.NET Core, Reliable Actors ve Reliable Services](service-fabric-choose-framework.md). Örneğin, sistem durumu ve tanılama bilgilerini alabilir veya yerleşik yüksek kullanılabilirlik özelliğinden yararlanabilirsiniz.

***Service Fabric, hizmetinizi nasıl derlemenize ilişkin bağımsız olarak, herhangi bir teknolojiyi de kullanabilirsiniz. Ancak, mikro hizmetler oluşturmayı kolaylaştıran yerleşik programlama API 'Leri sağlar.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Mevcut uygulamaları Service Fabric geçirme

Service Fabric, var olan kodu yeniden kullanmanıza ve yeni mikro hizmetlerle modernleştirin izin verir. Uygulama modernleştirmesi için beş aşama vardır ve herhangi bir aşamada başlatabilir ve durdurabilirsiniz. Aşamalar şunlardır:

1) Geleneksel bir tek parçalı uygulama ile başlayın.  
2) Geçiremezsiniz. Mevcut kodu Service Fabric barındırmak için kapsayıcıları veya Konuk yürütülebilir dosyalarını kullanın.  
3) Modernleştirin. Varolan Kapsayıcılı kodun yanı sıra yeni mikro hizmetler ekleyin.  
4) Yenilik yapın. Tek parçalı uygulamayı, ihtiyaca göre mikro hizmetlere bölün.  
5) Uygulamaları mikro hizmetlere dönüştürün. Var olan tek parçalı uygulamaları dönüştürün veya yeni bir doğa alanı uygulamaları oluşturun.

![Mikro hizmetlere geçiş][Image3]

Bunu unutmayın, *Bu aşamaların herhangi birini başlatabilir ve durdurabilirsiniz*. Sonraki aşamada ilerlemeniz gerekmez. 

Bu aşamaların her biri için örneklere bakalım.

**Geçiş**  
İki nedenden dolayı birçok şirket, mevcut tek parçalı uygulamaları kapsayıcılara geçirmektedir:

* Mevcut donanımların birleştirilmesi ve kaldırılması nedeniyle ya da daha yüksek yoğunlukta çalışan uygulamalar nedeniyle maliyet indirimi.
* Geliştirme ve işlemler arasında tutarlı bir dağıtım sözleşmesi.

Maliyet indirimleri basittir. Microsoft 'ta, birçok mevcut uygulama Kapsayıcılı, tasarruflarda milyonlarca dolar ile başa alınır. Tutarlı dağıtım değerlendirmek zordur, ancak eşit derecede önemlidir. Bu, geliştiricilerin bunlara uyan teknolojileri seçebildiği anlamına gelir ancak işlemler, uygulamaları dağıtmak ve yönetmek için yalnızca tek bir yöntem kabul eder. BT, geliştiricilerin yalnızca belirli olanları seçmesini zorlamadan farklı teknolojileri desteklemeye yönelik karmaşıklıktan uğraşmak konuma almayı azaltır. Temelde, her uygulama kendine dahil edilen dağıtım görüntülerinde Kapsayıcılı hale getirilir.

Birçok kuruluş burada durur. Kapsayıcıların avantajları zaten vardır ve Service Fabric dağıtım, yükseltmeler, sürüm oluşturma, geri alma ve sistem durumu izleme dahil olmak üzere tüm yönetim deneyimini sağlar.

**Modernleştirin**  
Modernleştirme, varolan Kapsayıcılı kodun yanı sıra yeni hizmetlerin eklenmesiyle birlikte bulunur. Yeni kod yazacaksanız, mikro hizmetler yolunda küçük bir adım daha almak en iyisidir. Bu, yeni bir REST API uç noktası veya yeni iş mantığı eklemek anlamına gelebilir. Bu şekilde, yeni mikro hizmetler oluşturma ve bunları geliştirme ve dağıtma işlemlerini başlatın.

**Yenilik yapma**  
Mikro hizmet yaklaşımı, değişen iş ihtiyaçlarını karşılar. Bu aşamada, tek parçalı uygulamayı hizmetlere bölmeye veya sürmek. Burada klasik bir örnek, bir iş akışı kuyruğu olarak kullandığınız bir veritabanının bir işleme sorunu olması durumunda meydana gelir. İş akışı isteklerinin sayısı arttıkça, çalışmanın ölçek için dağıtılması gerekir. Ölçeklendirmeyen veya daha sık güncelleştirilmesi gereken uygulamanın bu özel parçasını alın ve mikro hizmet ve yenilik yapın olarak bölmeniz gerekir.

**Uygulamaları mikro hizmetlere dönüştürme**  
Bu aşamada, uygulamanız mikro hizmetlerden tamamen oluşur (veya ' den ayrılır). Bu noktaya ulaşmak için mikro hizmet yolculuğuna ulaştınız. Buradan başlayabilir, ancak önemli bir yatırım yapmanıza yardımcı olması için mikro hizmetler platformu olmadan bunu yapabilirsiniz.

### <a name="are-microservices-right-for-my-application"></a>Uygulamam için mikro hizmetler doğru mu?

Belki. Microsoft 'ta, daha fazla takım iş açısından bulut açısından daha fazla ekibe başladığı için, mikro hizmet benzeri bir yaklaşım alma avantajlarından birçoğuna neden olmuş olabilir. Örneğin, Bing, yıl boyunca mikro Hizmetleri kullanıyor. Diğer takımlar için, mikro hizmetler yaklaşımı yenidir. Takımlar, temel şiddetini dışında çözülecekleri zor sorunlar olduğunu buldu. Bu neden Service Fabric hizmet oluşturma teknolojisi olarak gelişteledir.

Service Fabric amacı, mikro hizmet uygulamalarının oluşturulmasına ilişkin karmaşıklıkları azaltmaktır. böylece, maliyeti yüksek yeniden tasarımlara kadar ilerlemelidir. Kısa bir başlangıç yapın, gerektiğinde ölçeklendirin, Hizmetleri kullanımdan kaldırır, yenilerini ekleyin ve müşteri kullanımı ile geliştirin. Ayrıca, çoğu geliştirici için mikro hizmetleri daha ulaşılabilir hale getirmek üzere çözülmesi gereken birçok farklı sorun olduğunu da biliyoruz. Kapsayıcılar ve aktör programlama modeli, bu yöndeki küçük adımlara örnektir. Mikro hizmet yaklaşımını daha kolay hale getirmek için daha fazla yenilikle karşılaşacağız.

## <a name="next-steps"></a>Sonraki adımlar

* [Mikro hizmetler: bulut tarafından desteklenen bir uygulama Devrimi](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Azure Mimari Merkezi: Azure 'da mikro hizmetler oluşturma](/azure/architecture/microservices/)
* [Azure Service Fabric uygulaması ve kümesi için en iyi yöntemler](./service-fabric-best-practices-security.md)
* [Service Fabric terimlere genel bakış](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png