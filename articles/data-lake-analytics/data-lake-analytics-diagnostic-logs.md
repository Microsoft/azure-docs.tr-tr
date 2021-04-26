---
title: Azure Data Lake Analytics için tanılama günlüklerini etkinleştirme ve görüntüleme
description: Azure Data Lake Analytics için tanılama günlüklerini ayarlamayı ve erişmeyi öğrenin
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 02/12/2018
ms.openlocfilehash: cd339729f2300ff7e13e7422bf73373b4ce4658e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221018"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics’te tanılama günlüklerine erişim

Tanılama günlüğü, veri erişim denetim izlerini toplamanıza olanak tanır. Bu Günlükler aşağıdaki gibi bilgiler sağlar:

* Verilere erişen kullanıcıların listesi.
* Verilere ne sıklıkta erişilir.
* Hesapta ne kadar veri depolanıyor.

## <a name="enable-logging"></a>Günlü kaydını etkinleştir

1. [Azure Portal](https://portal.azure.com)oturum açın.

2. Data Lake Analytics hesabınızı açın ve __izleme__ bölümünden **tanılama günlükleri** ' ni seçin. Sonra __tanılamayı aç__' ı seçin.

    !["Tanılama günlükleri" eyleminin seçili olduğunu ve "aşağıdaki günlükleri toplamak için tanılamayı aç" vurgulandığı ekran görüntüsü.](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. __Tanılama ayarlarından__, bu günlüğe kaydetme yapılandırması Için bir __ad__ girin ve ardından günlük seçeneklerini belirleyin.

    ![Denetim ve istek günlüklerini toplamak için tanılamayı açın](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Tanılama günlüklerini etkinleştirme")

   * Verileri üç farklı şekilde depolamayı/işlemeyi tercih edebilirsiniz.

     * Günlükleri bir Azure depolama hesabında depolamak için __depolama hesabına arşiv__ ' i seçin. Verileri arşivlemek istiyorsanız bu seçeneği kullanın. Bu seçeneği belirlerseniz, günlüklerin kaydedileceği bir Azure depolama hesabı sağlamanız gerekir.

     * Günlük verilerinin bir Azure Olay Hub 'ına akışını sağlamak için **bir olay hub 'ına akış** ' yı seçin. Gelen günlükleri gerçek zamanlı olarak analiz eden bir aşağı akış işlem ardışık düzenine sahipseniz bu seçeneği kullanın. Bu seçeneği belirlerseniz, kullanmak istediğiniz Azure Olay Hub 'ının ayrıntılarını sağlamanız gerekir.

     * Verileri Azure Izleyici hizmetine göndermek için __Log Analytics gönder__ ' i seçin. Günlükleri toplamak ve analiz etmek için Azure Izleyici günlüklerini kullanmak istiyorsanız bu seçeneği kullanın.
   * Denetim günlüklerini veya istek günlüklerini mi yoksa her ikisini mi almak istediğinizi belirtin.  İstek günlüğü her API isteğini yakalar. Bir denetim günlüğü, bu API isteği tarafından tetiklenen tüm işlemleri kaydeder.

   * __Bir depolama hesabına arşivleme__ için, verilerin saklanacağı gün sayısını belirtin.

   * __Kaydet__’e tıklayın.

        > [!NOTE]
        > __Kaydet__ düğmesine tıklamadan önce __bir depolama hesabına arşiv__, __bir olay hub 'ına akış__ veya __Log Analytics gönder__ ' i seçmeniz gerekir.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Günlük verilerini içeren Azure Depolama hesabını kullanma

1. Günlüğe kaydetme verilerini tutan blob kapsayıcılarını göstermek için Data Lake Analytics için kullanılan Azure Depolama hesabını açın ve sonra __Bloblar__' a tıklayın.

   * Kapsayıcı **öngörüleri-Günlükler-denetim** denetim günlüklerini içerir.
   * Kapsayıcı **öngörüleri-Günlükler-istekler** istek günlüklerini içerir.

2. Kapsayıcılar içinde Günlükler aşağıdaki dosya yapısı altında depolanır:

   ```text
   resourceId=/
     SUBSCRIPTIONS/
       <<SUBSCRIPTION_ID>>/
         RESOURCEGROUPS/
           <<RESOURCE_GRP_NAME>>/
             PROVIDERS/
               MICROSOFT.DATALAKEANALYTICS/
                 ACCOUNTS/
                   <DATA_LAKE_ANALYTICS_NAME>>/
                     y=####/
                       m=##/
                         d=##/
                           h=##/
                             m=00/
                               PT1H.json
   ```

   > [!NOTE]
   > `##`Yoldaki girişler, günlüğün oluşturulduğu yılı, ayı, günü ve saati içerir. Data Lake Analytics her saat bir dosya oluşturur, bu nedenle `m=` her zaman bir değeri içerir `00` .

    Örnek olarak, bir denetim günlüğüne yönelik yolun tamamı şöyle olabilir:

    `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Benzer şekilde, bir istek günlüğüne yönelik yolun tamamı şu olabilir:

    `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="log-structure"></a>Günlük yapısı

Denetim ve istek günlükleri yapılandırılmış bir JSON biçimindedir.

### <a name="request-logs"></a>İstek günlükleri

JSON biçimli istek günlüğünde örnek bir giriş aşağıda verilmiştir. Her Blobun, bir dizi günlük nesnesi içeren **kayıtlar** adlı bir kök nesnesi vardır.

```json
{
"records":
  [
    . . . .
    ,
    {
         "time": "2016-07-07T21:02:53.456Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
         "category": "Requests",
         "operationName": "GetAggregatedJobHistory",
         "resultType": "200",
         "callerIpAddress": "::ffff:1.1.1.1",
         "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
         "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
         "properties": {
             "HttpMethod":"POST",
             "Path":"/JobAggregatedHistory",
             "RequestContentLength":122,
             "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
             "StartTime":"2016-07-07T21:02:52.472Z",
             "EndTime":"2016-07-07T21:02:53.456Z"
             }
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>İstek günlüğü şeması

| Ad | Tür | Description |
| --- | --- | --- |
| time |Dize |Günlüğün zaman damgası (UTC) |
| resourceId |Dize |İşlemin gerçekleştiği kaynağın tanımlayıcısı |
| category |Dize |Günlük kategorisi. Örneğin, **istekler**. |
| operationName |Dize |Günlüğe kaydedilen işlemin adı. Örneğin, Getaggregısjobhistory. |
| resultType |Dize |İşlemin durumu, örneğin, 200. |
| callerIpAddress |Dize |İsteği yapan istemcinin IP adresi |
| correlationId |Dize |Günlüğün tanımlayıcısı. Bu değer, bir dizi ilgili günlük girişini gruplandırmak için kullanılabilir. |
| identity |Nesne |Günlüğü oluşturan kimlik |
| properties |JSON |Ayrıntılar için sonraki bölüme (Istek günlüğü özellikleri şeması) bakın |

#### <a name="request-log-properties-schema"></a>İstek günlüğü özellikleri şeması

| Ad | Tür | Description |
| --- | --- | --- |
| HttpMethod |Dize |İşlem için kullanılan HTTP yöntemi. Örneğin, GET. |
| Yol |Dize |İşlemin gerçekleştirildiği yol |
| RequestContentLength |int |HTTP isteğinin içerik uzunluğu |
| Clientrequestıd 'ye sahip |Dize |Bu isteği benzersiz bir şekilde tanımlayan tanımlayıcı |
| StartTime |Dize |Sunucunun isteği aldığı zaman |
| EndTime |Dize |Sunucunun yanıt gönderdiği zaman |

### <a name="audit-logs"></a>Denetim günlükleri

JSON biçimli denetim günlüğünde örnek bir giriş aşağıda verilmiştir. Her Blobun, bir dizi günlük nesnesi içeren **kayıtlar** adlı bir kök nesnesi vardır.

```json
{
"records":
  [
    {
         "time": "2016-07-28T19:15:16.245Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
         "category": "Audit",
         "operationName": "JobSubmitted",
         "identity": "user@somewhere.com",
         "properties": {
             "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
             "JobName": "New Job",
             "JobRuntimeName": "default",
             "SubmitTime": "7/28/2016 7:14:57 PM"
             }
    }
  ]
}
```

#### <a name="audit-log-schema"></a>Denetim günlüğü şeması

| Ad | Tür | Description |
| --- | --- | --- |
| time |Dize |Günlüğün zaman damgası (UTC) |
| resourceId |Dize |İşlemin gerçekleştiği kaynağın tanımlayıcısı |
| category |Dize |Günlük kategorisi. Örneğin, **Denetim**. |
| operationName |Dize |Günlüğe kaydedilen işlemin adı. Örneğin, Jobgönderildi. |
| resultType |Dize |İş durumu (operationName) için bir alt durum. |
| resultSignature |Dize |İş durumu (operationName) hakkında ek ayrıntılar. |
| identity |Dize |İşlemi isteyen kullanıcı. Örneğin, susan@contoso.com. |
| properties |JSON |Ayrıntılar için sonraki bölüme (denetim günlüğü özellikleri şeması) bakın |

> [!NOTE]
> **ResultType** ve **resultsignature** bir işlemin sonucu hakkında bilgi sağlar ve yalnızca bir işlem tamamlanırsa bir değer içerir. Örneğin, **OperationName** bir **Jobstarted** veya **jobstarted** değeri içerdiğinde yalnızca bir değer içerirler.
>
>

#### <a name="audit-log-properties-schema"></a>Denetim günlüğü özellikleri şeması

| Ad | Tür | Description |
| --- | --- | --- |
| JobId |Dize |İşe atanan KIMLIK |
| JobName |Dize |İş için belirtilen ad |
| JobRunTime |Dize |İşi işlemek için kullanılan çalışma zamanı |
| Gönderim Zamanı |Dize |İşin gönderildiği saat (UTC) |
| StartTime |Dize |Gönderimden sonra işin çalışmaya başladığı zaman (UTC) |
| EndTime |Dize |İşin bitiş saati |
| Paralellik |Dize |Gönderim sırasında bu iş için istenen Data Lake Analytics birim sayısı |

> [!NOTE]
> **Submittime**, **StartTime**, **BitişZamanı** ve **paralellik** , bir işlem hakkında bilgi sağlar. Bu girişler yalnızca, işlem başlatılmış veya tamamlandığında bir değer içerir. Örneğin, **Submittime** yalnızca **OperationName** değerinden **jobgönderildi** değeri varsa bir değer içerir.

## <a name="process-the-log-data"></a>Günlük verilerini işleme

Azure Data Lake Analytics günlük verilerini işleme ve çözümleme hakkında bir örnek sağlar. Örneği adresinde bulabilirsiniz [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) .

## <a name="next-steps"></a>Sonraki adımlar

[Azure Data Lake Analytics’e Genel Bakış](data-lake-analytics-overview.md)
