---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: ab31d45808a8c77c53b895643eec63952201d9e4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800183"
---
`inferenceconfig.json` belgesindeki girdiler, [ınenceconfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) sınıfının parametreleriyle eşlenir. Aşağıdaki tabloda, JSON belgesindeki varlıklar ve yöntemin parametreleri arasındaki eşleme açıklanmaktadır:

| JSON varlığı | Yöntem parametresi | Açıklama |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Görüntüde çalıştırılacak kodu içeren yerel bir dosyanın yolu. |
| `runtime` | `runtime` | Görüntü için kullanılacak çalışma zamanı. Desteklenen geçerli çalışma zamanları `spark-py` ve `python`. |
| `condaFile` | `conda_file` | İsteğe bağlı. Görüntü için kullanılacak Conda ortam tanımını içeren yerel bir dosyanın yolu. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | İsteğe bağlı. Görüntüyü ayarlarken çalıştırılacak ek Docker adımlarını içeren yerel bir dosyanın yolu. |
| `sourceDirectory` | `source_directory` | İsteğe bağlı. Görüntüyü oluşturmak için tüm dosyaları içeren klasörlerin yolu. |
| `enableGpu` | `enable_gpu` | İsteğe bağlı. Görüntüde GPU desteğinin etkinleştirilip etkinleştirilmeyeceğini belirtir. GPU görüntüsünün Azure Container Instances, Azure Machine Learning Işlem, Azure sanal makineleri ve Azure Kubernetes hizmeti gibi bir Azure hizmetinde kullanılması gerekir. Varsayılan değer false 'dur. |
| `baseImage` | `base_image` | İsteğe bağlı. Temel görüntü olarak kullanılacak özel görüntü. Hiçbir temel görüntü sağlanmazsa, görüntü, belirtilen çalışma zamanı parametresini temel alır. |
| `baseImageRegistry` | `base_image_registry` | İsteğe bağlı. Temel görüntüyü içeren görüntü kayıt defteri. |
| `cudaVersion` | `cuda_version` | İsteğe bağlı. GPU desteği gerektiren görüntüler için yüklenecek CUDA sürümü. GPU görüntüsünün Azure Container Instances, Azure Machine Learning Işlem, Azure sanal makineleri ve Azure Kubernetes hizmeti gibi bir Azure hizmetinde kullanılması gerekir. Desteklenen sürümler 9,0, 9,1 ve 10,0. `enable_gpu` ayarlandıysa, varsayılan değer 9,1 ' dir. |
| `description` | `description` | Görüntü için bir açıklama. |

Aşağıdaki JSON, CLı ile kullanılacak örnek bir çıkarım yapılandırması örneğidir:

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```