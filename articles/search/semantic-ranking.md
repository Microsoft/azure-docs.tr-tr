---
title: Anlam derecelendirmesi
titleSuffix: Azure Cognitive Search
description: Anlamsal derecelendirme algoritmasının Azure Bilişsel Arama 'da nasıl çalıştığını öğrenin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: bf311eb2b2d0ff7a9c17380d2e384bc05c6f05f3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562044"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Azure Bilişsel Arama anlam derecelendirmesi

> [!IMPORTANT]
> Anlamsal arama özellikleri, önizleme REST API ve Portal aracılığıyla kullanılabilen genel önizlemededir. Önizleme özellikleri, olduğu gibi, [ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)altında sunulur ve genel kullanıma sunulmakta olan uygulamanın garantisi yoktur. Bu özellikler faturalandırılabilir. Daha fazla bilgi için bkz. [kullanılabilirlik ve fiyatlandırma](semantic-search-overview.md#availability-and-pricing).

Anlamsal sıralama, bir ilk sonuç kümesinin en üst eşleşmelerini yeniden vererek duyarlık artıran sorgu yürütme işlem hattının bir uzantısıdır. Anlam derecelendirmesi, anahtar kelimelerinde dil eşleştirmesinin aksine, sorgu koşullarının anlam anlamı yakalanarak, büyük transformatör tabanlı ağlar tarafından desteklenir. [Varsayılan benzerlik derecelendirme algoritmasındaki](index-ranking-similarity.md)aksine, semantik derecelendiricisini ilgiyi anlamak için, sözcüklerin bağlamını ve anlamını kullanır.

Anlamsal sıralama, kaynak ve zaman yoğunluğu olur. Bir sorgu işleminin beklenen gecikme süresi içinde işlemeyi tamamlayabilmeniz için, semantik derecelendirmede yapılan girişler birleştirilir ve azaltılır, böylece temeldeki özetleme ve yeniden yönlendirme adımları mümkün olduğunca hızlı bir şekilde tamamlanabilir.

## <a name="pre-processing"></a>Ön işleme

Yakınlık açısından Puanlama öncesinde, içerik anlam derecelendirmesi tarafından verimli bir şekilde işlenebilen Yönetilebilir sayıda girişe düşürülmelidir.

1. İlk olarak, içerik azaltma anahtar sözcük araması için kullanılan varsayılan [benzerlik derecelendirmesi algoritması](index-ranking-similarity.md) tarafından döndürülen ilk sonuçlar kümesiyle başlar. Herhangi bir sorgu için sonuçlar, en fazla 1.000 sınırına kadar çok sayıda belge olabilir. Çok sayıda eşleşme işlemek çok uzun sürbildiğinden, yalnızca anlam derecelendirmesinin ilk 50 ilerlemesi.

   Belge sayısı ne olursa olsun, bir veya 50 olduğunda, ilk sonuç kümesi, anlam derecelendirmesi için belge yapı ' i ilk yinelemesini belirler.

1. Ardından, Corpus 'lerde, "searchFields" içindeki her alanın içeriği ayıklanır ve uzun bir dizeye birleştirilir.

1. Dize Birleştirmeden sonra, genel uzunluğunun özetleme adımının giriş gereksinimlerini karşıladığından emin olmak için, aşırı uzun olan tüm dizeler kırpılır.

   Bu kırpma işlemi, dizeye dahil olduklarından emin olmak için "searchFields" içindeki kısa alanları ilk olarak konumlandırmak önemlidir. Metin ağır alanları olan çok büyük belgeleriniz varsa, en fazla sınır yoksayıldıktan sonra herhangi bir şey yok sayılır.

Her belge artık tek bir uzun dizeyle temsil edilir.

> [!NOTE]
> Dize, karakterlerden veya sözcüklerden değil belirteçlerden oluşur. Simgeleştirme, aranabilir alanlarda çözümleyici ataması tarafından kısmen belirlenir. NGram veya EdgeNGram gibi özelleştirilmiş çözümleyici kullanıyorsanız, bu alanı searchFields dışında bırakmak isteyebilirsiniz. Dizelerin nasıl simgelendirilebilen hakkında Öngörüler için [Test çözümleyici REST API](/rest/api/searchservice/test-analyzer)kullanarak bir çözümleyici 'nin belirteç çıkışını inceleyebilirsiniz.

## <a name="extraction"></a>Ayıklama

Dize azaltmasından sonra, sorguya göre hangi Tümcelerin ve deyimlerin belgeyi en iyi şekilde özetleyeceğini belirleyen, artık makine okuma kavrama ve dil temsili modelleriyle sınırlı girdileri geçirmek mümkündür. Bu aşama anlam derecelendirmesine ilerlemek için dizeden içerik ayıklar.

Özetleme girişleri, hazırlama aşamasındaki her belge için elde edilen uzun dizelerdir. Her bir dizeden özetleme modeli, en çok temsilci olan bir pası bulur. Bu, belge için anlamsal bir [Açıklama yazısı](semantic-how-to-query-request.md) da oluşturur. Her bir başlık düz metin sürümünde ve bir vurgulama sürümünde bulunur ve belge başına 200 sözcükten daha azdır.

"Yanıtlar" parametresini belirttiyseniz, sorgu bir soru olarak ortaya anıdıysa ve bu, soruya yanıt veren uzun dizede bir işlem bulunursa, [anlamsal bir yanıt](semantic-answers.md) da döndürülür.

## <a name="semantic-ranking"></a>Anlam derecelendirmesi

1. Açıklamalı alt yazılar, girilen sorguya göre kavramsal ve anlamsal ilgi için değerlendirilir.

   Aşağıdaki diyagramda "anlam uygunluğu" ne anlama geldiğini gösteren bir çizim sunulmaktadır. Finans, hukuk, Coğrafya veya dilbilgisi bağlamında kullanılabilecek "sermaye" terimini göz önünde bulundurun. Bir sorgu aynı vektör alanından terimler içeriyorsa (örneğin, "sermaye" ve "yatırım"), aynı küme içindeki belirteçleri de içeren bir belge, bunlardan daha yüksek puan verir.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Bağlam için vektör temsili" border="true":::

1. , @search.rerankerScore Her belgeye, açıklamalı alt yazısının anlam derecesine göre atanır.

1. Tüm belgeler puanlandıktan sonra, puanla azalan sırada listelenir ve sorgu yanıt yüküne dahil edilir. Yük, yanıtlar, düz metin ve vurgulanmış açıklamalı alt yazılar ve bir SELECT yan tümcesinde alınabilir veya belirtilen olarak işaretlediğiniz tüm alanları içerir.

## <a name="next-steps"></a>Sonraki adımlar

Anlamsal sıralama, belirli bölgelerde Standart katmanlarda sunulur. Kullanılabilirlik ve kaydolma hakkında daha fazla bilgi için bkz. [kullanılabilirlik ve fiyatlandırma](semantic-search-overview.md#availability-and-pricing). Yeni bir sorgu türü, anlamsal aramanın derecelendirme ve yanıt yapılarını mümkün bir şekilde sunar. Başlamak için [bir anlam sorgusu oluşturun](semantic-how-to-query-request.md).

Alternatif olarak, varsayılan derecelendirmeden aşağıdaki makalelere göz atın. Anlam derecelendirmesi, ilk sonuçları döndürmek için benzerlik derecelendirmesine bağlıdır. Sorgu yürütme ve derecelendirme hakkında bilinmesi, işlemin tamamının nasıl çalıştığına ilişkin ayrıntılı bilgiler sunar.

+ [Azure Bilişsel Arama 'de tam metin araması](search-lucene-query-architecture.md)
+ [Azure Bilişsel Arama benzerlik ve Puanlama](index-similarity-and-scoring.md)
+ [Azure Bilişsel Arama metin işleme için çözümleyiciler](search-analyzers.md)