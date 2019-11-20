---
title: FPGA nedir? nasıl dağıtılır?
titleSuffix: Azure Machine Learning
description: Bir FPGA üzerinde çalışan bir modelle bir Web hizmetini, son derece düşük gecikme çıkarımı için Azure Machine Learning ile dağıtmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 5e8dc6181660f0c1545df0688e2749f8f0187027
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496893"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>Alan-programlanabilir kapı dizileri (FPGA) ve dağıtma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makale, alan-programlanabilir kapı dizileri (FPGA) ile bir giriş sağlar ve Azure FPGA 'ye Azure Machine Learning kullanarak modellerinizi nasıl dağıtacağınızı gösterir. 

FPGAs, programlanabilir mantık blokları dizisi ve yeniden yapılandırılabilir birbirine bağlı bir hiyerarşi içerir. Birbirine bağlı, bu blokların üretim sonrasında çeşitli şekillerde yapılandırılmasına izin verir. Diğer yongalarla karşılaştırıldığında, FPGAs, bir programlama programlamasına ve performansına ilişkin bir bileşim sunar.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs vs. CPU, GPU ve ASIC

Aşağıdaki diyagramda ve tabloda Fpg'in diğer işlemcilerle nasıl Karşılaştırıldığı gösterilmektedir.

![Azure Machine Learning FPGA karşılaştırması diyagramı](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|İşlemci||Açıklama|
|---|:-------:|------|
|Uygulamaya özgü tümleşik devreler|ASICS|Google 'ın TensorFlow Işlemci birimleri (TPU) gibi özel devreler en yüksek verimliliği sağlar. Gereksinimleriniz değiştikçe bu değişiklikler yeniden yapılandırılamaz.|
|Alan-programlanabilir kapı dizileri|FPGA'lar|Azure 'da kullanılabilir olanlar gibi FPGAs 'ler, ASICs performansına yakın performans sağlar. Ayrıca, yeni mantık uygulamak için zaman içinde esnek ve yeniden yapılandırılabilir.|
|Grafik işleme birimleri|GPU’lar|AI hesaplamaları için popüler bir seçenek. GPU 'Lar paralel işleme özellikleri sunarak CPU 'dan görüntü işlemeye daha hızlı bir şekilde çalışır.|
|Merkezi işleme birimleri|CPU’lar|Performans ve video işleme için ideal olmayan genel amaçlı işlemciler.|

Azure 'daki FPGAs, veri bilimcilerinin ve geliştiricilerin gerçek zamanlı AI hesaplamalarını hızlandırmak için kullanacağı Intel FPGA cihazlarını temel alır. Bu FPGA özellikli mimari, performans, esneklik ve ölçek sunar ve Azure 'da kullanılabilir.

FPGAs gerçek zamanlı çıkarım (veya model Puanlama) istekleri için düşük gecikme süresi elde etmenizi mümkün kılar. Zaman uyumsuz istekler (toplu işleme) gerekli değildir. Daha fazla verinin işlenmesi gerektiğinden, toplu işleme gecikmeye neden olabilir. Sinir işleme birimlerinin uygulamaları toplu işleme gerektirmez; Bu nedenle gecikme süresi, CPU ve GPU işlemcilere kıyasla birçok kez daha düşük olabilir.

### <a name="reconfigurable-power"></a>Yeniden yapılandırılabilir güç
Farklı makine öğrenimi modelleri türleri için FPGAs 'yi yeniden yapılandırabilirsiniz. Bu esneklik, kullanılan en iyi sayısal duyarlık ve bellek modeline göre uygulamaların hızlandırlanmasını kolaylaştırır. FPGAs yeniden yapılandırdığından, hızlı değişen AI algoritmalarının gereksinimleriyle güncel kalabilirler.

## <a name="whats-supported-on-azure"></a>Azure 'da desteklenen özellikler
Microsoft Azure, dünyanın en büyük bulut yatırımı Ile FPGAs ' dir. Bu FPGA özellikli donanım mimarisini kullanarak eğitilen sinir ağları hızla ve daha düşük gecikme süresiyle çalışır. Azure, hizmetinizin ölçeğini genişletmek için FPGAs genelinde önceden eğitilen derin sinir ağlarını (DNN) paralel hale getirmek. DNNs, aktarım öğrenimi için derin bir şekilde veya güncelleştirilmiş ağırlıklarla ince ayar olarak önceden eğitilmiş olabilir.

Azure 'da FPGAs şunları destekler:

+ Görüntü sınıflandırması ve tanıma senaryoları
+ TensorFlow dağıtımı
+ Intel FPGA donanımı 

Bu DNN modelleri şu anda kullanılabilir:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGAs, bu Azure bölgelerinde kullanılabilir:
  - Doğu ABD
  - Güneydoğu Asya
  - Batı Avrupa
  - Batı ABD 2

> [!IMPORTANT]
> Gecikme ve aktarım hızını iyileştirmek için, FPGA modeline veri gönderen istemciniz yukarıdaki bölgelerden birinde (modeli dağıttığınız) olmalıdır.

**Azure VM 'leri Için PBS ailesi** , Intel varış a 10 FPGAs içerir. Azure kota ayırmayı denetlediğinizde, "standart PBS ailesi vCPU 'Lar" olarak gösterilir. PB6 VM 'sinin altı vCPU ve bir FPGA vardır ve bir FPGA 'ye model dağıtmanın bir parçası olarak Azure ML tarafından otomatik olarak sağlanır. Yalnızca Azure ML ile kullanılır ve rastgele bitstreams çalıştırılamaz. Örneğin, FPGA 'yi bitstreams ile, şifreleme, kodlama vb. için flabileceksiniz.

### <a name="scenarios-and-applications"></a>Senaryolar ve uygulamalar

Azure FPGAs Azure Machine Learning tümleşiktir. Microsoft, DNN değerlendirmesi, Bing Arama derecelendirmesi ve yazılım tanımlı ağ (SDN) hızlandırma için FPGAs ' i kullanarak diğer görevler için CPU 'Ları serbest bırakarak gecikme süresini azaltır.

Aşağıdaki senaryolar FPGAs kullanır:
+ [Otomatik Optik İnceleme sistemi](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Land kapağı eşleme](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)



## <a name="example-deploy-models-on-fpgas"></a>Örnek: FPGAs 'de modelleri dağıtma 

Azure Machine Learning Hızlandırılmış Donanım Modelleri ile FPGAs üzerinde bir modeli Web hizmeti olarak dağıtabilirsiniz. FPGAs kullanımı, tek bir toplu iş boyutuyla bile Ultra düşük gecikme çıkarımı sağlar. Çıkarım veya model Puanlama, dağıtılan modelin tahmin için en yaygın olarak üretim verilerinde kullanıldığı aşamadır.


### <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği.  Bir tane yoksa, başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

- FPGA kotası. Kotayı içerip içermediğini denetlemek için Azure CLı 'yı kullanın:

    ```shell
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > Diğer olası konumlar ``southeastasia``, ``westeurope``ve ``westus2``.

    Komut şuna benzer bir metin döndürür:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    __CurrentValue__altında en az 6 vCPU kullandığınızdan emin olun.

    Kotayı yoksa [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)bir istek gönderebilirsiniz.

- Python için bir Azure Machine Learning çalışma alanı ve Azure Machine Learning SDK 'Sı yüklendi. Daha fazla bilgi için bkz. [çalışma alanı oluşturma](how-to-manage-workspace.md).
 
- Donanım hızlandırmalı modeller için Python SDK:

    ```shell
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1. modelleri oluşturma ve Kapsayıcılı hale getirme

Bu belge, giriş görüntüsünü önceden işlemek için bir TensorFlow grafiği oluşturmayı, bir FPGA üzerinde ResNet 50 ' i kullanarak bir baskın hale getirme yapmayı ve ardından, ImageNet veri kümesi üzerinde eğitilen bir sınıflandırıcı aracılığıyla özellikleri çalıştırmayı açıklamaktadır.

Yönergeleri izleyerek şunları yapın:

* TensorFlow modelini tanımlama
* Modeli Dönüştür
* Modeli dağıtma
* Dağıtılan modeli tüketme
* Dağıtılan Hizmetleri Sil

Bir hizmet tanımı oluşturmak için [Python için Azure Machine Learning SDK 'sını](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) kullanın. Hizmet tanımı, TensorFlow 'a göre grafiklerin bir işlem hattını (giriş, korturun ve sınıflandırıcı) açıklayan bir dosyadır. Dağıtım komutu, tanım ve grafikleri otomatik olarak bir ZIP dosyası olarak sıkıştırır ve ZIP 'i Azure Blob depolama alanına yükler. DNN, FPGA üzerinde çalışmak üzere zaten dağıtıldı.

### <a name="load-azure-ml-workspace"></a>Azure ML çalışma alanı yükleme

Azure ML çalışma alanınızı yükleyin.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Ön işleme resmi

Web hizmeti girişi bir JPEG görüntüsüdür.  İlk adım, JPEG görüntüsünün kodunu çözmeye ve ön işlemden geçmelidir.  JPEG görüntüleri dize olarak değerlendirilir ve sonuç, ResNet 50 modelinin girişi olacak şekilde görünür.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Yük korleştirici

Modeli başlatın ve bir özellik olarak kullanılmak üzere ResNet50 'in quantiizer sürümünün bir TensorFlow kontrol noktasını indirin.  Aşağıdaki kod parçacığında "QuantizedResnet50" yerine, diğer derin sinir ağlarını içeri aktarabilirsiniz:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Sınıflandırıcı Ekle

Bu sınıflandırıcı, ımagenet veri kümesi üzerinde eğitildi.  Özelleştirilmiş ağırlıklarınız için aktarım öğrenimi ve eğitim örnekleri, [örnek Not defterleri](https://aka.ms/aml-notebooks)kümesinde mevcuttur.

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Modeli kaydetme

Artık Önişlemci, ResNet 50 feaizer ve sınıflandırıcının yüklenmiş olduğuna göre, grafiği ve ilişkili değişkenleri model olarak kaydedin.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                               inputs={'images': in_images},
                               outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>Giriş ve çıkış tenörleri 'nı Kaydet
Ön işleme ve sınıflandırıcı adımları sırasında oluşturulan giriş ve çıkış, model dönüştürme ve çıkarım için gerekecektir.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Model dönüştürme ve çıkarım istekleri için gerekli olacak şekilde giriş ve çıkış tenörleri ' nı kaydedin.

Kullanılabilir modeller ve karşılık gelen varsayılan sınıflandırıcı çıktısı, varsayılan sınıflandırıcısını kullandıysanız çıkarımı için kullanacağınız şeydir.

+ Resnet50, QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Modeli kaydetme

Azure Blob depolama alanındaki ZIP dosyası ile SDK 'Yı kullanarak modeli [kaydedin](./concept-model-management-and-deployment.md) . Model hakkında Etiketler ve diğer meta veriler eklemek, eğitilen modellerinizi izlemenize yardımcı olur.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

Zaten bir model kaydolduysanız ve yüklemek istiyorsanız, bu modeli alabilirsiniz.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>Modeli Dönüştür

TensorFlow grafiğini Open sinir Network Exchange biçimine ([Onnx](https://onnx.ai/)) dönüştürün.  Giriş ve çıkış tenörleri adlarını sağlamanız gerekir ve bu adlar, Web hizmetini kullandığınızda istemciniz tarafından kullanılacaktır.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Docker görüntüsü oluşturma

Dönüştürülen model ve tüm bağımlılıklar bir Docker görüntüsüne eklenir.  Bu Docker görüntüsü daha sonra dağıtılabilir ve örneklenebilir.  Desteklenen dağıtım hedefleri, bulutta veya [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)gibi bir uç cihazda aks 'leri içerir.  Ayrıca, kayıtlı Docker görüntünüz için Etiketler ve açıklamalar ekleyebilirsiniz.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

Görüntüleri etikete göre listeleyin ve herhangi bir hata ayıklama için ayrıntılı günlükleri alın.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. buluta veya kenara dağıtın

### <a name="deploy-to-the-cloud"></a>Buluta dağıtın

Modelinizi yüksek ölçekli bir üretim Web hizmeti olarak dağıtmak için Azure Kubernetes hizmeti (AKS) kullanın. Azure Machine Learning SDK, CLı veya [Azure Machine Learning Studio](https://ml.azure.com)kullanarak yeni bir tane oluşturabilirsiniz.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2”. If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

AKS dağıtımı 15 dakika içinde sürebilir.  Dağıtımın başarılı olup olmadığını denetleyin.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Kapsayıcıyı AKS kümesine dağıtın.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>Bulut hizmetini test etme
Docker görüntüsü, gRPC 'yi ve "tahmin" API 'sini sunan TensorFlow 'ı destekler.  Modelden öngörülere ulaşmak için Docker görüntüsünü çağırmak üzere örnek istemcisini kullanın.  Örnek istemci kodu kullanılabilir:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

TensorFlow hizmeti kullanmak istiyorsanız, [örnek bir istemci indirebilirsiniz](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Bu sınıflandırıcı, [ımagenet](http://www.image-net.org/) veri kümesi üzerinde eğitilen olduğundan, sınıfları okunabilir etiketlerle eşleyin.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>Hizmeti Temizleme
Web hizmetinizi, resminizi ve modelinizi silin (bağımlılıklar olduğundan bu sırada yapılmalıdır).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>Yerel bir uç sunucusuna dağıtma

Tüm [Azure Data Box Edge cihazlar](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) , modeli çalıştırmak IÇIN BIR FPGA içerir.  Yalnızca bir model FPGA üzerinde tek seferde çalıştırılabilir.  Farklı bir model çalıştırmak için yeni bir kapsayıcı dağıtmanız yeterlidir. Yönergeler ve örnek kod, [Bu Azure örneğinde](https://github.com/Azure-Samples/aml-hardware-accelerated-models)bulunabilir.

## <a name="secure-fpga-web-services"></a>Güvenli FPGA Web Hizmetleri

FPGA Web hizmetlerinizin güvenliğini sağlamak için [Güvenli Web Hizmetleri](how-to-secure-web-service.md) belgesine bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu not defterlerine, videolara ve bloglara göz atın:

+ Birçok [örnek Not defteri](https://aka.ms/aml-accel-models-notebooks).

+ [Hiper ölçekli donanım: Azure + FPGA üzerinde ölçekli ML: derleme 2018 (video)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Microsoft FPGA tabanlı yapılandırılabilir bulut (video) içinde](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Gerçek zamanlı AI için Project Brainwave: proje giriş sayfası](https://www.microsoft.com/research/project/project-brainwave/)
