---
title: Yerel olarak çalıştırma ve dağıtma
titleSuffix: Azure Machine Learning
description: Bu makalede, Azure Machine Learning ' de oluşturulan modelleri eğitim, hata ayıklama veya dağıtmaya yönelik bir hedef olarak yerel bilgisayarınızın nasıl kullanılacağı açıklanır.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: 75836580fc2dc5a2090047865610e26d856387b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861237"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>Yerel makinelerinizde Azure Machine Learning eğitilen modeller dağıtın 

Bu makalede, Azure Machine Learning ' de oluşturulan modellerin eğitimi veya dağıtımı için yerel bilgisayarınızın bir hedef olarak nasıl kullanılacağı açıklanır. Azure Machine Learning, en çok Python Machine Learning çerçeveleri ile çalışmak için yeterince esnektir. Makine öğrenimi çözümleri genellikle, yinelenmesinin zor olduğu karmaşık bağımlılıklara sahiptir. Bu makalede, toplam denetimin kullanım kolaylığı ile nasıl dengelenmesi gösterilmektedir.

Yerel dağıtım senaryoları şunları içerir:

* Verileri, betikleri ve modelleri bir projede erkenden hızlı bir şekilde yineleme.
* Sonraki aşamalarda hata ayıklama ve sorun giderme.
* Kullanıcı tarafından yönetilen donanımda son dağıtım.

## <a name="prerequisites"></a>Önkoşullar

- Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).
- Model ve ortam. Eğitilen bir modeliniz yoksa, [Bu öğreticide](tutorial-train-models-with-aml.md)verilen model ve bağımlılık dosyalarını kullanabilirsiniz.
- [Python için Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/intro).
- Azure Machine Learning paket bağımlılıklarını yansıtmak istiyorsanız, Anaconda veya Miniconda gibi Conda Yöneticisi.
- Docker, Azure Machine Learning ortamının kapsayıcılı bir sürümünü kullanmak istiyorsanız.

## <a name="prepare-your-local-machine"></a>Yerel makinenizi hazırlama

