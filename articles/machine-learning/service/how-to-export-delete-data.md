---
title: Çalışma alanı verilerini dışarı aktarma veya silme
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Studio, CLı, SDK ve kimliği doğrulanmış REST API 'Leri ile çalışma alanınızı dışarı aktarmayı veya silmeyi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 34ff3f9704b9c84a7daddcfd14fb9cd3e990f794
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73716517"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Machine Learning hizmeti çalışma alanı verilerinizi dışarı veya silme 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning, çalışma alanı verilerinizi kimliği doğrulanmış REST API dışa aktarabilir veya silebilirsiniz. Bu makalede nasıl yapılacağı açıklanır.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Çalışma alanı verilerinizi denetleme
Azure Machine Learning tarafından depolanan ürün içi veriler Azure Machine Learning Studio, CLı, SDK ve kimliği doğrulanmış REST API 'Leri aracılığıyla dışarı ve silmeye hazırdır. Telemetri verilerine Azure Gizlilik portalı üzerinden erişilebilir. 

Azure Machine Learning, kişisel veriler, çalışma geçmişi belgelerinin ve hizmet ile bazı kullanıcı etkileşimlerinin telemetri kayıtlarındaki Kullanıcı bilgilerinden oluşur.

## <a name="delete-workspace-data-with-the-rest-api"></a>REST API çalışma alanı verilerini silme 

Verileri silmek için aşağıdaki API çağrıları HTTP DELETE fiili ile yapılabilir. Bunlar, istekte bir `Authorization: Bearer <arm-token>` üst bilgisi bulundurarak yetkilendirilir; burada `<arm-token>`, `https://management.core.windows.net/` uç noktası için AAD erişim belirtecidir.  

Bu belirteci alma ve Azure uç noktalarını çağırma hakkında bilgi edinmek için bkz. [azure REST API belgeleri](https://docs.microsoft.com/rest/api/azure/).  

Aşağıdaki örneklerde {} içindeki metni, ilişkili kaynağı belirten örnek adlarıyla değiştirin.

### <a name="delete-an-entire-workspace"></a>Tüm çalışma alanını sil

Tüm çalışma alanını silmek için bu çağrıyı kullanın.  
> [!WARNING]
> Tüm bilgiler silinecek ve çalışma alanı artık kullanılabilir olmayacaktır.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Modelleri Sil

Modellerin ve kimliklerinin bir listesini almak için bu çağrıyı kullanın:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Tek tek modeller şu şekilde silinebilir:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Varlıkları Sil

Varlıkların bir listesini ve kimliklerini almak için bu çağrıyı kullanın:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Her bir varlık, ile silinebilir:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Görüntüleri Sil

Görüntülerin ve kimliklerinin bir listesini almak için bu çağrıyı kullanın:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Tek tek görüntüler şu şekilde silinebilir:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Hizmetleri Sil

Hizmetlerin ve kimliklerinin bir listesini almak için bu çağrıyı kullanın:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Ayrı hizmetler şu şekilde silinebilir:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Hizmet verilerini REST API dışa aktarma

Verileri dışarı aktarmak için, aşağıdaki API çağrıları HTTP GET fiili ile yapılabilir. Bunlar, istekte bir `Authorization: Bearer <arm-token>` üst bilgisi bulundurarak yetkilendirilir; burada `<arm-token>`, uç nokta için AAD erişim belirtecidir `https://management.core.windows.net/`  

Bu belirteci alma ve Azure uç noktalarını çağırma hakkında bilgi edinmek için bkz. [azure REST API belgeleri](https://docs.microsoft.com/rest/api/azure/).   

Aşağıdaki örneklerde {} içindeki metni, ilişkili kaynağı belirten örnek adlarıyla değiştirin.

### <a name="export-workspace-information"></a>Çalışma alanı bilgilerini dışarı aktar

Tüm çalışma alanlarının bir listesini almak için bu çağrıyı kullanın:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Tek bir çalışma alanıyla ilgili bilgiler şu şekilde edinilebilir:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Işlem bilgilerini dışarı aktar

Bir çalışma alanına bağlı tüm işlem hedefleri şu şekilde elde edilebilir:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Tek bir işlem hedefi hakkında bilgi şu şekilde edinilebilir:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Çalışma geçmişi verilerini dışarı aktar
Tüm denemeleri ve bilgilerinin bir listesini almak için bu çağrıyı kullanın:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Belirli bir deneme için tüm çalıştırmalar şu şekilde edinilebilir:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Çalıştırma geçmişi öğeleri şu şekilde elde edilebilir:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Bir deneme için tüm çalıştırma ölçümleri şu şekilde edinilebilir:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Tek bir çalıştırma ölçümü şu şekilde elde edilebilir:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Yapıtları dışarı aktar

Yapıtların ve yollarının bir listesini almak için bu çağrıyı kullanın:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Bildirimleri dışarı aktar

Saklı görevlerin bir listesini almak için bu çağrıyı kullanın:     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Tek bir görev için bildirimler şu şekilde elde edilebilir:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Veri depolarını dışarı aktar

Veri depolarının bir listesini almak için bu çağrıyı kullanın:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Tek tek veri depoları şu şekilde elde edilebilir:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Modelleri dışarı aktarma

Modellerin ve kimliklerinin bir listesini almak için bu çağrıyı kullanın:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Tek tek modeller şu şekilde edinilebilir:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Varlıkları dışarı aktar

Varlıkların bir listesini ve kimliklerini almak için bu çağrıyı kullanın:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Tek tek varlıklar şu şekilde edinilebilir:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Görüntüleri dışarı aktar

Görüntülerin ve kimliklerinin bir listesini almak için bu çağrıyı kullanın:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Tek tek görüntüler şu şekilde alınabilir:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Hizmetleri dışarı aktar

Hizmetlerin ve kimliklerinin bir listesini almak için bu çağrıyı kullanın:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Tek tek hizmetler şu şekilde edinilebilir:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Dışarı aktarma işlem hattı denemeleri

Tek tek denemeleri şu şekilde edinilebilir:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Ardışık düzen grafiklerini dışa aktarma

Tek tek grafikler şu şekilde alınabilir:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Ardışık düzen modüllerini dışarı aktarma

Modüller şu şekilde edinilebilir:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>İşlem hattı şablonlarını dışarı aktarma

Şablonlar şu şekilde edinilebilir:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>İşlem hattı veri kaynaklarını dışa aktarma

Veri kaynakları şu şekilde elde edilebilir:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Tasarımcıda varlıkları silme

Denemenizi oluşturduğunuz tasarımcıda tek tek varlıkları silin:

1. Tasarımcıya git

    ![Varlıkları Sil](media/how-to-export-delete-data.md/delete-experiment.png)

1. Listede, silinecek tek işlem hattı taslağını seçin.

1. **Sil**’i seçin.

### <a name="delete-datasets-in-the-designer"></a>Tasarımcıda veri kümelerini silme

Tasarımcıda veri kümelerini silmek için Azure portal veya Depolama Gezgini kullanarak bağlı depolama hesaplarına gidin ve veri kümelerini burada silin. Tasarımcıdaki veri kümelerinin kaydı, yalnızca depolamadaki başvuru noktasını kaldırır. 

## <a name="export-data-in-the-designer"></a>Tasarımcıda verileri dışarı aktarma

Denemenizi oluşturduğunuz tasarımcıda, eklediğiniz verileri dışarı aktarın:

1. Sol tarafta **veri kümeleri**' ni seçin.

    ![Verileri indir](media/how-to-export-delete-data.md/unregister-dataset.png)
