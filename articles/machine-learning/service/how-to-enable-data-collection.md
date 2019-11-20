---
title: Üretim modellerinizde veri toplama
titleSuffix: Azure Machine Learning
description: Azure Blob depolamada Azure Machine Learning giriş modeli verilerini nasıl toplayacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 20bc148e392900aecb63ad393ec6e90cda65585a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839094"
---
# <a name="collect-data-for-models-in-production"></a>Üretimde modeller için veri toplama
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Bu SDK yakında devre dışı bırakılıyor. Bu SDK, modellerdeki veri drmasını izleyen geliştiriciler için hala uygundur, ancak çoğu Geliştirici [Application Insights ile basitleştirilmiş veri izlemeyi](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights)kullanmalıdır. 

Bu makalede, Azure Kubernetes kümesine (AKS) bir Azure Blob depolama alanına dağıttığınız Azure Machine Learning giriş modeli verilerini nasıl toplayabileceğinizi öğrenebilirsiniz. 

Bu veriler etkinleştirildikten sonra şunları yapmanıza yardımcı olur:
* Üretim verileri modelinize girdiğinde [veri Drifts izleme](how-to-monitor-data-drift.md)

* Modelinizin ne zaman yeniden eğitileceği veya iyileştirilmesine yönelik daha iyi kararlar alın

* Modelinize toplanan verilerle yeniden eğitme

## <a name="what-is-collected-and-where-does-it-go"></a>Ne toplanır ve nerede gider?

Aşağıdaki veriler toplanabilir:
* Azure Kubernetes kümesinde (AKS) dağıtılan Web hizmetlerinden model **girişi** verileri (ses, görüntüler ve **video toplanmaz)** 
  
* Üretim girişi verilerini kullanarak model tahminleri

> [!Note]
> Bu verilerdeki ön toplama veya ön hesaplamalar hizmetin bir parçası değildir.   

Çıktı bir Azure Blob 'Una kaydedilir. Veriler bir Azure Blob 'Una eklendiğinden, Analizi çalıştırmak için en sevdiğiniz aracı seçebilirsiniz. 

Blob 'daki çıkış verilerinin yolu şu sözdizimini izler:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> `0.1.0a16` öncesindeki SDK sürümlerinde `designation` bağımsız değişkeni `identifier`olarak adlandırılmıştır. Kodunuz daha önceki bir sürümle geliştirilmişse, buna uygun olarak güncelleştirmeniz gerekecektir.

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin

- Azure Machine Learning çalışma alanı, betikleri içeren yerel dizin ve Python için Azure Machine Learning SDK 'Sı yüklendi. [Geliştirme ortamını yapılandırma](how-to-configure-environment.md) belgesini kullanarak bu önkoşulları nasıl alabileceğinizi öğrenin

- Azure Kubernetes Service 'e (AKS) dağıtılacak eğitilen bir makine öğrenme modeli. Bir tane yoksa, bkz. [eğitim resmi sınıflandırma modeli](tutorial-train-models-with-aml.md) öğreticisi

- Bir Azure Kubernetes hizmet kümesi. Oluşturma ve dağıtma hakkında daha fazla bilgi için, bkz. [dağıtım ve nerede](how-to-deploy-and-where.md) belge

