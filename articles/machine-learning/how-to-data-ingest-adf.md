---
title: Azure Data Factory ile veri alımı
titleSuffix: Azure Machine Learning
description: Azure Data Factory ve her birinin avantajları ile bir veri alma işlem hattı oluşturmaya yönelik kullanılabilir seçenekleri öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b842934ea6bb458a59a53ea7068aa9ece98aacf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98796154"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Azure Data Factory ile veri alımı

Bu makalede, [Azure Data Factory](../data-factory/introduction.md)bir veri alma işlem hattı oluşturmaya yönelik kullanılabilir seçenekler hakkında bilgi edineceksiniz. Bu Azure Data Factory işlem hattı, [Azure Machine Learning](overview-what-is-azure-ml.md)kullanım için verileri almak üzere kullanılır. Data Factory, verileri kolayca ayıklamanızı, dönüştürmenizi ve yüklemeyi (ETL) sağlar. Veriler dönüştürüldükten ve depolama alanına yüklendikten sonra, Azure Machine Learning makine öğrenimi modellerinizi eğitebilmeniz için kullanılabilir.

Basit veri dönüştürme, [veri akışı](../data-factory/control-flow-execute-data-flow-activity.md)gibi yerel Data Factory etkinlikleriyle ve gereçlerle işlenebilir. Daha karmaşık senaryolara geldiğinde veriler bazı özel kodla işlenebilir. Örneğin, Python veya R kodu.

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>Azure Data Factory veri alımı işlem hatlarını karşılaştırın 
Alma sırasında verileri dönüştürmek için Data Factory kullanmanın birçok yaygın tekniği vardır. Her bir tekniğe, belirli bir kullanım durumu için uygun olup olmadığını belirlemenize yardımcı olan avantajlar ve dezavantajlar vardır:

| Teknik | Avantajlar | Dezavantajlar |
| ----- | ----- | ----- |
| Data Factory + Azure Işlevleri | <li> Düşük gecikme süresi, sunucusuz işlem<li>Durum bilgisi olan işlevler<li>Yeniden kullanılabilir işlevler | Yalnızca kısa süre çalışan işleme için iyi |
| Data Factory + özel bileşen | <li>Büyük ölçekli paralel bilgi işlem<li>Ağır algoritmalara uygun | <li>Yürütülebilir dosya için kod sarmalanması gerekir<li>Bağımlılıkları ve GÇ işleme karmaşıklığı |
| Data Factory + Azure Databricks Not defteri |<li> Apache Spark<li>Yerel Python ortamı |<li>Pahalı olabilir<li>Başlangıçta küme oluşturulması zaman alır ve gecikme süresi ekler

## <a name="azure-data-factory-with-azure-functions"></a>Azure işlevleri ile Azure Data Factory

Azure Işlevleri, uygulama altyapısı hakkında endişelenmeden küçük kod parçalarını (işlevler) çalıştırmanıza olanak sağlar. Bu seçenekte, veriler bir Azure Işlevine Sarmalanan özel Python koduyla işlenir. 

İşlev, [Azure Data Factory Azure işlevi etkinliğiyle](../data-factory/control-flow-azure-function-activity.md)çağrılır. Bu yaklaşım, hafif veri dönüştürmeleri için iyi bir seçenektir. 

![Diyagramda bir Azure Data Factory işlem hattı ve Azure Işlevi ile ML işlem hattı ile bir Azure Machine Learning işlem hattı, eğitim modeli ile birlikte nasıl etkileşim kurdukları ve ham verilerle nasıl etkileşime gireceğini ve bu verilerin hazırlandığını gösterir.](media/how-to-data-ingest-adf/adf-function.png)



* Artılar:
    * Veriler, görece düşük gecikme süresine sahip sunucusuz bir işlem üzerinde işlenir
    * Data Factory işlem hattı, gelişmiş bir veri dönüştürme akışı uygulayabilen [dayanıklı bir Azure işlevini](../azure-functions/durable/durable-functions-overview.md) çağırabilir 
    * Veri dönüşümünün ayrıntıları, diğer konumlardan yeniden kullanılabilen ve çağrılabilen Azure Işlevi tarafından soyutlanmıştır
