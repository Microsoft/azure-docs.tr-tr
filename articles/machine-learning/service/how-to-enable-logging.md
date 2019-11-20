---
title: Azure Machine Learning oturum açmayı etkinleştir
description: Hem varsayılan Python günlüğü paketini hem de SDK 'ya özgü işlevselliği kullanarak Azure Machine Learning oturum açmayı nasıl etkinleştireceğinizi öğrenin.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 07/12/2019
ms.openlocfilehash: 91e00b8e1d13f69ae3ba446bcbc09f06f387c439
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931127"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Azure Machine Learning oturum açmayı etkinleştir
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning Python SDK 'Sı, hem varsayılan Python günlüğü paketini hem de yerel günlüğe kaydetme ve portalda çalışma alanınıza oturum açmak için SDK 'ya özgü işlevselliği kullanarak günlüğü etkinleştirmenizi sağlar. Günlükler geliştiricilere uygulama durumu hakkında gerçek zamanlı bilgiler sağlar ve hataları veya uyarıları tanılamanıza yardımcı olabilir. Bu makalede, aşağıdaki alanlarda günlüğü etkinleştirmenin farklı yollarını öğreneceksiniz:

> [!div class="checklist"]
> * Eğitim modelleri ve işlem hedefleri
> * Görüntü oluşturma
> * Dağıtılan modeller
> * Python `logging` ayarları

[Azure Machine Learning çalışma alanı oluşturun](how-to-manage-workspace.md). SDK hakkında daha fazla bilgi için [Kılavuzu](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) kullanın.

## <a name="training-models-and-compute-target-logging"></a>Eğitim modelleri ve işlem hedefi günlüğü

Model eğitimi işlemi sırasında günlüğü etkinleştirmenin birden çok yolu vardır ve gösterilen örneklerde yaygın tasarım desenleri gösterilmektedir. `Experiment` sınıfında `start_logging` işlevini kullanarak, çalıştırmaya ilişkin verileri bulutta çalışma alanınıza kolayca kaydedebilirsiniz.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Ek günlüğe kaydetme işlevlerine yönelik [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) sınıfı için başvuru belgelerine bakın.

Eğitim süreci sırasında uygulama durumunun yerel günlük kaydını etkinleştirmek için `show_output` parametresini kullanın. Ayrıntılı günlüğün etkinleştirilmesi, eğitim sürecinin ayrıntılarını ve uzak kaynaklar veya işlem hedefleri hakkındaki bilgileri görmenizi sağlar. Deneme gönderimi sırasında günlüğe kaydetmeyi etkinleştirmek için aşağıdaki kodu kullanın.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Ayrıca, sonuç çalıştırmasında `wait_for_completion` işlevinde aynı parametreyi de kullanabilirsiniz.

```python
run.wait_for_completion(show_output=True)
```

SDK Ayrıca, eğitim için belirli senaryolarda varsayılan Python günlük paketi ' ni kullanmayı da destekler. Aşağıdaki örnek, bir `AutoMLConfig` nesnesindeki `INFO` günlüğe kaydetme düzeyini mümkün bir şekilde sunar.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task='regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

Kalıcı bir işlem hedefi oluştururken `show_output` parametresini de kullanabilirsiniz. İşlem hedefi oluşturma sırasında günlüğe kaydetmeyi etkinleştirmek için `wait_for_completion` işlevinde parametresini belirtin.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Görüntü oluşturma sırasında günlüğe kaydetme

Görüntü oluşturma sırasında günlüğe kaydetmenin etkinleştirilmesi, derleme işlemi sırasında herhangi bir hata görmenizi sağlar. `wait_for_deployment()` işlevindeki `show_output` param ' i ayarlayın.

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                       image=image,
                                       name="example-image",
                                       workspace=ws
                                       )

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Dağıtılan modeller için günlüğe kaydetme

Daha önce dağıtılan bir Web hizmetinden günlükleri almak için hizmeti yükleyin ve `get_logs()` işlevini kullanın. Günlükler, dağıtım sırasında oluşan hatalar hakkında ayrıntılı bilgiler içerebilir.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Ayrıca, istek/yanıt sürelerini, hata oranlarını ve özel durumları izlemenize olanak tanıyan Application Insights etkinleştirerek Web hizmetiniz için özel yığın izlemelerini günlüğe kaydedebilirsiniz. Application Insights etkinleştirmek için mevcut bir Web hizmetindeki `update()` işlevini çağırın.

```python
service.update(enable_app_insights=True)
```

Azure Machine Learning Studio 'da Application Insights nasıl çalışılacağı hakkında daha fazla bilgi için bkz. [nasıl yapılır](how-to-enable-app-insights.md) .

## <a name="python-native-logging-settings"></a>Python yerel günlük kaydı ayarları

SDK 'daki belirli günlüklerde, günlüğe kaydetme düzeyini hata ayıklama olarak ayarlamanıza olanak tanıyan bir hata bulunabilir. Günlüğe kaydetme düzeyini ayarlamak için betiğe aşağıdaki kodu ekleyin.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```
