---
title: Visual Studio için Azure Stream Analytics ile canlı verileri test etme
description: Canlı akış verilerini kullanarak Azure Stream Analytics işinizi yerel olarak test etme hakkında bilgi edinin.
author: ajetasin
ms.author: ajetasi
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 99e9ce85bb1e59b5016e836ff16d03cb3326ec8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020375"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Visual Studio için Azure Stream Analytics araçları 'nı kullanarak canlı verileri yerel olarak test etme (Önizleme)

Visual Studio için Azure Stream Analytics araçları, Azure Olay Hub 'ı, IoT Hub ve BLOB depolamadaki canlı olay akışlarını kullanarak işleri yerel olarak IDE 'den test etmenizi sağlar. Canlı veri yerel testi, bulutta gerçekleştirebileceğiniz [performans ve ölçeklenebilirlik testini](stream-analytics-streaming-unit-consumption.md) değiştirmez, ancak Stream Analytics işinizi test etmek istediğiniz her seferinde buluta göndermek zorunda kalmadan, işlevsel test sırasında zamandan tasarruf edebilirsiniz. Bu özellik önizlemededir ve üretim iş yükleri için kullanılmamalıdır.

## <a name="testing-options"></a>Test seçenekleri

Aşağıdaki yerel test seçenekleri desteklenir:

|**Giriş**  |**Çıktı**  |**İş türü**  |
|---------|---------|---------|
|Yerel statik veriler   |  Yerel statik veriler   |   Bulut/kenar |
|Canlı giriş verileri   |  Yerel statik veriler   |   Bulut |
|Canlı giriş verileri   |  Canlı çıkış verileri   |   Bulut |

## <a name="local-testing-with-live-data"></a>Canlı verilerle yerel test

1. [Visual Studio 'da Azure Stream Analytics bir bulut projesi](stream-analytics-quick-create-vs.md)oluşturduktan sonra **Script. aşama QL**' yi açın. Yerel test varsayılan olarak yerel giriş ve yerel çıkış kullanır.

   ![Azure Stream Analytics Visual Studio yerel giriş ve yerel çıkış](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Canlı verileri test etmek için açılan kutudan **bulut girişini kullan** ' ı seçin.

   ![Azure Stream Analytics Visual Studio Live Cloud Input](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. İş giriş verilerinin işlenmesine ne zaman başlayacağınızı tanımlamak için **başlangıç saatini** ayarlayın. İşin doğru sonuçları sağlamak için iş giriş verilerini daha önce okuması gerekebilir. Varsayılan saat, geçerli zamandan önce 30 dakikaya ayarlanır.

   ![Azure Stream Analytics Visual Studio canlı veri başlangıç zamanı](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. **Yerel olarak çalıştır**' a tıklayın. Çalışan ilerleme ve iş ölçümleriyle bir konsol penceresi görüntülenir. İşlemi durdurmak istiyorsanız bunu el ile yapabilirsiniz. 

   ![Visual Studio Live Data Process penceresini Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Çıkış sonuçları, yerel çalıştırma sonucu penceresindeki ilk 500 çıkış satırı ile üç saniyede bir yenilenir ve çıkış dosyaları proje yolu **Asalocalrun** klasörünüze yerleştirilir. Ayrıca, yerel çalışma sonucu penceresinde **Sonuçlar klasörünü aç** düğmesine tıklayarak çıktı dosyalarını açabilirsiniz.

   ![Visual Studio canlı veri açık sonuçları klasörünü Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Sonuçları bulut çıkış havuza almak istiyorsanız ikinci açılan kutudan **buluta çıkış** ' ı seçin. Power BI ve Azure Data Lake Storage, çıkış havuzları için desteklenmez.

   ![Visual Studio canlı veri çıkışını buluta Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Sınırlamalar

* Power BI ve Azure Data Lake Storage, kimlik doğrulama modeli sınırlamaları nedeniyle desteklenen çıkış havuzları değil.

* Yalnızca bulut girişi seçeneklerinde [zaman ilkeleri](./stream-analytics-time-handling.md) desteklenir, ancak yerel giriş seçenekleri değildir.

## <a name="next-steps"></a>Sonraki adımlar

* [Visual Studio için Azure Stream Analytics araçları 'nı kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-vs.md)
* [Visual Studio ile yerel olarak Stream Analytics sorguları test etme](stream-analytics-vs-tools-local-run.md)
* [Azure Stream Analytics işleri görüntülemek için Visual Studio 'Yu kullanma](stream-analytics-vs-tools.md)