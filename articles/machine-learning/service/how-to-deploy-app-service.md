---
title: Ml modellerini Azure App Service dağıtma (Önizleme)
titleSuffix: Azure Machine Learning
description: Azure App Service bir modeli bir Web uygulamasına dağıtmak için Azure Machine Learning nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 52c65bced1d78a4fdad1fbfd59c7a8d6d99d0c4a
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123286"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Azure App Service bir makine öğrenimi modeli dağıtma (Önizleme)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning bir modeli Azure App Service Web uygulaması olarak nasıl dağıtacağınızı öğrenin.

> [!IMPORTANT]
> Hem Azure Machine Learning hem de Azure App Service genel kullanıma sunulduğunda Machine Learning hizmetinden App Service bir model dağıtabilme özelliği önizlemededir.

Azure Machine Learning, eğitilen makine öğrenimi modellerinden Docker görüntüleri oluşturabilirsiniz. Bu görüntü, veri alan bir Web hizmeti içerir, bunu modele gönderir ve ardından yanıtı döndürür. Azure App Service görüntüyü dağıtmak için kullanılabilir ve aşağıdaki özellikleri sağlar:

* Gelişmiş güvenlik için Gelişmiş [kimlik doğrulaması](/azure/app-service/configure-authentication-provider-aad) . Kimlik doğrulama yöntemleri hem Azure Active Directory hem de Multi-Factor auth içerir.
* Yeniden dağıtmak zorunda kalmadan [Otomatik ölçeklendirme](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) .
* İstemcilerle hizmet arasında güvenli iletişim için [SSL desteği](/azure/app-service/configure-ssl-certificate-in-code) .

Azure App Service tarafından sunulan özellikler hakkında daha fazla bilgi için bkz. [App Service genel bakış](/azure/app-service/overview).

> [!IMPORTANT]
> Dağıtılan modelinizle kullanılan Puanlama verilerini veya Puanlama sonuçlarını günlüğe kaydetmek istiyorsanız, bunun yerine Azure Kubernetes hizmetine dağıtmanız gerekir. Daha fazla bilgi için bkz. [Üretim modellerinizde veri toplama](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure Machine Learning çalışma alanı. Daha fazla bilgi için [çalışma alanı oluşturma](how-to-manage-workspace.md) makalesine bakın.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Çalışma alanınıza kayıtlı eğitilen makine öğrenimi modeli. Bir modeliniz yoksa, görüntü oluşturma öğreticisini kullanın: eğitim ve kayıt yapmak için [modeli eğitme](tutorial-train-models-with-aml.md) .

    > [!IMPORTANT]
    > Bu makaledeki kod parçacıkları aşağıdaki değişkenleri ayarlamış olduğunu varsayar:
    >
    > * `ws`-Azure Machine Learning çalışma alanınız.
    > * `model`-dağıtılacak kayıtlı model.
    > * `inference_config`-modelin çıkarım yapılandırması.
    >
    > Bu değişkenleri ayarlama hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Dağıtıma hazırlanma

Dağıtılmadan önce, modeli bir Web hizmeti olarak çalıştırmak için gerekenleri tanımlamanız gerekir. Aşağıdaki listede bir dağıtım için gereken temel öğeler açıklanmaktadır:

* Bir __giriş betiği__. Bu betik istekleri kabul eder, modeli kullanarak isteği puan eder ve sonuçları döndürür.

    > [!IMPORTANT]
    > Giriş betiği modelinize özeldir; gelen istek verilerinin biçimini, modelinizde beklenen verilerin biçimini ve istemcilere döndürülen verilerin biçimini anlamalıdır.
    >
    > İstek verileri modelinize uygun olmayan bir biçimdeyse, komut dosyası bunu kabul edilebilir bir biçime dönüştürebilir. Ayrıca, istemciye döndürmeden önce yanıtı dönüştürebilir.

    > [!IMPORTANT]
    > Azure Machine Learning SDK, Web hizmeti için veri deposuna veya veri kümelerine erişim için bir yol sağlamaz. Dağıtım dışında depolanan verilere (örneğin, bir Azure depolama hesabında) erişmek için dağıtılan modele ihtiyacınız varsa, ilgili SDK 'yı kullanarak özel bir kod çözümü geliştirmeniz gerekir. Örneğin, [Python Için Azure depolama SDK 'sı](https://github.com/Azure/azure-storage-python).
    >
    > Senaryonuza yönelik olabilecek başka bir alternatif de [toplu tahmindir](how-to-run-batch-predictions.md). Bu, Puanlama sırasında veri depolarına erişim sağlar.

    Giriş betikleri hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md).

* Giriş betiğini veya modelini çalıştırmak için gereken yardımcı betikler veya Python/Conda paketleri gibi **Bağımlılıklar**

Bu varlıklar bir __çıkarım yapılandırmasında__kapsüllenir. Çıkarım yapılandırması, giriş betiğine ve diğer bağımlılıklara başvurur.

> [!IMPORTANT]
> Azure App Service ile kullanım için bir çıkarım yapılandırması oluştururken, bir [ortam](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) nesnesi kullanmanız gerekir. Aşağıdaki örnek, bir ortam nesnesi oluşturmayı ve bunu bir çıkarım yapılandırmasıyla kullanmayı gösterir:
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

