---
title: Şifrelenmiş bir ınırm hizmeti dağıtma (Önizleme)
titleSuffix: Azure Machine Learning
description: Microsoft MÜHÜRLEMEK için, görüntü sınıflandırması için şifrelenmiş bir tahmin hizmeti dağıtmak üzere nasıl kullanacağınızı öğrenin
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, responsible-ml
ms.openlocfilehash: 625ca217374fdb7558bf50faafb62c773cbc68b9
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059488"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service-preview"></a>Şifrelenmiş bir ınlebilme Web hizmeti dağıtma (Önizleme)

Bir görüntü sınıflandırma modelini [Azure Container Instances](../container-instances/index.yml) (aci) ' de şifrelenmiş bir Inactive bir Web hizmeti olarak dağıtmayı öğrenin. Web hizmeti, model ve Puanlama mantığını içeren bir Docker kapsayıcı görüntüsüdür.

Bu kılavuzda Azure Machine Learning hizmetini kullanarak şunları yapabilirsiniz:

> [!div class="checklist"]
> * Ortamlarınızı yapılandırın
> * Şifrelenmiş ınlelenmiş Web hizmeti dağıtma
> * Test verilerini hazırlama
> * Şifrelenmiş tahminler oluşturun
> * Kaynakları temizleme

ACI, model dağıtımı iş akışını test etmek ve anlamak için harika bir çözümdür. Ölçeklenebilir üretim dağıtımları için Azure Kubernetes Service’i kullanmayı göz önünde bulundurabilirsiniz. Daha fazla bilgi için bkz. [dağıtma ve nerede](./how-to-deploy-and-where.md).

