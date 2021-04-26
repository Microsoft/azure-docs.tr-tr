---
title: ParallelRunStep sorunlarını giderme
titleSuffix: Azure Machine Learning
description: Machine Learning işlem hatları 'nda ParallelRunStep kullanarak hata alırken sorun gidermeye yönelik ipuçları.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: pansav
author: psavdekar
ms.date: 09/23/2020
ms.openlocfilehash: 619123cc2723fcf8e4bd80410c6b098b113d61c6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286326"
---
# <a name="troubleshooting-the-parallelrunstep"></a>ParallelRunStep sorunlarını giderme

Bu makalede, [Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro)'Dan [Parallelrunstep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) sınıfını kullanarak hata alırken sorun gidermeyi öğreneceksiniz.

İşlem hattının sorunlarını giderme hakkında genel ipuçları için bkz. [makine öğrenimi işlem hatları sorunlarını giderme](how-to-debug-pipelines.md).

## <a name="testing-scripts-locally"></a>Betikleri yerel olarak test etme

 ParallelRunStep 'niz ML işlem hatlarında bir adım olarak çalışır. Komut dosyalarınızı ilk adım olarak [yerel olarak test](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) etmek isteyebilirsiniz.

##  <a name="script-requirements"></a>Betik gereksinimleri

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
        > [!NOTE]
        > SQL tarafından desteklenen Tabulardataset 'ler bölümlenemez. 

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

İşlem hattı çalıştırmasını yeniden gönderdiğinizde,,,,, `mini_batch_size` `node_count` ve olarak belirtebilirsiniz, `process_count_per_node` `logging_level` `run_invocation_timeout` `run_max_try` `PipelineParameter` Bu sayede parametre değerleri üzerinde ince ayar yapabilirsiniz. Bu örnekte, `PipelineParameter` ve için kullanırsınız `mini_batch_size` `Process_count_per_node` ve başka bir çalışmayı yeniden gönderdiğinizde bu değerleri değiştirirsiniz. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>ParallelRunStep oluşturma parametreleri

Betiği, ortam yapılandırması ve parametreleri kullanarak ParallelRunStep öğesini oluşturun. Çalışma alanınıza zaten eklediğiniz işlem hedefini, çıkarım betiğinizin yürütme hedefi olarak belirtin. `ParallelRunStep`Aşağıdaki parametreleri alan toplu çıkarım ardışık düzen adımını oluşturmak için kullanın:
- `name`: Adım adı, şu adlandırma kısıtlamalarına sahip: benzersiz, 3-32 karakter ve Regex ^ \[ a-z \] ([-a-Z0-9] * [a-Z0-9])? $.
- `parallel_run_config`: `ParallelRunConfig` Daha önce tanımlanan bir nesne.
- `inputs`: Bir veya daha fazla tek tür veri kümesi paralel işleme için bölümlenecek Azure Machine Learning.
- `side_inputs`: Bir veya daha fazla başvuru verisi veya yan giriş olarak kullanılan veri kümelerinin bölümlenmiş olması gerekmez.
- `output`: `OutputFileDatasetConfig` Çıkış verilerinin depolanacağı dizin yolunu temsil eden nesne.
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

## <a name="debugging-scripts-from-remote-context"></a>Uzak bağlamdaki betiklerin hatalarını ayıklama

Gerçek bir işlem hattındaki Puanlama betiğine yerel olarak hata ayıklama için bir Puanlama betiğinin hata ayıklamasının yapılması zor olabilir. Günlüklerinizi portalda bulma hakkında daha fazla bilgi için,  [uzak bağlamdaki betiklerin hatalarını ayıklama bölümünde makine öğrenimi ardışık düzenleri bölümüne](how-to-debug-pipelines.md)bakın. Bu bölümdeki bilgiler bir ParallelRunStep için de geçerlidir.

Örneğin, günlük dosyası `70_driver_log.txt` denetleyicinin ParallelRunStep kodunu Başlatan bilgilerini içerir.

ParallelRunStep işlerinin dağıtılmış doğası nedeniyle, birkaç farklı kaynaktaki Günlükler vardır. Ancak, üst düzey bilgi sağlayan iki birleştirilmiş dosya oluşturulur:

- `~/logs/job_progress_overview.txt`: Bu dosya, şimdiye kadar oluşturulan mini toplu işlerin sayısı (görevler olarak da bilinir) ve şimdiye kadar işlenen mini toplu iş sayısı hakkında üst düzey bilgiler sağlar. Bu uçta, işin sonucunu gösterir. İş başarısız olursa, hata iletisini ve sorun giderme işleminin başlatılacağı konumu gösterir.

- `~/logs/sys/master_role.txt`: Bu dosya, çalışan işin asıl düğümünü (Orchestrator olarak da bilinir) sağlar. Görev oluşturma, ilerleme izleme, çalıştırma sonucu içerir.

Giriş betiğinin EntryScript yardımcısını ve Print deyimlerini kullanarak oluşturulan Günlükler aşağıdaki dosyalarda bulunur:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: Bu dosyalar, EntryScript Yardımcısı kullanılarak entry_script yazılan günlüklerdir.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: Bu dosyalar, entry_script stdout 'daki günlüklerdir (örneğin, PRINT deyimidir).

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: Bu dosyalar, entry_script stderr 'ten alınan günlüklerdir.

