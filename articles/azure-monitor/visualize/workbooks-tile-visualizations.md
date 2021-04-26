---
title: Azure Izleyici çalışma kitabı kutucuk görselleştirmeleri
description: Tüm Azure Izleyici çalışma kitabı kutucuk görselleştirmeleri hakkında bilgi edinin.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 07de9fcd01559e59a1bcd5d8928075aebcf79cc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100622819"
---
# <a name="tile-visualizations"></a>Döşeme görselleştirmeleri

Kutucuklar, çalışma kitaplarında Özet verileri sunmak için kullanışlı bir yoldur. Aşağıdaki görüntüde, ayrıntılı bir kılavuzun üzerine uygulama düzeyi Özeti ile yaygın olarak kullanılan bir kullanım durumu gösterilmektedir.

[![Kutucuk Özet görünümünün ekran görüntüsü](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

Çalışma kitabı kutucukları; bir başlık, alt başlık, büyük metin, simge, ölçüm tabanlı degradeler, Spark çizgisi/çubukları, alt bilgi vb. gösteren

## <a name="adding-a-tile"></a>Kutucuk ekleme

1. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgu denetimi eklemek için **Ekle** ' yi ve ardından *Sorgu Ekle* ' yi seçin.
3. Sorgu türünü **günlük**, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
4. Analizinize yönelik KQL 'yi girmek için sorgu düzenleyicisini kullanın.

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. Boyutu **tam** olarak ayarlayın.
6. Görselleştirmeyi **döşemeler** olarak ayarlayın.
7. Ayarlar bölmesini açmak için **kutucuk ayarları** düğmesini seçin.
    1. *Başlık* bölümünde şunları ayarlayın:
        * Sütunu kullan: `name` .
    2. *Sol tarafta* şunları ayarlayın:
        * Sütunu kullan: `Requests` .
        * Sütun Oluşturucu: `Big Number` .
        * Renk paleti: `Green to Red`
        * En küçük değer: `0` .
    3. *En altta*, şunları ayarlayın:
        * Sütunu kullan: `appName` .
8. Bölmenin alt kısmındaki **Kaydet ve Kapat** düğmesini seçin.

[![Yukarıdaki sorgu ve kutucuk ayarlarını içeren kutucuk Özeti görünümünün ekran görüntüsü.](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

Okuma modundaki kutucuklar:

[![Okuma modundaki kutucuk Özeti görünümünün ekran görüntüsü.](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>Kutucuklarda Spark satırları

1. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Adlı bir zaman aralığı parametresi ekleyin `TimeRange` .
    1. **Ekle** ' yi seçin ve ardından *parametreleri ekleyin*.
    2. Parametre denetiminde **parametre Ekle**' yi seçin.
    3. `TimeRange` *Parametre adı* alanına girin ve `Time range picker` *parametre türü* için öğesini seçin.
    4. Bölmenin en üstünde bulunan **Kaydet** ' i seçin ve ardından parametre denetiminde **Düzenle bitti** ' yi seçin.
3. **Ekle** ' yi seçin ve ardından parametre denetiminin altına bir günlük sorgusu denetimi eklemek Için *Sorgu Ekle* ' ye tıklayın.
4. Sorgu türünü **günlük**, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
5. Analizinize yönelik KQL 'yi girmek için sorgu düzenleyicisini kullanın.

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. **Sorgu Çalıştır**'ı seçin. ( `TimeRange` Sorguyu çalıştırmadan önce seçtiğiniz bir değere ayarladığınızdan emin olun.)
7. *Görselleştirmeyi* "kutucuklar" olarak ayarlayın.
8. *Boyutu* "tam" olarak ayarlayın.
9. **Kutucuk ayarları**' nı seçin.
    1. *Kutucukta* şunları ayarlayın:
        * Sütunu kullan: `name` .
    2. *Alt kutucukta* şunları ayarlayın:
        *  Sütunu kullan: `appNAme` .
    3. *Sol tarafta* şunları ayarlayın:
        *  Sütunu kullan: `Requests` .
        * Sütun Oluşturucu: `Big Number` .
        * Renk paleti: `Green to Red` .
        * En küçük değer: `0` .
    4. *En altta*, şunları ayarlayın:
        * Sütunu kullan: `Trend` .
        * Sütun Oluşturucu: `Spark line` .
        * Renk paleti: `Green to Red` .
        * En küçük değer: `0` .
10. Bölmenin alt kısmındaki **Kaydet ve Kapat ' ı** seçin.

![Spark satırıyla kutucuk görselleştirmesinin ekran görüntüsü](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>Döşeme boyutları

Yazar, kutucuk ayarlarında kutucuk genişliğini ayarlama seçeneğine sahiptir.

* `fixed` varsayılanını

    Kutucukların varsayılan davranışı, yaklaşık olarak aynı sabit genişlik, yaklaşık 160 piksel genişliğinde ve kutucukların çevresindeki boşluk olacaktır.

    ![Sabit genişlikli kutucukları görüntüleyen ekran görüntüsü](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    Her bir başlık, içeriklerini sığacak şekilde küçülür veya büyür, kutucuklar kutucukların görünümü (yatay kaydırma yok) ile sınırlıdır.

    ![Otomatik genişlik kutucukları görüntüleyen ekran görüntüsü](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    Her bir başlık her zaman kutucukların görünüm, her satır için bir başlık olacak şekilde tam genişlik olacak.

     ![Tam boyut genişliği kutucukları görüntüleyen ekran görüntüsü](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>Sonraki adımlar

* Kutucuklar Ayrıca bileşik çubuk oluşturucuyu destekler. Daha fazla bilgi edinmek için [Birleşik çubuk belgelerini](workbooks-composite-bar.md)ziyaret edin.
* `TimeRange` [Çalışma kitabı zaman parametreleri belgelerini](workbooks-time.md)ziyaret etme gibi zaman parametreleri hakkında daha fazla bilgi edinmek için.