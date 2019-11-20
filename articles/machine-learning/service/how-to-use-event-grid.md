---
title: Olay odaklı makine öğrenimi iş akışları oluşturma
titleSuffix: Azure Machine Learning
description: Olay odaklı çözümleri etkinleştirmek için Azure Machine Learning ile Event Grid 'i nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 11/04/2019
ms.openlocfilehash: b10b848918fda7a1f271a4e617b1706971f103d7
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622297"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Olay odaklı makine öğrenimi iş akışları oluşturma (Önizleme)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) Azure Machine Learning hizmeti olaylarını destekler. Örneğin, çalışma alanı için kapsamlı çalıştırma, model kaydı, model dağıtımı ve veri DRI algılamada olayları kullanabilirsiniz.

Daha fazla bilgi için bkz. [Event Grid ile Azure Machine Learning tümleştirme](concept-event-grid-integration.md) ve [Azure Machine Learning Event Grid şeması](/azure/event-grid/event-schema-machine-learning).

Gibi yaygın senaryoları etkinleştirmek için Event Grid kullanın:

* Yeniden eğitim için işlem hatlarını tetikleme
* Azure Machine Learning ile çeşitli uç noktalara olay akışı

## <a name="prerequisites"></a>Ön koşullar

* Azure Machine Learning hizmeti çalışma alanına katkıda bulunan veya sahip erişimi, için olay oluşturacaksınız.
* Web kancası veya Olay Hub 'ı gibi bir olay işleyici uç noktası seçin. Daha fazla bilgi için bkz. [olay işleyicileri](https://docs.microsoft.com/azure/event-grid/event-handlers). 

## <a name="register-resource-providers"></a>Kaynak sağlayıcılarını kaydetme

1 2019 Kasım 'Dan önce Azure Event Grid veya Machine Learning kullandıysanız, bu belgedeki adımları izlemeden önce kaynak sağlayıcılarını yeniden kaydetmeniz gerekebilir. Sağlayıcıları yeniden kaydetmek için aşağıdaki adımları kullanın:

1. Azure portal gidin ve __abonelikler__' i seçin. Birlikte çalışmak istediğiniz aboneliği seçin.
1. __Kaynak sağlayıcıları__' nı seçin ve ardından __eventgrid__' i arayın.
1. __Microsoft. EventGrid__ girişini seçin ve sonra __yeniden kaydet__' i seçin.

    ![yeniden kaydet-kaynak-sağlayıcı](media/how-to-use-event-grid/re-register-resource-provider.png)

1. __Machinelearningservices__araması yapın, girişi seçin ve sonra __yeniden kaydet__' i seçin.

> [!TIP]
> Bu adımları tamamlamaya yönelik izinleriniz yoksa, abonelik yöneticinizden bunları gerçekleştirmesini isteyin.

## <a name="configure-machine-learning-events-using-the-azure-portal"></a>Azure portal kullanarak makine öğrenimi olaylarını yapılandırma

