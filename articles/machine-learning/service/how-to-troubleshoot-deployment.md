---
title: Dağıtım sorunlarını giderme kılavuzu
titleSuffix: Azure Machine Learning
description: Azure Kubernetes hizmeti ile genel Docker dağıtım hatalarını çözmenin, çözme ve sorunlarını giderme hakkında bilgi edinin ve Azure Machine Learning kullanarak Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 1dc66ae0f69c19524b32b55c654f7c8fd2d32762
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123216"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Azure Kubernetes hizmeti ve Azure Container Instances dağıtımı Azure Machine Learning sorunlarını giderme

Azure Machine Learning kullanarak Azure Container Instances (ACI) ve Azure Kubernetes hizmeti (AKS) ile genel Docker dağıtım hatalarını çözmenin veya çözme hakkında bilgi edinin.

Azure Machine Learning bir modeli dağıttığınızda, sistem bir dizi görevi gerçekleştirir. Dağıtım görevleri şunlardır:

1. Çalışma alanı model kayıt defterinde modeli kaydedin.

2. Bir Docker görüntüsü oluşturun dahil olmak üzere:
    1. Kayıt defterinden kayıtlı modeli indirin. 
    2. Bir dockerfile ortam yaml dosyasında belirttiğiniz bağımlılıkları temel bir Python ortamı oluşturun.
    3. Model dosyalarınızı ve sağladığınız Puanlama betiği dockerfile'da ekleyin.
    4. Dockerfile'ı kullanarak yeni bir Docker görüntüsü oluşturun.
    5. Çalışma alanı ile ilişkili Azure Container Registry ile Docker görüntü kaydedin.

    > [!IMPORTANT]
    > Kodunuza bağlı olarak, görüntü oluşturma, sizin giriş bilgileriniz olmadan otomatik olarak gerçekleşir.

3. Docker görüntüsünü Azure Container örneği (ACI) hizmetine veya Azure Kubernetes Service (AKS) dağıtın.

4. Yeni bir kapsayıcı (veya kapsayıcıları) ACI veya AKS başlatın. 

Bu işlem hakkında daha fazla bilgi [Model Yönetimi](concept-model-management-and-deployment.md) giriş.

## <a name="prerequisites"></a>Önkoşullar

