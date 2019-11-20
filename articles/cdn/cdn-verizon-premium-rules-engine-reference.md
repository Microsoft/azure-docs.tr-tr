---
title: Azure CDN kuralları altyapısı başvurusu | Microsoft Docs
description: Azure CDN kuralları altyapısı için başvuru belgeleri koşullar ve özelliklerle eşleşir.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: aab93204c850223756f28a56ea550f912e28e0d2
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996763"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Verizon Premium kuralları altyapı başvurusundan Azure CDN

Bu makalede, Azure Content Delivery Network (CDN) [kuralları altyapısının](cdn-verizon-premium-rules-engine.md)kullanılabilir eşleşme koşullarının ve özelliklerinin ayrıntılı açıklamaları listelenmektedir.

Kural altyapısı, belirli istek türlerinin CDN tarafından nasıl işlendiği konusunda son yetkili olacak şekilde tasarlanmıştır.

**Ortak kullanımlar**:

- Özel bir önbellek ilkesini geçersiz kılın veya tanımlayın.
- Hassas içeriğe yönelik güvenli veya reddetme istekleri.
- İstekleri yeniden yönlendirin.
- Özel günlük verilerini depolayın.

## <a name="terminology"></a>Terminoloji

Bir kural [**Koşullu ifadeler**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**eşleşme koşulları**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)ve [**özellikleri**](cdn-verizon-premium-rules-engine-reference-features.md)kullanılarak tanımlanır. Bu öğeler aşağıdaki çizimde vurgulanır:

 ![CDN eşleşme koşulu](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Sözdizimi

Özel karakterlerin ele alındığı şekilde, eşleşme koşulunun veya özelliğin metin değerlerini nasıl işleydiğine göre farklılık gösterir. Bir eşleştirme koşulu veya özelliği, aşağıdaki yollarla metni yorumlayabilir:

1. [**Değişmez değerler**](#literal-values)
2. [**Joker karakter değerleri**](#wildcard-values)
3. [**Normal ifadeler**](#regular-expressions)

### <a name="literal-values"></a>Değişmez değerler

Bir sabit değer olarak yorumlanan metin,% symbol özel durumuyla, eşleşmesi gereken değerin bir parçası olarak tüm özel karakterleri ele alır. Diğer bir deyişle, değişmez değer eşleştirme koşulu `\'*'\` yalnızca tam değer (yani, `\'*'\`) bulunduğunda karşılanır.

Bir yüzde sembolü, URL kodlamasını göstermek için kullanılır (örneğin, `%20`).

### <a name="wildcard-values"></a>Joker karakter değerleri

Joker karakter değeri olarak yorumlanan metin özel karakterlere ek anlam atar. Aşağıdaki tabloda aşağıdaki karakter kümesinin nasıl yorumlanacağı açıklanmaktadır:

Karakter | Açıklama
----------|------------
\ | Bu tabloda belirtilen karakterlerden herhangi birini atlamak için ters eğik çizgi kullanılır. Bir ters eğik çizgi, önüne kaçılması gereken özel karakterden önce belirtilmelidir.<br/>Örneğin, aşağıdaki sözdizimi bir yıldız işaretine çıkar:`\*`
% | Bir yüzde sembolü, URL kodlamasını göstermek için kullanılır (örneğin, `%20`).
\* | Yıldız işareti bir veya daha fazla karakteri temsil eden bir joker karakterdir.
Uzay | Boşluk karakteri, bir eşleşme koşulunun belirtilen değerlerden veya desenlerden herhangi biri tarafından karşılanamayacağını gösterir.
deeri | Tek bir teklifin özel anlamı yoktur. Ancak, bir değerin değişmez değer olarak değerlendirilip değerlendirilmeyeceğini göstermek için bir dizi tek tırnak kullanılır. Aşağıdaki yollarla kullanılabilir:<br><br/>-Belirtilen değerin karşılaştırma değerinin herhangi bir bölümüyle eşleşmesi durumunda eşleşme koşulunun karşılanmasına izin verir.  Örneğin, `'ma'` aşağıdaki dizelerin herhangi biriyle eşleşir: <br/><br/>/Business/**ma**rathon/Asset.htm<br/>**ma**p. gif<br/>/Business/Template. **ma**p<br /><br />-Özel bir karakterin sabit karakter olarak belirtilmesini sağlar. Örneğin, bir boşluk karakterini tek tırnak (yani, `' '` veya `'sample value'`) kümesi içinde çevreleyerek bir sabit boşluk karakteri belirtebilirsiniz.<br/>-Boş değer belirtilmesini sağlar. Bir dizi tek tırnak (' ') belirterek boş bir değer belirtin.<br /><br/>**Önemli:**<br/>-Belirtilen değer bir joker karakter içermiyorsa, otomatik olarak değişmez değer olarak değerlendirilir, bu da tek tırnak kümesini belirtmek için gerekli değildir.<br/>-Ters eğik çizgi bu tablodaki başka bir karakterle eşleşmezse, bir dizi tek tırnak içinde belirtildiğinde yok sayılır.<br/>-Sabit karakter olarak özel bir karakter belirtmenin diğer bir yolu da bir ters eğik çizgi (yani, `\`) kullanarak kaçış yöntemidir.

### <a name="regular-expressions"></a>Normal ifadeler

Normal ifadeler, metin değeri içinde aranan bir model tanımlar. Normal ifade gösterimi çeşitli simgelere özgü anlamları tanımlar. Aşağıdaki tabloda, özel karakterlerin, normal ifadeleri destekleyen koşullar ve özellikler ile nasıl işlendiği gösterilmektedir.

Özel karakter | Açıklama
------------------|------------
\ | Ters eğik çizgi, bu karakterin normal ifade anlamı yerine değişmez değer olarak işlenmesine neden olan karakteri izler. Örneğin, aşağıdaki sözdizimi bir yıldız işaretine çıkar:`\*`
% | Bir yüzde sembolünün anlamı, kullanımına bağlıdır.<br/><br/> `%{HTTPVariable}`: Bu söz dizimi bir HTTP değişkenini tanımlar.<br/>`%{HTTPVariable%Pattern}`: Bu sözdizimi bir HTTP değişkenini ve bir sınırlayıcı olarak tanımlamak için bir yüzde simgesi kullanır.<br />`\%`: Bir yüzde sembolünü kaçış, bir sabit değer olarak veya URL kodlamasının (örneğin, `\%20`) kullanılmasına izin verir.
\* | Bir yıldız işareti, önceki karakterin sıfır veya daha fazla kez eşleştirilmesini sağlar.
Uzay | Boşluk karakteri genellikle sabit karakter olarak değerlendirilir.
deeri | Tek tırnak, sabit karakter olarak değerlendirilir. Tek tırnak kümesinin özel anlamı yoktur.

Normal ifadeleri destekleyen koşulların ve özelliklerin eşleşmesi, Perl uyumlu normal Ifadeler (PCRE) tarafından tanımlanan desenleri kabul eder.

## <a name="next-steps"></a>Sonraki adımlar

- [Kural altyapısı eşleştirme koşulları](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Kural altyapısı koşullu ifadeleri](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Kural altyapısı özellikleri](cdn-verizon-premium-rules-engine-reference-features.md)
- [Kural altyapısını kullanarak HTTP davranışını geçersiz kılma](cdn-verizon-premium-rules-engine.md)
- [Azure CDN genel bakış](cdn-overview.md)
