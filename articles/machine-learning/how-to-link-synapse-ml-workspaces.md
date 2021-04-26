---
title: SYNAPSE ve Azure Machine Learning çalışma alanlarıyla bağlantılı hizmet oluşturma (Önizleme)
titleSuffix: Azure Machine Learning
description: Birleşik bir veri denetimi deneyimi için Azure SYNAPSE ve Azure Machine Learning çalışma alanlarını bağlamayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 23184eee67013e39400446db5f744dd0ddb7bc50
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575759"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Azure SYNAPSE Analytics ve Azure Machine Learning çalışma alanlarını bağlama (Önizleme)

Bu makalede, [Azure SYNAPSE Analytics](/azure/synapse-analytics/overview-what-is) çalışma alanınızı ve [Azure Machine Learning çalışma alanınızı](concept-workspace.md)bağlayan bağlı bir hizmetin nasıl oluşturulacağını öğreneceksiniz.

Azure SYNAPSE çalışma alanınız ile bağlantılı Azure Machine Learning çalışma alanınız sayesinde, bir Apache Spark havuzu, ölçeklendirmekte olan veriler için ayrılmış bir işlem olarak ekleyebilir veya aynı Python not defteriyle model eğitimi gerçekleştirebilirsiniz.

[Python SDK 'sı](#link-sdk) veya [Azure MACHINE LEARNING Studio](#link-studio)aracılığıyla ml çalışma alanınızı ve SYNAPSE çalışma alanınızı bağlayabilirsiniz.

Ayrıca, çalışma alanlarını bağlayabilir ve tek bir [Azure Resource Manager (ARM) şablonuyla](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)bir Synapse Spark havuzu iliştirebilirsiniz.

>[!IMPORTANT]
> Azure Machine Learning ve Azure SYNAPSE tümleştirmesi genel önizlemededir. Paketten sunulan işlevler `azureml-synapse` [deneysel](/python/api/overview/azure/ml/#stable-vs-experimental) önizleme özellikleridir ve herhangi bir zamanda değişebilir.

## <a name="prerequisites"></a>Önkoşullar

* [Azure Machine Learning çalışma alanı oluşturun](how-to-manage-workspace.md?tabs=python).

* [Azure Portal bir Synapse çalışma alanı oluşturun](/azure/synapse-analytics/quickstart-create-workspace).

* [Azure portal, Web araçları veya SYNAPSE Studio kullanarak Apache Spark havuzu oluşturma](/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)

* [Azure Machine Learning Python SDK 'sını](/python/api/overview/azure/ml/intro) yükler

* [Azure Machine Learning Studio](https://ml.azure.com/)'ya erişin.

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Python SDK ile çalışma alanlarını bağlama

> [!IMPORTANT]
> SYNAPSE çalışma alanına başarıyla bağlantı sağlamak için SYNAPSE çalışma alanının **sahip** rolü verilmelidir. [Azure Portal](https://ms.portal.azure.com/)erişiminizi denetleyin.
>
> Bir **sahip** değilseniz ve yalnızca SYNAPSE çalışma alanına **katkıda** bulundıysanız yalnızca var olan bağlı hizmetleri kullanabilirsiniz. Bkz. [mevcut bir bağlı hizmeti alma ve kullanma](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service).

Aşağıdaki kod, [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) ve sınıflarını kullanır [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) ,

* Machine Learning çalışma alanınızı `ws` Azure SYNAPSE çalışma alanınıza bağlayın.
* SYNAPSE çalışma alanınızı bağlı hizmet olarak Azure Machine Learning kaydettirin.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> `system_assigned_identity_principal_id`Her bağlantılı hizmet için yönetilen bir kimlik oluşturulur. SYNAPSE oturumuna başlamadan önce, bu yönetilen kimliğe SYNAPSE çalışma alanının **Synapse Apache Spark Yöneticisi** rolü verilmelidir. [Synapse Apache Spark Yöneticisi rolünü SYNAPSE Studio 'daki yönetilen kimliğe atayın](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> `system_assigned_identity_principal_id`Belirli bir bağlı hizmeti bulmak için kullanın `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

### <a name="manage-linked-services"></a>Bağlı hizmetleri yönetme

Machine Learning çalışma alanınız ile ilişkili tüm bağlı hizmetleri görüntüleyin.

```python
LinkedService.list(ws)
```

Çalışma alanlarınızın bağlantısını kaldırmak için yöntemini kullanın `unregister()`

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Çalışma alanlarını Studio aracılığıyla bağlama

Machine Learning çalışma alanınızı ve SYNAPSE çalışma alanınızı, Azure Machine Learning Studio ile aşağıdaki adımlarla ilişkilendirin: 

1. [Azure Machine Learning Studio](https://ml.azure.com/)'da oturum açın.
1. Sol bölmedeki **Yönet** bölümünde **bağlı hizmetler** ' i seçin.
1. **Tümleştirme Ekle**' yi seçin.
1. **Bağlantı çalışma alanı** formunda, alanları doldurun

    |Alan| Açıklama    
    |---|---
    |Ad| Bağlı hizmetiniz için bir ad sağlayın. Bu ad, bu bağlı hizmete başvurmak için kullanılacak olan addır.
    |Abonelik adı | Makinenizin öğrenimi çalışma alanınız ile ilişkili aboneliğinizin adını seçin. 
    |SYNAPSE çalışma alanı | Bağlamak istediğiniz SYNAPSE çalışma alanını seçin.
    
1. **İleri ' yi** seçerek **Spark havuzlarını seçin (isteğe bağlı)** formunu açın. Bu formda, çalışma alanınıza iliştirilecek SYNAPSE Spark havuzunu seçersiniz

1. **İnceleme** formunu açmak ve seçimlerinizi denetlemek için **İleri ' yi** seçin.
1. Bağlı hizmet oluşturma işlemini gerçekleştirmek için **Oluştur** ' u seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Synapse (Önizleme) ile veri hazırlığı Için SYNAPSE Spark havuzları ekleyin](how-to-data-prep-synapse-spark-pool.md).
* [Azure SYNAPSE ile Machine Learning işlem hattınızda Apache Spark kullanma (Önizleme)](how-to-use-synapsesparkstep.md)
* [Bir modeli eğitme](how-to-set-up-training-targets.md).
