---
title: moreLikeThis (Önizleme) sorgu özelliği
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama REST API önizleme sürümlerinde kullanılabilen moreLikeThis (Önizleme) özelliğini açıklar.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fdde89f9ff88b15c464af805b81708b268e5ddf5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721743"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure Bilişsel Arama moreLikeThis (Önizleme)

> [!IMPORTANT] 
> Bu özellik şu anda genel önizleme aşamasındadır. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API sürüm 2019-05-06-önizleme](search-api-preview.md) bu özelliği sağlar. Şu anda portal veya .NET SDK desteği yok.

`moreLikeThis=[key]`, belge anahtarı tarafından belirtilen belgeye benzer belgeleri bulan [arama belgeleri API 'sindeki](https://docs.microsoft.com/rest/api/searchservice/search-documents) bir sorgu parametresidir. `moreLikeThis`ile bir arama isteği yapıldığında, bu belgeyi en iyi şekilde tanımlayan belirtilen belgeden ayıklanan arama terimleriyle bir sorgu oluşturulur. Oluşturulan sorgu daha sonra arama isteğini yapmak için kullanılır. Varsayılan olarak, tüm aranabilir alanların içeriği, `searchFields` parametresini kullanarak belirttiğiniz tüm kısıtlı alanlar olarak değerlendirilir. `moreLikeThis` parametresi, `search=[string]`arama parametresiyle birlikte kullanılamaz.

Varsayılan olarak, en üst düzey aranabilir alanların içeriği göz önünde bulundurululur. Bunun yerine belirli alanları belirtmek istiyorsanız `searchFields` parametresini kullanabilirsiniz. 

[Karmaşık bir türdeki](search-howto-complex-data-types.md)aranabilir alt alanlarda moreLikeThis kullanamazsınız.

## <a name="examples"></a>Örnekler 

Aşağıda bir moreLikeThis sorgusuna örnek verilmiştir. Sorgu, açıklama alanları `moreLikeThis` parametresi tarafından belirtildiği gibi, kaynak belgenin alanına benzer olan belgeleri bulur.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>Sonraki adımlar

Bu özellikle denemek için herhangi bir Web testi aracı kullanabilirsiniz.  Bu alıştırma için Postman kullanmanızı öneririz.

> [!div class="nextstepaction"]
> [Postman kullanarak Azure Bilişsel Arama REST API 'Lerini keşfet](search-get-started-postman.md)