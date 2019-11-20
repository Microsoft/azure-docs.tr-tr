---
title: Azure Notebooks Azure Machine Learning kullanma
description: Azure Notebooks ile kullanabileceğiniz Azure Machine Learning yönelik örnek not defterlerine genel bakış.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 6eac5d77404c85d5481ded7e58b0cd9fab0de083
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496644"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Bir not defterinde Azure Machine Learning kullanma

Azure Notebooks, [Azure Machine Learning](/azure/machine-learning/service/)çalışmak için gerekli ortamla önceden yapılandırılmış olarak gelir. Çeşitli Machine Learning senaryolarını araştırmak için, örnek bir projeyi kolaylıkla Not defteri hesabınıza kopyalayabilirsiniz.

## <a name="clone-the-sample-into-your-account"></a>Örneği hesabınıza kopyalayın

1. [Azure Notebooks](https://notebooks.azure.com/)oturum açın.
1. Projeler panosuna gitmek için **Projelerim** ' ı seçin.
1. GitHub **deposunu karşıya** yükle açılan penceresini açmak Için **GitHub deposunu karşıya yükle** (yukarı ok) düğmesini seçin.
1. Açılan pencerede, **GitHub deposuna**`Azure/MachineLearningNotebooks` girin, proje **adında** "Azure Machine Learning" gibi proje için bir ad sağlayın, proje **kimliğinde**bir tanımlayıcı sağlayın, isterseniz **genel** ' i temizleyin, sonra **içeri aktar**' ı seçin.

    ![Azure Machine Learning Not defteri örneğini Not defteri hesabınıza aktarın](media/azureml-import-project.png)

1. Bir dakikadan veya iki kez sonra, Azure Notebooks otomatik olarak yeni projenin panosuna götürür.

## <a name="run-a-sample-notebook"></a>Örnek bir not defteri çalıştırma

1. Not defterinin yapılandırma bölümünü başlatmak için **00-Configuration. ipynb** ' yi seçin ve Azure Machine Learning çalışma alanı oluşturmak için yönergelerini izleyin.

    - Azure Notebooks gerekli Python paketlerini zaten içerdiğinden, Azure ML SDK sürümünü doğrulamak için önkoşulların adım 2 ' de kod parçacığını çalıştırmanız yeterlidir.

1. Yapılandırma tamamlandıktan sonra, her biri kendi kendine açıklayıcı olan üçüncü dört farklı örnek Not defteri içeren klasörü açmak için **01. Başlarken** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning belgeler, Not defterleri içinde Machine Learning çalışırken size rehberlik eden diğer kaynakları içerir:

- [Hızlı başlangıç: Azure Machine Learning kullanmaya başlamak için Python kullanma](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Öğretici #1: bir görüntü sınıflandırma modelini Azure Machine Learning ile eğitme](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Öğretici #2: Azure Container Instance (acı) içinde bir görüntü sınıflandırma modeli dağıtma](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Öğretici: Azure Machine Learning 'de otomatik makine öğrenimi ile sınıflandırma modeli eğitme](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Ayrıca, [Python için Azure MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)belgelerine bakın.
