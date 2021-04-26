---
title: Azure Cosmos DB ile küresel dağıtım
description: Bu makalede, Azure Cosmos DB genel dağıtımına ilişkin teknik ayrıntılar sunulmaktadır.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 592a9b89379094c88881c3c8485c7e38a1613b34
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219493"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Azure Cosmos DB ile küresel veri dağıtımı-
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB, Azure 'daki temel bir hizmettir, bu nedenle dünya çapında genel, bağımsız, Savunma Bakanlığı (DoD) ve kamu bulutları dahil tüm Azure bölgelerinde dağıtılır. Bir veri merkezinde, her biri ayrılmış yerel depolama alanı olan çok büyük bir makine Damgalarında Azure Cosmos DB dağıtıp yönetiyoruz. Bir veri merkezi içinde Azure Cosmos DB, her biri çok sayıda donanımı çalıştıran birçok kümeye dağıtılır. Bir küme içindeki makineler genellikle bölge içinde yüksek kullanılabilirlik için 10-20 hata etki alanı arasında yayılır. Aşağıdaki görüntüde genel dağıtım sistemi topolojisi Cosmos DB gösterilmektedir:

:::image type="content" source="./media/global-dist-under-the-hood/distributed-system-topology.png" alt-text="Sistem topolojisi" border="false":::

**Azure Cosmos DB genel dağıtım, anahtar:** Her zaman, birkaç tıklamayla veya tek bir API çağrısıyla programlama yoluyla, Cosmos veritabanızla ilişkili coğrafi bölgeleri ekleyebilir veya kaldırabilirsiniz. Cosmos veritabanı, sırasıyla bir Cosmos kapsayıcıları kümesinden oluşur. Cosmos DB, kapsayıcılar mantıksal dağıtım ve ölçeklenebilirlik birimleri olarak görev yapar. Oluşturduğunuz koleksiyonlar, tablolar ve grafikler yalnızca Cosmos kapsayıcılarıdır. Kapsayıcılar tamamen şematik ve bir sorgu için kapsam sağlar. Cosmos kapsayıcısındaki veriler, alma sırasında otomatik olarak dizinlenir. Otomatik Dizin oluşturma, kullanıcıların, özellikle de genel olarak dağıtılmış bir kurulumda şema veya dizin yönetimi kurtulur olmadan verileri sorgulamasına olanak sağlar.  

- Belirli bir bölgede, bir kapsayıcı içindeki veriler, sağladığınız ve temel alınan fiziksel bölümler (*Yerel dağıtım*) tarafından saydam olarak yönetilen bir bölüm anahtarı kullanılarak dağıtılır.  

- Her fiziksel bölüm ayrıca coğrafi bölgeler (*genel dağıtım*) genelinde çoğaltılır. 

Cosmos DB esnek kullanan bir uygulama, Cosmos kapsayıcısındaki üretilen işi ölçeklendirir veya daha fazla depolama alanı tüketir, Cosmos DB bölüm yönetimi işlemlerini (bölünmüş, kopya, silme) tüm bölgelerde saydam şekilde işler. Ölçeklendirmenin, dağıtımın veya hatalardan bağımsız olarak Cosmos DB, kapsayıcıların içindeki verilerin tek bir sistem görüntüsünü sağlamaya devam eder ve bu, herhangi bir sayıda bölgede genel olarak dağıtılır.  

Aşağıdaki görüntüde gösterildiği gibi, bir kapsayıcı içindeki veriler iki boyut boyunca dağıtılır: bir bölgedeki ve bölgeler arasında, dünya çapındaki:  

:::image type="content" source="./media/global-dist-under-the-hood/distribution-of-resource-partitions.png" alt-text="fiziksel bölümler" border="false":::

Fiziksel bir bölüm, *çoğaltma kümesi* olarak adlandırılan bir çoğaltmalar grubu tarafından uygulanır. Her makine, yukarıdaki görüntüde gösterildiği gibi, sabit bir işlem kümesi içindeki çeşitli fiziksel bölümlere karşılık gelen yüzlerce çoğaltma barındırır. Fiziksel bölümlere karşılık gelen çoğaltmalar, bir bölgedeki makineler ve bir bölgedeki veri merkezleri arasında dinamik olarak yerleştirildiğinden ve yük dengelemesi yapılır.  

