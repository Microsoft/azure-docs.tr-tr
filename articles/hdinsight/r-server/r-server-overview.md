---
title: Azure HDInsight 'ta ML hizmetlerine giriş
description: Büyük veri analizi için uygulamalar oluşturmak üzere HDInsight 'ta ML hizmetlerini nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/12/2019
ms.openlocfilehash: 782c860ca50c1ef12eda1ec33f29c5103b396349
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665297"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Azure HDInsight 'ta ML Hizmetleri nedir?

Microsoft Machine Learning Server, Azure 'da HDInsight kümeleri oluştururken dağıtım seçeneği olarak kullanılabilir. Bu seçeneği sağlayan küme türü **ml Hizmetleri**olarak adlandırılır. Bu özellik, HDInsight üzerinde ölçeklenebilir ve dağıtılmış analiz yöntemlerine isteğe bağlı erişim sağlayan veri bilimcileri, İstatistikçilerin ve R programcıları sağlar.

HDInsight üzerinde ML Hizmetleri, Azure Blob veya Data Lake depolama alanına yüklenmiş, neredeyse her boyuttaki veri kümelerinde R tabanlı analizler için en son özellikleri sağlar. ML Hizmetleri kümesi açık kaynak R üzerine inşa edildiğinden, oluşturduğunuz R tabanlı uygulamalar 8000 + açık kaynaklı R paketlerinden herhangi birini kullanabilir. ScaleR 'daki yordamlar, Microsoft 'un büyük veri analizi paketi de mevcuttur.

Bir kümenin kenar düğümü, kümeye bağlanmak ve R betiklerinizi çalıştırmak için uygun bir yer sağlar. Edge düğümü ile, kenar düğüm sunucusunun çekirdekleri arasında Koraler 'ın paralelleştirilmiş dağıtılmış işlevlerini çalıştırma seçeneğiniz vardır. Ayrıca, ScaleR 'ın Hadoop haritasını azaltma veya Apache Spark işlem bağlamlarını kullanarak bunları kümenin düğümleri arasında çalıştırabilirsiniz.

Analizden kaynaklanan modeller veya tahminler, şirket içi kullanım için indirilebilirler. Ayrıca, Azure 'da [Azure Machine Learning Studio (klasik)](https://studio.azureml.net) [Web hizmeti](../../machine-learning/studio/deploy-a-machine-learning-web-service.md)aracılığıyla başka bir yerde de gerçekleştirilebilir.

## <a name="get-started-with-ml-services-on-hdinsight"></a>HDInsight 'ta ML hizmetlerini kullanmaya başlama

Azure HDInsight 'ta bir ML Hizmetleri kümesi oluşturmak için, Azure portal kullanarak bir HDInsight kümesi oluştururken **ml Hizmetleri** küme türünü seçin. ML Hizmetleri küme türü, kümenin veri düğümlerinde ve ML Hizmetleri tabanlı analizler için bir giriş bölgesi görevi gören bir kenar düğümünde ML Server içerir. Kümenin nasıl oluşturulacağı hakkında yönergeler için [Azure Portal kullanarak Apache Hadoop kümeleri oluşturma](../hdinsight-hadoop-create-linux-clusters-portal.md) konusuna bakın.

## <a name="why-choose-ml-services-in-hdinsight"></a>HDInsight 'ta neden ML Hizmetleri seçmeliyim?

HDInsight 'ta ML Hizmetleri aşağıdaki avantajları sağlar:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Microsoft ve açık kaynaklı AI yeniliği

  ML Hizmetleri, fiziksel belleğin boyutundan daha [büyük olan veri](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) boyutları üzerinde çalışabilen ve ' deki çok çeşitli platformlar üzerinde çalışabilen, geri [alınamaz](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), geri alınamaz, [iptal](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)edilebilir ve bir dağıtılmış şekilde. Microsoft 'un özel [R paketleri](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) ve ürünle birlikte bulunan [Python paketlerinin](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) koleksiyonu hakkında daha fazla bilgi edinin.
  
  ML Hizmetleri, bu Microsoft yeniliklerini ve katkılarını açık kaynaklı topluluktan (R, Python ve AI araç takımları), hepsi de tek bir kurumsal sınıf platformun üzerine köprüler. Tüm R veya Python açık kaynaklı makine öğrenimi paketleri, Microsoft 'un herhangi bir özel yeniliği ile yan yana çalışabilir.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Basit, güvenli ve yüksek ölçekli işlemleştirme ve yönetim

  Geleneksel paradigmalarına ve ortamlara bağlı olan kuruluşlar, işlem için çok zaman ve çaba harcamaya yatırım sağlar. Bu durum, modellerle ilgili çeviri süresi, geçerli ve geçerli, mevzuata onay ve operationalization aracılığıyla izinleri yönetme gibi maliyetler ve gecikmelerle sonuçlanır.

  ML hizmetleri kurumsal düzeyde [kullanım](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)sağlar. Bu, bir makine öğrenimi modeli tamamlandığında, Web Hizmetleri API 'leri oluşturmak için yalnızca birkaç tıklamayla yararlanır. Bu [Web Hizmetleri](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) , buluttaki bir sunucu kılavuzunda barındırılır ve iş kolu uygulamalarıyla tümleştirilebilir. Elastik bir kılavuza dağıtım özelliği, hem Batch hem de gerçek zamanlı Puanlama için işletmenizin ihtiyaçlarına sorunsuz şekilde ölçeklendirmenize olanak tanır. Yönergeler için bkz. [HDInsight ÜZERINDE ml Hizmetleri](r-server-operationalize.md)'ni kullanma.

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>HDInsight üzerinde ML Hizmetleri 'nin temel özellikleri

