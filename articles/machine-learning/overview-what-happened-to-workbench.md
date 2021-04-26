---
title: Workbench'e ne oldu?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning, bulut ölçeğinde ML uygulamalarını modelleyerek ve dağıtmaya yönelik tümleşik bir veri bilimi çözümüdür. Çalışma ekranı özelliği kullanımdan kaldırıldı.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: larryfr
author: BlackMist
ms.date: 03/05/2020
ms.openlocfilehash: 4c680be897c4c1bf2ccf20df1d34ab6f59f559f2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816161"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench’te neler oldu?

Azure Machine Learning Workbench uygulaması ve diğer bazı erken Özellikler kullanım dışı bırakılmıştır ve geliştirilmiş bir [mimarinin](concept-azure-machine-learning-architecture.md)yolu olması için **Eylül 2018** sürümünde değiştirilmiştir.

Deneyim, deneyiminizi geliştirmek için müşteri geri bildirimi tarafından istenen birçok önemli güncelleştirme içerir. Deneme çalıştırmalarının temel işlevleri model dağıtımına göre değişmemiştir. Ancak şimdi, Machine Learning görevlerinizi ve işlem hatlarınızı gerçekleştirmek için sağlam <a href="/python/api/overview/azure/ml/intro" target="_blank">Python SDK 'sı</a>, R SDK ve [Azure CLI](reference-azure-machine-learning-cli.md) kullanabilirsiniz.

Önceki Azure Machine Learning sürümünde oluşturulan yapıtların çoğu kendi yerel veya bulut depolama ortamınızda saklanır. Bu yapıtlar hiçbir zaman kaybolmayacaktır.

Bu makalede, nelerin değiştiğini ve Azure Machine Learning Workbench ve API 'Leri ile önceden var olan çalışmanızı nasıl etkilediğini öğreneceksiniz.

>[!Warning]
>Bu makale Azure Machine Learning Studio kullanıcılarına yönelik değildir. Bu, çalışma ekranı (Önizleme) uygulamasını yükleyen ve/veya deneme ve model yönetimi önizleme hesaplarına sahip olan Azure Machine Learning müşteriler içindir.


## <a name="what-changed"></a>Ne değişti?

Azure Machine Learning en son sürümü aşağıdaki özellikleri içerir:
+ [Basitleştirilmiş bir Azure kaynakları modeli](concept-azure-machine-learning-architecture.md).
+ Denemeleri ve işlem hedeflerinizi yönetmek için [Yeni bir Portal Kullanıcı arabirimi](how-to-log-view-metrics.md) .
+ Yeni, daha kapsamlı bir Python <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK</a>.
+ Machine Learning için yeni genişletilmiş [Azure CLI uzantısı](reference-azure-machine-learning-cli.md) .

[Mimari](concept-azure-machine-learning-architecture.md) kullanım kolaylığı için yeniden tasarlanmıştır. Birden çok Azure kaynağı ve hesabı yerine, size gereken yalnızca bir [Azure Machine Learning Çalışma Alanı](concept-workspace.md)'dır. [Azure portalda](how-to-manage-workspace.md) hemen çalışma alanları oluşturabilirsiniz. Bir çalışma alanı kullanarak, birden çok Kullanıcı eğitim ve dağıtım işlem hedeflerini, model denemeleri, Docker görüntülerini, dağıtılan modelleri ve benzerlerini depolayabilirler.

Geçerli sürümde geliştirilmiş yeni CLı ve SDK istemcileri olsa da masaüstü çalışma ekranı uygulamasının kendisi kullanımdan kaldırılmıştır. Denemeleri, [Azure Machine Learning Studio 'daki çalışma alanı panosunda](how-to-log-view-metrics.md#view-the-experiment-in-the-web-portal)yönetilebilir. Deneme geçmişinizi almak, çalışma alanınıza bağlı işlem hedeflerini yönetmek, modellerinizi ve Docker görüntülerinizi yönetmek, hatta web hizmetlerini dağıtmak için panoyu kullanın.

<a name="timeline"></a>

## <a name="support-timeline"></a>Destek zaman çizelgesi

9 Ocak 'ta, Machine Learning çalışma zamanı, Azure Machine Learning Denemesi ve Model Yönetimi hesapları için 2019 desteği ve bunlarla ilişkili SDK ve CLı bitti.

En son tüm yetenekler, bu <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK</a>, [CLI](reference-azure-machine-learning-cli.md)ve [Portal](how-to-manage-workspace.md)kullanılarak kullanılabilir.

## <a name="what-about-run-histories"></a>Çalıştırma geçmişleri ne olacak?

Daha eski çalışma geçmişlerini artık erişilebilir değildir ve en son sürümde çalıştırmalarını nasıl görebilirsiniz.

Çalışma geçmişi artık **denemeleri** olarak adlandırılır. Modelinizin denemeleri toplayıp SDK, CLı veya Azure Machine Learning Studio 'yu kullanarak bunları keşfedebilirsiniz.

Portalın çalışma alanı panosu yalnızca Microsoft Edge, Chrome ve Firefox tarayıcılarında desteklenir:

[![Çevrimiçi portal](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Modellerinizi eğitmek ve yeni CLı ve SDK kullanarak çalıştırma geçmişlerini izlemeye başlayın. Öğreticiden nasıl eğitim alabileceğinizi öğrenebilirsiniz [: modelleri Azure Machine Learning ile eğitme](tutorial-train-models-with-aml.md).

## <a name="will-projects-persist"></a>Projeler kalacak mı?

Hiçbir kodu veya çalışmayı kaybetmeyeceksiniz. Eski sürümde projeler yerel dizini olan bulut varlıklarıydı. En son sürümde, yerel bir yapılandırma dosyası kullanarak Azure Machine Learning çalışma alanına yerel dizinler iliştirerek. [En son mimarinin diyagramını](concept-azure-machine-learning-architecture.md)görüntüleyin.

Proje içeriğinin büyük bölümü zaten yerel makinenizde. Bu nedenle, çalışma alanınıza bağlanmak için bu dizinde bir yapılandırma dosyası oluşturmanız ve kodunuzda buna başvurmanız yeterlidir. Dosyalarınızı ve betikleri içeren yerel dizini kullanmaya devam etmek için, [' denemeler. Gönder '](/python/api/azureml-core/azureml.core.experiment.experiment) Python komutunda veya CLI komutunu kullanarak dizinin adını belirtin `az ml project attach` .  Örnek:
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

  + [Kendi ortamınızı kullanın](tutorial-1st-experiment-sdk-setup-local.md)
  + [Python not defterlerini kullanma](tutorial-1st-experiment-sdk-setup.md)
  + [R Markdown kullan](https://github.com/Azure/azureml-sdk-for-r) 
  + [Otomatik makine öğrenimi kullan](tutorial-designer-automobile-price-train-score.md) 
  + [Tasarımcı 'nın sürükleme & bırakma özelliklerini kullanın](tutorial-first-experiment-automated-ml.md) 
  + [CLı için ML uzantısını kullanma](tutorial-train-deploy-model-cli.md)
