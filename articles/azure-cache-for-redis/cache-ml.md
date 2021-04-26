---
title: Redsıs için Azure önbelleği ile bir makine öğrenimi modelini Azure Işlevlerine dağıtma
description: Bu makalede, Azure Işlevleri 'nde Azure Machine Learning bir modeli Redsıs örneği için Azure önbelleği kullanarak bir işlev uygulaması olarak dağıtacaksınız. Redsıs için Azure önbelleği son derece performans ve ölçeklenebilir bir Azure Machine Learning modeliyle eşlendiğinde uygulamanızda düşük gecikme süresi ve yüksek performans elde edersiniz.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: ec8943bc73cac2020350dd4916f040f031cd842b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499705"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>Redsıs için Azure önbelleği ile bir makine öğrenimi modelini Azure Işlevlerine dağıtma 

Bu makalede, Azure Işlevleri 'nde Azure Machine Learning bir modeli Redsıs örneği için Azure önbelleği kullanarak bir işlev uygulaması olarak dağıtacaksınız.  

Redsıs için Azure önbelleği son derece performans ve ölçeklenebilir bir Azure Machine Learning modeliyle eşlendiğinde uygulamanızda düşük gecikme süresi ve yüksek performans elde edersiniz. Verilerin çok fazla olması ve gerçek model çıkarım sonuçları için önbelleğin özellikle yararlı olduğu birkaç senaryo. Her iki senaryoda da meta veriler veya sonuçlar bellek içinde depolanır ve bu da performansı artırabilir. 

> [!NOTE]
> Hem Azure Machine Learning hem de Azure Işlevleri genel kullanıma sunulduğunda, Işlevleri için Machine Learning hizmetinden bir modeli paketleyebilme özelliği önizlemededir.  
>

## <a name="prerequisites"></a>Önkoşullar
* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/).
* Azure Machine Learning çalışma alanı. Daha fazla bilgi için [çalışma alanı oluşturma](../machine-learning/how-to-manage-workspace.md) makalesine bakın.
* [Azure CLI](/cli/azure/install-azure-cli).
* Çalışma alanınıza kayıtlı eğitilen makine öğrenimi modeli. Bir modeliniz yoksa, görüntü oluşturma öğreticisini kullanın: eğitim ve kayıt yapmak için [modeli eğitme](../machine-learning/tutorial-train-models-with-aml.md) .

> [!IMPORTANT]
> Bu makaledeki kod parçacıkları aşağıdaki değişkenleri ayarlamış olduğunu varsayar:
>
> * `ws` -Azure Machine Learning çalışma alanınız.
> * `model` -Dağıtılacak kayıtlı model.
> * `inference_config` -Modelin çıkarım yapılandırması.
>
> Bu değişkenleri ayarlama hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](../machine-learning/how-to-deploy-and-where.md).

## <a name="create-an-azure-cache-for-redis-instance"></a>Redsıs örneği için Azure önbelleği oluşturma 
Herhangi bir temel, standart veya Premium önbellek örneğiyle Azure Işlevlerine bir makine öğrenimi modeli dağıtabileceksiniz. Bir önbellek örneği oluşturmak için aşağıdaki adımları izleyin.  

1. Azure portal giriş sayfasına gidin veya kenar çubuğu menüsünü açın ve ardından **kaynak oluştur**' u seçin. 
   
1. **Yeni** sayfada **veritabanları** ' nı seçin ve ardından **redsıs için Azure önbelleği**' ni seçin.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Redsıs için Azure önbelleği ' ni seçin.":::
   