Ortamlar hakkında daha fazla bilgi için bkz. [eğitim ve dağıtım için ortamları oluşturma ve yönetme](how-to-use-environments.md).

Çıkarım yapılandırması hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Azure App Service ' ye dağıtım yaparken, bir __dağıtım yapılandırması__oluşturmanız gerekmez.

## <a name="create-the-image"></a>Görüntü oluşturma

Azure App Service dağıtılan Docker görüntüsünü oluşturmak için [model. Package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)kullanın. Aşağıdaki kod parçacığı, modelden ve çıkarım yapılandırmasından nasıl yeni bir görüntü oluşturulacağını gösterir:

> [!NOTE]
> Kod parçacığı, `model` kayıtlı bir model içerdiğini ve `inference_config`, çıkarım ortamının yapılandırmasını içerdiğini varsayar. Daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

`show_output=True`, Docker Build işleminin çıktısı gösterilir. İşlem tamamlandıktan sonra görüntü, çalışma alanınızın Azure Container Registry oluşturulur. Görüntü derlendikten sonra Azure Container Registry konum görüntülenir. Döndürülen konum `<acrinstance>.azurecr.io/package:<imagename>`biçimindedir. Örneğin, `myml08024f78fd10.azurecr.io/package:20190827151241`.

> [!IMPORTANT]
> Görüntü dağıtımında kullanılan konum bilgilerini kaydedin.

## <a name="deploy-image-as-a-web-app"></a>Web uygulaması olarak görüntü dağıtma

1. Görüntüyü içeren Azure Container Registry oturum açma kimlik bilgilerini almak için aşağıdaki komutu kullanın. `<acrinstance>`, daha önce `package.location`döndürülen TH değeri ile değiştirin: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Bu komutun çıktısı aşağıdaki JSON belgesine benzer:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    __Kullanıcı adı__ ve __parolalardan__biri için değeri kaydedin.

1. Hizmeti dağıtmaya yönelik bir kaynak grubunuz veya App Service planınız yoksa, aşağıdaki komutlar her ikisinin de nasıl oluşturulacağını gösterir:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    Bu örnekte, __temel__ bir fiyatlandırma katmanı (`--sku B1`) kullanılır.

    > [!IMPORTANT]
    > Azure Machine Learning tarafından oluşturulan görüntüler Linux kullanır, bu nedenle `--is-linux` parametresini kullanmanız gerekir.

1. Web uygulamasını oluşturmak için aşağıdaki komutu kullanın. `<app-name>`, kullanmak istediğiniz adla değiştirin. `<acrinstance>` ve `<imagename>`, daha önce döndürülen `package.location` değerlerle değiştirin:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    Bu komut aşağıdaki JSON belgesine benzer bilgiler döndürür:

    ```json
    { 
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > Bu noktada, Web uygulaması oluşturulmuştur. Ancak, görüntüyü içeren Azure Container Registry kimlik bilgilerini sağlamadıysanız, Web uygulaması etkin değildir. Bir sonraki adımda, kapsayıcı kayıt defteri için kimlik doğrulama bilgilerini sağlarsınız.

1. Web uygulamasına kapsayıcı kayıt defterine erişmek için gereken kimlik bilgilerini sağlamak üzere aşağıdaki komutu kullanın. `<app-name>`, kullanmak istediğiniz adla değiştirin. `<acrinstance>` ve `<imagename>`, daha önce döndürülen `package.location` değerlerle değiştirin. `<username>` ve `<password>` daha önce alınan ACR oturum açma bilgileriyle değiştirin:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Bu komut aşağıdaki JSON belgesine benzer bilgiler döndürür:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

Bu noktada, Web uygulaması görüntüyü yüklemeye başlar.

> [!IMPORTANT]
> Görüntünün yüklenmesi birkaç dakika sürebilir. İlerlemeyi izlemek için aşağıdaki komutu kullanın:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Görüntü yüklendikten ve site etkin olduktan sonra, günlük `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`bildiren bir ileti görüntüler.

Görüntü dağıtıldıktan sonra, aşağıdaki komutu kullanarak ana bilgisayar adını bulabilirsiniz:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Bu komut aşağıdaki ana bilgisayar adına benzer bilgileri döndürür `<app-name>.azurewebsites.net`. Bu değeri, hizmetin __temel URL__ 'sinin bir parçası olarak kullanın.

## <a name="use-the-web-app"></a>Web uygulamasını kullanma

İstekleri modele geçiren Web hizmeti `{baseurl}/score`konumunda bulunur. Örneğin, `https://<app-name>.azurewebsites.net/score`. Aşağıdaki Python kodu, URL 'ye veri göndermeyi ve yanıtı görüntülemeyi gösterir:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Sonraki adımlar

* [Linux belgelerindeki App Service](/azure/app-service/containers/) Web uygulamanızı yapılandırmayı öğrenin.
* [Azure 'Da otomatik ölçeklendirme ile çalışmaya başlama](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)bölümünde ölçeklendirme hakkında daha fazla bilgi edinin.
* [Azure App Service BIR SSL sertifikası kullanın](/azure/app-service/configure-ssl-certificate-in-code).
* [App Service uygulamanızı Azure Active Directory oturum açma kullanacak şekilde yapılandırın](/azure/app-service/configure-authentication-provider-aad).
* [Bir web hizmeti olarak ML modeli kullanma](how-to-consume-web-service.md)
