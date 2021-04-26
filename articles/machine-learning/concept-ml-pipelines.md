---
title: Makine öğrenimi ardışık düzenleri nedir?
titleSuffix: Azure Machine Learning
description: Makine öğrenimi ardışık düzenleri makine öğrenimi iş akışlarını oluşturmanıza, iyileştirmenize ve yönetmenize nasıl yardımcı olduğunu öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.custom: devx-track-python
ms.openlocfilehash: 57f5da06909436e0cbce92559c29c309ca9e20e3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819241"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Azure Machine Learning işlem hatları nelerdir?

Bu makalede, Machine Learning işlem hattının makine öğrenimi iş akışınızı oluşturmanıza, iyileştirmenize ve yönetmenize nasıl yardımcı olduğunu öğreneceksiniz. 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>Hangi Azure ardışık düzen teknolojisini kullanmalıyım?

Azure bulutu, her biri farklı bir amaçla çeşitli işlem hattı türleri sağlar. Aşağıdaki tabloda, farklı işlem hatları ve bunların kullanıldıkları özellikler listelenmiştir:

| Senaryo | Birincil kişi | Azure teklifi | OSS sunumu | Kurallı Kanal | Yönleri | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Model düzenleme (Machine Learning) | Veri bilimcisi | Azure Machine Learning işlem hatları | Kubeflow işlem hatları | Veri > modeli | Dağıtım, önbelleğe alma, kod ilk, yeniden kullanım | 
| Veri düzenleme (veri hazırlığı) | Veri mühendisi | [Azure Data Factory işlem hatları](../data-factory/concepts-pipelines-activities.md) | Apache Airflow | Veri > verileri | Kesin tür belirtilmiş hareket, veri merkezli etkinlikler |
| Kod & uygulama düzenlemesi (CI/CD) | Uygulama geliştiricisi/Ops | [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Kod + model-> App/Service | En açık ve esnek etkinlik desteği, onay kuyrukları, geçişi olan aşamalar | 

## <a name="what-can-machine-learning-pipelines-do"></a>Makine öğrenimi ardışık düzenleri ne yapabilir?

Azure Machine Learning işlem hattı, bir makine öğrenimi görevinin bağımsız bir yürütülebilir iş akışıdır. Alt görevler işlem hattı içinde bir dizi adım olarak kapsüllenir. Bir Azure Machine Learning işlem hattı, Python betiği çağıran bir basit olabilir, bu nedenle yalnızca herhangi bir şey _olabilir_ . İşlem hatları, şu gibi makine öğrenimi görevlerine _odaklanmalıdır_ :

+ İçeri aktarma, doğrulama ve temizleme, ham verileri kullanışlı hale getirme ve dönüştürme, normalleştirme ve hazırlamayı içeren veri hazırlığı
+ Bağımsız değişkenleri, dosya yollarını ve günlük / raporlama yapılandırmalarını parametreleştirmeyi içeren eğitim yapılandırması
+ Verimli ve sürekli olarak eğitim ve doğrulama. Verimlilik, belirli veri alt kümeleri, farklı donanım işlem kaynakları, dağıtılmış işlem ve ilerleme izlemeyi belirtmekten gelebilir
+ Sürüm oluşturma, ölçeklendirme, sağlama ve erişim denetimini içeren dağıtım

Bağımsız adımlar, birden fazla veri bilimcilerinin aynı işlem hattı üzerinde, yoğun işlem kaynakları olmadan aynı anda çalışmasına izin verir. Ayrı adımlar, her adım için farklı işlem türleri/boyutları kullanmayı da kolaylaştırır.

İşlem hattı tasarlandıktan sonra, işlem hattının eğitim döngüsü etrafında genellikle daha fazla ince ayar yapılır. Bir işlem hattını yeniden çalıştırdığınızda, çalıştırma, güncelleştirilmiş bir eğitim betiği gibi yeniden çalıştırılması gereken adımlara atlar. Yeniden çalıştırılması gerekmeyen adımlar atlanır. 

İşlem hatları ile farklı görevler için farklı donanımlar kullanmayı seçebilirsiniz. Azure, kullandığınız çeşitli [işlem hedeflerini](concept-azure-machine-learning-architecture.md) koordine eder, bu sayede ara veriniz aşağı akış işlem hedeflerine sorunsuzca akar.

İşlem [hattı denemeleri için ölçümleri](./how-to-log-view-metrics.md) doğrudan Azure Portal veya [çalışma alanı giriş sayfanız (Önizleme)](https://ml.azure.com)içinde izleyebilirsiniz. Bir işlem hattı yayımlandıktan sonra, herhangi bir platform veya yığından işlem hattını yeniden çalıştırmanıza olanak sağlayan bir REST uç noktası yapılandırabilirsiniz.

Kısacası, makine öğrenimi yaşam döngüsünün tüm karmaşık görevlerine işlem hatları ile yardımcı olabilir. Diğer Azure ardışık düzen teknolojilerinin kendi güçlü yönleri vardır. [Azure Data Factory işlem hatları](../data-factory/concepts-pipelines-activities.md) , verilerle çalışma ve [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) sürekli tümleştirme ve dağıtım için doğru araçtır. Ancak odaklanmanız makine öğrenimi ise, Azure Machine Learning işlem hatları, iş akışı gereksinimleriniz için en iyi seçenektir. 

### <a name="analyzing-dependencies"></a>Bağımlılıklar çözümleniyor

Birçok programlama ekosisteminde kaynak, kitaplık veya derleme bağımlılıklarını düzenleyen araçlar vardır. Genellikle, bu araçlar bağımlılıkları hesaplamak için dosya zaman damgalarını kullanır. Bir dosya değiştirildiğinde yalnızca bu ve bağımlıları güncelleştirilir (indirilir, yeniden derlenir veya paketlenmiş). Azure Machine Learning işlem hatları bu kavramı genişletir. Geleneksel derleme araçları gibi işlem hatları, adımlar arasındaki bağımlılıkları hesaplar ve yalnızca gerekli yeniden hesaplamaları gerçekleştirir. 

Azure Machine Learning işlem hatlarında bağımlılık analizi, ancak basit zaman damgalarından daha karmaşıktır. Her adım farklı bir donanım ve yazılım ortamında çalıştırılabilir. Veri hazırlama zaman alan bir işlem olabilir ancak güçlü GPU 'Lar ile donanımda çalıştırılması gerekmez, bazı adımlar işletim sistemine özgü yazılım gerektirebilir, dağıtılmış eğitim kullanmak isteyebilirsiniz ve bu şekilde devam edebilir. 

Azure Machine Learning, işlem hattı adımları arasındaki tüm bağımlılıkları otomatik olarak düzenler. Bu düzenleme, Docker görüntülerini alıp, işlem kaynaklarını ekleyerek ve ayırmayı ve adımlar arasında tutarlı ve otomatik bir şekilde veri taşımayı içerebilir.

### <a name="coordinating-the-steps-involved"></a>İlgili adımları koordine etme

Bir nesne oluşturup çalıştırdığınızda `Pipeline` , aşağıdaki üst düzey adımlar oluşur:

+ Her adım için, hizmet gereksinimleri hesaplar:
    + Donanım işlem kaynakları
    + İşletim sistemi kaynakları (Docker görüntüsü)
    + Yazılım kaynakları (Conda/virtualenv bağımlılıkları)
    + Veri girişleri 
+ Hizmet, adımlar arasındaki bağımlılıkları belirler ve bu, dinamik bir yürütme grafiğine yol açar
+ Yürütme grafiğindeki her düğüm çalıştığında:
    + Hizmet gereken donanım ve yazılım ortamını yapılandırır (Belki de mevcut kaynakları yeniden kullanılıyor)
    + Bu adım çalışma, kapsayan nesnesine günlük kaydı ve izleme bilgilerini sağlar `Experiment`
    + Adım tamamlandığında, çıkışları bir sonraki adıma giriş olarak hazırlanır ve/veya depolama alanına yazılır
    + Artık gerekli olmayan kaynaklar sonlandırılır ve ayrılır

![Ardışık düzen adımları](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Python SDK ile işlem hatları oluşturma

[Azure Machine Learning Python SDK 'sında](/python/api/overview/azure/ml/install), işlem hattı modülünde tanımlanan bir Python nesnesidir `azureml.pipeline.core` . İşlem [hattı](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29) nesnesi bir veya daha fazla [ardışık düzen inestep](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) nesnesinin sıralı dizisini içerir. `PipelineStep`Sınıf soyuttur ve gerçek adımlar, [Estimatorstep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep), [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep)veya [datatransferstep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep)gibi alt sınıflar olacaktır. [Modulestep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) sınıfı, işlem hatları arasında paylaşılabilen, yeniden kullanılabilir bir adım dizisi içerir. Bir `Pipeline` parçası olarak çalışır `Experiment` .

Bir Azure Machine Learning işlem hattı, bir Azure Machine Learning çalışma alanıyla ilişkilendirilir ve bir işlem hattı adımı söz konusu çalışma alanı içinde kullanılabilir bir işlem hedefi ile ilişkilendirilir. Daha fazla bilgi için bkz. [Azure portal Azure Machine Learning çalışma alanları oluşturma ve yönetme](./how-to-manage-workspace.md) veya [Azure Machine Learning 'Nda işlem hedefleri nelerdir?](./concept-compute-target.md).

### <a name="a-simple-python-pipeline"></a>Basit bir Python işlem hattı

Bu kod parçacığı, oluşturmak ve çalıştırmak için gereken nesneleri ve çağrıları gösterir `Pipeline` :

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))
prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    source_directory="prep_src",
    compute_target=compute_target,
    arguments=["--prepped_data_path", prepped_data_path],
    inputs=[input_dataset.as_named_input('raw_data').as_mount() ]
    )

prepped_data = prepped_data_path.read_delimited_files()

train_step = PythonScriptStep(
    name="train",
    script_name="train.py",
    compute_target=compute_target,
    arguments=["--prepped_data", prepped_data],
    source_directory="train_src"
)
steps = [ dataprep_step, train_step ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Kod parçacığı, ortak Azure Machine Learning nesneleri, a `Workspace` , a `Datastore` , [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget)ve ile başlar `Experiment` . Daha sonra kod, ve tutulacak nesneleri oluşturur `input_data` `prepped_data_path` . , `input_data` [Filedataset](/python/api/azureml-core/azureml.data.filedataset) 'in bir örneğidir ve `prepped_data_path` bir  [outputfiledatasetconfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)örneğidir. `OutputFileDatasetConfig`Varsayılan davranış için çıktıyı `workspaceblobstore` yolun altındaki veri deposuna kopyalama `/dataset/{run-id}/{output-name}` , burada `run-id` çalıştırma kimliği ve `output-name` Geliştirici tarafından belirtilmemişse otomatik olarak oluşturulan bir değer.

Veri hazırlama kodu (gösterilmez), ayrılmış dosyaları öğesine yazar `prepped_data_path` . Veri hazırlama adımındaki bu çıktılar eğitim adımına olarak geçirilir `prepped_data` . 

Dizi, `steps` `PythonScriptStep` ve ' nin ikisini de `dataprep_step` barındırır `train_step` . Azure Machine Learning, ' nin veri bağımlılığını analiz eder `prepped_data` ve daha `dataprep_step` önce çalışır `train_step` . 

Daha sonra, kod `Pipeline` nesnenin kendisini başlatır, çalışma alanı ve Steps dizisine geçer. `experiment.submit(pipeline)`Azure ML işlem hattı çalıştırmasını Başlatan çağrı. `wait_for_completion()`İşlem hattı bitene kadar blokların çağrısı. 

İşlem hattınızı verilerinize bağlama hakkında daha fazla bilgi edinmek için [Azure Machine Learning makaleleri veri erişimi](concept-data.md) ' ne bakın ve [verileri ml ardışık düzen adımları (Python) arasında ve arasında hareket ettirin](how-to-move-data-in-out-of-pipelines.md). 

## <a name="building-pipelines-with-the-designer"></a>Tasarımcı ile işlem hatları oluşturma

Görsel tasarım yüzeyini tercih eden geliştiriciler, işlem hatları oluşturmak için Azure Machine Learning tasarımcısını kullanabilir. Bu araca, çalışma alanınızın giriş sayfasındaki **Tasarımcı** seçiminden erişebilirsiniz.  Tasarımcı, tasarım yüzeyine olan adımları sürükleyip bırakmayı sağlar. 

İşlem hatlarını görsel olarak tasarladığınızda, bir adımın giriş ve çıkışları görünmez bir şekilde görüntülenir. Veri akışını sürükleyip bırakabilir, böylece işlem hattınızdaki veri akışını hızla anlamanıza ve değiştirebilirsiniz.

![Azure Machine Learning tasarımcı örneği](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Önemli avantajlar

Machine Learning iş akışlarınız için işlem hatlarını kullanmanın temel avantajları şunlardır:

|Önemli avantaj|Description|
|:-------:|-----------|
|**Katılımsız &nbsp; çalıştırmalar**|Adımları güvenilir ve katılımsız bir şekilde paralel veya sırayla çalışacak şekilde zamanlayın. Veri hazırlama ve modelleme son günler veya haftadır ve işlem hatları, işlem çalışırken diğer görevlere odaklanabilmenize olanak tanır. |
|**Heterojen işlem**|Heterojen ve ölçeklenebilir işlem kaynakları ve depolama konumları genelinde güvenilir bir şekilde koordine edilen birden çok işlem hattı kullanın. HDInsight, GPU veri bilimi VM 'Leri ve Databricks gibi farklı işlem hedeflerinde bireysel ardışık düzen adımlarını çalıştırarak kullanılabilir işlem kaynaklarının verimli bir şekilde kullanılmasını sağlayın.|
|**Ölçeklendirilebileceği**|Yeniden eğitme ve Batch Puanlama gibi belirli senaryolar için ardışık düzen şablonları oluşturun. Dış sistemlerden yayınlanan işlem hatlarını basit REST çağrıları aracılığıyla tetikleyin.|
|**İzleme ve sürüm oluşturma**|Yineleme sırasında verileri ve sonuç yollarını el ile izlemek yerine, veri kaynaklarınızı, girdilerlerinizi ve çıkışları açıkça adlandırmak ve sürümüne eklemek için işlem hatları SDK 'sını kullanın. Ayrıca, daha fazla üretkenlik için betikleri ve verileri ayrı olarak yönetebilirsiniz.|
| **Modülerlik** | Kaygıları ve değişiklikleri yalıtma alanlarının ayrılması, yazılımın daha yüksek kalitede daha hızlı gelişmeye olanak tanır. | 
|**İşbirliği**|İşlem hatları, veri bilimcilerinin makine öğrenimi tasarım sürecinin tüm alanlarında işbirliği yapmasına olanak sağlarken işlem hattı adımlarında eşzamanlı olarak çalışabiliyor.|

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning işlem hatları, erken geliştirme aşamalarında değer sunmaya başlayan güçlü bir olanaklardır. Takım ve proje büyüdükçe değer artar. Bu makalede, işlem hatlarının Azure Machine Learning Python SDK ile nasıl belirtildiği ve Azure 'da düzenlendiği açıklanmaktadır. Bazı basit kaynak kodları gördünüz ve `PipelineStep` kullanılabilir sınıfların bazılarına sunuldu. Azure Machine Learning işlem hatlarını ne zaman kullanacağınızı ve Azure 'un nasıl çalıştığını anlamalısınız. 

+ [İlk işlem hattınızı oluşturmayı](./how-to-create-machine-learning-pipelines.md)öğrenin.

+ [Büyük verilerde toplu tahmine dayalı tahminleri nasıl çalıştıracağınızı](tutorial-pipeline-batch-scoring-classification.md )öğrenin.

+ İşlem [hattı çekirdeği](/python/api/azureml-pipeline-core/) ve [ardışık düzen adımları](/python/api/azureml-pipeline-steps/)için SDK başvuru belgeleri bölümüne bakın.

+ İşlem [hatları Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)gösteren örnek bir Jupyter Not defteri deneyin. [Bu hizmeti araştırmak için not defterlerini çalıştırmayı](samples-notebooks.md)öğrenin.