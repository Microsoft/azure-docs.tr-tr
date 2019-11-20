---
title: Stream Analytics kullanarak Azure Event Hubs verileri işleme | Microsoft Docs
description: Bu makalede, Azure Event hub 'ınızdaki verileri bir Azure Stream Analytics işi kullanarak nasıl işleyebilmeniz gösterilmektedir.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991940"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Azure Stream Analytics kullanarak Olay Hub 'ınızdaki verileri işleme 
Azure Stream Analytics hizmeti, Azure Event Hubs akış verilerini almak, işlemek ve analiz etmeyi kolaylaştırır, böylece gerçek zamanlı eylemler için güçlü öngörüler sağlar. Bu tümleştirme, hızlı bir şekilde bir etkin yol analizi işlem hattı oluşturmanıza olanak sağlar. Gelen verileri görselleştirmek ve bir Stream Analytics sorgusu yazmak için Azure portal kullanabilirsiniz. Sorgunuz hazırlandıktan sonra, bunu yalnızca birkaç tıklamayla üretime taşıyabilirsiniz. 

## <a name="key-benefits"></a>Önemli avantajlar
Azure Event Hubs ve Azure Stream Analytics tümleştirmenin temel avantajları aşağıda verilmiştir: 
- **Veri önizleme** : Azure Portal bir olay hub 'ından gelen verileri önizleyebilirsiniz.
- **Sorgunuzu test edin** – bir dönüştürme sorgusu hazırlayın ve bunu doğrudan Azure Portal test edin. Sorgu dili sözdizimi için bkz. [Stream Analytics sorgu dili](/stream-analytics-query/built-in-functions-azure-stream-analytics) belgeleri.
- **Sorgunuzu üretime dağıtma** – bir Azure Stream Analytics işi oluşturup başlatarak sorguyu üretime dağıtabilirsiniz.

## <a name="end-to-end-flow"></a>Uçtan uca akış

1. [Azure Portal](https://portal.azure.com) oturum açın. 
1. **Event Hubs ad alanına** gidin ve ardından gelen verileri içeren **Olay Hub 'ına**gidin. 
1. Olay Hub 'ı sayfasında **Işlem verileri** ' ni seçin.  

    ![İşlem verileri kutucuğu](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Olaylar kutucuğunun **gerçek zamanlı** Içgörüleri etkinleştir kutucuğunda **keşfet** ' i seçin. 

    ![Stream Analytics seçin](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Aşağıdaki alanlar için önceden ayarlanmış değerler içeren bir sorgu sayfası görürsünüz:
    1. Sorgu için giriş olarak **Olay Hub** 'ınız.
    1. SELECT ifadesiyle örnek **SQL sorgusu** . 
    1. Sorgu test sonuçlarınıza başvurmak için bir **Çıkış** diğer adı. 

        ![Sorgu Düzenleyicisi](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Bu özelliği ilk kez kullandığınızda, Bu sayfa, gelen verileri önizlemek için bir tüketici grubu ve Olay Hub 'ınız için bir ilke oluşturma izninizi ister.
1. Önceki görüntüde gösterildiği gibi **giriş önizleme** bölmesinde **Oluştur** ' u seçin. 
1. Bu sekmede, en son gelen verilerin anlık görüntüsünü hemen görürsünüz.
    - Verilerinizde serileştirme türü otomatik olarak algılanır (JSON/CSV). Bunu, JSON/CSV/AVRO ile el ile değiştirebilirsiniz.
    - Gelen verileri tablo biçiminde veya ham biçimde önizleyebilirsiniz. 
    - Gösterilen veriniz güncel değilse, en son olayları görmek için **Yenile** ' yi seçin. 

        **Tablo biçiminde**bir veri örneği aşağıda verilmiştir:   ![Tablo biçiminde sonuçlar](./media/process-data-azure-stream-analytics/snapshot-results.png)

        **Ham biçimdeki**verilerin bir örneği aşağıda verilmiştir: 

        ![Ham biçimde sonuçlar](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Test **sonuçları** sekmesinde sorgunuzun test sonuçlarının anlık görüntüsünü görmek için **Test sorgusu** ' nu seçin. Sonuçları da indirebilirsiniz.

    ![Test sorgu sonuçları](./media/process-data-azure-stream-analytics/test-results.png)
1. Verileri dönüştürmek için kendi sorgunuzu yazın. Bkz. [Stream Analytics sorgu dili başvurusu](/stream-analytics-query/stream-analytics-query-language-reference).
1. Sorguyu test edildikten ve bunu üretime taşımak istediğinizde **sorguyu dağıt**' ı seçin. Sorguyu dağıtmak için, işiniz için bir çıktı ayarlayabileceğiniz bir Azure Stream Analytics işi oluşturun ve işi başlatın. Stream Analytics işi oluşturmak için, iş için bir ad belirtin ve **Oluştur**' u seçin.

      ![Azure Stream Analytics işi oluşturma](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Event Hubs sayfasından oluşturduğunuz her yeni Azure Stream Analytics işi için bir tüketici grubu ve ilke oluşturmanızı öneririz. Tüketici grupları yalnızca beş eşzamanlı okuyucu sağlar; bu nedenle her iş için adanmış bir tüketici grubu sağlanması bu sınırı aşmaktan kaynaklanan hatalardan kaçınacaktır. Adanmış bir ilke, diğer kaynakları etkilemeden anahtarınızı döndürmenize veya izinleri iptal etmenize olanak tanır. 
1. Stream Analytics işiniz, sorgunuz test ettiğiniz ve Olay Hub 'ınız olan girişte oluşturulur. 

9.  İşlem hattını tamamladıktan sonra, sorgunun **çıkışını** ayarlayın ve işi başlatmak için **Başlat** ' ı seçin.

    > [!NOTE]
    > İşi başlatmadan önce, outputalias öğesini Azure Stream Analytics oluşturduğunuz çıkış adına göre değiştirmeyi unutmayın.

      ![Çıktıyı ayarlama ve işi başlatma](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Bilinen sınırlamalar
Sorgunuzu test ederken, test sonuçlarının yüklenmesi yaklaşık 6 saniye sürer. Sınama performansını geliştirmeye çalışıyoruz. Ancak, üretimde dağıtıldığında, Azure Stream Analytics ikinci saniye gecikme süresine sahip olur.

## <a name="streaming-units"></a>Akış birimleri
Azure Stream Analytics işiniz varsayılan olarak üç akış birimine (SUs) sahiptir. Bu ayarı ayarlamak için, Azure portal **Stream Analytics iş** sayfasında sol menüdeki **Ölçek** ' i seçin. Akış birimleri hakkında daha fazla bilgi edinmek için bkz. [akış birimlerini anlama ve ayarlama](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Akış birimlerini ölçeklendirme](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Sonraki adımlar
Stream Analytics sorguları hakkında daha fazla bilgi edinmek için bkz. [Stream Analytics sorgu dili](/stream-analytics-query/built-in-functions-azure-stream-analytics)