- [Ortamınızı ayarlama](how-to-configure-environment.md) ve [izleme SDK 'sını](https://aka.ms/aml-monitoring-sdk) kurma

## <a name="enable-data-collection"></a>Veri toplamayı etkinleştirme
Veri toplama, Azure Machine Learning veya diğer araçlarla dağıtılan modelden bağımsız olarak etkinleştirilebilir. 

Bunu etkinleştirmek için şunları yapmanız gerekir:

1. Puanlama dosyasını aç

1. [Aşağıdaki kodu](https://aka.ms/aml-monitoring-sdk) dosyanın üst kısmına ekleyin:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. `init()` işlevinizde veri toplama değişkenlerinizi bildirin:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* isteğe bağlı bir parametredir, modelinizin gerekmiyorsa ayarlamanız gerekmez. Bir Bağıntıkimliği 'ın yerinde olması, diğer verilerle daha kolay eşleme yapmanıza yardımcı olur. (Örnekler şunlardır: Krenumber, CustomerID, vb.)
    
    *Tanımlayıcı* daha sonra Blobun klasör yapısını oluşturmak için kullanılıyorsa, "işlenmemiş" verileri "işlenmiş" olarak bölmek için kullanılabilir

3.  `run(input_df)` işlevine aşağıdaki kod satırlarını ekleyin:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. AKS 'de bir hizmet dağıtırken veri **toplama otomatik olarak** **doğru** olarak ayarlanmamış, bu nedenle yapılandırma dosyanızı şu şekilde güncelleştirmeniz gerekir: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    Hizmet izleme için Appınsights, bu yapılandırma değiştirilerek de açılabilir:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Yeni bir görüntü oluşturmak ve hizmeti dağıtmak için bkz. [dağıtım ve nerede](how-to-deploy-and-where.md) belge


**Ortam dosyanıza** ve **Puanlama dosyanıza**yüklenmiş bağımlılıklara sahip bir hizmetiniz zaten varsa, veri toplamayı şu şekilde etkinleştirin:

1. [Azure Machine Learning Studio](https://ml.azure.com) 'ya git

1. Çalışma alanınızı açın

1. **Dağıtımlar** 'a git -> **Hizmet Seç** -> **Düzenle**

   ![Hizmeti Düzenle](media/how-to-enable-data-collection/EditService.PNG)

1. **Gelişmiş ayarlar**' da **model veri toplamayı etkinleştir** ' i seçin.

    [Veri toplamayı denetim ![](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Bu pencerede, hizmetinizin sistem durumunu izlemek için "Appınsights tanılamayı etkinleştir" i de seçebilirsiniz

1. Değişikliği uygulamak için **Güncelleştir** ' i seçin


## <a name="disable-data-collection"></a>Veri toplamayı devre dışı bırak
Verileri toplamayı her zaman durdurabilirsiniz. Veri toplamayı devre dışı bırakmak için Python kodu veya Azure Machine Learning Studio kullanın.

+ Seçenek 1-Azure Machine Learning Studio 'da devre dışı bırak: 
  1. [Azure Machine Learning Studio](https://ml.azure.com) 'da oturum açın

  1. Çalışma alanınızı açın

  1. **Dağıtımlar** 'a git -> **Hizmet Seç** -> **Düzenle**

     [![düzenleme seçeneği](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. **Gelişmiş ayarlar**' da **model veri toplamayı etkinleştir** ' i seçimden çıkar

     [![veri toplamayı kaldır](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Değişikliği uygulamak için **Güncelleştir** ' i seçin

  Ayrıca, bu ayarlara [Azure Machine Learning Studio](https://ml.azure.com)'daki çalışma alanınızda de erişebilirsiniz.

+ Seçenek 2-veri toplamayı devre dışı bırakmak için Python kullanın:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Verilerinizi doğrulayın ve çözümleyin
Azure bloba toplanan verileri çözümlemek için tercih ettiğiniz herhangi bir aracı seçebilirsiniz.

Blobun verilere hızlıca erişmek için:

1. [Azure Machine Learning Studio](https://ml.azure.com) 'da oturum açın

1. Çalışma alanınızı açın
1. **Depolama alanı** ' na tıklayın

    [![depolama](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Blob 'daki çıkış verilerinin yolunu şu sözdizimiyle izleyin:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Power BI aracılığıyla model verilerini çözümleme

1. [Power BI Desktop](https://www.powerbi.com) Indir ve aç

1. **Veri al** ' ı seçin ve [**Azure Blob depolama**](https://docs.microsoft.com/power-bi/desktop-data-sources) ' ya tıklayın.

    [PBı blob kurulumunu ![](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Depolama hesabınızın adını ekleyin ve depolama anahtarınızı girin. Bu bilgileri, blob **ayarlarınızda** > erişim anahtarlarına > bulabilirsiniz

1. Kapsayıcı **modeldata** ' ı seçin ve **Düzenle** ' ye tıklayın.

    [![PBı Gezgini](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Sorgu Düzenleyicisi 'nde, "ad" sütununa tıklayın ve depolama hesabınızı ekleyin 1. Filtre içindeki model yolu. Note: belirli bir yıldan veya aydan yalnızca dosyalara bakmak isterseniz, filtre yolunu genişletmeniz yeterlidir. Örneğin, yalnızca Mart verilerine göz atın:/modeldata/SubscriptionID >/resourcegroupname >/çalışmaadı >/WebServiceName >/ModelName >/modelversion >/GroupName >/Year >/3

1. **Adına**göre sizin için uygun olan verileri filtreleyin. **Tahminleri** ve **girişleri**depoladıysanız her biri için bir sorgu oluşturmanız gerekir

1. Dosyaları birleştirmek için **içerik** sütununu iki katına kaydederek çift oka tıklayın

    [![PBı Içeriği](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Tamam ' a tıklayın ve veriler önyüklenir

    [![Pbımbine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Şimdi **Kapat ve Uygula** öğesine tıklayabilirsiniz

1.  Giriş ve tahmin eklediyseniz, tablolarınız otomatik olarak **RequestId** ile ilişkilendirilecektir

1. Model verileriniz üzerinde özel raporlarınızı oluşturmaya başlayın


### <a name="analyzing-model-data-using-databricks"></a>Databricks kullanarak model verilerini çözümleme

1. [Databricks çalışma alanı](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) oluşturma

1. Databricks çalışma alanınıza gidin

1. Databricks çalışma alanınızda **verileri karşıya yükle** ' yi seçin.

    [![DB karşıya yükleme](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Yeni tablo oluşturma ve **diğer veri kaynaklarını** seçme-> Azure Blob depolama-> tablo oluşturma

    [![DB tablosu](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Verilerinizin konumunu güncelleştirin. Örnek aşağıda verilmiştir:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Verilerinizi görüntülemek ve analiz etmek için şablondaki adımları izleyin

## <a name="example-notebook"></a>Örnek Not defteri

[How-to-Use-azureml/Deployment/Enable-Data-Collection-for-models-in-aks/Enable-Data-Collection-for-models-in-aks. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) Not defteri, bu makaledeki kavramları gösterir.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