1. **Yeni Redis Cache** sayfasında, yeni önbelleğiniz için ayarları yapılandırın.
   
   | Ayar      | Önerilen değer  | Açıklama |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS adı** | Genel olarak benzersiz bir ad girin. | Önbellek adı, yalnızca rakam, harf veya kısa çizgi içeren 1 ile 63 karakter arasında bir dize olmalıdır. Ad bir sayı veya harfle başlamalı ve bitmeli ve ardışık kısa çizgi içeremez. Önbellek örneğinizin *ana bilgisayar adı* *\<DNS name> . Redis.cache.Windows.net* olacaktır. | 
   | **Abonelik** | Açılır ve aboneliğinizi seçin. | Redsıs örneği için bu yeni Azure önbelleğinin oluşturulacağı abonelik. | 
   | **Kaynak grubu** | Açılır ve bir kaynak grubu seçin veya **Yeni oluştur** ' u seçin ve yeni bir kaynak grubu adı girin. | Önbelleğinizin ve diğer kaynaklarınızın oluşturulacağı kaynak grubunun adı. Tüm uygulama kaynaklarınızı tek bir kaynak grubuna yerleştirerek, bunları birlikte kolayca yönetebilir veya silebilirsiniz. | 
   | **Konum** | Açılır ve bir konum seçin. | Önbelleğinizi kullanacak diğer hizmetlerin yakınında bir [bölge](https://azure.microsoft.com/regions/) seçin. |
   | **Fiyatlandırma katmanı** | Açılır ve bir [fiyatlandırma katmanı](https://azure.microsoft.com/pricing/details/cache/)seçin. |  Fiyatlandırma katmanı önbellek için kullanılabilen boyut, performans ve özellikleri belirler. Daha fazla bilgi için bkz. [redsıs Için Azure önbelleği 'Ne genel bakış](cache-overview.md). |

1. **Ağ** sekmesini seçin veya sayfanın altındaki **ağ** düğmesine tıklayın.

1. **Ağ** sekmesinde, bağlantı yönteminizi seçin.

1. **İleri: Gelişmiş** sekmesini seçin veya sayfanın altındaki **İleri: Gelişmiş** düğmesine tıklayın.

1. Bir temel veya standart önbellek örneğinin **Gelişmiş** SEKMESINDE, TLS olmayan bir bağlantı noktasını etkinleştirmek istiyorsanız geçişi etkinleştir ' i seçin.

1. Premium önbellek örneğinin **Gelişmiş** SEKMESINDE, TLS olmayan bağlantı noktası, kümeleme ve veri kalıcılığı için ayarları yapılandırın.

1. **Sonraki: Etiketler** sekmesini seçin veya sayfanın altındaki **Sonraki: Etiketler** düğmesine tıklayın.

1. İsteğe bağlı olarak, **Etiketler** sekmesinde, kaynağı sınıflandırmak istiyorsanız ad ve değeri girin. 

1. **Gözden geçir ve oluştur**’u seçin. Azure 'un yapılandırmanızı doğruladığı, gözden geçir + Oluştur sekmesine götürülürsünüz.

1. Yeşil doğrulama başarılı iletisi göründüğünde **Oluştur**' u seçin.

Önbelleğin oluşturulması biraz zaman alır. Redsıs **genel bakış** sayfasında ilerlemeyi izleyebilirsiniz. **Durum** **çalışıyor** olarak görüntülendiğinde, önbellek kullanıma hazırdır. 

## <a name="prepare-for-deployment"></a>Dağıtım için hazırlanma

Dağıtılmadan önce, modeli bir Web hizmeti olarak çalıştırmak için gerekenleri tanımlamanız gerekir. Aşağıdaki liste, bir dağıtım için gereken temel öğeleri açıklar:

* Bir __giriş betiği__. Bu betik istekleri kabul eder, modeli kullanarak isteği puan eder ve sonuçları döndürür.

    > [!IMPORTANT]
    > Giriş betiği modelinize özeldir; gelen istek verilerinin biçimini, modelinizde beklenen verilerin biçimini ve istemcilere döndürülen verilerin biçimini anlamalıdır.
    >
    > İstek verileri modelinize uygun olmayan bir biçimdeyse, komut dosyası bunu kabul edilebilir bir biçime dönüştürebilir. Ayrıca, istemciye döndürmeden önce yanıtı dönüştürebilir.
    >
    > Varsayılan olarak, işlevler için paketleme sırasında giriş metin olarak değerlendirilir. Girişin ham baytlarını kullanmak istiyorsanız (örneğin, blob Tetikleyicileri için), [ham verileri kabul etmek Için Amlrequest](../machine-learning/how-to-deploy-advanced-entry-script.md#binary-data)' i kullanmanız gerekir.

Run işlevi için bir redo uç noktaya bağlandığından emin olun.

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Giriş betiği hakkında daha fazla bilgi için bkz [. Puanlama kodu tanımlama.](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-entry-script)

* Giriş betiğini veya modelini çalıştırmak için gereken yardımcı betikler veya Python/Conda paketleri gibi **Bağımlılıklar**

Bu varlıklar bir __çıkarım yapılandırmasında__ kapsüllenir. Çıkarım yapılandırması, giriş betiğine ve diğer bağımlılıklara başvurur.

> [!IMPORTANT]
> Azure Işlevleri ile kullanmak üzere bir çıkarım yapılandırması oluştururken, bir [ortam](/python/api/azureml-core/azureml.core.environment%28class%29) nesnesi kullanmanız gerekir. Özel bir ortam tanımlıyorsanız, bir PIP bağımlılığı olarak >= 1.0.45 sürümü ile azureml-varsayılan değer eklemeniz gerektiğini unutmayın. Bu paket, modeli bir Web hizmeti olarak barındırmak için gereken işlevleri içerir. Aşağıdaki örnek, bir ortam nesnesi oluşturmayı ve bunu bir çıkarım yapılandırmasıyla kullanmayı gösterir:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Ortamlar hakkında daha fazla bilgi için bkz. [eğitim ve dağıtım için ortamları oluşturma ve yönetme](../machine-learning/how-to-use-environments.md).

Çıkarım yapılandırması hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-inference-configuration).

> [!IMPORTANT]
> IŞLEVLERE dağıtım yaparken, bir __dağıtım yapılandırması__ oluşturmanız gerekmez.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Işlevler desteği için SDK önizleme paketini yükler

Azure Işlevlerine yönelik paketler oluşturmak için SDK önizleme paketini yüklemelisiniz.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Görüntü oluşturma

Azure Işlevlerine dağıtılan Docker görüntüsünü oluşturmak için, kullanarak ilgilendiğiniz tetikleyici için [azureml. contrib. Functions. Package](/python/api/azureml-contrib-functions/azureml.contrib.functions) veya belirli paket işlevini kullanın. Aşağıdaki kod parçacığı, model ve çıkarım yapılandırmasından HTTP tetikleyicisi ile yeni bir paket oluşturmayı göstermektedir:

> [!NOTE]
> Kod parçacığı, `model` kayıtlı bir model içerdiğini ve `inference_config` çıkarım ortamının yapılandırmasını içeren olduğunu varsayar. Daha fazla bilgi için bkz. [Azure Machine Learning modelleri dağıtma](../machine-learning/how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

Ne zaman `show_output=True` , Docker Build işleminin çıktısı gösterilir. İşlem tamamlandıktan sonra görüntü, çalışma alanınızın Azure Container Registry oluşturulur. Görüntü derlendikten sonra Azure Container Registry konum görüntülenir. Döndürülen konum biçimindedir `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> Işlevlerin paketlenmesi Şu anda HTTP tetikleyicilerini, blob Tetikleyicileri ve Service Bus tetikleyicilerini desteklemektedir. Tetikleyiciler hakkında daha fazla bilgi için bkz. [Azure işlevleri bağlamaları](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns).

> [!IMPORTANT]
> Görüntü dağıtımında kullanılan konum bilgilerini kaydedin.

## <a name="deploy-image-as-a-web-app"></a>Web uygulaması olarak görüntü dağıtma

1. Görüntüyü içeren Azure Container Registry oturum açma kimlik bilgilerini almak için aşağıdaki komutu kullanın. `<myacr>`Daha önce döndürülen değerle değiştirin `package.location` : 

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

    __Kullanıcı adı__ ve __parolalardan__ biri için değeri kaydedin.

1. Hizmeti dağıtmaya yönelik bir kaynak grubunuz veya App Service planınız yoksa, aşağıdaki komutlar her ikisinin de nasıl oluşturulacağını gösterir:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    Bu örnekte, bir _Linux temel_ Fiyatlandırma Katmanı ( `--sku B1` ) kullanılır.

    > [!IMPORTANT]
    > Azure Machine Learning tarafından oluşturulan görüntüler Linux kullanır, bu nedenle parametresini kullanmanız gerekir `--is-linux` .

1. Web işi depolaması için kullanılacak depolama hesabını oluşturun ve bağlantı dizesini alın. `<webjobStorage>`Kullanmak istediğiniz adla değiştirin.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. İşlev uygulaması oluşturmak için aşağıdaki komutu kullanın. `<app-name>`Kullanmak istediğiniz adla değiştirin. `<acrinstance>`Ve `<imagename>` değerlerini daha önce döndürülen değerlerle değiştirin `package.location` . `<webjobStorage>`Önceki adımda bulunan depolama hesabının adıyla değiştirin:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > Bu noktada, işlev uygulaması oluşturulmuştur. Ancak, HTTP tetikleyicisi için bağlantı dizesini veya görüntüyü içeren Azure Container Registry kimlik bilgilerini sağlamadıysanız, işlev uygulaması etkin değil. Sonraki adımlarda, kapsayıcı kayıt defteri için bağlantı dizesini ve kimlik doğrulama bilgilerini sağlarsınız. 

1. İşlev uygulamasını kapsayıcı kayıt defterine erişmek için gereken kimlik bilgileriyle sağlamak için aşağıdaki komutu kullanın. `<app-name>`İşlev uygulamasının adıyla değiştirin. `<acrinstance>`Ve `<imagetag>` değerlerini, önceki ADıMDA bulunan az CLI çağrısındaki değerlerle değiştirin. `<username>`Ve `<password>` daha önce alınan ACR oturum açma bilgileriyle değiştirin:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

Bu noktada, işlev uygulaması görüntüyü yüklemeye başlar.

> [!IMPORTANT]
> Görüntünün yüklenmesi birkaç dakika sürebilir. Azure portal kullanarak ilerlemeyi izleyebilirsiniz.

## <a name="test-azure-functions-http-trigger"></a>Azure Işlevleri HTTP tetikleyicisini test et 

Şimdi Azure Işlevleri HTTP tetikleyicimizi çalıştırıp test edeceğiz.

1. Azure portal işlev uygulamanıza gidin.
1. Geliştirici altında **kod + test**' i seçin. 
1. Sağ taraftaki **giriş** sekmesini seçin. 
1. **Çalıştır** düğmesine tıklayarak Azure işlevleri http tetikleyicisini test edin. 

Bir modeli, Redsıs örneği için Azure önbelleğini kullanarak bir işlev uygulaması olarak Azure Machine Learning başarıyla dağıttınız. Aşağıdaki bölümdeki bağlantılara giderek Red. için Azure önbelleği hakkında daha fazla bilgi edinin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki öğreticiyle devam ediyorsanız, bu hızlı başlangıçta oluşturulan kaynakları tutabilir ve sonraki öğreticide yeniden kullanabilirsiniz.

Aksi takdirde, hızlı başlangıç ile işiniz bittiğinde, ücretlerden kaçınmak için bu hızlı başlangıçta oluşturduğunuz Azure kaynaklarını silebilirsiniz. 

> [!IMPORTANT]
> Silinen kaynak grupları geri alınamaz. Kaynak grubunu sildiğinizde, içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. Bu örneği, tutmak istediğiniz kaynakları içeren mevcut bir kaynak grubunda barındırmak için kaynaklar oluşturduysanız, kaynak grubunu silmek yerine her kaynağı kendi ilgili dikey penceresinden tek tek silebilirsiniz.

### <a name="to-delete-a-resource-group"></a>Kaynak grubunu silmek için

1. [Azure portalında](https://portal.azure.com) oturum açın ve **Kaynak grupları**’nı seçin.

2. **Ada göre filtrele...** kutusuna kaynak grubunuzun adını girin. Sonuç listesindeki kaynak grubunuzda **...** düğmesini ve sonra **Kaynak grubunu sil**’i seçin.

Kaynak grubunun silinmesini onaylamanız istenir. Onaylamak için kaynak grubunuzun adını yazın ve ardından **Sil**’i seçin.

Birkaç dakika sonra kaynak grubu ve bu gruptaki kaynakların tümü silinir.

## <a name="next-steps"></a>Sonraki adımlar 

* [Redsıs Için Azure önbelleği](./cache-overview.md) hakkında daha fazla bilgi edinin
* [İşlevler](../azure-functions/functions-create-function-linux-custom-image.md) belgelerinde işlev uygulamanızı yapılandırmayı öğrenin.
* [API Başvurusu](/python/api/azureml-contrib-functions/azureml.contrib.functions) 
* [Redsıs Için Azure önbelleği kullanan bir Python uygulaması](./cache-python-get-started.md) oluşturma
