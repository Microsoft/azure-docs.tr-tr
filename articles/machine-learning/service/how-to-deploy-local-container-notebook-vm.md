---
title: Modelleri dizüstü bilgisayar VM 'lerine dağıtma
titleSuffix: Azure Machine Learning
description: Azure Machine Learning modellerinizi Not defteri VM 'lerini kullanarak bir Web hizmeti olarak dağıtmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: d4e37b02b3d7a21546a04c8948fbbfb7262bfa6a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584765"
---
# <a name="deploy-a-model-to-azure-machine-learning-notebook-vms"></a>Azure Machine Learning Not defteri VM 'lerine model dağıtma

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning kullanarak bir modeli bir Web hizmeti olarak Azure Machine Learning Not defteri sanal makinenizde nasıl dağıtacağınızı öğrenin. Aşağıdaki koşullardan biri doğru ise Not defteri VM 'lerini kullanın:

- Modelinizi hızlıca dağıtmanız ve doğrulamanız gerekir.
- Geliştirme aşamasındaki bir modeli test edersiniz.

> [!TIP]
> Bir not defteri VM 'deki Jupyter Notebook bir modeli aynı VM 'deki bir Web hizmetine dağıtmak _yerel bir dağıtımdır_. Bu durumda, ' yerel ' bilgisayar, Not defteri sanal bilgisayardır. Dağıtımlar hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Ön koşullar

- Bir not defteri VM 'si çalıştıran bir Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [Kurulum ortamı ve çalışma alanı](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-notebook-vms"></a>Not defteri VM 'lerine dağıtma

Not defteri sanal makinenize yerel dağıtımları gösteren örnek bir not defteri dahildir. Not defterini yüklemek ve modeli bir Web hizmeti olarak sanal makineye dağıtmak için aşağıdaki adımları kullanın:

1. [Azure Machine Learning Studio](https://ml.azure.com)'Dan Azure Machine Learning Not defteri VM 'lerinizi seçin.

1. `samples-*` alt dizinini açın ve `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`açın. Açıldığında, Not defterini çalıştırın.

    ![Not defterindeki çalışan yerel hizmetin ekran görüntüsü](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. Not defteri, hizmetin üzerinde çalıştığı URL 'YI ve bağlantı noktasını görüntüler. Örneğin, `https://localhost:6789`. Bağlantı noktasını göstermek için `print('Local service port: {}'.format(local_service.port))` içeren hücreyi de çalıştırabilirsiniz.

    ![Çalışan yerel hizmet bağlantı noktasının ekran görüntüsü](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. Hizmeti not defteri VM 'sinden test etmek için `https://localhost:<local_service.port>` URL 'sini kullanın. Uzak bir istemciden test etmek için, Not defteri VM 'sinde çalışan hizmetin genel URL 'sini alın. Ortak URL belirlenebilir ve şu formülü kullanın; `https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score`. Örneğin, `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score`.

## <a name="test-the-service"></a>Hizmeti test etme

Çalışan hizmete örnek veri göndermek için aşağıdaki kodu kullanın. `service_url` değerini önceki adımdaki URL ile değiştirin:

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Sonraki adımlar

* [Özel bir Docker görüntüsü kullanarak model dağıtma](how-to-deploy-custom-docker-image.md)
* [Dağıtım sorunlarını giderme](how-to-troubleshoot-deployment.md)
* [SSL ile güvenli Azure Machine Learning Web Hizmetleri](how-to-secure-web-service.md)
* [Web hizmeti olarak dağıtılan bir ML modelini kullanma](how-to-consume-web-service.md)
* [Application Insights Azure Machine Learning modellerinizi izleyin](how-to-enable-app-insights.md)
* [Üretimde modeller için veri toplama](how-to-enable-data-collection.md)