* Olumsuz
    * ADF ile kullanılmadan önce Azure Işlevlerinin oluşturulması gerekir
    * Azure Işlevleri yalnızca kısa süre çalışan veri işleme için uygundur

## <a name="azure-data-factory-with-custom-component-activity"></a>Özel bileşen etkinliğiyle Azure Data Factory

Bu seçenekte, veriler yürütülebilir dosyaya sarmalanmış özel Python kodu ile işlenir. [Azure Data Factory özel bileşen etkinliğiyle](../data-factory/transform-data-using-dotnet-custom-activity.md)çağrılır. Bu yaklaşım, önceki tekniğinden daha büyük veriler için daha iyi bir uyum.

![Diyagramda bir Azure Data Factory işlem hattı, özel bir bileşen ve M L işlem hattı ve bir Azure Machine Learning işlem hattı, eğitim modeli ile birlikte nasıl etkileşim kurdukları ve ham verilerle nasıl etkileşime gireceğini gösterir.](media/how-to-data-ingest-adf/adf-customcomponent.png)

* Artılar:
    * Veriler, büyük ölçekli paralel ve yüksek performanslı bilgi işlem sağlayan [Azure Batch](../batch/batch-technical-overview.md) havuzunda işlenir
    * Ağır algoritmaları çalıştırmak ve önemli miktarda veriyi işlemek için kullanılabilir
* Eksileri:
    * Azure Batch havuzun Data Factory kullanılmadan önce oluşturulması gerekir
    * Python kodunu yürütülebilir dosyaya sarmalama ile ilgili mühendisler. Bağımlılıkları ve giriş/çıkış parametrelerini işleme karmaşıklığı

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>Azure Databricks Python not defteriyle Azure Data Factory