1. [Azure Portal](https://portal.azure.com) açın ve Azure Machine Learning çalışma alanınıza gidin.

1. Sol çubuktan __Olaylar__ ' ı seçin ve **olay abonelikleri**' ni seçin. 

    ![Select-Events-in-Workspace. png](media/how-to-use-event-grid/select-event.png)

1. Kullanmak istediğiniz olay türünü seçin. Örneğin, aşağıdaki ekran görüntüsünde __kayıtlı model__, __dağıtılan model__, __Tamamlanan çalıştırma__ve __veri kümesi DRI algılandı__:

    ![Add-Event-Type](media/how-to-use-event-grid/add-event-type.png)

1. Olayın yayımlanacağı uç noktayı seçin. Aşağıdaki ekran görüntüsünde, __Olay Hub__ 'ı seçili uç noktadır:

    ![Select-Event-Handler](media/how-to-use-event-grid/select-event-handler.png)

Seçiminizi onayladıktan sonra __Oluştur__' a tıklayın. Yapılandırma sonrasında bu olaylar uç noktanıza gönderilir.

## <a name="set-up-azure-event-grid-using-cli"></a>CLı kullanarak Azure Event Grid ayarlama

En son [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)'yı yükleyebilir veya Azure aboneliğinizin bir parçası olarak sağlanmış Azure Cloud Shell kullanabilirsiniz.

Event Grid uzantısını yüklemek için, CLı 'dan aşağıdaki komutu kullanın:

```azurecli-interactive
az add extension --name eventgrid
```

Aşağıdaki örnek, bir Azure aboneliğinin nasıl seçeceğinizi ve ardından Azure Machine Learning için yeni bir olay aboneliği nasıl oluşturulacağını gösterir:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="sample-scenarios"></a>Örnek senaryolar

### <a name="use-a-logic-app-to-send-email-alerts"></a>E-posta uyarıları göndermek için bir mantıksal uygulama kullanma

Tüm olaylarınızın e-postalarını yapılandırmak için [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) yararlanın. Koşullarla özelleştirin ve birlikte çalışan takımlar genelinde işbirliğini ve tanımayı etkinleştirmek için alıcıları belirtin.

1. Azure portal, Azure Machine Learning çalışma alanınıza gidin ve sol çubukta olaylar sekmesini seçin. Buradan __Logic Apps__' i seçin. 

    ![Select-Logic-AP](media/how-to-use-event-grid/select-logic-ap.png)

1. Mantıksal uygulama kullanıcı arabiriminde oturum açın ve konu türü olarak Machine Learning hizmet ' ı seçin. 

    ![Select-topic-Type](media/how-to-use-event-grid/select-topic-type.png)

1. Hangi olayların bildirileceğini seçin. Örneğin, aşağıdaki ekran görüntüsü __RunCompleted__.

    ![Select-Event-runtamamlanmıştır](media/how-to-use-event-grid/select-event-runcomplete.png)

1. Yalnızca bir olay türleri alt kümesinde Logic App 'i tetiklemek için filtre de ekleyebilirsiniz. Aşağıdaki ekran görüntüsünde, __/Datadriftıd/Runs/__ adlı __önek filtresi__ kullanılır.

    ![Filtre olayları](media/how-to-use-event-grid/filtering-events.png)

1. Sonra, bu olayı tüketmek ve e-posta aramak için bir adım ekleyin. Olayları almak için kullanabileceğiniz birkaç farklı posta hesabı vardır. Ayrıca, e-posta uyarısına ne zaman gönderileceğini yapılandırabilirsiniz.

    ![Seç-e-posta-eylem](media/how-to-use-event-grid/select-email-action.png)

1. __E-posta gönder__ ' i seçin ve parametreleri girin. Konu başlığında olayları filtrelemeye yardımcı olmak için __olay türünü__ ve __konuyu__ dahil edebilirsiniz. İleti gövdesinde çalıştırmalar için çalışma alanı sayfasına bir bağlantı da ekleyebilirsiniz. 

    ![Yapılandır-e-posta-gövde](media/how-to-use-event-grid/configure-email-body.png)

1. Bu eylemi kaydetmek için sayfanın sol köşesindeki **farklı kaydet** ' i seçin. Görüntülenen sağ çubuktan bu eylemin oluşturulmasını onaylayın.

    ![Onayla-Logic-App-oluştur](media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Veri kayması gerçekleştiğinde yeniden eğitim iş akışlarını tetiklemek için bir mantıksal uygulama kullanın

Modeller zaman içinde eskiyor ve üzerinde çalıştığı bağlamda yararlı olarak kalmaz. Modelin yeniden eğitilmesi için zaman olup olmadığını söylemek için bir yol, veri drıt 'i algılamaktır. 

Bu örnek, yeniden eğitim tetiklenmesi için bir Azure Logic App ile Event Grid 'in nasıl kullanılacağını gösterir. Örnek, bir modelin eğitimi ile veri kümeleri sunan bir Azure Data Factory işlem hattını tetikler.

Başlamadan önce aşağıdaki eylemleri gerçekleştirin:

* Bir çalışma alanında veri yük kümesini [algılamak]( https://aka.ms/datadrift) için bir veri kümesi izleyicisini ayarlama
* Yayımlanmış bir [Azure Data Factory işlem hattı](https://docs.microsoft.com/azure/data-factory/)oluşturun.

Bu örnekte, bir blob deposuna dosya kopyalamak ve yayımlanmış bir Machine Learning işlem hattı çalıştırmak için basit bir Data Factory işlem hattı kullanılır. Bu senaryo hakkında daha fazla bilgi için bkz. Machine Learning bir adım ayarlama [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![ADF-mlpipeline-aşama](media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Mantıksal uygulama oluşturma ile başlayın. [Azure Portal](https://portal.azure.com)gidin, Logic Apps araması yapın ve Oluştur ' u seçin.

    ![arama mantığı-uygulama](media/how-to-use-event-grid/search-for-logic-app.png)

1. İstenen bilgileri girin. Deneyimi basitleştirmek için, Azure Data Factory işlem hattı ve Azure Machine Learning çalışma alanı olarak aynı abonelik ve kaynak grubunu kullanın.

    ![set-up-Logic-App-for-ADF](media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Mantıksal uygulamayı oluşturduktan sonra __bir Event Grid kaynak olayı gerçekleştiğinde__öğesini seçin. 

    ![Select-Event-Grid-Trigger](media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Oturum açın ve etkinliğin ayrıntılarını girin. __Kaynak adını__ çalışma alanı adına ayarlayın. __Olay türünü__ __Datasetdriftalgılandı__olarak ayarlayın.

    ![oturum açma ve ekleme olayı](media/how-to-use-event-grid/login-and-add-event.png)

1. Yeni bir adım ekleyin ve __Azure Data Factory__arayın. İşlem __hattı çalıştırması oluştur__' u seçin. 

    ![oluşturma-adfpipeline-Çalıştır](media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Oturum açın ve çalıştırmak için yayımlanmış Azure Data Factory işlem hattını belirtin.

    ![-ADF-işlem hattı belirt](media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Sayfanın sol üst kısmındaki **Kaydet** düğmesini kullanarak mantıksal uygulamayı kaydedin ve oluşturun. Uygulamanızı görüntülemek için [Azure Portal](https://portal.azure.com) çalışma alanınıza gidin ve **Olaylar**' a tıklayın.

    ![Logic-App-Web kancasını göster](media/how-to-use-event-grid/show-logic-app-webhook.png)

Artık, Veri Fabrikası işlem hattı, DRT gerçekleştiğinde tetiklenir. [Yeni çalışma alanı portalındaki](https://ml.azure.com)veri Drın çalışma ve makine öğrenimi ardışık düzeni ayrıntılarını görüntüleyin. 

![çalışma alanını görüntüle](media/how-to-use-event-grid/view-in-workspace.png)


### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Azure Işlevleri 'ni kullanarak bir modeli etiketlere göre dağıtma

Azure Machine Learning model nesnesi model adı, sürüm, etiket ve özellik gibi bir Özet dağıtımı yaptığınız parametreler içerir. Model kayıt olayı bir uç nokta tetikleyebilir ve bu parametrelerin değerine göre bir modeli dağıtmak için bir Azure Işlevi kullanabilirsiniz.

Bir örnek için [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) deposuna bakın ve **Benioku** dosyasındaki adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* Kullanılabilir olaylar hakkında daha fazla bilgi edinmek için bkz. [Azure Machine Learning olay şeması](/azure/event-grid/event-schema-machine-learning)