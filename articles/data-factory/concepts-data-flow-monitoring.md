---
title: Azure Data Factory eşleme veri akışı görsel Izleme
description: Azure Data Factory veri akışlarını görsel olarak izleme
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: e92471bd467557f3ac8eb0404d672355ccab8449
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597104"
---
# <a name="monitor-data-flows"></a>Veri akışlarını izleme



Veri akışınızı oluşturma ve hata ayıklamayı tamamladıktan sonra, veri akışınızı bir işlem hattı bağlamı içindeki bir zamanlamaya göre yürütülecek şekilde zamanlamak isteyeceksiniz. Azure Data Factory işlem hattını Tetikleyicileri kullanarak zamanlayabilirsiniz. Ya da işlem hattı bağlamında veri akışınızı test etmek için tek çalıştırma yürütme yürütmek üzere Azure Data Factory işlem hattı Oluşturucusisinden şimdi Tetikle seçeneğini kullanabilirsiniz.

İşlem hattınızı yürüttüğünüzde, işlem hattını ve veri akışı etkinliği dahil olmak üzere işlem hattında bulunan tüm etkinlikleri izleyebilirsiniz. Sol taraftaki Azure Data Factory UI panelinde bulunan izleyici simgesine tıklayın. Aşağıdakine benzer bir ekran görürsünüz. Vurgulanan simgeler, veri akışı etkinliği dahil olmak üzere işlem hattındaki etkinliklerin ayrıntılarına gitmenizi sağlar.

![Veri Akışını İzleme](media/data-flow/mon001.png "Veri Akışını İzleme")

Bu düzeyde istatistikler, çalışma süreleri ve durumu da dahil olmak üzere görüntülenir. Etkinlik düzeyindeki çalışma KIMLIĞI, işlem hattı düzeyinde çalıştırma KIMLIĞININ farklıdır. Önceki düzeydeki çalıştırma KIMLIĞI işlem hattı içindir. Duygunluğa tıklamak, veri akışı yürütmeyle ilgili ayrıntılı bilgiler verir.

![Veri Akışını İzleme](media/data-flow/mon002.png "Veri Akışını İzleme")

Grafik düğüm izleme görünümünde olduğunuzda, veri akışı grafınızı yalnızca Basitleştirilmiş bir görünüm sürümü görürsünüz.

![Veri Akışını İzleme](media/data-flow/mon003.png "Veri Akışını İzleme")

## <a name="view-data-flow-execution-plans"></a>Veri akışı yürütme planlarını görüntüleme

Veri akışınız Spark 'ta yürütüldüğünde, Azure Data Factory veri akışınızı tamamen temel alarak en iyi kod yollarını belirler. Ek olarak, yürütme yolları farklı genişleme düğümlerinde ve veri bölümlerinde de gerçekleşebilir. Bu nedenle, izleme grafiği, dönüştürmelerinizin yürütme yolunu dikkate alarak akışınızın tasarımını temsil eder. Ayrı düğümlere tıkladığınızda, kümede birlikte yürütülen kodu temsil eden "gruplandırmaları" görürsünüz. Gördüğünüz zamanlamalar ve sayımlar, tasarımınızda bireysel adımlara karşılık bu grupları temsil eder.

![Veri Akışını İzleme](media/data-flow/mon004.png "Veri Akışını İzleme")

* İzleme penceresindeki açık alana tıkladığınızda, alt bölmedeki istatistikler her bir havuz için zamanlama ve satır sayılarını ve dönüştürme kökenini için havuz verilerine işaret eden dönüşümleri görüntüler.

* Tek tek dönüştürmeleri seçtiğinizde, sağ bölmede bölüm istatistiklerini, sütun sayılarını, çarpıklığı (bölümler arasında dağıtılan veriler ne kadar eşit olduğu) ve basıklık (nasıl çıkmanız gerekirse) gösteren ek geri bildirim alırsınız.

* Düğüm görünümündeki havuza tıkladığınızda, kökenini sütununu görürsünüz. Her sütun, veri akışınız genelinde havuza akacak şekilde birikmiş üç farklı yöntem vardır. Bunlar:

  * Hesaplandı: sütunu, Koşullu işleme için veya veri akışındaki bir ifade dahilinde kullanın, ancak bunu havuza girmeyin
  * Türetilmiş: sütun, akışta oluşturduğunuz yeni bir sütundur, yani kaynakta yer almıyor
  * Eşlendi: kaynak, kaynaktan geldiğini ve bir havuz alanıyla eşlemenizi sağlar
  * Veri akışı durumu: yürütmenin geçerli durumu
  * Küme başlangıç zamanı: veri akışı yürütmenizle ilgili JıT Spark işlem ortamının elde edilecek süre miktarı
  * Dönüşüm sayısı: akışınızda kaç dönüştürme adımı yürütüldüğü
  
![Acıt veri akışı Izleme](media/data-flow/monitornew.png "Veri akışı Izleme yeni")  
  
## <a name="monitor-icons"></a>Simgeleri izle

Bu simge, dönüştürme verilerinin kümede zaten önbelleğe alındığı anlamına gelir; bu nedenle zamanlamalar ve yürütme yolu şu hesaba alınır:

![Veri Akışını İzleme](media/data-flow/mon004.png "Veri Akışını İzleme")

Ayrıca, dönüşümde yeşil daire simgeleri görürsünüz. Bunlar, verilerin akan havuz sayısını temsil eder.
