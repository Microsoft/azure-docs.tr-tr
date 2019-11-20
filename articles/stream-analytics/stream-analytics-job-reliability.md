---
title: Azure Stream Analytics işlerinde hizmet kesintilerini önleme
description: Bu makalede, yükseltme, Stream Analytics işleri dayanıklı hale yönergeler açıklanmaktadır.
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: e38f8a923daa210d8aa5b56631e5f8157d4b3f70
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620874"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Stream Analytics işi güvenilirlik garanti sırasında hizmet güncelleştirmeleri

Tam olarak yönetilen bir hizmet olan yeni hizmet işlevlerini ve geliştirmeleri hızlı bir hızda özelliği parçasıdır. Sonuç olarak, Stream Analytics, bir hizmet güncelleştirmesinden tek haftalık (veya daha sık) temelinde dağıtma olabilir. Ne kadarlık testi bitti ne olursa olsun yine de, var olan, çalışan bir iş sunulmasıyla bir hata nedeniyle kesilebilir sokması mümkündür. Görev açısından kritik çalıştırıyorsanız, işleri, bu riskleri kaçınılması gerekir. Aşağıdaki Azure'nın bu riski azaltabilir **[eşleştirilmiş bölge](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** modeli. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Azure eşleştirilmiş bölgeleri başlıklarla bu nasıl karşılayabileceği?

Stream Analytics, eşleştirilmiş bölgelerin işlerinde ayrı toplu işler üzerinde güncelleştirilir garanti eder. Sonuç olarak olası sorunları belirlemek ve düzeltmek için güncelleştirmeleri arasında yeterli bir zaman aralığı yok.

_Orta Hindistan dışında_ (eşleştirilmiş olan bölge, Güney Hindistan, Stream Analytics durum yok), Stream Analytics için bir güncelleştirme dağıtımı, eşleştirilmiş bölgelerin kümesinde aynı anda oluşmaz. Birden çok bölgede dağıtımları **aynı gruptaki** oluşabilir **aynı anda**.

Makale **[kullanılabilirlik ve eşleştirilmiş bölgelerin](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** bölgeleri halindedir en güncel bilgilere sahip.

Her iki bölge çiftlerine özdeş işleri dağıtmak için önerilir. Ardından gereken [bu işleri izlemek](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) beklenmeyen bir sorun meydana geldiğinde bildirim almak için. Bunlardan birini uçları yedekleme işlerini varsa bir [başarısız durumu](https://docs.microsoft.com/azure/stream-analytics/job-states) bir Stream Analytics Hizmet güncelleştirmesinden sonra kök nedeni belirlemenize yardımcı olması için müşteri desteğine başvurun. Ayrıca herhangi bir aşağı akış tüketiciye sağlıklı iş çıktısı için yük devretme.

## <a name="next-steps"></a>Sonraki adımlar

* [Stream analytics'e giriş](stream-analytics-introduction.md)
* [Stream Analytics ile çalışmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Stream Analytics sorgu dili başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics Yönetimi REST API Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)
