---
title: Azure Data Factory veri akışı grafikleri
description: Data Factory veri akışı grafikleriyle çalışma
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 38f85dea554000daf89689a311c091bc9796a658
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74015905"
---
# <a name="mapping-data-flow-graphs"></a>Veri akışı grafiklerini eşleme

Eşleme veri akışları tasarım yüzeyi, veri akışlarını yukarıdan aşağı doğru bir şekilde oluşturduğunuz bir "yapım" yüzeyidir. Her bir dönüşümün bir artı (+) simgesiyle eklenmiş bir araç kutusu vardır. Serbest biçimli bir DAG ortamındaki kenarlar aracılığıyla düğümleri bağlamak yerine iş mantığınızı yoğunlaşın.

Aşağıda, veri akışı grafiğini yönetmek için yerleşik mekanizmalar verilmiştir.

## <a name="move-nodes"></a>Düğümleri taşıma

![Toplu dönüştürme seçenekleri](media/data-flow/agghead.png "Toplayıcı üst bilgisi")

Bir sürükle ve bırak paradigması olmadan, bir dönüşüm düğümünü "taşıma" yöntemi, gelen akışı değiştirlemektir. Bunun yerine, "gelen akışı" değiştirerek dönüşümleri taşıyabilirsiniz.

## <a name="streams-of-data-inside-of-data-flow"></a>Veri akışı içindeki veri akışları

Azure Data Factory veri akışı ' nda akışlar, veri akışını temsil eder. Dönüştürme ayarları bölmesinde, "gelen akış" alanını görürsünüz. Bu, size gelen veri akışının bu dönüşümü beslemeyi olduğunu söyler. Gelen akış adına tıklayıp başka bir veri akışı seçerek, grafikteki dönüştürme düğümünüz fiziksel konumunu değiştirebilirsiniz. O akıştaki sonraki dönüşümlerle birlikte geçerli dönüşüm yeni konuma taşınır.

Bir dönüştürmeyi sonrasında bir veya daha fazla dönüşümle taşıyorsanız, veri akışındaki yeni konum yeni bir dal ile birleştirilir.

Seçtiğiniz düğümden sonra sonraki dönüşümünüz yoksa, yalnızca o dönüşüm yeni konuma taşınır.

## <a name="hide-graph-and-show-graph"></a>Grafiği gizle ve grafiği göster

En sağdaki yapılandırma bölmesinin sağ tarafında, dönüşüm yapılandırmalarında çalışırken alt bölmeyi tam ekran olarak genişletebileceğiniz bir düğme vardır. Bu, grafiğin yapılandırmalarında gezinmek için "önceki" ve "ileri" düğmelerini kullanmanıza imkan tanır. Grafik görünümüne geri dönmek için aşağı düğmesine tıklayın ve bölme ekranına geri dönün.

## <a name="search-graph"></a>Arama grafiği

Grafik üzerinde tasarım yüzeyinde ara düğmesini kullanarak arama yapabilirsiniz.

![Search](media/data-flow/search001.png "Arama grafiği")

## <a name="next-steps"></a>Sonraki adımlar

Veri akışı tasarımınızı tamamladıktan sonra, hata ayıklama düğmesini açın ve doğrudan [veri akışı Tasarımcısı](concepts-data-flow-debug-mode.md) 'nda veya işlem [hattı hata ayıklamada](control-flow-execute-data-flow-activity.md)hata ayıklama modunda test edin.
