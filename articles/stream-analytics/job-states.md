---
title: Azure Stream Analytics iş durumları
description: Bu makalede bir Stream Analytics işinin dört farklı durumu açıklanır; çalışıyor, durduruldu, düşürülmüş ve başarısız oldu.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: c533463ff544dc315142f7fb95c34c67933f9614
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444259"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics iş durumları

Stream Analytics bir iş, belirli bir zamanda dört durumdan birinde olabilir: çalışıyor, durduruldu, düşürülmüş veya başarısız. İşinizin durumunu, Azure portal Stream Analytics işinizin Genel Bakış sayfasında bulabilirsiniz. 

| Durum | Açıklama | Önerilen eylemler |
| --- | --- | --- |
| **Çalıştıran** | İşiniz, tanımlı giriş kaynaklarından gelen Azure okuma olayları üzerinde çalışıyor, onları işliyor ve yapılandırılan çıktı havuzları 'na sonuçları yazıyor. | [Anahtar ölçümlerini](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)izleyerek işinizin performansını izlemek en iyi uygulamadır. |
| **Durdurulacağını** | İşiniz durdurulur ve olayları işlemez. | NA | 
| **Düzey** | Giriş ve çıkış bağlantılarınızın aralıklı sorunları olabilir. Bu hatalar, işinizi düşürülmüş bir durum girmeye neden olabilecek geçici hatalar olarak adlandırılır. Stream Analytics, bu hatalardan hemen kurtarmaya çalışacaktır ve çalışır duruma geri dönecektir (birkaç dakika içinde). Bu hatalar ağ sorunları, diğer Azure kaynaklarının kullanılabilirliği, seri durumdan çıkarma hataları vb. olabilir. İş düşürüldü durumunda işinizin performansı etkilenebilir.| Bu geçici hataların nedeni hakkında daha fazla bilgi edinmek için [Tanılama veya etkinlik günlüklerine](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) bakabilirsiniz. Hata serisini kaldırma gibi durumlarda, olayların hatalı biçimlendirilmiş olmamasını sağlamak için düzeltici eylem yapmanız önerilir. İş, kaynak kullanım sınırına ulaşılmaya devam ederse, SU numarasını veya [paralel hale getirmek](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)'yi artırmayı deneyin. Herhangi bir işlem gerçekleştirmeniz gereken diğer durumlarda Stream Analytics, *çalışan* bir duruma kurtarmaya çalışacaktır. <br> Bu geçici hataların işinizin performansını etkileyip etkilemediğini anlamak için, [filigran gecikmesi](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) ölçümünü kullanabilirsiniz.|
| **Başarısız** | İşiniz, başarısız durumuna neden olan kritik bir hatayla karşılaştı. Olaylar okunamaz ve işlenmez. Çalışma zamanı hataları, başarısız bir durumda biten işlerin yaygın bir nedendir. | Uyarıları, iş başarısız durumuna geçtiğinde bildirim almak için [yapılandırabilirsiniz](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) . <br> <br>Temel nedeni belirlemek ve sorunu gidermek için [etkinlik ve tanılama günlüklerini](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) kullanarak hata ayıklaması yapabilirsiniz.|

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream Analytics işleri için kurulum uyarıları](stream-analytics-set-up-alerts.md)
* [Stream Analytics kullanılabilen ölçümler](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Etkinlik ve tanılama günlüklerini kullanarak sorun giderme](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
