---
title: Azure Stream Analytics hata kodlarında sorun giderme
description: İç hata kodlarıyla ilgili Azure Stream Analytics sorunları giderin.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: ddea74997850ff080f6c82d55d1be3cfb851deda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019389"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Azure Stream Analytics iç hata kodları

Azure Stream Analytics işinizin beklenmedik davranışlarından hata ayıklamaya yardımcı olması için etkinlik günlüklerini ve kaynak günlüklerini kullanabilirsiniz. Bu makalede, her dahili hata kodu için açıklama listelenir. İç hatalar, Stream Analytics hata bir Iç kullanılabilirlik hatası ya da sistemdeki bir hata olup olmadığını ayıramadığından Stream Analytics platformda oluşturulan genel hatalardır.

## <a name="cosmosdboutputbatchsizetoolarge"></a>Cosmosdi Putbatchsizetoolarge

* **Neden**: Cosmos DB yazmak için kullanılan toplu iş boyutu çok büyük. 
* **Öneri**: daha küçük bir toplu iş boyutuyla yeniden deneyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Giriş bağlantısı sorunlarını giderme](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics çıkışları sorunlarını giderme](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics sorgularının sorunlarını giderme](stream-analytics-troubleshoot-query.md)
* [Kaynak günlüklerini kullanarak Azure Stream Analytics sorunlarını giderme](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics veri hataları](data-errors.md)