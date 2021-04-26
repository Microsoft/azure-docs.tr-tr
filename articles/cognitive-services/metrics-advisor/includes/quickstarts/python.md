---
title: Ölçüm Danışmanı Python hızlı başlangıç
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: b6bea3fdc47bfbb74c18161a400d59453540b14d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081040"
---
[Başvuru belgeleri](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md)  |  [Paket (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/)  |  [Örnekler](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Azure aboneliğiniz olduktan sonra, ölçüm Danışmanı <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" "  target="_blank"> </a> örneğinizi dağıtmak için Azure Portal ölçüm Danışmanı kaynağı oluşturun.  
* Zaman serisi verileriyle kendi SQL veritabanınız.
  
> [!TIP]
> * [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples)'Da Python ölçüm Danışmanı örneklerini bulabilirsiniz.
> * Ölçüm Danışmanı kaynağınızın kullanabilmeniz için bir hizmet örneği dağıtması 10 ila 30 dakika sürebilir. Başarılı bir şekilde dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın. Dağıtımdan sonra, ölçüm Danışmanı örneğinizi hem Web portalı hem de REST API kullanmaya başlayabilirsiniz.
> * Azure portal REST API URL 'sini kaynağınızın **genel bakış** bölümünde bulabilirsiniz. Şu şekilde görünür:
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>Ayarlanıyor

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Python yükledikten sonra, ile istemci kitaplığını yükleyebilirsiniz:

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>Yeni bir Python uygulaması oluşturma

Yeni bir Python dosyası oluşturun ve aşağıdaki kitaplıkları içeri aktarın.

```python
import os
import datetime
```

Kaynağınızın Azure uç noktası ve anahtarı için değişkenler oluşturun.

> [!IMPORTANT]
> Azure portala gidin. **Önkoşullar** bölümünde oluşturduğunuz ölçüm Danışmanı kaynağı başarıyla dağıtılırsa, **sonraki adımlar** altında **Kaynağa Git** düğmesine tıklayın. Kaynak **yönetimi** altında, kaynağın **anahtar ve uç** nokta sayfasında abonelik Anahtarlarınızı ve uç noktanızı bulabilirsiniz. <br><br>API anahtarınızı almak için adresine gitmeniz gerekir [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Kaynağınız için uygun: **Dizin**, **abonelikler** ve **çalışma alanını** seçin ve **kullanmaya** başlayın ' ı seçin. Bundan sonra API anahtarlarınızı adresinden alabilirsiniz [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> İşiniz bittiğinde kodu koddan kaldırmayı unutmayın ve hiçbir zaman herkese açık bir şekilde nakletmeyin. Üretim için, kimlik bilgilerinizi depolamak ve bunlara erişmek için güvenli bir yol kullanmayı düşünün. Daha fazla bilgi için bilişsel Hizmetler [güvenlik](../../../cognitive-services-security.md) makalesine bakın.

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar, ölçüm Danışmanı Python SDK 'sının önemli özelliklerinden bazılarını işler.

|Ad|Açıklama|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **Kullanım için**: <br> -Olayları listeleme <br> -Olayların temel nedenini listeleme <br> -Özgün zaman serisi verileri ve zaman serisi verilerinin hizmet tarafından zenginleştirerek elde alınması. <br> -Uyarılar listeleniyor <br> -Modelinizi ayarlamak için geri bildirim ekleme |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient) | **Şunları yapmanıza olanak sağlar:** <br> -Veri akışlarını yönetme <br> -Anomali algılama yapılandırması oluşturma, yapılandırma, alma, listeleme ve silme <br> -Anomali uyarı yapılandırması oluşturma, yapılandırma, alma, listeleme ve silme <br> -Kancaları Yönet  |
| [Veri akışı](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **Veri kaynağından alınan ölçüm danışmanı. Şu `DataFeed` satırları içerir:** <br> -Zaman damgaları <br> -Sıfır veya daha fazla boyut <br> -Bir veya daha fazla ölçü  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeedmetric#azure.ai.metricsadvisor.models.DataFeedMetric) | , `DataFeedMetric` Belirli bir iş sürecinin durumunu izlemek ve değerlendirmek için kullanılan, ölçülebilir bir ölçüdür. Bu, boyutlara bölünen birden çok zaman serisi değerinin bir birleşimi olabilir. Örneğin, bir Web durumu ölçümü Kullanıcı sayısı ve en-US pazarında boyut içerebilir. |

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Python için ölçüm Danışmanı istemci kitaplığı ile aşağıdakileri nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Veri akışı Ekle](#add-a-data-feed)
* [Alma durumunu denetle](#check-the-ingestion-status)
* [Kurulum algılama yapılandırması ve uyarı yapılandırması](#configure-anomaly-detection)
* [Uyarı yapılandırması oluşturma](#create-an-alert-configuration)
* [Sorgu anomali algılama sonuçları](#query-the-alert)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Bu örnekteki istemci, `MetricsAdvisorAdministrationClient` uç noktanızı ve anahtarlarınızı içeren bir nesneyi kullanan bir nesnedir `MetricsAdvisorKeyCredential` . Bu kod örneğini kopyalamanız gerekmez. Daha sonra oluşturduğunuz Yöntemler istemcisini örneklendirilecek. Diğer istemci, `MetricsAdvisorClient` Bu istemci hakkında daha fazla bilgi olarak, [başvuru belgelerinde](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient)bulunabilir.

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>Veri akışı Ekle

Yeni bir yöntemde, aşağıdaki örnekte olduğu gibi içeri aktarma deyimleri oluşturun. `sql_server_connection_string`Kendı SQL Server bağlantı dizeniz ile değiştirin ve `query` tek bir zaman damgasında verilerinizi döndüren bir sorgu ile değiştirin. Ayrıca, `DataFeedmetric` ve `DataFeedDimension` değerlerini özel verilerinize göre ayarlamanız gerekir.

> [!IMPORTANT]
> Sorgu her bir zaman damgasında her bir boyut birleşimi için en çok bir kayıt döndürmelidir. Ve sorgu tarafından döndürülen tüm kayıtlar aynı zaman damgalarına sahip olmalıdır. Ölçüm Danışmanı, verilerinizi almak için bu sorguyu her zaman damgası için çalıştırır. Daha fazla bilgi ve örnek için [sorgular hakkında SSS bölümüne](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) bakın. 

Anahtarlarınız ve uç noktanızla bir istemci oluşturun ve `client.create_data_feed()` ad, kaynak, ayrıntı düzeyi ve şemayı yapılandırmak için kullanın. Alma süresini, toplama ayarlarını ve daha fazlasını da ayarlayabilirsiniz.


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        DataFeedMetric,
        DataFeedDimension,
        DataFeedOptions,
        DataFeedRollupSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
    name="My data feed",
    source=SQLServerDataFeed(
        connection_string=sql_server_connection_string,
        query=query,
    ),
    granularity="Daily",
    schema=DataFeedSchema(
        metrics=[
            DataFeedMetric(name="cost", display_name="Cost"),
            DataFeedMetric(name="revenue", display_name="Revenue")
        ],
        dimensions=[
            DataFeedDimension(name="category", display_name="Category"),
            DataFeedDimension(name="city", display_name="City")
        ],
        timestamp_column="Timestamp"
    ),
    ingestion_settings=datetime.datetime(2019, 10, 1),
    options=DataFeedOptions(
        data_feed_description="cost/revenue data feed",
        rollup_settings=DataFeedRollupSettings(
            rollup_type="AutoRollup",
            rollup_method="Sum",
            rollup_identification_value="__CUSTOM_SUM__"
        ),
        missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
            fill_type="SmartFilling"
        ),
        access_mode="Private"
    )
)

return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>Alma durumunu denetleme

Yeni bir yöntemde, aşağıdaki örnekte olduğu gibi bir içeri aktarma açıklaması oluşturun. `data_feed_id`Oluşturduğunuz veri akışı kimliğiyle değiştirin. Anahtarlarınız ve uç noktanız ile bir istemci oluşturun ve `client.list_data_feed_ingestion_status()` alma ilerlemesini almak için kullanın. Son etkin ve başarılı zaman damgaları gibi ayrıntıları yazdırın.


```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

   client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

ingestion_status = client.list_data_feed_ingestion_status(
    data_feed_id,
    datetime.datetime(2020, 9, 20),
    datetime.datetime(2020, 9, 25)
)
for status in ingestion_status:
    print("Timestamp: {}".format(status.timestamp))
    print("Status: {}".format(status.status))
    print("Message: {}\n".format(status.message))

```

## <a name="configure-anomaly-detection"></a>Anomali algılamayı yapılandırma

Yeni bir yöntemde, aşağıdaki örnekte olduğu gibi içeri aktarma deyimleri oluşturun. `metric_id`Yapılandırmak istediğiniz ölçümün kimliğiyle değiştirin. Anahtarlarınız ve uç noktanızla bir istemci oluşturun ve `client.create_detection_configuration` Yeni bir algılama yapılandırması oluşturmak için kullanın. Eşik koşulları anomali algılama için parametreleri belirtir.

```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    
client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

change_threshold_condition = ChangeThresholdCondition(
    anomaly_detector_direction="Both",
    change_percentage=20,
    shift_point=10,
    within_range=True,
    suppress_condition=SuppressCondition(
        min_number=5,
        min_ratio=2
    )
)
hard_threshold_condition = HardThresholdCondition(
    anomaly_detector_direction="Up",
    upper_bound=100,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)
smart_detection_condition = SmartDetectionCondition(
    anomaly_detector_direction="Up",
    sensitivity=10,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)

detection_config = client.create_detection_configuration(
    name="my_detection_config",
    metric_id=metric_id,
    description="anomaly detection config for metric",
    whole_series_detection_condition=MetricDetectionCondition(
        cross_conditions_operator="OR",
        change_threshold_condition=change_threshold_condition,
        hard_threshold_condition=hard_threshold_condition,
        smart_detection_condition=smart_detection_condition
    )
)
return detection_config
```

## <a name="create-a-hook"></a>Kanca oluşturma

Yeni bir yöntemde, aşağıdaki örnekte olduğu gibi içeri aktarma deyimleri oluşturun. Anahtarlarınız ve uç noktanızla bir istemci oluşturun ve `client.create_hook()` bir kanca oluşturmak için kullanın. Uyarı almak için bir açıklama, uyarının gönderileceği e-postaların bir listesini ve bir dış bağlantıyı girin.  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailNotificationHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key))

hook = client.create_hook(
    hook=EmailNotificationHook(
        name="email hook",
        description="my email hook",
        emails_to_alert=["alertme@alertme.com"],
        external_link="https://example.com/handleAlerts", # you must enter a valid webhook url to post the alert payload
    )
)
```

##  <a name="create-an-alert-configuration"></a>Uyarı yapılandırması oluşturma

Yeni bir yöntemde, aşağıdaki örnekte olduğu gibi içeri aktarma deyimleri oluşturun. `detection_configuration_id`Anomali algılama YAPıLANDıRMANıZıN kimliğiyle değiştirin ve öğesini `hook_id` daha önce oluşturduğunuz kanca ile değiştirin. Anahtarlarınız ve uç noktanızla bir istemci oluşturun ve `client.create_alert_configuration()` bir uyarı yapılandırması oluşturmak için kullanın. 

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

alert_config = client.create_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>Uyarıyı sorgulama

Yeni bir yöntemde, aşağıdaki örnekte olduğu gibi bir içeri aktarma açıklaması oluşturun. `alert_id`Uyarınız için olan kimlikle değiştirin ve `alert_config_id` uyarı yapılandırma kimliğiyle değiştirin. Anahtarlarınız ve uç noktanız ile bir istemci oluşturun ve `client.list_anomalies` bir uyarının anorlarını listelemek için kullanın. 

```python
from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
alert_id = "<replace-with-your-alert-id>"
alert_config_id = "<replace-with-your-alert-configuration-id"

client = MetricsAdvisorClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

results = client.list_alerts(
    alert_configuration_id=alert_config_id,
    start_time=datetime.datetime(2020, 1, 1),
    end_time=datetime.datetime(2020, 9, 9),
    time_mode="AnomalyTime",
)
for result in results:
    print("Alert id: {}".format(result.id))
    print("Create on: {}".format(result.created_on))

results = client.list_anomalies(
    alert_configuration_id=alert_config_id,
    alert_id=alert_id,
)
for result in results:
    print("Create on: {}".format(result.created_on))
    print("Severity: {}".format(result.severity))
    print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı `python` hızlı başlangıç dosyanızdaki komutla çalıştırın.

```console
python quickstart-file.py
```
