---
title: Visual Studio için Data Lake araçları 'nda köşe yürütme görünümü
description: Bu makalede Data Lake Analytics işleri yürütmek için köşe yürütme görünümünün nasıl kullanılacağı açıklanır.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/13/2016
ms.openlocfilehash: 3fba7bdaa5db1d812fbcd479e5f1eab50c8d1032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215867"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Visual Studio için Data Lake araçları 'nda köşe yürütme görünümünü kullanma
Data Lake Analytics işleri yürütmek için köşe yürütme görünümünü nasıl kullanacağınızı öğrenin.


## <a name="open-the-vertex-execution-view"></a>Köşe yürütme görünümünü açın
Visual Studio için Data Lake araçları 'nda bir U-SQL işi açın. Sol alt köşedeki **köşe yürütme görünümü** ' ne tıklayın. Önce profilleri yüklemeniz istenebilir ve ağ bağlantınız bağlı olarak bir süre zaman alabilir.

![Data Lake Analytics araçları köşe yürütme görünümünü gösteren ekran görüntüsü](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Köşe yürütme görünümünü anlama
Köşe yürütme görünümü üç bölümden oluşur:

![Köşe yürütme görünümünü "köşe seçiciyle" ve orta-üst ve orta alt bölmeler vurgulanmış şekilde gösteren ekran görüntüsü.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

Soldaki **köşe Seçicisi** , özelliklere göre köşeler seçmenizi sağlar (örneğin, ilk 10 veri okuma veya aşamasına göre seçim). En yaygın olarak kullanılan filtrelerden biri, **Kritik yoldaki köşeleri** görmeleridir. **Kritik yol** bir U-SQL işinin en uzun köşe zinciridir. Kritik yolu anlamak, en uzun zaman hangi köşeyi aldığını denetleyerek işlerinizi iyileştirmek için faydalıdır.
  
!["Tüm köşelerin çalışma durumunu" gösteren köşe yürütme görünümü üst orta bölmesini gösteren ekran görüntüsü.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Üstteki Orta bölmede **tüm köşelerin çalışma durumu** gösterilmektedir.
  
![Her köşe hakkında bilgi görüntüleyen köşe yürütme görünümü alt orta bölmesini gösteren ekran görüntüsü.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Alt orta bölmede her bir köşe hakkında bilgi gösterilir:
* İşlem adı: köşe örneğinin adı. Bu, StageName 'daki farklı bölümlerden oluşur | VertexName | Vertexrunınstance. Örneğin, SV7_Split [62]. v1 köşe, ikinci çalışan örnek (. v1, 0 ' dan başlayarak dizin), SV7_Split aşamasında 62 köşe numarası için temsil eder.
* Okunan/yazılan toplam veri: Bu köşe tarafından okunan/yazılan veriler.
* Durum/çıkış durumu: köşe bittiğinde nihai durum.
* Çıkış kodu/hata türü: köşe başarısız olduğunda hata.
* Oluşturma nedeni: köşe neden oluşturulur.
* Kaynak gecikme süresi/Işlem gecikmesi/PN kuyruk gecikmesi: köşenin kaynakları beklemesi, verileri işlemesi ve kuyrukta kalmak için geçen süre.
* İşlem/Oluşturucu GUID: geçerli çalışan köşe veya oluşturucusunun GUID 'SI.
* Sürüm: çalışan köşenin N-th örneği (sistem, yük devretme, işlem yedekliği vb. gibi birçok nedenden dolayı yeni bir köşe örnekleri zamanlayabilir)
* Sürüm oluşturma zamanı.
* İşlem oluşturma başlangıç zamanı/Işlem sıraya alma zamanı/işlem başlangıç zamanı/işlem tamamlanma zamanı: köşe işlemi oluşturma başladığında; köşe işlemi sıraya başladığında; belirli bir köşe işlemi başladığında; belirli köşe tamamlandığında.

## <a name="next-steps"></a>Sonraki adımlar
* Tanılama bilgilerini günlüğe kaydetmek için bkz. [Azure Data Lake Analytics için tanılama günlüklerine erişme](data-lake-analytics-diagnostic-logs.md)
* Daha karmaşık bir sorgu görmek için [Azure Data Lake Analytics'i kullanarak Web sitesi günlüklerini çözümleme](data-lake-analytics-analyze-weblogs.md) makalesine bakın.
* İş ayrıntılarını görüntülemek için bkz. [Azure Data Lake Analytics işleri Için Iş tarayıcısını ve Iş görünümünü kullanma](data-lake-analytics-data-lake-tools-view-jobs.md)
