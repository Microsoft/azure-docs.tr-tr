---
title: Veri kümelerinde veri kümeleri için analiz ve izleme (Önizleme)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning veri kümeleri izleyicileri (Önizleme) oluşturun, veri kümelerinde veri kümesini izleyin ve uyarıları ayarlayın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 24b9b120240ffc6f7dd2252d12c9f8af2bcfafbc
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049166"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Veri kümelerinde veri kayması (Önizleme) Algıla
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, Azure Machine Learning veri kümesi izleyicilerinin (Önizleme) nasıl oluşturulacağını, veri kümelerinde veri kayması ve istatistiksel değişikliklerin izlenmesini ve uyarıların nasıl ayarlanacağını öğreneceksiniz.

Azure Machine Learning veri kümesi izleyicilerinde şunları yapabilirsiniz:
* Verilerin zaman içinde nasıl değiştiği hakkında bilgi sahibi olmak için **verilerinizde DRFT 'Yi çözümleyin** .
* Eğitim ve veri kümeleri sunma arasındaki farklılıklar için **model verilerini izleyin** .
* Herhangi bir taban çizgisi ve hedef veri kümesi arasındaki farklılıklar için **yeni verileri izleyin** .
* İstatistik özelliklerinin zaman içinde nasıl değişme şeklini izlemek için **verilerdeki özellikleri profil** oluşturma.
* Olası sorunlara yönelik erken uyarılar için **veri kayması hakkında uyarı ayarlayın** . 

Ölçümler ve Öngörüler, Azure Machine Learning hizmeti çalışma alanıyla ilişkili [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) kaynağı aracılığıyla kullanılabilir.

> [!Important]
> Bkz. SDK ile izleme verileri, tüm sürümlerde kullanılabilir, ancak Web üzerindeki Studio üzerinden izleme verileri, yalnızca Enterprise Edition.

## <a name="prerequisites"></a>Önkoşullar

Veri kümesi izleyicileri oluşturmak ve bunlarla çalışmak için şunlar gerekir:
* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree) bugün deneyin.
* [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md).
* Azureml [için Azure Machine Learning SDK 'sı](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), azureml veri kümesi paketini içerir.
* Dosya yolunda belirtilen bir zaman damgasına sahip yapılandırılmış (tablolu) veriler, dosya adı veya veri sütunu.

## <a name="what-is-data-drift"></a>Veri kayması nedir?

Machine Learning bağlamında, veri dolu modeli, performans düşüşünü modellemeyi sağlayan model girişi verilerinde değişiklik gösterir. Model doğruluğunun zaman içindeki en önemli nedenlerinden biridir, böylece izleme verileri, model performans sorunlarını algılamaya yardımcı olur.

Veri Drın nedenleri şunlardır: 

- Değiştirilen bir algılayıcı gibi, ölçü birimlerini inç ve santimetre arasında değiştiren bir algılayıcı gibi yukarı akış işlemi değişiklikleri. 
- Bozuk algılayıcı gibi veri kalitesi sorunları daima 0 ' ı okur.
- Verilerdeki doğal Drın, mevsimler ile değişen ortalama sıcaklık.
- Özellikler veya birlikte değişen kaydırma arasındaki ilişki değişikliği. 

Azure Machine Learning veri kümesi izleyicilerinde, zaman içinde veri kümelerinde veri kayması algılamasında yardımcı olacak uyarılar ayarlayabilirsiniz. 

### <a name="dataset-monitors"></a>Veri kümesi izleyicileri 

Bir veri kümesindeki yeni verilerde verileri algılayıp uyarmak, geçmiş verileri DRFT için analiz etmek ve zaman içinde yeni veri profili oluşturmak için bir veri kümesi İzleyicisi oluşturabilirsiniz. Veri drara algoritması, verilerde bir değişikliğin genel bir ölçüsünü ve daha fazla araştırmadan hangi özelliklerin sorumlu olduğunu belirtir. Veri kümesi izleyicileri `timeseries` veri kümesindeki yeni verileri profilleyerek birçok farklı ölçüm üretir. Özel uyarı, [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)aracılığıyla izleyici tarafından oluşturulan tüm ölçümlerde ayarlanabilir. Veri kümesi izleyicileri, veri sorunlarını hızla yakalamak ve olası nedenleri tanımlayarak sorun ayıklama süresini azaltmak için kullanılabilir.  

Kavramsal olarak, Azure Machine Learning veri kümesi izleyicileri ayarlamaya yönelik üç birincil senaryo vardır.

Senaryo | Açıklama
---|---
Modelin eğitim verilerinden DRFT için bir modelin hizmet verilerini izleme | Bu senaryonun sonuçları, modelin doğruluğu için bir ara sunucu izleme olarak yorumlanabilir. bu da, veri sunma verileri eğitim verilerinden Drifts.
Bir zaman serisi veri kümesini önceki bir dönemde DRFT için izleme. | Bu senaryo daha genel olduğundan, yukarı akış veya model oluşturma yönündeki aşağı akış olan veri kümelerini izlemek için kullanılabilir.  Hedef veri kümesinin bir zaman damgası sütunu olması gerekir, ancak temel veri kümesi hedef veri kümesiyle ortak özellikler içeren herhangi bir tablo veri kümesi olabilir.
Geçmiş veriler üzerinde analiz gerçekleştiriliyor. | Bu, geçmiş verileri anlamak ve veri kümesi izleyicilerine yönelik ayarlarda kararları bilgilendirmek için kullanılabilir.

## <a name="how-dataset-can-monitor-data"></a>Veri kümesinin verileri nasıl izleyebilirler

Azure Machine Learning kullanarak veri kümeleri veri kümeleri aracılığıyla izlenir. Bir taban çizgisi veri kümesi (genellikle bir model için eğitim veri kümesi) için veri kayması izlemek üzere belirtilir. Hedef veri kümesi-genellikle model girişi verileri-taban çizgisi veri kümeniz zamana göre karşılaştırılır. Bu, hedef veri kümenizin belirtilen bir zaman damgası sütunu olması gerektiği anlamına gelir.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>Hedef veri kümesinde `timeseries` nitelik ayarlama

Hedef veri kümesinin, verilerdeki bir sütundan veya dosyaların yol düzeninden türetilmiş bir sanal sütunda zaman damgası sütununu belirterek, üzerinde `timeseries` nitelik ayarlanmış olması gerekir. Bu, Python SDK veya Azure Machine Learning Studio aracılığıyla yapılabilir. Veri kümesine `timeseries` nitelik eklemek için "ince gren" zaman damgasını temsil eden bir sütun belirtilmelidir. Verileriniz ' {yyyy/aa/gg} ' gibi zaman bilgileriyle klasör yapısına bölünmemişse, zaman serisi işlevinin önemini artırmak için yol deseninin ayarı aracılığıyla bir sanal sütun oluşturabilir ve bunu "kaba bir" zaman damgası olarak ayarlayabilirsiniz. 

#### <a name="python-sdk"></a>Python SDK'sı

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) class ' [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) yöntemi veri kümesi için zaman damgası sütununu tanımlar. 

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

