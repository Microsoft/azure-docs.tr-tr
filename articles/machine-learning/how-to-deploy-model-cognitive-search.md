---
title: Bilişsel Arama ile kullanmak üzere bir model dağıtma
titleSuffix: Azure Machine Learning
description: Bilişsel Arama ile kullanmak üzere bir modeli dağıtmak için Azure Machine Learning nasıl kullanacağınızı öğrenin. Model, arama deneyimini zenginleştirmek için özel bir beceri olarak kullanılır.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 03/11/2021
ms.custom: deploy
ms.openlocfilehash: 22c8880cbcde1f1a55fa66beee0323e0348e1164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149622"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>Bilişsel Arama ile kullanmak üzere bir model dağıtma


Bu makalede, [Azure bilişsel arama](../search/search-what-is-azure-search.md)ile kullanmak üzere bir modeli dağıtmak için Azure Machine Learning kullanma öğretilir.

Bilişsel Arama, heterojen içeriği üzerinde içerik işleme gerçekleştirerek, bu, insanlardan veya uygulamalarla sorgulanabilir hale gelir. Bu işlem, Azure Machine Learning dağıtılan bir model kullanılarak geliştirilebilir.

Azure Machine Learning, eğitilen bir modeli Web hizmeti olarak dağıtabilir. Web hizmeti daha sonra işleme işlem hattının bir parçası haline gelen Bilişsel Arama bir _yeteneğe_ katıştırılır.

> [!IMPORTANT]
> Bu makaledeki bilgiler, modelin dağıtımına özgüdür. Modelin Bilişsel Arama tarafından kullanılmasına izin veren desteklenen dağıtım yapılandırmalarına ilişkin bilgiler sağlar.
>
> Dağıtılan modeli kullanmak üzere Bilişsel Arama yapılandırma hakkında daha fazla bilgi için, bkz. [özel bir yetenek oluşturma ve dağıtma Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md) öğreticisi.
>
> Öğreticinin temel aldığı örnek için bkz [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) ..

Bir modeli Azure Bilişsel Arama ile kullanmak üzere dağıttığınızda, dağıtım aşağıdaki gereksinimleri karşılamalıdır:

* Modeli çıkarımı için barındırmak üzere Azure Kubernetes hizmetini kullanın.
* Azure Kubernetes hizmeti için Aktarım Katmanı Güvenliği 'ni (TLS) etkinleştirin. TLS, Bilişsel Arama ile dağıtılan model arasındaki HTTPS iletişimlerini güvenli hale getirmek için kullanılır.
* Giriş betiği, `inference_schema` hizmet için bir Openapı (Swagger) şeması oluşturmak üzere paketini kullanmalıdır.
* Giriş betiği Ayrıca JSON verilerini girdi olarak kabul etmelidir ve JSON 'u çıkış olarak oluşturur.


## <a name="prerequisites"></a>Önkoşullar

* Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

* Azure Machine Learning SDK yüklü bir Python geliştirme ortamı. Daha fazla bilgi için bkz. [SDK Azure Machine Learning](/python/api/overview/azure/ml/install).  

* Kayıtlı bir model. Bir modeliniz yoksa, üzerinde örnek Not defteri ' ni kullanın [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

* [Modellerin nasıl ve nereye dağıtılacağını](how-to-deploy-and-where.md)gösteren genel bir anlama.

## <a name="connect-to-your-workspace"></a>Çalışma alanınıza bağlanma

Azure Machine Learning çalışma alanı, Azure Machine Learning kullandığınızda oluşturduğunuz tüm yapıtlarla birlikte çalışmak için merkezi bir yer sağlar. Çalışma alanı; Günlükler, ölçümler, çıktılar ve betiklerinizin bir anlık görüntüsü dahil olmak üzere tüm eğitim çalıştırmaları geçmişini tutar.

Mevcut bir çalışma alanına bağlanmak için aşağıdaki kodu kullanın:

