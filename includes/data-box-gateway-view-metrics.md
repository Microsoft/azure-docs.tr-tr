---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/16/2020
ms.author: alkohli
ms.openlocfilehash: 27503d1d60d961bac6dd41ebfe4fb083948cb251
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582447"
---
Ayrıca, cihazın performansını izlemek için ölçümleri ve cihaz sorunlarını gidermeye yönelik bazı örnekleri görüntüleyebilirsiniz.

Seçili cihaz ölçümleri için bir grafik oluşturmak üzere Azure portal aşağıdaki adımları uygulayın.

1. Azure portal kaynağınız için **izleme > ölçümleri** ' ne gidin ve **ölçüm Ekle**' yi seçin.

    ![Ölçüm ekleme](media/data-box-gateway-view-metrics/view-metrics-add-metric.png)

2. Kaynak otomatik olarak doldurulur.  

    ![Geçerli kaynak](media/data-box-gateway-view-metrics/view-metrics-current-resource.png)

    Başka bir kaynak belirtmek için kaynağı seçin. **Kaynak Seç** dikey penceresinde, ölçümü göstermek istediğiniz aboneliği, kaynak grubunu, kaynak türünü ve belirli kaynağı seçin ve **Uygula**' yı seçin.

    ![Başka bir kaynak seçin](media/data-box-gateway-view-metrics/view-metrics-choose-another-resource.png)

3. Açılan listeden, cihazınızı izlemek için bir ölçüm seçin. Bu ölçümlerin tam listesi için bkz. [cihazınızdaki ölçümler](#metrics-on-your-device).

4. Açılan listeden bir ölçüm seçildiğinde, toplama de tanımlanabilir. Toplama, belirli bir zaman aralığında toplanan gerçek değeri ifade eder. Toplanmış değerler ortalama, en küçük veya en büyük değer olabilir. AVG, Max veya min toplamını seçin.

    ![Grafiği görüntüle](media/data-box-gateway-view-metrics/view-metrics-view-chart.png)

5. Seçtiğiniz metriğin birden çok örneği varsa, bölme seçeneği kullanılabilir. **Bölmeyi Uygula**' yı seçin ve ardından dökümü görmek istediğiniz değeri seçin.

    ![Bölmeyi Uygula](media/data-box-gateway-view-metrics/view-metrics-apply-splitting.png)

6. Artık dökümü yalnızca birkaç örnek için görmek istiyorsanız, verileri filtreleyebilirsiniz. Örneğin, bu durumda, yalnızca cihazınızdaki iki bağlı ağ arabirimi için ağ aktarım hızını görmek istiyorsanız, bu arabirimlere filtre uygulanabilir. **Filtre Ekle** ' yi seçin ve filtreleme için ağ arabirimi adını belirtin.

    ![Filtre ekle](media/data-box-gateway-view-metrics/view-metrics-add-filter.png)

7. Ayrıca, kolay erişim için grafiği panoya sabitleyebilirsiniz.

    ![Panoya sabitle](media/data-box-gateway-view-metrics/view-metrics-pin-to-dashboard.png)

8. Grafik verilerini bir Excel elektronik tablosuna aktarmak veya paylaşabileceğiniz grafiğin bir bağlantısını almak için komut çubuğundan Share (paylaşma) seçeneğini belirleyin.

    ![Verileri dışarı aktarma](media/data-box-gateway-view-metrics/view-metrics-export-data.png)
