---
title: Azure Stream Analytics iş durumları
description: Bu makalede bir Stream Analytics işinin dört farklı durumu açıklanır; çalışıyor, durduruldu, düşürülmüş ve başarısız oldu.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: dd298fae148b9e1e51072e98a4e0e5ece10967f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020460"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics iş durumları

Stream Analytics bir iş, belirli bir zamanda dört durumdan birinde olabilir: çalışıyor, durduruldu, düşürülmüş veya başarısız. İşinizin durumunu, Azure portal Stream Analytics işinizin Genel Bakış sayfasında bulabilirsiniz. 

| Durum | Açıklama | Önerilen Eylemler |
| --- | --- | --- |
| **Çalışma** | İşiniz, tanımlı giriş kaynaklarından gelen Azure okuma olayları üzerinde çalışıyor, onları işliyor ve yapılandırılan çıktı havuzları 'na sonuçları yazıyor. | [Anahtar ölçümlerini](./stream-analytics-set-up-alerts.md#scenarios-to-monitor)izleyerek işinizin performansını izlemek en iyi uygulamadır. |
| **Durduruldu** | İşiniz durdurulur ve olayları işlemez. | NA | 
| **Düzeyi düşürüldü** | Giriş ve çıkış bağlantılarınızın aralıklı sorunları olabilir. Bu hatalar, işinizi düşürülmüş bir durum girmeye neden olabilecek geçici hatalar olarak adlandırılır. Stream Analytics, bu hatalardan hemen kurtarmaya çalışacaktır ve çalışır duruma geri dönecektir (birkaç dakika içinde). Bu hatalar ağ sorunları, diğer Azure kaynaklarının kullanılabilirliği, seri durumdan çıkarma hataları vb. olabilir. İş düşürüldü durumunda işinizin performansı etkilenebilir.| Bu geçici hataların nedeni hakkında daha fazla bilgi edinmek için [Tanılama veya etkinlik günlüklerine](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) bakabilirsiniz. Hata serisini kaldırma gibi durumlarda, olayların hatalı biçimlendirilmiş olmamasını sağlamak için düzeltici eylem yapmanız önerilir. İş, kaynak kullanım sınırına ulaşılmaya devam ederse, SU numarasını veya [paralel hale getirmek](./stream-analytics-parallelization.md)'yi artırmayı deneyin. Herhangi bir işlem gerçekleştirmeniz gereken diğer durumlarda Stream Analytics, *çalışan* bir duruma kurtarmaya çalışacaktır. <br> Bu geçici hataların işinizin performansını etkileyip etkilemediğini anlamak için, [filigran gecikmesi](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) ölçümünü kullanabilirsiniz.|
| **Başarısız** | İşiniz, başarısız durumuna neden olan kritik bir hatayla karşılaştı. Olaylar okunamaz ve işlenmez. Çalışma zamanı hataları, başarısız bir durumda biten işlerin yaygın bir nedendir. | Uyarıları, iş başarısız durumuna geçtiğinde bildirim almak için [yapılandırabilirsiniz](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) . <br> <br>Temel nedeni belirlemek ve sorunu gidermek için [etkinlik ve kaynak günlüklerini](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) kullanarak hata ayıklaması yapabilirsiniz.|

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream Analytics işleri için kurulum uyarıları](stream-analytics-set-up-alerts.md)
* [Stream Analytics kullanılabilen ölçümler](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)
* [Etkinlik ve kaynak günlüklerini kullanarak sorun giderme](./stream-analytics-job-diagnostic-logs.md)