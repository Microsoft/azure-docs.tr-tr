---
title: Günlük & görünüm ölçümleri ve günlük dosyaları
titleSuffix: Azure Machine Learning
description: Gerçek zamanlı çalıştırma ölçümlerini izlemek ve hataların ve uyarıların tanılanmasına yardımcı olmak için ML eğitim çalışmalarında günlüğe kaydetmeyi etkinleştirin.
services: machine-learning
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 04/19/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bc7564a11f570833bd6cb10c14267e08c3b5753
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820555"
---
# <a name="log--view-metrics-and-log-files"></a>Günlük & görünüm ölçümleri ve günlük dosyaları

Hem varsayılan Python günlüğü paketini hem de Python SDK 'ya özgü işlevleri Azure Machine Learning gerçek zamanlı bilgileri günlüğe kaydedin. Günlükleri yerel olarak kaydedebilir ve portaldaki çalışma alanınıza gönderebilirsiniz.

Günlükler; hataları ve uyarıları tanılamanıza veya parametreler ve model performansı gibi performans ölçümlerini takip etmenize yardımcı olabilir. Bu makalede aşağıdaki senaryolarda günlüğe kaydetmeyi nasıl etkinleştirebileceğinizi öğreneceksiniz:

> [!div class="checklist"]
> * Günlük çalıştırma ölçümleri
> * Etkileşimli eğitim oturumları
> * ScriptRunConfig kullanarak eğitim işi gönderme
> * Python'daki yerel `logging` ayarları
> * Ek kaynaklardan günlüğe kaydetme


