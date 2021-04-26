---
title: Tahmin Araçlarından ScriptRunConfig'e geçirme
titleSuffix: Azure Machine Learning
description: Eğitim işlerini yapılandırmak için estimators 'dan ScriptRunConfig 'e geçiş kılavuzu.
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: ae0623a11b940a4d142f6bfae02d4b20727a6f55
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518881"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>Estimators 'dan ScriptRunConfig 'e geçiş

Şu an kadar Azure Machine Learning bir eğitim işini, estimators, ScriptRunConfig ve alt düzey RunConfiguration dahil olmak üzere SDK aracılığıyla yapılandırmaya yönelik birden çok yöntem vardır.   Bu belirsizliğe ve tutarsızlığa yönelik olarak, Azure ML 'de iş yapılandırma işlemini basitleştireceğiz.  Artık eğitim işlerini yapılandırmak için önerilen seçenek olarak ScriptRunConfig kullanmanız gerekir. 

Estimators, Python SDK 'sının 1.19.0 sürümü ile kullanım dışıdır. Ayrıca, bir RunConfiguration nesnesini kendi kendinize açıkça örneklemeyi ve bunun yerine ScriptRunConfig sınıfını kullanarak işinizi yapılandırmanızı de önleyin.

Bu makalede, estimators 'dan ScriptRunConfig 'e geçiş yaparken sık görülen noktalar ele alınmaktadır.

> [!IMPORTANT]
> Estimators 'dan ScriptRunConfig 'e geçiş yapmak için Python SDK >= 1.15.0 kullandığınızdan emin olun.

## <a name="scriptrunconfig-documentation-and-samples"></a>ScriptRunConfig belgeleri ve örnekleri
Azure Machine Learning belge ve örnekler, iş yapılandırması ve gönderimi için [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) kullanacak şekilde güncelleştirilmiştir.

ScriptRunConfig kullanma hakkında daha fazla bilgi için aşağıdaki belgelere bakın:
* [Eğitim çalıştırmalarını yapılandırma ve gönderme](how-to-set-up-training-targets.md)
* [PyTorch eğitim çalıştırmalarını yapılandırma](how-to-train-pytorch.md)
* [TensorFlow eğitim çalıştırmalarını yapılandırma](how-to-train-tensorflow.md)
* [Scikit 'i yapılandırma-eğitim çalıştırmalarını öğrenin](how-to-train-scikit-learn.md)

Ayrıca, öğreticilerle & aşağıdaki örneklere bakın:
* [Azure/Machinelearningnot defterleri](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml-örnekler](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>Eğitim ortamını tanımlama
Çeşitli çerçeve tahminleri Docker görüntüleri tarafından desteklenen önceden yapılandırılmış ortamlara sahip olsa da, bu görüntüler için Dockerfiles özeldir.  Bu nedenle, bu ortamların neleri içerdiği konusunda çok sayıda saydamlığın olması gerekmez. Ayrıca, tahmini, `pip_packages` ilgili oluşturucularındaki ayrı parametreler (gibi) olarak ortamla ilgili yapılandırma de alır `custom_docker_image` .

ScriptRunConfig kullanılırken, ortamla ilgili tüm yapılandırma, `Environment` ScriptRunConfig oluşturucusunun parametresine geçirilen nesne içinde kapsüllenir `environment` . Bir eğitim işini yapılandırmak için eğitim betiğinizin gerektirdiği tüm bağımlılıklara sahip bir ortam sağlayın. Hiçbir ortam sağlanmazsa Azure ML, varsayılan ortam olarak tarafından tanımlanan Azure ML taban görüntülerinden birini kullanır `azureml.core.environment.DEFAULT_CPU_IMAGE` . Ortam sağlamanın birkaç yolu vardır:

* [Seçkin ortam kullanma](how-to-use-environments.md#use-a-curated-environment) -seçkin ortamlar, varsayılan olarak çalışma alanınızda bulunan önceden tanımlanmış ortamlardır. Her çerçeve tahmin eden her bir önceden yapılandırılmış çerçeve/sürüm Docker görüntüsü için ilgili bir seçkin ortam vardır.
* [Kendi özel ortamınızı tanımlama](how-to-use-environments.md)

Eğitim için seçkin pytorch 1,6 ortamını kullanmaya bir örnek aşağıda verilmiştir:

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

Eğitim betiğinin yürütüldüğü işlemde ayarlanacak **ortam değişkenlerini** belirtmek Istiyorsanız, ortam nesnesini kullanın:
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Azure ML ortamlarını yapılandırma ve yönetme hakkında bilgi için bkz.:
* [Ortamları kullanma](how-to-use-environments.md)
* [Seçilen ortamlar](resource-curated-environments.md)
* [Özel bir Docker görüntüsü ile eğitme](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>Eğitim için verileri kullanma
### <a name="datasets"></a>Veri kümeleri
Eğitim için bir Azure ML veri kümesi kullanıyorsanız, parametresini kullanarak veri kümesini betiğe bir bağımsız değişken olarak geçirin `arguments` . Bunu yaptığınızda, eğitim betiğinizdeki veri yolunu (takma noktası veya indirme yolu) bağımsız değişkenler aracılığıyla alacaksınız.

Aşağıdaki örnek, FileDataset 'In uzak işlem üzerine bağlanması için bir eğitim işi yapılandırır `mnist_ds` .
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (eski)
Azure ML veri kümelerini eski DataReference Way üzerinde kullanmanız önerilir, ancak herhangi bir nedenden dolayı Datareferde kullanıyorsanız, işinizi aşağıdaki şekilde yapılandırmanız gerekir:
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

Eğitim için veri kullanma hakkında daha fazla bilgi için bkz.
* [Azure ML 'de veri kümeleriyle eğitme](./how-to-train-with-datasets.md)

## <a name="distributed-training"></a>Dağıtılmış eğitim
Eğitim için bir dağıtılmış iş yapılandırmanız gerekiyorsa, `distributed_job_config` ScriptRunConfig oluşturucusunda parametresini belirterek bunu yapın. İlgili türlerin dağıtılmış işleri için bir [Mpiconation](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration), [pytorchconfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration)veya [tensorflowconfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) geçirin.

Aşağıdaki örnek, MPı/Horovod ile dağıtılmış eğitimi kullanmak için PyTorch eğitim işini yapılandırır:
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

Daha fazla bilgi için bkz.
* [PyTorch ile dağıtılmış eğitim](how-to-train-pytorch.md#distributed-training)
* [TensorFlow ile dağıtılmış eğitim](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>Çeşitli
Herhangi bir nedenle bir ScriptRunConfig için temel alınan RunConfiguration nesnesine erişmeniz gerekiyorsa, bunu aşağıdaki şekilde yapabilirsiniz:
```
src.run_config
```

## <a name="next-steps"></a>Sonraki adımlar

* [Eğitim çalıştırmalarını yapılandırma ve gönderme](how-to-set-up-training-targets.md)