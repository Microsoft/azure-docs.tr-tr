---
title: Azure Machine Learning olaylarını tüketme
titleSuffix: Azure Machine Learning
description: Bu makalede, Azure Machine Learning tarafından oluşturulan olaylara abone olma, tepki verme ve aboneliği kaldırma Azure Event Grid nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: 2fe2d07b29b8799712d59cdf21aeb3ce989ca3b2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158465"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Azure Machine Learning olaylarını tüketme (Önizleme)

Azure Machine Learning, model eğitimi, model dağıtımı ve izleme dahil olmak üzere makine öğrenimi işlemi yaşam döngüsünün tamamını yönetir. Azure Machine Learning olaylar, uygulamaların makine öğrenimi yaşam döngüsü sırasında, eğitim çalıştırmaları tamamlandığında, modellerin kaydı ve dağıtımı ve modern sunucusuz kullanarak veri kayması algılama gibi olaylara tepki vermesini sağlar mimarileri. 

Bu olaylar [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)aracılığıyla yayımlanır. Azure portal, PowerShell veya Azure CLı kullanarak, müşteriler [bir veya daha fazla olay türünü ve filtreleme koşullarını belirterek](/azure/event-grid/event-filtering)olaylara kolayca abone olabilir. Müşteriler ayrıca Azure Işlevleri, Azure Logic Apps veya genel Web kancaları gibi çok çeşitli olay işleyicileri oluşturmayı tercih etiyor. Azure Machine Learning, Azure Event Grid birlikte, olay odaklı uygulamalar oluşturmanız için yüksek düzeyde kullanılabilir, güvenilir ve hataya dayanıklı bir olay teslim platformu sağlar.

Event Grid ile Azure Machine Learning kullanma hakkında daha fazla bilgi için bkz. [olay odaklı Machine Learning Iş akışları oluşturma (Önizleme)](how-to-use-event-grid.md).

## <a name="the-event-model"></a>Olay modeli 

Azure Event Grid, Azure Machine Learning ve diğer Azure hizmetleri gibi kaynaklardaki olayları okur. Bu olaylar daha sonra Azure Event Hubs, Azure Işlevleri, Logic Apps ve diğerleri gibi olay işleyicilerine gönderilir. Aşağıdaki diyagramda kaynakları ve işleyicileri Event Grid nasıl bağlandığı, ancak desteklenen tümleştirmelerin kapsamlı bir listesi olmadığı gösterilmiştir.

