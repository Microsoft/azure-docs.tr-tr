---
title: ML modellerini FPGAs 'ye dağıtma
titleSuffix: Azure Machine Learning
description: Alan-programlanabilir kapı dizileri hakkında bilgi edinin. Bir Web hizmetini bir FPGA üzerinde, ultra düşük gecikme çıkarımı için Azure Machine Learning ile dağıtabilirsiniz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 09/24/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, devx-track-python, deploy
ms.openlocfilehash: e6a58a6555602af2494683037721a1f83e7ea33c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519325"
---
# <a name="deploy-ml-models-to-field-programmable-gate-arrays-fpgas-with-azure-machine-learning"></a>Azure Machine Learning ile ML modellerini alan programlanabilir kapı dizileri (FPGAs) ile dağıtma 

Bu makalede, FPGAs hakkında bilgi edinirsiniz ve [Azure Machine Learning](overview-what-is-azure-ml.md)' den [donanım hızlandırmalı modeller Python paketini](/python/api/azureml-accel-models/azureml.accel) kullanarak ml MODELLERINIZI bir Azure FPGA 'ye nasıl dağıtırsınız.

## <a name="what-are-fpgas"></a>FPGAs nedir?
FPGA’lar programlanabilen bir mantık blokları dizisi ve yeniden yapılandırılabilen bir bağlantı hiyerarşisi içerir. Birbirine bağlı, bu blokların üretim sonrasında çeşitli şekillerde yapılandırılmasına izin verir. Diğer yongalarla karşılaştırıldığında, FPGAs, bir programlama programlamasına ve performansına ilişkin bir bileşim sunar. 

FPGAs gerçek zamanlı çıkarım (veya model Puanlama) istekleri için düşük gecikme süresi elde etmenizi mümkün kılar. Zaman uyumsuz istekler (toplu işleme) gerekli değildir. Daha fazla verinin işlenmesi gerektiğinden, toplu işleme gecikmeye neden olabilir. Sinir işleme birimlerinin uygulamaları toplu işleme gerektirmez; Bu nedenle gecikme süresi, CPU ve GPU işlemcilere kıyasla birçok kez daha düşük olabilir.

Farklı makine öğrenimi modelleri türleri için FPGAs 'yi yeniden yapılandırabilirsiniz. Bu esneklik, kullanılan en iyi sayısal duyarlık ve bellek modeline göre uygulamaların hızlandırlanmasını kolaylaştırır. FPGAs yeniden yapılandırdığından, hızlı değişen AI algoritmalarının gereksinimleriyle güncel kalabilirler.

