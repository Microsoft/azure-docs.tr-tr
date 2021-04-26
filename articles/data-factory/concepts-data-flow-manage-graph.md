---
title: Eşleme veri akışı grafiğini yönetme
description: Eşleme veri akışı grafiğini etkin bir şekilde yönetme ve düzenleme
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2020
ms.openlocfilehash: f55364fe0eabae4bb1698bd655cde8d3e01b127a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590469"
---
# <a name="managing-the-mapping-data-flow-graph"></a>Eşleme veri akışı grafiğini yönetme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Eşleme veri akışları, veri akışı grafiği olarak bilinen bir tasarım yüzeyi kullanılarak yazılır. Grafikte, dönüştürme mantığı soldan sağa oluşturulmuştur ve ek veri akışları yukarıdan aşağı eklenir. Yeni bir dönüşüm eklemek için, varolan bir dönüşümün sağ alt köşesindeki artı işaretini seçin.

![Tuval](media/data-flow/canvas-2.png)

Veri akışlarınız daha karmaşık olduğu için, veri akışı grafiğine etkin bir şekilde gezinmek ve bunları yönetmek için aşağıdaki mekanizmalardan yararlanın. 

## <a name="moving-transformations&quot;></a>Dönüşümleri taşıma

Veri akışlarını eşleme bölümünde, bir bağlı dönüştürme mantığı kümesi **akış** olarak bilinir. **Gelen akış** alanı, geçerli dönüştürmeyi hangi veri akışının beslemeyi belirler. Her bir dönüşüme, işlevine bağlı olarak bir veya iki gelen akış vardır ve bir çıkış akışı temsil eder. Gelen akışların çıkış şeması, geçerli dönüşüm tarafından hangi sütun meta verilerine başvuramayacağını belirler.

![Düğümü taşı](media/data-flow/move-nodes.png &quot;düğümü taşı")

Ardışık düzen tuvalinin aksine, veri akışı dönüştürmeleri bir sürükle ve bırak modeli kullanılarak düzenlenmez. Bir dönüşümün gelen akışını veya "taşıma" dönüşümünü değiştirmek için **gelen akış** açılan listesinden farklı bir değer seçin. Bunu yaptığınızda, tüm aşağı akış dönüştürmeleri düzenlenmiş dönüşüme göre hareket eder. Grafik, yeni mantıksal akışı göstermek için otomatik olarak güncelleştirilecek. Gelen akışı zaten aşağı akış dönüşümünü içeren bir dönüşüme değiştirirseniz yeni bir dal veya paralel veri akışı oluşturulur. [Eşleme veri akışı 'nda yeni dallar](data-flow-new-branch.md)hakkında daha fazla bilgi edinin.

## <a name="hide-graph-and-show-graph"></a>Grafiği gizle ve grafiği göster

Dönüşümünüzü düzenlediğinizde, pano 'nun tamamını gizleyerek, yapılandırma panelini genişleterek tuvali genişletebilirsiniz. Tuvalin sağ tarafında bulunan yukarı bakan köşeli çift ayraca tıklayın.

![Grafiği gizle](media/data-flow/hide-graph.png "grafiği gizle")

Grafik gizli olduğunda, **İleri** veya **geri**' ye tıklayarak bir akış içindeki dönüşümler arasında geçiş yapabilirsiniz. Grafiği göstermek için aşağı bakan köşeli çift ayraca tıklayın.

![Grafiği göster](media/data-flow/show-graph.png "Grafiği göster")

## <a name="searching-for-transformations"></a>Dönüşümler aranıyor

Grafiğinizde bir dönüştürmeyi hızlıca bulmak için yakınlaştırma ayarının üzerindeki **arama** simgesine tıklayın.

![Ekran görüntüsü arama düğmesini gösterir.](media/data-flow/search-1.png "Arama grafiği")

Bir dönüştürmeyi bulmak için dönüşüm adına veya açıklamaya göre arama yapabilirsiniz.

![Ekran görüntüsü arama metin kutusunu gösterir.](media/data-flow/search-2.png "Arama grafiği")

## <a name="hide-reference-nodes"></a>Başvuru düğümlerini gizle

Veri akışınız herhangi bir birleştirme, arama, mevcut veya birleşim dönüşümlerine sahipse, veri akışı tüm gelen akışlara başvuru düğümlerini gösterir. Alınan dikey alan miktarını en aza indirmek isterseniz, başvuru düğümlerinizi en aza indirmenize olanak sağlayabilirsiniz. Bunu yapmak için tuvale sağ tıklayıp **başvuru düğümlerini Gizle**' yi seçin.

![Başvuru düğümlerini gizle](media/data-flow/hide-reference-nodes.png "Başvuru düğümlerini gizle")

## <a name="next-steps"></a>Sonraki adımlar

Veri akışı mantığınızı tamamladıktan sonra, [hata ayıklama modunu](concepts-data-flow-debug-mode.md) açın ve bir veri önizlemesinde test edin.
