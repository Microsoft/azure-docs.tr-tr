---
title: Azure Data Factory eşleme veri akışı sıralama dönüşümü
description: Azure Data Factory eşleme veri sıralama dönüşümü
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 029ce3c509d3f4d241012d3786e60f0c6e95fdc2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387186"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Azure Data Factory veri akışı sıralama dönüşümleri



![Sıralama ayarları](media/data-flow/sort.png "Sırala")

Sıralama dönüştürmesi, geçerli veri akışındaki gelen satırları sıralamanıza olanak tanır. Sıralama dönüştürmesinin giden satırları daha sonra, ayarladığınız sıralama kurallarını izler. Her bir alanın yanındaki ok göstergesini kullanarak sütunları tek tek seçebilir ve ASC veya DEC olarak sıralayabilirsiniz. Sıralamayı uygulamadan önce sütunu değiştirmeniz gerekiyorsa, ifade düzenleyicisini başlatmak için "hesaplanan sütunlar" a tıklayın. Bu, sıralama için bir sütun uygulamak yerine sıralama işlemi için bir ifade oluşturmak üzere bir fırsat sağlar.

## <a name="case-insensitive"></a>Büyük/Küçük harfe duyarsız
Dize veya metin alanlarını sıralarken büyük/küçük harfi yoksaymak istiyorsanız "büyük/küçük harf duyarsız" seçeneğini etkinleştirebilirsiniz.

"Yalnızca bölümlerin Içinde sıralama" Spark veri bölümlemesini kullanır. Gelen verileri yalnızca her bölüm içinde sıralayarak, veri akışları veri akışının tamamını sıralamak yerine bölümlenmiş verileri sıralayabilir.

Sıralama dönüştürmesinin her bir sıralama koşulu yeniden düzenlenebilir. Bu nedenle, bir sütunu sıralama önceliğinde daha yukarıya taşımanız gerekiyorsa, bu satırı farenize koyun ve sıralama listesinde daha yüksek veya daha aşağı taşıyın.

Sıralamaya göre bölümleme etkileri

ADF veri akışı, birden fazla düğüm ve bölüm arasında dağıtılan verilerle büyük veri Spark kümelerinde yürütülür. Verileri aynı sırada tutmak için sıralama dönüşümüyle karşılaşırsanız, veri akışınızı mimara göre tasarlarken bunu göz önünde bulundurmanız önemlidir. Verilerinizi sonraki bir dönüşümde yeniden bölümlendirmayı seçerseniz, bu veri reshuffling nedeniyle sıralıyorsanız kaybedebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Sıralama sonrasında, [Toplam dönüştürmeyi](data-flow-aggregate.md) kullanmak isteyebilirsiniz
