---
title: Azure HDInsight 'ta ML hizmetlerine giriş
description: Büyük veri analizi için uygulamalar oluşturmak üzere HDInsight 'ta ML hizmetlerini nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 87f4181e820b1c6ecdeb0fda85a88e80db248dd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943929"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Azure HDInsight 'ta ML Hizmetleri nedir?

Microsoft Machine Learning Server, Azure 'da HDInsight kümeleri oluştururken dağıtım seçeneği olarak kullanılabilir. Bu seçeneği sağlayan küme türü **ml Hizmetleri** olarak adlandırılır. Bu özellik, HDInsight 'ta uyarlanmayan analiz yöntemlerine yönelik isteğe bağlı erişim sağlar.

HDInsight üzerinde ML Hizmetleri, neredeyse her boyuttaki veri kümelerinde R tabanlı analizler için en son özellikleri sağlar. Veri kümeleri, Azure Blob veya Data Lake Storage 'a yüklenebilir. R tabanlı uygulamalarınızın 8000 + açık kaynaklı R paketleri kullanılabilir. ScaleR 'daki yordamlar, Microsoft 'un büyük veri analizi paketi de mevcuttur.

Edge düğümü, kümeye bağlanmak ve R betiklerinizi çalıştırmak için uygun bir yer sağlar. Edge düğümü, sunucunun çekirdekleri genelinde ScaleR paralelleştirilmiş dağıtılan işlevlerin çalıştırılmasına izin verir. Ayrıca, ScaleR 'nın Hadoop Haritası ' nı kullanarak bunları kümenin düğümleri arasında çalıştırabilirsiniz. Apache Spark işlem bağlamlarını da kullanabilirsiniz.

Analizden kaynaklanan modeller veya tahminler, şirket içi kullanım için indirilebilirler. `operationalized`Azure 'da başka bir yerde de olabilirler. Özellikle, [Azure Machine Learning Studio (klasik)](https://studio.azureml.net)ve [Web hizmeti](../../machine-learning/classic/deploy-a-machine-learning-web-service.md)aracılığıyla.

## <a name="get-started-with-ml-services-on-hdinsight"></a>HDInsight 'ta ML hizmetlerini kullanmaya başlama

HDInsight 'ta bir ML Hizmetleri kümesi oluşturmak için **ml Hizmetleri** küme türünü seçin. ML Hizmetleri küme türü, veri düğümleri ve kenar düğümüne ML Server içerir. Edge düğümü, ML Hizmetleri tabanlı analizler için bir giriş bölgesi görevi görür. Kümenin nasıl oluşturulacağı hakkında yönergeler için [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) konusuna bakın.

## <a name="why-choose-ml-services-in-hdinsight"></a>HDInsight 'ta neden ML Hizmetleri seçmeliyim?

