---
title: Azure Stream Analytics işi izlemeyi anlama
description: Bu makalede, Azure portalında Azure Stream Analytics işlerini izleme açıklar.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 9432b43498cd48352427bf1ec2709a2d0f13a797
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172773"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Stream Analytics işi izleme ve sorguları izleme anlama

## <a name="introduction-the-monitor-page"></a>Giriş İzleyici sayfası
Azure portal, sorgunuzu ve iş performansınızı izlemek ve sorunlarını gidermek için kullanılabilen temel performans ölçümlerini yüzeyler. Bu ölçümler görmek için Gözat görmeniz için ölçümleri ilgilendiğiniz ve görüntülemek Stream Analytics işi **izleme** bölümüne genel bakış sayfasında.  

![Stream Analytics işini bağlantı izleme](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Penceresinde gösterildiği gibi görünür:

![Stream Analytics işi izleme Panosu](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Stream Analytics için mevcut olan ölçümler
| Ölçüm                 | Tanım                               |
| ---------------------- | ---------------------------------------- |
| Biriktirme Listesindeki Giriş Olayları       | Biriktirme listesindeki giriş olayların sayısı. Bu ölçüm için sıfır olmayan bir değer, işinizin gelen olay sayısıyla devam edemediğinden emin olmak anlamına gelir. Bu değer yavaş bir şekilde artarak veya sürekli olarak sıfır değilse, işinizi ölçeklendirmelisiniz. Daha fazla bilgi edinmek için öğrenme [ve akış birimlerini ayarlayın](stream-analytics-streaming-unit-consumption.md). |
| Veri Dönüştürme Hataları | Beklenen çıktıyı şemaya dönüştürülemiyor çıkış olaylarının sayısı. Hata ilkesi, bu senaryoya ilişkin olayları bırakmak için ' Drop ' olarak değiştirilebilir. |
| Erken Giriş Olayları       | Uygulama zaman damgası, 5 dakikadan uzun bir süre içinde olan olay zaman damgasından daha önce. |
| Başarısız İşlev İstekleri | Başarısız (varsa) Azure Machine Learning işlevi çağrı sayısı. |
| İşlev Olayları        | (Varsa) Azure Machine Learning işleve gönderilen olay sayısı. |
| İşlev İstekleri      | Azure Machine Learning işlevi (varsa) yapılan çağrı sayısı. |
| Giriş Serileştirme Kaldırma Hataları       | Seri durumdan çıkarılabilen giriş olaylarının sayısı.  |
| Giriş Olayı Bayt Sayısı      | Stream Analytics işinde bayt tarafından alınan veri miktarı. Bu olaylar için giriş kaynağı gönderildiğini doğrulamak için kullanılabilir. |
| Giriş Olayları           | Giriş olaylarından Serisi kaldırılan kayıt sayısı. Bu sayı, hata serisini kaldırma ile sonuçlanan gelen olayları içermez. |
| Alınan Giriş Kaynakları       | İş tarafından alınan ileti sayısı. Olay Hub 'ı için, bir ileti tek bir EventData ' dır. Blob için, bir ileti tek bir Blobun olur. Giriş kaynaklarının seri durumundan önce sayıldığını lütfen unutmayın. Seri kaldırma hataları varsa, giriş kaynakları giriş olaylarından daha büyük olabilir. Aksi halde, her ileti birden çok olay içerebileceğinden, bu, giriş olayından daha az veya eşit olabilir. |
| Geç Giriş Olayları      | Yapılandırılan geç gelme toleransı penceresinden daha sonra gelen olaylar. [Azure Stream Analytics olay sırası konuları](stream-analytics-out-of-order-and-late-events.md) hakkında daha fazla bilgi edinin. |
| Sıra dışı olayları    | Bırakılan veya olay sıralama ilkesine göre ayarlanmış bir zaman damgası, verilen sıranın dışında alınan olay sayısı. Out of toleransı penceresi ayarının yapılandırmasını tarafından etkilenmiş. |
| Çıkış Olayları          | Çıkış hedefte olayların sayısı Stream Analytics işi tarafından gönderilen veri miktarı. |
| Çalışma Zamanı Hataları         | Sorgu işlemeyle ilgili toplam hata sayısı (olayları alırken veya sonuçları yazarken bulunan hatalar hariç) |
| SU Kullanım Yüzdesi       | Akış birimi kullanımına iş ölçek sekmesinden bir işe atanmış. Bu gösterge % 80 düzeyine ulaşması gerektiğini, yukarıdaki var veya olay işlenmesi gecikebilir veya ilerleme durduruldu yüksek olasılık. |
| Eşik Gecikmesi       | İşin tüm çıkışları tüm bölümleri arasında en yüksek eşik gecikmesi. |

[Stream Analytics işinizin performansını izlemek](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)için bu ölçümleri kullanabilirsiniz. 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Azure portalında izleme özelleştirme
Grafikte gösterilen ölçümleri türünü ayarlamak ve zaman aralığı grafiği Düzenle ayarlarında. Ayrıntılar için bkz [özelleştirme izleme nasıl](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Stream Analytics, izleme zaman grafiği sorgulama](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Son çıkış
İşi izlemek için başka bir ilgi çekici veri noktası genel bakış sayfasında gösterilen son çıkış zamanı gelmiştir.
Bu uygulamanın süresi (Olay verileri itibaren zaman damgası kullanarak zaman)'dır işinin en son çıktı.

## <a name="get-help"></a>Yardım alın
Daha fazla yardım için [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) deneyin.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)
