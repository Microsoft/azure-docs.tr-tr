---
title: ML işlem hatları nelerdir
titleSuffix: Azure Machine Learning
description: Bu makalede, Python için Azure Machine Learning SDK ile oluşturabileceğiniz makine öğrenimi ardışık yönlerinin avantajlarını öğrenin. Machine learning (ML) işlem hatları oluşturmak, en iyi duruma getirmek ve makine öğrenimi iş akışları yönetmek için veri uzmanları tarafından kullanılır.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: 21aa869d53a35ee974fb2f852b9be9b10eb929b0
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112410"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Azure Machine Learning işlem hatları nelerdir?

Azure Machine Learning işlem hatları, Machine Learning projelerinizde iş akışları oluşturmanıza olanak tanır. Bu iş akışlarının çeşitli avantajları vardır: 

+ Basitlik
+ Hız
+ Yinelenebilirlik
+ Esneklik
+ Sürüm oluşturma ve izleme
+ Modülerlik 
+ Kalite güvencesi
+ Maliyet denetimi

Bu avantajlar, Machine Learning projeniz saf araştırmadan ve yinelemeye doğru ilerlemediğinde önemli hale gelir. Basit tek adımlı işlem hatları da değerli olabilir. Makine öğrenimi projeleri genellikle karmaşık bir durumlardır ve tek bir iş akışının kesin bir işlem olmasını sağlamak zor olabilir.

Bilgi edinmek için nasıl [ilk işlem hattınızı oluşturma](how-to-create-your-first-pipeline.md).

