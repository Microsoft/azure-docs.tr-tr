---
title: Azure Cosmos DB ile küresel dağıtım
description: Bu makalede Azure Cosmos DB genel dağıtımını ilgili teknik ayrıntılar sağlar
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: ce943fbed0774667100f6de4c60f91c0b02de6c3
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615354"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Azure Cosmos DB ile küresel veri dağıtımı-

Azure Cosmos DB, Azure 'daki temel bir hizmettir, bu nedenle dünya çapında genel, bağımsız, Savunma Bakanlığı (DoD) ve kamu bulutları dahil tüm Azure bölgelerinde dağıtılır. Bir veri merkezi içinde dağıtın ve Azure Cosmos DB, makinelerin büyük Damgalar üzerinde her ayrılmış yerel depolama ile yönetin. Bir veri merkezi içinde Azure Cosmos DB her potansiyel olarak birden fazla donanım Nesilleri çalıştıran birçok kümeler arasında dağıtılır. Bir küme içindeki makineler genellikle bölge içinde yüksek kullanılabilirlik için 10-20 hata etki alanı arasında yayılır. Cosmos DB genel dağıtım sistemin topolojisi aşağıdaki resimde gösterilmektedir:

![Sistemin topolojisi](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Azure Cosmos DB genel dağıtım, anahtar:** Her zaman, birkaç tıklamayla veya tek bir API çağrısıyla programlama yoluyla, Cosmos veritabanızla ilişkili coğrafi bölgeleri ekleyebilir veya kaldırabilirsiniz. Cosmos veritabanı, sırasıyla bir Cosmos kapsayıcıları kümesinden oluşur. Cosmos DB'de kapsayıcıları dağıtımı ve ölçeklenebilirliği mantıksal birimleri görev yapar. Koleksiyonlar, tablolar ve grafikler oluşturduğunuz (dahili) yalnızca Cosmos kapsayıcılardır. Kapsayıcılar tamamen şematik ve bir sorgu için kapsam sağlar. Bir Cosmos kapsayıcıdaki veri alımı sırasında otomatik olarak dizine alınır. Otomatik Dizin oluşturma, kullanıcıların, özellikle de genel olarak dağıtılmış bir kurulumda şema veya dizin yönetimi kurtulur olmadan verileri sorgulamasına olanak sağlar.  

- Belirli bir bölgede, bir kapsayıcı içindeki veriler, sağladığınız ve temel alınan fiziksel bölümler (*Yerel dağıtım*) tarafından saydam olarak yönetilen bir bölüm anahtarı kullanılarak dağıtılır.  

- Her fiziksel bölüm ayrıca coğrafi bölgeler (*genel dağıtım*) genelinde çoğaltılır. 

Cosmos DB esnek kullanan bir uygulama, Cosmos kapsayıcısındaki üretilen işi ölçeklendirir veya daha fazla depolama alanı tüketir, Cosmos DB bölüm yönetimi işlemlerini (bölünmüş, kopya, silme) tüm bölgelerde saydam şekilde işler. Ölçek, dağıtım veya hatalardan bağımsız, Cosmos DB verilerin herhangi sayıda bölgesinde Global olarak dağıtılmış kapsayıcılar içinde tek sistem görüntüsünü sağlamaya devam eder.  

Aşağıdaki görüntüde gösterildiği gibi, bir kapsayıcı içindeki veriler iki boyut boyunca dağıtılır: bir bölgedeki ve bölgeler arasında, dünya çapındaki:  

![Fiziksel bölümler](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Fiziksel bir bölüm, *çoğaltma kümesi*olarak adlandırılan bir çoğaltmalar grubu tarafından uygulanır. Her makine, yukarıdaki görüntüde gösterildiği gibi, sabit bir işlem kümesi içindeki çeşitli fiziksel bölümlere karşılık gelen yüzlerce çoğaltma barındırır. Çoğaltmaları karşılık gelen fiziksel bölümlere dinamik olarak yerleştirilir ve bir küme ve veri merkezleri bölge içindeki makineler arasında Yük Dengelemesi.  

Bir yinelemenin benzersiz bir Azure Cosmos DB kiracıya ait. Her yineleme Cosmos DB'NİN'ın bir örneğini barındıran [veritabanı altyapısı](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), ilişkili dizinleri yanı sıra kaynakları yönetir. Cosmos veritabanı altyapısı, bir atom kayıt sırası (ARS) tabanlı tür sistemi üzerinde çalışır. Motor, bir şemanın kavramına belirsiz bir şekilde, kayıtların yapısı ve örnek değerleri arasındaki sınırı bulanıklaştırma. Cosmos DB, otomatik olarak her şeyi alımı sırasında şema veya dizin yönetimiyle ilgilenmenize gerek kalmadan, Global olarak dağıtılmış verileri sorgulamak kullanıcılara verimli bir biçimde dizin oluşturma tarafından tam şema agnosticism ulaşır.

Cosmos veritabanı altyapısı, çeşitli koordinasyon temelleri, dil çalışma zamanları, sorgu işlemcisi ve işlem depolamadan ve veri dizinlemeden sorumlu depolama ve dizin oluşturma alt sistemlerinin uygulanması dahil bileşenlerden oluşur. anı. Dayanıklılık ve yüksek kullanılabilirlik sağlamak için veritabanı altyapısı, veri ve dizin ssd'lerde devam ediyorsa ve yineleme içinde veritabanı altyapısı örneği arasında çoğaltır-kümelerini sırasıyla. Daha büyük kiracılar, üretilen iş ve depolama alanı ölçeğinde ve daha büyük ya da daha fazla kopyaya veya her ikisine sahip olur. Sistemin her bileşenin tam olarak zaman uyumsuz: hiç olmadığı kadar hiçbir iş parçacığını engeller ve her bir iş parçacığı kısa süreli bir gereksiz iş parçacığı anahtar ödemeden çalışır. Genelinde tüm giriş denetimi yığından tüm g/ç yolu için oran sınırlandırma ve arka baskısı genişletilmeyecek. Cosmos veritabanı altyapısı, ayrıntılı eşzamanlılık yararlanmak ve yüksek verimlilik sağlamak için tasarlanmıştır.

Cosmos DB genel dağıtımı, *çoğaltma-kümeler* ve *bölüm kümeleri*olmak üzere iki anahtar soyutlamalarını kullanır. Çoğaltma kümesi modüler bir Lego blok düzenlemesi için ve dinamik bir katmana coğrafi olarak dağıtılmış bir veya daha fazla fiziksel bölüm bölüm kümesi ise. Nasıl genel dağıtımını anlamak için çalışır, ihtiyacımız bu iki anahtar soyutlama anlamak. 

## <a name="replica-sets"></a>Çoğaltma-ayarlar

Fiziksel bir bölüm, çoğaltma kümesi olarak adlandırılan birden çok hata etki alanına yayılan, kendinden yönetilen ve dinamik olarak yük dengeli bir çoğaltmalar grubu olarak gerçekleştirilmiş olur. Bu küme, fiziksel bölümdeki verileri yüksek oranda kullanılabilir, dayanıklı ve tutarlı hale getirmek için, çoğaltılan durum makinesi protokolünü topluca uygular. Çoğaltma kümesi üyeliği *N* dinamiktir; hatalara, yönetim işlemlerine ve başarısız çoğaltmaların yeniden oluşturulması/kurtarılması için geçen süre temelinde *nMin* ve *NMAX* arasında dalgalanmaya devam eder. Üyelik değişiklikleri bağlı olarak, çoğaltma, ayrıca yeniden yapılandırır Okuma boyutu protokolünü ve çekirdekleri yazma. Belirli bir fiziksel bölüme atanan aktarım hızını tek tek dağıtmak için iki fikir sunuyoruz: 

- İlk olarak, lider üzerindeki yazma isteklerinin işlenme maliyeti, güncelleştirmeleri uygulama maliyetinden uygulamanın maliyetinden daha yüksektir. Öncü ayrılmaması gelenlere, takipçi değerinden daha fazla sistem kaynakları. 

- İkincisi, mümkün olduğunca uzak onaylamaktan okuma çekirdek verilen tutarlılık düzeyi için özel olarak İzleyici çoğaltmaları oluşur. Biz okuma gerekmedikçe hizmet vermek için öncü başvurarak kaçının. Cosmos DB desteklediği [beş tutarlılık modeli](consistency-levels.md) için çekirdek tabanlı sistemlerdeki [Yük ve kapasite](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) ilişkisi üzerinde gerçekleştirilen araştırmadan çok sayıda fikir sunuyoruz.  

## <a name="partition-sets"></a>Bölüm-ayarlar

Her biri Cosmos veritabanı bölgeleriyle yapılandırılmış olan bir grup fiziksel bölüm, yapılandırılan tüm bölgelerde çoğaltılan aynı anahtar kümesini yönetmek için oluşturulur. Bu daha yüksek düzenleme temel yapısına, belirli bir anahtar kümesini yöneten, coğrafi olarak dağıtılmış fiziksel bölümlerin coğrafi olarak dağıtılmış dinamik bir kaplaması olan *bölüm kümesi* adı verilir. Belirli bir fiziksel bölüm (bir çoğaltma kümesi) bir küme içinde kapsamlandırılaken, Bölüm kümesi aşağıdaki görüntüde gösterildiği gibi kümelere, veri merkezlerine ve coğrafi bölgelere yayılabilir:  

![Bölüm ayarlar](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Bir coğrafi olarak dağınık "süper çoğaltma çoğaltma-anahtarları kümesinin aynısına sahip olan birden fazla oluşan kümesi", bölüm bir dizi düşünebilirsiniz. Bir çoğaltma kümesine benzer şekilde, Bölüm kümesinin üyeliği de dinamik olur. belirli bir bölüm kümesine/kaynağından yeni bölümler eklemek/kaldırmak için örtük fiziksel bölüm yönetimi işlemlerine göre farklılık gösterir (örneğin, bir üzerinde üretilen işi ölçeklendirebileceğiniz zaman kapsayıcı, Cosmos veritabanınıza bölge ekleyin/kaldırın, ya da başarısızlık oluştuğunda). Her bölümün (Bölüm kümesi), Bölüm kümesi üyeliğini kendi çoğaltma kümesi içinde yönetmesine sahip olan sanallaştırmaya göre, üyelik tamamen açık ve yüksek oranda kullanılabilir olur. Bir bölüm kümesi yeniden yapılandırma sırasında topolojisi, fiziksel bölümler arasında katman da oluşturulur. Topoloji, kaynak ve hedef fiziksel bölümler arasındaki tutarlılık düzeyine, coğrafi mesafeye ve kullanılabilir ağ bant genişliğine göre dinamik olarak seçilir.  

Hizmet bir tek bir yazma bölgesi veya birden çok yazma bölgeleri ile Cosmos veritabanlarınızı yapılandırmanıza olanak tanır ve seçime bağlı olarak, bölüm kümeleri yazma tam olarak bir ya da tüm bölgelerde kabul edecek şekilde yapılandırılmış. Sistem iki düzeyli, iç içe geçmiş bir konsensus protokolünü kullanır – bir düzey, yazmaları kabul eden bir fiziksel bölüm kümesinin çoğaltmaları içinde çalışır ve diğeri için tam bir sıralama garantisi sağlamak üzere bir bölüm kümesi düzeyinde çalışır işlenen, Bölüm kümesi içindeki yazma işlemleri. Bu çok katmanlı, iç içe geçmiş fikir birliğine varılmış, Cosmos DB, müşterilerine sağlar. tutarlılık modeli uygulamasının yanı sıra, yüksek kullanılabilirlik için katı SLA'larımız uygulanması için önemlidir.  

## <a name="conflict-resolution"></a>Çakışma çözümleme

Güncelleştirme yayma, çakışma çözümü ve izleme nedensellik ilişkilerini bizim tasarımı üzerinde Önceki işten esinlenilmiştir [epidemic algoritmaları](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) ve [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) sistem. Fikirleri çekirdekler kurtulan ve Cosmos DB'nin sistem tasarımı iletişim kurmak için kullanışlı bir çerçeve başvuru sağlar, ancak Cosmos DB sistemde uyguladığımız bunları gibi bunlar da önemli dönüştürme yapılmıştır. Bu işlem, önceki sistemler kaynak İdaresi ile veya Cosmos DB çalışması gereken ölçekte tasarlandığından ya da özellikleri (örneğin, sınırlanmış stalet tutarlılığı) ve katı ve Cosmos DB kullanıcılarına müşterilerine sunduğu kapsamlı SLA 'Lar.  

Bir bölüm kümesi birden çok bölgede dağıtılır ve belirli bir bölüm kümesi oluşturan fiziksel bölümler arasında veri çoğaltmak için Cosmos Db'ler (çok ana) çoğaltmayı Protokolü aşağıdaki geri çağırma. Her fiziksel bölüm (bir bölüm kümesi), yazma işlemlerini kabul eder ve genellikle bu bölgeye yerel olan istemcilere okuma işlemleri yapar. Bir bölgedeki fiziksel bölüm tarafından kabul edilen yazma işlemleri, istemciye alınmadan önce fiziksel bölüm içinde yüksek oranda kullanılabilir hale getirilir. Bunlar belirsiz yazma ve koruma entropi kanalı kullanılarak bölüm kümesi içinde fiziksel diğer bölümlerine yayılır. İstemciler, bir istek üst bilgisi geçirerek belirsiz veya kaydedilmiş yazma isteyebilir. (Yayma sıklığını dahil) koruma entropi yayma dinamik fiziksel bölümler ve tutarlılık düzeyi yapılandırılmış bölüm kümesi, bölgesel yakınlık topolojiye bağlı. Bir bölüm kümesinde Cosmos DB, dinamik olarak seçilen arbiter bölümü olan bir birincil işleme düzeni izler. Arbiter seçimi dinamiktir ve yeniden yapılandırma bölümü kümesinin bir parçası katmana topolojisine dayanır. Taahhüt edilen (çok-row/toplu güncelleştirmeler dahil) yazma sipariş edilmesi garanti edilir. 

Nedensellik ilişkilerini izlemek ve algılamak ve çözmek için vektörleri güncelleştirme çakışması sürümü için kodlanmış vektör saatleri (sırasıyla bölge kodu ve mantıksal saatler her çoğaltma kümesi, fikir birliğine varılmış düzeyine karşılık gelen ve bölüm kümesi içeren) kullanıyoruz. Topoloji ve eş seçimi algoritması, sabit ve en az depolama ve sürüm vektörü, en az bir ağ yükünü sağlamak için tasarlanmıştır. Algoritma katı yakınsama özelliği garanti eder.  

Birden çok yazma bölgeleri ile yapılandırılmış Cosmos veritabanları için sistem esnek otomatik çakışma çözümleme ilkeler arasından, geliştiriciler için de dahil olmak üzere sunar: 

- **Son yazma-WINS (LWW)** , varsayılan olarak, sistem tarafından tanımlanan bir zaman damgası özelliği kullanır (zaman eşitleme saati protokolüne dayanır). Cosmos DB Çakışma çözümlemesi için kullanılan diğer özelliklerden herhangi birini özel sayısal belirtmenizi sağlar.  
- **Uygulama tanımlı (özel) çakışma çözümleme ilkesi** (birleştirme yordamları aracılığıyla ifade edilir), bu, çakışmaların uygulama tanımlı semantik mutabakatı için tasarlanmıştır. Bu yordamlar, bir veritabanı işlemi sunucu tarafı şirket himayesinde yazma yazma çakışmaları algılanması üzerine çağrılır. Sistem, taahhüt protokolünün bir parçası olarak birleştirme yordamının yürütülmesi için tam olarak bir kez sağlar. İle oynaması için kullanabileceğiniz [birkaç çakışma çözümü örneği](how-to-manage-conflicts.md) vardır.  

## <a name="consistency-models"></a>Tutarlılık modeli

Cosmos veritabanınızı tek veya birden çok yazma bölgeleriyle yapılandırıp, iyi tanımlanmış beş tutarlılık modelinden seçim yapabilirsiniz. Birden çok yazma bölgesi ile, tutarlılık düzeylerinin bazı önemli yönleri aşağıda verilmiştir:  

Sınırlanmış Eskime durumu tutarlılığı, tüm okumaların her bölgede en son yazma işleminin *K* ön ekleri veya *T* saniye içinde olmasını güvence altına alır. Ayrıca, sınırlı stalet tutarlılığı olan okumaların tek Tonic olma garantisi ve tutarlı ön ek garantisi vardır. Koruma entropi protokolü, oranı sınırlı bir biçimde çalışır ve ön ekler değil accumulate ve yazma backpressure uygulanacak yok sağlar. Oturum tutarlılığı, monoton okuma, monoton yazma, kendi yazmalarınızı okuma, aşağıdaki okuma ve tutarlı ön ek garantisi yazma işlemlerini garanti eder, dünya çapındaki. Güçlü tutarlılık ile yapılandırılmış veritabanları için, bölgeler arasında zaman uyumlu çoğaltma nedeniyle birden fazla yazma bölgesinin avantajları (düşük yazma gecikmesi, yüksek yazma kullanılabilirliği) uygulanmaz.

Cosmos DB 'deki beş tutarlılık modelinin semantiği [burada](consistency-levels.md)açıklanmıştır ve [burada](https://github.com/Azure/azure-cosmos-tla)yüksek düzeyli bir TLA + belirtimleri kullanılarak matematiksel olarak açıklanacaktır.

## <a name="next-steps"></a>Sonraki adımlar

Ardından aşağıdaki makaleleri kullanarak genel dağıtımı yapılandırma işlemleri gerçekleştirmeyi öğreneceksiniz:

* [Bölge veritabanı hesabınızdan Ekle/Kaldır](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Birden çok giriş için istemcileri yapılandırma](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Özel bir çakışma çözümü ilkesi oluşturma](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
