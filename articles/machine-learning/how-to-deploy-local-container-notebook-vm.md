---
title: İşlem örneklerine model dağıtma
titleSuffix: Azure Machine Learning
description: İşlem örnekleri kullanarak Azure Machine Learning modellerinizi Web hizmeti olarak dağıtmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: d8b1c2a5384e479e39d169d368554f16c300a33e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98954555"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Azure Machine Learning işlem örneklerine model dağıtma



Azure Machine Learning kullanarak bir modeli Azure Machine Learning işlem örneğiniz üzerinde Web hizmeti olarak dağıtma hakkında bilgi edinin. Aşağıdaki koşullardan biri doğru ise, işlem örnekleri kullanın:

- Modelinizi hızlıca dağıtmanız ve doğrulamanız gerekir.
- Geliştirme aşamasındaki bir modeli test edersiniz.

> [!TIP]
> Bir işlem örneğindeki Jupyter Notebook bir modeli aynı VM 'deki bir Web hizmetine dağıtmak _yerel bir dağıtımdır_. Bu durumda, ' yerel ' bilgisayar işlem örneğidir. Dağıtımlar hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Önkoşullar

- İşlem örneği çalıştıran bir Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [Kurulum ortamı ve çalışma alanı](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>İşlem örneklerine dağıtın

Yerel dağıtımları gösteren örnek bir not defteri, işlem örneğinize dahil edilmiştir. Not defterini yüklemek ve modeli bir Web hizmeti olarak sanal makineye dağıtmak için aşağıdaki adımları kullanın:

1. [Azure Machine Learning Studio](https://ml.azure.com)'Dan "Not defterleri" ni seçin ve ardından "örnek Not defterleri" altında How-to-Use-azureml/Deployment/Deploy-to-Local/Register-model-Deploy-Local. ipynb ' yi seçin. Bu Not defterini Kullanıcı klasörünüze kopyalayın.

1. 1. adımda kopyalanan Not defterini bulun, Not defterini çalıştırmak için bir Işlem örneği seçin veya oluşturun.

    ![Not defterindeki çalışan yerel hizmetin ekran görüntüsü](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)


1. Not defteri, hizmetin üzerinde çalıştığı URL 'YI ve bağlantı noktasını görüntüler. Örneğin, `https://localhost:6789`. Ayrıca, `print('Local service port: {}'.format(local_service.port))` bağlantı noktasını göstermek için içeren hücreyi de çalıştırabilirsiniz.

    ![Çalışan yerel hizmet bağlantı noktasının ekran görüntüsü](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Hizmeti bir işlem örneğinden test etmek için `https://localhost:<local_service.port>` URL 'yi kullanın. Uzak bir istemciden test etmek için, işlem örneğinde çalışan hizmetin genel URL 'sini alın. Ortak URL belirlenebilir ve şu formülü kullanın; 
    * Not defteri VM 'si: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score` . 
    * İşlem örneği: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score` . 

    Örneğin, 
    * Not defteri VM 'si: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * İşlem örneği: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Hizmeti test etme

Çalışan hizmete örnek veri göndermek için aşağıdaki kodu kullanın. Değerini `service_url` önceki adımdan URL 'si ile değiştirin:

> [!NOTE]
> İşlem örneğindeki bir dağıtımda kimlik doğrulanırken, Azure Active Directory kullanılarak kimlik doğrulaması yapılır. Örnek kodda öğesine yapılan çağrı, `interactive_auth.get_authentication_header()` AAD 'yi kullanarak kimlik doğrular ve daha sonra işlem örneğindeki hizmette kimlik doğrulaması yapmak için kullanılabilecek bir üst bilgi döndürür. Daha fazla bilgi için bkz. [Azure Machine Learning kaynakları ve iş akışları için kimlik doğrulamasını ayarlama](how-to-setup-authentication.md#interactive-authentication).
>
> Azure Kubernetes hizmetinde veya Azure Container Instances bir dağıtımda kimlik doğrulanırken, farklı bir kimlik doğrulama yöntemi kullanılır. Hakkında daha fazla bilgi için bkz. [Web Hizmetleri olarak dağıtılan Azure makine modelleri için kimlik doğrulamasını yapılandırma](how-to-authenticate-web-service.md).

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Sonraki adımlar

* [Özel bir Docker görüntüsü kullanarak model dağıtma](how-to-deploy-custom-docker-image.md)
* [Dağıtım sorunlarını giderme](how-to-troubleshoot-deployment.md)
* [TLS kullanarak Azure Machine Learning aracılığıyla web hizmetinin güvenliğini sağlama](how-to-secure-web-service.md)
* [Web hizmeti olarak dağıtılan bir ML modelini kullanma](how-to-consume-web-service.md)
* [Application Insights Azure Machine Learning modellerinizi izleyin](how-to-enable-app-insights.md)
* [Üretimde modeller için veri toplama](how-to-enable-data-collection.md)