> [!TIP]
> Bu makalede model eğitim sürecini nasıl izleyebileceğiniz gösterilmiştir. Azure Machine Learning ile ilgili kaynak kullanımlarını ve olayları (kotalar, tamamlanan eğitim çalıştırmaları veya tamamlanan model dağıtımları gibi) bkz. [Azure Machine Learning'i izleme](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Veri türleri

Skaler değerler, listeler, tablolar, görüntüler ve dizinler gibi birçok farklı veri türünü günlüğe kaydedebilirsiniz. Farklı veri türleri hakkında daha fazla bilgi edinmek ve Python kodu örneklerini görmek için bkz. [Çalıştırma sınıfı başvuru sayfası](/python/api/azureml-core/azureml.core.run%28class%29).

### <a name="logging-run-metrics"></a>Günlük çalıştırma ölçümleri 

Ölçüm görselleştirmelerini etkilemek için günlüğe kaydetme API 'Lerinde aşağıdaki yöntemleri kullanın. Günlüğe kaydedilen bu ölçümler için [hizmet sınırlarını](./resource-limits-quotas-capacity.md#metrics) göz önünde edin. 

|Günlüğe kaydedilen değer|Örnek kod| Portalda biçim|
|----|----|----|
|Sayısal değerlerden oluşan diziyi günlüğe kaydet| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Tek değişkenli çizgi grafik|
|Aynı ölçüm adı ile birlikte sürekli kullanılan tek bir sayısal değeri günlüğe kaydet (for döngüsü içinde olduğu gibi)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Tek değişkenli çizgi grafik|
|2 sayısal sütundan oluşan bir satırı sürekli olarak günlüğe kaydet|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|İki değişkenli çizgi grafik|
|2 sayısal sütun içeren günlük tablosu|`run.log_table(name='Sine Wave', value=sines)`|İki değişkenli çizgi grafik|
|Günlük resmi|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Çalışma için bir görüntü dosyası veya Matplotlib çizimi günlüğe kaydetmek için bu yöntemi kullanın. Bu görüntüler, çalıştırma kaydında görünür ve karşılaştırılabilir olacaktır|

### <a name="logging-with-mlflow"></a>MLflow ile günlüğe kaydetme
Ölçümleri günlüğe kaydetmek için Mlflowgünlükçüsü kullanın.

```python
from azureml.core import Run
# connect to the workspace from within your running code
run = Run.get_context()
ws = run.experiment.workspace

# workspace has associated ml-flow-tracking-uri
mlflow_url = ws.get_mlflow_tracking_uri()

#Example: PyTorch Lightning
from pytorch_lightning.loggers import MLFlowLogger

mlf_logger = MLFlowLogger(experiment_name=run.experiment.name, tracking_uri=mlflow_url)
mlf_logger._run_id = run.id
```

## <a name="view-run-metrics"></a>Çalıştırma ölçümlerini görüntüle

## <a name="via-the-sdk"></a>SDK aracılığıyla
İle eğitilen bir modelin ölçümlerini görüntüleyebilirsiniz ```run.get_metrics()``` . Aşağıdaki örneğe bakın. 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping mertic names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-metrics-in-aml-studio-ui"></a>AML Studio Kullanıcı arabirimindeki çalışma ölçümlerini görüntüleme

Günlüğe kaydedilen ölçümler de dahil olmak üzere tamamlanan çalışma kayıtlarına gidebilirsiniz [Azure Machine Learning Studio](https://ml.azure.com).

**Denemeleri** sekmesine gidin. Çalışma alanınızdaki tüm çalıştırmalarınızı denemeleri genelinde görüntülemek için **Tüm çalıştırmalar** sekmesini seçin. En üstteki menü çubuğuna deneme filtresini uygulayarak belirli denemeleri için çalıştırmalar üzerinde ayrıntıya gidebilirsiniz.

Bireysel deneme görünümü için **tüm denemeleri** sekmesini seçin. Deneme çalıştırması panosunda, her çalıştırma için izlenen ölçümleri ve günlükleri görebilirsiniz. 

Ayrıca, çalışma listesi tablosunu, birden çok çalıştırma seçmek ve çalıştırmalarınız için en son, en düşük veya en büyük günlüğe kaydedilmiş değeri göstermek üzere düzenleyebilirsiniz. Birden çok çalıştırma sırasında günlüğe kaydedilen ölçüm değerlerini ve toplamlarını karşılaştırmak için grafiklerinizi özelleştirin. Grafiğinizin y ekseninde birden çok ölçüm oluşturabilir ve günlüğe kaydedilen ölçümlerinizi çizmek için x eksenini özelleştirebilirsiniz. 


### <a name="view-and-download-log-files-for-a-run"></a>Bir çalıştırma için günlük dosyalarını görüntüleyin ve indirin 

Günlük dosyaları, Azure ML iş yüklerinde hata ayıklamak için gereken önemli bir kaynaktır. Bir eğitim işi gönderdikten sonra, günlüklerini ve çıktılarını görüntülemek için belirli bir çalıştırmaya gidin:  

1. **Denemeleri** sekmesine gidin.
1. Belirli bir çalıştırma için RunId 'yi seçin.
1. Sayfanın üst kısmındaki **çıktılar ve Günlükler '** i seçin.
2. Tüm günlüklerinizi bir zip klasörüne indirmek için **Tümünü İndir** ' i seçin.
3. Günlük dosyalarını seçip **İndir** ' i seçerek tek tek günlük dosyalarını da indirebilirsiniz.

:::image type="content" source="media/how-to-log-view-metrics/download-logs.png" alt-text="Bir çalıştırmanın çıkış ve Günlükler bölümünün ekran görüntüsü.":::

Aşağıdaki tablolarda, bu bölümde göreceğiniz klasörlerdeki günlük dosyalarının içerikleri gösterilmektedir.

> [!NOTE]
> Her çalıştırma için her dosyayı görmeyecektir. Örneğin, 20_image_build_log *. txt yalnızca yeni bir görüntü oluşturulduğunda görüntülenir (örneğin, ortamınızı değiştirdiğinizde).

#### <a name="azureml-logs-folder"></a>`azureml-logs` klasörde

|Dosya  |Description  |
|---------|---------|
|20_image_build_log.txt     | Eğitim ortamı için Docker görüntü oluşturma günlüğü, her çalıştırma için isteğe bağlı. Yalnızca ortamınız güncelleştirilirken geçerlidir. Aksi takdirde AML, önbelleğe alınmış görüntüyü yeniden kullanır. Başarılı olursa, ilgili görüntü için görüntü kayıt defteri ayrıntılarını içerir.         |
|55_azureml yürütme-<node_id # C1.txt     | düğüm başına bir tane olmak üzere konak aracının stdout/stderr günlüğü. İşlem hedefine görüntü çeker. Bu günlük, işlem kaynaklarını güvenli hale getirildikten sonra yalnızca bir kez görünür.         |
|65_job_prep-<node_id # C1.txt     |   Her düğüm için bir tane olmak üzere iş hazırlama betiği stdout/stderr günlüğü. Hedef ve veri depolarını (istenirse) hesaplamak için kodunuzu indirin.       |
|70_driver_log (_x). txt      |  AML denetim betiğinden stdout/stderr günlüğü, işlem başına bir tane. **Betiğinizden standart çıkış. Bu dosya, kodunuzun günlüklerinin (örneğin, yazdırma deyimlerinin) gösterilmesi.** Çoğu durumda günlükleri burada takip edersiniz.       |
|70_mpi_log.txt     |   MPı Framework günlüğü, isteğe bağlı, her çalıştırma için bir. Yalnızca MPı çalıştırmaları için.   |
|75_job_post-<node_id # C1.txt     |  Her düğüm için bir tane olmak üzere iş sürümü betiği stdout/stderr günlüğü. Günlükleri gönderin, işlem kaynaklarını Azure 'a geri bırakın.        |
|Üzerinde process_info.js      |   hangi işlemin hangi düğümde çalıştığını gösterir.  |
|Üzerinde process_status.js      | işlem durumunu göster, örneğin bir işlem başlatılmamış, çalışıyor veya tamamlandı.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` klasörde

|Dosya  |Description  |
|---------|---------|
|110_azureml. log      |         |
|job_prep_azureml. log     |   iş hazırlığı için sistem günlüğü        |
|job_release_azureml. log     | iş sürümü için sistem günlüğü        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` klasörde

Sepet etkinleştirildiğinde, iş hazırlığı ve iş sürümü betikleri, sepet kapsayıcısı içinde çalıştırılır.  Her düğüm için bir klasör vardır. 

|Dosya  |Description  |
|---------|---------|
|start_cms.txt     |  Sepet kapsayıcısı başladığında başlayan işlem günlüğü       |
|prep_cmd.txt      |   Çalıştırıldığında girilen Contextyöneticilerinin günlüğü `job_prep.py` (Bu içerikten bazıları akışa alınacaktır `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  Çalıştırıldıktan sonra Comtextyöneticileri için günlüğe çıkıldı `job_release.py`        |

#### <a name="other-folders"></a>Diğer klasörler

Çoklu işlem kümelerinde iş eğitimi için, her düğüm IP 'si için Günlükler bulunur. Her bir düğümün yapısı, tek düğümlü işlerle aynıdır. Genel yürütme, stderr ve STDOUT günlükleri için bir veya daha fazla günlük klasörü vardır.

Azure Machine Learning, eğitim sırasında (örneğin, oto ml) veya eğitim işini çalıştıran Docker kapsayıcısı gibi çeşitli kaynaklardan bilgileri günlüğe kaydeder. Bu günlüklerin birçoğu açıklanmamıştır. Sorunlarla karşılaşırsanız ve Microsoft Destek ile iletişime geçerek, sorun giderme sırasında bu günlükleri kullanabiliyor olabilirler.


## <a name="interactive-logging-session"></a>Etkileşimli günlüğe kaydetme oturumu

Etkileşimli günlüğe kaydetme oturumları genellikle not defteri ortamlarında kullanılır. [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) yöntemi etkileşimli günlüğe kaydetme oturumu başlatır. Oturum sırasında günlüğe kaydedilen ölçümler, denemedeki çalıştırma kaydına eklenir. [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) yöntemi oturumları sonlandırır ve çalıştırmayı tamamlandı olarak işaretler.

## <a name="scriptrun-logs"></a>ScriptRun günlükleri

Bu bölümde ScriptConfig ile yapılandırıldığında oluşturulan çalıştırmalara günlüğe kaydetme kodu eklemeyi öğreneceksiniz. [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig) sınıfını kullanarak yinelenebilir çalıştırmalar için betikleri ve ortamları kapsülleyebilirsiniz. Bu seçeneği ayrıca izleme amacıyla görsel bir Jupyter Notebook pencere öğesi göstermek için de kullanabilirsiniz.

Bu örnek, [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----) yöntemini kullanarak alfa değerleri üzerinde parametre tarama işlemi gerçekleştirir ve sonuçları yakalar.

1. Günlüğe kaydetme mantığını içeren bir eğitim betiği oluşturun: `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. ```train.py``` betiğini kullanıcı tarafından yönetilen bir ortamda çalıştırılmak üzere gönderin. Betik klasörünün tamamı eğitim için gönderilir.

   [! Not defteri-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = src)]


   [! Not defteri-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? ad = Run)]

    `show_output` parametresi, eğitim sürecinin ayrıntılarına ek olarak uzak kaynaklar veya işlem hedefleri hakkındaki bilgileri görmenizi sağlayan ayrıntılı günlüğe kaydetme özelliğini etkinleştirir. Denemeyi gönderdiğinizde ayrıntılı günlüğe kaydetme özelliğini açmak için aşağıdaki kodu kullanın.

```python
run = exp.submit(src, show_output=True)
```

İsterseniz aynı parametreyi sonuç çalıştırmasındaki `wait_for_completion` işlevde de kullanabilirsiniz.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Yerel Python günlüğü

SDK'daki bazı günlüklerde günlük kaydı düzeyini DEBUG olarak ayarlamanızı isteyen hatalar bulunabilir. Günlük kaydı düzeyini ayarlamak için aşağıdaki kodu betiğinize ekleyin.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="other-logging-sources"></a>Diğer günlük kaynakları

Azure Machine Learning; eğitim sırasında otomatik makine öğrenmesi çalıştırmaları veya işleri çalıştıran Docker kapsayıcıları gibi farklı kaynaklardan alınan günlük bilgilerini de kullanabilir. Bu günlük kayıtları belge halinde sunulmaz ancak karşılaştığınız sorunlar için Microsoft destek ekibiyle iletişime geçerseniz sorun giderme aşamasında bu günlükler kullanılabilir.

Azure Machine Learning tasarımcısında ölçümleri günlüğe kaydetme hakkında daha fazla bilgi için bkz. [Tasarımcıda ölçümleri günlüğe kaydetme](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Örnek not defterleri

Aşağıdaki not defterlerinde bu makaledeki kavramlar gösterilmiştir:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning'i kullanma hakkında daha fazla bilgi edinmek için şu makaleleri inceleyin:

* En iyi modeli kaydetme ve dağıtma öğreticisi için bkz. [Azure Machine Learning ile görüntü sınıflandırma modeli eğitme](tutorial-train-models-with-aml.md).