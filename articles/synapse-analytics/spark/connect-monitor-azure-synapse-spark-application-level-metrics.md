---
title: Öğretici-Azure SYNAPSE Spark uygulama düzeyi ölçümlerini bağlama ve izleme
description: Öğretici-SYNAPSE Prometheus bağlayıcısını kullanarak, neredeyse gerçek zamanlı Azure Spark uygulama ölçümleri için mevcut şirket içi Prometheus sunucunuzu Azure SYNAPSE çalışma alanıyla tümleştirmeyi öğrenin.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: d22975199eedae353f2dc12588671ae4b54c85ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109327"
---
# <a name="tutorial-connect-and-monitor-azure-synapse-spark-application-level-metrics"></a>Öğretici: Azure SYNAPSE Spark uygulama düzeyi ölçümlerini bağlama ve izleme

## <a name="overview"></a>Genel Bakış

Bu öğreticide, mevcut şirket içi Prometheus sunucunuzu, SYNAPSE Prometheus bağlayıcısını kullanarak neredeyse gerçek zamanlı Azure Spark uygulama ölçümleri için Azure SYNAPSE çalışma alanı ile tümleştirmeyi öğreneceksiniz. 

Bu öğretici Ayrıca Azure SYNAPSE REST ölçümleri API 'Lerini de tanıtır. Kendi izleme ve tanılama araç setinizi oluşturmak veya izleme sistemlerinizle bütünleştirmek için REST API 'Leri aracılığıyla Spark uygulama ölçümleri verilerini getirebilirsiniz.

## <a name="use-azure-synapse-prometheus-connector-for-your-on-premises-prometheus-servers"></a>Şirket içi Prometheus sunucularınız için Azure SYNAPSE Prometheus bağlayıcısını kullanın

