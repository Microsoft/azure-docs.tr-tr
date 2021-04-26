---
title: Ölçümleri ve günlükleri toplamak ve görselleştirmek için Azure Log Analytics kullanma (Önizleme)
description: Azure Log Analytics çalışma alanınıza Apache Spark uygulama ölçümlerini ve günlüklerini toplamak ve göndermek için SYNAPSE yerleşik Azure Log Analytics bağlayıcısını nasıl etkinleştireceğinizi öğrenin.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: 243618192593d93bba9d5229e7becfb2af62ce32
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108568"
---
# <a name="tutorial-use-azure-log-analytics-to-collect-and-visualize-metrics-and-logs-preview"></a>Öğretici: ölçümleri ve günlükleri toplamak ve görselleştirmek için Azure Log Analytics kullanma (Önizleme)

Bu öğreticide, [azure Log Analytics çalışma alanınıza](/azure/azure-monitor/logs/quick-create-workspace)Apache Spark uygulama ölçümlerini ve günlüklerini toplamak ve göndermek için SYNAPSE yerleşik Azure Log Analytics bağlayıcısını nasıl etkinleştireceğinizi öğreneceksiniz. Daha sonra ölçümleri ve günlükleri görselleştirmek için bir Azure izleyici çalışma kitabından yararlanabilirsiniz.

## <a name="configure-azure-log-analytics-workspace-information-in-synapse-studio"></a>SYNAPSE Studio 'da Azure Log Analytics çalışma alanı bilgilerini yapılandırma

### <a name="step-1-create-an-azure-log-analytics-workspace"></a>1. Adım: Azure Log Analytics çalışma alanı oluşturma

