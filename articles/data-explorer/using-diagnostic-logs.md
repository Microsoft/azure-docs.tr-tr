---
title: Tanılama günlüklerini kullanarak Azure Veri Gezgini alma işlemlerini izleme
description: Alma işlemlerini izlemek için Azure Veri Gezgini tanılama günlüklerini ayarlamayı öğrenin.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 13f86f0156299619d8bf8d92eb92bbcf8b4cb76c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173808"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Tanılama günlüklerini kullanarak Azure Veri Gezgini alma işlemlerini izleme (Önizleme)

Azure Veri Gezgini uygulamalar, web siteleri, IoT cihazları ve daha fazlasından akışı yapılan büyük miktarda veri üzerinde gerçek zamanlı analiz yapmaya yönelik hızlı ve tam olarak yönetilen bir veri analizi hizmetidir. Azure Veri Gezgini kullanmak için, önce bir küme oluşturun ve bu kümede bir veya daha fazla veritabanı oluşturursunuz. Daha sonra sorguları bu verilere karşı çalıştırmak için bir veritabanındaki tabloya (yükleme) sahip olursunuz. [Azure izleyici tanılama günlükleri](/azure/azure-monitor/platform/diagnostic-logs-overview) , Azure kaynakları 'nın çalışması hakkında veri sağlar. Azure Veri Gezgini, alma başarıları ve hatalarıyla ilgili Öngörüler için tanılama günlüklerini kullanır. Alma durumunu izlemek için işlem günlüklerini Azure depolama, Olay Hub 'ı veya Log Analytics dışarı aktarabilirsiniz. Azure depolama ve Azure Olay Hub 'ından Günlükler, daha fazla analiz için Azure Veri Gezgini kümenizdeki bir tabloya yönlendirilebilir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/)oluşturun.
* Bir [küme ve veritabanı](create-cluster-database-portal.md)oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Azure Veri Gezgini kümesi için tanılama günlüklerini ayarlama

Tanılama günlükleri, aşağıdaki günlük verilerinin toplanmasını yapılandırmak için kullanılabilir:
* Başarılı alma işlemleri: Bu günlüklerde başarıyla tamamlanan alma işlemleri hakkında bilgiler vardır.
* Başarısız alma işlemleri: Bu günlüklerde hata ayrıntıları da dahil olmak üzere başarısız alma işlemleriyle ilgili ayrıntılı bilgiler vardır. 

Veriler daha sonra bir depolama hesabında arşivlenir, bir olay hub 'ına kaydedilir ya da belirtimlerinize göre Log Analytics gönderilir.

### <a name="enable-diagnostic-logs"></a>Tanılama günlüklerini etkinleştirme

Tanılama günlükleri, varsayılan olarak devre dışıdır. Tanılama günlüklerini etkinleştirmek için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com), Izlemek istediğiniz Azure Veri Gezgini kümesi kaynağını seçin.
1. Altında **izleme**seçin **tanılama ayarları**.
  
    ![Tanılama günlükleri ekleme](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. **Tanılama ayarı Ekle**' yi seçin.
1. **Tanılama ayarları** penceresinde:
 
    ![Tanılama Ayarları Yapılandırması](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Tanılama ayarınız için **ad** seçin.
    1. Bir veya daha fazla hedef seçin: depolama hesabı, Olay Hub 'ı veya Log Analytics.
    1. Toplanacak günlükleri seçin: `SucceededIngestion` veya `FailedIngestion`.
    1. Toplanacak [ölçümleri](using-metrics.md) seçin (isteğe bağlı).   
    1. Yeni tanılama günlükleri ayarlarını ve ölçümlerini kaydetmek için **Kaydet** ' i seçin.
    1. Tanılama günlüklerini etkinleştirmeyi istemek için Azure portal **Yeni bir destek isteği** oluşturun.

Yeni ayarlar birkaç dakika içinde ayarlanacak. Günlükler daha sonra yapılandırılan arşiv hedefi (depolama hesabı, Olay Hub 'ı veya Log Analytics) içinde görüntülenir. 

## <a name="diagnostic-logs-schema"></a>Tanılama günlükleri şeması

Tüm [Azure izleyici tanılama günlükleri, ortak bir en üst düzey şemayı paylaşır](/azure/azure-monitor/platform/diagnostic-logs-schema). Azure Veri Gezgini kendi olayları için benzersiz özelliklere sahiptir. Tüm Günlükler JSON biçiminde depolanır.

### <a name="ingestion-logs-schema"></a>Alım günlükleri şeması

Günlük JSON dizeleri aşağıdaki tabloda listelenen öğeleri içerir:

|Ad               |Açıklama
|---                |---
|time               |Raporun saati
|resourceId         |Azure Resource Manager kaynak KIMLIĞI
|operationName      |İşlemin adı: ' MICROSOFT. KUSTO/KÜMELER/ALMA/EYLEM '
|operationVersion   |Şema sürümü: ' 1,0 ' 
|category           |İşlemin kategorisi. `SucceededIngestion` veya `FailedIngestion`. [Başarılı işlem](#successful-ingestion-operation-log) veya [başarısız işlem](#failed-ingestion-operation-log)için özellikler farklılık gösterir.
|properties         |İşlemin ayrıntılı bilgileri.

#### <a name="successful-ingestion-operation-log"></a>Başarılı alma işlemi günlüğü

**Örnek:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Başarılı bir işlem tanılama günlüğü özellikleri**

|Ad               |Açıklama
|---                |---
|succeededOn        |Alma işleminin tamamlanma süresi
|operationId        |Azure Veri Gezgini alma işlemi KIMLIĞI
|veritabanı           |Hedef veritabanının adı
|table              |Hedef tablonun adı
|ınestionsourceıd  |Alım veri kaynağının KIMLIĞI
|ınestionsourcepath|Alma veri kaynağının veya blob URI 'sinin yolu
|Rootactivityıd     |Etkinlik Kimliği

#### <a name="failed-ingestion-operation-log"></a>Başarısız alma işlemi günlüğü

**Örnek:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**Başarısız bir işlem tanılama günlüğünün özellikleri**

|Ad               |Açıklama
|---                |---
|failedOn           |Alma işleminin tamamlanma süresi
|operationId        |Azure Veri Gezgini alma işlemi KIMLIĞI
|veritabanı           |Hedef veritabanının adı
|table              |Hedef tablonun adı
|ınestionsourceıd  |Alım veri kaynağının KIMLIĞI
|ınestionsourcepath|Alma veri kaynağının veya blob URI 'sinin yolu
|Rootactivityıd     |Etkinlik Kimliği
|details            |Hatanın ve hata iletisinin ayrıntılı açıklaması
|errorCode          |Hata kodu 
|failureStatus      |`Permanent` veya `Transient`. Geçici bir hata yeniden deneniyorsa başarılı olabilir.
|originatesFromUpdatePolicy|Hata bir güncelleştirme ilkesinden kaynaklanıyorsa doğru
|shouldRetry        |Yeniden deneme başarılı olursa doğru

## <a name="next-steps"></a>Sonraki adımlar

* [Öğretici: Azure Veri Gezgini veri alma ve sorgu izleme](ingest-data-no-code.md)
* [Küme durumunu izlemek için ölçümleri kullanma](using-metrics.md)

