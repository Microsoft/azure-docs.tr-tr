---
title: Azure Data Factory eşleme veri akışı UNPIVOT dönüşümü
description: Azure Data Factory eşleme veri akışı UNPIVOT dönüşümü
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 41423f704c87f05ad3e31b253d4a80799a554849
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387739"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Özet Azure Data Factory dönüştürmeyi kaldır



Tek bir kayıttaki birden fazla sütundaki değerleri tek bir sütunda aynı değerlerle birden çok kayda genişleterek, Normalleştirilmemiş bir veri kümesini daha normalleştirilmiş bir sürüme dönüştürmek için bir yol olarak ADF eşleme veri akışı ' nda UNPIVOT kullanın.

![Özet olmayan dönüştürme](media/data-flow/unpivot1.png "UNPIVOT seçenekleri 1")

## <a name="ungroup-by"></a>Çözme ölçütü

![Özet olmayan dönüştürme](media/data-flow/unpivot5.png "Özet seçeneklerini kaldırma 2")

İlk olarak, pivot toplamadır için gruplandırmak istediğiniz sütunları ayarlayın. Gruplama için bir veya daha fazla sütunu sütun listesinin yanındaki + işaretiyle ayarlayın.

## <a name="unpivot-key"></a>Özet olmayan anahtar

![Özet olmayan dönüştürme](media/data-flow/unpivot6.png "UNPIVOT seçenekleri 3")

Pivot tuşu, ADF 'nin satırdan sütuna kadar Pivot olacağını belirten sütundur. Varsayılan olarak, bu alan için veri kümesindeki her benzersiz değer bir sütuna Özet olur. Ancak, isteğe bağlı olarak sütun değerlerine Pivot eklemek istediğiniz veri kümesinden değerleri girebilirsiniz.

## <a name="unpivoted-columns"></a>Özetlenen sütunlar

![Özet olmayan dönüştürme](media/data-flow//unpivot7.png "UNPIVOT seçenekleri 4")

Son olarak, özetleme değerleri için kullanmak istediğiniz toplamayı ve yeni çıkış projeksiyonundaki sütunları dönüşümden nasıl görüntülenmesini istediğinizi seçin.

Seçim Satır değerlerinden her yeni sütun adına eklenmek üzere ön ek, orta ve son ek içeren bir adlandırma deseninin ayarlanabilir olmasını sağlayabilirsiniz.

Örneğin, "bölge" ile "Sales" özetleme, her bir satış değerinden yalnızca yeni sütun değerleri sunar. Örneğin: "25", "50", "1000",... Bununla birlikte, "Sales" önekini bir önek değeri olarak ayarlarsanız, "Sales" değerinin ön eki olur.

<img src="media/data-flow/unpivot3.png" width="400">

Sütun düzenlemesini "normal" olarak ayarlamak, özetleme sütunlarının tümünü toplanmış değerlerle birlikte gruplandırır. Sütun düzenleme "yan yana" olarak ayarlandığında sütun ve değer arasında alternatif olur.

![Özet olmayan dönüştürme](media/data-flow//unpivot7.png "Özet kaldırma seçenekleri 5")

Özet olmayan son veri sonuç kümesi, artık ayrı satır değerlerine göre özetlenen sütun toplamlarını gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Satırları sütunlara eklemek için [Pivot dönüşümünü](data-flow-pivot.md) kullanın.