HDInsight 'ta ML Hizmetleri aşağıdaki avantajları sağlar:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Microsoft ve açık kaynaklı AI yeniliği

  ML Hizmetleri, geri [alınamaz](/machine-learning-server/r-reference/revoscaler/revoscaler), geri [alınamaz, Iptal edilebilir](/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)ve [microsoftml](/machine-learning-server/python-reference/microsoftml/microsoftml-package)gibi dağıtılmış algoritmaların bir kümesini içerir. Bu algoritmalar, fiziksel belleğin boyutundan daha büyük olan veri boyutları üzerinde çalışabilir. Bunlar, dağıtılmış bir şekilde çok çeşitli platformlar üzerinde de çalışır. Microsoft 'un özel [R paketleri](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) ve ürünle birlikte bulunan [Python paketlerinin](/machine-learning-server/python-reference/introducing-python-package-reference) koleksiyonu hakkında daha fazla bilgi edinin.
  
  ML Hizmetleri, bu Microsoft yeniliklerini ve katkılarını açık kaynaklı topluluktan (R, Python ve AI araç takımları) köprüler. Tümü tek bir kurumsal sınıf platformun üzerine. Tüm R veya Python açık kaynaklı makine öğrenimi paketleri, Microsoft 'un herhangi bir özel yeniliği ile yan yana çalışabilir.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Basit, güvenli ve yüksek ölçekli işlemleştirme ve yönetim

  Geleneksel paradigmalarına ve ortamlara bağlı olan kuruluşlar, işlem için çok zaman ve çaba harcamaya yatırım sağlar. Bu eylem, aşağıdakiler için çeviri süresi dahil olmak üzere önceden yapılan maliyetlere ve gecikmelere neden olur: modeller, yineleme ve geçerli tutmak için yinelemeler, mevzuat onayı ve izinleri yönetme.

  ML hizmetleri kurumsal düzeyde [kullanım](/machine-learning-server/what-is-operationalization)sağlar. Machine Learning modeli tamamlandıktan sonra, Web Hizmetleri API 'Leri oluşturmak için yalnızca birkaç tıklama sürer. Bu [Web Hizmetleri](/machine-learning-server/operationalize/concept-what-are-web-services) , buluttaki bir sunucu kılavuzunda barındırılır ve iş kolu uygulamalarıyla tümleştirilebilir. Elastik bir kılavuza dağıtım özelliği, hem Batch hem de gerçek zamanlı Puanlama için işletmenizin ihtiyaçlarına sorunsuz şekilde ölçeklendirmenize olanak tanır. Yönergeler için bkz. [HDInsight ÜZERINDE ml Hizmetleri](r-server-operationalize.md)'ni kullanma.

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> HDInsight üzerinde ML Hizmetleri küme türü yalnızca HDInsight 3,6 ' de desteklenir. HDInsight 3,6, 31 Aralık 2020 tarihinde devre dışı bırakılacak şekilde zamanlandı.

## <a name="key-features-of-ml-services-on-hdinsight"></a>HDInsight üzerinde ML Hizmetleri 'nin temel özellikleri

Aşağıdaki özellikler, HDInsight üzerinde ML hizmetlerine eklenmiştir.

