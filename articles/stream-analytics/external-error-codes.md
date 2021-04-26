---
title: Dış hata kodları-Azure Stream Analytics
description: Dış hata kodlarıyla ilgili Azure Stream Analytics sorunları giderin.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9f55a715b11b126ea340e665e008d7245e578190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016397"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Azure Stream Analytics dış hata kodları

Azure Stream Analytics işinizin beklenmedik davranışlarından hata ayıklamaya yardımcı olması için etkinlik günlüklerini ve kaynak günlüklerini kullanabilirsiniz. Bu makalede, her dış hata kodu için açıklama listelenir. Dış hatalar, bir yukarı akış veya aşağı akış hizmeti tarafından oluşturulan ve Stream Analytics veri hatası, yapılandırma hatası veya dış kullanılabilirlik hatası olarak ayıramıyorum genel hatalardır.

## <a name="adapterinitializationerror"></a>Adapterınitializationerror

* **Neden**: bağdaştırıcı başlatılırken bir hata oluştu.

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **Neden**: bir bağdaştırıcıya veri yazılırken bir hata oluştu.

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **Neden**: Azure IŞLEVLERINDEN bir HTTP hatası döndürüldü.

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **Neden**: Stream Analytics olayları Azure işlevine yazamadı.

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **Neden**: Azure işlevlerine çıktı alırken bir yeniden yönlendirme hatası var.

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **Neden**: Azure işlevlerine çıktı olarak bir istemci hatası var.

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **Neden**: Azure işlevlerine çıktı olarak bir sunucu hatası var.

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **Neden**: http isteği zaman aşımını aştığından Azure işlevlerine yazma işlemi başarısız oldu. 
* **Öneri**: olası gecikmeler Için Azure işlevlerinizin günlüklerine bakın.

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **Neden**: giriş uzaklıkları geçersiz. Bunun nedeni yük devretme olabilir.
* **Öneri**: Stream Analytics işinizi son çıkış zamanından yeniden başlatın.

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **Neden**: Olay Hub 'ına veri gönderilirken bir hata oluştu.

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>Cosmosdbconnectionfailureaftermaxdenemeler

* **Neden**: Stream Analytics en fazla yeniden deneme sayısından sonra Cosmos DB hesabına bağlanılamadı.

## <a name="cosmosdbfailureaftermaxretries"></a>Cosmosdbfailureaftermaxdenemeler

* **Neden**: Stream Analytics Cosmos db veritabanını ve koleksiyonu en fazla yeniden deneme sayısından sonra sorguleyemedi.

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **Neden**: cosmosdb birkaç yeniden denemeden sonra saklı yordam oluşturamaz.

## <a name="cosmosdboutputrequesttimeout"></a>Cosmosdi Putrequesttimeout

* **Neden**: upsert saklı yordam bir hata döndürdü. 

## <a name="sqldatabaseoutputinitializationerror"></a>Sqldatabaseoutputınitializationerror

* **Neden**: Stream Analytics SQL veritabanı çıkışını başlatamıyor.

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **Neden**: Stream Analytics SQL veritabanı çıktısına olay yazamaz.

## <a name="sqldwoutputinitializationerror"></a>Sqldwoutputınitializationerror

* **Neden**: ADANMıŞ bir SQL havuzu çıkışı başlatılırken bir hata oluştu.

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **Neden**: özel bir SQL havuzuna çıkış yazılırken bir hata oluştu.

## <a name="next-steps"></a>Sonraki adımlar

* [Giriş bağlantısı sorunlarını giderme](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics çıkışları sorunlarını giderme](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics sorgularının sorunlarını giderme](stream-analytics-troubleshoot-query.md)
* [Kaynak günlüklerini kullanarak Azure Stream Analytics sorunlarını giderme](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics veri hataları](data-errors.md)
