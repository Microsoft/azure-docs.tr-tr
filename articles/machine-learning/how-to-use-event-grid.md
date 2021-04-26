---
title: ML iş akışlarında olayları tetikleme (Önizleme)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 'de olay odaklı uygulamalar, süreçler veya CI/CD makine öğrenimi iş akışlarını ayarlayın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 09a541e784167251e0833f6fd3f6130a450e07d8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478854"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Uygulama, işlem veya CI/CD iş akışlarını Azure Machine Learning olaylara göre tetikleyin (Önizleme)

Bu makalede, [Azure Event Grid](../event-grid/index.yml)tarafından bazı koşullar algılandığında hata bildirimi e-POSTALARı veya ml işlem hattı çalıştırmaları gibi Azure Machine Learning olaylara dayalı olay odaklı uygulamaları, süreçler veya CI/CD iş akışlarını ayarlamayı öğreneceksiniz.

Azure Machine Learning, model eğitimi, model dağıtımı ve izleme dahil olmak üzere makine öğrenimi işlemi yaşam döngüsünün tamamını yönetir. Eğitim çalışmalarının tamamlanması, modellerin kaydı ve dağıtımı ve modern sunucusuz mimariler kullanılarak veri kayması algılaması gibi Azure Machine Learning olaylara yanıt vermek için Event Grid kullanabilirsiniz. Daha sonra çalışma durumu değişikliği, çalıştırma tamamlama, model kaydı, model dağıtımı ve bir çalışma alanı içinde veri yük algılama gibi olaylara abone olabilir ve bunları kullanabilirsiniz.

Olay odaklı eylemler için Event Grid ne zaman kullanılır:
* Çalıştırma hatası ve çalıştırma tamamlandığında e-posta gönder
* Model kaydedildikten sonra bir Azure işlevi kullanma
* Azure Machine Learning ile çeşitli uç noktalara olay akışı
* Değişikliklerini algılandığında ml işlem hattı tetikleyin

## <a name="prerequisites"></a>Önkoşullar
Event Grid kullanmak için, olaylarını oluşturacağınız Azure Machine Learning çalışma alanına katkıda bulunan veya Owner erişiminizin olması gerekir.

## <a name="the-event-model--types"></a>Olay modeli & türleri

Azure Event Grid, Azure Machine Learning ve diğer Azure hizmetleri gibi kaynaklardaki olayları okur. Bu olaylar daha sonra Azure Event Hubs, Azure Işlevleri, Logic Apps ve diğerleri gibi olay işleyicilerine gönderilir. Aşağıdaki diyagramda kaynakları ve işleyicileri Event Grid nasıl bağlandığı, ancak desteklenen tümleştirmelerin kapsamlı bir listesi olmadığı gösterilmiştir.