> [!IMPORTANT]
> Bu kod parçacığı, çalışma alanı yapılandırmasının geçerli dizine veya onun üst öğesine kaydedilmesini bekliyor. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanları oluşturma ve yönetme](how-to-manage-workspace.md). Yapılandırmayı dosyaya kaydetme hakkında daha fazla bilgi için bkz. [çalışma alanı yapılandırma dosyası oluşturma](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>Kubernetes kümesi oluşturma

**Tahmini süre**: yaklaşık 20 dakika.

Bir Kubernetes kümesi, Kapsayıcılı uygulamalar çalıştırmak için kullanılan bir sanal makine örnekleri kümesidir (düğümler olarak adlandırılır).

Azure Kubernetes hizmetine Azure Machine Learning bir model dağıttığınızda, model ve Web hizmeti olarak barındırmak için gereken tüm varlıklar bir Docker kapsayıcısına paketlenir. Bu kapsayıcı daha sonra kümeye dağıtılır.

Aşağıdaki kod, çalışma alanınız için yeni bir Azure Kubernetes Service (AKS) kümesi oluşturmayı göstermektedir:

> [!TIP]
> Ayrıca, Azure Machine Learning çalışma alanınıza mevcut bir Azure Kubernetes hizmeti ekleyebilirsiniz. Daha fazla bilgi için bkz. [modelleri Azure Kubernetes hizmetine dağıtma](how-to-deploy-azure-kubernetes-service.md).

> [!IMPORTANT]
> Kodun, `enable_ssl()` küme için Aktarım Katmanı Güvenliği 'ni (TLS) etkinleştirmek üzere yöntemini kullandığına dikkat edin. Bilişsel hizmetler 'den dağıtılan modeli kullanmayı planlarken bu gereklidir.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Azure, AKS kümesi mevcut olduğu sürece sizi faturalandıracaktır. İle işiniz bittiğinde AKS kümenizi sildiğinizden emin olun.

Azure Machine Learning ile AKS kullanma hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmetine dağıtım](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Giriş betiğini yazın

Giriş betiği Web hizmetine gönderilen verileri alır, modele geçirir ve Puanlama sonuçlarını döndürür. Aşağıdaki betik, modeli başlangıçta yükler ve ardından modeli kullanarak verileri puan alır. Bu dosya bazen çağrılır `score.py` .

> [!TIP]
> Giriş betiği, modelinize göre değişir. Örneğin, komut dosyası modelinizle, veri biçimlerinizin vb. ile kullanılacak çerçeveyi bilmelidir.

> [!IMPORTANT]
> Azure bilişsel hizmetler 'den dağıtılan modeli kullanmayı planlarken, `inference_schema` dağıtım için şema oluşturmayı etkinleştirmek üzere paketini kullanmanız gerekir. Bu paket, modeli kullanarak çıkarımı gerçekleştiren Web hizmeti için girdi ve çıktı veri biçimini tanımlamanıza olanak tanıyan dekoratörler sağlar.

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

Giriş betikleri hakkında daha fazla bilgi için bkz. [nasıl ve nereye dağıtılacak](how-to-deploy-and-where.md).

## <a name="define-the-software-environment"></a>Yazılım ortamını tanımlama

Ortam sınıfı, hizmet için Python bağımlılıklarını tanımlamak üzere kullanılır. Hem model hem de giriş betiği için gereken bağımlılıkları içerir. Bu örnekte, normal Pypı dizininden ve bir GitHub deposundan paketleri de yüklüyor. 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

Ortamlar hakkında daha fazla bilgi için bkz. [eğitim ve dağıtım için ortamları oluşturma ve yönetme](how-to-use-environments.md).

## <a name="define-the-deployment-configuration"></a>Dağıtım yapılandırmasını tanımlama

Dağıtım yapılandırması, Web hizmetini çalıştırmak için kullanılan Azure Kubernetes hizmeti barındırma ortamını tanımlar.

> [!TIP]
> Dağıtımınızın bellek, CPU veya GPU gereksinimleriyle ilgili olduğundan emin değilseniz, bu bilgileri öğrenmek için profil oluşturma 'yı kullanabilirsiniz. Daha fazla bilgi için bkz. [bir modelin nasıl ve nereye dağıtılacağı](how-to-deploy-and-where.md).

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

Daha fazla bilgi için bkz. [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)için başvuru belgeleri.

## <a name="define-the-inference-configuration"></a>Çıkarım yapılandırmasını tanımlama

Çıkarım yapılandırması giriş betiğine ve ortam nesnesine işaret eder:

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

Daha fazla bilgi için bkz. [ınenceconfig](/python/api/azureml-core/azureml.core.model.inferenceconfig)için başvuru belgeleri.

## <a name="deploy-the-model"></a>Modeli dağıtma

Modeli AKS kümenize dağıtın ve hizmetinizi oluşturmak için bekleyin. Bu örnekte, kayıt defterinden iki kayıtlı model yüklenir ve AKS 'e dağıtılır. Dağıtımdan sonra, `score.py` Dağıtımdaki dosya bu modelleri yükler ve çıkarımı yapmak için kullanır.

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

Daha fazla bilgi için bkz. [model](/python/api/azureml-core/azureml.core.model.model)için başvuru belgeleri.

## <a name="issue-a-sample-query-to-your-service"></a>Hizmetinize örnek bir sorgu verme

Aşağıdaki örnek, `aks_service` önceki kod bölümü tarafından değişkende depolanan dağıtım bilgilerini kullanır. Hizmet ile iletişim kurmak için gereken Puanlama URL 'sini ve kimlik doğrulama belirtecini almak için bu değişkeni kullanır:

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

Hizmetten döndürülen sonuç aşağıdaki JSON ile benzerdir:

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>Bilişsel Arama Bağlan

Bu modeli Bilişsel Arama kullanma hakkında daha fazla bilgi için, bkz. [Azure Machine Learning öğreticisi ile özel bir yetenek oluşturma ve dağıtma](../search/cognitive-search-tutorial-aml-custom-skill.md) .

## <a name="clean-up-the-resources"></a>Kaynakları temizleme

Bu örnek için AKS kümesini özel olarak oluşturduysanız, Bilişsel Arama ile test etmeyi tamamladıktan sonra kaynaklarınızı silin.

> [!IMPORTANT]
> Azure, AKS kümesinin ne kadar süreyle dağıtıldığını temel alır. Bu işlemi tamamladıktan sonra temizlediğinizden emin olun.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Machine Learning ile özel bir yetenek oluşturun ve dağıtın](../search/cognitive-search-tutorial-aml-custom-skill.md)