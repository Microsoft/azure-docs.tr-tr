---
title: Depolama Analizi günlüklerini kullanarak gecikme sorunlarını giderme
description: Azure depolama analitik günlüklerini kullanarak gecikme sorunlarını tanımlayıp sorun giderin ve istemci uygulamasını iyileştirin.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 1e6033f9a8f4cecd2429eca67a3d58e54d7ae1f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221117"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Depolama Analizi günlüklerini kullanarak gecikme sorunlarını giderme

Tanılama ve sorun giderme, Azure depolama ile istemci uygulamaları oluşturmaya ve desteklemeye yönelik bir temel yetenbedir.

Bir Azure uygulamasının dağıtılmış doğası nedeniyle, hataları ve performans sorunlarını tanılamak ve sorunlarını gidermek geleneksel ortamlarından daha karmaşık olabilir.

Aşağıdaki adımlarda, Azure Storage analitik günlüklerini kullanarak gecikme sorunlarının nasıl tanımlanacağına ve giderileceğini ve istemci uygulamasının en iyileştirilmesi gösterilmektedir.

## <a name="recommended-steps"></a>Önerilen adımlar

1. [Depolama Analizi günlüklerini](./manage-storage-analytics-logs.md#download-storage-logging-log-data)indirin.

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

   * Uçtan uca gecikme = Server-Latency + Istemci gecikmesi

   Günlük girişini kullanarak Istemci gecikmesini hesaplayın:

   * İstemci gecikmesi = uçtan uca gecikme süresi – Server-Latency

        Örnek: 8453 – 391 = 8062ms

   Aşağıdaki tabloda, yüksek gecikmeli OperationType ve RequestStatus sonuçları hakkında bilgi verilmektedir:

   | Blob türü |RequestStatus =<br>Başarılı|RequestStatus =<br>'LARıNıN NetworkError|Öneri|
   |---|---|---|---|
   |GetBlob|Yes|Hayır|[**GetBlob işlemi:** RequestStatus = başarılı](#getblob-operation-requeststatus--success)|
   |GetBlob|Hayır|Yes|[**GetBlob işlemi:** RequestStatus = (SAS) NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Yes|Hayır|[**PUT işlemi:** RequestStatus = başarılı](#put-operation-requeststatus--success)|
   |PutBlob|Hayır|Yes|[**PUT işlemi:** RequestStatus = (SAS) NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Durum sonuçları

### <a name="getblob-operation-requeststatus--success"></a>GetBlob Işlemi: RequestStatus = başarılı

"Önerilen adımlar" bölümünün 5. adımında bahsedilen şekilde aşağıdaki değerleri kontrol edin:

* Uçtan uca gecikme
* Server-Latency
* Client-Latency

**RequestStatus = Success** Ile bir **GetBlob Işleminde** , **Istemci gecikmesi** için **en uzun süre** harcanması durumunda bu, Azure depolama 'nın istemciye veri yazma sırasında büyük bir süre harcadığını gösterir. Bu gecikme Client-Side bir sorunu gösterir.

**Önerilen**

* İstemcinizdeki kodu araştırın.
* İstemciden gelen ağ bağlantısı sorunlarını araştırmak için Wireshark, Microsoft Ileti Çözümleyicisi veya Tcping kullanın. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob Işlemi: RequestStatus = (SAS) NetworkError

"Önerilen adımlar" bölümünün 5. adımında bahsedilen şekilde aşağıdaki değerleri kontrol edin:

* Uçtan uca gecikme
* Server-Latency
* Client-Latency

**RequestStatus = (SAS) NetworkError** Ile bir **GetBlob Işleminde** , **Istemci gecikmesi** için **en uzun süre** harcaniyorsa en yaygın sorun, depolama hizmetindeki zaman aşımı süresi dolmadan önce istemcinin bağlantısının kesilmesi durumundadır.

**Önerilen**

* İstemcinin depolama hizmetinden ne zaman ve ne zaman bağlantısını kesmediğini anlamak için, istemcinizdeki kodu araştırın.
* İstemciden gelen ağ bağlantısı sorunlarını araştırmak için Wireshark, Microsoft Ileti Çözümleyicisi veya Tcping kullanın. 

### <a name="put-operation-requeststatus--success"></a>Put Işlemi: RequestStatus = başarılı

"Önerilen adımlar" bölümünün 5. adımında bahsedilen şekilde aşağıdaki değerleri kontrol edin:

* Uçtan uca gecikme
* Server-Latency
* Client-Latency

**RequestStatus = Success** Ile bir **PUT Işleminde** , **Istemci gecikmesi** için **en uzun süre** harcanması durumunda bu, istemcinin Azure depolama 'ya veri göndermek için daha fazla zaman aldığını gösterir. Bu gecikme Client-Side bir sorunu gösterir.

**Önerilen**

* İstemcinizdeki kodu araştırın.
* İstemciden gelen ağ bağlantısı sorunlarını araştırmak için Wireshark, Microsoft Ileti Çözümleyicisi veya Tcping kullanın. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Put Işlemi: RequestStatus = (SAS) NetworkError

"Önerilen adımlar" bölümünün 5. adımında bahsedilen şekilde aşağıdaki değerleri kontrol edin:

* Uçtan uca gecikme
* Server-Latency
* Client-Latency

**Istek durumu = (SAS) NetworkError** Ile bir **PutBlob Işleminde** , **Istemci gecikmesi** için **en uzun süre** harcaniyorsa, en sık karşılaşılan sorun, depolama hizmetindeki zaman aşımı süresi dolmadan önce istemcinin bağlantısının kesilmesi durumundadır.

**Önerilen**

* İstemcinin depolama hizmetinden ne zaman ve ne zaman bağlantısını kesmediğini anlamak için, istemcinizdeki kodu araştırın.
* İstemciden gelen ağ bağlantısı sorunlarını araştırmak için Wireshark, Microsoft Ileti Çözümleyicisi veya Tcping kullanın.