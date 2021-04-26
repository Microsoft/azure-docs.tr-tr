---
title: 'ONNX: yüksek performans, platformlar arası çıkarımı'
titleSuffix: Azure Machine Learning
description: Open sinir Network Exchange (ONNX) kullanmanın, makine öğrenimi modelinizin çıkarımını en üst düzeye getirmenize nasıl yardımcı olabileceğini öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: prasantp
author: prasanthpul
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: 5a1ed6c43e4d3a96e0e19d7be50e1e16bcab780e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98071247"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX ve Azure Machine Learning: ML modellerini oluşturma ve hızlandırma

[Open sinir Network Exchange](https://onnx.ai) (onnx) kullanmanın, makine öğrenimi modelinizin çıkarımını en üst düzeye getirmenize nasıl yardımcı olabileceğini öğrenin. Çıkarım veya model Puanlama, dağıtılan modelin tahmin için en yaygın olarak üretim verilerinde kullanıldığı aşamadır. 

Donanım yeteneklerini en iyi hale getirmek için modeli ve çıkarım kitaplığını ayarlamanız gerektiğinden, bu yana, makine öğrenimi modellerini çıkarma (veya model Puanlama) için optimize etmek zordur. Farklı türlerde platformlar (bulut/kenar, CPU/GPU vb.) üzerinde en iyi performansı almak istiyorsanız sorun son derece zor olur. çünkü her biri farklı özelliklere ve özelliklere sahiptir. Çeşitli platformlarda çalışması gereken çeşitli çerçeveler için modelleriniz varsa karmaşıklık artar. Tüm farklı çerçeve ve donanım birleşimlerini iyileştirmek çok zaman alır. Tercih ettiğiniz çerçevede bir kez eğitme ve bulutta veya kenarda her yerde çalıştırılan bir çözüm. Burada ONNX yer verilir.

Microsoft ve makine öğrenimi modellerini temsil etmek için açık bir standart olarak ONNX tarafından oluşturulan bir iş ortağı topluluğu. TensorFlow, PyTorch, SciKit-öğren, keras, Chainer, MXNet, MATLAB ve mini ml gibi [birçok çerçeve](https://onnx.ai/supported-tools) modeli, standart onnx biçimine aktarılabilir veya dönüştürülebilirler. Modeller ONNX biçiminde olduktan sonra çeşitli platformlar ve cihazlarda çalıştırılabilir.

[Onnx çalışma zamanı](https://onnxruntime.ai) , üretim için onnx modellerini dağıtmaya yönelik yüksek performanslı bir çıkarım altyapısıdır. Bulut ve kenar için en iyi duruma getirilmiştir ve Linux, Windows ve Mac üzerinde çalışmaktadır. C++ dilinde yazıldığında, çeşitli ortamlarda kullanım için C, Python, C#, Java ve JavaScript (Node.js) API 'Leri de vardır. ONNX çalışma zamanı hem DNN hem de geleneksel ML modellerini destekler ve NVIDIA GPU 'Lar, Intel işlemcilerde OpenVINO, Windows üzerinde DirectML ve daha fazlası gibi farklı donanımlarda Hızlandırıcılar ile tümleşir. ONNX çalışma zamanını kullanarak, kapsamlı üretim sınıfı iyileştirmeleri, test ve devam eden geliştirmelerden yararlanabilirsiniz.

ONNX çalışma zamanı, Bing, Office ve Azure bilişsel hizmetler gibi yüksek ölçekli Microsoft hizmetlerinde kullanılır. Performans kazançları bir dizi etkene bağlıdır, ancak bu Microsoft Hizmetleri __CPU üzerinde ortalama bir 2x performans kazancı__ gördük. Azure Machine Learning hizmetlerine ek olarak ONNX çalışma zamanı, aşağıdakiler de dahil olmak üzere Machine Learning iş yüklerini destekleyen diğer ürünlerde de çalışır:
+ Windows: çalışma zamanı Windows [Machine Learning](/windows/ai/windows-ml/) kapsamında yerleşik olarak bulunur ve yüzlerce milyonlarca cihazda çalışır. 
+ Azure SQL ürün ailesi: [Azure SQL Edge](../azure-sql-edge/onnx-overview.md) ve [Azure SQL yönetilen örneği](../azure-sql/managed-instance/machine-learning-services-overview.md)'nde veriler üzerinde yerel Puanlama çalıştırın.
+ ML.NET: [ml.NET içinde ONNX modellerini çalıştırın](/dotnet/machine-learning/tutorials/object-detection-onnx).


[![Eğitim, dönüştürücüler ve dağıtımı gösteren ONNX akış diyagramı](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX modellerini al

ONNX modellerini çeşitli yollarla edinebilirsiniz:
+ Azure Machine Learning yeni bir ONNX modeli eğitme (Bu makalenin altındaki örneklere bakın) veya [otomatik Machine Learning yeteneklerini](concept-automated-ml.md#automl--onnx) kullanarak
+ Varolan modeli başka bir biçimden ONNX 'e Dönüştür ( [öğreticilere](https://github.com/onnx/tutorials)bakın) 
+ [Onnx model Zoo](https://github.com/onnx/models) 'tan önceden eğitilen BIR onnx modeli alın
+ [Azure özel görüntü işleme Service](../cognitive-services/custom-vision-service/index.yml) 'ten özelleştirilmiş bir onnx modeli oluşturma 

Görüntü sınıflandırması, nesne algılama ve metin işleme gibi birçok model ONNX modelleri olarak temsil edilebilir. Başarıyla dönüştürülemeyen bir modelle ilgili bir sorun yaşıyorsanız, lütfen kullandığınız ilgili dönüştürücünün GitHub 'da bir sorun bildirin. Sorun giderilene kadar mevcut biçim modelinizi kullanmaya devam edebilirsiniz.

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

Modelinize çıkarmasını sağlamak için, `run` döndürülmek istediğiniz çıkış (tümünün istiyorsanız boş bırakın) ve giriş değerlerinin haritasını kullanın ve geçirin. Sonuç, çıktıların bir listesidir.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Tüm Python API başvurusu için bkz. [Onnx çalışma zamanı başvuru belgeleri](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Örnekler
Bkz. [nasıl yapılır kullanımı-azureml/dağıtım/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) for the onnx modellerini oluşturan ve dağıtan Python Not defterleri.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

Diğer dillerdeki kullanım örnekleri [Onnx çalışma zamanı GitHub](https://github.com/microsoft/onnxruntime/tree/master/samples)' da bulunabilir.

## <a name="more-info"></a>Daha fazla bilgi

**Onnx** hakkında daha fazla bilgi edinin veya projeye katkıda bulunun:
+ [ONNX proje Web sitesi](https://onnx.ai)
+ [GitHub 'da ONNX kodu](https://github.com/onnx/onnx)

**Onnx çalışma zamanı** veya projeye katkıda bulunma hakkında daha fazla bilgi edinin:
+ [ONNX çalışma zamanı proje Web sitesi](https://onnxruntime.ai)
+ [ONNX çalışma zamanı GitHub deposu](https://github.com/Microsoft/onnxruntime)