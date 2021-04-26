---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: ff64a0948402ff152e45bd4702d986f51b9547aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563202"
---
Aşağıdaki tablo ML Studio (klasik) ve Azure Machine Learning arasındaki önemli farklılıkları özetler.

| Özellik | ML Studio (klasik) | Azure Machine Learning |
|---| --- | --- |
| Sürükle ve bırak arabirimi | Klasik deneyim | Güncelleştirilmiş deneyim- [Azure Machine Learning Tasarımcısı](../articles/machine-learning/concept-designer.md)| 
| Kod SDK 'Ları | Desteklenmeyen | [Azure Machine Learning Python](/python/api/overview/azure/ml/) ve [R](https://github.com/Azure/azureml-sdk-for-r) SDK 'leriyle tamamen tümleşik |
| Deneme | Ölçeklenebilir (10 GB eğitim veri limiti) | İşlem hedefi ile ölçeklendirme |
| Eğitim işlem hedefleri | Özel işlem hedefi, yalnızca CPU desteği | Geniş kapsamlı özelleştirilebilir [eğitim işlem hedefleri](../articles/machine-learning/concept-compute-target.md#train). GPU ve CPU desteği içerir | 
| Dağıtım işlem hedefleri | Özel Web hizmeti biçimi özelleştirilebilir değil | Geniş kapsamlı özelleştirilebilir [dağıtım işlem hedefleri](../articles/machine-learning/concept-compute-target.md#deploy). GPU ve CPU desteği içerir |
| ML işlem hattı | Desteklenmez | İş akışlarını otomatikleştirmek için esnek, modüler işlem [hatları](../articles/machine-learning/concept-ml-pipelines.md) oluşturun |
| MLOps | Temel model yönetimi ve dağıtımı; Yalnızca CPU dağıtımları | Varlık sürümü oluşturma (model, veri, iş akışları), iş akışı Otomasyonu, CICD araçları ile tümleştirme, CPU ve GPU dağıtımları [ve daha fazlası](../articles/machine-learning/concept-model-management-and-deployment.md) |
| Model biçimi | Özel biçim, yalnızca Studio (klasik) | Eğitim işi türüne bağlı olarak desteklenen birden çok biçim |
| Otomatik model eğitimi ve hyperparameter ayarlaması |  Desteklenmez | [Desteklenir](../articles/machine-learning/concept-automated-ml.md). Code-First ve Code No Options. | 
| Veri drması algılama | Desteklenmez | [Desteklenir](../articles/machine-learning/how-to-monitor-datasets.md) |
| Veri etiketleme projeleri | Desteklenmez | [Desteklenir](../articles/machine-learning/how-to-create-labeling-projects.md) |
| Rol Tabanlı Erişim Denetimi (RBAC) | Yalnızca katkıda bulunan ve sahip rolü | [Esnek rol tanımı ve RBAC denetimi](../articles/machine-learning/how-to-assign-roles.md) |
| AI Galerisi | Desteklenen ( [https://gallery.azure.ai/](https://gallery.azure.ai/) ) | Desteklenmeyen <br><br> [Örnek Python SDK Not defterleri](https://github.com/Azure/MachineLearningNotebooks)ile öğrenin. |