| Özellik kategorisi | Description |
|------------------|-------------|
| R-etkin | R 'de yazılmış çözümler için r ve betik yürütme için çalışma zamanı altyapısını içeren r için [r paketleri](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) . |
| Python etkin | Python 'da yazılmış çözümler için Python [modülleri](/machine-learning-server/python-reference/introducing-python-package-reference) , Python 'un açık kaynaklı bir dağıtımı ve betik yürütme için çalışma zamanı altyapısı.
| [Önceden eğitilen modeller](/machine-learning-server/install/microsoftml-install-pretrained-models) | Görsel analiz ve metin yaklaşımı analizi için sağladığınız verileri puanlamaya hazırlanın. |
| [Dağıtma ve kullanma](r-server-operationalize.md) | `Operationalize` sunucunuz ve Web hizmeti olarak çözüm dağıtın. |
| [Uzaktan yürütme](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | İstemci iş istasyonunuzdan ağınızdaki ML Hizmetleri kümesinde uzak oturumlar başlatın. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>HDInsight üzerinde ML Hizmetleri için veri depolama seçenekleri

Bir Azure depolama hesabı veya Azure Data Lake Storage, bu dosya sistemi için varsayılan depolama alanı olabilir. Analiz sırasında küme depolamaya karşıya yüklenen veriler kalıcı hale getirilir. Veriler, küme silindikten sonra bile kullanılabilir. Çeşitli araçlar, depolama alanına veri aktarımını işleyebilir. Araçlar, depolama hesabı ve AzCopy yardımcı programının portal tabanlı karşıya yükleme özelliğini içerir.

Küme oluşturma sırasında ek blob ve Data Lake mağazalarına erişimi etkinleştirebilirsiniz. Kullanılan birincil depolama seçeneğiyle sınırlı değilsiniz.  Birden çok depolama hesabı kullanma hakkında daha fazla bilgi edinmek için bkz. [HDInsight 'TA ml Hizmetleri Için Azure depolama seçenekleri](./r-server-storage.md) makalesi.

Azure dosyalarını, kenar düğümünde kullanmak üzere bir depolama seçeneği olarak da kullanabilirsiniz. Azure dosyaları, Azure depolama 'da oluşturulan dosya paylaşımlarının Linux dosya sistemine olanak sağlar. Daha fazla bilgi için bkz. [HDInsight 'TA ml Hizmetleri Için Azure depolama seçenekleri](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>ML Hizmetleri kenar düğümüne erişin

Bir tarayıcı veya SSH/PuTTY kullanarak kenar düğümündeki Microsoft ML Server 'a bağlanabilirsiniz. R konsolu, küme oluşturma sırasında varsayılan olarak yüklenir.  

## <a name="develop-and-run-r-scripts"></a>R betikleri geliştirme ve çalıştırma

R betikleriniz 8000 + açık kaynaklı R paketlerinden herhangi birini kullanabilir. Ayrıca, ScaleR kitaplığından paralelleştirilmiş ve dağıtılmış yordamları da kullanabilirsiniz. Edge düğümünde çalıştırılan betikler, söz konusu düğümdeki R yorumlayıcısı içinde çalışır. ScaleR işlevlerini eşleme ile çağıran adımlar dışında (RxHadoopMR) veya Spark (RxSpark) işlem bağlamını azaltır. İşlevler, verilerle ilişkili veri düğümleri arasında dağıtılmış bir biçimde çalışır. Bağlam seçenekleri hakkında daha fazla bilgi için bkz. [HDInsight 'TA ml Hizmetleri Için işlem bağlamı seçenekleri](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>`Operationalize` Model

Veri modellemesi tamamlandığında, `operationalize` Azure 'dan veya şirket içinde yeni veriler için tahmine dayalı hale getirmek üzere bir modeldir. Bu işlem Puanlama olarak bilinir. Puanlama, HDInsight, Azure Machine Learning veya şirket içinde yapılabilir.

### <a name="score-in-hdinsight"></a>HDInsight 'ta puan

HDInsight 'ta puan vermek için bir R işlevi yazın. İşlevi, depolama hesabınıza yüklediğiniz yeni bir veri dosyası için tahminleri yapmak üzere modelinizi çağırır. Ardından, tahminleri depolama hesabına geri kaydedin. Bu yordamı, kümenizin kenar düğümünde veya zamanlanan bir iş ile isteğe bağlı olarak çalıştırabilirsiniz.

### <a name="score-in-azure-machine-learning-aml"></a>Azure Machine Learning puan (AML)

Azure Machine Learning kullanarak puan almak için, modelinizi bir Azure Web hizmeti olarak yayımlamak üzere [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) olarak bilinen açık kaynaklı Azure Machine Learning R paketini kullanın. Kolaylık olması için, bu paket kenar düğümüne önceden yüklenmiştir. Daha sonra, Web hizmeti için bir kullanıcı arabirimi oluşturmak üzere Azure Machine Learning ' deki olanakları kullanın ve ardından Web hizmetini Puanlama için gereken şekilde çağırın. Ardından, Web hizmeti ile kullanmak üzere ScaleR model nesnelerini eşdeğer açık kaynaklı Model nesnelerine dönüştürün. `as.randomForest()`Bu dönüştürme için ensesıya tabanlı modeller gibi ScaleR zorlama işlevlerini kullanın.

### <a name="score-on-premises"></a>Şirket içi puan

Modelinizi oluşturduktan sonra şirket içinde puan almak için: modeli R 'de serileştirme, indirme, serbest bırakma, seri hale getirme ve ardından yeni veri Puanlama için kullanma. Daha önce HDInsight 'ta veya [Web Hizmetleri](/machine-learning-server/operationalize/concept-what-are-web-services)'ni kullanarak, daha önce açıklanan yaklaşımı kullanarak yeni verileri puanlandırın.

## <a name="maintain-the-cluster"></a>Kümeyi koruma

### <a name="install-and-maintain-r-packages"></a>R paketlerini yükleyip bakımını yapma

R betiklerinizin birçok adımı üzerinde çalıştığı için, kullandığınız R paketlerinin çoğu Edge düğümünde gereklidir. Edge düğümüne ek R paketleri yüklemek için, bu `install.packages()` yöntemi R 'de kullanabilirsiniz.

Yalnızca ScaleR kitaplık yordamlarını kullanıyorsanız, genellikle ek R paketlerine gerek kalmaz. Veri düğümlerinde **Rxexec** veya **Rxdatastep** yürütmesi için ek paketlere ihtiyacınız bulunabilir.

Kümeyi oluşturduktan sonra ek paketler bir betik eylemiyle yüklenebilir. Daha fazla bilgi için bkz. [HDInsight KÜMESINDE ml hizmetlerini yönetme](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>MapReduce bellek ayarlarını Apache Hadoop değiştirme

ML Hizmetleri için kullanılabilir bellek, bir MapReduce işi çalıştırıldığında değiştirilebilir. Bir kümeyi değiştirmek için kümeniz için Apache ambarı Kullanıcı arabirimini kullanın. Ambarı Kullanıcı arabirimi yönergeleri için bkz. [ambarı Web Kullanıcı arabirimini kullanarak HDInsight kümelerini yönetme](../hdinsight-hadoop-manage-ambari.md).

ML Hizmetleri için kullanılabilir bellek, **RxHadoopMR** çağrısında bulunan Hadoop anahtarları kullanılarak değiştirilebilir:

```r
hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"
```

### <a name="scale-your-cluster"></a>Kümenizi ölçeklendirin

HDInsight 'ta mevcut bir ML Hizmetleri kümesi, Portal üzerinden yukarı veya aşağı ölçeklendirilebilir. Ölçeği yukarı ölçeklendirerek daha büyük işleme görevleri için ek kapasite elde edersiniz. Boşta kaldığında, bir kümeyi geri ölçeklendirebilirsiniz. Bir kümenin ölçeklendirilmesi hakkında yönergeler için bkz. [HDInsight kümelerini yönetme](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Sistemi koruma

İşletim sistemi bakımı, bir HDInsight kümesinde çalışan Linux VM 'lerinde, çalışma saatleri dışında yapılır. Genellikle, bakım her Pazartesi ve Perşembe 3:30 (VM 'nin yerel saati) ile yapılır. Güncelleştirmeler, kümenin bir çeyreğinin her seferinde fazlasını etkilemez.

Çalışan işler bakım sırasında yavaşlayabilir. Ancak, hala tamamlanmayı çalıştırmaları gerekir. Bir küme yeniden oluşturma gerektiren çok zararlı bir hata oluşmadığı takdirde, bu bakım olayları genelinde korunan özel yazılım veya yerel veriler.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>HDInsight üzerinde ML Hizmetleri için IDE seçenekleri

HDInsight kümesinin Linux Edge düğümü, R tabanlı analize yönelik giriş bölgesidir. HDInsight 'ın son sürümleri Edge düğümünde RStudio Server 'ın tarayıcı tabanlı bir IDE 'si sağlar. RStudio Server, geliştirme ve yürütme için R konsolundan daha üretken.

Masaüstü IDE, kümeye uzak MapReduce veya Spark işlem bağlamı aracılığıyla erişebilir. Seçenekler şunlardır: Microsoft 'un [Visual Studio için R araçları](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (rtvs), rstudio ve walware 'in tutulma tabanlı stateT.

Komut istemine **r** yazarak kenar düğümündeki r konsoluna erişin. Konsol arabirimini kullanırken, bir metin düzenleyicisinde R betiği geliştirmek kullanışlı olur. Ardından, gerekli olduğu gibi betiğinizin bölümlerini R konsoluna kesip yapıştırın.

## <a name="pricing"></a>Fiyatlandırma

Bir ML Hizmetleri HDInsight kümesiyle ilişkili fiyatlar, diğer HDInsight küme türlerine benzer şekilde yapılandırılmıştır. Bunlar, temel alınan VM 'lerin ad, veri ve kenar düğümleri genelinde boyutlandırılmasına dayalıdır. Çekirdek saat ön ek. Daha fazla bilgi için bkz. [HDInsight fiyatlandırması](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Sonraki adımlar

HDInsight kümelerinde ML Hizmetleri kullanma hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [RStudio Server kullanarak Azure HDInsight 'ta bir ML Hizmetleri kümesinde R betiği yürütme](machine-learning-services-quickstart-job-rstudio.md)
* [HDInsight üzerinde ML Services kümesi için işlem bağlamı seçenekleri](r-server-compute-contexts.md)
* [HDInsight üzerinde ML Hizmetleri kümesi için depolama seçenekleri](r-server-storage.md)