![Azure Machine Learning FPGA karşılaştırması diyagramı](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|İşlemci| Kısaltma |Description|
|---|:-------:|------|
|Uygulamaya özgü tümleşik devreler|ASICS|Google 'ın Tensor Işlemci birimleri (TPU) gibi özel devreler en yüksek verimliliği sağlar. Gereksinimleriniz değiştikçe bu değişiklikler yeniden yapılandırılamaz.|
|Alan-programlanabilir kapı dizileri|FPGA'lar|Azure 'da kullanılabilir olanlar gibi FPGAs 'ler, ASICs performansına yakın performans sağlar. Ayrıca, yeni mantık uygulamak için zaman içinde esnek ve yeniden yapılandırılabilir.|
|Grafik işleme birimleri|GPU’lar|AI hesaplamaları için popüler bir seçenek. GPU 'Lar paralel işleme özellikleri sunarak CPU 'dan görüntü işlemeye daha hızlı bir şekilde çalışır.|
|Merkezi işleme birimleri|CPU Sayısı|Performans ve video işleme için ideal olmayan genel amaçlı işlemciler.|

## <a name="fpga-support-in-azure"></a>Azure 'da FPGA desteği

Microsoft Azure, dünyanın en büyük bulut yatırımı Ile FPGAs ' dir. Microsoft, derin sinir ağı (DNN) değerlendirmesi, Bing Arama derecelendirmesi ve yazılım tanımlı ağ (SDN) hızlandırma için FPGAs kullanır ve bu da diğer görevler için CPU 'Ları boşaltıp gecikme süresini azaltır.

Azure 'daki FPGAs, veri bilimcilerinin ve geliştiricilerin gerçek zamanlı AI hesaplamalarını hızlandırmak için kullanacağı Intel FPGA cihazlarını temel alır. Bu FPGA özellikli mimari, performans, esneklik ve ölçek sunar ve Azure 'da kullanılabilir.

Azure FPGAs Azure Machine Learning tümleşiktir. Azure, hizmetinizin ölçeğini genişletmek için FPGAs genelinde önceden eğitilen DNN paralel hale getirmek. DNNs, aktarım öğrenimi için derin bir şekilde veya güncelleştirilmiş ağırlıklarla ince ayar olarak önceden eğitilmiş olabilir.

|Azure üzerinde senaryolar & yapılandırma|Desteklenen DNN modelleri|Bölgesel destek|
|--------------------------|--------------------|----------------|
|+ Görüntü sınıflandırması ve tanıma senaryoları<br/>+ TensorFlow dağıtımı (TensorFlow 1. x gerektirir)<br/>+ Intel FPGA donanımı|-ResNet 50<br/>-ResNet 152<br/>-DenseNet-121<br/>-VGG-16<br/>-SSD-VGG|-Doğu ABD<br/>-Güneydoğu Asya<br/>-Batı Avrupa<br/>-Batı ABD 2|

Gecikme ve aktarım hızını iyileştirmek için, FPGA modeline veri gönderen istemciniz yukarıdaki bölgelerden birinde (modeli dağıttığınız) olmalıdır.

**Azure VM 'leri Için PBS ailesi** , Intel varış a 10 FPGAs içerir. Azure kota ayırmayı denetlediğinizde, "standart PBS ailesi vCPU 'Lar" olarak gösterilir. PB6 VM 'sinin altı vCPU ve bir FPGA vardır. PB6 VM, bir FPGA 'ye model dağıtımı sırasında Azure Machine Learning tarafından otomatik olarak sağlanır. Yalnızca Azure ML ile kullanılır ve rastgele bitstreams çalıştırılamaz. Örneğin, FPGA 'yi bitstreams ile, şifreleme, kodlama vb. için flabileceksiniz.

## <a name="deploy-models-on-fpgas"></a>FPGAs 'de modeller dağıtma

[Azure Machine Learning hızlandırılmış donanım modelleri](/python/api/azureml-accel-models/azureml.accel)Ile fpgas üzerinde bir modeli Web hizmeti olarak dağıtabilirsiniz. FPGAs kullanımı, tek bir toplu iş boyutuyla bile Ultra düşük gecikme çıkarımı sağlar. 

Bu örnekte, giriş görüntüsünü önceden işlemek için bir TensorFlow grafiği oluşturun, bir FPGA üzerinde ResNet 50 kullanarak çalışır hale getirin ve ardından, ImageNet veri kümesi üzerinde eğitilen bir sınıflandırıcı aracılığıyla özellikleri çalıştırın. Ardından, model bir AKS kümesine dağıtılır.

### <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bir tane yoksa, [Kullandıkça Öde](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) hesabı oluşturun (ücretsiz Azure hesapları FPGA kotası için uygun değildir).

- [Çalışma alanı oluşturma](how-to-manage-workspace.md)bölümünde açıklandığı gibi, Python için bir Azure Machine Learning çalışma alanı ve Azure Machine Learning SDK 'sı yüklendi.
 
- Donanım hızlandırmalı modeller paketi:  `pip install --upgrade azureml-accel-models[cpu]`    
    
- [Azure CLI](/cli/azure/install-azure-cli)

- FPGA kotası. Kota [isteği](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2nac9-PZhBDnNSV2ITz0LNUN0U5S0hXRkNITk85QURTWk9ZUUFUWkkyTC4u)gönder veya kotayı denetlemek IÇIN bu CLI komutunu çalıştırın: 

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

   En az __geçerli değer__ döndüren 6 vCPU kullandığınızdan emin olun.  

### <a name="define-the-tensorflow-model"></a>TensorFlow modelini tanımlama

Bir hizmet tanımı oluşturmak için [Python için Azure Machine Learning SDK 'sını](/python/api/overview/azure/ml/intro) kullanarak başlayın. Hizmet tanımı, TensorFlow 'a göre grafiklerin bir işlem hattını (giriş, korturun ve sınıflandırıcı) açıklayan bir dosyadır. Dağıtım komutu, tanım ve grafikleri bir ZIP dosyası olarak sıkıştırır ve ZIP 'yi Azure Blob depolama alanına yükler. DNN, FPGA üzerinde çalışmak üzere zaten dağıtıldı.

1. Azure Machine Learning çalışma alanını yükle

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. Görüntü ön işlemden. Web hizmeti girişi bir JPEG görüntüsüdür.  İlk adım, JPEG görüntüsünün kodunu çözmeye ve ön işlemden geçmelidir.  JPEG görüntüleri dize olarak değerlendirilir ve sonuç, ResNet 50 modelinin girişi olacak şekilde görünür.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Yük korleştirici. Modeli başlatın ve bir özellik olarak kullanılmak üzere ResNet50 'in quantiizer sürümünün bir TensorFlow kontrol noktasını indirin.  Diğer derin sinir ağlarını içeri aktarmak için kod parçacığındaki "QuantizedResnet50" öğesini değiştirin:

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

1. Bir sınıflandırıcı ekleyin. Bu sınıflandırıcı, ımagenet veri kümesi üzerinde eğitildi.

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. Modeli kaydedin. Artık Önişlemci, ResNet 50 feaizer ve sınıflandırıcının yüklenmiş olduğuna göre, grafiği ve ilişkili değişkenleri model olarak kaydedin.

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

1. Giriş ve çıkış tenörleri ' nı, **model dönüştürme ve çıkarım istekleri için kullanacaksınız**. 

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   Varsayılan sınıflandırıcısını kullandıysanız çıkarımı için sınıflandırıcılarına yönelik aşağıdaki modeller mevcuttur.

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

### <a name="convert-the-model-to-the-open-neural-network-exchange-format-onnx"></a>Modeli Open sinir Network Exchange biçimine (ONNX) Dönüştür

FPGAs 'e dağıtabilmeniz için önce modeli [Onnx](https://onnx.ai/) biçimine dönüştürün.

1. Azure Blob depolama alanındaki ZIP dosyası ile SDK 'Yı kullanarak modeli [kaydedin](concept-model-management-and-deployment.md) . Model hakkında Etiketler ve diğer meta veriler eklemek, eğitilen modellerinizi izlemenize yardımcı olur.

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

1. TensorFlow grafiğini ONNX biçimine dönüştürün.  Giriş ve çıkış tenörleri adlarını sağlamanız gerekir, bu sayede istemciniz Web hizmetini kullanırken bunları kullanabilir.

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

### <a name="containerize-and-deploy-the-model"></a>Modeli kapsayıcı ve dağıtma

Sonra, dönüştürülen modelden ve tüm bağımlılıklardan bir Docker görüntüsü oluşturun.  Bu Docker görüntüsü daha sonra dağıtılabilir ve örneklenebilir.  Desteklenen dağıtım hedefleri, Bulutta Azure Kubernetes hizmeti (AKS) veya [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md)gibi bir uç cihaz içerir.  Ayrıca, kayıtlı Docker görüntünüz için Etiketler ve açıklamalar ekleyebilirsiniz.

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

#### <a name="deploy-to-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes hizmet kümesine dağıtma

1. Modelinizi yüksek ölçekli üretim Web hizmeti olarak dağıtmak için AKS kullanın. Azure Machine Learning SDK, CLı veya [Azure Machine Learning Studio](https://ml.azure.com)kullanarak yeni bir tane oluşturabilirsiniz.

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget
    
    # Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
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

1. Kapsayıcıyı AKS kümesine dağıtın.

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

#### <a name="deploy-to-a-local-edge-server"></a>Yerel bir uç sunucusuna dağıtma

Tüm [Azure Data Box Edge cihazlar](../databox-online/azure-stack-edge-overview.md
) , modeli çalıştırmak IÇIN BIR FPGA içerir.  Yalnızca bir model FPGA üzerinde tek seferde çalıştırılabilir.  Farklı bir model çalıştırmak için yeni bir kapsayıcı dağıtmanız yeterlidir. Yönergeler ve örnek kod, [Bu Azure örneğinde](https://github.com/Azure-Samples/aml-hardware-accelerated-models)bulunabilir.

### <a name="consume-the-deployed-model"></a>Dağıtılan modeli tüketme

Son olarak, modelden öngörülere ulaşmak için Docker görüntüsünü çağırmak üzere örnek istemcisini kullanın.  Örnek istemci kodu kullanılabilir:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Docker görüntüsü, gRPC 'yi ve "tahmin" API 'sini sunan TensorFlow 'ı destekler.

Ayrıca, TensorFlow hizmeti için örnek bir istemci indirebilirsiniz.

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
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

### <a name="clean-up-resources"></a>Kaynakları temizleme

Gereksiz maliyetlerin önüne geçmek için kaynaklarınızı **Şu sırayla** temizleyin: Web hizmeti, görüntü ve sonra model.

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>Sonraki adımlar

+ [Web Hizmetleri belgenizi güvenli hale getirme](how-to-secure-web-service.md) hakkında bilgi edinin.

+ FPGA ve [Azure Machine Learning fiyatlandırma ve maliyetler](https://azure.microsoft.com/pricing/details/machine-learning/)hakkında bilgi edinin.

+ [Hiper ölçekli donanım: Azure + FPGA üzerinde ölçekli ML: derleme 2018 (video)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Microsoft FPGA tabanlı yapılandırılabilir bulut (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Gerçek zamanlı AI için proje Brainwave](https://www.microsoft.com/research/project/project-brainwave/)

+ [Otomatik Optik İnceleme sistemi](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