Aşağıdaki özellikler, HDInsight üzerinde ML hizmetlerine eklenmiştir.

| Özellik kategorisi | Açıklama |
|------------------|-------------|
| R-etkin | R 'de yazılmış çözümler için r [paketleri](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) ve betik yürütme için çalışma zamanı altyapısının bulunduğu r. |
| Python etkin | Python 'da yazılmış çözümler için Python [modülleri](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) , Python 'un açık kaynaklı bir dağıtımı ve betik yürütme için çalışma zamanı altyapısı.
| [Önceden eğitilen modeller](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Görsel analiz ve metin yaklaşımı analizi için sağladığınız verileri puanlamaya hazırlanın. |
| [Dağıtma ve kullanma](r-server-operationalize.md) | Sunucunuzu çalıştırın ve Web hizmeti olarak çözüm dağıtın. |
| [Uzaktan yürütme](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | İstemci iş istasyonunuzdan ağınızdaki ML Hizmetleri kümesinde uzak oturumlar başlatın. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>HDInsight üzerinde ML Hizmetleri için veri depolama seçenekleri

HDInsight kümelerinin varsayılan depolama alanı, Azure depolama hesabıyla veya bir Azure Data Lake Storage ilişkilendirilebilir. Bu ilişki, analiz sırasında küme depolamaya hangi verilerin yüklendiğini ve küme silindikten sonra bile verilerin kullanılabilir olmasını sağlar. Depolama hesabının ve [AzCopy](../../storage/common/storage-use-azcopy.md) yardımcı programının portal tabanlı karşıya yükleme özelliği de dahil olmak üzere, seçtiğiniz depolama seçeneğine veri aktarımını işlemeye yönelik çeşitli araçlar vardır.

Kullanılan birincil depolama seçeneğinden bağımsız olarak, Küme sağlama işlemi sırasında ek blob ve Data Lake depolarına erişimi etkinleştirme seçeneğiniz vardır.  Birden çok depolama hesabı kullanma hakkında daha fazla bilgi edinmek için bkz. [HDInsight 'TA ml Hizmetleri Için Azure depolama seçenekleri](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) makalesi.

[Azure dosyalarını](../../storage/files/storage-how-to-use-files-linux.md) , kenar düğümünde kullanmak üzere bir depolama seçeneği olarak da kullanabilirsiniz. Azure dosyaları, Azure depolama 'da oluşturulmuş bir dosya paylaşımının Linux dosya sistemine takabilmesini sağlar. HDInsight kümesindeki ML Hizmetleri için bu veri depolama seçenekleri hakkında daha fazla bilgi için bkz. [HDInsight 'TA ml Hizmetleri Için Azure depolama seçenekleri](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>ML Hizmetleri kenar düğümüne erişin

Bir tarayıcı kullanarak Edge düğümündeki Microsoft ML Server 'a bağlanabilirsiniz. Küme oluşturma sırasında varsayılan olarak yüklenir.  Ayrıca, R konsoluna erişmek için SSH/PuTTY kullanarak komut satırından küme kenar düğümüne bağlanabilirsiniz.

## <a name="develop-and-run-r-scripts"></a>R betikleri geliştirme ve çalıştırma

Oluşturduğunuz ve çalıştırdığınız R betikleri, bir 8000 + açık kaynaklı R paketlerinden herhangi birini, ScaleR kitaplığındaki kullanılabilir paralelleştirilmiş ve dağıtılmış yordamlara ek olarak kullanabilir. Genel olarak, uç düğümünde ML Hizmetleri ile çalıştırılan bir betik, o düğümdeki R yorumlayıcısı içinde çalışır. Özel durumlar, Hadoop Haritası ' nı (RxHadoopMR) veya Spark (RxSpark) olarak ayarlanmış bir işlem bağlamı ile bir ScaleR işlevi çağırma ihtiyacı olan adımlardır. Bu durumda, işlev başvurulan verilerle ilişkili kümenin bu veri (görev) düğümleri arasında dağıtılmış bir biçimde çalışır. Farklı işlem bağlamı seçenekleri hakkında daha fazla bilgi için bkz. [HDInsight 'TA ml Hizmetleri Için işlem bağlamı seçenekleri](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Bir modeli kullanıma hazır hale getirme

Veri modellemesi tamamlandığında, Azure 'dan ya da şirket içinde yeni veriler için tahmine dayalı hale getirmek üzere modeli çalıştırabilirsiniz. Bu işlem Puanlama olarak bilinir. Puanlama, HDInsight, Azure Machine Learning veya şirket içinde yapılabilir.

### <a name="score-in-hdinsight"></a>HDInsight 'ta puan

HDInsight 'ta puan vermek için, depolama hesabınıza yüklediğiniz yeni bir veri dosyası için tahminleri yapmak üzere modelinize çağrı yapan bir R işlevi yazın. Ardından, tahminleri depolama hesabına geri kaydedin. Bu yordamı, kümenizin kenar düğümünde veya zamanlanan bir iş ile isteğe bağlı olarak çalıştırabilirsiniz.

### <a name="score-in-azure-machine-learning-aml"></a>Azure Machine Learning puan (AML)

Azure Machine Learning kullanarak puan almak için, modelinizi bir Azure Web hizmeti olarak yayımlamak üzere [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) olarak bilinen açık kaynaklı Azure Machine Learning R paketini kullanın. Kolaylık olması için, bu paket kenar düğümüne önceden yüklenmiştir. Daha sonra, Web hizmeti için bir kullanıcı arabirimi oluşturmak üzere Azure Machine Learning ' deki olanakları kullanın ve ardından Web hizmetini Puanlama için gereken şekilde çağırın.

Bu seçeneği belirlerseniz, herhangi bir ScaleR model nesnesini Web hizmeti ile kullanmak üzere eşdeğer açık kaynaklı Model nesnelerine dönüştürmeniz gerekir. Bu dönüştürme için, ensere tabanlı modeller için `as.randomForest()` gibi ScaleR zorlama işlevlerini kullanın.

### <a name="score-on-premises"></a>Şirket içi puan

Modelinizi oluşturduktan sonra şirket içinde puan almak için modeli R 'de seri hale getirebilirsiniz, indirebilir, seri durumdan çıkarılamıyor ve ardından yeni veri Puanlama için kullanabilirsiniz. Daha önce [HDInsight 'ta](#score-in-hdinsight) veya [Web Hizmetleri](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)'ni kullanarak, daha önce açıklanan yaklaşımı kullanarak yeni verileri puanlandırın.

## <a name="maintain-the-cluster"></a>Kümeyi koruma

### <a name="install-and-maintain-r-packages"></a>R paketlerini yükleyip bakımını yapma

R betiklerinizin birçok adımı üzerinde çalıştığı için, kullandığınız R paketlerinin çoğu Edge düğümünde gereklidir. Kenar düğümüne ek R paketleri yüklemek için R 'de `install.packages()` yöntemini kullanabilirsiniz.

Yalnızca küme genelinde ScaleR kitaplığından yordamlar kullanıyorsanız, genellikle veri düğümlerine ek R paketleri yüklemeniz gerekmez. Ancak, veri düğümlerinde **Rxexec** veya **Rxdatastep** yürütmesinin kullanımını desteklemek için ek paketlere ihtiyaç duyabilirsiniz.

Bu gibi durumlarda, kümeyi oluşturduktan sonra ek paketler bir betik eylemiyle yüklenebilir. Daha fazla bilgi için bkz. [HDInsight KÜMESINDE ml hizmetlerini yönetme](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>MapReduce bellek ayarlarını Apache Hadoop değiştirme

Bir küme, MapReduce işi çalıştırıldığında ML Hizmetleri için kullanılabilir bellek miktarını değiştirecek şekilde değiştirilebilir. Bir kümeyi değiştirmek için, kümenizin Azure portal dikey penceresinde bulunan Apache ambarı Kullanıcı arabirimini kullanın. Kümeniz için ambarı Kullanıcı arabirimine erişme hakkında yönergeler için, bkz. [Web arabirimi kullanarak HDInsight kümelerini yönetme](../hdinsight-hadoop-manage-ambari.md).

Ayrıca, **RxHadoopMR** çağrısında bulunan Hadoop ANAHTARLARıNı kullanarak ml Hizmetleri için kullanılabilir bellek miktarını aşağıdaki şekilde değiştirebilirsiniz:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Kümenizi ölçeklendirme

HDInsight 'ta mevcut bir ML Hizmetleri kümesi, Portal üzerinden yukarı veya aşağı ölçeklendirilebilir. Ölçeği büyüerek, daha büyük işlem görevleri için ihtiyaç duyduğunuz ek kapasiteyi elde edebilir veya boşta kaldığında bir kümeyi geri ölçeklendirebilirsiniz. Bir kümenin ölçeklendirilmesi hakkında yönergeler için bkz. [HDInsight kümelerini yönetme](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Sistemi koruma

İşletim sistemi düzeltme ekleri uygulama bakımı ve diğer güncelleştirmeler, bir HDInsight kümesindeki temel Linux VM 'lerinde, çalışma saatleri dışında gerçekleştirilir. Genellikle bakım, her Pazartesi ve Perşembe 3:30 (VM 'nin yerel saatine göre) ile yapılır. Güncelleştirmeler, kümenin bir çeyreğinin her seferinde daha fazlasını etkilemeyeceğinden bu şekilde gerçekleştirilir.

Baş düğümler gereksiz olduğundan ve tüm veri düğümleri etkilenmediğinden, bu süre boyunca çalışan tüm işler yavaşlayabilir. Ancak, hala tamamlanmayı çalıştırmaları gerekir. Bir küme yeniden oluşturma gerektiren çok zararlı bir hata oluşmadığı takdirde, sahip olduğunuz herhangi bir özel yazılım veya yerel veri, bu bakım olayları arasında korunur.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>HDInsight üzerinde ML Hizmetleri için IDE seçenekleri

HDInsight kümesinin Linux Edge düğümü, R tabanlı analize yönelik giriş bölgesidir. HDInsight 'ın son sürümleri, bir tarayıcı tabanlı IDE olarak uç düğümüne varsayılan bir RStudio Server yüklemesi sağlar. R betiklerinin geliştirilmesi ve yürütülmesi için IDE olarak RStudio Server kullanımı, yalnızca R konsolu kullanmaktan çok daha üretken olabilir.

Ayrıca, bir masaüstü IDE yükleyebilir ve bunu, uzak MapReduce veya Spark işlem bağlamını kullanarak kümeye erişmek için kullanabilirsiniz. Seçenekler Microsoft 'un [Visual Studio için R araçları](https://www.visualstudio.com/features/rtvs-vs.aspx) (rtvs), rstudio ve walware 'in tutulma tabanlı [stateT](http://www.walware.de/goto/statet)' i içerir.

Ayrıca, SSH veya PuTTY aracılığıyla bağlandıktan sonra, Linux komut isteminde **r** yazarak r konsoluna da erişebilirsiniz. Konsol arabirimini kullanırken, R betiği geliştirmesi için bir metin düzenleyicisini başka bir pencerede çalıştırmak ve gerekirse betiğinizin bölümlerini R konsoluna kesmek ve yapıştırmak kullanışlıdır.

## <a name="pricing"></a>Fiyatlandırma

Bir ML Hizmetleri HDInsight kümesiyle ilişkili fiyatlar, diğer HDInsight küme türleri için fiyatlara benzer şekilde yapılandırılır. Bunlar, temel alınan VM 'lerin ad, veri ve kenar düğümleri genelinde boyutlandırılmasına dayalıdır ve bu da çekirdek saatlik bir yükseltme eklenmesiyle yapılır. Daha fazla bilgi için bkz. [HDInsight fiyatlandırması](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Sonraki adımlar

HDInsight kümelerinde ML Hizmetleri kullanma hakkında daha fazla bilgi edinmek için aşağıdaki konulara bakın:

* [RStudio Server kullanarak Azure HDInsight 'ta bir ML Hizmetleri kümesinde R betiği yürütme](machine-learning-services-quickstart-job-rstudio.md)
* [HDInsight üzerinde ML Services kümesi için işlem bağlamı seçenekleri](r-server-compute-contexts.md)
* [HDInsight üzerinde ML Hizmetleri kümesi için depolama seçenekleri](r-server-storage.md)
