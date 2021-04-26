---
title: Azure Data Lake Storage 1. ile performans ayarlama-fırtınası
description: Yaygın sorunları giderme dahil olmak üzere bir Azure fırtınası topolojisinin performansını ayarladığınızda göz önünde bulundurmanız gereken faktörleri anlayın.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 95619c75d332ec1bf68af97fc3dddbc67b6706ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97725046"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen1"></a>HDInsight ve Azure Data Lake Storage 1. için performans ayarlama Kılavuzu

Azure fırtınası topolojisinin performansını ayarladığınızda göz önünde bulundurmanız gereken faktörleri anlayın. Örneğin, Spog 'ler ve cıvatalar tarafından gerçekleştirilen işin özelliklerinin anlaşılması önemlidir (çalışmanın g/ç veya bellek yoğun olup olmadığı). Bu makalede, yaygın sorunları giderme dahil olmak üzere bir dizi performans ayarlama Kılavuzu ele alınmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage 1. hesabı**. Bir oluşturma hakkında yönergeler için bkz. Azure Data Lake Storage 1. kullanmaya [başlama](data-lake-store-get-started-portal.md).
* Data Lake Storage 1. hesabına erişimi olan **bir Azure HDInsight kümesi** . Bkz. [Data Lake Storage 1. HDInsight kümesi oluşturma](data-lake-store-hdinsight-hadoop-use-portal.md). Küme için Uzak Masaüstü 'Nü etkinleştirdiğinizden emin olun.
* **Data Lake Storage 1. bir fırtınası kümesi çalıştırılıyor**. Daha fazla bilgi için bkz. [HDInsight 'Ta fırtınası](../hdinsight/storm/apache-storm-overview.md).
* **Data Lake Storage 1. performans ayarlama yönergeleri**.  Genel performans kavramları için [Data Lake Storage 1. performans ayarlama Kılavuzu](./data-lake-store-performance-tuning-guidance.md)' na bakın.  

## <a name="tune-the-parallelism-of-the-topology"></a>Topolojinin paralelliğini ayarlama

G/ç 'nin eşzamanlılık düzeyini Data Lake Storage 1. artırarak performansı artırabilirsiniz. Bir fırtınası topolojisi paralelliği tespit eden bir yapılandırma kümesine sahiptir:
* Çalışan işlem sayısı (çalışanlar VM 'lerde eşit olarak dağıtılır).
* Spout yürütücü örneklerinin sayısı.
* Rulo yürütücü örneklerinin sayısı.
* Spout görevlerinin sayısı.
* Cıvatın görevi sayısı.

Örneğin, 4 VM ve 4 çalışan işlem, 32 Spout yürütmesi ve 32 Spout görevi ve 256 sürgüler ve 512 cıvatların bulunduğu bir kümede aşağıdakileri göz önünde bulundurun:

Çalışan düğümü olan her gözetmen, tek bir çalışan Java sanal makinesi (JVM) işlemine sahiptir. Bu JVM işlemi 4 Spout iş parçacığını ve 64 rulut iş parçacığını yönetir. Her iş parçacığı içinde görevler sırayla çalıştırılır. Yukarıdaki yapılandırmayla, her Spout iş parçacığının bir görevi vardır ve her bir rulo iş parçacığının iki görevi vardır.

Bu durumda, dahil edilen çeşitli bileşenler ve sahip olduğunuz paralellik düzeyini nasıl etkilediği aşağıda verilmiştir:
* İş göndermek ve yönetmek için baş düğüm (Nimbus ' de fırtınası olarak adlandırılır) kullanılır. Bu düğümlerin paralellik derecesi üzerinde hiçbir etkisi yoktur.
* Gözetmen düğümleri. HDInsight 'ta, bu bir çalışan düğümü olan Azure VM 'sine karşılık gelir.
* Çalışan görevleri VM 'lerde çalışan bir fırtınası işlemleridir. Her çalışan görevi bir JVM örneğine karşılık gelir. Fırtınası, belirttiğiniz çalışan işlem sayısını olabildiğince eşit şekilde çalışan düğümlere dağıtır.
* Spout ve cıvam yürütücü örnekleri. Her yürütücü örneği çalışanlar içinde çalışan bir iş parçacığına karşılık gelir (JVM 'Ler).
* Fırtınası görevleri. Bunlar, bu iş parçacıklarının her birinin çalıştırdığı mantıksal görevlerdir. Bu, paralellik düzeyini değiştirmez, bu nedenle yürütücü başına birden çok görev gerekip gerekmediğini değerlendirmelisiniz.

### <a name="get-the-best-performance-from-data-lake-storage-gen1"></a>Data Lake Storage 1. en iyi performansı elde edin

Data Lake Storage 1. ile çalışırken, aşağıdakileri yaparsanız en iyi performansı elde edersiniz:
* Küçük Birleşik değişikliklerinizi daha büyük boyutlara (ideal 4 MB) birleştirin.
* Birçok eşzamanlı istek yapabilirsiniz. Her bir sürgüle iş parçacığı engelleme okumaları yaptığından, çekirdek başına 8-12 iş parçacığı aralığında bir yere sahip olmak istiyorsunuz. Bu, NIC 'i ve CPU 'YU iyi şekilde korur. Daha büyük bir VM daha fazla eşzamanlı istek sunar.  

### <a name="example-topology"></a>Örnek topoloji

D13v2 Azure VM 'si olan sekiz çalışan düğüm kümeniz olduğunu varsayalım. Bu sanal makinede sekiz çekirdek bulunur, bu nedenle sekiz çalışan düğümü arasında toplam 64 çekirdek vardır.

Çekirdek başına sekiz rulumu iş parçacığı yaptık. Verilen 64 çekirdek, bu, 512 toplam (iş parçacığı). Bu durumda, sanal makine başına tek bir JVM ile başlayacağız ve genellikle eşzamanlılık elde etmek için JVM içinde eşzamanlılık iş parçacığı kullanır. Bu, sekiz çalışan görevi (Azure VM başına bir tane) ve 512 cıvatörler için gereken anlamına gelir. Bu yapılandırma verildiğinde, fırtınası çalışanları çalışan düğümleri arasında eşit olarak (gözetmen düğümleri olarak da bilinir) dağıtmayı dener ve her çalışan düğümüne bir JVM verir. Artık süper vizörlerin içinde, fırtınası, her gözetmen (yani JVM) sekiz iş parçacığını sunarak, yürüticileri, her bir gözetmen (yani JVM) arasında eşit olarak dağıtmaya çalışır.

## <a name="tune-additional-parameters"></a>Ek parametreleri ayarla
Temel topolojiden sonra, parametrelerden herhangi birini ince ayar isteyip istemediğinizi göz önünde bulundurun:
* **Çalışan düğümü başına JVM sayısı.** Bellekte barındırdığınızda büyük bir veri yapınız (örneğin, bir arama tablosu) varsa, her JVM ayrı bir kopya gerektirir. Alternatif olarak, daha az JVM varsa veri yapısını birçok iş parçacığı üzerinde kullanabilirsiniz. Cıvatanın g/ç için, JVM 'lerin sayısı bu JVM 'Ler genelinde eklenen iş parçacığı sayısı kadar farklılık yapmaz. Kolaylık olması açısından, çalışan başına bir JVM olması iyi bir fikirdir. Aradığınıza bağlı olarak veya ihtiyacınız olan uygulama işlemlerini, ancak bu numarayı değiştirmeniz gerekebilir.
* **Spout yürüticileri sayısı.** Yukarıdaki örnek Data Lake Storage 1. yazmak için cıvatları kullandığından, spotların sayısı, doğrudan cıvatiye uygun değildir. Ancak, Spout 'daki işleme veya g/ç miktarına bağlı olarak en iyi performansı elde etmek iyi bir fikirdir. Cıvatları meşgul tutmaya yetecek kadar biriktirmeolduğunuzdan emin olun. Spotların çıkış ücretleri cıvatların verimlilik ile aynı olmalıdır. Gerçek yapılandırma Spout 'a bağlıdır.
* **Görev sayısı.** Her bir sürgüsü tek bir iş parçacığı olarak çalışır. Her bir ek görev, ek eşzamanlılık sağlamaz. Yalnızca kullanım açısından, kayıt düzeni elde etmek için gereken tek zaman, rulonun yürütme süresinin büyük bir oranını alırsa. Bir onay noktasından bildirim göndermeden önce çok sayıda tanımlama grubunu daha büyük bir ekleme halinde gruplamak iyi bir fikirdir. Bu nedenle, çoğu durumda birden çok görev ek bir avantaj sağlamaz.
* **Yerel veya karıştırma gruplandırması.** Bu ayar etkinleştirildiğinde, diziler aynı çalışan işlemi içinde cıvatlara gönderilir. Bu işlem işlemler arası iletişimi ve ağ çağrılarını azaltır. Bu çoğu topolojilerde önerilir.

Bu temel senaryo, iyi bir başlangıç noktasıdır. En iyi performansı elde etmek için yukarıdaki parametreleri ince ayar için kendi verilerinize test edin.

## <a name="tune-the-spout"></a>Spout ayarla

Spout 'yi ayarlamak için aşağıdaki ayarları değiştirebilirsiniz.

- **Demet zaman aşımı: topoloji. Message. Timeout. secs**. Bu ayar, bir iletinin tamamlanma süresini belirler ve başarısız kabul edilmeden önce onay alır.

- **Çalışan işlemi başına en fazla bellek: Worker. childopts**. Bu ayar, Java çalışanlarına ek komut satırı parametreleri belirtmenizi sağlar. Burada en yaygın olarak kullanılan ayar, bir JVM 'nin yığınına ayrılan maksimum belleği belirleyen XmX ' dir.

- **Maksimum Spout bekliyor: topoloji. Max. Spout. Pending**. Bu ayar, herhangi bir zamanda Spout iş parçacığı başına uçuş (henüz topoloji olmayan tüm düğümlerde onaylanmamış) tanımlama gruplarının sayısını belirler.

  İçin iyi bir hesaplama, her bir tanımlama grubu için boyut tahminidir. Daha sonra, bir Spout iş parçacığının ne kadar bellek olduğunu anlamak. Bu değere bölünen bir iş parçacığına ayrılan toplam bellek, en fazla Spout bekleyen parametresi için üst sınır vermelidir.

## <a name="tune-the-bolt"></a>Sürgüsü ayarla
Data Lake Storage 1. yazarken bir boyut eşitleme ilkesi (istemci tarafında arabellek) 4 MB olarak ayarlayın. Bir reçeteye göre veya HSync (), yalnızca arabellek boyutu bu değerde olduğunda gerçekleştirilir. Açıkça bir HSync () gerçekleştirmediğiniz müddetçe, çalışan VM 'deki Data Lake Storage 1. sürücüsü bu arabelleğe alma işlemini otomatik olarak yapar.

Varsayılan Data Lake Storage 1. fırtınası işareti, bu parametreyi ayarlamak için kullanılabilen bir boyut eşitleme ilkesi parametresine (fileBufferSize) sahiptir.

G/ç yoğunluklu topolojilerde, her bir soıt iş parçacığının kendi dosyasına yazması ve bir dosya döndürme ilkesi (fileRotationSize) ayarlaması iyi bir fikirdir. Dosya belirli bir boyuta ulaştığında, akış otomatik olarak temizlenir ve üzerine yeni bir dosya yazılır. Döndürme için önerilen dosya boyutu 1 GB 'tır.

### <a name="handle-tuple-data"></a>Kayıt düzeni verilerini işleme

Bu durumda, Spout, cıvam tarafından açıkça onaylanana kadar bir kayıt düzeni üzerinde tutulur. Bir tanımlama grubu, cıvam tarafından okunmadıysa ancak henüz onaylanmadıysa, Spout Data Lake Storage 1. arka uca kalıcı olmayabilir. Bir tanımlama grubu onaylandıktan sonra Spout, cıvatanın sürekliliği olabilir ve sonra kaynak verileri okuma kaynağı olan herhangi bir kaynaktan silebilir.  

Data Lake Storage 1. en iyi performansı elde etmek için, cıvam arabelleği 4 MB 'lık kayıt verisi olmalıdır. Ardından Data Lake Storage 1. arka ucuna 1 4 MB yazma olarak yazın. Veriler depoya başarıyla yazıldıktan sonra (hflush () çağırarak, bu, verileri Spout 'e geri kabul edebilir. Burada sağlanan örnek bir sürgüsü aşağıda verilmiştir. Ayrıca, hflush () çağrısı yapılmadan ve tanımlama grupları onaylanmadan önce daha fazla sayıda tanımlama kümesini tutmak da kabul edilebilir. Bununla birlikte, bu, Spout 'ın tutması gereken Uçuş ve bu nedenle JVM başına gereken bellek miktarını artıran başlık sayısını artırır.

> [!NOTE]
> Uygulamalar, diğer performans dışı nedenlerle, başlıkları daha sık (4 MB 'tan az veri boyutunda) kabul etmek için bir gereksinime sahip olabilir. Ancak bu, depolama arka ucunun g/ç verimini etkileyebilir. Bu zorunluluğunu getirir 'in, cıvatanın g/ç performansına karşı dikkatli bir şekilde karşılaştırın.

Tanımlama gruplarının gelen hızı yüksek değilse, 4 MB arabelleğin doldurulması uzun sürer, bunu şu şekilde değerlendirin:
* Cıvatların sayısını azaltarak, doldurmanız gereken daha az arabellek vardır.
* Her x temizlemesi veya her y milisaniyesi için bir hflush () tetiklendiği zaman tabanlı veya sayı tabanlı bir ilkeye sahip olmak ve şimdiye kadar toplanan başlıkların geri kabul edildiği.

Bu durumda üretilen iş hacmi daha düşüktür, ancak düşük bir olay oranıyla, en büyük verimlilik yine de en büyük amaç değildir. Bu azaltmaları, kayıt düzeni için gereken toplam süreyi, mağazaya akacak şekilde azaltmanıza yardımcı olur. Bu durum, düşük bir olay oranıyla bile gerçek zamanlı bir işlem hattı ister misiniz? Ayrıca, gelen tanımlama grubu oranınızı düşükse, topology.message.timeout_secs parametresini ayarlamanız gerektiğini unutmayın. bu nedenle, ara belleğe alınan veya işlenen sırada tanımlama gruplarının zaman aşımına uğrar.

## <a name="monitor-your-topology-in-storm"></a>Topolojinizi fırtınası içinde izleyin  
Topolojiniz çalışırken, bunu fırtınası Kullanıcı arabiriminde izleyebilirsiniz. Bakılacak ana parametreler şunlardır:

* **Toplam işlem yürütme gecikmesi.** Bu, bir tanımlama grubunun Spout tarafından, cıvata işlenen ve onaylanan ortalama süredir.

* **Toplam rulo işlem gecikmesi.** Bu, bir onay alana gelinceye kadar, bu kayıt düzeni tarafından harcanan ortalama süredir.

* **Toplam cıvata yürütme gecikmesi.** Bu, Execute yöntemindeki cıvatın harcadığı ortalama süredir.

* **Başarısızlık sayısı.** Bu, zaman aşımına uğramadan önce tam olarak işlenemeyen tanımlama grubu sayısını ifade eder.

* **Kü.** Bu, sisteminizin ne kadar meşgul olduğunu gösteren bir ölçüdür. Bu sayı 1 ise, cıvatları, olabildiğince hızlı çalışır. 1 ' den küçükse paralellik düzeyini artırın. 1 ' den büyükse paralelliği azaltın.

## <a name="troubleshoot-common-problems"></a>Sık karşılaşılan sorunları giderme
Yaygın olarak karşılaşılan birkaç sorun giderme senaryosu aşağıda verilmiştir.
* **Birçok tanımlama grubu zaman aşımına uğruyor.** Performans sorununa nerede olduğunu öğrenmek için topolojideki her düğüme bakın. Bunun en yaygın nedeni, cıvatların spokörlerle devam edebilmemelidir. Bu, işlenmek üzere bekleyen iç arabelleklerin iş gruplarını oluşturmayı sağlar. Zaman aşımı değerini artırmayı veya en fazla Spout bekletmeyi azaltmayı düşünün.

* **Yüksek toplam işlem yürütme gecikmesi vardır, ancak düşük bir işlem gecikmesi vardır.** Bu durumda, tanımlama gruplarının yeterince hızlı bir şekilde onaylanmaması mümkündür. Yeterli sayıda bildirimlerin olup olmadığını denetleyin. Diğer bir olasılık ise, cıvatları işlemeden önce kuyrukta çok uzun süre beklemelidir. En fazla Spout bekleyen değerini azaltın.

* **Yüksek bir cıvata yürütme gecikmesi vardır.** Bu, cıvatlarınızın Execute () yönteminin çok uzun sürmesi anlamına gelir. Kodu iyileştirin veya yazma boyutlarına ve Temizleme davranışına bakın.

### <a name="data-lake-storage-gen1-throttling"></a>Data Lake Storage 1. azaltma
Data Lake Storage 1. tarafından belirtilen bant genişliği sınırlarına ulaşırsanız, görev hatalarıyla karşılaşabilirsiniz. Azaltma hataları için görev günlüklerine bakın. Kapsayıcının boyutunu artırarak paralellik değerini azaltabilirsiniz.    

Kısıtlanıyor olup olmadığınızı denetlemek için istemci tarafında hata ayıklama günlüğünü etkinleştirin:

1. **Ambarı**  >  **fırtınası**  >  **yapılandırması**  >  **Gelişmiş fırtınası-çalışan-Log4J**' de **&lt; kök düzeyi = "info" &gt;** öğesini **&lt; root Level = "Debug" &gt;** olarak değiştirin. Yapılandırmanın etkili olması için tüm düğümleri/hizmeti yeniden başlatın.
2. Data Lake Storage 1. azaltma özel durumları için çalışan düğümlerdeki fırtınası topolojisi günlüklerini (/var/log/Storm/Worker-Artifacts/ &lt; topologyıname &gt; / &lt; Port &gt; /Worker.log altında) izleyin.

## <a name="next-steps"></a>Sonraki adımlar
[Bu blogda](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs), fırtınası için ek performans ayarlamaya başvurulabilir.

Çalıştırmak için ek bir örnek için bkz. [GitHub 'da bu](https://github.com/hdinsight/storm-performance-automation).