Log Analytics çalışma alanı oluşturmak için aşağıdaki belgeleri takip edebilirsiniz:
- [Azure portalında Log Analytics çalışma alanı oluşturma](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace)
- [Azure CLı ile Log Analytics çalışma alanı oluşturma](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace-cli)
- [PowerShell kullanarak Azure Izleyici 'de Log Analytics çalışma alanı oluşturma ve yapılandırma](https://docs.microsoft.com/azure/azure-monitor/logs/powershell-workspace-configuration)

### <a name="step-2-prepare-a-spark-configuration-file"></a>2. Adım: Spark yapılandırma dosyası hazırlama

#### <a name="option-1-configure-with-azure-log-analytics-workspace-id-and-key"></a>1. Seçenek Azure Log Analytics çalışma alanı KIMLIĞI ve anahtarı ile yapılandırma 

Aşağıdaki Spark yapılandırmasını kopyalayın, **"spark_loganalytics_conf.txt"** olarak kaydedin ve parametreleri girin:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Azure Log Analytics çalışma alanı KIMLIĞI.
   - `<LOG_ANALYTICS_WORKSPACE_KEY>`: Azure Log Analytics anahtarı: **Azure portal > azure Log Analytics çalışma alanı > Agents yönetimi > birincil anahtar**

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-an-azure-key-vault"></a>2. Seçenek Azure Key Vault ile yapılandırma

> [!NOTE]
>
> Spark uygulamaları gönderecek kullanıcılara gizli dizi Oku izni vermeniz gerekir. Lütfen bkz. [Azure rol tabanlı erişim denetimi ile Key Vault anahtarlarına, sertifikalara ve gizli anahtarlara erişim sağlama](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)

Çalışma alanı anahtarını depolamak üzere bir Azure Key Vault yapılandırmak için aşağıdaki adımları izleyin:

1. Azure portal anahtar kasanıza oluşturun ve bu sayfaya gidin
2. Key Vault ayarları sayfalarında **gizlilikler**' ı seçin.
3. **Oluştur/İçeri Aktar**’a tıklayın.
4. **Bir gizli dizi oluştur** ekranında aşağıdaki değerleri seçin:
   - **Ad**: parola için bir ad yazın, `"SparkLogAnalyticsSecret"` Varsayılan olarak yazın.
   - **Değer**: gizli dizi için **<LOG_ANALYTICS_WORKSPACE_KEY>** yazın.
   - Diğer değerleri varsayılan değerlerinde bırakın. **Oluştur**’a tıklayın.
5. Aşağıdaki Spark yapılandırmasını kopyalayın, **"spark_loganalytics_conf.txt"** olarak kaydedin ve parametreleri girin:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Azure Log Analytics çalışma alanı KIMLIĞI.
   - `<AZURE_KEY_VAULT_NAME>`: Yapılandırdığınız Azure Key Vault adı.
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>` (İsteğe bağlı): Azure Key Vault çalışma alanı anahtarı için gizli dizi adı, varsayılan: "parlak Loganalssecret".

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
>
> Log Analytics çalışma alanı kimliğini Azure Anahtar Kasası 'na da saklayabilirsiniz. Lütfen yukarıdaki adımlara başvurun ve çalışma alanı kimliğini gizli adla depolayın `"SparkLogAnalyticsWorkspaceId"` . Ya da `spark.synapse.logAnalytics.keyVault.key.workspaceId` Azure Anahtar Kasası 'nda çalışma alanı kimliği gizli adını belirtmek için config 'i kullanın.

#### <a name="option-3-configure-with-an-azure-key-vault-linked-service"></a>3. Seçenek Azure Key Vault bağlı bir hizmetle yapılandırma

> [!NOTE]
>
> SYNAPSE çalışma alanına gizli dizi Oku izni vermeniz gerekir. Lütfen bkz. [Azure rol tabanlı erişim denetimi ile Key Vault anahtarlarına, sertifikalara ve gizli anahtarlara erişim sağlama](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)

SYNAPSE Studio 'da, çalışma alanı anahtarını depolamak üzere Azure Key Vault bağlı bir hizmeti yapılandırmak için şu adımları izleyin:

1. Bölümündeki tüm adımları izleyin `Option 2. Configure with an Azure Key Vault` .
2. SYNAPSE Studio 'da bir Azure Anahtar Kasası bağlı hizmeti oluşturun:

    a. **SYNAPSE Studio 'ya gidin > bağlı hizmetleri > yönetin**, **Yeni** düğmesine tıklayın.

    b. Arama kutusunda **Azure Key Vault** arama yapın.

    c. Bağlı hizmet için bir ad yazın.

    d. Azure anahtar kasanızı seçin. **Oluştur**’a tıklayın.

3. `spark.synapse.logAnalytics.keyVault.linkedServiceName`Spark yapılandırmasına bir öğe ekleyin.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-spark-configuration"></a>Kullanılabilir Spark yapılandırması

| Yapılandırma adı                                  | Varsayılan değer                | Description                                                                                                                                                                                                |
| --------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| spark. SYNAPSE. logAnalytics. Enabled                  | yanlış                        | Spark uygulamaları için Azure Log Analytics havuzunu etkinleştirmek için true. Aksi durumda, false değerini alır.                                                                                                                  |
| spark. SYNAPSE. logAnalytics. Workspace kimliği              | -                            | Hedef Azure Log Analytics çalışma alanı KIMLIĞI                                                                                                                                                          |
| spark. SYNAPSE. logAnalytics. Secret                   | -                            | Hedef Azure Log Analytics çalışma alanı gizli dizisi.                                                                                                                                                      |
| spark. SYNAPSE. logAnalytics. Keykasa. linkedServiceName   | -                            | Azure Log Analytics çalışma alanı KIMLIĞI ve anahtarı için Azure Anahtar Kasası bağlantılı hizmet adı                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.name            | -                            | Azure Log Analytics KIMLIĞI ve anahtarı için Azure Anahtar Kasası adı                                                                                                                                                |
| spark. SYNAPSE. logAnalytics. Keykasa. Key. Çalışmakimliği | Parlak Loganaliz Ticsworkspace kimliği | Azure Log Analytics çalışma alanı KIMLIĞI için Azure Anahtar Kasası gizli adı                                                                                                                                       |
| spark. SYNAPSE. logAnalytics. Keykasa. Key. Secret      | Parlak Loganaliz Ticssecret      | Azure Log Analytics çalışma alanı anahtarı için Azure Anahtar Kasası gizli adı                                                                                                                                      |
| spark. SYNAPSE. logAnalytics. Keykasa. Urisufdüzeltmesini       | ods.opinsights.azure.com     | Hedef Azure Log Analytics çalışma alanı [URI son eki][uri_suffix]. Azure Log Analytics çalışma alanınız Azure küresel değilse, URI sonekini ilgili buluta göre güncelleştirmeniz gerekir. |

> [!NOTE]  
> - Azure Çin bulutları için, "Spark. SYNAPSE. logAnalytics. Keykasa. Urisufdüzeltmesini" parametresi "ods.opinsights.azure.cn" olmalıdır. 
> - Azure gov bulutları için "Spark. SYNAPSE. logAnalytics. Keykasa. Urisufdüzeltmesini" parametresi "ods.opinsights.azure.us" olmalıdır. 

[uri_suffix]: https://docs.microsoft.com/azure/azure-monitor/logs/data-collector-api#request-uri


### <a name="step-3-upload-your-spark-configuration-to-a-spark-pool"></a>3. Adım: Spark yapılandırmanızı Spark havuzuna yükleme
Yapılandırma dosyasını SYNAPSE Studio 'daki SYNAPSE Spark havuzunuza yükleyebilirsiniz.

   1. Azure SYNAPSE Studio 'da (Manage-> Apache Spark havuzları) Apache Spark havuzunuza gidin
   2. Apache Spark havuzunuzun sağ tarafındaki **"..."** düğmesine tıklayın
   3. Apache Spark yapılandırma seçin 
   4. **Karşıya yükle** ' ye tıklayın ve oluşturulan **"spark_loganalytics_conf.txt"** öğesini seçin.
   5. **Karşıya yükle** ve **Uygula**' ya tıklayın.

      > [!div class="mx-imgBorder"]
      > ![Spark havuzu yapılandırması](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> Yukarıdaki Spark havuzuna gönderilen tüm Spark uygulamaları, Spark uygulama ölçümlerini ve günlüklerini belirtilen Azure Log Analytics çalışma alanınıza göndermek için yapılandırma ayarını kullanacaktır.

## <a name="submit-a-spark-application-and-view-the-logs-and-metrics-in-azure-log-analytics"></a>Azure Log Analytics Spark uygulaması gönderme ve günlükleri ve ölçümleri görüntüleme

 1. Aşağıdaki yollarla, önceki adımda yapılandırılan Spark havuzuna bir Spark uygulaması gönderebilirsiniz:
    - Bir Synapse Studio Not defteri çalıştırın. 
    - Spark iş tanımı aracılığıyla bir Synapse Apache Spark Batch işi gönderme.
    - Spark etkinliği içeren bir işlem hattı çalıştırın.

 2. Belirtilen Azure Log Analytics çalışma alanına gidin ve Spark uygulaması çalışmaya başladığında uygulama ölçümlerini ve günlüklerini görüntüleyin.

## <a name="use-the-sample-azure-log-analytics-workbook-to-visualize-the-metrics-and-logs"></a>Ölçümleri ve günlükleri görselleştirmek için örnek Azure Log Analytics çalışma kitabını kullanın

1. [Çalışma kitabını buradan indirin](https://aka.ms/SynapseSparkLogAnalyticsWorkbook) .
2. Çalışma kitabı dosya içeriğini açın ve **kopyalayın** .
3. Azure Log Analytics çalışma kitabına gidin ([Azure portal](https://portal.azure.com/) > Log Analytics çalışma alanı > çalışma kitapları)
4. " **Gelişmiş Düzenleyici"** modunda **"boş"** Azure Log Analytics çalışma kitabını açın (</> simgesine basın).
5. Var olan herhangi bir JSON üzerine **yapıştırın** .
6. Sonra **Uygula** ' ya ve ardından **Düzenle**' ye basın.

    > [!div class="mx-imgBorder"]
    > ![Yeni çalışma kitabı](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![çalışma kitabını içeri aktar](./media/apache-spark-azure-log-analytics/import-workbook.png)

Ardından, Apache Spark uygulamanızı yapılandırılmış Spark havuzuna gönderebilirsiniz. Uygulama çalışır duruma geçtikten sonra çalışma kitabı açılır listesinden çalışan uygulamayı seçin.

> [!div class="mx-imgBorder"]
> ![çalışma kitabı ımange](./media/apache-spark-azure-log-analytics/workbook.png)

Çalışma kitabını kusto sorgulama ve Uyarıları yapılandırma ile özelleştirebilirsiniz.

> [!div class="mx-imgBorder"]
> ![kusto sorgusu ve uyarılar](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="sample-kusto-queries"></a>Örnek kusto sorguları

1. Sorgu Spark olayları örneği.

   ```kusto
   SparkListenerEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100 
   ```

2. Sorgu Spark uygulama sürücüsü ve yürütme günlükleri örneği.

   ```kusto
   SparkLoggingEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100
   ```

3. Sorgu Spark ölçümleri örneği.

   ```kusto
   SparkMetrics_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | where name_s endswith "jvm.total.used"
   | summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
   | order by TimeGenerated asc
   ```

## <a name="create-and-manage-alerts-using-azure-log-analytics"></a>Azure Log Analytics kullanarak uyarı oluşturma ve yönetme

Azure Izleyici uyarıları, kullanıcıların ölçümleri değerlendirmek için Log Analytics bir sorgu kullanmasına izin verir ve her ayarlama sıklığını kaydeder ve sonuçlara göre bir uyarı tetiklemez.

Daha fazla bilgi için bkz. [Azure izleyici kullanarak günlük uyarıları oluşturma, görüntüleme ve yönetme](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log).

## <a name="limitation"></a>Sınırlama

 - [Yönetilen sanal ağ](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-vnet) etkinleştirilmiş Azure SYNAPSE Analytics çalışma alanı desteklenmiyor.
 - Şu bölgeler Şu anda desteklenmemektedir:
   - Doğu ABD 2
   - Norveç Doğu
   - BAE Kuzey

## <a name="next-steps"></a>Sonraki adımlar

 - [SYNAPSE Studio 'da sunucusuz Apache Spark havuzunu nasıl kullanacağınızı](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)öğrenin.
 - [Not defterinde Spark uygulamasının nasıl çalıştırılacağını](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-development-using-notebooks)öğrenin.
 - [SYNAPSE Studio 'da Apache Spark iş tanımı oluşturmayı](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-job-definitions)öğrenin.