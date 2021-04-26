---
title: Eşleme veri akışında birleşim dönüşümü
description: Azure Data Factory eşleme veri akışı yeni dal dönüştürmesi
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/27/2020
ms.openlocfilehash: e61c53cabc8f784ba9f0a3e78b5de01be134b0cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367852"
---
# <a name="union-transformation-in-mapping-data-flow"></a>Eşleme veri akışında birleşim dönüşümü

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Birleşim birden çok veri akışını birleştirme dönüşümünde yeni çıkış olarak bu akışların SQL birleşimi ile tek bir birleştirir. Her giriş akışındaki tüm şema, bir JOIN anahtarına gerek duymadan veri akışınız içinde birleştirilir.

Her iki kaynak veri da dahil olmak üzere, her bir yapılandırılmış satırın yanındaki "+" simgesini, hem de veri akışınızda mevcut dönüşümlerden Akışlar ' ı seçerek ayarlar tablosunda n-sayıda akışı birleştirebilirsiniz.

ADF 'nin eşleme veri akışındaki birleşim dönüşümünde kısa bir video izlenecek yol aşağıda verilmiştir:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

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
