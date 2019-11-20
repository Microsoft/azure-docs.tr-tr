---
title: AKS dağıtımlarında veri drları algılama
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 'de Azure Kubernetes Service tarafından dağıtılan modellerdeki veri kayması 'nı (Önizleme) algıla.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: bf82714011754ba516fa38444b1019b9cc1aa732
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111871"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) ' e dağıtılan modellerdeki veri kayması 'nı (Önizleme) Algıla
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Bu makalede, dağıtılan bir modelin eğitim veri kümesi ve çıkarım verileri arasında veri kayması izlemeyi öğreneceksiniz. Makine öğrenimi bağlamında eğitilen makine öğrenimi modelleri, Drın nedeniyle düşürülmüş tahmin performansına neden olabilir. Azure Machine Learning, veri drınızı izleyebilir ve hizmet, Drın algılandığında size bir e-posta uyarısı gönderebilir.

## <a name="what-is-data-drift"></a>Veri kayması nedir?

Machine Learning bağlamında, veri dolu modeli, performans düşüşünü modellemeyi sağlayan model girişi verilerinde değişiklik gösterir. Model doğruluğunun zamana göre düşürmesinin en önemli nedenlerinden biridir, böylece izleme verileri, model performans sorunlarını algılamaya yardımcı olur. 

## <a name="what-can-i-monitor"></a>Neleri izleyebilirim?

Azure Machine Learning, AKS üzerinde dağıtılan bir modelin girişlerini izleyebilir ve bu verileri modelin eğitim veri kümesiyle karşılaştırabilirsiniz. Düzenli aralıklarla, çıkarım verileri [anlık görüntü ve profillendirilir ve](how-to-explore-prepare-data.md)ardından temel veri kümesine göre hesaplanır. 

+ , DRFT katsayısı olarak adlandırılan veri kayması miktarını ölçer.
+ Veri DRIP katkısını özelliklerine göre ölçer ve hangi özelliklerin veri üzerinde olduğunu bilgilendirin.
+ Mesafe ölçümlerini ölçer. Şu anda Wasserstein ve enerji mesafesi hesaplanır.
+ Özelliklerin dağıtımlarını ölçer. Şu anda Çekirdek yoğunluğu tahmini ve histogramları.
+ Uyarıları e-posta ile veri kayması ile gönderin.

