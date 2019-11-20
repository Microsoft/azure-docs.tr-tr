---
title: Modellerin nasıl ve nereye dağıtılacağı
titleSuffix: Azure Machine Learning
description: Azure Container Instances, Azure Kubernetes hizmeti, Azure IoT Edge ve alan programlanabilir kapı dizileri dahil olmak üzere Azure Machine Learning modellerinizi nasıl ve nereye dağıtacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 9d7dc724130286298af6e1d4b0babc551f3f2658
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123627"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Azure Machine Learning modelleri dağıtma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Machine Learning modelinizi bir Web hizmeti olarak Azure bulutu 'nda veya Azure IoT Edge cihazlarda dağıtmayı öğrenin.

İş akışı modelinize [nereden dağıttığınız](#target) her ne kadar benzerdir:

1. Modeli kaydedin.
1. Dağıtmaya hazırlanın. (Varlıkları, kullanımı, işlem hedefini belirtin.)
1. Modeli işlem hedefine dağıtın.
1. Web hizmeti olarak da bilinen dağıtılan modeli test edin.

Dağıtım iş akışında yer alan kavramlar hakkında daha fazla bilgi için bkz. [Azure Machine Learning modelleri yönetme, dağıtma ve izleme](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Önkoşullar

- Azure Machine Learning çalışma alanı. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

- Bir model. Eğitilen bir modeliniz yoksa, [Bu öğreticide](https://aka.ms/azml-deploy-cloud)verilen model ve bağımlılık dosyalarını kullanabilirsiniz.

- [Machine Learning hizmeti Için Azure CLI uzantısı](reference-azure-machine-learning-cli.md), [Python için Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)veya [Azure Machine Learning Visual Studio Code uzantısı](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Çalışma alanınıza bağlanın

Aşağıdaki kod, yerel geliştirme ortamında önbelleğe alınan bilgileri kullanarak bir Azure Machine Learning çalışma alanına bağlanmayı gösterir:

+ **SDK 'Yı kullanma**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Bir çalışma alanına bağlanmak için SDK 'Yı kullanma hakkında daha fazla bilgi için bkz. [Python için Azure MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) belgeleri.

+ **CLı 'yi kullanma**

   CLı kullanırken, komut için çalışma alanını belirtmek üzere `-w` veya `--workspace-name` parametresini kullanın.

+ **VS Code kullanma**

   VS Code kullandığınızda, bir grafik arabirim kullanarak çalışma alanını seçersiniz. Daha fazla bilgi için bkz. VS Code uzantısı belgelerindeki [modelleri dağıtma ve yönetme](how-to-vscode-tools.md#deploy-and-manage-models) .

## <a id="registermodel"></a>Modelinizi kaydetme

Kayıtlı bir model, modelinizi oluşturan bir veya daha fazla dosya için mantıksal bir kapsayıcıdır. Örneğin, birden çok dosyada depolanan bir modeliniz varsa, bunları çalışma alanına tek bir model olarak kaydedebilirsiniz. Dosyaları kaydettikten sonra, kayıtlı modeli indirebilir veya dağıtabilir ve kaydettiğiniz tüm dosyaları alabilirsiniz.

> [!TIP]
> Bir modeli kaydettiğinizde, bir bulut konumunun (bir eğitim çalıştırmasında) ya da yerel bir dizinin yolunu sağlarsınız. Bu yol, kayıt işleminin bir parçası olarak karşıya yüklenecek dosyaları bulmak için yeterlidir. Giriş betiğinde kullanılan yol ile eşleşmesi gerekmez. Daha fazla bilgi için bkz. [giriş betiğinizdeki model dosyalarını bulma](#locate-model-files-in-your-entry-script).

Machine Learning modelleri Azure Machine Learning çalışma alanınıza kaydedilir. Model Azure Machine Learning veya herhangi bir yerden gelebilir. Aşağıdaki örneklerde bir modelin nasıl kaydedileceği gösterilmektedir.

### <a name="register-a-model-from-an-experiment-run"></a>Deneme çalıştırmasında bir modeli kaydetme

Bu bölümdeki kod parçacıkları, bir eğitim çalıştırmasının nasıl bir model kaydedeceğinizi göstermektedir:

> [!IMPORTANT]
> Bu kod parçacıklarını kullanmak için, daha önce bir eğitim çalışması gerçekleştirdiyseniz ve `Run` nesnesine (SDK örneği) veya çalıştırma KIMLIĞI değerine (CLı örneği) erişiminizin olması gerekir. Eğitim modelleri hakkında daha fazla bilgi için bkz. [model eğitimi için işlem hedeflerini ayarlama](how-to-set-up-training-targets.md).

+ **SDK 'Yı kullanma**

  Bir modeli eğitebilmeniz için SDK 'yı kullandığınızda, modeli eğitidiğinize bağlı olarak bir [Run](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) nesnesi ya da bir [oto mlrun](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master) nesnesi alabilirsiniz. Her nesne, bir deneme çalıştırması tarafından oluşturulan bir modeli kaydetmek için kullanılabilir.

  + `azureml.core.Run` nesnesinden model kaydetme:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` parametresi, modelin bulut konumunu ifade eder. Bu örnekte, tek bir dosyanın yolu kullanılır. Model kaydına birden çok dosya eklemek için `model_path`, dosyaları içeren bir klasörün yoluna ayarlayın. Daha fazla bilgi için bkz [. Run. register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-) belgeleri.

  + `azureml.train.automl.run.AutoMLRun` nesnesinden model kaydetme:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    Bu örnekte `metric` ve `iteration` parametreleri belirtilmediği için en iyi birincil ölçüye sahip yineleme kaydedilir. Çalışma alanından döndürülen `model_id` değeri bir model adı yerine kullanılır.

    Daha fazla bilgi için bkz. [oto Mlrun. register_model](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master#register-model-description-none--tags-none--iteration-none--metric-none-) belgeleri.

+ **CLı 'yi kullanma**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  `--asset-path` parametresi, modelin bulut konumunu ifade eder. Bu örnekte, tek bir dosyanın yolu kullanılır. Model kaydına birden çok dosya eklemek için `--asset-path`, dosyaları içeren bir klasörün yoluna ayarlayın.

+ **VS Code kullanma**

  [Vs Code](how-to-vscode-tools.md#deploy-and-manage-models) uzantısını kullanarak model dosyalarını veya klasörleri kullanarak modelleri kaydedin.

### <a name="register-a-model-from-a-local-file"></a>Yerel dosyadan model kaydetme

Modelin yerel yolunu sağlayarak bir modeli kaydedebilirsiniz. Bir klasörün ya da tek bir dosyanın yolunu sağlayabilirsiniz. Bu yöntemi, Azure Machine Learning eğitilen ve daha sonra indirilen modelleri kaydetmek için kullanabilirsiniz. Bu yöntemi, Azure Machine Learning dışında eğitilen modelleri kaydetmek için de kullanabilirsiniz.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **SDK ve ONNX kullanma**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Model kaydına birden çok dosya eklemek için `model_path`, dosyaları içeren bir klasörün yoluna ayarlayın.

+ **CLı 'yi kullanma**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Model kaydına birden çok dosya eklemek için `-p`, dosyaları içeren bir klasörün yoluna ayarlayın.

**Tahmini süre**: yaklaşık 10 saniye.

Daha fazla bilgi için [model sınıfına](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)yönelik belgelere bakın.

Azure Machine Learning dışında eğitilen modellerle çalışma hakkında daha fazla bilgi için bkz. [var olan bir modeli dağıtma](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>İşlem hedefi seçin

Web hizmeti dağıtımınızı barındırmak için aşağıdaki işlem hedeflerini veya işlem kaynaklarını kullanabilirsiniz:

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Dağıtmaya hazırlanma

Modeli dağıtmak için aşağıdaki öğeler gereklidir:

* **Bir giriş betiği**. Bu betik istekleri kabul eder, modeli kullanarak istekleri puan eder ve sonuçları döndürür.

    > [!IMPORTANT]
    > * Giriş betiği modelinize özeldir. Gelen istek verilerinin biçimini, modelinizde beklenen verilerin biçimini ve istemcilere döndürülen verilerin biçimini anlamalıdır.
    >
    >   İstek verileri modelinize uygun olmayan bir biçimdeyse, komut dosyası bunu kabul edilebilir bir biçime dönüştürebilir. Ayrıca, yanıtı istemciye döndürmeden önce dönüştürebilir.
    >
    > * Azure Machine Learning SDK, Web Hizmetleri veya IoT Edge dağıtımlarının veri deponuza veya veri kümelerine erişmesi için bir yol sağlamaz. Dağıtılmış modelinizin, bir Azure depolama hesabındaki veriler gibi dağıtım dışında depolanan verilere erişmesi gerekiyorsa, ilgili SDK 'yı kullanarak özel bir kod çözümü geliştirmeniz gerekir. Örneğin, [Python Için Azure depolama SDK 'sı](https://github.com/Azure/azure-storage-python).
    >
    >   Senaryonuz için işe yönelik bir alternatif, Puanlama sırasında veri depolarına erişim sağlayan [toplu tahmindir](how-to-run-batch-predictions.md).

* Giriş betiğini veya modelini çalıştırmak için gereken yardımcı betikler veya Python/Conda paketleri gibi **Bağımlılıklar**.

* Dağıtılan modeli barındıran işlem hedefi için **dağıtım yapılandırması** . Bu yapılandırma, modeli çalıştırmak için gereken bellek ve CPU gereksinimleri gibi şeyleri açıklar.

Bu öğeler bir *çıkarım yapılandırmasına* ve bir *dağıtım yapılandırmasına*kapsüllenir. Çıkarım yapılandırması, giriş betiğine ve diğer bağımlılıklara başvurur. Dağıtımı gerçekleştirmek için SDK 'Yı kullandığınızda bu konfigürasyonları programlama yoluyla tanımlarsınız. CLı kullandığınızda bunları JSON dosyalarında tanımlarsınız.

### <a id="script"></a>1. giriş betiğinizi ve bağımlılıklarınızı tanımlayın

Giriş betiği dağıtılan bir Web hizmetine gönderilen verileri alır ve modele geçirir. Ardından model tarafından döndürülen yanıtı alır ve istemciye döndürür. *Betik, modelinize özeldir*. Modelin beklediği ve döndürdüğü verileri anlaması gerekir.

Betik, modeli yükleyen ve çalıştıran iki işlev içerir:

* `init()`: Bu işlev genellikle modeli genel bir nesneye yükler. Bu işlev, Web hizmetiniz için Docker kapsayıcısı başlatıldığında yalnızca bir kez çalıştırılır.

* `run(input_data)`: Bu işlev, giriş verileri temel alan bir değer tahmin modelini kullanır. Çalıştırmanın giriş ve çıkışları genellikle serileştirme ve seri durumundan çıkarma için JSON kullanır. Ham ikili verilerle de çalışabilirsiniz. Verileri modele göndermeden önce veya istemciye döndürmeden önce dönüştürebilirsiniz.

#### <a name="locate-model-files-in-your-entry-script"></a>Giriş betiğinizdeki model dosyalarını bulun

Giriş betiğinizdeki modelleri bulmanın iki yolu vardır:
* `AZUREML_MODEL_DIR`: model konumunun yolunu içeren bir ortam değişkeni.
* `Model.get_model_path`: kayıtlı model adı kullanılarak model dosyasının yolunu döndüren bir API.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR, hizmet dağıtımı sırasında oluşturulan bir ortam değişkenidir. Dağıtılan model konumunu bulmak için bu ortam değişkenini kullanabilirsiniz.

Aşağıdaki tabloda, dağıtılan modellerin sayısına bağlı olarak AZUREML_MODEL_DIR değeri açıklanmaktadır:

| Dağıtım | Ortam değişkeni değeri |
| ----- | ----- |
| Tek model | Modeli içeren klasörün yolu. |
| Birden çok model | Tüm modelleri içeren klasörün yolu. Modeller bu klasördeki ad ve sürüm ile bulunur (`$MODEL_NAME/$VERSION`) |

Bir modeldeki bir dosyanın yolunu almak için, ortam değişkenini aradığınız dosya adıyla birleştirin.
Model dosyalarının dosya adları kayıt ve dağıtım sırasında korunur. 

**Tek model örneği**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
```

**Birden çok model örneği**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model/1/sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Bir modeli kaydettiğinizde, kayıt defterinde modeli yönetmek için kullanılan bir model adı sağlarsınız. Bu adı model dosyasının veya yerel dosya sistemindeki dosyaların yolunu almak için [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) yöntemiyle birlikte kullanırsınız. Bir klasörü veya dosya koleksiyonunu kaydettiğinizde, bu API bu dosyaları içeren dizinin yolunu döndürür.

Bir modeli kaydettiğinizde, buna bir ad verirsiniz. Ad, modelin yerel olarak ya da hizmet dağıtımı sırasında yerleştirildiği yere karşılık gelir.

> [!IMPORTANT]
> Bir modeli eğitebilmeniz için otomatik makine öğrenimi kullandıysanız, model adı olarak `model_id` bir değer kullanılır. Otomatik makine öğrenimi ile eğitilen bir modeli kaydetme ve dağıtmayla ilgili bir örnek için bkz. GitHub 'da [Azure/Machinelearningnot defterleri](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features) .

Aşağıdaki örnek, `sklearn_mnist_model.pkl` adlı tek bir dosyanın yolunu döndürür (`sklearn_mnist`adı ile kaydedilir):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Seçim Otomatik şema oluşturma

Web hizmetiniz için otomatik olarak bir şema oluşturmak üzere, tanımlı tür nesnelerinden biri için kurucudaki girişin ve/veya çıktının bir örneğini sağlayın. Tür ve örnek, otomatik olarak şemayı oluşturmak için kullanılır. Azure Machine Learning, dağıtım sırasında Web hizmeti için bir [Openapı](https://swagger.io/docs/specification/about/) (Swagger) belirtimi oluşturur.

Bu türler Şu anda destekleniyor:

* `pandas`
* `numpy`
* `pyspark`
* Standart Python nesnesi

Şema oluşturmayı kullanmak için, `inference-schema` paketini Conda ortam dosyanıza ekleyin. Bu paket hakkında daha fazla bilgi için bkz. [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema).

##### <a name="example-dependencies-file"></a>Örnek bağımlılıklar dosyası

Aşağıdaki YAML, çıkarım için Conda Dependencies bir dosya örneğidir:

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
    - azureml-defaults
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Eğer koda ve PIP (PyPi) ile bağımlılığı varsa Microsoft, Conda 'in, daha güvenilir bir şekilde yükleme yapan önceden oluşturulmuş ikili dosyalarla birlikte geldiğinden, Conda sürümünün kullanılmasını önerir.
>
> Daha fazla bilgi için bkz. [Conda ve PIP 'Yi anlama](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Bağımlılarınızın Conda ile kullanılabilir olup olmadığını denetlemek için `conda search <package-name>` komutunu kullanın veya [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) ve [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo)paket dizinlerini kullanın.

Otomatik şema oluşturmayı kullanmak istiyorsanız, giriş betiğinizin `inference-schema` paketlerini içeri aktarması gerekir.

Web hizmetinin istek ve yanıt biçimlerini temsil eden `input_sample` ve `output_sample` değişkenlerinde giriş ve çıkış örnek biçimlerini tanımlayın. Bu örnekleri, `run()` işlevindeki giriş ve çıkış işlevi dekoratlarını kullanın. Aşağıdaki scikit-örnek, şema oluşturmayı kullanır.

##### <a name="example-entry-script"></a>Örnek giriş betiği

Aşağıdaki örnek, JSON verilerinin nasıl kabul edileceği ve geri dönebileceğinizi göstermektedir:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Aşağıdaki örnek, veri çerçevesini kullanarak giriş verilerinin bir `<key: value>` sözlüğü olarak nasıl tanımlanacağını göstermektedir. Bu yöntem, dağıtılan Web hizmetinin Power BI tüketmesi için desteklenir. ([Power BI Web hizmetini kullanma hakkında daha fazla bilgi edinin](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Daha fazla örnek için aşağıdaki betiklerine bakın:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>İkili veriler

Modeliniz bir görüntü gibi ikili verileri kabul ediyorsa, dağıtımınız için kullanılan `score.py` dosyasını ham HTTP isteklerini kabul edecek şekilde değiştirmeniz gerekir. Ham verileri kabul etmek için, giriş betiğinizdeki `AMLRequest` sınıfını kullanın ve `@rawhttp` dekoratörü `run()` işlevine ekleyin.

İkili verileri kabul eden bir `score.py` örneği aşağıda verilmiştir:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest` sınıfı `azureml.contrib` ad alanıdır. Bu ad alanındaki varlıklar, hizmeti geliştirmek için çalışmamız sırasında sıklıkla değişir. Bu ad alanındaki her türlü şey, Microsoft tarafından tam olarak desteklenmeyen bir önizleme olarak düşünülmelidir.
>
> Bunu yerel geliştirme ortamınızda test etmeniz gerekirse, aşağıdaki komutu kullanarak bileşenleri yükleyebilirsiniz:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Çıkış noktaları arası kaynak paylaşımı (CORS)

Çıkış noktaları arası kaynak paylaşımı, bir Web sayfasındaki kaynakların başka bir etki alanından istenmesinin izin vermesinin bir yoludur. CORS, istemci isteğiyle gönderilen ve hizmet yanıtıyla döndürülen HTTP üstbilgileri aracılığıyla çalışmaktadır. CORS ve geçerli üstbilgiler hakkında daha fazla bilgi için bkz. Vikipde [çıkış noktaları arası kaynak paylaşma](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) .

Model dağıtımınızı CORS 'yi destekleyecek şekilde yapılandırmak için giriş betiğinizdeki `AMLResponse` sınıfını kullanın. Bu sınıf, yanıt nesnesindeki üst bilgileri ayarlamanıza olanak sağlar.

Aşağıdaki örnek, giriş betiğinin yanıt için `Access-Control-Allow-Origin` üst bilgisini ayarlar:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` sınıfı `azureml.contrib` ad alanıdır. Bu ad alanındaki varlıklar, hizmeti geliştirmek için çalışmamız sırasında sıklıkla değişir. Bu ad alanındaki her türlü şey, Microsoft tarafından tam olarak desteklenmeyen bir önizleme olarak düşünülmelidir.
>
> Bunu yerel geliştirme ortamınızda test etmeniz gerekirse, aşağıdaki komutu kullanarak bileşenleri yükleyebilirsiniz:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. ınısenceconfig 'nizi tanımlayın

Çıkarım yapılandırması, tahmine dayalı hale getirmek üzere modelin nasıl yapılandırılacağını açıklar. Bu yapılandırma giriş betiğinizin bir parçası değil. Giriş betiğine başvurur ve dağıtım için gerekli tüm kaynakları bulmak için kullanılır. Modeli dağıtırken daha sonra kullanılır.

Çıkarım yapılandırması, dağıtımınız için gereken yazılım bağımlılıklarını tanımlamak için Azure Machine Learning ortamlar kullanabilir. Ortamlar, eğitim ve dağıtım için gereken yazılım bağımlılıklarını oluşturmanıza, yönetmenize ve yeniden kullanmanıza olanak tanır. Aşağıdaki örnek, çalışma alanınızdan bir ortamı yüklemeyi ve ardından çıkarım yapılandırmasıyla kullanmayı gösterir:

```python
from azureml.core import Environment
from azureml.core.model import InferenceConfig

deploy_env = Environment.get(workspace=ws,name="myenv",version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=deploy_env)
```

Ortamlar hakkında daha fazla bilgi için bkz. [eğitim ve dağıtım için ortamları oluşturma ve yönetme](how-to-use-environments.md).

Ayrıca, bir ortamı kullanmadan bağımlılıkları doğrudan belirtebilirsiniz. Aşağıdaki örnek, bir Conda dosyasından yazılım bağımlılıklarını yükleyen bir çıkarım yapılandırmasının nasıl oluşturulacağını gösterir:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Daha fazla bilgi için bkz. [ınenceconfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) sınıfı belgeleri.

Bir çıkarım yapılandırmasıyla özel bir Docker görüntüsü kullanma hakkında daha fazla bilgi için bkz. [özel bir Docker görüntüsü kullanarak model dağıtma](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Inenceconfig CLı örneği

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

Aşağıdaki komut, CLı kullanarak bir modelin nasıl dağıtılacağını göstermektedir:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Bu örnekte, yapılandırma aşağıdaki ayarları belirtir:

* Model Python gerektirir.
* Dağıtılan hizmete gönderilen Web isteklerini işlemek için kullanılan [giriş betiği](#script).
* Çıkarımı için gereken Python paketlerini açıklayan Conda dosyası.

Bir çıkarım yapılandırmasıyla özel bir Docker görüntüsü kullanma hakkında daha fazla bilgi için bkz. [özel bir Docker görüntüsü kullanarak model dağıtma](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. dağıtım yapılandırmanızı tanımlayın

Modelinizi dağıtılmadan önce dağıtım yapılandırmasını tanımlamanız gerekir. *Dağıtım yapılandırması, Web hizmetini barındıracak işlem hedefine özgüdür.* Örneğin, bir modeli yerel olarak dağıttığınızda, hizmetin istekleri kabul ettiği bağlantı noktasını belirtmeniz gerekir. Dağıtım yapılandırması, giriş betiğinizin bir parçası değil. Model ve giriş betiğini barındıracak işlem hedefinin özelliklerini tanımlamak için kullanılır.

Ayrıca, çalışma alanınız ile ilişkili bir Azure Kubernetes Service (AKS) örneğiniz yoksa, işlem kaynağını oluşturmanız da gerekebilir.

Aşağıdaki tabloda her işlem hedefi için bir dağıtım yapılandırması oluşturma örneği verilmiştir:

| İşlem hedefi | Dağıtım yapılandırma örneği |
| ----- | ----- |
| Yerel | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Yerel, Azure Container Instances ve AKS Web Hizmetleri için sınıflar `azureml.core.webservice`içeri aktarılabilir:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>Profil oluşturma

Modelinizi bir hizmet olarak dağıtmadan önce en iyi CPU ve bellek gereksinimlerini öğrenmek için profili oluşturmanız gerekebilir. Modelinizin profilini eklemek için SDK veya CLı kullanabilirsiniz. Aşağıdaki örnekler SDK kullanarak bir modelin profilini oluşturmayı gösterir.

> [!IMPORTANT]
> Profil oluşturma kullandığınızda, sağladığınız çıkarım yapılandırması bir Azure Machine Learning ortamına başvuramaz. Bunun yerine, `InferenceConfig` nesnesinin `conda_file` parametresini kullanarak yazılım bağımlılıklarını tanımlayın.

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(True)
profiling_results = profile.get_results()
print(profiling_results)
```

Bu kod, aşağıdaki çıktıya benzer bir sonuç görüntüler:

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

Model profil oluşturma sonuçları `Run` nesne olarak dağıtılır.

CLı 'dan profil oluşturmayı kullanma hakkında daha fazla bilgi için, bkz. [az ml model profili](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

Daha fazla bilgi için aşağıdaki belgelere bakın:

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [profil ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [Çıkarım yapılandırma dosyası şeması](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>Hedefe dağıt

Dağıtım, modelleri dağıtmak için çıkarım yapılandırma dağıtımı yapılandırmasını kullanır. Dağıtım işlemi, işlem hedefi ne olursa olsun benzerdir. Aks kümesine bir başvuru sağlamanız gerektiğinden AKS 'e dağıtım biraz farklıdır.

### <a id="local"></a>Yerel dağıtım

Bir modeli yerel olarak dağıtmak için, yerel makinenizde Docker yüklü olmalıdır.

#### <a name="using-the-sdk"></a>SDK’yı kullanarak
ZZS
```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Daha fazla bilgi için bkz. [Localwebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)ve [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)belgeleri.

#### <a name="using-the-cli"></a>CLı 'yi kullanma

CLı kullanarak bir modeli dağıtmak için aşağıdaki komutu kullanın. `mymodel:1`, kayıtlı modelin adı ve sürümüyle değiştirin:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Daha fazla bilgi için bkz. [az ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) belgeleri.

### <a id="notebookvm"></a>Not defteri VM Web hizmeti (geliştirme/test)

Bkz. [Azure Machine Learning Not DEFTERI VM 'sine model dağıtma](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Azure Container Instances (geliştirme/test)

Bkz. [Azure Container Instances dağıtma](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Azure Kubernetes hizmeti (geliştirme/test ve üretim)

Bkz. [Azure Kubernetes hizmetine dağıtma](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Web hizmetlerini kullanma

Dağıtılan her Web hizmeti, çeşitli programlama dillerinde istemci uygulamaları oluşturabilmeniz için bir REST API sağlar.
Hizmetiniz için anahtar kimlik doğrulamasını etkinleştirdiyseniz, istek üst bilgisinde belirteç olarak bir hizmet anahtarı sağlamanız gerekir.
Hizmetiniz için belirteç kimlik doğrulamasını etkinleştirdiyseniz, istek üst bilgisinde taşıyıcı belirteci olarak bir Azure Machine Learning JWT belirteci sağlamanız gerekir.

> [!TIP]
> Hizmeti dağıttıktan sonra şema JSON belgesini alabilirsiniz. Yerel Web hizmetinin Swagger dosyasına URI 'yi almak için dağıtılan Web hizmetindeki [swagger_uri özelliğini](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) kullanın (örneğin, `service.swagger_uri`).

### <a name="request-response-consumption"></a>İstek-yanıt tüketimi

Aşağıda, Python 'da hizmetinizin nasıl çağrılcağına dair bir örnek verilmiştir:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Daha fazla bilgi için bkz. [Web hizmetlerini kullanmak için istemci uygulamaları oluşturma](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Web hizmeti şeması (Openapı belirtimi)

Dağıtımınızla otomatik şema oluşturma kullandıysanız, [swagger_uri özelliğini](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)kullanarak hizmetin openapı belirtiminin adresini alabilirsiniz. (Örneğin, `print(service.swagger_uri)`.) Belirtimi almak için bir GET isteği kullanın veya bir tarayıcıda URI 'yi açın.

Aşağıdaki JSON belgesi, bir dağıtım için oluşturulan bir şemanın (Openapı belirtimi) bir örneğidir:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Daha fazla bilgi için bkz. [Openapı belirtimi](https://swagger.io/specification/).

Belirtiden istemci kitaplıkları oluşturabileceğiniz bir yardımcı program için bkz. [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a>Toplu çıkarım
Azure Machine Learning Işlem hedefleri Azure Machine Learning tarafından oluşturulur ve yönetilir. Bunlar, Azure Machine Learning işlem hatlarından toplu tahmin için kullanılabilirler.

Azure Machine Learning Işlem ile Batch çıkarımı hakkında yönergeler için bkz. [Batch öngörülerini çalıştırma](tutorial-pipeline-batch-scoring-classification.md).

### <a id="iotedge"></a>IoT Edge çıkarımı
Kenara dağıtım desteği önizleme aşamasındadır. Daha fazla bilgi için bkz. [Azure Machine Learning IoT Edge modül olarak dağıtma](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a>Web hizmetlerini güncelleştirme

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Modelleri sürekli dağıt

[Azure DevOps](https://azure.microsoft.com/services/devops/)için Machine Learning uzantısını kullanarak, modelleri sürekli olarak dağıtabilirsiniz. Yeni bir makine öğrenimi modeli bir Azure Machine Learning çalışma alanına kaydedildiğinde bir dağıtım işlem hattı tetiklemek için Azure DevOps Machine Learning uzantısını kullanabilirsiniz.

1. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)için kaydolun, bu, uygulamanızı sürekli tümleştirme ve mümkün olan herhangi bir platforma veya buluta teslim eden bir uygulamadır. (Azure Pipelines [Machine Learning işlem hatları](concept-ml-pipelines.md#compare)ile aynı olmadığını unutmayın.)

1. [Azure DevOps projesi oluşturun.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. [Azure Pipelines için Machine Learning uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)yükler.

1. Yapılarınıza erişebilmek için Azure Machine Learning çalışma alanınıza bir hizmet sorumlusu bağlantısı kurmak üzere hizmet bağlantıları 'nı kullanın. Proje Ayarları ' na gidin, **hizmet bağlantıları**' nı seçin ve ardından **Azure Resource Manager**' yi seçin:

    [![Azure Resource Manager seçin](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. **Kapsam düzeyi** listesinde **AzureMLWorkspace**' ı seçin ve ardından kalan değerleri girin:

    ![AzureMLWorkspace seçin](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Makine öğrenimi modelinizi Azure Pipelines kullanarak sürekli olarak dağıtmak için, işlem hatları altında **yayın**' ı seçin. Yeni bir yapıt ekleyin ve ardından daha önce oluşturduğunuz **AzureML model** yapıtını ve hizmet bağlantısını seçin. Bir dağıtımı tetiklemek için modeli ve sürümü seçin:

    [![AzureML modeli seçin](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Model yapıtında model tetikleyiciyi etkinleştirin. Tetikleyiciyi açtığınızda, söz konusu modelin belirtilen sürümü (yani, en yeni sürümü) çalışma alanınıza kaydedildiğinde, bir Azure DevOps sürümü işlem hattı tetiklenir.

    [Model tetikleyiciyi etkinleştirmek ![](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Daha fazla örnek proje ve örnek için GitHub 'daki Şu örnek depolara bakın:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Model indirme
Modelinizi kendi yürütme ortamınızda kullanmak üzere indirmek isterseniz, bunu aşağıdaki SDK/CLı komutlarıyla yapabilirsiniz:

'SıNıN
```python
model_path = Model(ws,'mymodel').download()
```

CLı
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>Önizle Kod olmayan model dağıtımı

Şu anda önizleme aşamasında olan kod modeli dağıtımı yok ve şu makine öğrenimi çerçevelerini destekliyor:

### <a name="tensorflow-savedmodel-format"></a>TensorFlow SavedModel biçimi

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>ONNX modelleri

ONNX model kaydı ve dağıtımı herhangi bir ONNX çıkarım grafiği için desteklenir. Ön işlem ve postprocess adımları şu anda desteklenmiyor.

Bir MNIST ONNX modelinin nasıl kaydedileceği ve dağıtılacağı hakkında bir örnek aşağıda verilmiştir:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="scikit-learn-models"></a>Scikit-modelleri öğrenme

Tüm yerleşik scikit-öğrenme model türleri için hiçbir kod modeli dağıtımı desteklenmez.

Ek kod olmadan bir sköğren modelinin nasıl kaydedileceği ve dağıtıldığı hakkında bir örnek aşağıda verilmiştir:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

NOTE: Bu bağımlılıklar önceden oluşturulmuş sköğren çıkarımı kapsayıcısına dahil edilmiştir:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Paket modelleri

Bazı durumlarda, modeli dağıtmadan bir Docker görüntüsü oluşturmak isteyebilirsiniz (örneğin, [Azure App Service dağıtmayı](how-to-deploy-app-service.md)planlıyorsanız). Ya da görüntüyü indirmek ve yerel bir Docker yüklemesinde çalıştırmak isteyebilirsiniz. Görüntüyü oluşturmak, incelemek, değiştirmek ve görüntüyü el ile oluşturmak için kullanılan dosyaları da indirmek isteyebilirsiniz.

Model paketleme, bu işlemleri yapmanızı sağlar. Bir modeli Web hizmeti olarak barındırmak için gereken tüm varlıkları paketler ve tamamen oluşturulmuş bir Docker görüntüsünü ya da bir tane oluşturmak için gereken dosyaları indirmelerini sağlar. Model paketlemeyi kullanmanın iki yolu vardır:

**Paketlenmiş bir model indirin:** Modeli ve bir Web hizmeti olarak barındırmak için gereken diğer dosyaları içeren bir Docker görüntüsü indirin.

**Dockerfile oluştur:** Docker görüntüsü oluşturmak için gereken Dockerfile, model, giriş betiği ve diğer varlıkları indirin. Görüntüyü yerel olarak oluşturmadan önce dosyaları inceleyebilir veya değişiklik yapabilirsiniz.

Her iki paket de yerel bir Docker görüntüsü almak için kullanılabilir.

> [!TIP]
> Bir paket oluşturmak, model dağıtmaya benzer. Kayıtlı bir model ve çıkarım yapılandırmasını kullanırsınız.

> [!IMPORTANT]
> Tamamen oluşturulmuş bir görüntüyü indirmek veya yerel olarak bir görüntü oluşturmak için, geliştirme ortamınızda [Docker](https://www.docker.com) 'ın yüklü olması gerekir.

### <a name="download-a-packaged-model"></a>Paketlenmiş model indirme

Aşağıdaki örnek, çalışma alanınız için Azure Container Registry 'de kayıtlı olan bir görüntü oluşturur:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Bir paket oluşturduktan sonra, görüntüyü yerel Docker ortamınıza çekmek için `package.pull()` kullanabilirsiniz. Bu komutun çıktısı görüntünün adını görüntüler. Örneğin: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Modeli indirdikten sonra, yerel görüntüleri listelemek için `docker images` komutunu kullanın:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Bu görüntüye göre yerel bir kapsayıcı başlatmak için, Shell veya komut satırından adlandırılmış bir kapsayıcı başlatmak üzere aşağıdaki komutu kullanın. `<imageid>` değerini `docker images` komutu tarafından döndürülen görüntü KIMLIĞIYLE değiştirin.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Bu komut, `myimage`adlı görüntünün en son sürümünü başlatır. Yerel bağlantı noktası 6789 ' i Web hizmetinin dinlediği kapsayıcıda bağlantı noktasına eşler (5001). Ayrıca, kapsayıcıya daha kolay durmayı kolaylaştıran `mycontainer` adı kapsayıcıya atar. Kapsayıcı başlatıldıktan sonra, `http://localhost:6789/score`istek gönderebilirsiniz.

### <a name="generate-a-dockerfile-and-dependencies"></a>Dockerfile ve bağımlılıklar oluşturma

Aşağıdaki örnek, bir görüntüyü yerel olarak oluşturmak için gereken Dockerfile, model ve diğer varlıkların nasıl indirileceği gösterilmektedir. `generate_dockerfile=True` parametresi, tam olarak oluşturulmuş bir görüntü değil, dosyaları istediğinizi belirtir.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Bu kod, `imagefiles` dizinine görüntü oluşturmak için gereken dosyaları indirir. Kayıtlı dosyalara eklenen Dockerfile, bir Azure Container Registry 'de depolanan bir temel görüntüye başvurur. Yerel Docker yüklemenizde görüntüyü oluştururken, kayıt defterinde kimlik doğrulamak için adresi, Kullanıcı adını ve parolayı kullanmanız gerekir. Yerel bir Docker yüklemesi kullanarak görüntüyü derlemek için aşağıdaki adımları kullanın:

1. Bir kabuk veya komut satırı oturumundan, Azure Container Registry ile Docker kimlik doğrulaması yapmak için aşağıdaki komutu kullanın. `<address>`, `<username>`ve `<password>` `package.get_container_registry()`tarafından alınan değerlerle değiştirin.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Görüntüyü oluşturmak için aşağıdaki komutu kullanın. `<imagefiles>`, dosyaları `package.save()` kaydedildiği dizinin yoluyla değiştirin.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Bu komut, görüntü adını `myimage`olarak ayarlar.

Görüntünün derlendiğini doğrulamak için `docker images` komutunu kullanın. `myimage` görüntüsünü listede görmeniz gerekir:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Bu görüntüye göre yeni bir kapsayıcı başlatmak için aşağıdaki komutu kullanın:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Bu komut, `myimage`adlı görüntünün en son sürümünü başlatır. Yerel bağlantı noktası 6789 ' i Web hizmetinin dinlediği kapsayıcıda bağlantı noktasına eşler (5001). Ayrıca, kapsayıcıya daha kolay durmayı kolaylaştıran `mycontainer` adı kapsayıcıya atar. Kapsayıcı başlatıldıktan sonra, `http://localhost:6789/score`istek gönderebilirsiniz.

### <a name="example-client-to-test-the-local-container"></a>Yerel kapsayıcıyı test eden örnek istemci

Aşağıdaki kod, kapsayıcında kullanılabilecek bir Python istemcisi örneğidir:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Örneğin, diğer programlama dillerinde istemciler için bkz. [Web Hizmetleri olarak dağıtılan modelleri](how-to-consume-web-service.md)kullanma.

### <a name="stop-the-docker-container"></a>Docker kapsayıcısını durdur

Kapsayıcıyı durdurmak için, farklı bir kabuktan veya komut satırından aşağıdaki komutu kullanın:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Dağıtılmış bir web hizmetini silmek için kullanın `service.delete()`.
Kayıtlı bir model silmek için kullanın `model.delete()`.

Daha fazla bilgi için bkz. [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) ve [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)belgeleri.

## <a name="next-steps"></a>Sonraki adımlar

* [Özel bir Docker görüntüsü kullanarak model dağıtma](how-to-deploy-custom-docker-image.md)
* [Dağıtım sorunlarını giderme](how-to-troubleshoot-deployment.md)
* [Azure Machine Learning web hizmetleri SSL ile güvenli hale getirme](how-to-secure-web-service.md)
* [Web hizmeti olarak dağıtılan bir Azure Machine Learning modeli kullanma](how-to-consume-web-service.md)
* [Application Insights Azure Machine Learning modellerinizi izleyin](how-to-enable-app-insights.md)
* [Üretimde modeller için veri toplama](how-to-enable-data-collection.md)

