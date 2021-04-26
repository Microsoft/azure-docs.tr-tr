---
title: Machine Learning Web hizmeti uç noktalarından verileri izleme ve toplama
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service (AKS) veya Azure Container Instances (acı) içindeki Web hizmeti uç noktalarına dağıtılan modellerden nasıl veri toplayacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: blackmist
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 2740a86c5ff68e851d592533b48dc8ee60d817ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98070822"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>ML web hizmet uç noktalarından verileri izleme ve toplama


Bu makalede, Azure Kubernetes Service (AKS) veya Azure Container Instances (acı) içindeki Web hizmeti uç noktalarına dağıtılan modellerden nasıl veri toplayacağınızı öğreneceksiniz. [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) kullanarak bir uç noktadan aşağıdaki verileri toplayın:
* Çıktı verileri
* Yanıtlar
* İstek ücretleri, yanıt süreleri ve hata oranları
* Bağımlılık oranları, yanıt süreleri ve hata oranları
* Özel durumlar

[Enable-App-Insights-in-Production-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) Not defteri, bu makaledeki kavramları gösterir.
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
 
## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

* Azure Machine Learning çalışma alanı, betiklerinizi içeren yerel bir dizin ve Python için Azure Machine Learning SDK yüklü. Daha fazla bilgi için bkz. [geliştirme ortamı yapılandırma](how-to-configure-environment.md).

* Eğitilen makine öğrenimi modeli. Daha fazla bilgi edinmek için bkz. [görüntü sınıflandırma modelini eğitme](tutorial-train-models-with-aml.md) öğreticisi.

<a name="python"></a>

## <a name="configure-logging-with-the-python-sdk"></a>Python SDK ile günlüğe kaydetmeyi yapılandırma

Bu bölümde, Python SDK 'sını kullanarak Application Insight Logging 'i nasıl etkinleştireceğinizi öğreneceksiniz. 

### <a name="update-a-deployed-service"></a>Dağıtılan bir hizmeti güncelleştirme

Mevcut bir Web hizmetini güncelleştirmek için aşağıdaki adımları kullanın:

1. Çalışma alanınızdaki hizmeti belirler. Değeri, `ws` çalışma alanınızın adıdır

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Hizmetinizi güncelleştirme ve Azure Application Insights etkinleştirme

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Hizmetinizdeki özel izlemeleri günlüğe kaydet

