---
title: Veri hata kodları-Azure Stream Analytics
description: Veri hata kodlarıyla ilgili Azure Stream Analytics sorunları giderin.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 33e617c8c3589d76d649dd2ea2236a4247b12500
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016499"
---
# <a name="azure-stream-analytics-data-error-codes"></a>Azure Stream Analytics veri hata kodları

Azure Stream Analytics işinizin beklenmedik davranışlarından hata ayıklamaya yardımcı olması için etkinlik günlüklerini ve kaynak günlüklerini kullanabilirsiniz. Bu makalede, her veri hata hata kodu için açıklama listelenir. Akışta beklenmeyen bir kayıt şeması gibi hatalı veriler olduğunda veri hataları oluşur.

## <a name="inputdeserializationerror"></a>Inputdeserializationerror

* **Neden**: giriş verilerinin serisi kaldırılırken bir hata oluştu.

## <a name="inputeventtimestampnotfound"></a>Inputeventtimestamp NotFound

* **Neden**: Stream Analytics kaynak için zaman damgası alamıyor. 

## <a name="inputeventtimestampbyovervaluenotfound"></a>Inputeventtimestamp Byovervaluenotfound

* **Neden**: Stream Analytics değerini alamadı `TIMESTAMP BY OVER COLUMN` .

## <a name="inputeventlatebeyondthreshold"></a>Inputeventlatebeyondthreshold

* **Neden**: bir giriş olayı, yapılandırılan toleranstan daha sonra gönderildi.

## <a name="inputeventearlybeyondthreshold"></a>Inputeventearlybeyondthreshold

* **Neden**: giriş olayı varış saati, giriş olay uygulaması zaman damgası eşiğinden daha önce.

## <a name="azurefunctionmessagesizeexceeded"></a>AzureFunctionMessageSizeExceeded

* **Neden**: Azure işlevlerine giden ileti çıktısı boyut sınırını aşıyor.

## <a name="eventhuboutputrecordexceedssizelimit"></a>Eventhui Putrecordexceedssizelimit

* **Neden**: bir çıkış kaydı, Olay Hub 'ına yazarken en büyük boyut sınırını aşıyor.

## <a name="cosmosdboutputinvalidid"></a>Cosmosdi Putınvalidıd

* **Neden**: belirli bir sütunun değeri veya türü geçersiz.
* **Öneri**: 255 karakterden daha uzun olmayan benzersiz olmayan boş dizeler sağlar.

## <a name="cosmosdboutputinvalididcharacter"></a>CosmosDBOutputInvalidIdCharacter

* **Neden**: çıkış KAYDıNıN belge kimliği geçersiz bir karakter içeriyor.

## <a name="cosmosdboutputmissingid"></a>Cosmosdi Putmissingıd

* **Neden**: çıkış kaydı, \[ birincil anahtar özelliği olarak kullanılacak olan sütun kimliğini içermiyor.

## <a name="cosmosdboutputmissingidcolumn"></a>CosmosDBOutputMissingIdColumn

* **Neden**: çıkış kaydı belge kimliği özelliğini içermiyor. 
* **Öneri**: sorgu çıkışının ' 255 ' karakterden küçük, boş olmayan benzersiz bir dizeye sahip sütunu içerdiğinden emin olun.

## <a name="cosmosdboutputmissingpartitionkey"></a>Cosmosdi Putmissingpartitionkey

* **Neden**: çıkış kaydında bölüm anahtarı özelliği olarak kullanılacak bir sütun eksik.

## <a name="cosmosdboutputsinglerecordtoolarge"></a>CosmosDBOutputSingleRecordTooLarge

* **Neden**: Cosmos DB için tek bir kayıt yazma çok büyük.

## <a name="sqldatabaseoutputdataerror"></a>SQLDatabaseOutputDataError

* **Neden**: Stream Analytics verilerdeki sorunlar nedeniyle SQL veritabanına olay (ler) yazamaz.

## <a name="next-steps"></a>Sonraki adımlar

* [Giriş bağlantısı sorunlarını giderme](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics çıkışları sorunlarını giderme](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics sorgularının sorunlarını giderme](stream-analytics-troubleshoot-query.md)
* [Kaynak günlüklerini kullanarak Azure Stream Analytics sorunlarını giderme](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics veri hataları](data-errors.md)
