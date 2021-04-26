---
title: Azure CDN için standart kurallar altyapısı başvurusu | Microsoft Docs
description: Azure Content Delivery Network için standart kurallar altyapısından eşleşme koşulları ve eylemleri için başvuru belgeleri (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 1a0f4456f38939632026645500dd48acbf7dbc88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93242217"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN için standart kural altyapısı başvurusu

Azure Content Delivery Network için [standart kurallar altyapısında](cdn-standard-rules-engine.md) (Azure CDN), bir kural bir veya daha fazla eşleşme koşulu ve bir eylemden oluşur. Bu makalede, Azure CDN için standart kurallar altyapısında bulunan eşleşme koşullarına ve özelliklerine ilişkin ayrıntılı açıklamalar sağlanmaktadır.

Kural altyapısı, belirli istek türlerinin standart Azure CDN nasıl işlendiği konusunda son yetkili olacak şekilde tasarlanmıştır.

**Kuralların ortak kullanımları**:

- Özel bir önbellek ilkesini geçersiz kılın veya tanımlayın.
- İstekleri yeniden yönlendirin.
- HTTP isteği ve yanıt üst bilgilerini değiştirin.

## <a name="terminology"></a>Terminoloji

Kurallar altyapısında bir kural tanımlamak için, [eşleşme koşullarını](cdn-standard-rules-engine-match-conditions.md) ve [eylemleri](cdn-standard-rules-engine-actions.md)ayarlayın:

 ![Azure CDN kuralları yapısı](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Her kurala en fazla on eşleşme koşulu ve beş eylem olabilir. Her Azure CDN uç noktası en fazla 25 kurala sahip olabilir. 

Bu sınıra dahil edilen varsayılan bir *genel kuraldır*. Genel kural eşleşme koşullarına sahip değildir; bir genel kuralda tanımlanan eylemler her zaman tetiklenir.

   > [!IMPORTANT]
   > Birden çok kuralın listelenme sırası kuralların işlenme sırasını etkiler. Bir kuralda belirtilen eylemlerin sonraki bir kuralla üzerine yazılabilir.

## <a name="limits-and-pricing"></a>Sınırlar ve fiyatlandırma 

Her Azure CDN uç noktası en fazla 25 kurala sahip olabilir. Her kurala en fazla on eşleşme koşulu ve beş eylem olabilir. Kural altyapısı için fiyatlandırma aşağıdaki boyutları izler: 
- Kurallar: her ay kural başına $1 
- Işlenen istek sayısı: milyon istek başına $0,60
- İlk 5 kural ücretsiz kalacak

## <a name="syntax"></a>Syntax

Kural içinde özel karakterlerin nasıl ele alındığı, farklı eşleşme koşullarının ve eylemlerin metin değerlerini nasıl işleydiğine göre farklılık gösterir. Bir eşleştirme koşulu veya eylemi, aşağıdaki yollarla metni yorumlayabilir:

- [Değişmez değerler](#literal-values)
- [Joker karakter değerleri](#wildcard-values)


### <a name="literal-values"></a>Değişmez değerler

Değişmez değer olarak yorumlanan metin, bir kuralda eşleşmesi gereken değerin bir parçası olarak *% simgesi hariç* tüm özel karakterleri değerlendirir. Örneğin, yalnızca tam değer bulunduğunda, olarak ayarlanmış bir değişmez değer eşleşmesi koşulu `'*'` karşılandı `'*'` .

URL kodlamasını göstermek için yüzde işareti kullanılır (örneğin, `%20` ).

### <a name="wildcard-values"></a>Joker karakter değerleri

Şu anda standart kurallar altyapısındaki **URLPath eşleşme koşulunda** joker karakteri destekliyoruz. \*Karakter bir veya daha fazla karakteri temsil eden bir joker karakterdir. 

## <a name="next-steps"></a>Sonraki adımlar

- [Standart kurallar altyapısındaki koşulları Eşleştir](cdn-standard-rules-engine-match-conditions.md)
- [Standart kurallar altyapısındaki eylemler](cdn-standard-rules-engine-actions.md)
- [Standart kural altyapısını kullanarak HTTPS'yi zorlama](cdn-standard-rules-engine.md)
- [Azure CDN genel bakış](cdn-overview.md)