Veri kümelerinin `timeseries` nitelik kullanmanın tam bir örneği için bkz. [örnek Not defteri](https://aka.ms/azureml-tsd-notebook) veya [veri kümeleri SDK belgeleri](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-).

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

Azure Machine Learning Studio kullanarak veri kümenizi oluşturursanız, verilerinize ait yolun zaman damgası bilgilerini içerdiğinden emin olun, verileri içeren tüm alt klasörleri dahil edin ve bölüm biçimini ayarlayın. 

Aşağıdaki örnekte, *Noaaisdflorde/2019* alt klasörü altındaki tüm veriler alınır ve bölüm biçimi zaman damgasının yıl, ay ve gününü belirtir. 

[![bölüm biçimi](media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

**Şema** ayarları ' nda, belirtilen veri kümesindeki bir sanal veya gerçek sütundan zaman damgası sütununu belirtin:

![Zaman damgası](media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Veri kümesi izleyici ayarları

Veri kümenizi belirtilen zaman damgası ayarlarıyla oluşturduktan sonra, veri kümesi monitörünüzü yapılandırmaya hazırsınız demektir.

Çeşitli veri kümesi izleyici ayarları üç gruba ayrılır: **temel bilgi, izleyici ayarları** ve **geri doldurma ayarları**.

### <a name="basic-info"></a>Temel bilgi

Bu tablo, veri kümesi İzleyicisi için kullanılan temel ayarları içerir.

| Ayar | Açıklama | İpuçları | Değiştirilebilir | 
| ------- | ----------- | ---- | ------- | 
| Ad | Veri kümesi izleyicisinin adı. | | Hayır |
| Taban çizgisi veri kümesi | Zaman içinde hedef veri kümesinin karşılaştırılması için temel olarak kullanılacak tablo veri kümesi. | Temel veri kümesi, hedef veri kümesiyle ortak olan özelliklere sahip olmalıdır. Genellikle, taban çizgisinin bir modelin eğitim veri kümesine veya hedef veri kümesinin dilimine ayarlanmış olması gerekir. | Hayır |
| Hedef veri kümesi | Veri kayması için analiz edilecek zaman damgası sütunuyla belirtilen tablo veri kümesi | Hedef veri kümesinde, temel veri kümesiyle ortak olan özellikler olmalıdır ve yeni verilerin eklendiği bir `timeseries` veri kümesi olmalıdır. Hedef veri kümesindeki geçmiş verileri analiz edilebilir veya yeni veriler izlenebilir. | Hayır | 
| Frequency | Bu, ardışık düzen işini zamanlamak ve geri doldurma çalıştırıyorsa geçmiş verileri çözümlemek için kullanılacak sıklığıdır. Seçenekler günlük, haftalık veya aylık olarak verilebilir. | Bu ayarı, taban çizgisine benzer bir veri boyutu içerecek şekilde ayarlayın. | Hayır | 
| Özellikler | Zaman içinde veri kayması için analiz edilecek özelliklerin listesi | Bir modelin, kavram SLA 'ları ölçmek için çıkış özelliklerine ayarlayın. Zamana göre (ay, yıl, Dizin vb.) bir zaman içinde olan özellikleri eklemeyin. Özellik listesini ayarladıktan sonra, var olan veri kayması izleyicisini geri doldurabilir. | Yes | 
| İşlem hedefi | Veri kümesi izleyici işlerini çalıştırmak için işlem hedefini Azure Machine Learning. | | Yes | 

### <a name="monitor-settings"></a>İzleme ayarları

Bu ayarlar, oluşturulacak zamanlanmış veri kümesi İzleyicisi işlem hattı içindir. 

| Ayar | Açıklama | İpuçları | Değiştirilebilir | 
| ------- | ----------- | ---- | ------- |
| Etkinleştir | Veri kümesi izleyici ardışık düzeninde zamanlamayı etkinleştirme veya devre dışı bırakma | Geri doldurma ayarıyla geçmiş verileri çözümlemek için bunu devre dışı bırakın. Veri kümesi İzleyicisi oluşturulduktan sonra etkinleştirilebilir. | Yes | 
| Gecikme süresi | Saat olarak, verilerin veri kümesine gelmesi için zaman alır. Örneğin, verilerin SQL DB My DataSet 'e ulaşması üç gün sürüyorsa, gecikme süresini 72 olarak ayarlayın. | Veri kümesi İzleyicisi oluşturulduktan sonra değiştirilemez | Hayır | 
| E-posta adresleri | Veri DRIP yüzdesi eşiğini ihlal eden uyarı için e-posta adresleri. | E-postalar Azure Izleyici aracılığıyla gönderilir. | Yes | 
| Eşik | E-posta uyarısı için veri kayması yüzdesi eşiği. | Daha fazla uyarı ve olay, çalışma alanının ilişkili Application Insights kaynağındaki diğer birçok ölçümde ayarlanabilir. | Yes | 

### <a name="backfill-settings"></a>Geri doldurma ayarları

Bu ayarlar, veri DRIP ölçümleri için geçmiş veriler üzerinde bir geri doldurma çalıştırmak içindir.

| Ayar | Açıklama | İpuçları |
| ------- | ----------- | ---- |
| Başlangıç tarihi | Geri doldurma işinin başlangıç tarihi. | | 
| Bitiş tarihi | Geri doldurma işinin bitiş tarihi. | Bu, başlangıç tarihinden itibaren 31 * sıklık biriminden daha uzun olamaz. Mevcut bir veri kümesi izleyicisinde, ölçümler geçmiş verileri analiz etmek veya güncelleştirilmiş ayarlarla ölçümleri değiştirmek için geri alınabilir. |

## <a name="create-dataset-monitors"></a>Veri kümesi izleyicileri oluşturma 

Azure Machine Learning Studio veya Python SDK ile yeni bir veri kümesindeki verileri algılamak ve uyarmak için veri kümesi izleyicileri oluşturun. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

Veri kümesi izleyicinizdeki uyarıları ayarlamak için, bir izleyici oluşturmak istediğiniz veri kümesini içeren çalışma alanının Enterprise Edition becerileri olmalıdır. 

Çalışma alanı işlevselliği onaylandıktan sonra, Studio 'nun giriş sayfasına gidin ve sol taraftaki veri kümeleri sekmesini seçin. Veri kümesi izleyicileri seçin.

![İzleme listesi](media/how-to-monitor-datasets/monitor-list.png)

**+ Izleyici oluştur** düğmesine tıklayın ve **İleri**' ye tıklayarak sihirbaza devam edin.

![Sihirbazı](media/how-to-monitor-datasets/wizard.png)

Elde edilen veri kümesi İzleyicisi listede görüntülenir. Bu izleyicinin ayrıntılar sayfasına gitmek için seçin.

### <a name="from-python-sdk"></a>Python SDK 'dan

Tüm ayrıntılar için [veri üzerinde Python SDK başvuru belgelerine](/python/api/azureml-datadrift/azureml.datadrift) bakın. 

Aşağıda, Python SDK kullanılarak veri kümesi izleyicisinin oluşturulmasına ilişkin bir örnek verilmiştir

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

`timeseries` veri kümesi ve veri kayması algılayıcısı ayarlama hakkında tam bir örnek için bkz. [örnek Not Defterim](https://aka.ms/datadrift-notebook).

## <a name="understanding-data-drift-results"></a>Veri drime sonuçlarını anlama

Veri izleyici iki sonuç grubu oluşturur: DRFT genel bakış ve özellik ayrıntıları. Aşağıdaki animasyon, seçilen özelliğe ve ölçüme göre kullanılabilir DRFT izleyici grafiklerini gösterir. 

![Tanıtım videosu](media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Drift genel bakış

**Değişikliklerini genel bakış** bölümü, veri tepesinin büyüklüğü ve hangi özelliklerin daha araştırılması gerektiği hakkında üst düzey Öngörüler içerir. 

| Ölçüm | Açıklama | İpuçları | 
| ------ | ----------- | ---- | 
| Veri dolu büyüklüğü | Zaman içinde temel ve hedef veri kümesi arasında bir yüzde olarak verilirler. 0 ile 100 arasında, 0 ' dan ' a kadar değişen veri kümeleri ve 100, Azure Machine Learning Data değişikliklerini özelliğinin iki veri kümesini birbirinden tamamen söyleyebilir olduğunu gösterir. | Bu büyüklüğü oluşturmak için kullanılan makine öğrenimi teknikleri nedeniyle ölçülen tam yüzdedeki gürültü beklenmektedir. | 
| Özelliğe göre DRFT katkısı | Hedef veri kümesindeki her özelliğin ölçülen ölçü cinsinden katkısı. |  Birlikte değişen kaydırma nedeniyle, bir özelliğin temel dağıtımı görece yüksek Özellik önem düzeyine sahip olacak şekilde değişikliğe gerek duymayabilir. | 

Aşağıdaki görüntü, [NOAA Ile tümleşik yüzey verilerinin](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)geri doldurulmasıyla sonuçlanan Azure Machine Learning Studio 'Daki **drift genel bakış** sonuçlarında görülen grafiklerin bir örneğidir. Veriler `stationName contains 'FLORIDA'`için örneklenir, çünkü temel veri kümesi olarak kullanılan 2019 Ocak ve hedef olarak kullanılan tüm 2019 verileri.
 
![Drift genel bakış](media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Özellik ayrıntıları

**Özellik ayrıntıları** bölümü, seçilen özelliğin dağıtımında değişiklik ve zaman içinde diğer istatistiklerle ilgili özellik düzeyi öngörüleri içerir. 

Hedef veri kümesi zaman içinde de profili oluşturulmuş olur. Her bir özelliğin taban çizgisi dağıtımı arasındaki istatistiksel mesafe, hedef veri kümesinin zaman içinde olduğu şekilde karşılaştırılır. Bu, kavramsal olarak bir özellik için olduğu özel durum ile verilere benzer şekilde benzerdir. Min, Max ve ortalama de mevcuttur. 

Azure Machine Learning Studio 'da, grafikteki bir veri noktasına tıkladığınızda gösterilen özelliğin dağıtımı buna göre değişir. Varsayılan olarak, temel veri kümesinin dağıtımını ve en son çalıştırmanın aynı özelliğin dağıtımını gösterir. 

Bu ölçümler Ayrıca, bir `DataDriftDetector` nesnesindeki `get_metrics()` yöntemi aracılığıyla Python SDK 'da de alınabilir. 

#### <a name="numeric-features"></a>Sayısal özellikler 

Her veri kümesi izleyicisinde sayısal özellikler profili oluşturulur. Aşağıdakiler Azure Machine Learning Studio 'da kullanıma sunuldu. Dağıtım için olasılık yoğunluğu gösterilir.

| Ölçüm | Açıklama |  
| ------ | ----------- |  
| Wasserstein mesafe | Taban çizgisi dağıtımını hedef dağıtıma dönüştürmek için gereken en düşük iş miktarı. |
| Ortalama değer | Özelliğin ortalama değeri. |
| En düşük değer | Özelliğin en küçük değeri. |
| En büyük değer | Özelliğin maksimum değeri. |

![Özellik ayrıntıları sayısal](media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Kategorik Özellikler 

Her veri kümesi izleyicisinde sayısal özellikler profili oluşturulur. Aşağıdakiler Azure Machine Learning Studio 'da kullanıma sunuldu. Dağıtım için bir histogram gösterilir.

| Ölçüm | Açıklama |  
| ------ | ----------- |  
| Eucliyen uzaklığı | Taban çizgisi ve hedef dağıtımlar arasındaki geometrik uzaklık. |
| Benzersiz değerler | Özelliğin benzersiz değerlerinin (kardinalite) sayısı. |


![Özellik ayrıntıları kategorik](media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Ölçümler, uyarılar ve olaylar

Ölçümler, Machine Learning çalışma alanı ile ilişkili [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) kaynağında sorgulanabilir. , E-posta/SMS/Push/Voice veya Azure Işlevi gibi bir eylemi tetiklemek için özel uyarı kuralları ve eylem grupları için ayarlama dahil Application Insights tüm özelliklerine erişim sağlar. Ayrıntılar için lütfen tüm Application Insights belgelerine bakın. 

Başlamak için Azure portal gidin ve çalışma alanınızın **genel bakış** sayfasını seçin.  İlişkili Application Insights kaynağı en sağda:

[![Azure portal genel bakış](media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Sol bölmedeki Izleme altında günlükleri (Analiz) seçin:

![Application Insights genel bakış](media/how-to-monitor-datasets/ai-overview.png)

Veri kümesi izleyici ölçümleri `customMetrics`olarak depolanır. Bir veri kümesi izleyicisini ayarladıktan sonra, bunları görüntülemek için basit bir sorgu yazabilir ve çalıştırabilirsiniz:

[![Log Analytics sorgusu](media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Uyarı kurallarını ayarlamaya yönelik ölçümleri tanımladıktan sonra yeni bir uyarı kuralı oluşturun:

![Yeni uyarı kuralı](media/how-to-monitor-datasets/alert-rule.png)

Küme Koşulları karşılandığında gerçekleştirilecek eylemi tanımlamak için mevcut bir eylem grubunu kullanabilir veya yeni bir tane oluşturabilirsiniz:

![Yeni eylem grubu](media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Sorun giderme

Sınırlamalar ve bilinen sorunlar:

* Arka doldurma işlerinin zaman aralığı, izleyicinin sıklık ayarının 31 aralıklarıyla sınırlıdır. 
* Bir özellik listesi belirtilmediği takdirde (kullanılan tüm özellikler) 200 özelliklerinin sınırlaması.
* İşlem boyutu, verileri işleyecek kadar büyük olmalıdır. 
* Veri kümenizin, belirli bir izleyici çalıştırması için başlangıç ve bitiş tarihi içinde verileri olduğundan emin olun.

Veri kümesindeki sütunlar veya özellikler, aşağıdaki tabloda yer alan koşullara göre kategorik veya sayısal olarak sınıflandırılır. Özellik bu koşulları karşılamıyorsa (örneğin, > 100 benzersiz değeri olan dize türünde bir sütun), bu özellik veri dramızda algoritmasından bırakılır ancak yine de profil oluşturulur. 

| Özellik türü | Veri türü | Koşul | Sınırlamalar | 
| ------------ | --------- | --------- | ----------- |
| Kategorik | String, bool, int, float | Özelliğindeki benzersiz değer sayısı 100 ' den az ve satır sayısının %5 ' inden az. | Null, kendi kategorisi olarak değerlendirilir. | 
| Sayısal | int, float | Bir sayısal veri türü ve kategorik bir özelliğin koşullarını karşılamıyor. | Değerin %15 ' i > null ise özellik bırakıldı. | 

## <a name="next-steps"></a>Sonraki adımlar

* Bir veri kümesi izleyicisini ayarlamak için [Azure Machine Learning Studio](https://ml.azure.com) 'Ya veya [Python not defterine](https://aka.ms/datadrift-notebook) gidin.
* Bkz. [Azure Kubernetes hizmetine dağıtılan modellerde](how-to-monitor-data-drift.md)veri drları ayarlama.
* [Olay kılavuzuyla](how-to-use-event-grid.md)veri kümesi DRFT izleyicileri ayarlayın. 