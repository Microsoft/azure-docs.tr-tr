---
title: GPU özellikli Azure Container Instances dağıtma
description: GPU kaynaklarını kullanarak işlem yoğunluklu kapsayıcı uygulamaları çalıştırmak için Azure Container Instances 'ı dağıtmayı öğrenin.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/17/2019
ms.author: danlep
ms.openlocfilehash: 08a3cd8841b6b867a2dd22078fca3543d2067830
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147882"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>GPU kaynaklarını kullanan kapsayıcı örneklerini dağıtma

Azure Container Instances yoğun işlem yoğunluklu iş yüklerini çalıştırmak için, [kapsayıcı gruplarınızı](container-instances-container-groups.md) *GPU kaynaklarıyla*dağıtın. Gruptaki kapsayıcı örnekleri, CUDA ve derin öğrenme uygulamaları gibi kapsayıcı iş yüklerini çalıştırırken bir veya daha fazla NVıDıA Tesla GPU erişimine erişebilir.

Bu makalede, bir [YAML dosyası](container-instances-multi-container-yaml.md) veya [Kaynak Yöneticisi şablonu](container-instances-multi-container-group.md)kullanarak BIR kapsayıcı grubunu dağıtırken GPU kaynaklarının nasıl ekleneceği gösterilmektedir. Ayrıca, Azure portal kullanarak bir kapsayıcı örneği dağıtırken GPU kaynaklarını belirtebilirsiniz.

