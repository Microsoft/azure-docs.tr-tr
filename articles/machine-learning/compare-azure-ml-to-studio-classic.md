---
title: Azure Machine Learning-ML Studio (klasik)
description: Azure Machine Learning ML Studio 'den farklı (klasik)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: 00a2268442f94822064f86d95b14160d67e1516c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084753"
---
# <a name="how-azure-machine-learning-differs-from-ml-studio-classic"></a>Azure Machine Learning ML Studio 'den farklı (klasik)

Bu makalede, Azure Machine Learning özellikleri, özellikleri ve arabirimi ML Studio (klasik) ile karşılaştırılır. 

## <a name="about-ml-studio-classic"></a>ML Studio hakkında (klasik)
[Ml Studio (klasik)](studio/what-is-ml-studio.md) , kod yazmaya gerek kalmadan makine öğrenimi çözümlerini oluşturabileceğiniz, test ettiğiniz ve dağıtabileceğiniz, birlikte çalışan, sürükle ve bırak görsel çalışma alanıdır. Önceden oluşturulmuş ve önceden yapılandırılmış makine öğrenimi algoritmalarını ve veri işleme modüllerini, ayrıca özel bir işlem platformunu kullanır.

## <a name="about-azure-machine-learning"></a>Azure Machine Learning hakkında

[Azure Machine Learning](service/overview-what-is-azure-ml.md) , her ikisi de tasarımcı (Önizleme) **ve** ÇEŞITLI SDK 'lar ve CLI adlı bir Web arabirimi sağlayarak verileri hızlı bir şekilde hazırlayın, eğitim ve dağıtım, makine öğrenimi modellerini dağıtır. Azure Machine Learning, otomatik makine öğrenimi ve işlem hattı desteği gibi ölçek, birden çok Framework desteği, gelişmiş ML özellikleri de alır.

Azure Machine Learning tasarımcı, Studio 'ya (klasik) benzer bir sürükle ve bırak deneyimi sağlar. Ancak, Studio 'nun özel işlem platformunun (klasik) aksine, tasarımcı kendi işlem kaynaklarınızı kullanır, ölçeklenebilir ve Azure Machine Learning tamamen tümleşiktir.  

> [!TIP]
> Şu anda Machine Learning Studio (klasik) kullanan veya değerlendiren müşterilerin, sürükle ve bırak __ml modülleri ve__ ölçeklenebilirlik, sürüm denetimi ve kurumsal güvenlik sağlayan [Azure Machine Learning tasarımcı](https://docs.microsoft.com/azure/machine-learning/service/ui-concept-visual-interface) 'yı (Önizleme) denemesini sağlamak önerilir.

## <a name="comparison-azure-machine-learning-vs-ml-studio-classic"></a>Karşılaştırma: Azure Machine Learning ile ML Studio (klasik)

Hızlı bir karşılaştırma aşağıda verilmiştir.

||  Azure Machine Learning Tasarımcısı|Studio (klasik) |
|---| --- | --- |
||Tasarımcı önizlemededir, Azure Machine Learning GA 'dir|Genel olarak kullanılabilir (GA) | 
|Sürükle ve bırak arabirimi| Yes | Yes|
|Deneme| İşlem hedefi ile ölçeklendirme|Ölçek (10 GB eğitim veri limiti) | 
|Arabirim için modüller| [Birçok popüler modül](algorithm-module-reference/module-reference.md) | Sayısı |
|Eğitim işlem hedefleri| AML Işlem (GPU/CPU)|Özel işlem hedefi, yalnızca CPU|
|Inlekrime işlem hedefleri| Gerçek zamanlı çıkarım için Azure Kubernetes hizmeti <br/>Toplu çıkarım için AML Işlem|Özel Web hizmeti biçimi özelleştirilebilir değil | 
|ML işlem hattı| İşlem hattı yazma <br/> Yayınlanan işlem hattı <br/> Ardışık düzen uç noktası <br/> [ML işlem hattı hakkında daha fazla bilgi](service/concept-ml-pipelines.md)|Desteklenmiyor | 
|ML Ops| Yapılandırılabilir dağıtım, model ve işlem hattı sürümü oluşturma|Temel model yönetimi ve dağıtımı | 
|Model| Standart biçim, çeşitli eğitim işine bağlıdır|Tescilli, taşınabilir olmayan biçim.| 
|Otomatik model eğitimi|Henüz tasarımcıda değildir, ancak arabirim ve SDK 'lar aracılığıyla mümkün değildir.| Hayır | 

## <a name="get-started-with-azure-machine-learning"></a>Azure Machine Learning kullanmaya başlayın

Aşağıdaki kaynaklar Azure Machine Learning kullanmaya başlamanıza yardımcı olabilir

- [Azure Machine Learning genel bakış](service/tutorial-first-experiment-automated-ml.md) bölümünü okuyun 

- Otomatik fiyatları tahmin etmek için [ilk tasarımcı işlem hattınızı oluşturun](service/tutorial-designer-automobile-price-train-score.md) .

![Azure Machine Learning tasarımcı örneği](service/media/concept-ml-pipelines/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Sonraki adımlar

Tasarımcıda Sürükle ve bırak özelliklerine ek olarak, Azure Machine Learning başka araçlar da mevcuttur:  
  + [ML modellerini eğitmek & dağıtmak için Python not defterlerini kullanma](./service/tutorial-1st-experiment-sdk-setup.md)
  + [ML modellerini eğitme & dağıtmak için R Markdown kullanma](./service/tutorial-1st-r-experiment.md) 
  + [ML modellerini eğitmek & dağıtmak için otomatik makine öğrenimi kullanma](./service/tutorial-designer-automobile-price-train-score.md) 
  + [Makine öğrenimi CLı kullanarak bir modeli eğitme ve dağıtma](./service/tutorial-train-deploy-model-cli.md)

