---
title: Üretim modellerinizde veri toplama
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service (AKS) kümesine dağıtılan Azure Machine Learning modelden veri toplamayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 07/14/2020
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.openlocfilehash: f42a2f9e606eaa0475f2e35c479e99545ecf8193
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521909"
---
# <a name="collect-data-from-models-in-production"></a>Üretim içindeki modellerden veri toplama

Bu makalede, bir Azure Kubernetes Service (AKS) kümesine dağıtılan bir Azure Machine Learning modelinden nasıl veri toplanacağı gösterilmektedir. Toplanan veriler daha sonra Azure Blob depolama alanında depolanır.

Koleksiyon etkinleştirildikten sonra, topladığınız veriler size yardımcı olur:

* Topladığınız üretim verilerinde [veri Drifts izleyin](how-to-monitor-datasets.md) .

* [Power BI](#powerbi) veya [Azure Databricks](#databricks) kullanarak toplanan verileri analiz etme

* Modelinizin ne zaman yeniden eğitileceği veya iyileştirilmesine yönelik daha iyi kararlar alın.

* Toplanan verilerle modelinize yeniden eğitme.

## <a name="what-is-collected-and-where-it-goes"></a>Ne toplanır ve nerede gidiyor

Aşağıdaki veriler toplanabilir:

* AKS kümesinde dağıtılan Web hizmetlerinden model girişi verileri. Ses ses, görüntü ve *video toplanmaz.*
  
* Üretim girişi verilerini kullanarak model tahminleri.

>[!NOTE]
> Bu verilerdeki ön kimlik doğrulama ve önceden hesaplamaları şu anda koleksiyon hizmeti 'nin bir parçası değil.

Çıktı, BLOB depolama alanına kaydedilir. Veriler blob depolamaya eklendiğinden, Analizi çalıştırmak için en sevdiğiniz aracı seçebilirsiniz.

Blob 'daki çıkış verilerinin yolu şu sözdizimini izler:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> Python için Azure Machine Learning SDK 'nın 0.1.0 A16 sürümünden önceki sürümlerinde `designation` bağımsız değişken olarak adlandırılır `identifier` . Kodunuzu daha önceki bir sürümle geliştirdiyseniz, uygun şekilde güncelleştirmeniz gerekir.

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://aka.ms/AMLFree) oluşturun.

- Azure Machine Learning çalışma alanı, betiklerinizi içeren bir yerel dizin ve Python için Azure Machine Learning SDK yüklü olmalıdır. Bunları nasıl yükleyeceğiniz hakkında bilgi edinmek için bkz. [geliştirme ortamı yapılandırma](how-to-configure-environment.md).

- AKS 'e dağıtılacak eğitilen makine öğrenimi modelinin olması gerekir. Modeliniz yoksa, [görüntü sınıflandırma modeline eğit](tutorial-train-models-with-aml.md) öğreticiye bakın.

- Bir AKS kümeniz olması gerekir. Oluşturma ve dağıtma hakkında daha fazla bilgi için bkz. [nasıl dağıtılır ve nerede](how-to-deploy-and-where.md).

- [Ortamınızı ayarlayın](how-to-configure-environment.md) ve [Azure Machine Learning izleme SDK 'sını](/python/api/overview/azure/ml/install)kurun.

## <a name="enable-data-collection"></a>Veri toplamayı etkinleştirme

Azure Machine Learning veya diğer araçlarla dağıttığınız modelden bağımsız olarak [veri toplamayı](/python/api/azureml-monitoring/azureml.monitoring.modeldatacollector.modeldatacollector) etkinleştirebilirsiniz.

Veri toplamayı etkinleştirmek için şunları yapmanız gerekir:

1. Puanlama dosyasını açın.

1. Aşağıdaki kodu dosyanın üst kısmına ekleyin:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. İşlevinizdeki veri toplama değişkenlerinizi bildirin `init` :

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3", "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* isteğe bağlı bir parametredir. Modeliniz gerekmiyorsa kullanmanız gerekmez. *CorrelationId* kullanımı, *Krete numarası* veya *CustomerID* gibi diğer verilerle daha kolay bir şekilde eşleşmenizin sağlanmasına yardımcı olur.
    
    *Tanımlayıcı* parametresi daha sonra Blobun klasör yapısını oluşturmak için kullanılır. Ham verileri işlenen verilerden ayırt etmek için kullanabilirsiniz.

1. Aşağıdaki kod satırlarını `run(input_df)` işleve ekleyin:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. AKS 'de bir hizmet dağıttığınızda veri *toplama otomatik olarak* **doğru** olarak ayarlanamaz. Yapılandırma dosyanızı aşağıdaki örnekte olduğu gibi güncelleştirin:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Ayrıca, bu yapılandırmayı değiştirerek hizmet izleme için Application Insights etkinleştirebilirsiniz:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Yeni bir görüntü oluşturmak ve Machine Learning modelini dağıtmak için bkz. [dağıtım ve nerede](how-to-deploy-and-where.md).

1. ' Azure-Monitoring ' PIP paketini Web hizmeti ortamının Conda-Dependencies öğesine ekleyin:
  ```Python
    env = Environment('webserviceenv')
    env.python.conda_dependencies = CondaDependencies.create(conda_packages=['numpy'],pip_packages=['azureml-defaults','azureml-monitoring','inference-schema[numpy-support]'])
  ```


## <a name="disable-data-collection"></a>Veri toplamayı devre dışı bırakma

İstediğiniz zaman veri toplamayı durdurabilirsiniz. Veri toplamayı devre dışı bırakmak için Python kodu kullanın.

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Verilerinizi doğrulayın ve çözümleyin

Blob depolamadaki toplanan verileri analiz etmek için tercih ettiğiniz bir aracı seçebilirsiniz.

### <a name="quickly-access-your-blob-data"></a>Blob verilerinize hızlıca erişin

1. [Azure Portal](https://portal.azure.com)oturum açın.

1. Çalışma alanınızı açın.

1. **Depolama**’yı seçin.

    [![Depolama seçeneğini belirleyin](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Bu söz dizimi ile Blobun çıkış verilerinin yolunu izleyin:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a><a id="powerbi"></a> Power BI kullanarak model verilerini analiz etme

1. [Power BI Desktop](https://www.powerbi.com)indirip açın.

1. **Veri al** ' ı seçin ve [**Azure Blob depolama**](/power-bi/desktop-data-sources)' yı seçin.

    [![Power BI blob kurulumu](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Depolama hesabınızın adını ekleyin ve depolama anahtarınızı girin. Bu bilgileri, Blobun **Ayarlar**  >  **erişim anahtarları** ' nı seçerek bulabilirsiniz.

1. **Model veri** kapsayıcısını seçin ve **Düzenle**' yi seçin.

    [![Power BI Gezgini](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Sorgu Düzenleyicisi 'nde **ad** sütununun altına tıklayın ve depolama hesabınızı ekleyin.

1. Model yolunuzu filtreye girin. Yalnızca belirli bir yıla veya aya ait dosyalara bakmak isterseniz, filtre yolunu genişletmeniz yeterlidir. Örneğin, yalnızca Mart verilerine bakmak için şu filtre yolunu kullanın:

   /modeldata/ \<subscriptionid> / \<resourcegroupname> / \<workspacename> / \<webservicename> / \<modelname> / \<modelversion> / \<designation> / \<year> /3

1. **Ad** değerlerine göre sizin için uygun olan verileri filtreleyin. Tahminleri ve girişleri depoladıysanız, her biri için bir sorgu oluşturmanız gerekir.

1. Dosyaları birleştirmek için **içerik** sütun başlığının yanındaki aşağı çift okları seçin.

    [![Power BI Içerik](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. **Tamam**’ı seçin. Veriler önceden yüklenir.

    [![Dosyaları birleştirme Power BI](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. **Kapat ve Uygula '** yı seçin.

1. Giriş ve tahmin eklediyseniz, tablolarınız otomatik olarak **RequestId** değerlerine göre sıralanır.

1. Model verileriniz üzerinde özel raporlarınızı oluşturmaya başlayın.

### <a name="analyze-model-data-using-azure-databricks"></a><a id="databricks"></a> Azure Databricks kullanarak model verilerini analiz etme

1. [Azure Databricks çalışma alanı](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)oluşturun.

1. Databricks çalışma alanınıza gidin.

1. Databricks çalışma alanınızda **verileri karşıya yükle**' yi seçin.

    [![Databricks veri yükleme seçeneğini belirleme](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. **Yeni tablo oluştur** ' u seçin ve **diğer veri kaynakları**' nı seçin  >  **Azure Blob Storage**  >  **tablo oluştur Not defteri**.

    [![Databricks tablosu oluşturma](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Verilerinizin konumunu güncelleştirin. Aşağıda bir örnek verilmiştir:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks kurulumu](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Verilerinizi görüntülemek ve analiz etmek için şablondaki adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Topladığınız veriler üzerindeki [verileri algılar](how-to-monitor-datasets.md) .