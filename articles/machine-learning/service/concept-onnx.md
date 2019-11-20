---
title: 'ONNX: yüksek performans, platformlar arası çıkarımı'
titleSuffix: Azure Machine Learning
description: Open sinir Network Exchange (ONNX) kullanmanın, makine öğrenimi modelinizin çıkarımını en üst düzeye getirmenize nasıl yardımcı olabileceğini öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: c94d36065b27b0afcaeb214b8a86e5e2a09df14b
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648015"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX ve Azure Machine Learning: ML modellerini oluşturma ve hızlandırma

[Open sinir Network Exchange](https://onnx.ai) (onnx) kullanmanın, makine öğrenimi modelinizin çıkarımını en üst düzeye getirmenize nasıl yardımcı olabileceğini öğrenin. Çıkarım veya model Puanlama, dağıtılan modelin tahmin için en yaygın olarak üretim verilerinde kullanıldığı aşamadır. 

Donanım yeteneklerini en iyi hale getirmek için modeli ve çıkarım kitaplığını ayarlamanız gerektiğinden, bu yana, makine öğrenimi modellerini çıkarma (veya model Puanlama) için optimize etmek zordur. Farklı türlerde platformlar (bulut/kenar, CPU/GPU vb.) üzerinde en iyi performansı almak istiyorsanız sorun son derece zor olur. çünkü her biri farklı özelliklere ve özelliklere sahiptir. Çeşitli platformlarda çalışması gereken çeşitli çerçeveler için modelleriniz varsa karmaşıklık artar. Tüm farklı çerçeve ve donanım birleşimlerini iyileştirmek çok zaman alır. Tercih ettiğiniz çerçevede bir kez eğitme ve bulutta veya kenarda her yerde çalıştırılan bir çözüm. Burada ONNX yer verilir.

Microsoft ve makine öğrenimi modellerini temsil etmek için açık bir standart olarak ONNX tarafından oluşturulan bir iş ortağı topluluğu. TensorFlow, PyTorch, SciKit-öğren, keras, Chainer, MXNet ve MATLAB gibi [birçok çerçeve](https://onnx.ai/supported-tools) modeli, standart onnx biçimine aktarılabilir veya dönüştürülebilirler. Modeller ONNX biçiminde olduktan sonra çeşitli platformlar ve cihazlarda çalıştırılabilir.

[Onnx çalışma zamanı](https://github.com/Microsoft/onnxruntime) , üretim için onnx modellerini dağıtmaya yönelik yüksek performanslı bir çıkarım altyapısıdır. Bulut ve kenar için en iyi duruma getirilmiştir ve Linux, Windows ve Mac üzerinde çalışmaktadır. İçinde C++yazılmış, C, Python ve C# API 'leri de vardır. ONNX çalışma zamanı tüm ONNX-ML belirtiminin desteğini sağlar ve ayrıca NVIDIA GPU 'Larda TensorRT gibi farklı donanımlarda Hızlandırıcılar ile tümleşir.

ONNX çalışma zamanı, Bing, Office ve bilişsel hizmetler gibi yüksek ölçekli Microsoft hizmetlerinde kullanılır. Performans kazançları bir dizi etkene bağlıdır, ancak bu Microsoft Hizmetleri __CPU üzerinde ortalama bir 2x performans kazancı__gördük. ONNX çalışma zamanı, yüzlerce milyonlarca cihazda Windows ML 'nin bir parçası olarak da kullanılır. Çalışma zamanını Azure Machine Learning ile kullanabilirsiniz. ONNX çalışma zamanını kullanarak, kapsamlı üretim sınıfı iyileştirmeleri, test ve devam eden geliştirmelerden yararlanabilirsiniz.

[Eğitim, dönüştürücüler ve dağıtımı gösteren ONNX akış diyagramı ![](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX modellerini al

ONNX modellerini çeşitli yollarla edinebilirsiniz:
+ Azure Machine Learning yeni bir ONNX modeli eğitme (Bu makalenin altındaki örneklere bakın)
+ Varolan modeli başka bir biçimden ONNX 'e Dönüştür ( [öğreticilere](https://github.com/onnx/tutorials)bakın) 
+ [Onnx model Zoo](https://github.com/onnx/models) (Bu makalenin altındaki örneklere bakın) için önceden eğitilen BIR onnx modeli alın
+ [Azure özel görüntü işleme Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 'ten özelleştirilmiş bir onnx modeli oluşturma 

Görüntü sınıflandırması, nesne algılama ve metin işleme gibi birçok model ONNX modelleri olarak temsil edilebilir. Ancak bazı modeller başarıyla dönüştürülemeyebilir. Bu durumla karşılaşırsanız lütfen kullandığınız ilgili dönüştürücünün GitHub 'da bir sorun bildirin. Sorun giderilene kadar mevcut biçim modelinizi kullanmaya devam edebilirsiniz.

## <a name="deploy-onnx-models-in-azure"></a>Azure 'da ONNX modellerini dağıtma

Azure Machine Learning ile ONNX modellerinizi dağıtabilir, yönetebilir ve izleyebilirsiniz. Standart [dağıtım iş akışını](concept-model-management-and-deployment.md) ve Onnx çalışma zamanını kullanarak bulutta BARıNDıRıLAN bir REST uç noktası oluşturabilirsiniz. Kendiniz denemek için bu makalenin sonundaki örnek jupi not defterlerine bakın. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Python ile ONNX çalışma zamanını yükleyip kullanma

ONNX çalışma zamanına yönelik Python paketleri [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)) üzerinde bulunabilir. Lütfen yüklemeden önce [sistem gereksinimlerini](https://github.com/Microsoft/onnxruntime#system-requirements) okuyun. 

 Python için ONNX çalışma zamanını yüklemek için aşağıdaki komutlardan birini kullanın: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Python betiğinizdeki ONNX çalışma zamanını çağırmak için şunu kullanın:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

Modele eşlik eden belgeler genellikle modeli kullanmaya yönelik giriş ve çıkışları size bildirir. Ayrıca, modeli görüntülemek için [netron](https://github.com/lutzroeder/Netron) gibi bir görselleştirme aracı da kullanabilirsiniz. ONNX çalışma zamanı ayrıca model meta verilerini, girişleri ve çıkışları sorgulamanızı sağlar:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Modelinize çıkarmasını sağlamak için `run` kullanın ve döndürülmek istediğiniz çıktılar listesini (tümünün istiyorsanız boş bırakın) ve giriş değerlerinin haritasını geçirin. Sonuç, çıktıların bir listesidir.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Tüm Python API başvurusu için bkz. [Onnx çalışma zamanı başvuru belgeleri](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Örnekler

Bkz. [nasıl yapılır kullanımı-azureml/dağıtım/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) , onnx modellerini oluşturan ve dağıtan Not defterleri.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Daha fazla bilgi

ONNX hakkında daha fazla bilgi edinin veya projeye katkıda bulunun:
+ [ONNX proje Web sitesi](https://onnx.ai)
+ [GitHub 'da ONNX kodu](https://github.com/onnx/onnx)

ONNX çalışma zamanı veya projeye katkıda bulunma hakkında daha fazla bilgi edinin:
+ [ONNX çalışma zamanı GitHub deposu](https://github.com/Microsoft/onnxruntime)


