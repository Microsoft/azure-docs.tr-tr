---
title: Visual Studio için Data Lake araçları 'nda köşe yürütme görünümü
description: Bu makalede Data Lake Analytics işleri yürütmek için köşe yürütme görünümünün nasıl kullanılacağı açıklanır.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: f5adbb75e6852551976aa040a1a1c723d2e3f59b
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309722"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Visual Studio için Data Lake araçları 'nda köşe yürütme görünümünü kullanma
Data Lake Analytics işleri yürütmek için köşe yürütme görünümünü nasıl kullanacağınızı öğrenin.


## <a name="open-the-vertex-execution-view"></a>Köşe yürütme görünümünü açın
Visual Studio için Data Lake araçları 'nda bir U-SQL işi açın. Sol alt köşedeki **köşe yürütme görünümü** ' ne tıklayın. Önce profilleri yüklemeniz istenebilir ve ağ bağlantınız bağlı olarak bir süre zaman alabilir.

![Data Lake Analytics araçları köşe yürütme görünümü](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Köşe yürütme görünümünü anlama
Köşe yürütme görünümü üç bölümden oluşur:

![Data Lake Analytics araçları köşe yürütme görünümü](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

Soldaki **köşe Seçicisi** , özelliklere göre köşeler seçmenizi sağlar (örneğin, ilk 10 veri okuma veya aşamasına göre seçim). En yaygın olarak kullanılan filtrelerden biri, **Kritik yoldaki köşeleri**görmeleridir. **Kritik yol** bir U-SQL işinin en uzun köşe zinciridir. Kritik yolu anlamak, en uzun zaman hangi köşeyi aldığını denetleyerek işlerinizi iyileştirmek için faydalıdır.
  
![Data Lake Analytics araçları köşe yürütme görünümü](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Üstteki Orta bölmede **tüm köşelerin çalışma durumu**gösterilmektedir.
  
![Data Lake Analytics araçları köşe yürütme görünümü](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Alt orta bölmede her bir köşe hakkında bilgi gösterilir:
* İşlem adı: Köşe örneğinin adı. Bu, StageName 'daki farklı bölümlerden oluşur | VertexName | Vertexrunınstance. Örneğin, SV7_Split [62]. v1 köşesi, SV7_Split 62 ' de köşe numarası için ikinci çalışan örnek (. v1, 0 ' dan başlayan dizin) için temsil eder.
* Okunan/yazılan toplam veri: Veriler bu köşe tarafından okundu/yazıldı.
* Durum/çıkış durumu: Köşe sona erdikten sonra nihai durum.
* Çıkış kodu/hata türü: Köşe başarısız olduğunda hata.
* Oluşturma nedeni: Köşe neden oluşturulur.
* Kaynak gecikme süresi/Işlem gecikmesi/PN kuyruk gecikmesi: köşenin kaynakları beklemesi, verileri işlemesi ve kuyrukta kalmak için geçen süre.
* İşlem/Oluşturucu GUID 'SI: Geçerli çalışan köşe veya oluşturucusunun GUID 'SI.
* Sürüm: çalışan köşenin N-th örneği (sistem, yük devretme, işlem yedekliği vb. gibi birçok nedenden dolayı yeni bir köşe örnekleri zamanlayabilir)
* Sürüm oluşturma zamanı.
* İşlem oluşturma başlangıç zamanı/Işlem sıraya alma zamanı/işlem başlangıç zamanı/işlem tamamlanma zamanı: köşe işlemi oluşturma başladığında; köşe işlemi sıraya başladığında; belirli bir köşe işlemi başladığında; belirli köşe tamamlandığında.

## <a name="next-steps"></a>Sonraki adımlar
* Tanılama bilgilerini günlüğe kaydetmek için bkz. [Azure Data Lake Analytics için tanılama günlüklerine erişme](data-lake-analytics-diagnostic-logs.md)
* Daha karmaşık bir sorgu görmek için [Azure Data Lake Analytics'i kullanarak Web sitesi günlüklerini çözümleme](data-lake-analytics-analyze-weblogs.md) makalesine bakın.
* İş ayrıntılarını görüntülemek için bkz. [Azure Data Lake Analytics işleri Için Iş tarayıcısını ve Iş görünümünü kullanma](data-lake-analytics-data-lake-tools-view-jobs.md)