![Azure Event Grid işlevsel modeli](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Olay kaynakları ve olay işleyicileri hakkında daha fazla bilgi için bkz. [Event Grid nedir?](/azure/event-grid/overview).

## <a name="azure-machine-learning-event-types"></a>Azure Machine Learning olay türleri

Azure Machine Learning, çeşitli makine öğrenimi yaşam noktalarında Olaylar sağlar: 

| Olay türü | Açıklama |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Machine Learning deneme çalıştırması tamamlandığında tetiklenir |
| `Microsoft.MachineLearningServices.ModelRegistered` | Bir makine öğrenimi modeli çalışma alanına kaydedildiğinde tetiklenir |
| `Microsoft.MachineLearningServices.ModelDeployed` | Bir veya daha fazla modelle bir çıkarım hizmeti dağıtımı tamamlandığında tetiklenir |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | İki veri kümesi için bir veri drara algılama işi tamamlandığında tetiklenir |

## <a name="subscribe-to-machine-learning-events"></a>Machine Learning olaylarına abone olma

Azure Machine Learning olaylar için abonelikler rol tabanlı erişim denetimi (RBAC) tarafından korunur. Yalnızca bir çalışma alanının [katılımcısı veya sahibi](how-to-assign-roles.md#default-roles) olay abonelikleri oluşturabilir, güncelleştirebilir ve silebilir.

Olay abonelikleri, çeşitli koşullara göre filtrelenebilir. Filtre, olay aboneliklerinin [oluşturulması](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) sırasında veya [daha sonraki bir zamanda](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)olay aboneliklerine uygulanabilir. 

### <a name="filter-by-event-type"></a>Olay türüne göre filtrele
Bir olay aboneliği, bir veya daha fazla Azure Machine Learning olay türü belirtebilir.

### <a name="filter-by-event-subject"></a>Olay konusuna göre filtrele
Azure Event Grid, __ile başlayan__ ve eşleştirmelerle __biten__ konu filtrelerini destekler, böylece eşleşen bir konuya sahip olaylar aboneye teslim edilir. Farklı makine öğrenimi olaylarının farklı konu formatı vardır.

| Olay türü | Konu biçimi | Örnek konu |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |

### <a name="advanced-filtering"></a>Gelişmiş filtreleme

Azure Event Grid, yayımlanan olay şemasına göre gelişmiş filtrelemeyi de destekler. Azure Machine Learning olay şeması ayrıntıları, [Azure Machine Learning için Azure Event Grid olay şeması](../../event-grid/event-schema-machine-learning.md)' nda bulunabilir.

Gerçekleştirebileceğiniz bazı örnek Gelişmiş Filtreler şunları içerir:

* `Microsoft.MachineLearningServices.ModelRegistered` olay için modelin etiket değerini filtrelemek için:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Filtrelerin nasıl uygulanacağı hakkında daha fazla bilgi edinmek için bkz. [Event Grid olayları filtreleme](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Machine Learning olaylarını tüketme

Machine Learning olaylarını işleyen uygulamalar, önerilen birkaç uygulamayı izlemelidir:

> [!div class="checklist"]
> * Birden çok abonelik olayları aynı olay işleyicisine yönlendirmek üzere yapılandırılabildiğiniz için, olayların belirli bir kaynaktan olduğunu varsaymamak önemlidir, ancak beklediğiniz makine öğrenimi çalışma alanından geldiğinden emin olmak için iletinin konusunu kontrol edin.
> * Benzer şekilde, eventType için hazırlanmakta olan bir olay olduğunu ve aldığınız tüm olayların istediğiniz tür olacağını kabul edin.
> * İletiler sıraya alınır ve bir gecikmeden sonra, nesneler hakkındaki bilgilerinizin güncel olup olmadığını anlamak için ETag alanlarını kullanın.  Ayrıca, belirli bir nesne üzerindeki olayların sırasını anlamak için sıralayıcı alanlarını kullanın.
> * Anladığınızı alanları yoksayın. Bu uygulama, gelecekte eklenebilecek yeni özelliklere dayanıklı tutmaya yardımcı olur.
> * İşlem başarısız veya iptal edildi Azure Machine Learning işlemler bir olayı tetiklemez. Örneğin, bir model dağıtımı başarısız olursa Microsoft. MachineLearningServices. Modeldağıtılan tetiklenmez. Uygulamalarınızı tasarlarken bu hata modunu göz önünde bulundurun. Bir işlemin durumunu denetlemek ve ayrıntılı hata nedenlerini anlamak için Azure Machine Learning SDK, CLı veya Portal 'ı her zaman kullanabilirsiniz.

Azure Event Grid, müşterilerin Azure Machine Learning olayları tetiklenebilecek, birlikte bulunan ileti işleyicileri oluşturmasına izin verir. İleti işleyicilerinin bazı önemli örnekleri şunlardır:
* Azure İşlevleri
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory işlem hattı
* Azure platformunda veya başka bir yerde barındırılabilen genel Web kancaları

## <a name="next-steps"></a>Sonraki adımlar

Event Grid hakkında daha fazla bilgi edinin ve Azure Machine Learning olaylarına bir deneme verin:

- [Event Grid Hakkında](../../event-grid/overview.md)
- [Azure Machine Learning için Azure Event Grid olay şeması](../../event-grid/event-schema-machine-learning.md)
- [Azure Machine Learning olay odaklı iş akışları oluşturma](how-to-use-event-grid.md)