![Azure Machine Learning 'de makine öğrenimi ardışık düzenleri](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Hangi Azure ardışık düzen teknolojisini kullanmalıyım?

Azure bulutu, her biri farklı bir amaca sahip birkaç farklı işlem hattı sağlar. Aşağıdaki tabloda, farklı işlem hatları ve bunların kullanıldıkları özellikler listelenmiştir:

| İşlem hattı | Ne yapar? | Kurallı Kanal |
| ---- | ---- | ---- |
| Azure Machine Learning işlem hatları | Makine öğrenimi senaryolarınız için şablon olarak kullanılabilen yeniden kullanılabilir makine öğrenimi iş akışlarını tanımlar. | Veri > modeli |
| [Azure Data Factory işlem hatları](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Bir görevi gerçekleştirmek için gereken veri taşıma, dönüştürme ve denetim etkinliklerini gruplandırır.  | Veri > verileri |
| [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Uygulamanızın herhangi bir platforma/buluta yönelik sürekli tümleştirme ve teslim  | Kod-> uygulaması/hizmeti |

## <a name="what-can-azure-ml-pipelines-do"></a>Azure ML işlem hatları ne yapabilir?

Azure Machine Learning işlem hattı, bir makine öğrenimi görevinin bağımsız bir yürütülebilir iş akışıdır. Alt görevler, işlem hattının içindeki bir dizi adım olarak kapsüllenir. Bir Azure Machine Learning işlem hattı, Python betiği çağıran bir basit olabilir, bu nedenle yalnızca herhangi bir şey _olabilir_ . İşlem hatları, şu gibi makine öğrenimi görevlerine _odaklanmalıdır_ :

+ İçeri aktarma, doğrulama ve Temizleme, temizleme ve dönüştürme, normalleştirme ve hazırlama dahil veri hazırlığı
+ Belirteç bağımsız değişkenlerini, filePaths ve günlük/raporlama yapılandırmalarını içeren eğitim yapılandırması
+ Belirli veri alt kümelerini, farklı donanım işlem kaynaklarını, dağıtılmış işlemeyi ve ilerleme durumunu belirtmeyi içerebilen etkin ve çalıştırılabilecek 'ı eğitmek ve doğrulamak
+ Sürüm oluşturma, ölçeklendirme, sağlama ve erişim denetimi de dahil olmak üzere dağıtım 

Bağımsız adımlar, birden fazla veri bilimcilerinin aynı işlem hattı üzerinde, yoğun işlem kaynakları olmadan aynı anda çalışmasına izin verir. Ayrı adımlar, her adım için farklı işlem türleri/boyutları kullanmayı da kolaylaştırır.

İşlem hattı tasarlandıktan sonra, işlem hattının eğitim döngüsü etrafında genellikle daha fazla ince ayar yapılır. Bir işlem hattını yeniden çalıştırdığınızda, çalıştırma, güncelleştirilmiş bir eğitim betiği gibi yeniden çalıştırılması gereken adımlara atlar. Yeniden çalıştırılması gerekmeyen adımlar atlanır. Aynı analiz, adımın yerine kullanılan değiştirilmemiş betikler için geçerlidir. Bu yeniden kullanım işlevselliği, temel alınan veriler değişmemişse veri alımı ve dönüştürme gibi maliyetli ve zaman uyumlu adımların çalıştırılmasının önlenmesine yardımcı olur.

Azure Machine Learning, işlem hattınızdaki her adım için PyTorch veya TensorFlow gibi çeşitli araç setleri ve çerçeveler kullanabilirsiniz. Azure, kullandığınız çeşitli [işlem hedeflerini](concept-azure-machine-learning-architecture.md) koordine eder, bu sayede ara verileriniz aşağı akış işlem hedefleri ile paylaşılabilir.

İşlem [hattı denemeleri için ölçümleri](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) doğrudan Azure Portal veya [çalışma alanı giriş sayfanız (Önizleme)](https://ml.azure.com)içinde izleyebilirsiniz. Bir işlem hattı yayımlandıktan sonra, herhangi bir platform veya yığından işlem hattını yeniden çalıştırmanıza olanak sağlayan bir REST uç noktası yapılandırabilirsiniz.

Kısacası, makine öğrenimi yaşam döngüsünün tüm karmaşık görevlerine işlem hatları ile yardımcı olabilir. Diğer Azure işlem hattı teknolojilerinin, verilerle çalışma ve sürekli tümleştirme ve dağıtım için [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) [Azure Data Factory işlem hatları](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) gibi kendi güçlü yönleri vardır. Ancak odaklanmanız makine öğrenimi ise, Azure Machine Learning işlem hatları, iş akışı gereksinimleriniz için en iyi seçenektir. 

## <a name="what-are-azure-ml-pipelines"></a>Azure ML işlem hatları nelerdir?

Azure ML işlem hattı, sıralı bir adım dizisi ile tamamlanmış bir mantıksal iş akışı gerçekleştirir. Her adım ayrı bir işleme eylemi olur. İşlem hatları Azure Machine Learning [deneme](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)bağlamında çalışır.

Bir ML projesinin çok erken aşamalarında, Azure çalışma alanı ve kaynak yapılandırması, veri hazırlama, çalıştırma yapılandırma, eğitim ve doğrulama çalışmalarını yapan tek bir Jupyter Not defteri veya Python betiğinin olması iyi olabilir. Ancak, işlevlerin ve sınıfların tek bir kesinlik bloğu için hızlı bir şekilde tercih edileceği şekilde, ML iş akışlarının tek parçalı bir not defteri veya betiğe hızla tercih edileceği. 

ML görevleri modüle göre, işlem hatları, bir bileşenin "do (yalnızca) bir tane daha iyi olması gerektiğini, bilgisayar bilimi 'ni destekler. Modülerlik, ekiplerde programlarken proje başarısı için çok önemlidir, ancak tek başına çalışırken bile, küçük bir ML projesi her biri ayrı bir karmaşıklık miktarı olan ayrı görevler içerir. Görevler şunlardır: çalışma alanı yapılandırma ve veri erişimi, veri hazırlama, model tanımı ve yapılandırma ve dağıtım. Bir ya da daha fazla görevin çıkışları, girdileri başka bir şekilde oluştururken, her bir görevin tam uygulama ayrıntıları, en iyi ve bir sonraki konuda ilgisiz değildir. En kötü durumda, bir görevin hesaplama durumu başka bir hataya neden olabilir. 

### <a name="analyzing-dependencies"></a>Bağımlılıklar çözümleniyor

Birçok programlama ekosisteminde kaynak, kitaplık veya derleme bağımlılıklarını düzenleyen araçlar vardır. Genellikle, bu araçlar bağımlılıkları hesaplamak için dosya zaman damgalarını kullanır. Bir dosya değiştirildiğinde yalnızca bu ve bağımlıları güncelleştirilir (indirilir, yeniden derlenir veya paketlenmiş). Azure ML işlem hatları bu kavramı önemli ölçüde genişletir. Geleneksel derleme araçları gibi işlem hatları, adımlar arasındaki bağımlılıkları hesaplar ve yalnızca gerekli yeniden hesaplamaları gerçekleştirir. 

Azure ML işlem hatları 'ndaki bağımlılık analizi, basit zaman damgalarından daha karmaşıktır. Her adım farklı bir donanım ve yazılım ortamında çalıştırılabilir. Veri hazırlama zaman alan bir işlem olabilir ancak güçlü GPU 'Lar ile donanımda çalıştırılması gerekmez, bazı adımlar işletim sistemine özgü yazılım gerektirebilir, dağıtılmış eğitim kullanmak isteyebilirsiniz ve bu şekilde devam edebilir. Kaynakları iyileştirmek için maliyet tasarrufları önemli olabilir, ancak donanım ve yazılım kaynaklarındaki farklı çeşitliliğe el ile karşı el ile karşı bir işlem yapmak oldukça zor olabilir. Adımlar arasında aktardığınız verilerde bir hata yapmadan tüm bunları yapmak daha da zordur. 

İşlem hatları bu sorunu çözüyor. Azure Machine Learning, işlem hattı adımları arasındaki tüm bağımlılıkları otomatik olarak düzenler. Bu düzenleme, Docker görüntülerini alıp, işlem kaynaklarını ekleyerek ve ayırmayı ve adımlar arasında tutarlı ve otomatik bir şekilde veri taşımayı içerebilir.

### <a name="reusing-results"></a>Sonuçları yeniden kullanma

Ayrıca, bir adımın çıktısı, öğesini seçerseniz, yeniden kullanılabilir. Bir olasılık olarak yeniden kullan seçeneğini belirtirseniz ve yeniden hesaplamayı tetikleyen yukarı akış bağımlılığı yoksa, işlem hattı hizmeti adım sonuçlarının önbelleğe alınmış bir sürümünü kullanır. Bu yeniden kullanım, geliştirme süresini önemli ölçüde azaltabilir. Karmaşık bir veri hazırlama göreviniz varsa, büyük olasılıkla kesinlikle gerekli olandan daha sık yeniden çalıştırmanız gerekir. İşlem hatları sizi merak eder: gerekirse, adım çalışır, yoksa, bu işlem çalışmaz.

Bu bağımlılık analizi, düzenleme ve etkinleştirmenin hepsi, bir işlem [hattı](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) nesnesini örneklediğinizde, bir `Experiment`geçirdiğinizde ve `submit()`çağırdığınızda Azure Machine Learning tarafından işlenir. 

### <a name="coordinating-the-steps-involved"></a>İlgili adımları koordine etme

Bir `Pipeline` nesnesi oluşturup çalıştırdığınızda, aşağıdaki üst düzey adımlar oluşur:

+ Her adım için, hizmet gereksinimleri hesaplar:
    + Donanım işlem kaynakları
    + İşletim sistemi kaynakları (Docker görüntüsü)
    + Yazılım kaynakları (Conda/virtualenv bağımlılıkları)
    + Veri girişleri 
+ Hizmet, adımlar arasındaki bağımlılıkları belirler ve bu, dinamik bir yürütme grafiğine yol açar
+ Yürütme grafiğindeki her düğüm çalıştığında:
    + Hizmet gereken donanım ve yazılım ortamını yapılandırır (Belki de mevcut kaynakları yeniden kullanılıyor)
    + Bu adım çalışma, kendi içerdiği `Experiment` nesnesine günlük kaydı ve izleme bilgileri sağlar
    + Adım tamamlandığında, çıkışları bir sonraki adıma giriş olarak hazırlanır ve/veya depolama alanına yazılır
    + Artık gerekli olmayan kaynaklar sonlandırılır ve ayrılır

![Ardışık düzen adımları](media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Python SDK ile işlem hatları oluşturma

[Azure Machine Learning Python SDK 'sında](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), işlem hattı `azureml.pipeline.core` modülünde tanımlanan bir Python nesnesidir. İşlem [hattı](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) nesnesi bir veya daha fazla [ardışık düzen inestep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) nesnesinin sıralı dizisini içerir. `PipelineStep` sınıfı soyuttur ve gerçek adımlar, [Estimatorstep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)veya [datatransferstep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)gibi alt sınıflar olacaktır. [Modulestep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) sınıfı, işlem hatları arasında paylaşılabilen, yeniden kullanılabilir bir adım dizisi içerir. Bir `Pipeline` `Experiment`bir parçası olarak çalışır.

Bir Azure ML işlem hattı, bir Azure Machine Learning çalışma alanıyla ilişkilendirilir ve bir işlem hattı adımı söz konusu çalışma alanı içinde kullanılabilir bir işlem hedefi ile ilişkilendirilir. Daha fazla bilgi için bkz. [Azure portal Azure Machine Learning çalışma alanları oluşturma ve yönetme](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-workspace) veya [Azure Machine Learning 'Nda işlem hedefleri nelerdir?](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).

Azure Machine Learning, bir işlem hedefi, bir ML aşamasının gerçekleştiği ortamdır. Yazılım ortamı, uzak bir VM, Azure Machine Learning Işlem, Azure Databricks, Azure Batch vb. olabilir. Ayrıca, donanım ortamı GPU desteğine, belleğe, depolamaya ve benzeri önemli ölçüde farklılık gösterebilir. Her adım için işlem hedefini belirtebilir, bu, maliyetleri üzerinde ayrıntılı denetim sağlar. Projenizin belirli bir eylemi, veri hacmi ve performans ihtiyaçları için daha fazla veya daha az güçlü kaynak kullanabilirsiniz. 

## <a name="building-pipelines-with-the-designer"></a>Tasarımcı ile işlem hatları oluşturma

Görsel tasarım yüzeyini tercih eden geliştiriciler, işlem hatları oluşturmak için Azure Machine Learning tasarımcısını kullanabilir. Bu araca, çalışma alanınızın giriş sayfasındaki **Tasarımcı** seçiminden erişebilirsiniz.  Tasarımcı, tasarım yüzeyine olan adımları sürükleyip bırakmayı sağlar. Hızlı geliştirme için, ML görevlerinin spektrumu genelinde mevcut modülleri kullanabilirsiniz; mevcut modüller, veri dönüşümden algoritma seçimine, dağıtıma eğitimlere kadar her şeyi kapsar. Ya da Python betiklerinizde tanımlanan kendi adımlarınızı birleştirerek tamamen özel bir işlem hattı oluşturabilirsiniz.

İşlem hatlarını görsel olarak tasarladığınızda, bir adımın giriş ve çıkışları görünmez bir şekilde görüntülenir. Veri akışını sürükleyip bırakabilir, böylece işlem hattınızdaki veri akışını hızla anlamanıza ve değiştirebilirsiniz.
 
![Azure Machine Learning tasarımcı örneği](./media/concept-ml-pipelines/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Yürütme grafiğini anlama

Bir işlem hattının içindeki adımlarda diğer adımlara bağımlılıklar olabilir. Azure ML işlem hattı hizmeti bu bağımlılıkları çözümleme ve düzenleme işini yapar. Sonuçta elde edilen "yürütme grafiği" düğümleri işleme adımları. Her adım, belirli bir donanım ve yazılım birleşimini oluşturma veya yeniden kullanma, önbelleğe alınmış sonuçları yeniden kullanma ve benzeri bir işlem içerebilir. Hizmetin bu yürütme grafiğinin düzenlemesi ve iyileştirmesi, bir ML aşamasını önemli ölçüde hızlandırır ve maliyetleri azaltabilir. 

Adımlar bağımsız olarak çalıştırıldığı için, adımlar arasında akan giriş ve çıkış verilerini tutacak nesneler dışarıdan tanımlanmalıdır. Bu, [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)ve ilişkili sınıfların rolüdür. Bu veri nesneleri, depolama yapılandırmalarını kapsülleyen bir [veri deposu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) nesnesi ile ilişkilendirilir. `PipelineStep` temel sınıfı her zaman bir `name` dizesi, bir `inputs`listesi ve bir `outputs`listesi oluşturulur. Genellikle `arguments` bir listesi de vardır ve genellikle bir `resource_inputs`listesi olur. Alt sınıflarda genellikle ek bağımsız değişkenler de olur (örneğin, `PythonScriptStep` çalıştırmak için betiğin dosya adını ve yolunu gerektirir). 

Yürütme grafiği Çevrimsiz olur, ancak işlem hatları yinelenen bir zamanlamaya göre çalıştırılabilir ve dosya sistemine durum bilgilerini yazabilmesi için karmaşık profiller oluşturmak mümkün hale getiren Python betikleri çalıştırılabilir. İşlem hattınızı, belirli adımların paralel veya zaman uyumsuz olarak çalıştırılabilmesini sağlayacak şekilde tasarlarsanız, Azure Machine Learning, hem fan hem de fan için bağımlılık analizini ve koordinasyonunu, saydam bir şekilde işler. Genellikle yürütme grafiğinin ayrıntıları ile ilgilenmeniz gerekmez, ancak işlem [hattı. Graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) özniteliği aracılığıyla bulunabilir. 


### <a name="a-simple-python-pipeline"></a>Basit bir Python işlem hattı

Bu kod parçacığı, temel bir `Pipeline`oluşturmak ve çalıştırmak için gereken nesneleri ve çağrıları gösterir:

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = DataReference(
    datastore=Datastore(ws, blob_store),
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")

output_data = PipelineData(
    "output_data",
    datastore=blob_store,
    output_name="output_data1")

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_data, "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Kod parçacığı, ortak Azure Machine Learning nesneleriyle, bir `Workspace`, `Datastore`, bir [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)ve bir `Experiment`başlar. Daha sonra kod, `input_data` ve `output_data`tutmak için nesneleri oluşturur. Dizi `steps`, veri nesnelerini kullanacak ve `compute_target`çalıştıran bir `PythonScriptStep` tek bir öğe içerir. Daha sonra kod, `Pipeline` nesnenin kendisini başlatır, çalışma alanı ve adımlar dizisinin içine geçer. `experiment.submit(pipeline)` çağrısı Azure ML işlem hattı çalıştırmasına başlar. İşlem hattı bitene kadar blok `wait_for_completion()` çağrısı. 

## <a name="best-practices-when-using-pipelines"></a>İşlem hatları kullanırken en iyi uygulamalar

Gördüğünüz gibi, bir Azure ML işlem hattı oluşturmak, bir betiği başlatmaktan biraz daha karmaşıktır. İşlem hatları birkaç Python nesnesinin yapılandırılmasını ve oluşturulmasını gerektirir. 

İşlem hattı kullanmayı öneren bazı durumlar:

* Bir ekip ortamında: geliştiricilerin programlarını bağımsız olarak çalışabilmesi ve gelişmesi için ML görevlerini birden çok bağımsız adımlara bölün. 

* Ya da neredeyse dağıtım sırasında: yapılandırmayı değiştirip, değişen verilerin üzerine kalmak için zamanlanmış ve olay odaklı işlemleri kullanın.

* Bir ML projesinin erken aşamalarında veya tek başına çalışırken: derlemeyi otomatikleştirmek için işlem hatlarını kullanın. Yeni bir fikir uygulamadan önce yapılandırma ve hesaplama durumunu yeniden oluşturma konusunda endişelenmeye başladıysanız, bu, iş akışını otomatikleştirmek için bir işlem hattı kullanmayı düşünebileceğiniz bir sinyaldir. 

Önbelleğe alınmış sonuçları yeniden kullanma, işlem maliyetleri üzerinde ayrıntılı denetim ve işlem yalıtımının yanı sıra işlem hatlarında maliyetleri vardır. Bazı anti-desenler şunları içerir:

* Sorunları ayırmak için tek bir anlamı olan işlem hatlarını kullanma. Python 'un yerleşik işlevleri, nesneleri ve modülleri, programlı bir durum oluşmasını önlemek için uzun bir yoldur! İşlem hattı adımı, bir işlev çağrısından çok daha pahalıdır.

* Ardışık düzen adımları arasında ağır bir eşlenme. Bir bağımlı adımı sık sık yeniden düzenleme, önceki bir adımın çıktılarının değiştirilmesini gerektiriyorsa, bu durum büyük bir avantajdan daha fazla maliyetle zaten daha fazla maliyetlidir. Bu adımların çok fazla bağlanmış olduğunu gösteren bir adım, veri olmayan bir adımın bağımsız değişkenlerdir ancak işlemeyi denetlemek için bayraklar. 

* İşlem kaynaklarını erken iyileştirme. Örneğin, veri hazırlığı için genellikle birkaç aşama vardır ve diğeri genellikle paralel programlama için bir `MpiStep` kullanabilirim, ancak daha az güçlü bir işlem hedefi ile `PythonScriptStep` kullanabilirim bir yer olabilir , ve benzeri. Belki de uzun çalıştırmada, özellikle de her zaman yeniden hesaplama yerine önbelleğe alınmış sonuçları kullanma olanağı varsa, bu gibi hassas adımlar oluşturma. Ancak ardışık düzenleri `multiprocessing` modülünün yerini alacak şekilde tasarlanmamıştır. 

Bir proje büyük veya daha iyi bir dağıtım yapana kadar, işlem hatlarınızın hassas olması yerine kaba olması gerekir. ML projenizi _aşamalar_ ve bir işlem hattı dahil olmak üzere düşünüyorsanız, sizi belirli bir aşamada hareket ettirmek üzere bir iş akışı sağlamak için doğru yolda olursunuz. 

## <a name="key-advantages"></a>Başlıca avantajları

Machine Learning iş akışlarınız için işlem hatlarını kullanmanın temel avantajları şunlardır:

|Önemli bir avantajı|Açıklama|
|:-------:|-----------|
|**Katılımsız&nbsp;çalıştırır**|Adımları güvenilir ve katılımsız bir şekilde paralel veya sırayla çalışacak şekilde zamanlayın. Veri hazırlama ve modelleme son günler veya haftadır ve işlem hatları, işlem çalışırken diğer görevlere odaklanabilmenize olanak tanır. |
|**Heterojen işlem**|Heterojen ve ölçeklenebilir işlem kaynakları ve depolama konumları genelinde güvenilir bir şekilde koordine edilen birden çok işlem hattı kullanın. HDInsight, GPU veri bilimi VM 'Leri ve Databricks gibi farklı işlem hedeflerinde bireysel ardışık düzen adımlarını çalıştırarak kullanılabilir işlem kaynaklarının verimli bir şekilde kullanılmasını sağlayın.|
|**Yeniden kullanılırlığı**|Yeniden eğitme ve Batch Puanlama gibi belirli senaryolar için ardışık düzen şablonları oluşturun. Dış sistemlerden yayınlanan işlem hatlarını basit REST çağrıları aracılığıyla tetikleyin.|
|**İzleme ve sürüm oluşturma**|Yineleme sırasında verileri ve sonuç yollarını el ile izlemek yerine, veri kaynaklarınızı, girdilerlerinizi ve çıkışları açıkça adlandırmak ve sürümüne eklemek için işlem hatları SDK 'sını kullanın. Ayrıca, daha fazla üretkenlik için betikleri ve verileri ayrı olarak yönetebilirsiniz.|
| **Modülerlik** | Kaygıları ve değişiklikleri yalıtma alanlarının ayrılması, yazılımın daha yüksek kalitede daha hızlı gelişmeye olanak tanır. | 
|**İş**|İşlem hatları, veri bilimcilerinin makine öğrenimi tasarım sürecinin tüm alanlarında işbirliği yapmasına olanak sağlarken işlem hattı adımlarında eşzamanlı olarak çalışabiliyor.|

## <a name="next-steps"></a>Sonraki adımlar

Azure ML işlem hatları, erken geliştirme aşamalarında değer sunmaya başlayan güçlü bir olanaklardır. Takım ve proje büyüdükçe değer artar. Bu makalede, işlem hatlarının Azure Machine Learning Python SDK ile nasıl belirtildiği ve Azure 'da düzenlendiği açıklanmaktadır. Bazı temel kaynak kodlarını gördünüz ve kullanılabilir `PipelineStep` sınıflarından birkaçı sunulmuştur. Azure ML işlem hatlarını ne zaman kullanacağınızı ve Azure 'un nasıl çalıştığını anlamalısınız. 


+ Bilgi edinmek için nasıl [ilk işlem hattınızı oluşturma](how-to-create-your-first-pipeline.md).

+ [Büyük verilerde toplu tahmine dayalı tahminleri nasıl çalıştıracağınızı](tutorial-pipeline-batch-scoring-classification.md )öğrenin.

+ İşlem [hattı çekirdeği](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) ve [ardışık düzen adımları](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)için SDK başvuru belgeleri bölümüne bakın.

+ İşlem [hatları Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)gösteren örnek bir Jupyter Not defteri deneyin. [Bu hizmeti araştırmak için not defterlerini çalıştırmayı](samples-notebooks.md)öğrenin.