> [!IMPORTANT]
> Azure Application Insights, yalnızca 64 KB 'a kadar olan yükleri günlüğe kaydeder. Bu sınıra ulaşılırsa, yetersiz bellek gibi hatalarla karşılaşabilirsiniz veya hiçbir bilgi günlüğe kaydedilmez. Günlüğe kaydetmek istediğiniz veriler 64 KB veya daha büyükse, [üretim içindeki modeller Için veri toplama](how-to-enable-data-collection.md)bölümündeki bilgileri kullanarak BLOB depolama alanına depolamanız gerekir.
>
> Bir AKS dağıtımı içindeki model izleme gibi daha karmaşık durumlar için, [Opencensus](https://opencensus.io)gibi bir üçüncü taraf kitaplığı kullanmanızı öneririz.

Özel izlemeleri günlüğe kaydetmek için, AKS için standart dağıtım sürecini [ve nasıl dağıtılacağı ve nerede](how-to-deploy-and-where.md) belgede bir aci 'yi izleyin. Ardından, aşağıdaki adımları kullanın:

1. Çıkarımı sırasında Application Insights veri göndermek için Print deyimleri ekleyerek Puanlama dosyasını güncelleştirin. İstek verileri ve yanıt gibi daha karmaşık bilgiler için bir JSON yapısı kullanın. 

    Aşağıdaki örnek `score.py` Dosya, model başlatıldığında, çıkarım sırasında giriş ve çıkış sırasında ve hata oluştuğunda günlüğe kaydedilir.

    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. Hizmet yapılandırmasını güncelleştirin ve Application Insights etkinleştirdiğinizden emin olun.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Bir görüntü oluşturun ve AKS veya ACI üzerinde dağıtın. Daha fazla bilgi için bkz. [dağıtma ve nerede](how-to-deploy-and-where.md).


### <a name="disable-tracking-in-python"></a>Python 'da izlemeyi devre dışı bırak

Azure Application Insights 'yi devre dışı bırakmak için aşağıdaki kodu kullanın:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="configure-logging-with-azure-machine-learning-studio"></a>Azure Machine Learning Studio ile günlüğe kaydetmeyi yapılandırma

Azure Application Insights Azure Machine Learning Studio 'dan da etkinleştirebilirsiniz. Modelinizi bir Web hizmeti olarak dağıtmaya hazırsanız, Application Insights etkinleştirmek için aşağıdaki adımları kullanın:

1. Konumundaki Studio 'da oturum açın https://ml.azure.com .
1. **Modeller** ' e gidin ve dağıtmak istediğiniz modeli seçin.
1. **+ Dağıt**' ı seçin.
1. **Dağıtım modeli** formunu doldurun.
1. **Gelişmiş** menüsünü genişletin.

    ![Formu dağıt](./media/how-to-enable-app-insights/deploy-form.png)
1. **Tanılamayı ve veri toplamayı etkinleştir Application Insights** seçin.

    ![App Insights 'ı etkinleştir](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>Ölçümleri ve günlükleri görüntüleme

### <a name="query-logs-for-deployed-models"></a>Dağıtılan modeller için sorgu günlükleri

Gerçek zamanlı uç noktaların günlükleri müşteri verileri. `get_logs()`Daha önce dağıtılan bir Web hizmetinden günlükleri almak için işlevini kullanabilirsiniz. Günlükler, dağıtım sırasında oluşan hatalar hakkında ayrıntılı bilgiler içerebilir.

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Birden çok kiracıya sahipseniz, önce aşağıdaki kimlik doğrulama kodunu eklemeniz gerekebilir `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

### <a name="view-logs-in-the-studio"></a>Günlükleri Studio 'da görüntüleme

Azure Application Insights, hizmet günlüklerinizi Azure Machine Learning çalışma alanıyla aynı kaynak grubunda depolar. Studio 'yu kullanarak verilerinizi görüntülemek için aşağıdaki adımları kullanın:

1. [Studio](https://ml.azure.com/)'daki Azure Machine Learning çalışma alanınıza gidin.
1. **Uç noktaları** seçin.
1. Dağıtılan hizmeti seçin.
1. **Application Insights URL** bağlantısını seçin.

    [![Application Insights URL 'sini bul](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. Application Insights, **genel bakış** sekmesinde veya __izleme__ bölümünde __Günlükler__' i seçin.

    [![İzlemenin Genel Bakış sekmesi](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Score.py dosyasından günlüğe kaydedilen bilgileri görüntülemek için __izlemeler__ tablosuna bakın. Aşağıdaki sorgu, __giriş__ değerinin günlüğe kaydedildiği günlükleri arar:

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![izleme verileri](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Azure Application Insights kullanma hakkında daha fazla bilgi için bkz. [Application Insights nedir?](../azure-monitor/app/app-insights-overview.md).

## <a name="web-service-metadata-and-response-data"></a>Web hizmeti meta verileri ve yanıt verileri

> [!IMPORTANT]
> Azure Application Insights, yalnızca 64 KB 'a kadar olan yükleri günlüğe kaydeder. Bu sınıra ulaşıldığında, yetersiz bellek gibi hatalarla karşılaşabilirsiniz veya hiçbir bilgi günlüğe kaydedilmez.

Web hizmeti istek bilgilerini günlüğe kaydetmek için `print` Score.py dosyanıza deyimler ekleyin. Her `print` bir ifade, ileti altındaki Application Insights izleme tablosunda bir girdiye neden olur `STDOUT` . Application Insights, `print`  `customDimensions` izleme tablosunda ve içindeki bildiri çıkışlarını depolar `Contents` . JSON dizelerinin yazdırılması, altındaki izleme çıkışında hiyerarşik bir veri yapısı üretir `Contents` .

## <a name="export-data-for-retention-and-processing"></a>Bekletme ve işleme için verileri dışarı aktarma

>[!Important]
> Azure Application Insights yalnızca blob depolamaya dışarı aktarmaları destekler. Bu uygulamanın sınırları hakkında daha fazla bilgi için bkz. [Application Insights 'tan telemetri dışarı aktarma](../azure-monitor/app/export-telemetry.md#continuous-export-advanced-storage-configuration).

Verileri, bekletme ayarlarını tanımlayabileceğiniz bir BLOB depolama hesabına aktarmak için Application Insights ' [sürekli dışarı aktarma](../azure-monitor/app/export-telemetry.md) ' yı kullanın. Application Insights verileri JSON biçiminde dışarı aktarır. 

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="Sürekli dışarı aktarma":::

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Web hizmeti uç noktaları için günlük kaydını etkinleştirmeyi ve günlükleri görüntülemeyi öğrendiniz. Sonraki adımlar için bu makaleleri deneyin:


* [AKS kümesine model dağıtma](./how-to-deploy-azure-kubernetes-service.md)

* [Azure Container Instances model dağıtma](./how-to-deploy-azure-container-instance.md)

* Mlops: üretimde modellerden toplanan verileri kullanma hakkında daha fazla bilgi edinmek için [modelleri Azure Machine Learning Ile yönetin, dağıtın ve izleyin](./concept-model-management-and-deployment.md) . Bu tür veriler, Machine Learning işleminizi sürekli olarak iyileştirmenize yardımcı olabilir.