> [!IMPORTANT]
> Bu özellik şu anda önizleme aşamasındadır ve bazı [sınırlamalar geçerlidir](#preview-limitations). Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

## <a name="preview-limitations"></a>Önizleme sınırlamaları

Önizleme aşamasında, kapsayıcı gruplarında GPU kaynakları kullanılırken aşağıdaki sınırlamalar geçerlidir. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Destek, zaman içinde ek bölgeler için eklenecektir.

**Desteklenen işletim sistemi türleri**: yalnızca Linux

**Ek sınırlamalar**: bir kapsayıcı grubu [sanal bir ağa](container-instances-vnet.md)dağıtıldığında GPU kaynakları kullanılamaz.

## <a name="about-gpu-resources"></a>GPU kaynakları hakkında

### <a name="count-and-sku"></a>Sayı ve SKU

Bir kapsayıcı örneğinde GPU 'ları kullanmak için aşağıdaki bilgileri içeren bir *GPU kaynağı* belirtin:

* **Sayı** -GPU sayısı: **1**, **2**veya **4**.
* **SKU** -GPU SKU 'Su: **K80**, **P100**veya **V100**. Her SKU, aşağıdaki Azure GPU etkin VM ailelerinde bulunan NVıDıA Tesla GPU ile eşlenir:

  | SKU | VM ailesi |
  | --- | --- |
  | K80 | [KSK](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

GPU kaynaklarını dağıttığınızda, önceki tabloda gösterilen en fazla değere kadar iş yüküne uygun CPU ve bellek kaynakları ayarlayın. Bu değerler, GPU kaynakları olmadan kapsayıcı gruplarında kullanılabilir olan CPU ve bellek kaynaklarından daha büyüktür.  

### <a name="things-to-know"></a>Bilmemiz gerekenler

* **Dağıtım zamanı** -GPU kaynaklarını içeren bir kapsayıcı grubunun oluşturulması **8-10 dakikaya**kadar sürer. Bunun nedeni, Azure 'da bir GPU VM 'si sağlamak ve yapılandırmak için ek süredir. 

* **Fiyatlandırma** -GPU kaynakları olmadan kapsayıcı gruplarına benzer, Azure, GPU kaynaklarıyla bir kapsayıcı grubu *süresince* tüketilen kaynaklar için Azure faturaları. Süre, kapsayıcı grubu sonlanana kadar ilk kapsayıcının görüntüsünü çekme zamanından hesaplanır. Kapsayıcı grubunun dağıtılacağı zaman dahil değildir.

  [Fiyatlandırma ayrıntılarına](https://azure.microsoft.com/pricing/details/container-instances/) bakın.

* **CUDA sürücüleri** -GPU kaynaklarıyla kapsayıcı ÖRNEKLERI, NVıDıA CUDA sürücüleriyle ve kapsayıcı çalışma zamanları ile önceden sağlanmış olduğundan CUDA iş yükleri için geliştirilmiş kapsayıcı görüntülerini kullanabilirsiniz.

  Bu aşamada CUDA 9,0 destekliyoruz. Örneğin, Docker dosyanız için aşağıdaki temel görüntüleri kullanabilirsiniz:
  * [NVIDIA/CUDA: 9.0-Base-Ubuntu 16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [TensorFlow/TensorFlow: 1.12.0-GPU-PY3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>YAML örneği

GPU kaynaklarını eklemenin bir yolu, bir [YAML dosyası](container-instances-multi-container-yaml.md)kullanarak bir kapsayıcı grubu dağıtmaktır. Aşağıdaki YAML 'yi *GPU-Deploy-aci. YAML*adlı yeni bir dosyaya kopyalayın ve ardından dosyayı kaydedin. Bu YAML, K80 GPU ile bir kapsayıcı örneği belirten *gpucontainergroup* adlı bir kapsayıcı grubu oluşturur. Örnek, örnek CUDA vektör ekleme uygulaması çalıştırır. Kaynak istekleri iş yükünü çalıştırmak için yeterlidir.

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

, `--file` parametresi için YAML dosya adını belirterek, [az Container Create][az-container-create] komutuyla kapsayıcı grubunu dağıtın. Bir kaynak grubunun adını ve *eastus* gibi bir kapsayıcı grubu için GPU kaynaklarını destekleyen bir konum sağlamanız gerekir.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Dağıtımın tamamlanması birkaç dakika sürer. Daha sonra kapsayıcı başlar ve bir CUDA vektör ekleme işlemini çalıştırır. Günlük çıktısını görüntülemek için [az Container logs][az-container-logs] komutunu çalıştırın:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Çıktı:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Kaynak Yöneticisi şablonu örneği

Bir kapsayıcı grubunu GPU kaynaklarıyla dağıtmanın bir başka yolu da [Kaynak Yöneticisi şablonu](container-instances-multi-container-group.md)kullanmaktır. `gpudeploy.json`adlı bir dosya oluşturarak başlayın, ardından aşağıdaki JSON öğesine kopyalayın. Bu örnek, bir V100 GPU ile, bir [TensorFlow](https://www.tensorflow.org/) eğitim işini, veri kümesine göre çalıştıran bir kapsayıcı örneği dağıtır. Kaynak istekleri iş yükünü çalıştırmak için yeterlidir.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

[Az Group Deployment Create][az-group-deployment-create] komutuyla şablonu dağıtın. GPU kaynaklarını destekleyen *eastus* gibi bir bölgede oluşturulan bir kaynak grubunun adını sağlamanız gerekir.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

Dağıtımın tamamlanması birkaç dakika sürer. Ardından, kapsayıcı başlar ve TensorFlow işini çalıştırır. Günlük çıktısını görüntülemek için [az Container logs][az-container-logs] komutunu çalıştırın:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Çıktı:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

GPU kaynaklarının kullanılması pahalı olabileceğinden, kapsayıcılarınızın uzun süreler boyunca beklenmedik şekilde çalışmadığından emin olun. Azure portal Kapsayıcılarınızı izleyin veya [az Container Show][az-container-show] komutuyla bir kapsayıcı grubunun durumunu denetleyin. Örneğin:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Oluşturduğunuz kapsayıcı örnekleriyle çalışmayı tamamladığınızda, bunları aşağıdaki komutlarla silin:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Sonraki adımlar

* [YAML dosyası](container-instances-multi-container-yaml.md) veya [Kaynak Yöneticisi şablonu](container-instances-multi-container-group.md)kullanarak bir kapsayıcı grubu dağıtma hakkında daha fazla bilgi edinin.
* Azure 'da [GPU IYILEŞTIRILMIŞ VM boyutları](../virtual-machines/linux/sizes-gpu.md) hakkında daha fazla bilgi edinin.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