Bir çoğaltma, Azure Cosmos DB kiracıya benzersiz olarak aittir. Her çoğaltma, Cosmos DB [veritabanı altyapısının](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)bir örneğini barındırır ve bu da kaynakları ve ilişkili dizinleri yönetir. Cosmos veritabanı altyapısı, bir atom kayıt sırası (ARS) tabanlı tür sistemi üzerinde çalışır. Motor, bir şemanın kavramına belirsiz bir şekilde, kayıtların yapısı ve örnek değerleri arasındaki sınırı bulanıklaştırma. Cosmos DB, kullanıcıların, şema veya dizin yönetimiyle uğraşmak zorunda kalmadan küresel olarak dağıtılan verileri sorgulamasına olanak tanıyan, her şeyi verimli bir şekilde otomatik olarak dizinleyerek tam Şemasız bir konuyla karşılaşarak ulaşır.

Cosmos veritabanı altyapısı, çeşitli koordinasyon temelleri, dil çalışma zamanları, sorgu işlemcisi ve işlem depolamadan ve veri dizinlemeden sorumlu depolama ve dizin oluşturma alt sistemlerinin uygulanması dahil bileşenlerden oluşur. Dayanıklılık ve yüksek kullanılabilirlik sağlamak için veritabanı altyapısı SSD 'lerde verilerini ve dizinini sürdürür ve bunları sırasıyla çoğaltma kümesi içindeki veritabanı altyapısı örnekleri arasında çoğaltır. Daha büyük kiracılar, üretilen iş ve depolama alanı ölçeğinde ve daha büyük ya da daha fazla kopyaya veya her ikisine sahip olur. Sistemin her bileşeni tam olarak zaman uyumsuz – hiçbir iş parçacığı blok değildir ve her iş parçacığı gereksiz iş parçacığı anahtarlarını oluşturmadan kısa süreli çalışmalardır. Hız sınırlama ve arka basınç, giriş denetiminden tüm g/ç yollarına kadar tüm yığınta kullanıma alınır. Cosmos veritabanı altyapısı, ayrıntılı eşzamanlılık yararlanmak ve yüksek verimlilik sağlamak için tasarlanmıştır.

Cosmos DB genel dağıtımı, *çoğaltma-kümeler* ve *bölüm kümeleri* olmak üzere iki anahtar soyutlamalarını kullanır. Çoğaltma kümesi, bir düzenleme için modüler bir LEGO blok ve bölüm kümesi bir veya daha fazla coğrafi olarak dağıtılmış fiziksel bölümün dinamik bir yerleridir. Genel dağıtımın nasıl çalıştığını anlamak için, bu iki temel soyutlamayı anladık. 

## <a name="replica-sets"></a>Çoğaltma-ayarlar

Fiziksel bir bölüm, çoğaltma kümesi olarak adlandırılan birden çok hata etki alanına yayılan, kendinden yönetilen ve dinamik olarak yük dengeli bir çoğaltmalar grubu olarak gerçekleştirilmiş olur. Bu küme, fiziksel bölümdeki verileri yüksek oranda kullanılabilir, dayanıklı ve tutarlı hale getirmek için, çoğaltılan durum makinesi protokolünü topluca uygular. Çoğaltma kümesi üyeliği *N* dinamiktir; hatalara, yönetim işlemlerine ve başarısız çoğaltmaların yeniden oluşturulması/kurtarılması için geçen süre temelinde *nMin* ve *NMAX* arasında dalgalanmaya devam eder. Üyelik değişiklikleri temelinde, çoğaltma protokolü de okuma ve yazma çekirdeklerine boyutunu yeniden yapılandırır. Belirli bir fiziksel bölüme atanan aktarım hızını tek tek dağıtmak için iki fikir sunuyoruz: 

- İlk olarak, lider üzerindeki yazma isteklerinin işlenme maliyeti, güncelleştirmeleri uygulama maliyetinden uygulamanın maliyetinden daha yüksektir. Bu, lider, izleyicileri kıyasla daha fazla sistem kaynağı olduğunu anlatıyor. 

- İkinci olarak, mümkün olduğu kadar, belirli bir tutarlılık düzeyi için okuma çekirdeği, yalnızca izleyici Çoğaltmalarından oluşur. Gerekli olmadığı takdirde okuma sunma lideri ile iletişim kurmaktan kaçının. Cosmos DB desteklediği [beş tutarlılık modeli](consistency-levels.md) için çekirdek tabanlı sistemlerdeki [Yük ve kapasite](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) ilişkisi üzerinde gerçekleştirilen araştırmadan çok sayıda fikir sunuyoruz.  

## <a name="partition-sets"></a>Bölüm kümeleri

