---
title: Mimari & temel kavramlar
titleSuffix: Azure Machine Learning
description: Bu makale, Azure Machine Learning oluşturan mimari, hüküm ve kavramların yüksek düzeyde bir şekilde anlaşılmasına olanak tanır.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: f1eb7a5b4697801775d23091c610ab594b0b27ec
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813389"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Azure Machine Learning nasıl kullanılır: mimari ve kavramlar

[Azure Machine Learning](overview-what-is-azure-ml.md)için mimari ve kavramlar hakkında bilgi edinin.  Bu makale, bileşenleri ve makine öğrenimi modellerini oluşturma, dağıtma ve sürdürme sürecinde yardımcı olmak üzere nasıl birlikte çalıştıkları hakkında daha fazla bilgi sahibi olmanızı sağlar.

## <a name="workspace"></a><a name="workspace"></a> Alanında

[Machine Learning çalışma alanı](concept-workspace.md) , Azure Machine Learning için en üst düzey kaynaktır.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Diyagram: bir çalışma alanının ve bileşenlerinin Azure Machine Learning mimarisi":::

Çalışma alanı, şunları yapmak için merkezi yerdir:

* Modellerle ilgili eğitim ve dağıtım için kullandığınız kaynakları yönetme (örneğin, [hesaplar](#compute-instance) )
* Azure Machine Learning kullandığınızda oluşturduğunuz varlıkları depolama, aşağıdakiler dahil:
  * [Ortamlar](#environments)
  * [Denemeler](#experiments)
  * [Pipelines](#ml-pipelines)
  * [Veri kümeleri](#datasets-and-datastores)
  * [Modeller](#models)
  * [Uç Noktalar](#endpoints)

Çalışma alanı, çalışma alanı tarafından kullanılan diğer Azure kaynaklarını içerir:

+ [Azure Container Registry (ACR)](https://azure.microsoft.com/services/container-registry/): eğitim sırasında ve bir modeli dağıtırken kullandığınız Docker kapsayıcılarını kaydeder. Maliyetleri en aza indirmek için ACR yalnızca dağıtım görüntüleri oluşturulduğunda oluşturulur.
+ [Azure depolama hesabı](https://azure.microsoft.com/services/storage/): çalışma alanı için varsayılan veri deposu olarak kullanılır.  Azure Machine Learning işlem örneklerinizde kullanılan jupi Not defterleri burada da depolanır.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): modelleriniz hakkında izleme bilgilerini depolar.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): işlem hedefleri ve çalışma alanı için gereken diğer hassas bilgiler tarafından kullanılan gizli dizileri depolar.

Çalışma alanını başkalarıyla paylaşabilirsiniz.

## <a name="computes"></a>Karmasını

<a name="compute-targets"></a>[İşlem hedefi](concept-compute-target.md) , eğitim betiğinizi çalıştırmak veya hizmet dağıtımınızı barındırmak için kullandığınız herhangi bir makine veya makine kümesidir. Yerel makinenizi veya uzak bir işlem kaynağını işlem hedefi olarak kullanabilirsiniz.  İşlem hedefleri sayesinde, yerel makinenizde eğitime başlayabilir ve ardından eğitim betiğinizi değiştirmeden buluta ölçeklendirebilirsiniz.

Azure Machine Learning, makine öğrenimi görevleri için yapılandırılmış iki tam olarak yönetilen bulut tabanlı sanal makine (VM) sunar:

* <a name="compute-instance"></a>**İşlem örneği**: işlem örneği makine öğrenimi için yüklenen birden çok araç ve ortamı IÇEREN bir sanal makinedir. Bir işlem örneğinin birincil kullanımı geliştirme iş istasyonunuz içindir.  Örnek not defterlerini, kurulum gerekmeden çalıştırmaya başlayabilirsiniz. Bir işlem örneği, eğitim ve ınişsiz işler için işlem hedefi olarak da kullanılabilir.

* **İşlem kümeleri**: işlem kümeleri, çok düğümlü ölçeklendirme özelliklerine sahip VM 'lerin bir kümesidir. Bilgi işlem kümeleri, büyük işler ve üretim için işlem hedefleri için daha uygundur.  Bir iş gönderildiğinde küme otomatik olarak ölçeklendirilir.  Eğitim işlem hedefi olarak veya geliştirme/test dağıtımı için kullanın.

Eğitim işlem hedefleri hakkında daha fazla bilgi için bkz. [eğitim işlem hedefleri](concept-compute-target.md#train).  Dağıtım işlem hedefleri hakkında daha fazla bilgi için bkz. [dağıtım hedefleri](concept-compute-target.md#deploy).

## <a name="datasets-and-datastores"></a>Veri kümeleri ve veri depoları

[**Azure Machine Learning veri kümeleri**](concept-data.md#datasets)  , verilere erişmeyi ve verilerinize çalışmayı kolaylaştırır. Bir veri kümesi oluşturarak, veri kaynağı konumuna, meta verilerinin bir kopyasıyla birlikte bir başvuru oluşturursunuz. Veriler mevcut konumunda kaldığı için ek depolama ücreti ödemeniz ve veri kaynaklarınızın bütünlüğünü riske çıkarmazsınız.

Daha fazla bilgi için bkz. [Azure Machine Learning veri kümeleri oluşturma ve kaydetme](how-to-create-register-datasets.md).  Veri kümelerini kullanarak daha fazla örnek için bkz. [örnek Not defterleri](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Veri kümeleri, Azure depolama hizmetlerine güvenli bir şekilde bağlanmak için [veri depolarını](concept-data.md#datastores) kullanır. Veri, kimlik doğrulama kimlik bilgilerinizi ve risk altındaki özgün veri kaynağınızın bütünlüğünü yapmadan mağaza bağlantı bilgilerini depolar. Bu kişiler, çalışma alanıyla ilişkili Key Vault abonelik KIMLIĞINIZ ve belirteç yetkilendirmesi gibi bağlantı bilgilerini depolar, böylece depolama alanınıza güvenli bir şekilde kod yazmanız gerekmeden depolamaya güvenle erişebilirsiniz.

## <a name="environments"></a>Ortamlar

[Çalışma alanı](#workspace)  >  **Ortamlar**

[Ortam](concept-environments.md) , makine öğrenimi modelinizin eğitiminin veya Puanlarınızın gerçekleştiği ortamın kapsüllenmesi. Ortam, eğitim ve Puanlama betikleriniz etrafında Python paketlerini, ortam değişkenlerini ve yazılım ayarlarını belirtir.  

Kod örnekleri için, [ortamları kullanma](how-to-use-environments.md#manage-environments)konusunun "ortamları yönetme" bölümüne bakın.

## <a name="experiments"></a>Denemeler

[Çalışma alanı](#workspace)  >  **Denemeleri**

Deneme, belirtilen bir betikten birçok çalıştırma gruplandırmasıdır. Her zaman bir çalışma alanına aittir. Bir çalıştırma gönderdiğinizde, bir deneme adı sağlarsınız. Çalıştırma bilgileri bu deneme kapsamında saklanır. Bir deneme gönderdiğinizde ad yoksa, otomatik olarak yeni bir deneme oluşturulur.
  
Deneme kullanmanın bir örneği için bkz. [öğretici: İlk modelinize eğitme](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Çalıştırmalar

[Çalışma alanı](#workspace)  >  [Denemeleri](#experiments)  >  **Çalıştır**

Bir çalıştırma, bir eğitim betiğinin tek yürütülmesinden oluşur. Deneme genellikle birden çok çalıştırma içerir.

Azure Machine Learning tüm çalıştırmaları kaydeder ve aşağıdaki bilgileri denemenize depolar:

* Çalıştırma (zaman damgası, süre vb.) için meta veriler
* Betiğinizin günlüğe kaydettiği ölçümler
* Deneme tarafından oto tarafından toplanan ve sizin tarafınızdan açıkça yüklenen çıkış dosyaları
* Çalıştırmadan önce betiklerinizi içeren dizinin bir anlık görüntüsü

Bir modeli eğitme için bir komut dosyası gönderdiğinizde bir çalıştırma oluşturursunuz. Bir çalıştırmanın sıfır veya daha fazla alt çalıştırması olabilir. Örneğin, en üst düzey çalıştırmanın iki alt çalıştırması olabilir ve bunların her biri kendi alt öğesi olabilir.

### <a name="run-configurations"></a>Çalıştırma yapılandırması

[Çalışma alanı](#workspace)  >  [Denemeleri](#experiments)  >  [Çalıştır](#runs)  >  **Yapılandırmayı Çalıştır**

Çalıştırma yapılandırması, bir betiğin belirtilen işlem hedefinde nasıl çalıştırılacağını tanımlar. Betiği, üzerinde çalıştırılacak işlem hedefini ve Azure ML ortamını, dağıtılmış işe özgü yapılandırmaları ve bazı ek özellikleri belirtmek için yapılandırmayı kullanabilirsiniz. Çalışma için yapılandırılabilir seçeneklerin tam kümesi hakkında daha fazla bilgi için bkz. [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig).

Çalışma yapılandırması, eğitim betiğinizi içeren dizin içindeki bir dosyaya kalıcı yapılabilir.   Ya da bellek içi bir nesne olarak oluşturulabilir ve bir çalıştırma göndermek için kullanılabilir.

Örneğin, çalıştırma yapılandırması için bkz. [bir eğitim çalıştırmasını yapılandırma](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Anlık Görüntüler

[Çalışma alanı](#workspace)  >  [Denemeleri](#experiments)  >  [Çalıştır](#runs)  >  **Anlık görüntü**

Bir çalıştırma gönderdiğinizde, Azure Machine Learning betiği içeren dizini zip dosyası olarak sıkıştırır ve işlem hedefine gönderir. ZIP dosyası daha sonra ayıklanır ve betik burada çalıştırılır. Azure Machine Learning Ayrıca ZIP dosyasını çalışma kaydının bir parçası olarak bir anlık görüntü olarak depolar. Çalışma alanına erişimi olan herkes bir çalıştırma kaydına gözatabilir, anlık görüntüyü indirebilir.

### <a name="logging"></a>Günlüğe Kaydetme

Azure Machine Learning, standart çalıştırma ölçümlerini sizin için otomatik olarak günlüğe kaydeder. Ancak, [isteğe bağlı ölçümleri günlüğe kaydetmek Için Python SDK 'sını](how-to-log-view-metrics.md)de kullanabilirsiniz.

Günlüklerinizi görüntülemenin birden çok yolu vardır: çalışma durumunu gerçek zamanlı olarak izleme veya tamamlandığında sonuçları görüntüleme. Daha fazla bilgi için bkz. [ml çalıştırma günlüklerini izleme ve görüntüleme](how-to-log-view-metrics.md).


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git izleme ve Tümleştirme

Kaynak dizinin yerel bir git deposu olduğu bir eğitim çalıştırması başlattığınızda, depo hakkındaki bilgiler çalıştırma geçmişinde depolanır. Bu, komut dosyası çalıştırma yapılandırması veya ML işlem hattı kullanılarak gönderilen çalışmalarla birlikte çalışır. Ayrıca SDK veya Machine Learning CLı 'dan gönderilen çalıştırmalar için de çalışır.

Daha fazla bilgi için bkz. [Azure Machine Learning Için git tümleştirmesi](concept-train-model-git-integration.md).

### <a name="training-workflow"></a>Eğitim iş akışı

Bir modeli eğitemek için bir deneme çalıştırdığınızda aşağıdaki adımlar gerçekleşir. Bunlar aşağıdaki eğitim iş akışı diyagramında gösterilmektedir:

* Azure Machine Learning, önceki bölümde kaydedilen kod anlık görüntüsünün anlık görüntü KIMLIĞIYLE çağırılır.
* Azure Machine Learning, bir çalıştırma KIMLIĞI (isteğe bağlı) ve Machine Learning bir hizmet belirteci oluşturur. Bu, daha sonra Machine Learning hizmetiyle iletişim kurmak için Machine Learning İşlem/VM gibi işlem hedefleri tarafından daha sonra kullanılır.
* Eğitim işlerini çalıştırmak için yönetilen bir işlem hedefi (Machine Learning İşlem gibi) veya yönetilmeyen bir işlem hedefi (VM 'Ler gibi) seçebilirsiniz. Her iki senaryo için de veri akışları aşağıda verilmiştir:
   * Microsoft aboneliğindeki bir anahtar kasasında SSH kimlik bilgileri tarafından erişilen VM 'Ler/HDInsight. Azure Machine Learning, işlem hedefinde yönetim kodu çalıştırır:

   1. Ortamı hazırlar. (Docker, VM 'Ler ve yerel bilgisayarlar için bir seçenektir. Docker kapsayıcılarındaki denemeleri 'ın nasıl çalıştığını anlamak için Machine Learning İşlem için aşağıdaki adımlara bakın.)
   1. Kodu indirir.
   1. Ortam değişkenlerini ve konfigürasyonları ayarlar.
   1. Kullanıcı komut dosyalarını çalıştırır (önceki bölümde bahsedilen kod anlık görüntüsü).

   * Machine Learning İşlem, çalışma alanı tarafından yönetilen bir kimlikle erişilir.
Machine Learning İşlem yönetilen bir işlem hedefi olduğundan (Microsoft tarafından yönetilen), Microsoft Aboneliğiniz kapsamında çalışır.

   1. Gerekirse uzak Docker oluşturma kapalıdır.
   1. Yönetim kodu kullanıcının Azure dosya paylaşımında yazılır.
   1. Kapsayıcı bir başlangıç komutuyla başlatılır. Diğer bir deyişle, önceki adımda açıklandığı gibi yönetim kodu.

* Çalıştırma tamamlandıktan sonra, çalıştırmaları ve ölçümleri sorgulayabilirsiniz. Aşağıdaki akış diyagramında, bu adım, eğitim işlem hedefi Cosmos DB veritabanındaki depolamadan Azure Machine Learning için yeniden çalıştırma ölçümleri yazdığında meydana gelir. İstemciler, Azure Machine Learning çağırabilir. Machine Learning, Cosmos DB veritabanından çekme ölçümlerini açıp istemciye geri döndürmeyecektir.

[![Eğitim iş akışı](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>Modeller

En basit olan model, bir girişi alan ve çıkış üreten bir kod parçasıdır. Machine Learning modeli oluşturmak, bir algoritma seçmeyi, verileri verilerle sağlamayı ve [hiper parametreleri ayarlamayı](how-to-tune-hyperparameters.md)içerir. Eğitim, eğitilen bir model üreten ve eğitim sürecinde modelin öğrendiği bir işlem olan yinelemeli bir işlemdir.

Azure Machine Learning dışında eğitilen bir modeli getirebilirsiniz. Ya da Azure Machine Learning bir [işlem hedefine](#compute-targets) bir [deneme](#experiments) [çalıştırması](#runs) göndererek modeli eğitebilirsiniz. Bir modelinize sahip olduktan sonra modeli çalışma alanına [kaydedersiniz](#register-model) .

Azure Machine Learning çerçeve agstik. Bir model oluşturduğunuzda, Scikit-öğren, XGBoost, PyTorch, TensorFlow ve Chainer gibi popüler Machine Learning çerçevesini kullanabilirsiniz.

Scikit-öğrenme kullanarak bir modele eğitim verme örneği için bkz. [öğretici: görüntü sınıflandırma modelini Azure Machine Learning Ile eğitme](tutorial-train-models-with-aml.md).


### <a name="model-registry"></a><a name="register-model"></a> Model kayıt defteri

[Çalışma alanı](#workspace)  >  **Modeller**

**Model kayıt defteri** , Azure Machine Learning çalışma alanınızdaki tüm modelleri izlemenize olanak sağlar.

Modeller ad ve sürüm ile tanımlanır. Varolan bir adla aynı ada sahip bir modeli her kaydettiğinizde kayıt defteri yeni bir sürüm olduğunu varsayar. Sürüm artırılır ve yeni model aynı adla kaydedilir.

Modeli kaydettiğinizde, ek meta veri etiketleri sağlayabilir ve ardından modeller ararken etiketleri kullanabilirsiniz.

> [!TIP]
> Kayıtlı bir model, modelinizi oluşturan bir veya daha fazla dosya için mantıksal bir kapsayıcıdır. Örneğin, birden çok dosyada depolanan bir modeliniz varsa, bunları Azure Machine Learning çalışma alanınızda tek bir model olarak kaydedebilirsiniz. Kayıttan sonra, kayıtlı modeli indirebilir veya dağıtabilir ve kayıtlı tüm dosyaları alabilirsiniz.

Etkin bir dağıtım tarafından kullanılan kayıtlı bir modeli silemezsiniz.

Bir modeli kaydetme örneği için bkz. [Azure Machine Learning görüntü sınıflandırma modelini eğitme](tutorial-train-models-with-aml.md).

## <a name="deployment"></a>Dağıtım

[Kayıtlı bir modeli](#register-model) hizmet uç noktası olarak dağıtırsınız. Aşağıdaki bileşenlere ihtiyacınız vardır:

* **Ortam**. Bu ortam, modelinizi çıkarım için çalıştırmak için gereken bağımlılıkları kapsüller.
* **Puanlama kodu**. Bu betik istekleri kabul eder, modeli kullanarak istekleri puan eder ve sonuçları döndürür.
* **Çıkarım yapılandırması**. Çıkarım yapılandırması, modeli bir hizmet olarak çalıştırmak için gereken ortamı, giriş betiğini ve diğer bileşenleri belirtir.

Bu bileşenler hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md).

### <a name="endpoints"></a>Uç Noktalar

[Çalışma alanı](#workspace)  >  **Uç noktalar**

Bir uç nokta, modelinize veya tümleşik cihaz dağıtımları için bir IoT modülüne bir Web hizmetine barındırılabilir.

#### <a name="web-service-endpoint"></a>Web hizmeti uç noktası

Bir modeli Web hizmeti olarak dağıtırken, uç nokta Azure Container Instances, Azure Kubernetes hizmeti veya FPGAs üzerinde dağıtılabilir. Hizmeti modelinizde, betiğinizden ve ilişkili dosyalardan oluşturursunuz. Bunlar, modelin yürütme ortamını içeren bir temel kapsayıcı görüntüsüne yerleştirilir. Görüntüde, Web hizmetine gönderilen Puanlama isteklerini alan, yük dengeli bir HTTP uç noktası bulunur.

Web hizmetinizi izlemek için telemetri veya model telemetrisini Application Insights sağlayabilirsiniz. Telemetri verilerine yalnızca sizin erişebilirsiniz.  Application Insights ve depolama hesabı örneklerinizin içinde depolanır. Otomatik ölçeklendirmeyi etkinleştirdiyseniz, Azure dağıtımınızı otomatik olarak ölçeklendirir.

Aşağıdaki diyagramda, Web hizmeti uç noktası olarak dağıtılan bir model için çıkarım iş akışı gösterilmektedir:

Ayrıntılar aşağıda verilmiştir:

* Kullanıcı, Azure Machine Learning SDK gibi bir istemciyi kullanarak bir modeli kaydeder.
* Kullanıcı bir model, bir puan dosyası ve diğer model bağımlılıklarını kullanarak bir görüntü oluşturur.
* Docker görüntüsü oluşturulup Azure Container Registry depolanır.
* Web hizmeti, önceki adımda oluşturulan görüntüyü kullanarak işlem hedefine (Container Instances/AKS) dağıtılır.
* Puanlama isteği ayrıntıları, Kullanıcı aboneliğindeki Application Insights depolanır.
* Telemetri ayrıca Microsoft/Azure aboneliğine de gönderilir.

[![Çıkarım iş akışı](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


Bir modeli Web hizmeti olarak dağıtmaya ilişkin bir örnek için, bkz. [Azure Container Instances bir görüntü sınıflandırma modeli dağıtma](tutorial-deploy-models-with-aml.md).

#### <a name="real-time-endpoints"></a>Gerçek zamanlı uç noktalar

Tasarımcıda eğitilen bir model dağıttığınızda, [modeli gerçek zamanlı bir uç nokta olarak dağıtabilirsiniz](tutorial-designer-automobile-price-deploy.md). Gerçek zamanlı bir uç nokta, genellikle REST uç noktası aracılığıyla tek bir istek alır ve gerçek zamanlı bir tahmin döndürür. Bu, aynı anda birden çok değeri işleyen ve bir veri deposuna tamamlandığında sonuçları kaydeden toplu işleme karşıtlığına karşılık gelir.

#### <a name="pipeline-endpoints"></a>Ardışık düzen uç noktaları

Ardışık düzen uç noktaları, bir REST uç noktası aracılığıyla [ml](#ml-pipelines) işlem hattı program aracılığıyla aramanızı sağlar. Ardışık düzen uç noktaları, işlem hattı iş akışlarınızı otomatikleştirmenizi sağlar.

Ardışık düzen uç noktası yayımlanmış işlem hatları koleksiyonudur. Bu mantıksal kuruluş aynı uç noktayı kullanarak birden çok işlem hattını yönetmenizi ve çağırmanızı sağlar. Bir ardışık düzen uç noktasındaki yayımlanan her bir ardışık düzen sürümlüdür. Uç nokta için varsayılan bir işlem hattı seçebilir veya REST çağrısında bir sürüm belirtebilirsiniz.
 

#### <a name="iot-module-endpoints"></a>IoT modülü uç noktaları

Dağıtılan bir IoT modülü uç noktası, modelinizi ve ilişkili betiği ya da uygulamanızı ve ek bağımlılıkları içeren bir Docker kapsayıcısıdır. Bu modülleri, uç cihazlarda Azure IoT Edge kullanarak dağıtırsınız.

İzlemeyi etkinleştirdiyseniz, Azure Azure IoT Edge modülünün içindeki modelden telemetri verileri toplar. Telemetri verilerine yalnızca sizin için erişilebilir ve depolama hesabı Örneğinizde depolanır.

Azure IoT Edge modülünüzün çalıştığından emin olur ve bunu barındıran cihazı izler. 
## <a name="automation"></a>Otomasyon

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

[Azure MACHINE LEARNING CLI](reference-azure-machine-learning-cli.md) , Azure platformu için platformlar arası komut satırı arabirimi olan Azure CLI 'nin bir uzantısıdır. Bu uzantı, makine öğrenimi etkinliklerinizi otomatikleştirmek için komutlar sağlar.

### <a name="ml-pipelines"></a>ML işlem hatları

Machine Learning işlem [hatlarını](concept-ml-pipelines.md) kullanarak makine öğrenimi aşamalarını birlikte akışlı iş akışları oluşturabilir ve yönetebilirsiniz. Örneğin, bir işlem hattı veri hazırlığı, model eğitimi, model dağıtımı ve çıkarım/Puanlama aşamaları içerebilir. Her aşama, her biri çeşitli işlem hedeflerinde katılımsız olarak çalışabilen birden çok adımı kapsayabilir. 

İşlem hattı adımları yeniden kullanılabilir ve bu adımların çıkışı değişmemişse önceki adımları yeniden çalıştırmadan çalıştırılabilir. Örneğin, veriler değişmemişse maliyetli veri hazırlama adımlarını yeniden çalıştırmadan bir modeli yeniden eğitebilirsiniz. İşlem hatları, makine öğrenimi iş akışının ayrı alanlarında çalışırken veri bilimcilerinin işbirliği yapmasına de olanak tanır.

## <a name="monitoring-and-logging"></a>İzleme ve günlüğe kaydetme

Azure Machine Learning aşağıdaki izleme ve günlüğe kaydetme yeteneklerini sağlar:

* __Veri bilimcileri__ için denemeleri ve günlük bilgilerinizi eğitim çalışmalarından izleyebilirsiniz. Daha fazla bilgi için aşağıdaki makaleleri inceleyin:
   * [Eğitim çalıştırmalarını başlatın, izleyin ve iptal edin](how-to-track-monitor-analyze-runs.md)
   * [Eğitim çalıştırmaları için günlük ölçümleri](how-to-log-view-metrics.md)
   * [MLflow ile denemeleri izleme](how-to-use-mlflow.md)
   * [TensorBoard ile çalıştırmaları görselleştirme](how-to-monitor-tensorboard.md)
* __Yöneticiler__ Için, Azure izleyici kullanarak çalışma alanı, ilgili Azure kaynakları ve kaynak oluşturma ve silme gibi olaylar hakkındaki bilgileri izleyebilirsiniz. Daha fazla bilgi için bkz. [izleme Azure Machine Learning](monitor-azure-machine-learning.md).
* __DevOps__ veya __mlops__ için, dağıtımlarla ilgili sorunları belirlemek ve hizmete gönderilen verileri toplamak üzere Web Hizmetleri veya IoT Edge modülleri olarak dağıtılan modeller tarafından oluşturulan bilgileri izleyebilirsiniz. Daha fazla bilgi için bkz. [model verilerini toplama](how-to-enable-data-collection.md) ve [Application Insights ile izleme](how-to-enable-app-insights.md).

## <a name="interacting-with-your-workspace"></a>Çalışma alanınız ile etkileşim kurma

### <a name="studio"></a>Studio

[Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) , çalışma alanınızdaki tüm yapıtların bir web görünümünü sağlar.  Veri kümelerinizin, denemeleri, işlem hatlarının, modellerinizin ve uç noktaların sonuçlarını ve ayrıntılarını görüntüleyebilirsiniz.  Ayrıca, Studio 'daki işlem kaynaklarını ve veri depolarını yönetebilirsiniz.

Ayrıca, Azure Machine Learning parçası olan etkileşimli araçlara de erişebilirsiniz:

+ Kod yazmadan iş akışı adımlarını gerçekleştirmek için [tasarımcı Azure Machine Learning](concept-designer.md)
+ [Otomatik makine öğrenimi](concept-automated-ml.md) için Web deneyimi
+ Tümleşik Jupyter Not defteri sunucularında kendi kodunuzu yazmak ve çalıştırmak için [not defterlerini Azure Machine Learning](how-to-run-jupyter-notebooks.md) .
+ Verilerinizi etiketleyerek projeler oluşturmak, yönetmek ve izlemek için [veri etiketleme projeleri](how-to-create-labeling-projects.md)

### <a name="programming-tools"></a>Programlama araçları

> [!IMPORTANT]
> Aşağıda işaretlenmiş araçlar (Önizleme) Şu anda genel önizlemede.
> Önizleme sürümü, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  [Python için Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro)ile herhangi bir Python ortamında hizmetle etkileşime geçin.
+ [R için Azure MACHINE LEARNING SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (Önizleme) ile herhangi bir r ortamındaki hizmetle etkileşime geçin.
+ Kod yazmadan iş akışı adımlarını gerçekleştirmek için [Azure Machine Learning tasarımcısını](concept-designer.md) kullanın. 
+ Otomasyon için [Azure MACHINE LEARNING CLI](./reference-azure-machine-learning-cli.md) kullanın.
+ [Birçok model Çözüm Hızlandırıcısı](https://aka.ms/many-models) (önizleme) Azure Machine Learning oluşturur ve yüzlerce veya hatta binlerce makine öğrenimi modelini eğitmenize, çalıştırmanıza ve yönetmenize olanak sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning kullanmaya başlamak için bkz.:

* [Azure Machine Learning nedir?](overview-what-is-azure-ml.md)
* [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md)
* [Öğretici (Bölüm 1): bir modeli eğitme](tutorial-train-models-with-aml.md)