![Azure Event Grid işlevsel modeli](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Olay kaynakları ve olay işleyicileri hakkında daha fazla bilgi için bkz. [Event Grid nedir?](../event-grid/overview.md).

### <a name="event-types-for-azure-machine-learning"></a>Azure Machine Learning için olay türleri

Azure Machine Learning, çeşitli makine öğrenimi yaşam noktalarında Olaylar sağlar: 

| Olay türü | Açıklama |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Machine Learning deneme çalıştırması tamamlandığında tetiklenir |
| `Microsoft.MachineLearningServices.ModelRegistered` | Bir makine öğrenimi modeli çalışma alanına kaydedildiğinde tetiklenir |
| `Microsoft.MachineLearningServices.ModelDeployed` | Bir veya daha fazla modelle bir çıkarım hizmeti dağıtımı tamamlandığında tetiklenir |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | İki veri kümesi için bir veri drara algılama işi tamamlandığında tetiklenir |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Çalışma durumu değiştirildiğinde, şu anda yalnızca bir çalıştırma durumu ' Failed ' olduğunda tetiklenir |

### <a name="filter--subscribe-to-events"></a>Olaylara abone olmak & filtrele

Bu olaylar Azure Event Grid aracılığıyla yayımlanır. Azure portal, PowerShell veya Azure CLı kullanarak, müşteriler [bir veya daha fazla olay türünü ve filtreleme koşullarını belirterek](../event-grid/event-filtering.md)olaylara kolayca abone olabilir. 

Olaylarınızı ayarlarken yalnızca belirli olay verilerini tetiklemek için filtre uygulayabilirsiniz. Aşağıdaki örnekte, çalışma durumu değişti olayları için, çalıştırma türlerine göre filtreleyebilirsiniz. Olay yalnızca ölçüt karşılandığında tetiklenir. Filtreleyebileceğiniz olay verileri hakkında bilgi edinmek için [Azure Machine Learning Event Grid şemasına](../event-grid/event-schema-machine-learning.md) bakın. 

Azure Machine Learning olaylar için abonelikler Azure rol tabanlı erişim denetimi (Azure RBAC) tarafından korunur. Yalnızca bir çalışma alanının [katılımcısı veya sahibi](how-to-assign-roles.md#default-roles) olay abonelikleri oluşturabilir, güncelleştirebilir ve silebilir.  Filtre, olay aboneliklerinin [oluşturulması](/cli/azure/eventgrid/event-subscription) sırasında veya daha sonraki bir zamanda olay aboneliklerine uygulanabilir. 


1. Azure portal gidin, yeni bir abonelik veya mevcut bir abonelik seçin. 

1. Filtreler sekmesini seçin ve gelişmiş filtreler ' e kaydırın. **Anahtar** ve **değer** için, filtrelemek istediğiniz özellik türlerini belirtin. Burada, olayın yalnızca çalıştırma türü bir işlem hattı çalıştırması veya işlem hattı adımı çalıştırıldığında tetikleneceğini görebilirsiniz.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="Olayları Filtrele":::


+ **Olay türüne göre filtrele:** Bir olay aboneliği, bir veya daha fazla Azure Machine Learning olay türü belirtebilir.

+ **Olay konusuna göre filtrele:** Azure Event Grid, __ile başlayan__ ve eşleştirmelerle __biten__ konu filtrelerini destekler, böylece eşleşen bir konuya sahip olaylar aboneye teslim edilir. Farklı makine öğrenimi olaylarının farklı konu formatı vardır.

  | Olay türü | Konu biçimi | Örnek konu |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Gelişmiş filtreleme**: Azure Event Grid yayımlanan olay şemasına göre gelişmiş filtrelemeyi de destekler. Azure Machine Learning olay şeması ayrıntıları, [Azure Machine Learning için Azure Event Grid olay şeması](../event-grid/event-schema-machine-learning.md)' nda bulunabilir.  Gerçekleştirebileceğiniz bazı örnek Gelişmiş Filtreler şunları içerir:

  For `Microsoft.MachineLearningServices.ModelRegistered` olayı, modelin etiket değerini filtrelemek için:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Filtrelerin nasıl uygulanacağı hakkında daha fazla bilgi edinmek için bkz. [Event Grid olayları filtreleme](../event-grid/how-to-filter-events.md).

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

## <a name="set-up-in-azure-portal"></a>Azure portal ayarla

1. [Azure Portal](https://portal.azure.com) açın ve Azure Machine Learning çalışma alanınıza gidin.

1. Sol çubuktan __Olaylar__ ' ı seçin ve **olay abonelikleri**' ni seçin. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Kullanmak istediğiniz olay türünü seçin. Örneğin, aşağıdaki ekran görüntüsünde __kayıtlı model__, __dağıtılan model__, __Tamamlanan çalıştırma__ ve __veri kümesi DRI algılandı__:

    ![Add-Event-Type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Olayın yayımlanacağı uç noktayı seçin. Aşağıdaki ekran görüntüsünde, __Olay Hub__ 'ı seçili uç noktadır:

    ![Ekran görüntüsü, select Event hub açık olan olay aboneliği oluştur bölmesini gösterir.](./media/how-to-use-event-grid/select-event-handler.png)

Seçiminizi onayladıktan sonra __Oluştur__' a tıklayın. Yapılandırma sonrasında bu olaylar uç noktanıza gönderilir.


### <a name="set-up-with-the-cli"></a>CLı ile ayarlama

En son [Azure CLI](/cli/azure/install-azure-cli)'yı yükleyebilir veya Azure aboneliğinizin bir parçası olarak sağlanmış Azure Cloud Shell kullanabilirsiniz.

Event Grid uzantısını yüklemek için, CLı 'dan aşağıdaki komutu kullanın:

```azurecli-interactive
az add extension --name eventgrid
```

Aşağıdaki örnek, bir Azure aboneliğini nasıl seçeceğinizi ve Azure Machine Learning için yeni bir olay aboneliği nasıl oluşturduğunu göstermektedir:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace. This example uses EventHub as a destination. 
az eventgrid event-subscription create --name {eventGridFilterName} \
  --source-resource-id /subscriptions/{subId}/resourceGroups/{RG}/providers/Microsoft.MachineLearningServices/workspaces/{wsName} \
  --endpoint-type eventhub \
  --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.EventHub/namespaces/n1/eventhubs/EH1 \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>Örnekler

### <a name="example-send-email-alerts"></a>Örnek: e-posta uyarıları gönder

Tüm olaylarınızın e-postalarını yapılandırmak için [Azure Logic Apps](../logic-apps/index.yml) kullanın. Koşullarla özelleştirin ve birlikte çalışan takımlar genelinde işbirliğini ve tanımayı etkinleştirmek için alıcıları belirtin.

1. Azure portal, Azure Machine Learning çalışma alanınıza gidin ve sol çubukta olaylar sekmesini seçin. Buradan __Logic Apps__' i seçin. 

    ![Ekran görüntüsünde Logic Apps bir Machine Learning çalışma alanı olayları sayfası gösterilir.](./media/how-to-use-event-grid/select-logic-ap.png)

1. Mantıksal uygulama kullanıcı arabiriminde oturum açın ve konu türü olarak Machine Learning hizmet ' ı seçin. 

    ![Ekran görüntüsü, kaynak türü olarak makine öğrenimi seçiliyken bir kaynak olayı gerçekleştiğinde iletişim kutusu gösterir.](./media/how-to-use-event-grid/select-topic-type.png)

1. Hangi olayların bildirileceğini seçin. Örneğin, aşağıdaki ekran görüntüsü __RunCompleted__.

    ![Ekran görüntüsü, bir olay türü seçili olan bir kaynak olay gerçekleştiğinde iletişim kutusu gösterir.](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Yukarıdaki bölümde filtreleme yöntemini kullanabilir veya yalnızca bir olay türleri alt kümesinde mantıksal uygulamayı tetiklemek için filtre ekleyebilirsiniz. Aşağıdaki ekran görüntüsünde, __/Datadriftıd/Runs/__ adlı __önek filtresi__ kullanılır.

    ![Filtre olayları](./media/how-to-use-event-grid/filtering-events.png)

1. Sonra, bu olayı tüketmek ve e-posta aramak için bir adım ekleyin. Olayları almak için kullanabileceğiniz birkaç farklı posta hesabı vardır. Ayrıca, e-posta uyarısına ne zaman gönderileceğini yapılandırabilirsiniz.

    ![Ekran görüntüsü, arama satırına girilen e-posta ile eylem Seç iletişim kutusunu gösterir.](./media/how-to-use-event-grid/select-email-action.png)

1. __E-posta gönder__ ' i seçin ve parametreleri girin. Konu başlığında olayları filtrelemeye yardımcı olmak için __olay türünü__ ve __konuyu__ dahil edebilirsiniz. İleti gövdesinde çalıştırmalar için çalışma alanı sayfasına bir bağlantı da ekleyebilirsiniz. 

    ![Ekran görüntüsü, konu satırına sağdaki konu satırına ve olay türüne eklenen bir e-posta Gönder iletişim kutusunu gösterir.](./media/how-to-use-event-grid/configure-email-body.png)

1. Bu eylemi kaydetmek için sayfanın sol köşesindeki **farklı kaydet** ' i seçin. Görüntülenen sağ çubuktan bu eylemin oluşturulmasını onaylayın.

    ![Ekran görüntüsünde Logic Apps tasarımcısında farklı Kaydet ve oluştur düğmeleri gösterilir.](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Örnek: veri drmaları yeniden eğitim tetikler

Modeller zaman içinde eskiyor ve üzerinde çalıştığı bağlamda yararlı olarak kalmaz. Modelin yeniden eğitilmesi için zaman olup olmadığını söylemek için bir yol, veri drıt 'i algılamaktır. 

Bu örnek, yeniden eğitim tetiklenmesi için bir Azure Logic App ile Event Grid 'in nasıl kullanılacağını gösterir. Örnek, bir modelin eğitimi ile veri kümeleri sunan bir Azure Data Factory işlem hattını tetikler.

Başlamadan önce aşağıdaki eylemleri gerçekleştirin:

* Bir çalışma alanında veri yük kümesini [algılamak](how-to-monitor-datasets.md) için bir veri kümesi izleyicisini ayarlama
* Yayımlanmış bir [Azure Data Factory işlem hattı](../data-factory/index.yml)oluşturun.

Bu örnekte, bir blob deposuna dosya kopyalamak ve yayımlanmış bir Machine Learning işlem hattı çalıştırmak için basit bir Data Factory işlem hattı kullanılır. Bu senaryo hakkında daha fazla bilgi için bkz. Machine Learning bir adım ayarlama [Azure Data Factory](../data-factory/transform-data-machine-learning-service.md)

![Ekran görüntüsünde, Copy Veri1 besleme M L Execute Pipeline1 ile fabrika kaynaklarındaki eğitim işlem hattı gösterilmektedir.](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Mantıksal uygulama oluşturma ile başlayın. [Azure Portal](https://portal.azure.com)gidin, Logic Apps araması yapın ve Oluştur ' u seçin.

    ![arama mantığı-uygulama](./media/how-to-use-event-grid/search-for-logic-app.png)

1. İstenen bilgileri girin. Deneyimi basitleştirmek için, Azure Data Factory işlem hattı ve Azure Machine Learning çalışma alanı olarak aynı abonelik ve kaynak grubunu kullanın.

    ![Ekran görüntüsü mantıksal uygulama oluştur bölmesini gösterir.](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Mantıksal uygulamayı oluşturduktan sonra __bir Event Grid kaynak olayı gerçekleştiğinde__ öğesini seçin. 

    ![Ekran görüntüsü, bir Event Grid Kaynak olayının gerçekleştiği zaman dahil olmak üzere ortak bir tetikleyici seçenekleriyle başlayan Logic Apps tasarımcısını gösterir.](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Oturum açın ve etkinliğin ayrıntılarını girin. __Kaynak adını__ çalışma alanı adına ayarlayın. __Olay türünü__ __Datasetdriftalgılandı__ olarak ayarlayın.

    ![Ekran görüntüsünde, bir olay türü öğesi seçiliyken bir kaynak olayının ne zaman gerçekleştiği gösterilir.](./media/how-to-use-event-grid/login-and-add-event.png)

1. Yeni bir adım ekleyin ve __Azure Data Factory__ arayın. İşlem __hattı çalıştırması oluştur__' u seçin. 

    ![Ekran görüntüsü, işlem hattı oluştur çalıştırması seçiliyken Eylem Seç bölmesini gösterir.](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Oturum açın ve çalıştırmak için yayımlanmış Azure Data Factory işlem hattını belirtin.

    ![Ekran görüntüsü çeşitli değerlerle işlem hattı çalıştırması oluştur bölmesini gösterir.](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Sayfanın sol üst kısmındaki **Kaydet** düğmesini kullanarak mantıksal uygulamayı kaydedin ve oluşturun. Uygulamanızı görüntülemek için [Azure Portal](https://portal.azure.com) çalışma alanınıza gidin ve **Olaylar**' a tıklayın.

    ![Ekran görüntüsü mantıksal uygulamanın vurgulandığı olayları gösterir.](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Artık, Veri Fabrikası işlem hattı, DRT gerçekleştiğinde tetiklenir. [Yeni çalışma alanı portalındaki](https://ml.azure.com)veri Drın çalışma ve makine öğrenimi ardışık düzeni ayrıntılarını görüntüleyin. 

![Ekran görüntüsü, ardışık düzen uç noktalarını gösterir.](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Örnek: etiketleri temel alan bir model dağıtın

Azure Machine Learning model nesnesi model adı, sürüm, etiket ve özellik gibi bir Özet dağıtımı yaptığınız parametreler içerir. Model kayıt olayı bir uç nokta tetikleyebilir ve bu parametrelerin değerine göre bir modeli dağıtmak için bir Azure Işlevi kullanabilirsiniz.

Bir örnek için, depoya bakın [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) ve **Benioku** dosyasındaki adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Event Grid hakkında daha fazla bilgi edinin ve Azure Machine Learning olaylarına bir deneme verin:

- [Event Grid Hakkında](../event-grid/overview.md)

- [Azure Machine Learning için olay şeması](../event-grid/event-schema-machine-learning.md)