[Azure SYNAPSE Prometheus Bağlayıcısı](https://github.com/microsoft/azure-synapse-spark-metrics) açık kaynaklı bir projem. SYNAPSE Prometheus Bağlayıcısı, şunları yapmanıza olanak tanımak için dosya tabanlı bir hizmet bulma yöntemi kullanır:
 - AAD hizmet sorumlusu aracılığıyla SYNAPSE çalışma alanında kimlik doğrulaması yapın.
 - Çalışma alanını Apache Spark uygulamalar listesini getirin. 
 - Prometheus dosya tabanlı yapılandırması aracılığıyla Spark uygulama ölçümlerini çekin. 

### <a name="1-prerequisite"></a>1. önkoşul

Bir Linux sanal makinesine dağıtılan bir Prometheus sunucusuna sahip olmanız gerekir.

### <a name="2-create-a-service-principal"></a>2. hizmet sorumlusu oluşturma

Azure SYNAPSE Prometheus bağlayıcısını şirket içi Prometheus sunucunuzda kullanmak için, hizmet sorumlusu oluşturmak için aşağıdaki adımları izlemeniz gerekir.

#### <a name="21-create-a-service-principal"></a>2,1 hizmet sorumlusu oluşturma:

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Sonuç şöyle görünmelidir:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

AppID, parola ve kiracı KIMLIĞI ' ni aklınızda edin.

#### <a name="22-add-corresponding-permissions-to-the-service-principal-created-in-the-above-step"></a>2,2 yukarıdaki adımda oluşturulan hizmet sorumlusuna ilgili izinleri ekleyin.

![ekran görüntüsü izin ver srtzya](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)

1. [Azure SYNAPSE Analytics çalışma alanınızda](https://web.azuresynapse.net/) SYNAPSE Administrator olarak oturum açın

2. SYNAPSE Studio 'da, sol taraftaki bölmede **yönet > erişim denetimi** ' ni seçin.

3. **Rol ataması eklemek** için sol üstteki Ekle düğmesine tıklayın

4. Kapsam için **çalışma alanı** ' nı seçin.

5. Rol için **SYNAPSE Işlem işletmeni** seçin

6. Kullanıcı Seç için **<service_principal_name>** girin ve hizmet sorumlusuna tıklayın

7. **Uygula** ' ya tıklayın (iznin etkili olması için 3 dakika bekleyin.)


### <a name="3-download-the-azure-synapse-prometheus-connector"></a>3. Azure SYNAPSE Prometheus bağlayıcısını indirin

Azure SYNAPSE Prometheus bağlayıcısını yüklemek için komutları kullanın.

```bash
git clone https://github.com/microsoft/azure-synapse-spark-metrics.git
cd ./azure-synapse-spark-metrics/synapse-prometheus-connector/src
python pip install -r requirements.txt
```

### <a name="4-create-a-config-file-for-azure-synapse-workspaces"></a>4. Azure SYNAPSE çalışma alanları için bir yapılandırma dosyası oluşturma

Config klasöründe bir config. YAML dosyası oluşturun ve aşağıdaki alanları girin: workspace_name, tenant_id, service_principal_name ve service_principal_password.
YAML yapılandırmasında birden fazla çalışma alanı ekleyebilirsiniz.

```yaml
workspaces:
  - workspace_name: <your_workspace_name>
    tenant_id: <tenant_id>
    service_principal_name: <service_principal_app_id>
    service_principal_password: "<service_principal_password>"
```

### <a name="5-update-the-prometheus-config"></a>5. Prometheus yapılandırmasını güncelleştirin

Aşağıdaki yapılandırma bölümünü, Prometheus scrape_config ekleyin ve <your_workspace_name> çalışma alanınızın adı ve <path_to_synapse_connector> klonlanan SYNAPSE-Prometheus-Connector klasörünüze değiştirin

```yaml
- job_name: synapse-prometheus-connector
  static_configs:
  - labels:
      __metrics_path__: /metrics
      __scheme__: http
    targets:
    - localhost:8000
- job_name: synapse-workspace-<your_workspace_name>
  bearer_token_file: <path_to_synapse_connector>/output/workspace/<your_workspace_name>/bearer_token
  file_sd_configs:
  - files:
    - <path_to_synapse_connector>/output/workspace/<your_workspace_name>/application_discovery.json
    refresh_interval: 10s
  metric_relabel_configs:
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_application_[0-9]+_[0-9]+_(.+)
    replacement: spark_$1
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_(.+)
    replacement: spark_$1
```


### <a name="6-start-the-connector-in-the-prometheus-server-vm"></a>6. bağlayıcıyı Prometheus sunucusu sanal makinesinde başlatın

Prometheus sunucusu sanal makinesinde aşağıdaki gibi bir bağlayıcı sunucusu başlatın.

```
python main.py
```

Birkaç saniye bekleyin ve bağlayıcının çalışmaya başlaması gerekir. Ayrıca, Prometheus hizmeti bulma sayfasında "SYNAPSE-Prometheus-bağlayıcısını" görebilirsiniz.

## <a name="use-azure-synapse-prometheus-or-rest-metrics-apis-to-collect-metrics-data"></a>Ölçüm verilerini toplamak için Azure SYNAPSE Prometheus veya REST ölçümleri API 'Lerini kullanın

### <a name="1-authentication"></a>1. kimlik doğrulaması
Erişim belirteci almak için istemci kimlik bilgileri akışını kullanabilirsiniz. Ölçüm API 'sine erişmek için, hizmet sorumlusu için, API 'Lere erişmek için uygun izinlere sahip bir Azure AD erişim belirteci almalısınız.

| Parametre     | Gerekli | Açıklama                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| tenant_id     | Doğru     | Azure hizmet sorumlusu (uygulama) kiracı KIMLIĞINIZ                                                          |
| grant_type    | Doğru     | İstenen izin türünü belirtir. Istemci kimlik bilgileri verme akışında, değerin client_credentials olması gerekir. |
| client_id     | Doğru     | Azure portal veya Azure CLı 'da kaydettiğiniz uygulamanın uygulama (hizmet sorumlusu) KIMLIĞI.        |
| client_secret | Doğru     | Uygulama için oluşturulan gizli dizi (hizmet sorumlusu)                                                  |
| kaynak      | Doğru     | SYNAPSE Kaynak URI 'si, ' https://dev.azuresynapse.net ' olmalıdır                                                  |

```bash
curl -X GET -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&client_id=<service_principal_app_id>&resource=<azure_synapse_resource_id>&client_secret=<service_principal_secret>' \
  https://login.microsoftonline.com/<tenant_id>/oauth2/token
```

Yanıt şöyle görünür:

```json
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500666",
  "not_before": "1575499766",
  "resource": "2ff8...f879c1d",
  "access_token": "ABC0eXAiOiJKV1Q......un_f1mSgCHlA"
}
```

### <a name="2-list-running-applications-in-the-azure-synapse-workspace"></a>2. çalışan uygulamaları Azure SYNAPSE çalışma alanında listeleme

Bir Synapse çalışma alanı için Spark uygulamalarının listesini almak için, bu belge [izleme-Spark Iş listesini](/rest/api/synapse/data-plane/monitoring/getsparkjoblist)takip edebilirsiniz.


### <a name="3-collect-spark-application-metrics-with-the-prometheus-or-rest-apis"></a>3. Prometheus veya REST API 'Leriyle Spark uygulama ölçümleri toplayın


#### <a name="collect-spark-application-metrics-with-the-prometheus-api"></a>Prometheus API 'SI ile Spark uygulama ölçümlerini toplayın

Prometheus API 'SI ile belirtilen Spark uygulamasının en son ölçümlerini alın

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/metrics/executors/prometheus?format=html
```

| Parametre          | Gerekli | Açıklama                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Doğru     | Örneğin, çalışma alanı geliştirme uç noktası https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Doğru     | İstek için geçerli api-Version. Şu anda 2019-11-01-önizleme sürümündedir                    |
| Mini PoolName      | Doğru     | Spark havuzunun adı.                                                                   |
| Kimliği          | Doğru     | Oturumun tanımlayıcısı.                                                               |
| sparkApplicationId | Doğru     | Spark uygulama KIMLIĞI                                                                      |

Örnek Istek: 

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/metrics/executors/prometheus?format=html
```

Örnek yanıt:

Durum kodu: 200 yanıt şöyle görünür:

```
metrics_executor_rddBlocks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_memoryUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 74992
metrics_executor_diskUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_totalCores{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_maxTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_activeTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 1
metrics_executor_failedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_completedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 2
...

```

#### <a name="collect-spark-application-metrics-with-the-rest-api"></a>Spark uygulama ölçümlerini REST API toplayın

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/executors
```

| Parametre          | Gerekli | Açıklama                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Doğru     | Örneğin, çalışma alanı geliştirme uç noktası https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Doğru     | İstek için geçerli api-Version. Şu anda 2019-11-01-önizleme sürümündedir                    |
| Mini PoolName      | Doğru     | Spark havuzunun adı.                                                                   |
| Kimliği          | Doğru     | Oturumun tanımlayıcısı.                                                               |
| sparkApplicationId | Doğru     | Spark uygulama KIMLIĞI                                                                      |

Örnek İstek

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/executors
```

Örnek yanıt durum kodu: 200

```json
[
    {
        "id": "driver",
        "hostPort": "f98b8fc2aea84e9095bf2616208eb672007bde57624:45889",
        "isActive": true,
        "rddBlocks": 0,
        "memoryUsed": 75014,
        "diskUsed": 0,
        "totalCores": 0,
        "maxTasks": 0,
        "activeTasks": 0,
        "failedTasks": 0,
        "completedTasks": 0,
        "totalTasks": 0,
        "totalDuration": 0,
        "totalGCTime": 0,
        "totalInputBytes": 0,
        "totalShuffleRead": 0,
        "totalShuffleWrite": 0,
        "isBlacklisted": false,
        "maxMemory": 15845975654,
        "addTime": "2020-11-16T06:55:06.718GMT",
        "executorLogs": {
            "stdout": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stdout?start=-4096",
            "stderr": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stderr?start=-4096"
        },
        "memoryMetrics": {
            "usedOnHeapStorageMemory": 75014,
            "usedOffHeapStorageMemory": 0,
            "totalOnHeapStorageMemory": 15845975654,
            "totalOffHeapStorageMemory": 0
        },
        "blacklistedInStages": []
    },
    // ...
]
```


### <a name="4-build-your-own-diagnosis-and-monitoring-tools"></a>4. kendi tanılama ve izleme araçlarınızı oluşturun

Prometheus API 'SI ve REST API 'Leri Spark uygulaması ile ilgili bilgiler hakkında zengin ölçüm verileri sağlar. Uygulamayla ilgili ölçüm verilerini Prometheus API 'SI ve REST API 'Leri aracılığıyla toplayabilirsiniz. Ve gereksinimlerinize göre daha uygun olan kendi tanılama ve izleme araçlarınızı oluşturun.