* Bir **Azure aboneliği**. Bir tane yoksa, [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.
* [Azure MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Azure Machine Learning Için CLI uzantısı](reference-azure-machine-learning-cli.md).
* Yerel olarak hata ayıklamak için yerel sisteminizde çalışan bir Docker yüklemeniz olmalıdır.

    Docker yüklemenizi doğrulamak için, bir Terminal veya komut isteminden `docker run hello-world` komutunu kullanın. Docker 'ı yükleme veya Docker hataları sorunlarını giderme hakkında bilgi için bkz. [Docker belgeleri](https://docs.docker.com/).

## <a name="before-you-begin"></a>Başlamadan önce

Herhangi bir sorun çalıştırırsanız, yapılacak ilk şey dağıtım görevi bölmektir (önceki açıklanmıştır) sorunu ayırt etmek için tek tek adımlara.

Bu işlevlerin her ikisi de tek bir eylem olarak bahsedilen adımları gerçekleştirirken, [Web hizmeti. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none--overwrite-false-) API veya [WebService. deploy_from_model (](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none--overwrite-false-) Genellikle bu API 'Ler kullanışlıdır, ancak bunları aşağıdaki API çağrılarına değiştirerek sorun gidermeye yönelik adımları kesmeniz yardımcı olur.

1. Modeli kaydedin. Bazı örnek kodlar aşağıda verilmiştir:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Görüntü oluşturun. Bazı örnek kodlar aşağıda verilmiştir:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      entry_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Görüntü hizmeti olarak dağıtalım. Bazı örnek kodlar aşağıda verilmiştir:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Tek tek görevler dağıtım işlemine aşağı kıran sonra en yaygın hataların bazıları göz atabilirsiniz.

## <a name="image-building-fails"></a>Görüntü oluşturma başarısız

Docker görüntüsü derlenemez, [Image. wait_for_creation ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) veya [Service. wait_for_deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) çağrısı bazı ipuçları sunabileceği bazı hata iletileriyle başarısız olur. Görüntü oluşturma günlüğü hataları ile ilgili daha fazla ayrıntı bulabilirsiniz. Aşağıda bazı örnek kodlar görüntü derleme günlük URI'si bulma göstermez.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

Görüntü günlük URI'si, Azure blob Depolama'nızda depolanan bir günlük dosyasına işaret eden bir SAS URL'si ' dir. Yalnızca kopyalama ve yapıştırma URI ve bir tarayıcı penceresi içinde indirin ve günlük dosyasını görüntüleyin.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault erişim ilkesi ve Azure Resource Manager şablonları

Görüntü derlemesi, Azure Key Vault erişim ilkesiyle ilgili bir sorun nedeniyle da başarısız olabilir. Bu durum, çalışma alanını ve ilişkili kaynakları (Azure Key Vault dahil), birden çok kez oluşturmak için bir Azure Resource Manager şablonu kullandığınızda meydana gelebilir. Örneğin, şablonu bir sürekli tümleştirme ve dağıtım işlem hattının bir parçası ile aynı parametrelerle birden çok kez kullanmak.

Şablonlar aracılığıyla kaynak oluşturma işlemlerinin çoğu ıdempotent, ancak Key Vault şablon her kullanıldığında erişim ilkelerini temizler. Erişim ilkelerinin temizlenmesi, onu kullanan var olan bir çalışma alanının Key Vault erişimini keser. Yeni görüntü oluşturmaya çalıştığınızda bu durum hatalara neden olur. Aşağıda, alacağınız hataların örnekleri verilmiştir:

__Portal__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Bu sorundan kaçınmak için aşağıdaki yaklaşımlardan birini öneririz:

* Aynı parametreler için şablonu birden çok kez dağıtmayın. Ya da yeniden oluşturmak için şablonu kullanmadan önce mevcut kaynakları silin.
* Key Vault erişim ilkelerini inceleyin ve sonra şablonun `accessPolicies` özelliğini ayarlamak için bu ilkeleri kullanın.
* Key Vault kaynağının zaten var olup olmadığını denetleyin. Varsa, şablon aracılığıyla yeniden oluşturmayın. Örneğin, zaten varsa Key Vault kaynağı oluşturmayı devre dışı bırakmanızı sağlayan bir parametre ekleyin.

## <a name="debug-locally"></a>Yerel olarak hata ayıkla

Bir modeli ACG veya AKS 'e dağıtmaya yönelik sorunlarla karşılaşırsanız, yerel bir Web hizmeti olarak dağıtımı deneyin. Yerel bir Web hizmeti kullanmak, sorunları gidermenize daha kolay hale getirir. Modeli içeren Docker görüntüsü indirilip yerel sisteminizde başlatılır.

> [!WARNING]
> Yerel Web hizmeti dağıtımları, üretim senaryolarında desteklenmez.

Yerel olarak dağıtmak için kodunuzu bir dağıtım yapılandırması oluşturmak üzere `LocalWebservice.deploy_configuration()` kullanmak üzere değiştirin. Ardından, hizmeti dağıtmak için `Model.deploy()` kullanın. Aşağıdaki örnek, bir modeli (`model` değişkeninde yer alan) yerel bir Web hizmeti olarak dağıtır:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Bu noktada, hizmetle normal şekilde çalışabilirsiniz. Örneğin, aşağıdaki kod, hizmete veri gönderilmesini göstermektedir:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Hizmeti güncelleştirme

Yerel test sırasında, kayıt eklemek veya bulduğunuz herhangi bir sorunu çözmeye çalışmak için `score.py` dosyasını güncelleştirmeniz gerekebilir. `score.py` dosyadaki değişiklikleri yeniden yüklemek için `reload()`kullanın. Örneğin, aşağıdaki kod, hizmeti için betiği yeniden yükler ve ardından verileri bu sunucuya gönderir. Veriler, güncelleştirilmiş `score.py` dosyası kullanılarak puanlanır:

> [!IMPORTANT]
> `reload` yöntemi yalnızca yerel dağıtımlar için kullanılabilir. Bir dağıtımı başka bir işlem hedefine güncelleştirme hakkında daha fazla bilgi için bkz. dağıtım [modellerini](how-to-deploy-and-where.md#update)güncelleştirme bölümü.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Betik, hizmet tarafından kullanılan `InferenceConfig` nesnesi tarafından belirtilen konumdan yeniden yüklenir.

Modeli, Conda bağımlılıklarını veya dağıtım yapılandırmasını değiştirmek için [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)kullanın. Aşağıdaki örnek, hizmet tarafından kullanılan modeli güncelleştirir:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Hizmeti Sil

Hizmeti silmek için [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)kullanın.

### <a id="dockerlog"></a>Docker günlüğünü İnceleme

Hizmet nesnesinden ayrıntılı Docker altyapısı günlük iletilerini yazdırabilirsiniz. ACI, AKS ve yerel dağıtımlar için günlüğü görüntüleyebilirsiniz. Aşağıdaki örnek günlükleri nasıl yazdırabileceğinizi gösterir.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Hizmet başlatma başarısız

Görüntü başarıyla derlendikten sonra, sistem dağıtım yapılandırmanızı kullanarak bir kapsayıcı başlatmaya çalışır. Kapsayıcı başlatma artırma işleminin bir parçası olarak `init()` işlevi Puanlama komut dosyanızdaki sistem tarafından çağrılır. İçinde yakalanmamış istisnalar varsa `init()` görebileceğiniz işlev **CrashLoopBackOff** hata hata iletisi.

Günlükleri denetlemek için [Docker günlüğünü İnceleme](#dockerlog) bölümündeki bilgileri kullanın.

## <a name="function-fails-get_model_path"></a>İşlevi başarısız: get_model_path()

Genellikle, Puanlama betiğinin `init()` işlevinde, model dosyasını veya kapsayıcıdaki model dosyalarının bir klasörünü bulmak için [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) işlevi çağırılır. Model dosyası veya klasörü bulunamazsa, işlev başarısız olur. Çalıştırmak için bu hata ayıklama için en kolay yolu olan Python kodu kapsayıcı Kabuğu'nda aşağıdaki:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Bu örnek, Puanlama betiğinizin model dosyasını veya klasörünü bulmasını beklediği kapsayıcıda yerel yolu (`/var/azureml-app`göre) yazdırır. Ardından, dosya veya klasörün aslında burada olması beklenmektedir olup olmadığını doğrulayabilirsiniz.

Günlüğe kaydetme düzeyinin hata ayıklama olarak ayarlanması ek bilgilerin günlüğe kaydedilmesine neden olabilir ve bu da hatayı belirlemek için yararlı olabilir.

## <a name="function-fails-runinput_data"></a>İşlevi başarısız: run(input_data)

Hizmet başarıyla dağıtıldı, ancak Puanlama uç noktası veri göndermek çöküyor deyiminde yakalama hata ekleyebilirsiniz, `run(input_data)` ayrıntılı hata iletisi yerine döndürür, böylece işlev. Örneğin:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Not**: hata iletilerini döndüren `run(input_data)` sadece hata ayıklama için çağrısı yapılmalıdır. Güvenlik nedenleriyle, bir üretim ortamında bu şekilde hata iletileri döndürmemelisiniz.

## <a name="http-status-code-503"></a>HTTP durum kodu 503

Azure Kubernetes hizmet dağıtımları otomatik ölçeklendirmeyi destekler, bu da ek yükü desteklemek için çoğaltmaların eklenmesine izin verir. Ancak, otomatik Scaler, yükteki **aşamalı** değişiklikleri işlemek için tasarlanmıştır. Saniye başına isteklerde büyük ani artışlar alıyorsanız, istemciler bir HTTP durum kodu 503 alabilir.

503 durum kodlarının önlenmesine yardımcı olabilecek iki şey vardır:

* Otomatik ölçeklendirmenin yeni çoğaltmalar oluşturduğu kullanım düzeyini değiştirin.
    
    Varsayılan olarak, otomatik ölçeklendirme hedef kullanımı %70 olarak ayarlanır, bu da hizmetin saniyede %30 ' a varan (RPS) istek sayısını işleyebileceği anlamına gelir. `autoscale_target_utilization` daha düşük bir değere ayarlayarak kullanım hedefini ayarlayabilirsiniz.

    > [!IMPORTANT]
    > Bu değişiklik çoğaltmaların *daha hızlı*oluşturulmasına neden olmaz. Bunun yerine, daha düşük bir kullanım eşiğine göre oluşturulur. Hizmetin %70 olması beklenene kadar beklemek yerine %30 kullanım gerçekleştiğinde, çoğaltmanın oluşturulmasına neden olur.
    
    Web hizmeti zaten geçerli en fazla çoğaltmaları kullanıyorsa ve 503 durum kodu görmeye devam ediyorsanız, en fazla çoğaltma sayısını artırmak için `autoscale_max_replicas` değerini artırın.

* En az çoğaltma sayısını değiştirin. En düşük çoğaltmaları artırmak, gelen ani artışları işlemek için daha büyük bir havuz sağlar.

    En az çoğaltma sayısını artırmak için `autoscale_min_replicas` daha yüksek bir değere ayarlayın. Aşağıdaki kodu kullanarak gerekli çoğaltmaları hesaplayabilirsiniz ve değerleri projenize özgü değerlerle değiştirin:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Yeni en düşük çoğaltmalardan daha büyük istek ani artışları alırsanız, yeniden 503s alabilirsiniz. Örneğin, hizmetinizin trafiği arttıkça, en düşük çoğaltmaları artırmanız gerekebilir.

`autoscale_target_utilization`, `autoscale_max_replicas`ve `autoscale_min_replicas` ayarlama hakkında daha fazla bilgi için, bkz. [Akswebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) modül başvurusu.


## <a name="advanced-debugging"></a>Gelişmiş hata ayıklama

Bazı durumlarda, model dağıtımınızda bulunan Python kodunda etkileşimli olarak hata ayıklaması yapmanız gerekebilir. Örneğin, giriş betiği başarısız olursa ve neden ek günlüğe kaydetme ile saptanamaz. Visual Studio Code ve Visual Studio için Python Araçları (PTVSD) kullanarak Docker kapsayıcısının içinde çalışan koda iliştirebilirsiniz.

> [!IMPORTANT]
> Bu hata ayıklama yöntemi, bir modeli yerel olarak dağıtmak için `Model.deploy()` ve `LocalWebservice.deploy_configuration` kullanılırken çalışmaz. Bunun yerine, [containerımage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) sınıfını kullanarak bir görüntü oluşturmanız gerekir. 

Yerel Web hizmeti dağıtımları, yerel sisteminizde çalışan bir Docker yüklemesi gerektirir. Docker kullanma hakkında daha fazla bilgi için [Docker belgelerine](https://docs.docker.com/)bakın.

### <a name="configure-development-environment"></a>Geliştirme ortamını yapılandırma

1. Yerel VS Code geliştirme ortamınıza Visual Studio için Python Araçları (PTVSD) yüklemek için şu komutu kullanın:

    ```
    python -m pip install --upgrade ptvsd
    ```

    VS Code ile PTVSD kullanma hakkında daha fazla bilgi için bkz. [Uzaktan hata ayıklama](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Docker görüntüsüyle iletişim kurmak üzere VS Code yapılandırmak için yeni bir hata ayıklama yapılandırması oluşturun:

    1. VS Code, __Hata Ayıkla__ menüsünü ve ardından __yapılandırma aç__' ı seçin. __Launch. JSON__ adlı bir dosya açılır.

    1. __Launch. JSON__ dosyasında `"configurations": [`içeren satırı bulun ve sonra aşağıdaki metni ekleyin:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Konfigürasyonlar bölümünde zaten başka girdiler varsa, eklediğiniz koddan sonra bir virgül (,) ekleyin.

        Bu bölüm, 5678 numaralı bağlantı noktasını kullanarak Docker kapsayıcısına ekler.

    1. __Launch. JSON__ dosyasını kaydedin.

### <a name="create-an-image-that-includes-ptvsd"></a>PTVSD içeren bir görüntü oluşturma

1. Dağıtım için Conda ortamını, PTVSD öğesini içerecek şekilde değiştirin. Aşağıdaki örnek, `pip_packages` parametresi kullanılarak eklemeyi gösterir:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. PTVSD başlatmak ve hizmet başlatıldığında bir bağlantı beklemek için, `score.py` dosyanızın en üstüne aşağıdakileri ekleyin:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Hata ayıklama sırasında görüntünün dosyalarında yeniden oluşturmanız gerekmeden değişiklikler yapmak isteyebilirsiniz. Docker görüntüsüne bir metin Düzenleyicisi (VIM) yüklemek için `Dockerfile.steps` adlı yeni bir metin dosyası oluşturun ve dosyanın içeriği olarak aşağıdakileri kullanın:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    Bir metin Düzenleyicisi, yeni bir görüntü oluşturmadan değişiklikleri test etmek için Docker görüntüsündeki dosyaları değiştirmenize olanak sağlar.

1. `Dockerfile.steps` dosyasını kullanan bir görüntü oluşturmak için, bir görüntü oluştururken `docker_file` parametresini kullanın. Aşağıdaki örnek bunun nasıl yapılacağını göstermektedir:

    > [!NOTE]
    > Bu örnekte, `ws` Azure Machine Learning çalışma alanınıza işaret ettiğini ve bu `model` dağıtıldığı model olduğunu varsaymaktadır. `myenv.yml` dosyası, adım 1 ' de oluşturulan Conda bağımlılıklarını içerir.

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

Görüntü oluşturulduktan sonra, kayıt defterindeki görüntü konumu görüntülenir. Konum aşağıdaki metne benzer:

```text
myregistry.azurecr.io/myimage:1
```

Bu metin örneğinde, kayıt defteri adı `myregistry` ve görüntü `myimage`olarak adlandırılır. Görüntü sürümü `1`.

### <a name="download-the-image"></a>Görüntüyü indirin

1. Bir komut istemi, Terminal veya başka bir kabuk açın ve Azure Machine Learning çalışma alanınızı içeren Azure aboneliğinde kimlik doğrulaması yapmak için aşağıdaki [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) komutunu kullanın:

    ```azurecli
    az login
    ```

1. Görüntünüzü içeren Azure Container Registry (ACR) kimlik doğrulaması yapmak için aşağıdaki komutu kullanın. `myregistry`, görüntüyü kaydettiğinizde döndürülen ile değiştirin:

    ```azurecli
    az acr login --name myregistry
    ```

1. Görüntüyü yerel Docker 'a indirmek için aşağıdaki komutu kullanın. `myimagepath`, görüntüyü kaydettiğinizde döndürülen konumla değiştirin:

    ```bash
    docker pull myimagepath
    ```

    Görüntü yolu `myregistry.azurecr.io/myimage:1`benzer olmalıdır. Kayıt defteriniz `myregistry`, resminiz `myimage` ve `1` görüntü sürümüdür.

    > [!TIP]
    > Önceki adımdan alınan kimlik doğrulaması son olarak süresiz değildir. Kimlik doğrulama komutu ve çekme komutu arasında yeterince uzun süre beklerseniz bir kimlik doğrulama hatası alırsınız. Bu durumda, yeniden kimlik doğrulaması yapın.

    İndirme işleminin tamamlanma süresi Internet bağlantınızın hızına bağlıdır. İşlem sırasında bir indirme durumu görüntülenir. İndirme işlemi tamamlandıktan sonra, indirildiği doğrulamak için `docker images` komutunu kullanabilirsiniz.

1. Görüntüyle çalışmayı kolaylaştırmak için, bir etiket eklemek üzere aşağıdaki komutu kullanın. `myimagepath` değerini 2. adımdaki konum değeriyle değiştirin.

    ```bash
    docker tag myimagepath debug:1
    ```

    Adımlar geri kalanında, tam görüntü yolu değeri yerine `debug:1` olarak yerel görüntüye başvurabilirsiniz.

### <a name="debug-the-service"></a>Hizmette hata ayıkla

> [!TIP]
> `score.py` dosyasında PTVSD bağlantısı için bir zaman aşımı ayarlarsanız, zaman aşımı süresi dolmadan önce VS Code hata ayıklama oturumuna bağlamanız gerekir. VS Code başlatın, `score.py`yerel kopyasını açın, bir kesme noktası ayarlayın ve bu bölümdeki adımları kullanmadan önce başlamaya hazırlanın.
>
> Hata ayıklama ve kesme noktaları ayarlama hakkında daha fazla bilgi için bkz. [hata ayıklama](https://code.visualstudio.com/Docs/editor/debugging).

1. Görüntüyü kullanarak bir Docker kapsayıcısı başlatmak için aşağıdaki komutu kullanın:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Kapsayıcının içindeki PTVSD 'e VS Code iliştirmek için, VS Code açın ve F5 tuşunu kullanın veya __Hata Ayıkla__' yı seçin. İstendiğinde __Azure Machine Learning: Docker hata ayıklama__ yapılandırması ' nı seçin. Ayrıca, yan çubukta hata ayıklama simgesini, __Azure Machine Learning: Docker__ hata ayıklama girdisini hata ayıkla açılan menüsünden seçebilir ve ardından hata ayıklayıcıyı eklemek için yeşil oku kullanabilirsiniz.

    ![Hata ayıklama simgesi, hata ayıklamayı Başlat düğmesi ve yapılandırma Seçicisi](media/how-to-troubleshoot-deployment/start-debugging.png)

Bu noktada, VS Code Docker kapsayıcısının içindeki PTVSD 'a bağlanır ve daha önce ayarladığınız kesme noktasında durmaktadır. Artık kodu çalışırken, değişkenleri görüntülerken vb. adımları izleyebilirsiniz.

Python 'da hata ayıklamak için VS Code kullanma hakkında daha fazla bilgi için bkz. [Python kodunuzda hata ayıklama](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Kapsayıcı dosyalarını değiştirme

Görüntüdeki dosyalarda değişiklik yapmak için çalışan kapsayıcıya iliştirebilir ve bash kabuğu yürütebilirsiniz. Buradan, dosyaları düzenlemek için VIM kullanabilirsiniz:

1. Çalışan kapsayıcıya bağlanmak ve kapsayıcıda bir bash kabuğu başlatmak için aşağıdaki komutu kullanın:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Hizmet tarafından kullanılan dosyaları bulmak için, kapsayıcıdaki Bash kabuğundan aşağıdaki komutu kullanın:

    ```bash
    cd /var/azureml-app
    ```

    Buradan `score.py` dosyasını düzenlemek için VIM kullanabilirsiniz. VIM kullanma hakkında daha fazla bilgi için bkz. [VIM düzenleyicisini kullanma](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Kapsayıcıda yapılan değişiklikler normalde kalıcı olmaz. Yaptığınız değişiklikleri kaydetmek için aşağıdaki komutu kullanın, kabuktan çıkmadan önce, yukarıdaki adımda (başka bir kabukta) başlamadan önce aşağıdaki komutu kullanın:

    ```bash
    docker commit debug debug:2
    ```

    Bu komut, düzenlemelerinizi içeren `debug:2` adlı yeni bir görüntü oluşturur.

    > [!TIP]
    > Değişikliklerin etkili olabilmesi için geçerli kapsayıcıyı durdurmanız ve yeni sürümü kullanmaya başlamanız gerekir.

1. Kapsayıcıda bulunan dosyalarda yaptığınız değişiklikleri, VS Code yerel dosyalarla eşitlenmiş şekilde kaydettiğinizden emin olun. Aksi takdirde, hata ayıklayıcı deneyimi beklendiği gibi çalışmaz.

### <a name="stop-the-container"></a>Kapsayıcıyı durdur

Kapsayıcıyı durdurmak için aşağıdaki komutu kullanın:

```bash
docker stop debug
```

## <a name="next-steps"></a>Sonraki adımlar

Dağıtım hakkında daha fazla bilgi edinin:

* [Nasıl dağıtılacağı ve nerede](how-to-deploy-and-where.md)
* [Öğretici: Eğitme ve modelleri dağıtma](tutorial-train-models-with-aml.md)
