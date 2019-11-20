---
title: Workbench'e ne oldu?
titleSuffix: Azure Machine Learning
description: Çalışma ekranı uygulaması olmadan ve destek zaman çizelgesinin ne olduğu hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 11/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1ff70a8a794cbc4c201512a0cf578eb0a890a5e4
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904413"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench’te neler oldu?

Azure Machine Learning Workbench uygulaması ve diğer bazı erken Özellikler kullanım dışı bırakılmıştır ve geliştirilmiş bir [mimarinin](concept-azure-machine-learning-architecture.md)yolu olması için **Eylül 2018** sürümünde değiştirilmiştir.

Deneyim, deneyiminizi geliştirmek için müşteri geri bildirimi tarafından istenen birçok önemli güncelleştirme içerir. Deneme çalıştırmalarının temel işlevleri model dağıtımına göre değişmemiştir. Ancak şimdi, Machine Learning görevlerinizi ve işlem hatlarınızı gerçekleştirmek için sağlam <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK 'sı</a>, R SDK ve [Azure CLI](reference-azure-machine-learning-cli.md) kullanabilirsiniz.

Önceki Azure Machine Learning sürümünde oluşturulan yapıtların çoğu kendi yerel veya bulut depolama ortamınızda saklanır. Bu yapıtlar hiçbir zaman kaybolmayacaktır.

Bu makalede, nelerin değiştiğini ve Azure Machine Learning Workbench ve API 'Leri ile önceden var olan çalışmanızı nasıl etkilediğini öğreneceksiniz.

>[!Warning]
>Bu makale Azure Machine Learning Studio kullanıcılarına yönelik değildir. Bu, çalışma ekranı (Önizleme) uygulamasını yükleyen ve/veya deneme ve model yönetimi önizleme hesaplarına sahip olan Azure Machine Learning müşteriler içindir.


## <a name="what-changed"></a>Ne değişti?

Azure Machine Learning en son sürümü aşağıdaki özellikleri içerir:
+ [Basitleştirilmiş bir Azure kaynakları modeli](concept-azure-machine-learning-architecture.md).
+ Denemeleri ve işlem hedeflerinizi yönetmek için [Yeni bir Portal Kullanıcı arabirimi](how-to-track-experiments.md) .
+ Yeni, daha kapsamlı bir Python <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>.
+ Machine Learning için yeni genişletilmiş [Azure CLI uzantısı](reference-azure-machine-learning-cli.md) .

[Mimari](concept-azure-machine-learning-architecture.md) kullanım kolaylığı için yeniden tasarlanmıştır. Birden çok Azure kaynağı ve hesabı yerine, size gereken yalnızca bir [Azure Machine Learning Çalışma Alanı](concept-workspace.md)'dır. [Azure portalda](how-to-manage-workspace.md) hemen çalışma alanları oluşturabilirsiniz. Bir çalışma alanı kullanarak, birden çok Kullanıcı eğitim ve dağıtım işlem hedeflerini, model denemeleri, Docker görüntülerini, dağıtılan modelleri ve benzerlerini depolayabilirler.

