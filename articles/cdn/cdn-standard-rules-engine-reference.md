---
title: Azure CDN için standart kurallar altyapısı başvurusu | Microsoft Docs
description: Azure Content Delivery Network için standart kurallar altyapısından eşleşme koşulları ve eylemleri için başvuru belgeleri (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: aa401150ee7a0f02e809ad702b8247e18081c8a3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171555"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN için standart kurallar altyapısı başvurusu

Azure Content Delivery Network için [standart kurallar altyapısında](cdn-standard-rules-engine.md) (Azure CDN), bir kural bir veya daha fazla eşleşme koşulu ve bir eylemden oluşur. Bu makalede, Azure CDN için standart kurallar altyapısında bulunan eşleşme koşullarına ve özelliklerine ilişkin ayrıntılı açıklamalar sağlanmaktadır.

Kural altyapısı, belirli istek türlerinin standart Azure CDN nasıl işlendiği konusunda son yetkili olacak şekilde tasarlanmıştır.

**Kuralların ortak kullanımları**:

- Özel bir önbellek ilkesini geçersiz kılın veya tanımlayın.
- İstekleri yeniden yönlendirin.
- HTTP isteği ve yanıt üst bilgilerini değiştirin.

## <a name="terminology"></a>Terminoloji

Kurallar altyapısında bir kural tanımlamak için, [eşleşme koşullarını](cdn-standard-rules-engine-match-conditions.md) ve [eylemleri](cdn-standard-rules-engine-actions.md)ayarlayın:

 ![Azure CDN kuralları yapısı](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Her kuralda en fazla dört eşleşme koşulu ve üç eylem olabilir. Her Azure CDN uç noktası en fazla beş kurala sahip olabilir. 

Azure CDN uç noktası için geçerli beş kural sınırına dahil edilen varsayılan bir *genel kuraldır*. Genel kural eşleşme koşullarına sahip değildir ve bir genel kuralda tanımlanan eylemler her zaman tetikler.

## <a name="syntax"></a>Sözdizimi

Kural içinde özel karakterlerin nasıl ele alındığı, farklı eşleşme koşullarının ve eylemlerin metin değerlerini nasıl işleydiğine göre farklılık gösterir. Bir eşleştirme koşulu veya eylemi, aşağıdaki yollarla metni yorumlayabilir:

- [Değişmez değerler](#literal-values)
- [Joker karakter değerleri](#wildcard-values)


### <a name="literal-values"></a>Değişmez değerler

Değişmez değer olarak yorumlanan metin, bir kuralda eşleşmesi gereken değerin bir parçası olarak *% simgesi hariç* tüm özel karakterleri değerlendirir. Örneğin, `'*'` olarak ayarlanmış bir sabit değer eşleştirme koşulu yalnızca tam değer `'*'` bulunduğunda karşılanır.

URL kodlamasını göstermek için yüzde işareti kullanılır (örneğin, `%20`).

### <a name="wildcard-values"></a>Joker karakter değerleri

Joker karakter değeri olarak yorumlanan metin özel karakterlere ek anlam atar. Aşağıdaki tabloda, özel karakterlerin standart kurallar altyapısında nasıl yorumlanacağı açıklanmaktadır:

Karakter | Açıklama
----------|------------
\ | Bu tabloda belirtilen karakterlerden herhangi birini atlamak için ters eğik çizgi kullanılır. Bir ters eğik çizgi, önüne kaçılması gereken özel karakterden önce belirtilmelidir. Örneğin, aşağıdaki sözdizimi bir yıldız işaretine çıkar: `\*`
% | URL kodlamasını göstermek için yüzde işareti kullanılır (örneğin, `%20`).
\* | Yıldız işareti bir veya daha fazla karakteri temsil eden bir joker karakterdir.
Boşlu | Boşluk karakteri, bir eşleşme koşulunun belirtilen değerlerden veya desenlerden herhangi biri tarafından karşılanamayacağını gösterir.
tek tırnak işaretleri | Tek tırnak işareti özel bir anlamı yoktur. Ancak, bir dizi tek tırnak işareti, bir değerin değişmez değer olarak değerlendirilip değerlendirilmeyeceğini gösterir. Tek tırnak işaretleri aşağıdaki yollarla kullanılabilir:<ul><li>Belirtilen değer karşılaştırma değerinin herhangi bir bölümüyle eşleştiğinde eşleşme koşulunun karşılanmasına izin vermek için.  Örneğin, `'ma'` aşağıdaki dizelerin herhangi biriyle eşleşir: <ul><li>/Business/**ma**rathon/Asset.htm</li><li>**ma**p. gif</li><li>/Business/Template. **ma**p</li></ul><li>Özel bir karakterin sabit karakter olarak belirtilmesine izin vermek için. Örneğin, bir boşluk karakterini tek tırnak işareti (`' '` veya `'<sample value>'`) kümesinde birleştirerek bir sabit alan karakteri belirtebilirsiniz.</li><li>Boş değerin belirtilmesine izin vermek için. Bir dizi tek tırnak işareti ( **' '** ) belirterek boş bir değer belirtin.</li></ul>**Önemli**:<br /><ul><li>Belirtilen değer bir joker karakter içermiyorsa, değer otomatik olarak değişmez değer olarak değerlendirilir. Sabit değer değeri için bir dizi tek tırnak işareti belirtmeniz gerekmez.</li><li>Bu tablodaki başka bir karakteri atlamak için ters eğik çizgi kullanılmazsa, bir dizi tek tırnak işareti içinde belirtildiğinde ters eğik çizgi yok sayılır.</li><li>Özel bir karakteri sabit karakter olarak belirtmenin başka bir yolu da bir ters eğik çizgi (`\`) kullanarak kaçış yöntemidir.</li></ul>

## <a name="next-steps"></a>Sonraki adımlar

- [Standart kurallar altyapısındaki koşulları Eşleştir](cdn-standard-rules-engine-match-conditions.md)
- [Standart kurallar altyapısındaki eylemler](cdn-standard-rules-engine-actions.md)
- [Standart kurallar altyapısını kullanarak HTTPS 'yi zorla](cdn-standard-rules-engine.md)
- [Azure CDN genel bakış](cdn-overview.md)