[Azure Databricks](https://azure.microsoft.com/services/databricks/) , Microsoft bulutundaki Apache Spark tabanlı bir analiz platformudur.

Bu teknikte, veri dönüştürme bir Azure Databricks kümesinde çalışan bir [Python Not defteri](../data-factory/transform-data-using-databricks-notebook.md)tarafından gerçekleştirilir. Bu, Azure Databricks bir hizmetin tam gücünden yararlanan en yaygın yaklaşım olabilir. Ölçeklenebilir veri işleme için ölçekli olarak tasarlanmıştır.

![Diyagramda Azure Data Factory işlem hattı, Azure Databricks Python ve M L işlem hattı ve bir Azure Machine Learning işlem hattı ve eğitim modeli ile birlikte nasıl etkileşim kurdukları ve ham verilerle nasıl etkileşime gireceğini ve bu verilerin hazırlandığını gösterir.](media/how-to-data-ingest-adf/adf-databricks.png)

* Artılar:
    * Veriler, Apache Spark ortamı tarafından yedeklenen en güçlü veri işleme Azure hizmetinde dönüştürülür
    * TensorFlow, PyTorch ve scikit-öğrenme dahil olmak üzere veri bilimi çerçeveleri ve kitaplıkları ile Python 'un yerel desteği
    * Python kodunu işlevlere veya yürütülebilir modüllere sarmasına gerek yoktur. Kod olduğu gibi çalışıyor.
* Eksileri:
    * Azure Databricks altyapısı, Data Factory kullanılmadan önce oluşturulmalıdır
    * Azure Databricks yapılandırmasına bağlı olarak pahalı olabilir
    * "Soğuk" modundan işlem kümelerinin dönme, çözüme yüksek gecikme süresi getiren bir süre sürer 
    

## <a name="consume-data-in-azure-machine-learning"></a>Azure Machine Learning verileri tüketme 

Data Factory işlem hattı, hazırlanan verileri bulut depolama alanına kaydeder (Azure Blobu veya Azure Datalake gibi). <br>
Hazırlanan verilerinizi Azure Machine Learning ile kullanın, 

* Data Factory işlem hattınızdan bir Azure Machine Learning işlem hattı çağırma.<br>**OR**
* Daha sonraki bir zamanda kullanmak üzere [Azure Machine Learning veri deposu](how-to-access-data.md#create-and-register-datastores) ve [Azure Machine Learning veri kümesi](how-to-create-register-datasets.md) oluşturma.

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>Data Factory Azure Machine Learning ardışık düzeni çağır

Bu yöntem [Machine Learning işlemler (MLOps) iş akışları](concept-model-management-and-deployment.md#what-is-mlops)için önerilir. Azure Machine Learning bir işlem hattı ayarlamak istemiyorsanız, bkz. [verileri doğrudan depolamadan okuma](#read-data-directly-from-storage).

Data Factory işlem hattı her çalıştığında, 

1. Veriler, depolamada farklı bir konuma kaydedilir. 
1. Konumu Azure Machine Learning geçirmek için, Data Factory işlem hattı bir [Azure Machine Learning](concept-ml-pipelines.md)işlem hattını çağırır. ML ardışık düzeni çağrılırken, veri konumu ve çalıştırma KIMLIĞI parametre olarak gönderilir. 
1. ML işlem hattı daha sonra veri konumuna sahip bir Azure Machine Learning veri deposu ve veri kümesi oluşturabilir. [Data Factory ' de Azure Machine Learning işlem hatlarını yürütme](../data-factory/transform-data-machine-learning-service.md)hakkında daha fazla bilgi edinin.

![Diyagramda bir Azure Data Factory işlem hattı ve bir Azure Machine Learning işlem hattı ve bunların ham verilerle nasıl etkileşime gireceğini ve hazırlanan verileri gösterir. Data Factory işlem hattı, verileri, Machine Learning çalışma alanındaki veri kümelerini beslemelerini sağlayan, hazırlanan veri veritabanına akış akışı sağlar.](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> Veri kümeleri [sürüm oluşturmayı destekler](./how-to-version-track-datasets.md), bu nedenle ml ARDıŞıK düzeni ADF işlem hattındaki en son verileri gösteren yeni bir veri kümesinin yeni bir sürümünü kaydedebilir.

Veriler bir veri deposu veya veri kümesi aracılığıyla erişilebilir olduktan sonra, ML modelini eğitebilmeniz için bunu kullanabilirsiniz. Eğitim süreci, ADF 'den çağrılan aynı ML ardışık düzeninin bir parçası olabilir. Ya da bir Jupyter not defterinde deneme gibi ayrı bir işlem olabilir.

Veri kümeleri sürümü oluşturmayı desteklediği ve işlem hattından çalıştırılan her çalıştırma yeni bir sürüm oluşturduğundan, bir modeli eğitmek için hangi verilerin sürümünün kullanıldığını anlamak kolaydır.

### <a name="read-data-directly-from-storage"></a>Verileri doğrudan depolamadan okuma

ML işlem hattı oluşturmak istemiyorsanız, hazırlanan verilerinizin bir Azure Machine Learning veri deposu ve veri kümesiyle kaydedildiği depolama hesabından doğrudan verilere erişebilirsiniz. 

Aşağıdaki Python kodu, Azure Data Lake Generation 2 depolamasına bağlanan bir veri deposu oluşturmayı gösterir. [Veri depoları ve hizmet sorumlusu izinlerinin nerede bulunacağı hakkında daha fazla bilgi edinin](how-to-access-data.md#create-and-register-datastores).

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

Ardından, Machine Learning görevinde kullanmak istediğiniz dosya (ler) i için bir veri kümesi oluşturun. 

Aşağıdaki kod, bir CSV dosyasından bir TabularDataset oluşturur `prepared-data.csv` . [Veri kümesi türleri ve kabul edilen dosya biçimleri](how-to-create-register-datasets.md#dataset-types)hakkında daha fazla bilgi edinin. 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

Buradan, `prepared_dataset` eğitim betiklerinizde olduğu gibi hazırlanan verilerinize başvurmak için kullanın. [Azure Machine Learning veri kümeleri ile modelleri eğitme](./how-to-train-with-datasets.md)hakkında bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Factory bir Databricks Not defteri çalıştırma](../data-factory/transform-data-using-databricks-notebook.md)
* [Azure Storage hizmetlerindeki verilere erişme](./how-to-access-data.md#create-and-register-datastores)
* [Modelleri Azure Machine Learning veri kümeleri Ile eğitme](./how-to-train-with-datasets.md).
* [Veri alımı işlem hattı için DevOps](./how-to-cicd-data-ingestion.md)