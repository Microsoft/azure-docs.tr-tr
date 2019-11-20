---
title: Depolama Analizi günlüklerini kullanarak gecikme sorunlarını giderme
description: Azure depolama analitik günlüklerini kullanarak gecikme sorunlarını tanımlayıp sorun giderin ve istemci uygulamasını iyileştirin.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
ms.openlocfilehash: f8ec8e3f27e74f442f314d4c812908888598244c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180634"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Depolama Analizi günlüklerini kullanarak gecikme sorunlarını giderme

Tanılama ve sorun giderme, Azure depolama ile istemci uygulamaları oluşturmaya ve desteklemeye yönelik bir temel yetenbedir.

Bir Azure uygulamasının dağıtılmış doğası nedeniyle, hataları ve performans sorunlarını tanılamak ve sorunlarını gidermek geleneksel ortamlarından daha karmaşık olabilir.

Aşağıdaki adımlarda, Azure Storage analitik günlüklerini kullanarak gecikme sorunlarının nasıl tanımlanacağına ve giderileceğini ve istemci uygulamasının en iyileştirilmesi gösterilmektedir.

## <a name="recommended-steps"></a>Önerilen adımlar

1. [Depolama Analizi günlüklerini](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data)indirin.

2. Ham biçim günlüklerini tablo biçimine dönüştürmek için aşağıdaki PowerShell betiğini kullanın:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. Betik, aşağıda gösterildiği gibi, bilgileri sütunlara göre filtreleyebileceğiniz bir GUI penceresi başlatır.

   ![Depolama analitik günlüğü ayrıştırıcı penceresi](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. "İşlem-türü" temelinde günlük girdilerini daraltın ve sorunun zaman çerçevesinde oluşturulan günlük girişini arayın.

   ![İşlem türü günlük girişleri](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Sorunun oluştuğu zaman sırasında aşağıdaki değerler önemlidir:

   * İşlem-tür = GetBlob
   * istek-durum = SASNetworkError
   * Uçtan uca gecikme-MS = 8453
   * Sunucu-yanıt-MS = 391

   Uçtan uca gecikme aşağıdaki denklem kullanılarak hesaplanır:

   * Uçtan uca gecikme = sunucu gecikme süresi + Istemci gecikmesi

   Günlük girişini kullanarak Istemci gecikmesini hesaplayın:

   * İstemci gecikmesi = uçtan uca gecikme süresi – sunucu gecikme süresi

          * Example: 8453 – 391 = 8062ms

   Aşağıdaki tabloda, yüksek gecikmeli OperationType ve RequestStatus sonuçları hakkında bilgi verilmektedir:

   |   |RequestStatus =<br>Başarılı|RequestStatus =<br>'LARıNıN NetworkError|Öneri|
   |---|---|---|---|
   |GetBlob|Yes|Hayır|[**GetBlob işlemi:** RequestStatus = başarılı](#getblob-operation-requeststatus--success)|
   |GetBlob|Hayır|Yes|[**GetBlob işlemi:** RequestStatus = (SAS) NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Yes|Hayır|[**PUT işlemi:** RequestStatus = başarılı](#put-operation-requeststatus--success)|
   |PutBlob|Hayır|Yes|[**PUT işlemi:** RequestStatus = (SAS) NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Durum sonuçları

### <a name="getblob-operation-requeststatus--success"></a>GetBlob Işlemi: RequestStatus = başarılı

"Önerilen adımlar" bölümünün 5. adımında bahsedilen şekilde aşağıdaki değerleri kontrol edin:

* Uçtan uca gecikme
* Sunucu gecikme süresi
* İstemci gecikme süresi

**RequestStatus = Success**Ile bir **GetBlob Işleminde** , **Istemci gecikmesi**için **en uzun süre** harcanması durumunda bu, Azure depolama 'nın istemciye veri yazma sırasında büyük bir süre harcadığını gösterir. Bu gecikme, Istemci tarafı bir sorunu gösterir.

**Önerilen**

* İstemcinizdeki kodu araştırın.
* İstemciden gelen ağ bağlantısı sorunlarını araştırmak için Wireshark, Microsoft Ileti Çözümleyicisi veya Tcping kullanın. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob Işlemi: RequestStatus = (SAS) NetworkError

"Önerilen adımlar" bölümünün 5. adımında bahsedilen şekilde aşağıdaki değerleri kontrol edin:

* Uçtan uca gecikme
* Sunucu gecikme süresi
* İstemci gecikme süresi

**RequestStatus = (SAS) NetworkError**Ile bir **GetBlob Işleminde** , **Istemci gecikmesi**için **en uzun süre** harcaniyorsa en yaygın sorun, depolama hizmetindeki zaman aşımı süresi dolmadan önce istemcinin bağlantısının kesilmesi durumundadır.

**Önerilen**

* İstemcinin depolama hizmetinden ne zaman ve ne zaman bağlantısını kesmediğini anlamak için, istemcinizdeki kodu araştırın.
* İstemciden gelen ağ bağlantısı sorunlarını araştırmak için Wireshark, Microsoft Ileti Çözümleyicisi veya Tcping kullanın. 

### <a name="put-operation-requeststatus--success"></a>Put Işlemi: RequestStatus = başarılı

"Önerilen adımlar" bölümünün 5. adımında bahsedilen şekilde aşağıdaki değerleri kontrol edin:

* Uçtan uca gecikme
* Sunucu gecikme süresi
* İstemci gecikme süresi

**RequestStatus = Success**Ile bir **PUT Işleminde** , **Istemci gecikmesi**için **en uzun süre** harcanması durumunda bu, istemcinin Azure depolama 'ya veri göndermek için daha fazla zaman aldığını gösterir. Bu gecikme, Istemci tarafı bir sorunu gösterir.

**Önerilen**

* İstemcinizdeki kodu araştırın.
* İstemciden gelen ağ bağlantısı sorunlarını araştırmak için Wireshark, Microsoft Ileti Çözümleyicisi veya Tcping kullanın. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Put Işlemi: RequestStatus = (SAS) NetworkError

"Önerilen adımlar" bölümünün 5. adımında bahsedilen şekilde aşağıdaki değerleri kontrol edin:

* Uçtan uca gecikme
* Sunucu gecikme süresi
* İstemci gecikme süresi

**Istek durumu = (SAS) NetworkError**Ile bir **PutBlob Işleminde** , **Istemci gecikmesi**için **en uzun süre** harcaniyorsa, en sık karşılaşılan sorun, depolama hizmetindeki zaman aşımı süresi dolmadan önce istemcinin bağlantısının kesilmesi durumundadır.

**Önerilen**

* İstemcinin depolama hizmetinden ne zaman ve ne zaman bağlantısını kesmediğini anlamak için, istemcinizdeki kodu araştırın.
* İstemciden gelen ağ bağlantısı sorunlarını araştırmak için Wireshark, Microsoft Ileti Çözümleyicisi veya Tcping kullanın.

