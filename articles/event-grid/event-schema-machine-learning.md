---
title: Azure Event Grid Machine Learning olay şeması
description: Azure Event Grid Machine Learning Çalışma Alanı olaylar için belirtilen özellikleri açıklar
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 5f2d23b3fe33691d37dc00b2d4e79036293252d9
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132881"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Azure Machine Learning için Azure Event Grid olay şeması

Bu makalede Machine Learning çalışma alanı olayları için özellikler ve şema sağlanmaktadır. Olay şemalarına giriş için bkz. [Azure Event Grid olay şeması](event-schema.md).

Örnek betiklerin ve öğreticilerin bir listesi için bkz. [AzureML olay kaynağı](event-sources.md#azure-machine-learning).

## <a name="available-event-types"></a>Kullanılabilir olay türleri

Azure Machine Learning aşağıdaki olay türlerini yayar:

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft. MachineLearningServices. ModelRegistered | Yeni bir model veya model sürümü başarıyla kaydettirilirse tetiklenir. |
| Microsoft. MachineLearningServices. Modeldağıtıldı | Model (ler) bir uç noktaya başarıyla dağıtıldığında tetiklenir. |
| Microsoft. MachineLearningServices. RunCompleted | Bir çalıştırma başarıyla tamamlandığında tetiklenir. |
| Microsoft. MachineLearningServices. Datasetdriftalgılandı | Bir veri kümesi DRFT izleyici, DRFT algıladığında tetiklenir. |

## <a name="the-contents-of-an-event-response"></a>Olay yanıtının içeriği

Bir olay tetiklendiğinde, Event Grid hizmeti bu olayla ilgili verileri bu olay ile abone olacak şekilde gönderir.

Bu bölüm, verilerin her olay için nasıl görüneceğine ilişkin bir örnek içerir.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Microsoft. MachineLearningServices. ModelRegistered olayı

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Microsoft. MachineLearningServices. Modeldağıtılan olay

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Microsoft. MachineLearningServices. RunCompleted olayı

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Microsoft. MachineLearningServices. Datasetdriftalgılanan olay

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Olay özellikleri

Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| konu başlığı | string | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| subject | string | Olay konusunun yayımcı tanımlı yolu. |
| eventType | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| eventTime | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| id | string | Etkinliğin benzersiz tanımlayıcısı. |
| data | object | BLOB depolama olay verileri. |
| dataVersion | string | Veri nesnesinin şema sürümü. Yayımcı, şema sürümünü tanımlar. |
| metadataVersion | string | Olay meta verilerinin şema sürümü. Event Grid üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |

Veri nesnesi, her olay türü için aşağıdaki özelliklere sahiptir:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft. MachineLearningServices. ModelRegistered

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| ModelName | string | Kayıtlı olan modelin adı. |
| ModelVersion | int | Kaydedilen modelin sürümü. |
| ModelTags | object | Kaydedilen modelin etiketleri. |
| ModelProperties | object | Kaydedilen modelin özellikleri. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft. MachineLearningServices. Modeldağıtıldı

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| serviceName | string | Dağıtılan hizmetin adı. |
| ServiceComputeType | string | Dağıtılan hizmetin işlem türü (ör. ACI, AKS). |
  | Modelıds | string | Model kimliklerinin virgülle ayrılmış listesi. Hizmette dağıtılan modellerin kimlikleri. |
| ServiceTags | object | Dağıtılan hizmetin etiketleri. |
| ServiceProperties | object | Dağıtılan hizmetin özellikleri. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft. MachineLearningServices. RunCompleted

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| ExperimentId | string | Çalıştırmanın ait olduğu denemenin KIMLIĞI. |
| ExperimentName | string | Çalıştırmanın ait olduğu denemenin adı. |
| RunId | string | Tamamlanan çalıştırmanın KIMLIĞI. |
| RunType | string | Tamamlanan çalıştırmanın çalıştırma türü. |
| RunTags | object | Tamamlanan çalıştırmanın etiketleri. |
| RunProperties | object | Tamamlanan çalıştırmanın özellikleri. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft. MachineLearningServices. Datasetdriftalgılandı

| Özellik | Tür | Açıklama |
| -------- | ---- | ----------- |
| Datadriftıd | string | Olayı tetikleyen veri Drın izleyicisinin KIMLIĞI. |
| DataDriftName | string | Olayı tetikleyen veri Drın izleyicisinin adı. |
| RunId | string | Veri kayması algılanan çalıştırmanın KIMLIĞI. |
| Basedatasetıd | string | Drift ' i algılamak için kullanılan temel veri kümesinin KIMLIĞI. |
| Targetdatasetıd | string | Drift ' i algılamak için kullanılan hedef veri kümesinin KIMLIĞI. |
| Driftkatsayısı | double | Olayı tetikleyen katsayı sonucu. |
| StartTime | datetime | Hedef veri kümesi zaman serisinin değişikliklerini algılamasına neden olan başlangıç saati.  |
| EndTime | datetime | Hedef veri kümesi zaman serisinin değişikliklerini algılamasına neden olan bitiş saati. |


## <a name="next-steps"></a>Sonraki adımlar

* Azure Event Grid giriş için bkz. [Event Grid nedir?](overview.md)
* Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md)
* Azure Machine Learning Azure Event Grid kullanmaya giriş için bkz. [Azure Machine Learning olaylarını](/azure/machine-learning/service/concept-event-grid-integration) kullanma
* Azure Machine Learning Azure Event Grid kullanmanın bir örneği için bkz. [olay odaklı makine öğrenimi iş akışları oluşturma](/azure/machine-learning/service/how-to-use-event-grid)
