---
title: İşlem hattı günlük dosyalarını izleme &
titleSuffix: Azure Machine Learning
description: Eğitim ve Batch Puanlama işlem hatlarınıza günlük kaydı ekleyin ve günlüğe kaydedilen sonuçları Application Insights görüntüleyin.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 7ed27eba66b3d18bed8017934fce85928b961392
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520056"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>Uyarılar ve hata ayıklama için Application Insights makine öğrenimi ardışık düzen günlük dosyalarını toplayın


[Opencensus](https://opencensus.io/quickstart/python/) Python kitaplığı, günlükleri betiklerinizde Application Insights yönlendirmek için kullanılabilir. İşlem hattı çalıştırmalarının tek bir yerde toplamı toplama, sorgular oluşturmanıza ve sorunları tanılamanıza olanak sağlar. Application Insights kullanmak zaman içinde günlükleri izlemenize ve işlem hattı günlüklerini çalışma genelinde karşılaştırmanıza imkan tanır.

Günlüklerinizin bir yerde olması, özel durumların ve hata iletilerinin geçmişini sağlar. Application Insights Azure uyarıları ile tümleştirilebildiğinden, Application Insights sorgularını temel alan uyarılar da oluşturabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* [Azure Machine Learning](./how-to-manage-workspace.md) çalışma alanı oluşturma ve [ilk işlem hattınızı oluşturma](./how-to-create-machine-learning-pipelines.md) adımlarını izleyin
* Azure Machine Learning SDK 'Yı yüklemek için [geliştirme ortamınızı yapılandırın](./how-to-configure-environment.md) .
* [Opencensus Azure Izleyici Dışarı Aktarıcı](https://pypi.org/project/opencensus-ext-azure/) paketini yerel olarak yükler:
  ```python
  pip install opencensus-ext-azure
  ```
* Bir [Application Insights örneği](../azure-monitor/app/opencensus-python.md) oluşturun (Bu belge, kaynak için bağlantı dizesini alma hakkında bilgi de içerir)

## <a name="getting-started"></a>Kullanmaya Başlama

Bu bölüm, Azure Machine Learning bir işlem hattından OpenCensus kullanımına özgü bir giriş niteliğindedir. Ayrıntılı bir öğretici için bkz. [Opencensus Azure Izleyici Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Azure ML ardışık düzenine bir PythonScriptStep ekleyin. [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) 'nizi opencensus-ext-Azure bağımlılığı ile yapılandırın. `APPLICATIONINSIGHTS_CONNECTION_STRING`Ortam değişkenini yapılandırın.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

`sample_step.py` adlı bir dosya oluşturun. Günlükleri Application Insights dolaştırmak için AzureLogHandler sınıfını içeri aktarın. Ayrıca, Python günlük kitaplığını içeri aktarmanız gerekir.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Sonra, AzureLogHandler öğesini Python günlükçüsü öğesine ekleyin.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Özel boyutlarla günlüğe kaydetme
 
Varsayılan olarak, Application Insights iletilen Günlükler çalıştırmaya veya denemeye geri dönmek için yeterli içeriğe sahip olmayacaktır. Günlükleri sorunları tanılamak için kullanılabilir hale getirmek için ek alanlar gereklidir. 

Bu alanları eklemek için, bir günlük iletisine bağlam sağlamak üzere özel boyutlar eklenebilir. Bir örnek, birisi aynı işlem hattı çalıştırmasında birden çok adımda günlükleri görüntülemek istediğinde bir örnektir.

Özel boyutlar, anahtar-değer (dize, dize) çiftleri sözlüğü yapar. Sözlük daha sonra Application Insights gönderilir ve sorgu sonuçlarında sütun olarak görüntülenir. Tek tek boyutları [sorgu parametresi](#additional-helpful-queries)olarak kullanılabilir.

### <a name="helpful-context-to-include"></a>Dahil etmek için yararlı bağlam

| Alan                          | Reasoning/örnek                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | , Her bir adıma geçmek zorunda kalmak yerine, tüm adımlarda zaman içindeki günlükleri görmek için aynı parent_run_id günlükleri sorgulayabilir.                                        |
| step_id                        | , Tek tek adımla bir dar kapsam ile bir sorunun nerede oluştuğunu görmek için günlükleri aynı step_id sorgulayabilir.                                                        |
| step_name                      | , Zaman içinde adım performansını görmek için günlükleri sorgulayabilir. Ayrıca, Portal Kullanıcı arabirimine sorulmadan son çalıştırmalar için bir step_id bulmaya de yardımcı olur                                          |
| experiment_name                | Zaman içinde deneme performansını görmek için günlüklerde sorgulama yapabilir. Ayrıca, Portal Kullanıcı arabirimine girmeden, son çalıştırmalar için bir parent_run_id veya step_id bulmaya yardımcı olur                   |
| run_url                 | , Araştırma için doğrudan çalıştırmaya doğrudan geri bağlantı sağlayabilir. |

**Diğer faydalı alanlar**

Bu alanlar ek kod araçları gerektirebilir ve çalıştırma bağlamı tarafından sağlanmamıştır.

| Alan                   | Reasoning/örnek                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Dağıtmak için CI/CD kullanılıyorsa, bu alan günlükleri adım adım ve ardışık düzen mantığını sağlayan kod sürümüyle ilişkilendirebilir. Bu bağlantı, sorunları tanılamaya veya belirli nitelikleri olan modelleri (günlük/ölçüm değerleri) belirlemenize yardımcı olabilir |
| run_type                       | Farklı model türleri veya eğitim ve Puanlama çalıştırmaları arasında ayrım yapabilir |

### <a name="creating-a-custom-dimensions-dictionary"></a>Özel boyut sözlüğü oluşturma

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", extra= {"custom_dimensions":custom_dimensions})
```

## <a name="opencensus-python-logging-considerations"></a>OpenCensus Python günlüğe kaydetme konuları

OpenCensus AzureLogHandler, Python günlüklerini Application Insights yönlendirmek için kullanılır. Sonuç olarak, Python günlüğü nusları göz önünde bulundurulmalıdır. Bir günlükçü oluşturulduğunda, varsayılan bir günlük düzeyi olur ve bu düzeyden daha büyük veya bu düzeye eşit olan günlükleri gösterir. Python günlüğü özelliklerinin kullanılması için iyi bir başvuru, [günlük tanıtım rehberini](https://docs.python.org/3/howto/logging-cookbook.html)kullanmaktır.

`APPLICATIONINSIGHTS_CONNECTION_STRING`OpenCensus kitaplığı için ortam değişkeni gereklidir. Düz metin bağlantı dizelerinin etrafında geçiş yapmak için bu ortam değişkenini bir işlem hattı parametresi olarak geçirmek yerine ayarlamayı öneririz.

## <a name="querying-logs-in-application-insights"></a>Application Insights Günlükler sorgulanıyor

Application Insights yönlendirilen Günlükler ' izlemeler ' veya ' özel durumlar ' altında görünür. Zaman hattınızı dahil etmek için zaman pencerenizi ayarladığınızdan emin olun.

![Sorgu sonucu Application Insights](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Application Insights sonucu, günlük iletisini ve düzeyini, dosya yolunu ve kod satırı numarasını gösterir. Ayrıca, dahil edilen özel boyutları da gösterir. Bu görüntüde, customDimensions sözlüğü önceki [kod örneğindeki](#creating-a-custom-dimensions-dictionary)anahtar/değer çiftlerini gösterir.

### <a name="additional-helpful-queries"></a>Ek faydalı sorgular

Aşağıdaki bazı sorgular ' customDimensions. Level ' kullanır. Bu önem düzeyleri, Python günlüğünün ilk olarak gönderildiği düzeye karşılık gelir. Ek sorgu bilgileri için bkz. [Azure Izleyici günlük sorguları](/azure/data-explorer/kusto/query/).

| Kullanım örneği                                                               | Sorgu                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Belirli özel boyut için günlük sonuçları, örneğin ' parent_run_id ' | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Son 7 gün içinde tüm eğitime yönelik günlük sonuçları                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Son 7 gün içindeki severityLevel hatası ile sonuçları günlüğe kaydet              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Son 7 gün içinde severityLevel hatası ile günlük sonuçlarının sayısı     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Sonraki Adımlar

Application Insights Örneğinizde günlüklerinizi aldıktan sonra, [Azure izleyici uyarılarını](../azure-monitor/alerts/alerts-overview.md#what-you-can-alert-on) sorgu sonuçlarına göre ayarlamak için kullanılabilirler.

Ayrıca, ek Öngörüler için sorgulardan sonuçları bir [Azure panosuna](../azure-monitor/app/tutorial-app-dashboards.md#add-logs-query) ekleyebilirsiniz.