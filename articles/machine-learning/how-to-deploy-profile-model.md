---
title: Profil modeli belleği ve CPU kullanımı
titleSuffix: Azure Machine Learning
description: Dağıtımdan önce modelinizin profilini oluşturmayı öğrenin. Profil oluşturma, modelinizin bellek ve CPU kullanımını belirler.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: b9ae40b3d2673961f9b84ed702f18b25b79b6d0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93320396"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>Kaynak kullanımını öğrenmek için modelinizin profilini yapın

Bu makalede, bir Web hizmeti olarak dağıtıldığında model için ne kadar CPU ve bellek ayrılacağını öğrenmek üzere bir makine öğrenimi modelinin profilini oluşturmayı gösterir.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, Azure Machine Learning bir modeli eğitidiğinizi ve kaydettiniz. Eğitim örneği için [buradaki örnek öğreticiye](how-to-train-scikit-learn.md) bakın ve Azure Machine Learning bir scikit-öğrenme modelini kaydetme.

## <a name="limitations"></a>Sınırlamalar

* Çalışma alanınızın Azure Container Registry (ACR) bir sanal ağın arkasında olması durumunda profil oluşturma işlemi çalışmaz.

## <a name="run-the-profiler"></a>Profil oluşturucuyu çalıştırma

Modelinize kaydolduktan ve dağıtımı için gereken diğer bileşenleri hazırladıktan sonra, dağıtılan hizmete gereken CPU ve belleği belirleyebilirsiniz. Profil oluşturma, modelinizi çalıştıran hizmeti sınar ve CPU kullanımı, bellek kullanımı ve yanıt gecikme süresi gibi bilgileri döndürür. Ayrıca, CPU ve bellek için kaynak kullanımına göre bir öneri sağlar.

Modelinizin profilini oluşturmanız için şunlar gerekir:
* Kayıtlı bir model.
* Giriş betiğinizi ve çıkarım ortam tanımınızı temel alan bir çıkarım yapılandırması.
* Her satırın örnek istek verilerini temsil eden bir dize içerdiği tek sütunlu tablosal veri kümesi.

> [!IMPORTANT]
> Bu noktada, yalnızca istek verilerinin dize olmasını bekleyen hizmetlerin profilini oluşturmayı destekliyoruz, örneğin: dize serileştirilmiş JSON, metin, dize serileştirilmiş görüntü, vb. Veri kümesinin (dize) her satırının içeriği HTTP isteğinin gövdesine konur ve Puanlama için modeli kapsüllemek için hizmete gönderilir.

> [!IMPORTANT]
> Yalnızca ChinaEast2 ve USGovArizona bölgesinde 2 adede kadar CPU profili oluşturmayı destekliyoruz.

Aşağıda, gelen istek verilerinin serileştirilmiş JSON içermesini bekleyen bir hizmetin profilini oluşturmak için bir giriş veri kümesi nasıl oluşturabileceğiniz hakkında bir örnek verilmiştir. Bu durumda, aynı istek verisi içeriğine sahip bir veri kümesi tabanlı 100 örnekleri oluşturduk. Gerçek dünyada senaryolarda, özellikle model kaynak kullanımınız/davranışı girişe bağımlıysa, çeşitli girişler içeren daha büyük veri kümeleri kullanmanızı öneririz.

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Örnek istek verilerini içeren veri kümesine sahip olduktan sonra bir çıkarım yapılandırması oluşturun. Çıkarım yapılandırması, score.py ve ortam tanımına dayalıdır. Aşağıdaki örnek, çıkarım yapılandırmasını oluşturmayı ve profil oluşturmayı çalıştırmayı gösterir:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


Aşağıdaki komut, CLı kullanarak bir modelin profilini oluşturmayı gösterir:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Profil oluşturma tarafından döndürülen bilgileri kalıcı hale getirmek için model için Etiketler veya özellikler kullanın. Etiketlerin veya özelliklerin kullanılması, verileri model kayıt defteri 'nde modeliyle depolar. Aşağıdaki örneklerde ve bilgileri içeren yeni bir etiket ekleme gösterilmektedir `requestedCpu` `requestedMemoryInGb` :
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

* [Başarısız bir dağıtımın sorunlarını giderme](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service’e dağıtma](how-to-deploy-azure-kubernetes-service.md)
* [Web hizmetlerini kullanmak için istemci uygulamaları oluşturma](how-to-consume-web-service.md)
* [Web hizmetini güncelleştirme](how-to-deploy-update-web-service.md)
* [Özel bir Docker görüntüsü kullanarak model dağıtma](how-to-deploy-custom-docker-image.md)
* [TLS kullanarak Azure Machine Learning aracılığıyla web hizmetinin güvenliğini sağlama](how-to-secure-web-service.md)
* [Application Insights Azure Machine Learning modellerinizi izleyin](how-to-enable-app-insights.md)
* [Üretimde modeller için veri toplama](how-to-enable-data-collection.md)
* [Model dağıtımları için olay uyarıları ve Tetikleyicileri oluşturma](how-to-use-event-grid.md)

