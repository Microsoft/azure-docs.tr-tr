---
title: Azure Otomasyonu VM'leri çalışma saatleri dışında başlat/durdur sorgu günlükleri
description: Bu makalede, Azure Izleyici 'nin VM'leri çalışma saatleri dışında başlat/durdur tarafından oluşturulan günlük verilerini sorgulamak için nasıl kullanılacağı açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 3e9e924d6626d9f0dcd2db8a5e8b8f90a0aa01ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593848"
---
# <a name="query-logs-from-startstop-vms-during-off-hours"></a>VM'leri çalışma saatleri dışında başlat/durdur özelliğinden sorgu günlükleri

Azure Otomasyonu, iki kayıt türünü bağlı Log Analytics çalışma alanına iletir: iş günlükleri ve iş akışları. Bu makalede, Azure Izleyici 'de [sorgu](../azure-monitor/logs/log-query-overview.md) için kullanılabilen veriler incelenir.

## <a name="job-logs"></a>İş günlükleri

|Özellik | Açıklama|
|----------|----------|
|Çağıran |  İşlemi başlatandır. Olası değerler, bir e-posta adresi veya zamanlanan işlere yönelik bir sistemdir.|
|Kategori | Veri türü sınıflandırması. Otomasyon için değer JobLogs olacaktır.|
|CorrelationId | Runbook işinin bağıntı KIMLIĞI olan GUID.|
|JobId | Runbook işinin KIMLIĞI olan GUID.|
|operationName | Azure’da gerçekleştirilen işlem türünü belirtir. Otomasyon için değer Iş olur.|
|resourceId | Azure’daki kaynak türünü belirtir. Otomasyon için değer, runbook ile ilişkilendirilmiş Otomasyon hesabı olacaktır.|
|adlı yönetilen örnek, | Runbook işine ait kaynak grubunun adını belirtir.|
|ResourceProvider | Dağıtıp yönetebileceğiniz kaynakları sağlayan Azure hizmetini belirtir. Otomasyon için değer, Azure Otomasyonu olacaktır.|
|ResourceType | Azure’daki kaynak türünü belirtir. Otomasyon için değer, runbook ile ilişkilendirilmiş Otomasyon hesabı olacaktır.|
|resultType | Runbook işinin durumudur. Olası değerler şunlardır:<br>- Başlatıldı<br>- Durduruldu<br>- Askıya alındı<br>- Başarısız oldu<br>- Başarılı oldu|
|resultDescription | Runbook iş sonucu durumunu açıklar. Olası değerler şunlardır:<br>- İş başlatıldı<br>- İş Başarısız Oldu<br>- İş Tamamlandı|
|RunbookName | Runbook’un adını belirtir.|
|SourceSystem | Gönderilen verilere ilişkin kaynak sistemi belirtir. Otomasyon için, değer OpsManager ' dır|
|StreamType | Olay türünü belirtir. Olası değerler şunlardır:<br>- Ayrıntılı<br>- Çıktı<br>- Hata<br>- Uyarı|
|SubscriptionId | İşin abonelik kimliğini belirtir.
|Saat | Runbook işinin yürütüldüğü tarih ve saat.|

## <a name="job-streams"></a>İş akışları

|Özellik | Açıklama|
|----------|----------|
|Çağıran |  İşlemi başlatandır. Olası değerler, bir e-posta adresi veya zamanlanan işlere yönelik bir sistemdir.|
|Kategori | Veri türü sınıflandırması. Otomasyon için değer JobStreams olacaktır.|
|JobId | Runbook işinin KIMLIĞI olan GUID.|
|operationName | Azure’da gerçekleştirilen işlem türünü belirtir. Otomasyon için değer Iş olur.|
|adlı yönetilen örnek, | Runbook işine ait kaynak grubunun adını belirtir.|
|resourceId | Azure 'da kaynak KIMLIĞINI belirtir. Otomasyon için değer, runbook ile ilişkilendirilmiş Otomasyon hesabı olacaktır.|
|ResourceProvider | Dağıtıp yönetebileceğiniz kaynakları sağlayan Azure hizmetini belirtir. Otomasyon için değer, Azure Otomasyonu olacaktır.|
|ResourceType | Azure’daki kaynak türünü belirtir. Otomasyon için değer, runbook ile ilişkilendirilmiş Otomasyon hesabı olacaktır.|
|resultType | Runbook işinin, olayın oluşturulduğu andaki sonucu. Olası bir değer:<br>- Devam Ediyor|
|resultDescription | Runbook’un çıktı akışını içerir.|
|RunbookName | Runbook’un adı.|
|SourceSystem | Gönderilen verilere ilişkin kaynak sistemi belirtir. Otomasyon için, değer OpsManager ' dır.|
|StreamType | İş akışı türü. Olası değerler şunlardır:<br>-İlerleme durumu<br>- Çıktı<br>- Uyarı<br>- Hata<br>- Hata ayıklama<br>- Ayrıntılı|
|Saat | Runbook işinin yürütüldüğü tarih ve saat.|

**Joblogs** veya **jobstreams** kategorisi kayıtlarını döndüren bir günlük araması gerçekleştirdiğinizde, arama tarafından döndürülen güncelleştirmeleri özetleyen bir kutucuk kümesi görüntüleyen **joblogs** veya **jobstreams** görünümünü seçebilirsiniz.

## <a name="sample-log-searches"></a>Örnek günlük aramaları

Aşağıdaki tabloda, VM'leri çalışma saatleri dışında başlat/durdur tarafından toplanan iş kayıtlarına yönelik örnek günlük aramaları sunulmaktadır.

|Sorgu | Description|
|----------|----------|
|Başarıyla tamamlanan runbook ScheduledStartStop_Parent işleri bulun | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Runbook ScheduledStartStop_Parent başarıyla tamamlanmamış olan işleri bulun | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Başarıyla tamamlanan runbook SequencedStartStop_Parent işleri bulun | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Runbook SequencedStartStop_Parent başarıyla tamamlanmamış olan işleri bulun | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Sonraki adımlar

* Özelliği ayarlamak için, bkz. [çalışma saatleri dışında VM 'Leri durdurmayı/başlatmayı yapılandırma](automation-solution-vm-management-config.md).
* Özellik dağıtımı sırasında günlük uyarıları hakkında daha fazla bilgi için bkz. [Azure izleyici ile günlük uyarıları oluşturma](../azure-monitor/alerts/alerts-log.md).
* Özellik hatalarını çözümlemek için bkz. [sorun giderme VM'leri çalışma saatleri dışında Başlat/Durdur sorunları](troubleshoot/start-stop-vm.md).