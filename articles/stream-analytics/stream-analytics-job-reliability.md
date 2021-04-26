---
title: Azure Stream Analytics işlerinde hizmet kesintilerini önleyin
description: Bu makalede Stream Analytics işlerinin yükseltmesini dayanıklı hale getirme Kılavuzu açıklanmaktadır.
author: jseb225
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: f4eda779b1bc719706f9eb42cf805a7d5ce864aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020341"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Hizmet güncelleştirmeleri sırasında iş güvenilirliğini Stream Analytics garanti edin

Tam olarak yönetilen bir hizmetin bir parçası olarak, yeni hizmet işlevselliği ve iyileştirmeler hızla hızlı bir şekilde tanıtılmaktadır. Sonuç olarak, bir hizmet güncelleştirme dağıtımı için haftalık (veya daha sık) temelinde Stream Analytics. Ne kadar test edildiğine bakılmaksızın, var olan, çalışan bir işin bir hatanın tanıtımı nedeniyle kesintiye uğraması riskini ortadan kalmaz. Görev açısından kritik işler çalıştırıyorsanız, bu risklerin kaçınılması gerekir. Azure 'un **[eşleştirilmiş bölge](../best-practices-availability-paired-regions.md)** modelini izleyerek bu riski azaltabilirsiniz. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Azure eşlenmiş bölgeler bu sorunu nasıl ele veriyor?

Stream Analytics, eşleştirilmiş bölgelerdeki işlerin ayrı toplu işlerde güncelleştirilmesini güvence altına alır. Sonuç olarak, olası sorunları belirlemek ve düzeltmek için güncelleştirmeler arasında yeterli zaman aralığı vardır.

_Orta Hindistan dışında_ (eşleştirilmiş bölge, Güney Hindistan, Stream Analytics varlığı olmayan), Stream Analytics güncelleştirme dağıtımı bir eşleştirilmiş bölgeler kümesinde aynı anda gerçekleşmez. **Aynı gruptaki** birden çok bölgedeki dağıtımlar **aynı anda** oluşabilir.

**[Kullanılabilirlik ve eşleştirilmiş bölgelerde](../best-practices-availability-paired-regions.md)** bulunan makale, hangi bölgelerin eşleştirilildiği hakkında en güncel bilgileri içerir.

Aynı işlerin her ikisi de eşleştirilmiş bölgelere dağıtılması önerilir. Sonra [Bu işleri](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) , beklenmeyen bir şeyler gerçekleştiğinde bildirim almak için izlemeniz gerekir. Stream Analytics bir hizmet güncelleştirmesinden sonra bu işlerden biri [başarısız durumunda](./job-states.md) sonlanıyorsa, kök nedeni belirlemenize yardımcı olması için müşteri desteğiyle iletişim kurun. Ayrıca, herhangi bir aşağı akış tüketicilerinin yükünü sağlıklı iş çıktısına devreder.

## <a name="next-steps"></a>Sonraki adımlar

* [Stream Analytics giriş](stream-analytics-introduction.md)
* [Akış Analizi ile çalışmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Stream Analytics sorgu dili başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics yönetim REST API başvurusu](/rest/api/streamanalytics/)