Betikteki hataların kısa bir şekilde anlaşılmasından bazıları şunlardır:

- `~/logs/user/error.txt`: Bu dosya, betiğinizdeki hataları özetlemeye çalışacaktır.

Betiğinizdeki hatalar hakkında daha fazla bilgi için şu olabilir:

- `~/logs/user/error/`: Giriş betiği yüklenirken ve çalıştırılırken oluşturulan özel durumların tam yığın izlemelerini içerir.

Her bir düğümün puan betiğini nasıl yürütülebileceğini tam olarak anlamak istediğinizde her düğüm için ayrı işlem günlüklerine bakın. İşlem günlükleri, `sys/node` çalışan düğümlerine göre gruplanmış şekilde klasöründe bulunabilir:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Bu dosya, bir çalışan tarafından çekildiğinde veya tamamlandığında her mini toplu iş hakkında ayrıntılı bilgi sağlar. Her mini toplu iş için bu dosya şunları içerir:

    - Çalışan işlemin IP adresi ve PID 'SI. 
    - Toplam öğe sayısı, öğe sayısı başarıyla işlendi ve başarısız öğe sayısı.
    - Başlangıç zamanı, süre, işlem süresi ve çalıştırma yöntemi zamanı.

Her düğüm için kaynak kullanımının süreli denetim sonuçlarını da görüntüleyebilirsiniz. Günlük dosyaları ve kurulum dosyaları şu klasörslardır:

- `~/logs/perf`: `--resource_monitor_interval` Denetim aralığını saniye cinsinden değiştirmek için ayarlayın. Varsayılan Aralık, `600` yaklaşık 10 dakikadır. İzlemeyi durdurmak için değerini olarak ayarlayın `0` . Her `<ip_address>` klasör şunları içerir:

    - `os/`: Düğümdeki tüm çalışan işlemlerle ilgili bilgiler. Bir denetim bir işletim sistemi komutunu çalıştırır ve sonucu bir dosyaya kaydeder. Linux 'ta, komutu `ps` . Windows üzerinde kullanın `tasklist` .
        - `%Y%m%d%H`: Alt klasör adı saat ' tir.
            - `processes_%M`: Dosya, denetim zamanının dakikasına göre sona eriyor.
    - `node_disk_usage.csv`: Düğümün ayrıntılı disk kullanımı.
    - `node_resource_usage.csv`: Düğüme kaynak kullanımına genel bakış.
    - `processes_resource_usage.csv`: Her bir işleme yönelik kaynak kullanımına genel bakış.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Kullanıcı betiğimin uzak bağlamdan Nasıl yaparım? mi?

ParallelRunStep, bir düğümde process_count_per_node göre birden çok işlem çalıştırabilir. Düğüm üzerindeki her bir işlemden günlükleri düzenlemek ve Print ve log ifadesini birleştirmek için aşağıda gösterildiği gibi ParallelRunStep günlükçü kullanılması önerilir. EntryScript 'ten bir günlükçüyle alınır ve günlüklerin, portalda **günlüklerde/Kullanıcı** klasöründe görünmesini sağlayabilirsiniz.

**Günlükçü kullanılarak örnek bir giriş betiği:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Tüm çalışanlarıma, bir arama tablosu içeren dosya veya dosyalar gibi bir yan girişi nasıl geçebilirim?

Kullanıcı, ParalleRunStep side_inputs parametresi kullanarak başvuru verilerini betiğe geçirebilir. Side_inputs olarak belirtilen tüm veri kümeleri her çalışan düğümüne bağlanır. Kullanıcı, bağımsız değişkeni geçirerek bağlama konumunu alabilir.

Başvuru verilerini içeren bir [veri kümesi](/python/api/azureml-core/azureml.core.dataset.dataset) oluşturun, bir yerel bağlama yolu belirtin ve çalışma alanınıza kaydedin. Bu `side_inputs` parametreye geçirin `ParallelRunStep` . Ayrıca, `arguments` bağlı yoluna kolayca erişmek için bölümüne yolunu ekleyebilirsiniz:

```python
local_path = "/tmp/{}".format(str(uuid.uuid4()))
label_config = label_ds.as_named_input("labels_input").as_mount(local_path)
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Bundan sonra, çıkarım betiğinizdeki (örneğin, init () yönteminde) aşağıdaki gibi erişebilirsiniz:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Giriş veri kümelerini hizmet sorumlusu kimlik doğrulamasıyla kullanma?

Kullanıcı, çalışma alanında kullanılan hizmet sorumlusu kimlik doğrulaması ile giriş veri kümelerini geçirebilir. ParallelRunStep içinde böyle bir veri kümesini kullanmak için veri kümesinin ParallelRunStep yapılandırması oluşturmak için kaydedilmesini gerekir.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>Sonraki adımlar

* Azure Machine Learning işlem [hatlarını gösteren bu Jupyıter not defterlerine](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines) bakın

* [Azureml-işlem hattı-adımlar](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) paketiyle ilgili yardım için SDK başvurusuna bakın. ParallelRunStep sınıfı için başvuru [belgelerini](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep) görüntüleyin.

* ParallelRunStep ile işlem hatlarını kullanma hakkında [Gelişmiş öğreticiyi](tutorial-pipeline-batch-scoring-classification.md) izleyin. Öğreticide, başka bir dosyanın bir yan giriş olarak nasıl geçirileceğini gösterilmektedir.