Her biri Cosmos veritabanı bölgeleriyle yapılandırılmış olan bir grup fiziksel bölüm, yapılandırılan tüm bölgelerde çoğaltılan aynı anahtar kümesini yönetmek için oluşturulur. Bu daha yüksek düzenleme temel yapısına, belirli bir anahtar kümesini yöneten, coğrafi olarak dağıtılmış fiziksel bölümlerin coğrafi olarak dağıtılmış dinamik bir kaplaması olan *bölüm kümesi* adı verilir. Belirli bir fiziksel bölüm (bir çoğaltma kümesi) bir küme içinde kapsamlandırılaken, Bölüm kümesi aşağıdaki görüntüde gösterildiği gibi kümelere, veri merkezlerine ve coğrafi bölgelere yayılabilir:  

:::image type="content" source="./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png" alt-text="Bölüm kümeleri" border="false":::

Bölüm kümesini, coğrafi olarak dağınık bir "süper çoğaltma-kümesi" olarak düşünebilirsiniz, bu, aynı anahtar kümesine sahip birden fazla çoğaltma kümesinden oluşur. Bir çoğaltma kümesine benzer şekilde, Bölüm kümesinin üyeliği de dinamik olur. belirli bir bölüm kümesine/kaynağından yeni bölümler eklemek/kaldırmak (örneğin, bir kapsayıcıda bir kapsayıcıyı ölçeklendirirseniz, Cosmos veritabanınıza bölge eklemek/kaldırmak, ya da başarısızlık oluştuğunda) için örtük fiziksel bölüm yönetimi işlemlerine göre dalgalanmaktadır. Her bölümün (Bölüm kümesi), Bölüm kümesi üyeliğini kendi çoğaltma kümesi içinde yönetmesine sahip olan sanallaştırmaya göre, üyelik tamamen açık ve yüksek oranda kullanılabilir olur. Bölüm kümesini yeniden yapılandırma sırasında fiziksel bölümler arasındaki kaplamanın topolojisi de oluşturulur. Topoloji, kaynak ve hedef fiziksel bölümler arasındaki tutarlılık düzeyine, coğrafi mesafeye ve kullanılabilir ağ bant genişliğine göre dinamik olarak seçilir.  

Hizmet, Cosmos veritabanlarınızı tek bir yazma bölgesiyle veya birden fazla yazma bölgesiyle yapılandırmanıza olanak tanır ve seçime bağlı olarak, bölüm kümeleri tam olarak bir veya tüm bölgelerde yazma işlemleri için yapılandırılır. Sistem iki düzeyli, iç içe geçmiş bir konsensus protokolünü kullanır – bir düzey, yazmaları kabul eden bir fiziksel bölüm kümesinin çoğaltmaları içinde, diğeri ise bölüm kümesi içinde tüm işlenmiş yazma işlemleri için tam sıralama garantisi sağlamak üzere bir bölüm kümesi düzeyinde çalışır. Bu çok katmanlı, iç içe geçmiş yarışmalar, yüksek kullanılabilirlik için katı SLA 'larımızın uygulanması ve Cosmos DB müşterileri tarafından sunulan tutarlılık modellerinin uygulanması açısından önemlidir.  

## <a name="conflict-resolution"></a>Çakışma çözümü