Bir Azure Machine Learning modelini yerel olarak çalıştırmanın en güvenilir yolu bir Docker görüntüsüdür. Docker görüntüsü, donanım sorunları, Azure yürütme ortamı dışında yinelenen bir yalıtılmış, kapsayıcılı bir deneyim sağlar. Geliştirme senaryoları için Docker yükleme ve yapılandırma hakkında daha fazla bilgi için bkz. [Windows 'Da Docker uzaktan geliştirmeye genel bakış](/windows/dev-environment/docker/overview).

Docker 'da çalışan bir işleme hata ayıklayıcı eklemek mümkündür. (Bkz. [çalışan kapsayıcıya iliştirme](https://code.visualstudio.com/docs/remote/attach-container).) Ancak Docker ile Python kodunuzda hata ayıklama ve yineleme yapmayı tercih edebilirsiniz. Bu senaryoda, yerel makinenizin denemenizin Azure Machine Learning ' de çalıştırıldığı sırada kullanılan kitaplıkları kullanması önemlidir. Azure, Python bağımlılıklarını yönetmek için [Conda](https://docs.conda.io/)kullanır. Diğer paket yöneticilerini kullanarak ortamı yeniden oluşturabilirsiniz, ancak yerel makinenize Conda 'yi yüklemek ve yapılandırmak, eşitlemeniz için en kolay yoldur. 

## <a name="prepare-your-entry-script"></a>Giriş betiğinizi hazırlama

Modeli ve bağımlılıkları yönetmek için Docker kullanıyor olsanız da, Python Puanlama betiği yerel olmalıdır. Betiğin iki yöntemi olmalıdır:

- `init()`Bağımsız değişken alan ve hiçbir şey döndüren bir yöntem 
- `run()`JSON biçimli bir dize alan ve JSON ile serileştirilebilir nesne döndüren bir yöntem

Yöntemin bağımsız değişkeni `run()` Bu biçimde olacaktır: 

```json
{
    "data": <model-specific-data-structure>
}
```

Yönteminden döndürülen nesne `run()` uygulamanız gerekir `toJSON() -> string` .

Aşağıdaki örnek, bir kayıtlı scikit-öğrenme modelinin nasıl yükleneceğini ve sayısal tuş düzeyi y verilerini kullanarak nasıl puan alabileceğinizi gösterir. Bu örnek, [Bu öğreticinin](tutorial-train-models-with-aml.md)modelini ve bağımlılıklarını temel alır.

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It's the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION).
    # For multiple models, it points to the folder containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # Make prediction.
    y_hat = model.predict(data)
    # You can return any data type as long as it's JSON-serializable.
    return y_hat.tolist()
```

Otomatik Swagger şema oluşturma ve Puanlama ikili verileri (örneğin, görüntüler) dahil daha gelişmiş örnekler için bkz. [Gelişmiş giriş betiği yazma](how-to-deploy-advanced-entry-script.md). 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Docker kullanarak yerel bir Web hizmeti olarak dağıtma

Azure Machine Learning tarafından kullanılan ortamı çoğaltmak için en kolay yol, Docker kullanarak bir Web hizmetini dağıtmaktır. Yerel makinenizde Docker çalışırken şunları yapmanız gerekir:

1. Modelinizin kaydedildiği Azure Machine Learning çalışma alanına bağlanın.
1. `Model`Modeli temsil eden bir nesne oluşturun.
1. `Environment`Bağımlılıkları içeren bir nesne oluşturun ve kodunuzun çalışacağı yazılım ortamını tanımlar.
1. `InferenceConfig`Giriş betiğini ile ilişkilendiren bir nesne oluşturun `Environment` .
1. `DeploymentConfiguration`Alt sınıfta bir nesne oluşturun `LocalWebserviceDeploymentConfiguration` .
1. `Model.deploy()`Bir nesne oluşturmak için kullanın `Webservice` . Bu yöntem Docker görüntüsünü indirir ve,, ve ile ilişkilendirir `Model` `InferenceConfig` `DeploymentConfiguration` .
1. Kullanarak öğesini etkinleştirin `Webservice` `Webservice.wait_for_deployment()` .

Aşağıdaki kod şu adımları göstermektedir:

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

Çağrısı `Model.deploy()` birkaç dakika sürebilir. Web hizmetini ilk kez dağıttıktan sonra, `update()` sıfırdan başlamak yerine yöntemi kullanmak daha etkilidir. Bkz. [Dağıtılmış bir Web hizmetini güncelleştirme](how-to-deploy-update-web-service.md).

### <a name="test-your-local-deployment"></a>Yerel dağıtımınızı test etme

Önceki dağıtım betiğini çalıştırdığınızda, Puanlama için veri NAKLEDEBILECEĞINIZ URI 'yi çıktı olarak (örneğin, `http://localhost:6789/score` ). Aşağıdaki örnek, `"sklearn-mnist-local"` yerel olarak dağıtılan modeli kullanarak örnek veriler için bir komut dosyası gösterir. Doğru eğitilen model, `normalized_pixel_values` "2" olarak yorumlanmalıdır. 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>Modelinizi doğrudan indirip çalıştırın

Modelinizi Web hizmeti olarak dağıtmak için Docker kullanılması en yaygın seçenektir. Ancak yerel Python betiklerini kullanarak kodunuzu doğrudan çalıştırmak isteyebilirsiniz. İki önemli bileşen gerekir: 

- Modelin kendisi
- Modelin bağımlı olduğu bağımlılıklar 

Modeli indirebilirsiniz:  

- Portalda, **modeller** sekmesini seçin, istenen modeli seçin ve **Ayrıntılar** sayfasında **İndir**' i seçin.
- Komut satırından kullanarak `az ml model download` . (Bkz [. model indirme.](/cli/azure/ml/model#az_ml_model_download))
- Python SDK `Model.download()` yöntemini kullanarak. (Bkz [. model sınıfı.](/python/api/azureml-core/azureml.core.model.model#download-target-dir------exist-ok-false--exists-ok-none-))

Bir Azure modeli, Python Pickle dosyası (. pkl uzantısı) olarak paketlenmiş bir veya daha fazla serileştirilmiş Python nesnedir. Pickle dosyasının içeriği, modeli eğitmek için kullanılan makine öğrenimi kitaplığına veya tekniğe bağlıdır. Örneğin, öğreticiden model kullanıyorsanız, modeli şu şekilde yükleyebilirsiniz:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

Bağımlılıklar, özellikle makine öğrenimi ile, özellikle de belirli sürüm gereksinimlerinin bir Web 'i olan bir Web 'in bir Web 'e sahip olmak üzere her zaman daha hassas bir şekilde olsun. Yerel makinenizde bir Azure Machine Learning ortamını tam Conda ortamı olarak veya bir Docker görüntüsü olarak, sınıfının yöntemini kullanarak yeniden oluşturabilirsiniz `build_local()` `Environment` : 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

`build_local()` `useDocker` Bağımsız değişkenini olarak ayarlarsanız `True` , işlev Conda ortamı yerine bir Docker görüntüsü oluşturur. Daha fazla denetime sahip olmak istiyorsanız, `save_to_directory()` `Environment` conda_dependencies. yıml ve uzantısı için temel olarak kullanabileceğiniz tanımlama dosyalarında azureml_environment.jsyazar yöntemini kullanabilirsiniz. 

`Environment`Sınıfı, işlem donanımınızla, Azure çalışma alanınızda ve Docker görüntülerinizden ortamları eşitlemek için çeşitli diğer yöntemlere sahiptir. Daha fazla bilgi için bkz. [ortam sınıfı](/python/api/azureml-core/azureml.core.environment(class)).

Modeli indirip bağımlılıklarını çözdükten sonra, Puanlama gerçekleştirme, model üzerinde ince ayar yapma, aktarma öğrenimi kullanma gibi Azure tanımlı bir kısıtlama yoktur. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Geri çekme modelini Azure Machine Learning karşıya yükleyin

Yerel olarak eğitilen veya geri çekme modeliniz varsa, bunu Azure 'a kaydedebilirsiniz. Kaydolduktan sonra Azure işlem kullanarak ayarlamaya devam edebilir veya Azure [Kubernetes hizmeti](how-to-deploy-azure-kubernetes-service.md) ya da [Triton çıkarım sunucusu (Önizleme)](how-to-deploy-with-triton.md)gibi Azure olanaklarını kullanarak dağıtabilirsiniz.

Azure Machine Learning Python SDK ile kullanılmak üzere, bir modelin Pickle biçiminde (bir. PKL dosyası) serileştirilmiş bir Python nesnesi olarak depolanması gerekir. Ayrıca `predict(data)` , JSON serileştirilebilir nesne döndüren bir yöntem de uygulamalıdır. Örneğin, yerel olarak eğitilen bir scikit-şu ile diabetes modeliyle bilgi edinebilirsiniz: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Modeli Azure 'da kullanılabilir hale getirmek için, `register()` sınıfının yöntemini kullanabilirsiniz `Model` :

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

Daha sonra, yeni kayıtlı modelinizi Azure Machine Learning **model** sekmesinde bulabilirsiniz:

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Karşıya yüklenen bir modeli gösteren Azure Machine Learning modeli sekmesinin ekran görüntüsü.":::

Model ve ortamları karşıya yükleme ve güncelleştirme hakkında daha fazla bilgi için bkz. [modeli kaydetme ve gelişmiş kullanımlar ile yerel olarak dağıtma](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb).

## <a name="next-steps"></a>Sonraki adımlar

- Ortamları yönetme hakkında daha fazla bilgi için [Azure Machine Learning yazılım ortamları oluşturma & kullanma](how-to-use-environments.md)konusuna bakın.
- Veri deposundan verilere erişme hakkında bilgi edinmek için bkz. [Azure 'da depolama hizmetlerine bağlanma](how-to-access-data.md).
