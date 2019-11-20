---
title: Azure Data Lake Storage 2. fırtınası performansı ayarlama yönergeleri | Microsoft Docs
description: Azure Data Lake Storage 2. fırtınası performansı ayarlama yönergeleri
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 108eeb03c0ed484e40b884372018bbbef686ee62
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159867"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen2"></a>HDInsight ve Azure Data Lake Storage 2. için performans ayarlama Kılavuzu

Azure fırtınası topolojisinin performansını ayarladığınızda göz önünde bulundurmanız gereken faktörleri anlayın. Örneğin, Spog 'ler ve cıvatalar tarafından gerçekleştirilen işin özelliklerinin anlaşılması önemlidir (çalışmanın g/ç veya bellek yoğun olup olmadığı). Bu makalede, yaygın sorunları giderme dahil olmak üzere bir dizi performans ayarlama Kılavuzu ele alınmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage 2. hesabı**. Bir oluşturma hakkında yönergeler için bkz. [hızlı başlangıç: analitik için depolama hesabı oluşturma](data-lake-storage-quickstart-create-account.md).
* Data Lake Storage 2. hesabına erişimi olan **Azure HDInsight kümesi** . Bkz. [Azure HDInsight kümeleri ile Azure Data Lake Storage 2. kullanma](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Küme için Uzak Masaüstü 'Nü etkinleştirdiğinizden emin olun.
* **Data Lake Storage 2. bir fırtınası kümesi çalıştırılıyor**. Daha fazla bilgi için bkz. [HDInsight 'Ta fırtınası](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Data Lake Storage 2. performans ayarlama yönergeleri**.  Genel performans kavramları için [Data Lake Storage 2. performans ayarlama Kılavuzu](data-lake-storage-performance-tuning-guidance.md)' na bakın.   

## <a name="tune-the-parallelism-of-the-topology"></a>Topolojinin paralelliğini ayarlama

G/ç 'nin eşzamanlılık düzeyini Data Lake Storage 2. artırarak performansı artırabilirsiniz. Bir fırtınası topolojisi paralelliği tespit eden bir yapılandırma kümesine sahiptir:
* Çalışan işlem sayısı (çalışanlar VM 'lerde eşit olarak dağıtılır).
* Spout yürütücü örneklerinin sayısı.
* Rulo yürütücü örneklerinin sayısı.
* Spout görevlerinin sayısı.
* Cıvatın görevi sayısı.

Örneğin, 4 VM ve 4 çalışan işlem, 32 Spout yürütmesi ve 32 Spout görevi ve 256 sürgüler ve 512 cıvatların bulunduğu bir kümede aşağıdakileri göz önünde bulundurun:

Çalışan düğümü olan her gözetmen, tek bir çalışan Java sanal makinesi (JVM) işlemine sahiptir. Bu JVM işlemi 4 Spout iş parçacığını ve 64 rulut iş parçacığını yönetir. Her iş parçacığı içinde görevler sırayla çalıştırılır. Yukarıdaki yapılandırmayla, her Spout iş parçacığının 1 görevi vardır ve her bir cıvacın iş parçacığının 2 görevi vardır.

Bu durumda, dahil edilen çeşitli bileşenler ve sahip olduğunuz paralellik düzeyini nasıl etkilediği aşağıda verilmiştir:
* İş göndermek ve yönetmek için baş düğüm (Nimbus ' de fırtınası olarak adlandırılır) kullanılır. Bu düğümlerin paralellik derecesi üzerinde hiçbir etkisi yoktur.
* Gözetmen düğümleri. HDInsight 'ta, bu bir çalışan düğümü olan Azure VM 'sine karşılık gelir.
* Çalışan görevleri VM 'lerde çalışan bir fırtınası işlemleridir. Her çalışan görevi bir JVM örneğine karşılık gelir. Fırtınası, belirttiğiniz çalışan işlem sayısını olabildiğince eşit şekilde çalışan düğümlere dağıtır.
* Spout ve cıvam yürütücü örnekleri. Her yürütücü örneği çalışanlar içinde çalışan bir iş parçacığına karşılık gelir (JVM 'Ler).
* Fırtınası görevleri. Bunlar, bu iş parçacıklarının her birinin çalıştırdığı mantıksal görevlerdir. Bu, paralellik düzeyini değiştirmez, bu nedenle yürütücü başına birden çok görev gerekip gerekmediğini değerlendirmelisiniz.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Data Lake Storage 2. en iyi performansı elde edin

Data Lake Storage 2. ile çalışırken, aşağıdakileri yaparsanız en iyi performansı elde edersiniz:
* Küçük Birleşik değişikliklerinizi daha büyük boyutlarda birleştirin.
* Birçok eşzamanlı istek yapabilirsiniz. Her bir sürgüle iş parçacığı engelleme okumaları yaptığından, çekirdek başına 8-12 iş parçacığı aralığında bir yere sahip olmak istiyorsunuz. Bu, NIC 'i ve CPU 'YU iyi şekilde korur. Daha büyük bir VM daha fazla eşzamanlı istek sunar.  

### <a name="example-topology"></a>Örnek topoloji

D13v2 Azure VM ile 8 çalışan düğüm kümeniz olduğunu varsayalım. Bu VM 8 çekirdeğe sahiptir, bu nedenle 8 çalışan düğümleri arasında toplam 64 çekirdeğe sahip olursunuz.

Çekirdek başına 8 rulumu iş parçacığı yaptık. Verilen 64 çekirdek, bu, 512 toplam (iş parçacığı). Bu durumda, sanal makine başına tek bir JVM ile başlayacağız ve genellikle eşzamanlılık elde etmek için JVM içinde eşzamanlılık iş parçacığı kullanır. Bu, 8 çalışan görevlerine (Azure VM başına bir tane) ve 512 cıvatörlerine ihtiyaç duyduğumuz anlamına gelir. Bu yapılandırma verildiğinde, fırtınası çalışanları çalışan düğümleri arasında eşit olarak (gözetmen düğümleri olarak da bilinir) dağıtmayı dener ve her çalışan düğümü 1 JVM 'yi verir. Artık süper vizörlerin içinde, fırtınası, her gözetmen (yani, JVM) 8 iş parçacığını sunarak, yürüticileri, her bir gözetmen (yani, JVM) 8 iş parçacığı arasında eşit olarak

## <a name="tune-additional-parameters"></a>Ek parametreleri ayarla
Temel topolojiden sonra, parametrelerden herhangi birini ince ayar isteyip istemediğinizi göz önünde bulundurun:
* **Çalışan düğümü başına JVM sayısı.** Bellekte barındırdığınızda büyük bir veri yapınız (örneğin, bir arama tablosu) varsa, her JVM ayrı bir kopya gerektirir. Alternatif olarak, daha az JVM varsa veri yapısını birçok iş parçacığı üzerinde kullanabilirsiniz. Cıvatanın g/ç için, JVM 'lerin sayısı bu JVM 'Ler genelinde eklenen iş parçacığı sayısı kadar farklılık yapmaz. Kolaylık olması açısından, çalışan başına bir JVM olması iyi bir fikirdir. Aradığınıza bağlı olarak veya ihtiyacınız olan uygulama işlemlerini, ancak bu numarayı değiştirmeniz gerekebilir.
* **Spout yürüticileri sayısı.** Yukarıdaki örnek Data Lake Storage 2. yazmak için cıvatları kullandığından, spotların sayısı, doğrudan cıvatiye uygun değildir. Ancak, Spout 'daki işleme veya g/ç miktarına bağlı olarak en iyi performansı elde etmek iyi bir fikirdir. Cıvatları meşgul tutmaya yetecek kadar biriktirmeolduğunuzdan emin olun. Spotların çıkış ücretleri cıvatların verimlilik ile aynı olmalıdır. Gerçek yapılandırma Spout 'a bağlıdır.
* **Görev sayısı.** Her bir sürgüsü tek bir iş parçacığı olarak çalışır. Her bir ek görev, ek eşzamanlılık sağlamaz. Yalnızca kullanım açısından, kayıt düzeni elde etmek için gereken tek zaman, rulonun yürütme süresinin büyük bir oranını alırsa. Daha fazla tanımlama grubunu daha büyük bir ekleme için, fiteleten bir onay göndermeden önce bu çok sayıda grubu gruplamak iyi bir fikirdir. Bu nedenle, çoğu durumda birden çok görev ek bir avantaj sağlamaz.
* **Yerel veya karıştırma gruplandırması.** Bu ayar etkinleştirildiğinde, diziler aynı çalışan işlemi içinde cıvatlara gönderilir. Bu işlem işlemler arası iletişimi ve ağ çağrılarını azaltır. Bu çoğu topolojilerde önerilir.

Bu temel senaryo, iyi bir başlangıç noktasıdır. En iyi performansı elde etmek için yukarıdaki parametreleri ince ayar için kendi verilerinize test edin.

## <a name="tune-the-spout"></a>Spout ayarla

Spout 'yi ayarlamak için aşağıdaki ayarları değiştirebilirsiniz.

- **Demet zaman aşımı: topoloji. Message. Timeout. secs**. Bu ayar, bir iletinin tamamlanma süresini belirler ve başarısız kabul edilmeden önce onay alır.

- **Çalışan işlemi başına en fazla bellek: Worker. childopts**. Bu ayar, Java çalışanlarına ek komut satırı parametreleri belirtmenizi sağlar. Burada en yaygın olarak kullanılan ayar, bir JVM 'nin yığınına ayrılan maksimum belleği belirleyen XmX ' dir.

- **Maksimum Spout bekliyor: topoloji. Max. Spout. Pending**. Bu ayar, herhangi bir zamanda Spout iş parçacığı başına uçuş (henüz topoloji olmayan tüm düğümlerde onaylanmamış) tanımlama gruplarının sayısını belirler.

  İçin iyi bir hesaplama, her bir tanımlama grubu için boyut tahminidir. Daha sonra, bir Spout iş parçacığının ne kadar bellek olduğunu anlamak. Bu değere bölünen bir iş parçacığına ayrılan toplam bellek, en fazla Spout bekleyen parametresi için üst sınır vermelidir.

Varsayılan Data Lake Storage 2. fırtınası işareti, bu parametreyi ayarlamak için kullanılabilen bir boyut eşitleme ilkesi parametresine (fileBufferSize) sahiptir.

G/ç yoğunluklu topolojilerde, her bir soıt iş parçacığının kendi dosyasına yazması ve bir dosya döndürme ilkesi (fileRotationSize) ayarlaması iyi bir fikirdir. Dosya belirli bir boyuta ulaştığında, akış otomatik olarak temizlenir ve üzerine yeni bir dosya yazılır. Döndürme için önerilen dosya boyutu 1 GB 'tır.

## <a name="monitor-your-topology-in-storm"></a>Topolojinizi fırtınası içinde izleyin  
Topolojiniz çalışırken, bunu fırtınası Kullanıcı arabiriminde izleyebilirsiniz. Bakılacak ana parametreler şunlardır:

* **Toplam işlem yürütme gecikmesi.** Bu, bir tanımlama grubunun Spout tarafından, cıvata işlenen ve onaylanan ortalama süredir.

* **Toplam rulo işlem gecikmesi.** Bu, bir onay alana kadar, cıvata kayıt düzeni tarafından harcanan ortalama süredir.

* **Toplam cıvata yürütme gecikmesi.** Bu, Execute yöntemindeki cıvatın harcadığı ortalama süredir.

* **Başarısızlık sayısı.** Bu, zaman aşımına uğramadan önce tam olarak işlenemeyen tanımlama grubu sayısını ifade eder.

* **Kü.** Bu, sisteminizin ne kadar meşgul olduğunu gösteren bir ölçüdür. Bu sayı 1 ise, cıvatları, olabildiğince hızlı çalışır. 1 ' den küçükse paralellik düzeyini artırın. 1 ' den büyükse paralelliği azaltın.

## <a name="troubleshoot-common-problems"></a>Sık karşılaşılan sorunları giderme
Yaygın olarak karşılaşılan birkaç sorun giderme senaryosu aşağıda verilmiştir.
* **Birçok tanımlama grubu zaman aşımına uğruyor.** Performans sorununa nerede olduğunu öğrenmek için topolojideki her düğüme bakın. Bunun en yaygın nedeni, cıvatların spokörlerle devam edebilmemelidir. Bu, işlenmek üzere bekleyen iç arabelleklerin iş gruplarını oluşturmayı sağlar. Zaman aşımı değerini artırmayı veya en fazla Spout bekletmeyi azaltmayı düşünün.

* **Yüksek toplam işlem yürütme gecikmesi vardır, ancak düşük bir işlem gecikmesi vardır.** Bu durumda, tanımlama gruplarının yeterince hızlı bir şekilde onaylanmaması mümkündür. Yeterli sayıda bildirimlerin olup olmadığını denetleyin. Diğer bir olasılık ise, cıvatları işlemeden önce kuyrukta çok uzun süre beklemelidir. En fazla Spout bekleyen değerini azaltın.

* **Yüksek bir cıvata yürütme gecikmesi vardır.** Bu, cıvatlarınızın Execute () yönteminin çok uzun sürmesi anlamına gelir. Kodu iyileştirin veya yazma boyutlarına ve Temizleme davranışına bakın.

### <a name="data-lake-storage-gen2-throttling"></a>Data Lake Storage 2. azaltma
Data Lake Storage 2. tarafından belirtilen bant genişliği sınırlarına ulaşırsanız, görev hatalarıyla karşılaşabilirsiniz. Azaltma hataları için görev günlüklerine bakın. Kapsayıcının boyutunu artırarak paralellik değerini azaltabilirsiniz.    

Kısıtlanıyor olup olmadığınızı denetlemek için istemci tarafında hata ayıklama günlüğünü etkinleştirin:

1. **Ambarı** > **fırtınası** > **config** > **gelişmiş fırtınası-çalışan-Log4J**, **&lt;kök düzeyi = "Info"** &gt;&lt;**kök düzeyi = "Debug"** &gt;olarak değiştirin. Yapılandırmanın etkili olması için tüm düğümleri/hizmeti yeniden başlatın.
2. &gt;azaltma özel durumları için çalışan düğümlerinde (/var/log/Storm/Worker-Artifacts/&lt;Topologyıname&gt;/&lt;bağlantı noktası Data Lake Storage 2./Worker.log) yer alan fırtınası topolojisi günlüklerini izleyin.

## <a name="next-steps"></a>Sonraki adımlar
[Bu blogda](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/), fırtınası için ek performans ayarlamaya başvurulabilir.

Çalıştırmak için ek bir örnek için bkz. [GitHub 'da bu](https://github.com/hdinsight/storm-performance-automation).
