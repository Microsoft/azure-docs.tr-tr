---
title: Azure blok zinciri belirteçleri şablonları
description: Azure blok zinciri belirteçleri şablonları, defter tabanlı belirteçlerin oluşturulmasını ve dağıtılmasını kolaylaştıran standartlaştırılmış ve yeniden kullanılabilir şablonlardır.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: 5b8fc2bd4d2cedadb4c50f7ec11def8bea7b37c4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577287"
---
# <a name="azure-blockchain-tokens-templates"></a>Azure blok zinciri belirteçleri şablonları

[!INCLUDE [Preview note](./includes/preview.md)]

Azure blok zinciri belirteçleri şablonu, genel muhasebe tabanlı belirteçlerin oluşturulmasını ve dağıtılmasını kolaylaştıran standartlaştırılmış ve yeniden kullanılabilir bir şablondur. Şablon, [belirteç taksonomi çerçevesi (ttf)](overview.md#token-taxonomy-framework) dilbilgisini temel alarak formülden oluşur. Dilbilgisi taban belirteç türünü ve belirtecin davranış kümesini kapsar.  

Örneğin, **τϜ {d, m, b, r}** belirteç şablonu, alt, daha okunaklı, mintable, Bur ve rol desteği olan, uygun bir temel belirteci açıklar.
  
## <a name="base-token-types"></a>Taban belirteç türleri

Belirli varlığınız için genel muhasebe tabanlı belirteci tanımlayıp oluştururken, hangi temel belirtecin kullanılacağını göz önünde bulundurmanız önemlidir.

### <a name="fungible"></a>Farklı

Uygun olmayan belirteçlerin (τF), aynı sınıfta veya dizide oldukları sürece birbirleriyle değiştirilebilir değer vardır. Bir belirteç, başka bir belirteçle aynı değere sahip veya belirli bir belirteç miktarı aynı değere sahip başka bir eşit miktarla aynı değere sahip. Örneğin, bir dolar, komik bir belirteçtir. Her biri dolar faturanız iki kişi tarafından kullanılıyorsa, bu dolar faturaları hiçbir sonuç olmadan değiş tokuş edebilirler. Dolar faturaları eşittir değeri vardır. 

### <a name="non-fungible"></a>Komik olmayan

Komik olmayan belirteçler (τN), genellikle farklı değerlere sahip olan aynı türdeki diğer belirteçlerle birlikte kullanılamaz. Örneğin, bir özellik başlığı, komik olmayan bir belirteçtir. Bir grup karmadında bulunan iki farklı apartmandaki Özellik başlıkları, birimin konumu ya da birimin bulunduğu kata bağlı olarak eşit değer değildir. İki özellik başlığı belirtecinin algılanan değeri eşit değil.

### <a name="hybrid"></a>Karma

Karma belirteçler, hem uygun olmayan belirteçlerin hem de bir komik olmayan belirteçlerin bileşenlerine sahip belirteçlerdir. Karma belirteç, diğer belirteç türündeki bir sınıfa sahip olan bir taban belirteç türüdür.

#### <a name="hybrid-non-fungible-base-with-fungible-segments"></a>Komik olmayan kesimlerle karma, komik olmayan taban

Komik olmayan kesimlerdeki karma olmayan bir temel, uygun olmayan belirteç kesimlerine sahip, komik olmayan bir temel sahiptir.
Örneğin, konser bileti, konser 'ın tarih ve saatinin, komik olmayan temel belirteç olduğu karma bir belirteçtir. Verilen konser için çeşitli parçalar bölümündeki biletler, komik belirteçlere sahip segmentlerdir. Biletler, bölümler arasında değil, kendi bireysel bölümlerinin yerini alırlar.

#### <a name="hybrid-fungible-base-with-non-fungible-segments"></a>Komik olmayan kesimlerle karma değerli temel

Komik olmayan bir kesim belirtecine sahip bir karma değerli temel, değerli olmayan belirteç kesimlerine sahip komik bir temel sahiptir. Örneğin, ipotek sağlayan bir güvenlik, birçok Sahibe bölünen, çok sayıda sahibe sahip olan bir karma belirteçtir. Güvenlik, değiştirilebilir. Tek tek Mortgages, belirli ipotek düzeyli güvenliği temsil eden, komik olmayan kesimlerdir.

## <a name="token-behaviors"></a>Belirteç davranışları

Belirteç davranışı, belirtecin yeteneklerini veya kısıtlamalarını tanımlar. Davranışı, belirteç tanımının bir parçası olan destekleyici özellikleri içerir. Davranışlar, tüm belirteç türlerine veya yalnızca birine uygulanabilir. Davranışlar, davranışın etkilerinin ne olduğuna bağlı olarak iç veya dış olabilir. Bir iç davranış, belirtecin kendisindeki özellikleri sağlar veya kısıtlar. Dış bir davranış, bir dış aktörden davranışın çağrılmasını sağlar veya kısıtlar.

Azure blok zinciri belirteçleri desteklenen belirteç taksonomi çerçevesi (TTF) belirteci davranışları hakkında daha fazla bilgi için bkz. [belirteç bileşim](composability.md).

## <a name="pre-built-token-templates"></a>Önceden oluşturulmuş belirteç şablonları

Azure blok zinciri belirteçleri, değişiklik yapılmadan kullanılabilecek dört önceden oluşturulmuş belirteç şablonu sağlar. Belirteçlerinizi hızlıca oluşturmaya, dağıtmaya ve yönetmeye başlamak için kullanım örneklerinin çoğu için önceden oluşturulmuş bu şablonlara çağrı yapabilirsiniz.

### <a name="commodity-tokens"></a>Emtia belirteçleri

Emtia belirteçleri tutarlı bir değere sahiptir ve bu şekilde aktarılabilir. Örneğin, yağ veya enerji birimi gibi.

**τF {~ d, t, m, b, r}** -komik, tam, transferlenebilir, mintable, burlik, ve rol desteği var

Birçok blok zinciri senaryosu, tedarik zinciri veya birden çok kuruluş genelinde saydamlık ve görünürlük gerektirir. Emtia belirteçleri, bu genel kullanım durumlarını temel alınır. Belirteçler, değiştirilebilir ve tutarlıdır. Emtia belirteci şablonu esnektir ve meta verilerle özelleştirilebilir.

### <a name="qualified-tokens"></a>Nitelikli belirteçler

Nitelikli belirteçler, kazanılan bir şeyi temsil eder ve genellikle bir varlıkla ilişkilendirilir ve aktarılamaz. Örneğin, bir dıloma veya park ihlali.

**τN {s, ~ t}** -komik olmayan, tek ve aktarılamayan olmayan

Çeşitli denetim ve kanıtlama senaryoları, belirtecin sahipliğinin değiştirilmesini gerektirir. İlişkilendirmenin iyi veya hatalı olup olmadığını tam bir belirteç sağlaması gereken bir dizi kullanım durumu vardır.

### <a name="asset-tokens"></a>Varlık belirteçleri

Varlık belirteçlerinin öğeye bağımlı benzersiz bir değeri vardır ve commoditized değildir. Örneğin, bir Museum yapıtı veya bir özellik başlığı.

**τN {s, t}** -komik olmayan, tek ve aktarılamayan

Varlık belirteçleri, emtia belirteçleriyle karıştırılır. İki belirteç arasındaki önemli fark, varlık belirteçlerinin doğal olarak benzersiz olması ve değerin olduğu belirteç türünden bağımsız olması olabilir. Örneğin, kurulu bir sanatçının yağ boyama gibi bir resim parçası bir varlık belirtecidir. Ancak, Mona Lisa 'nın bir sanat basımı, bir emtia belirteci olarak kabul edilir. Benzer şekilde, Özellik başlığı özelliğin öznel kalitede bulunduğundan bir varlık belirtecidir.

### <a name="ticket-tokens"></a>Bilet belirteçleri

Bilet belirteçleri tutarlı bir değere sahiptir, ancak genellikle sona erer. Örneğin, bir düzlem bileti.

**τN {m, b, r}** -komik olmayan, mintable, patlama ve rol desteği var.

Bilet belirteçlerinin tipik olarak normal bir emtia belirtecinden farklı hale getiren bir sona erme tarihi vardır. Örneğin, bir uçak bileti, konser bileti veya spor bileti, belirli kullanım tarihleriyle atanmış atama seçeneklerine sahiptir. Tarihler veya alan bölümleri arasındaki anahtarları kolayca değiştiremezsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Senaryonuz için daha fazla esneklik gerekiyorsa, [belirteç bileşmesini](composability.md)kullanarak kendi belirteç şablonlarınızı oluşturma hakkında bilgi edinin.