> [!Note]
> Bu hizmet (Önizleme) ve yapılandırma seçeneklerinde sınırlıdır. Ayrıntılar ve güncelleştirmeler için lütfen [API belgelerimize](https://docs.microsoft.com/python/api/azureml-datadrift/) ve [Sürüm notlarımıza](azure-machine-learning-release-notes.md) bakın. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Azure Machine Learning ' de verileri Drın nasıl izlenir

Azure Machine Learning kullanarak, veri kümeleri veri kümeleri veya dağıtımlar aracılığıyla izlenir. Bir taban çizgisi veri kümesi (genellikle bir model için eğitim veri kümesi) için veri kayması izlemek üzere belirtilir. İkinci bir veri kümesi-genellikle bir dağıtımdan toplanan model giriş verileri, taban çizgisi veri kümesine göre test edilir. Her iki veri kümesi de veri Drın izleme hizmetine profil oluşturulur ve giriş yapılır. Bir makine öğrenimi modeli, iki veri kümesi arasındaki farkları tespit etmek için eğitilir. Modelin performansı, iki veri kümesi arasındaki drifit 'in boyutunu ölçen DRFT katna dönüştürülür. [Model yorumlenebilirliğini](how-to-machine-learning-interpretability.md)kullanarak, değişikliklerini katlarına katkıda bulunan özellikler hesaplanır. Veri kümesi profilinden her bir özellik hakkındaki istatistiksel bilgiler izlenir. 

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bir tane yoksa, başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.

- Python için Azure Machine Learning SDK 'Sı yüklendi. Şunları yapmak için [Azure MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 'daki yönergeleri kullanın:

    - Miniconda ortamı oluşturma
    - Python için Azure Machine Learning SDK 'sını yükler

- [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md).

- Bir çalışma alanı [yapılandırma dosyası](how-to-configure-environment.md#workspace).

- Aşağıdaki komutu kullanarak verileri DRFT SDK 'yi yükler:

    ```shell
    pip install azureml-datadrift
    ```

- Modelinizin eğitim verilerinden bir [veri kümesi](how-to-create-register-datasets.md) oluşturun.

- Modeli [kaydederken](concept-model-management-and-deployment.md) eğitim veri kümesini belirtin. Aşağıdaki örnek, eğitim veri kümesini belirtmek için `datasets` parametresini kullanmayı gösterir:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- Modelin AKS dağıtımından veri toplamak için [model veri toplamayı etkinleştirin](how-to-enable-data-collection.md) ve verilerin `modeldata` blob kapsayıcısında toplandığını onaylayın.

## <a name="configure-data-drift"></a>Veri drbir yapılandırma
Denemeniz için veri kayması yapılandırmak üzere aşağıdaki Python örneğinde görüldüğü gibi bağımlılıkları içeri aktarın. 

Bu örnekte [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) nesnesinin yapılandırılması gösterilmektedir:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Datadriftalgılayıcısı çalıştırması gönder

`DataDriftDetector` nesne yapılandırıldığında, model için verilen bir tarih üzerinde bir [veri DRI çalışması](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) gönderebilirsiniz. Çalıştırmanın bir parçası olarak `drift_threshold` parametresini ayarlayarak Datadriftalgılayıcısı uyarılarını etkinleştirin. [Datadrift_coefficient](#visualize-drift-metrics) verilen `drift_threshold`üstündeyse, bir e-posta gönderilir.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Değişikliklerini ölçümlerini görselleştirin

<a name="metrics"></a>

Datadriftdetektinizi gönderdikten sonra, bir veri DRI görevi için her bir çalıştırma yinelemesinde kaydedilen DRFT ölçümlerini görebilirsiniz:


|Ölçüm|Açıklama|
--|--|
wasserstein_distance|Tek boyutlu sayısal dağıtım için tanımlanan istatistiksel uzaklık.|
energy_distance|Tek boyutlu sayısal dağıtım için tanımlanan istatistiksel uzaklık.|
datadrift_coefficient|Benzer şekilde, Matthew 'ın bağıntı katsayısı olarak hesaplanır, ancak bu çıktı, 0 ile 1 arasında bir gerçek sayıdır. Değişikliklerini bağlamında 0, değişikliklerini ve 1 ' in maksimum değişikliklerini olduğunu gösterir.|
datadrift_contribution|Drift 'e katkıda bulunan özelliklerin özellik önemi.|

DRFT ölçümlerini görüntülemenin birden çok yolu vardır:

* `RunDetails`[Jupyıter pencere öğesini](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)kullanın.
* Herhangi bir `datadrift` Run nesnesi üzerinde [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) işlevini kullanın.
* [Azure Machine Learning Studio](https://ml.azure.com)'daki çalışma alanınızın **modeller** bölümünden ölçümleri görüntüleyin.

Aşağıdaki Python örneği, ilgili veri kayması ölçümlerinin nasıl çizeceğinizi gösterir. Döndürülen ölçümleri özel görselleştirmeler oluşturmak için kullanabilirsiniz:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Bkz. Azure Machine Learning tarafından algılanan veri kayması](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Veri drmaları zamanlama 

Veri drime algılamasını etkinleştirdiğinizde, belirtilen ve zamanlanan sıklıkta bir Datadriftalgılayıcısı çalıştırılır. Datadrift_coefficient verilen `drift_threshold`ulaşırsa, zamanlanan her çalıştırma ile bir e-posta gönderilir. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Veri dritörü yapılandırması, [Azure Machine Learning Studio](https://ml.azure.com)'daki çalışma alanınızdaki **Ayrıntılar** sekmesinde **modeller** altında görülebilir.

[![Azure Machine Learning Studio veri kayması](media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Azure Machine Learning Studio 'daki sonuçları görüntüleme

[Azure Machine Learning Studio](https://ml.azure.com)'da çalışma alanınızdaki sonuçları görüntülemek için model sayfasına gidin. Modelin Ayrıntılar sekmesinde, veri DRI yapılandırması gösterilir. Veri **Drın bir sekmesi** artık veri yük ölçümlerinin görselleştirilmesi için kullanılabilir. 

[![Azure Machine Learning Studio veri kayması](media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>DRFT uyarıları alma

Değişikliklerini katsayısını uyarma eşiğini ayarlayıp bir e-posta adresi sağladıktan sonra, DRX katsayısı eşiğin üstünde olduğunda bir [Azure izleyici](https://docs.microsoft.com/azure/azure-monitor/overview) e-posta uyarısı otomatik olarak gönderilir. 

Özel uyarıları ve eylemleri ayarlamanıza olanak sağlamak için tüm veri DRIP ölçümleri, Azure Machine Learning çalışma alanıyla birlikte oluşturulan [Application Insights](how-to-enable-app-insights.md) kaynağında depolanır. E-posta uyarısında Application Insights sorgusuna bağlantıyı izleyebilirsiniz.

![Veri Dre-posta uyarısı](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Modelinizi drsonra yeniden eğitme

Veri kayması dağıtılan modelinizin performansını olumsuz yönde etkilediğinde, modelin yeniden eğitilmesi zaman alabilir. Bunu yapmak için, aşağıdaki adımlarla devam edin.

* Toplanan verileri araştırın ve yeni modeli eğmek için verileri hazırlayın.
* Eğitim ve test verilerine ayırın.
* Yeni verileri kullanarak modeli yeniden eğitme.
* Yeni oluşturulan modelin performansını değerlendirin.
* Performans, üretim modelinden daha iyi ise yeni model dağıtın.

## <a name="next-steps"></a>Sonraki adımlar

* Veri drkullanımı hakkında tam bir örnek için bkz. [Azure ML Data değişikliklerini Not defteri](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb). Bu Jupyter Notebook, bir [Azure açık veri kümesini](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) kullanarak, hava durumunu tahmin etmek, aks 'e dağıtmak ve veri drını izlemek için bir modeli eğitmeniz gerektiğini gösterir. 

* Veri [kümesi izleyicilerinde](how-to-monitor-datasets.md)verileri Drın olarak algıla.

* Veri, genel kullanıma yönelik olarak hareket ederken sorularınızı, yorumlarınızı veya önerilerinizi önemli ölçüde beğeneceğiz. Aşağıdaki ürün geri bildirim düğmesini kullanın! 