Bu örnekte kullanılan şifreleme yöntemi [homomorphic şifredir](https://github.com/Microsoft/SEAL#homomorphic-encryption). Homomorphic şifrelemesi, bir gizli dizi (şifre çözme) anahtarına erişmek gerekmeden, şifrelenmiş verilerde hesaplamalar yapılmasına izin verir. Hesaplamaların sonuçları şifrelenir ve yalnızca gizli anahtar sahibi tarafından açığa çıkarlenebilir. 

## <a name="prerequisites"></a>Önkoşullar

Bu kılavuzda, Azure Machine Learning kayıtlı bir görüntü sınıflandırma modeliniz olduğunu varsaymaktadır. Aksi takdirde, modeli [önceden eğitilen bir model](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl) kullanarak kaydedin veya [Azure Machine Learning öğreticisi ile görüntü sınıflandırma modeliyle eğitme](tutorial-train-models-with-aml.md)işlemini tamamlayarak kendinizinkini oluşturun.

## <a name="configure-local-environment"></a>Yerel ortamı yapılandırma

Bir Jupyter not defterinde

1. Bu örnek için gereken Python paketlerini içeri aktarın.

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. Güvenli ınary için homomorphic şifreleme kitaplığı 'nı yükler.

    > [!NOTE]
    > `encrypted-inference`Paket şu anda önizleme aşamasındadır.

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference) , [Microsoft mühürlemek](https://github.com/Microsoft/SEAL)tabanlı şifreli ınary bağlamaları içeren bir kitaplıktır.

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>Inıri sınırlama ortamını yapılandırma

Indrda kısıtlaması için bir ortam oluşturun ve `encrypted-inference` paketi Conda bağımlılığı olarak ekleyin.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>Şifrelenmiş ınlelenmiş Web hizmeti dağıtma

Modeli ACI 'de barındırılan bir Web hizmeti olarak dağıtın.

Doğru ACI ortamını oluşturmak için şunları sağlayın:

* Modelin nasıl kullanılacağını gösteren puanlama betiği
* ACI’yı oluşturmak için bir yapılandırma dosyası
* Eğitilen bir model

### <a name="create-scoring-script"></a>Puanlama betiği oluşturma

`score.py`Web hizmeti tarafından ınnın için kullanılan Puanlama betiği oluşturun.

Puanlama betiğine gerekli iki işlevi eklemelisiniz:

* Normalde modeli genel bir nesneye yükleyen `init()` işlevi. Bu işlev, Docker kapsayıcısı başlatıldığında tek bir kez çalıştırılır.
* `run(input_data)` işlevi, giriş verileri temelinde bir değer tahmin etmek için modeli kullanır. Çalıştırmanın girişleri ve çıkışlarında serileştirme ve seriden çıkarma için normal olarak JSON kullanılır ama başka biçimler de desteklenir. İşlevi, hizmet çağıranı tarafından karşıya yüklenen homomorphic şifreleme tabanlı ortak anahtarları getirir.

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>Yapılandırma dosyası oluşturma

Dağıtım yapılandırma dosyası oluşturun ve ACI kapsayıcısına gereken CPU sayısını ve gigabayt cinsinden RAM miktarını belirtin. Modelinize bağlı olsa da, birçok model için varsayılan 1 çekirdek ve 1 gigabayt RAM çoğunlukla yeterlidir. Daha sonra fazlasına ihtiyacınız olduğunu düşünüyorsanız, görüntüyü yeniden oluşturabilir ve hizmeti yeniden dağıtabilirsiniz.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>Azure Container Instances’a dağıtma

Tahmini tamamlanma süresi: **yaklaşık 2-5 dakika**

Görüntüyü yapılandırın ve dağıtın. Aşağıdaki kod şu adımlardan geçer:

1. Ortam dosyasını () kullanarak modelin gerektirdiği bağımlılıkları içeren ortam nesnesi oluşturma `myenv.yml`
1. Modeli bir Web hizmeti olarak dağıtmak için gereken çıkarım yapılandırması oluşturma şunu kullanarak:
   * Puanlama dosyası (`score.py`)
   * Önceki adımda oluşturulan ortam nesnesi
1. Modeli ACI kapsayıcısına dağıtın.
1. Web hizmeti HTTP uç noktasını alın.

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

REST istemci çağrılarını kabul eden puanlama web hizmetinin HTTP uç noktasını alın. Bu uç nokta, web hizmetini test etmek veya bunu bir uygulamayla tümleştirmek isteyen herkesle paylaşılabilir.

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>Test verilerini hazırlama

1. Test verilerini indirin. Bu durumda, *veri* adlı bir dizine kaydedilir.

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. *Veri* dizininden test verilerini yükleyin.

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>Şifrelenmiş tahminler oluşturun

Öngörülere ulaşmak için test veri kümesini modeliyle birlikte kullanın.

Şifrelenmiş tahminler yapmak için:

1. Yeni `EILinearRegressionClient` , bir homomorphic şifreleme tabanlı istemci ve ortak anahtarlar oluşturun.

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. Homomorphic şifrelemesi tarafından oluşturulan ortak anahtarları çalışma alanı varsayılan blob deposuna yükleyin. Bu, anahtarları çıkarım sunucusuyla paylaşmanıza olanak sağlar.

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. Test verilerini şifreleyin

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. `run`Hizmeti çağırmak ve tahmine dayalı bir test veri kümesi sağlamak IÇIN SDK 'nın API 'sini kullanın. Bağlantı dizesini ortak anahtarların karşıya yüklendiği BLOB depolama alanına göndermemiz gerekir.

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. Sonuçların şifresini çözmek için istemcisini kullanın.

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu örnekte oluşturulan Web hizmetini silin:

```python
service.delete()
```

Oluşturduğunuz Azure kaynaklarını artık kullanmayı planlamıyorsanız, silin. Bu, hala çalışmakta olan unutilized kaynakları için ücretlendirmeye engel olur. Daha fazla bilgi edinmek için [Kaynakları Temizleme](how-to-manage-workspace.md#clean-up-resources) kılavuzuna bakın.