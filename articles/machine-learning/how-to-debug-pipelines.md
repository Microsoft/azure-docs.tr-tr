---
title: ML işlem hatları sorunlarını giderme
titleSuffix: Azure Machine Learning
description: Machine Learning işlem hattını çalıştırırken hata alırken sorun giderme. Uzaktan yürütmeden önce ve sırasında betiklerinizde hata ayıklamanıza yardımcı olacak genel ve ipuçları.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/22/2020
ms.topic: troubleshooting
ms.custom: troubleshooting, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 3a85566f395e97bbe88d52a8b306c7e0aa15669d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817350"
---
# <a name="troubleshooting-machine-learning-pipelines"></a>Makine öğrenimi ardışık düzenleri sorunlarını giderme

Bu makalede, [Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro) ve [Azure Machine Learning tasarımcısında](./concept-designer.md) [makine öğrenimi işlem hattı](concept-ml-pipelines.md) çalıştırırken hata almanıza nasıl sorun gidermeyi öğreneceksiniz. 

## <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

Aşağıdaki tabloda, potansiyel çözümlerle birlikte işlem hattı geliştirme sırasında yaygın sorunlar yer almaktadır.

| Sorun | Olası çözüm |
|--|--|
| Verileri dizine geçiremedi `PipelineData` | Betikte işlem hattınızın adım çıkış verilerini beklediği konuma karşılık gelen bir dizin oluşturduğunuzdan emin olun. Çoğu durumda bir giriş bağımsız değişkeni çıkış dizinini tanımlar ve ardından dizini açıkça oluşturursunuz. Çıkış dizinini oluşturmak için `os.makedirs(args.output_dir, exist_ok=True)` kullanın. Bu tasarım modelini gösteren bir Puanlama betiği örneği için [öğreticiye](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) bakın. |
| Bağımlılık hataları | Uzak işlem hattınızda yerel testlerde oluşmayan bağımlılık hataları görüyorsanız, uzak ortam bağımlılıklarınızın ve sürümlerinizin test ortamınızdakilerle eşleştiğini onaylayın. (Bkz. [ortam oluşturma, önbelleğe alma ve yeniden kullanma](./concept-environments.md#environment-building-caching-and-reuse)|
| İşlem hedefleri ile belirsiz hatalar | İşlem hedeflerini silmeyi ve yeniden oluşturmayı deneyin. İşlem hedeflerini yeniden oluşturmak hızlı bir şekilde yapılır ve bazı geçici sorunları çözebilir. |
| İşlem hattı adımları yeniden kullanma | Adım yeniden kullanım varsayılan olarak etkindir, ancak işlem hattı adımında devre dışı bırakılmadığınızdan emin olun. Yeniden kullanım devre dışıysa, `allow_reuse` adımdaki parametre olarak ayarlanır `False` . |
| İşlem hattı gereksiz yere yeniden çalıştırılıyor | Adımların yalnızca temel alınan verileri veya betikleri değiştiğinde yeniden çalıştığından emin olmak için, her adımın kaynak kodu dizinlerinizi ayırın. Birden çok adım için aynı kaynak dizinini kullanırsanız, gereksiz yeniden çalıştırma işlemleri yaşayabilirsiniz. İşlem `source_directory` hattı adımı nesnesi üzerinde parametresini kullanarak bu adım için yalıtılmış dizininizi işaret edin ve `source_directory` birden çok adım için aynı yolu kullanmadığınız emin olun. |
| Eğitim dönemlerinde veya diğer döngü davranışından daha fazla ilerleyin | Günlük kaydı dahil olmak üzere herhangi bir dosya yazmayı ' den ' a geçmeyi deneyin `as_mount()` `as_upload()` . **Bağlama** modu, uzak bir sanallaştırılmış FileSystem kullanır ve her eklendiği zaman tüm dosyayı yükler. |
| İşlem hedefinin başlaması uzun zaman alır | İşlem hedeflerine yönelik Docker görüntüleri Azure Container Registry (ACR) üzerinden yüklenir. Varsayılan olarak, Azure Machine Learning *temel* hizmet katmanını kullanan bir ACR oluşturur. Çalışma alanınızın ACR 'sini standart veya Premium katmana değiştirmek, görüntüleri oluşturmak ve yüklemek için geçen süreyi azaltabilir. Daha fazla bilgi için bkz. [Azure Container Registry hizmet katmanları](../container-registry/container-registry-skus.md). |

### <a name="authentication-errors"></a>Kimlik Doğrulama hataları

Uzak bir işten bir işlem hedefinde yönetim işlemi gerçekleştirirseniz, aşağıdaki hatalardan birini alırsınız: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Örneğin, uzaktan yürütme için gönderilen bir ML ardışık düzeninde bir işlem hedefi oluşturmaya veya eklemeye çalıştığınızda bir hata alırsınız.
## <a name="troubleshooting-parallelrunstep"></a>Sorunu `ParallelRunStep` 

Bir için betiği `ParallelRunStep` iki işlev *içermelidir* :
- `init()`: Bu işlevi, daha sonraki çıkarım için pahalı veya genel hazırlık için kullanın. Örneğin, modeli genel bir nesneye yüklemek için kullanın. Bu işlev, işlem başlangıcında yalnızca bir kez çağrılır.
-  `run(mini_batch)`: İşlev her örnek için çalışacaktır `mini_batch` .
    -  `mini_batch`: `ParallelRunStep` çalıştırma yöntemini çağırır ve `DataFrame` yönteme bağımsız değişken olarak bir liste ya da Pandas geçirirsiniz. Giriş bir ise ve giriş bir ise, mini_batch içindeki her giriş bir dosya yolu olacaktır `FileDataset` `DataFrame` `TabularDataset` .
    -  `response`: Run () yöntemi bir Pandas `DataFrame` veya dizi döndürmelidir. Append_row output_action için, döndürülen bu öğeler ortak çıkış dosyasına eklenir. Summary_only için öğelerin içeriği yok sayılır. Tüm çıkış eylemleri için, döndürülen her çıkış öğesi girdi öğesinin giriş mini Batch 'de başarılı bir şekilde çalıştırıldığını belirtir. Girişi, çıkış sonucunu çalıştırmak için eşlemek üzere, çalışma sonuçlarına yeterli miktarda veri eklendiğinden emin olun. Çalıştırma çıkışı çıkış dosyasında yazılır ve bu sırada olması garanti edilmez, çıktıda bir anahtarı, girişle eşlemek için kullanmalısınız.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Çıkarım betiğinizle aynı dizinde başka bir dosya veya klasörünüz varsa, geçerli çalışma dizinini bularak buna başvurabilirsiniz.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>ParallelRunConfig parametreleri

`ParallelRunConfig` , `ParallelRunStep` Azure Machine Learning işlem hattının içinde örnek için önemli bir yapılandırmadır. Komut dosyanızı kaydırmak ve aşağıdaki girdilerin tümü de dahil olmak üzere gerekli parametreleri yapılandırmak için kullanın:
- `entry_script`: Birden çok düğümde paralel olarak çalıştırılacak yerel dosya yolu olarak bir Kullanıcı betiği. Varsa `source_directory` , göreli bir yol kullanın. Aksi takdirde, makinede erişilebilen herhangi bir yolu kullanın.
- `mini_batch_size`: Tek bir çağrıya geçirilen mini toplu iş boyutu `run()` . (isteğe bağlı; varsayılan değer `10` `FileDataset` ve `1MB` için dosyalarıdır `TabularDataset` .)
    - İçin `FileDataset` , en az değeri olan dosya sayısıdır `1` . Birden çok dosyayı tek bir mini toplu işte birleştirebilirsiniz.
    - İçin `TabularDataset` , verilerin boyutudur. Örnek değerler şunlardır,, `1024` `1024KB` `10MB` ve `1GB` . Önerilen değer `1MB` . Mini toplu iş, `TabularDataset` hiçbir zamanı çapraz dosya sınırlarına sahip olmayacaktır. Örneğin, çeşitli boyutlarda. csv dosyalarınız varsa en küçük dosya 100 KB 'tır ve en büyük değer 10 MB 'tır. Ayarlarsanız `mini_batch_size = 1MB` , boyutu 1 MB 'tan küçük olan dosyalar bir mini toplu işlem olarak kabul edilir. Boyutu 1 MB 'tan büyük olan dosyalar birden çok mini toplu iş içine bölünür.
- `error_threshold`: `TabularDataset` `FileDataset` İşlem sırasında yok sayılacak olması gereken için kayıt hatalarının ve dosya hatalarının sayısı. Tüm girdinin hata sayısı bu değerin üzerine gittiğinde, iş iptal edilir. Hata eşiği, yönteme gönderilen tek bir mini toplu iş için değil, tüm giriş içindir `run()` . Aralık `[-1, int.max]` . `-1`Bölüm, işlem sırasında tüm hataların yoksayıyor olduğunu gösterir.
- `output_action`: Aşağıdaki değerlerden biri çıktının nasıl düzenleneceğini gösterir:
    - `summary_only`: Kullanıcı betiği çıktıyı depolayacaktır. `ParallelRunStep` yalnızca hata eşiği hesaplaması için çıktıyı kullanır.
    - `append_row`: Tüm girişler için, Line ile ayrılmış tüm çıktıları eklemek için çıkış klasöründe yalnızca bir dosya oluşturulur.
- `append_row_file_name`: Append_row output_action için çıkış dosyası adını özelleştirmek için (isteğe bağlı; varsayılan değer `parallel_run_step.txt` ).
- `source_directory`: İşlem hedefinde yürütülecek tüm dosyaları içeren klasörlere yönelik yollar (isteğe bağlı).
- `compute_target`: Yalnızca `AmlCompute` desteklenir.
- `node_count`: Kullanıcı betiğini çalıştırmak için kullanılacak işlem düğümlerinin sayısı.
- `process_count_per_node`: Düğüm başına işlem sayısı. En iyi yöntem, GPU sayısına veya CPU 'ya bir düğüm (isteğe bağlı; varsayılan değer) göre ayarlanmalıdır `1` .
- `environment`: Python ortam tanımı. Bunu, mevcut bir Python ortamını kullanacak şekilde veya geçici bir ortam ayarlamak için yapılandırabilirsiniz. Tanım ayrıca gerekli uygulama bağımlılıklarını ayarlamaktan de sorumludur (isteğe bağlı).
- `logging_level`: Günlük ayrıntı düzeyi. Artan ayrıntı değerleri: `WARNING` , `INFO` , ve `DEBUG` . (isteğe bağlı; varsayılan değer `INFO` )
- `run_invocation_timeout`: `run()` Saniye cinsinden Yöntem çağırma zaman aşımı. (isteğe bağlı; varsayılan değer `60` )
- `run_max_try`: `run()` Bir mini toplu iş için deneme sayısı üst sınırı. Bir `run()` özel durum oluşursa bir hata oluşur veya ulaşıldığında hiçbir şey döndürülmez `run_invocation_timeout` (isteğe bağlı; varsayılan değer `3` ). 

İşlem hattı çalıştırmasını yeniden gönderdiğinizde,,,,, `mini_batch_size` `node_count` ve olarak belirtebilirsiniz, `process_count_per_node` `logging_level` `run_invocation_timeout` `run_max_try` `PipelineParameter` Bu sayede parametre değerleri üzerinde ince ayar yapabilirsiniz. Bu örnekte, `PipelineParameter` ve için kullanırsınız `mini_batch_size` `Process_count_per_node` ve daha sonra bir çalıştırmayı yeniden gönderdiğinizde bu değerleri değiştirirsiniz. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>ParallelRunStep oluşturma parametreleri

Betiği, ortam yapılandırması ve parametreleri kullanarak ParallelRunStep öğesini oluşturun. Çalışma alanınıza zaten eklediğiniz işlem hedefini, çıkarım betiğinizin yürütme hedefi olarak belirtin. `ParallelRunStep`Aşağıdaki parametreleri alan toplu çıkarım ardışık düzen adımını oluşturmak için kullanın:
- `name`: Adım adı, şu adlandırma kısıtlamalarına sahip: benzersiz, 3-32 karakter ve Regex ^ \[ a-z \] ([-a-Z0-9] * [a-Z0-9])? $.
- `parallel_run_config`: `ParallelRunConfig` Daha önce tanımlanan bir nesne.
- `inputs`: Bir veya daha fazla tek tür veri kümesi paralel işleme için bölümlenecek Azure Machine Learning.
- `side_inputs`: Bir veya daha fazla başvuru verisi veya yan giriş olarak kullanılan veri kümelerinin bölümlenmiş olması gerekmez.
- `output`: `OutputFileDatasetConfig` Çıkış dizinine karşılık gelen bir nesne.
- `arguments`: Kullanıcı betiğine geçirilen bağımsız değişkenlerin bir listesi. Giriş betiğinizi (isteğe bağlı) almak için unknown_args kullanın.
- `allow_reuse`: Adımın, aynı ayarlarla/girişlerle çalıştırıldığında önceki sonuçları yeniden kullanıp kullanmayacağını belirtir. Bu parametre ise `False` , işlem hattı yürütmesi sırasında bu adım için her zaman yeni bir çalıştırma oluşturulacaktır. (isteğe bağlı; varsayılan değer `True` .)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-techniques"></a>Hata ayıklama teknikleri

Hata ayıklama ardışık düzenleri için üç önemli teknik vardır: 

* Yerel bilgisayarınızda ayrı ardışık düzen adımlarında hata ayıklayın
* Sorunun kaynağını yalıtmak ve tanılamak için günlük kaydı ve Application Insights kullanın
* Azure 'da çalışan bir işlem hattına uzaktan hata ayıklayıcı iliştirme

### <a name="debug-scripts-locally"></a>Betiklerin yerel olarak hata ayıklama

Bir işlem hattındaki en yaygın hatalardan biri, etki alanı betiğinin istendiği gibi çalıştırılmasıdır veya uzaktan işlem bağlamında hata ayıklaması zor olan çalışma zamanı hataları içerir.

Ardışık düzenleri yerel olarak çalıştırılamaz, ancak komut dosyalarını yerel makinenizde yalıtımlarda çalıştırmak, işlem ve ortam oluşturma işlemini beklemek zorunda olmadığınızdan daha hızlı hata ayıklamanıza olanak tanır. Bunu yapmak için bazı geliştirme işleri gereklidir:

* Verileriniz bir bulut veri deposunda yer alıyorsa, verileri indirmeniz ve betiğiniz için kullanılabilir hale getirmeniz gerekir. Verilerinizin küçük bir örneğini kullanmak çalışma zamanını kesmek ve betik davranışında hızlıca geri bildirim almak için iyi bir yoldur
* Ara işlem hattı adımının benzetimini gerçekleştirmeye çalışıyorsanız, belirli bir betiğin önceki adımdan beklediği nesne türlerini el ile oluşturmanız gerekebilir
* Ayrıca kendi ortamınızı tanımlamanız ve Uzaktan işlem ortamınızda tanımlanan bağımlılıkları çoğaltmanız gerekecektir

Yerel ortamınızda çalıştırmak üzere bir betik kurulumuna sahip olduktan sonra, şu gibi hata ayıklama görevlerinin olması çok daha kolaydır:

* Özel hata ayıklama yapılandırması iliştirme
* Yürütmeyi duraklatma ve nesne durumunu inceleme
* Çalışma zamanına kadar sunulmayacak tür veya mantıksal hataları yakalama

> [!TIP] 
> Betiğinizin beklendiği gibi çalıştığını doğrulayabilmeniz için, bir sonraki adım komut dosyasını birden çok adımla bir işlem hattında çalıştırmayı denemeden önce tek adımlı bir işlem hattında çalıştırıyor.

## <a name="configure-write-to-and-review-pipeline-logs"></a>İşlem hattı günlüklerini yapılandırma, yazma ve gözden geçirme

Komut dosyalarını yerel olarak test etmek, bir işlem hattı oluşturmaya başlamadan önce büyük kod parçalarının ve karmaşık mantığın hatalarını ayıklamanın harika bir yoludur. ancak bazı bir noktada, özellikle de işlem hattı adımları arasındaki etkileşim sırasında oluşan davranışı tanılarken, büyük olasılıkla gerçek ardışık düzen sırasında betiklerde hata ayıklaması yapmanız gerekir. `print()`Uzaktan yürütme sırasında nesne durumunu ve beklenen değerleri görebilmeniz için, JavaScript kodunun hatalarını ayıklamanıza benzer şekilde, adım betiklerinizde deyimlerin serbest bir şekilde kullanılmasını öneririz.

### <a name="logging-options-and-behavior"></a>Günlüğe kaydetme seçenekleri ve davranışı

Aşağıdaki tabloda, işlem hatları için farklı hata ayıklama seçenekleri sağlanmıştır. Burada yalnızca Azure Machine Learning, Python ve OpenCensus seçeneklerinin yanı sıra diğer seçenekler mevcut olduğundan, kapsamlı bir liste değildir.

| Kitaplık                    | Tür   | Örnek                                                          | Hedef                                  | Kaynaklar                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK’sı | Metric | `run.log(name, val)`                                             | Azure Machine Learning Portal Kullanıcı arabirimi             | [Denemeleri izleme](how-to-log-view-metrics.md)<br>[azureml. Core. Run sınıfı](/python/api/azureml-core/azureml.core.run%28class%29)                                                                                                                                                 |
| Python yazdırma/günlüğe kaydetme    | Günlük    | `print(val)`<br>`logging.info(message)`                          | Sürücü günlükleri, Azure Machine Learning Tasarımcısı | [Denemeleri izleme](how-to-log-view-metrics.md)<br><br>[Python günlüğü](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Günlük    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights-izlemeler                | [Application Insights’ta işlem hatlarında hata ayıklama](./how-to-log-pipelines-application-insights.md)<br><br>[OpenCensus Azure İzleyici Dışarı Aktarıcıları](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python günlüğü tanıtım rehberi](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Günlüğe kaydetme seçenekleri örneği

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning tasarımcısı

Tasarımcıda oluşturulan işlem hatları için, **70_driver_log** dosyasını yazma sayfasında veya işlem hattı çalıştırma ayrıntısı sayfasında bulabilirsiniz.

### <a name="enable-logging-for-real-time-endpoints"></a>Gerçek zamanlı uç noktalar için günlüğe kaydetmeyi etkinleştirme

Tasarımcıda gerçek zamanlı uç noktalarda sorun gidermek ve hata ayıklamak için SDK 'yı kullanarak Application Insight Logging 'i etkinleştirmeniz gerekir. Günlüğe kaydetme, model dağıtım ve kullanım sorunlarını gidermenize ve hata ayıklamanıza olanak sağlar. Daha fazla bilgi için bkz. [dağıtılan modeller Için günlüğe kaydetme](./how-to-enable-app-insights.md). 

### <a name="get-logs-from-the-authoring-page"></a>Yazma sayfasından günlükleri al

Bir işlem hattı çalıştırması gönderdiğinizde ve yazma sayfasında kaladığınızda, her modül çalışmayı bitirdiğinde her modül için oluşturulan günlük dosyalarını bulabilirsiniz.

1. Yazma tuvalinde çalışmayı tamamlamış bir modül seçin.
1. Modülün sağ bölmesinde,  **çıktılar + Günlükler** sekmesine gidin.
1. Sağ bölmeyi genişletin ve dosyayı tarayıcıda görüntülemek için **70_driver_log.txt** seçin. Günlükleri yerel olarak da indirebilirsiniz.

    ![Tasarımcıda genişletilmiş çıkış bölmesi](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>İşlem hattı çalıştırmalarından günlükleri al

Ayrıca, Studio 'nun işlem **hatları** veya **denemeleri** bölümünde bulunan işlem hattı çalışma ayrıntısı sayfasında belirli çalıştırmalar için günlük dosyalarını bulabilirsiniz.

1. Tasarımcıda oluşturulan bir işlem hattı çalıştırması seçin.

    ![İşlem hattı çalıştırma sayfası](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Önizleme bölmesinde bir modül seçin.
1. Modülün sağ bölmesinde,  **çıktılar + Günlükler** sekmesine gidin.
1. **70_driver_log.txt** dosyasını tarayıcıda görüntülemek için sağ bölmeyi genişletin veya günlükleri yerel olarak indirmek için dosyayı seçin.

> [!IMPORTANT]
> İşlem hattı çalıştırma ayrıntıları sayfasından bir işlem hattını güncelleştirmek için, işlem hattı çalıştırmasını yeni bir işlem hattı taslağında **kopyalamanız** gerekir. İşlem hattı çalıştırması, işlem hattının anlık görüntüsüdür. Bir günlük dosyasına benzer ve değiştirilemez. 

## <a name="application-insights"></a>Application Insights
OpenCensus Python kitaplığını bu şekilde kullanma hakkında daha fazla bilgi için şu kılavuza bakın: [Application Insights Machine Learning işlem hatlarında hata ayıklama ve sorun giderme](./how-to-log-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Visual Studio Code ile etkileşimli hata ayıklama

Bazı durumlarda, ML ardışık düzeninde kullanılan Python kodunda etkileşimli olarak hata ayıklaması yapmanız gerekebilir. Visual Studio Code (VS Code) ve hata ayıklama GPY kullanarak, eğitim ortamında çalışırken koda ekleyebilirsiniz. Daha fazla bilgi için [vs Code kılavuzunda etkileşimli hata ayıklamayı](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* Kullanarak kapsamlı bir öğretici için `ParallelRunStep` bkz. [öğretici: Batch puanlama için Azure Machine Learning Işlem hattı oluşturma](tutorial-pipeline-batch-scoring-classification.md).

* ML ardışık düzeninde otomatik makine öğrenimini gösteren bir örnek için bkz. [Python 'da Azure Machine Learning işlem hattında OTOMATIK ml kullanma](how-to-use-automlstep-in-pipelines.md).

* [Azureml işlem hatları-Core](/python/api/azureml-pipeline-core/) paketi ve [azureml-işlem hatları-adımlar](/python/api/azureml-pipeline-steps/) PAKETIYLE ilgili yardım için SDK başvurusuna bakın.

* [Tasarımcı özel durumları ve hata kodları](algorithm-module-reference/designer-error-codes.md)listesine bakın.