Güncelleştirme yayma, çakışma çözümü ve küçük önem derecesine yönelik tasarımımız, [epidemıc algoritmalarından](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) ve [Bayou](https://people.cs.umass.edu/~mcorner/courses/691M/papers/terry.pdf) sisteminde yer alan önceki çalışmalardan sorumludur. Fikirlerin çekirdekleri ilerlediklerinde ve Cosmos DB sistem tasarımına iletişim kurmak için uygun bir başvuru çerçevesi sağlarken, bunları Cosmos DB sistemine uyguladığımız için de önemli bir dönüşüm de gerçekleştirdi. Bu işlem, önceki sistemler kaynak İdaresi ile veya Cosmos DB çalışması gereken ölçeklendirmeye veya özellikleri (örneğin, sınırlı stalet tutarlılığı) ve Cosmos DB müşterilerine sunduğu sıkı ve kapsamlı SLA 'Ları sağlamak için gereklidir.  

Bölüm kümesinin birden çok bölgeye dağıtıldığını ve verileri belirli bir bölüm kümesini kapsayan fiziksel bölümler arasında çoğaltmak için Cosmos DBs (çok bölgeli yazma) çoğaltma protokolünü geri çekin. Her fiziksel bölüm (bir bölüm kümesi), yazma işlemlerini kabul eder ve genellikle bu bölgeye yerel olan istemcilere okuma işlemleri yapar. Bir bölgedeki fiziksel bölüm tarafından kabul edilen yazma işlemleri, istemciye alınmadan önce fiziksel bölüm içinde yüksek oranda kullanılabilir hale getirilir. Bunlar belirsiz yazmalar ve bölüm kümesi içindeki diğer fiziksel bölümlere, bir entropi koruma kanalı kullanılarak dağıtılır. İstemciler, istek üst bilgisini geçirerek geçici veya kaydedilmiş yazma işlemleri yapabilir. Entropi yayılması (yayma sıklığı dahil), Bölüm kümesinin topolojisine, fiziksel bölümlerin bölgesel yakınlığının ve yapılandırılan tutarlılık düzeyinin temel alınarak dinamik bir değer. Bölüm kümesi içinde, Cosmos DB dinamik olarak seçilmiş bir Arbiter bölümü ile birincil bir kayıt düzeni izler. Arbiter seçimi dinamiktir ve, Bölüm kümesinin, kaplamanın topolojisine göre yeniden yapılandırılmasına ilişkin integral bir parçasıdır. Kaydedilmiş yazmaları (çok satırlı/toplu güncelleştirmeler dahil) sıralı olarak garanti edilir. 

Güncelleştirme çakışmalarını tespit etmek ve çözmek için, büyük ölçekli ve sürüm vektörlerine yönelik olarak, çoğaltma ve bölüm kümesinde bulunan her bir konsensus düzeyine karşılık gelen bölge kimliği ve mantıksal saatleri içeren, kodlanmış vektör saatleri (sırasıyla, bölge kümesi ve bölüm kümesi) kullanıyoruz. Topoloji ve eş seçim algoritması, sürüm vektörlerine yönelik sabit ve minimum depolama ve en az ağ yükü sağlamak üzere tasarlanmıştır. Algoritma katı yakınsama özelliğini garanti eder.  

Birden çok yazma bölgesi ile yapılandırılmış Cosmos veritabanları için, sistem geliştiricilerin arasından seçim yapmak üzere çeşitli esnek otomatik çakışma çözümleme ilkeleri sunar: 

- **Son yazma-WINS (LWW)**, varsayılan olarak, sistem tarafından tanımlanan bir zaman damgası özelliği kullanır (zaman eşitleme saati protokolüne dayanır). Cosmos DB, çakışma çözümü için kullanılacak başka bir özel sayısal Özellik belirtmenize de olanak tanır.  
- Uygulama tanımlı **(özel) çakışma çözümleme ilkesi** (birleştirme yordamları aracılığıyla ifade edilir), bu, çakışmaların uygulama tanımlı semantik mutabakatı için tasarlanmıştır. Bu yordamlar, sunucu tarafındaki bir veritabanı işleminin auspices altına yazma yazma çakışmalarını algılamada çağrılır. Sistem, taahhüt protokolünün bir parçası olarak birleştirme yordamının yürütülmesi için tam olarak bir kez sağlar. İle oynaması için kullanabileceğiniz [birkaç çakışma çözümü örneği](how-to-manage-conflicts.md) vardır.  

## <a name="consistency-models"></a>Tutarlılık modelleri

Cosmos veritabanınızı tek veya birden çok yazma bölgeleriyle yapılandırıp, iyi tanımlanmış beş tutarlılık modelinden seçim yapabilirsiniz. Birden çok yazma bölgesi ile, tutarlılık düzeylerinin bazı önemli yönleri aşağıda verilmiştir:  

Sınırlanmış Eskime durumu tutarlılığı, tüm okumaların her bölgede en son yazma işleminin *K* ön ekleri veya *T* saniye içinde olmasını güvence altına alır. Ayrıca, sınırlı stalet tutarlılığı olan okumaların tek Tonic olma garantisi ve tutarlı ön ek garantisi vardır. Entropi devre dışı bırak Protokolü hız sınırlı bir şekilde çalışır ve ön eklerin birikmesini ve yazma işlemleri için ters basınç uygulanmasını sağlar. Oturum tutarlılığı, monoton okuma, monoton yazma, kendi yazmalarınızı okuma, aşağıdaki okuma ve tutarlı ön ek garantisi yazma işlemlerini garanti eder, dünya çapındaki. Güçlü tutarlılık ile yapılandırılmış veritabanları için, bölgeler arasında zaman uyumlu çoğaltma nedeniyle birden fazla yazma bölgesinin avantajları (düşük yazma gecikmesi, yüksek yazma kullanılabilirliği) uygulanmaz.

Cosmos DB 'deki beş tutarlılık modelinin semantiği [burada](consistency-levels.md)açıklanmıştır ve [burada](https://github.com/Azure/azure-cosmos-tla)yüksek düzeyli bir TLA + belirtimleri kullanılarak matematiksel olarak açıklanacaktır.

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra aşağıdaki makaleleri kullanarak genel dağıtımı yapılandırma hakkında bilgi edinin:

* [Veritabanı hesabınızda bölge ekleme/çıkarma işlemi gerçekleştirme](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Özel bir çakışma çözümü ilkesi oluşturma](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