Geçerli sürümde geliştirilmiş yeni CLı ve SDK istemcileri olsa da masaüstü çalışma ekranı uygulamasının kendisi kullanımdan kaldırılmıştır. Denemeleri, [Azure Machine Learning Studio 'daki çalışma alanı panosunda](how-to-track-experiments.md#view-the-experiment-in-the-web-portal)yönetilebilir. Deneme geçmişinizi almak, çalışma alanınıza bağlı işlem hedeflerini yönetmek, modellerinizi ve Docker görüntülerinizi yönetmek, hatta web hizmetlerini dağıtmak için panoyu kullanın.

<a name="timeline"></a>

## <a name="support-timeline"></a>Destek zaman çizelgesi

9 Ocak 'ta, Machine Learning çalışma ekranı, Azure Machine Learning Denemesi ve Model Yönetimi hesapları için 2019 desteği ve bunlarla ilişkili SDK ve CLı sona erdi.

En son tüm yetenekler, bu <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>, [CLI](reference-azure-machine-learning-cli.md)ve [Portal](how-to-manage-workspace.md)kullanılarak kullanılabilir.

## <a name="what-about-run-histories"></a>Çalıştırma geçmişleri ne olacak?

Daha eski çalışma geçmişlerini artık erişilebilir değildir ve en son sürümde çalıştırmalarını nasıl görebilirsiniz.

Çalışma geçmişi artık **denemeleri**olarak adlandırılır. Modelinizin denemeleri toplayıp SDK, CLı veya Azure Machine Learning Studio 'yu kullanarak bunları keşfedebilirsiniz.

Portalın çalışma alanı panosu yalnızca Microsoft Edge, Chrome ve Firefox tarayıcılarında desteklenir:

[Çevrimiçi ![portalı](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Modellerinizi eğitmek ve yeni CLı ve SDK kullanarak çalıştırma geçmişlerini izlemeye başlayın. Öğreticiden nasıl eğitim alabileceğinizi öğrenebilirsiniz [: modelleri Azure Machine Learning ile eğitme](tutorial-train-models-with-aml.md).

## <a name="will-projects-persist"></a>Projeler kalacak mı?

Hiçbir kodu veya çalışmayı kaybetmeyeceksiniz. Eski sürümde projeler yerel dizini olan bulut varlıklarıydı. En son sürümde, yerel bir yapılandırma dosyası kullanarak Azure Machine Learning çalışma alanına yerel dizinler iliştirerek. [En son mimarinin diyagramını](concept-azure-machine-learning-architecture.md)görüntüleyin.

Proje içeriğinin büyük bölümü zaten yerel makinenizde. Bu nedenle, çalışma alanınıza bağlanmak için bu dizinde bir yapılandırma dosyası oluşturmanız ve kodunuzda buna başvurmanız yeterlidir. Dosyalarınızı ve betikleri içeren yerel dizini kullanmaya devam etmek için, [' denemeler. Gönder '](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python komutunda veya `az ml project attach` CLI komutunu kullanarak dizinin adını belirtin.  Örneğin:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

Başlamak için [bir çalışma alanı oluşturun](how-to-manage-workspace.md) .

## <a name="what-about-my-registered-models-and-images"></a>Kayıtlı modellerim ve görüntülerim hakkında ne olacak?

Eski model kayıt defterinizde kaydettiğiniz modeller, kullanmaya devam etmek istiyorsanız yeni çalışma alanınıza geçirilmesi gerekir. Modellerinizi geçirmek için modelleri indirin ve yeni çalışma alanınıza yeniden kaydedin.

Eski görüntü kayıt defterinizde oluşturduğunuz görüntüler yeni çalışma alanına doğrudan geçirilemez. Çoğu durumda, model, görüntü oluşturmaya gerek kalmadan dağıtılabilir. Gerekirse, yeni çalışma alanında model için bir görüntü oluşturabilirsiniz. Daha fazla bilgi için bkz. [makine öğrenimi modellerini yönetme, kaydetme, dağıtma ve izleme](concept-model-management-and-deployment.md).

## <a name="what-about-deployed-web-services"></a>Dağıtılan web hizmetlerine ne oldu?

Artık eski CLı desteği sona ermiştir, artık modelleri yeniden dağıtırsınız veya ilk olarak Model Yönetimi hesabınızla dağıttığınız Web hizmetlerini yönetebilirsiniz. Ancak, Azure Container Service (ACS) hala desteklenene kadar bu Web Hizmetleri çalışmaya devam edecektir.

En son sürümde, modeller Azure Container Instances (ACI) veya Azure Kubernetes Service (AKS) kümelerine Web Hizmetleri olarak dağıtılır. Ayrıca, FPGAs ve Azure IoT Edge için de dağıtabilirsiniz.

Bu makalelerde daha fazla bilgi edinin:
+ [Modellerin nasıl ve nasıl dağıtılacağı](how-to-deploy-and-where.md)
+ [Öğretici: Azure Machine Learning modelleri dağıtma](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Sonraki adımlar

[Azure Machine Learning için en son mimari](concept-azure-machine-learning-architecture.md)hakkında bilgi edinin.

Hizmete genel bakış için [Azure Machine Learning nedir?](overview-what-is-azure-ml.md)makalesini okuyun.

Tercih ettiğiniz yöntemle ilk denemenizi oluşturun:
  + [Python not defterlerini kullanma](tutorial-1st-experiment-sdk-setup.md)
  + [R Markdown kullan]( tutorial-1st-r-experiment.md) 
  + [Otomatik makine öğrenimi kullan](ui-tutorial-automobile-price-train-score.md) 
  + [Tasarımcı 'nın sürükleme & bırakma özelliklerini kullanın](tutorial-first-experiment-automated-ml.md) 
  + [CLı için ML uzantısını kullanma](tutorial-train-deploy-model-cli.md)
