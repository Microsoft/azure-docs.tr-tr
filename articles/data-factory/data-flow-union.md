---
title: Azure Data Factory eşleme veri akışı yeni dal dönüştürmesi
description: Azure Data Factory eşleme veri akışı yeni dal dönüştürmesi
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 4832cd2036f615d1e90d5e7a21c1a9832c2fa837
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387128"
---
# <a name="mapping-data-flow-union-transformation"></a>Veri akışı birleşim dönüşümünü eşleme



Birleşim birden çok veri akışını birleştirme dönüşümünde yeni çıkış olarak bu akışların SQL birleşimi ile tek bir birleştirir. Her giriş akışındaki tüm şema, bir JOIN anahtarına gerek duymadan veri akışınız içinde birleştirilir.

Her iki kaynak veri da dahil olmak üzere, her bir yapılandırılmış satırın yanındaki "+" simgesini, hem de veri akışınızda mevcut dönüşümlerden Akışlar ' ı seçerek ayarlar tablosunda n-sayıda akışı birleştirebilirsiniz.

![Birleşim dönüşümü](media/data-flow/union.png "Birleşim")

Bu durumda, birden fazla kaynaktan farklı meta verileri birleştirebilir (Bu örnekte, üç farklı kaynak dosyası) ve bunları tek bir akışta birleştirebilirsiniz:

![Union dönüşümüne genel bakış](media/data-flow/union111.png "Birleşim 1")

Bunu başarmak için, eklemek istediğiniz tüm kaynağı ekleyerek birleşim ayarlarına ek satırlar ekleyin. Ortak arama veya bir JOIN anahtarına gerek yoktur:

![Birleşim dönüştürme ayarları](media/data-flow/unionsettings.png "Birleşim ayarları")

Birleşiminizden sonra bir seçme dönüşümü ayarlarsanız, örtüşen alanları veya headerless kaynaklarından isimsiz olmayan alanları yeniden adlandırabilirsiniz. Üç farklı kaynaktan bu örnekteki meta verileri 132 toplam sütun ile birleştirme bölümüne bakmak için "ıncele" düğmesine tıklayın:

![Birleşim dönüşümü son](media/data-flow/union333.png "Birleşim 3")

## <a name="name-and-position"></a>Ad ve konum

"Ada göre birleşim" seçeneğini belirlediğinizde her bir sütun değeri, yeni bir birleştirilmiş meta veri şeması ile her bir kaynaktaki karşılık gelen sütuna bırakılır.

"Konuma göre birleşim" seçeneğini belirlerseniz her bir sütun değeri, her bir kaynaktaki verilerin aynı akışa eklendiği yeni bir Birleşik veri akışına neden olur.

![Birleşim çıkışı](media/data-flow/unionoutput.png "Birleşim çıkışı")

## <a name="next-steps"></a>Sonraki adımlar

[JOIN](data-flow-join.md) ve [Exists](data-flow-exists.md)dahil benzer dönüşümleri keşfedebilir.
