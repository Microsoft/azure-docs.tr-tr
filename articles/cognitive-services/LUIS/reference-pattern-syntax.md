---
title: Model sözdizimi başvurusu-LUSıS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUSıS) uygulamalarındaki Kullanıcı dıklarından anahtar verileri ayıklamak için varlıklar oluşturun. Ayıklanan veriler, istemci uygulaması tarafından kullanılır.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: c1353ae530493c34413399a7fbbfe56fb74d452f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010753"
---
# <a name="pattern-syntax"></a>Desen sözdizimi

Desen, bir şablon için bir utterance sözdizimidir. Şablon, sözcük ve sözcükler yanı sıra eşleştirmek istediğiniz varlıkları ve yok saymak için istediğiniz noktalama içermelidir. Bu **değil** normal bir ifade. 

Varlıkları desenleri süslü ayraç tarafından çevrilmiş `{}`. Desenler, varlıkları ve varlıklar rolleriyle içerebilir. [Desen. any](luis-concept-entity-types.md#patternany-entity) yalnızca desenlerinde kullanılan bir varlıktır. 

Model sözdizimi aşağıdaki sözdizimini destekler:

|İşlev|Sözdizimi|İç içe geçme düzeyi|Örnek|
|--|--|--|--|
|varlık| {} küme ayraçları|2|{Entity-Name} formu nerede?|
|seçim|[]-köşeli parantezler<BR><BR>Herhangi bir isteğe bağlı ve gruplandırma birleşiminin iç içe geçme düzeylerinde 3 sınırı vardır |2|Soru işareti isteğe bağlıdır [?]|
|Gruplama|()-parantez|2|(a \| b)|
|veya| \|-dikey çubuk (kanal)<br><br>Bir gruptaki dikey çubuklar (veya) üzerinde 2 sınırı vardır |-|WHERE formu ({form-adı-Short} &#x7c; {form-adı-Long} &#x7c; {form-numarası})| 
|söylenişi başlangıcı ve/veya bitişi|^-şapka işareti|-|^ söylenişi 'a başla<br>söylenişi tamamlandı ^<br>{Number} varlık ^ ile tüm söylenişi ile tam sabit değer eşleşmesi ^|

## <a name="nesting-syntax-in-patterns"></a>Desenlerdeki iç içe sözdizimi

Köşeli parantezler ile **isteğe bağlı** sözdizimi, iç içe iki düzey olabilir. Örneğin: `[[this]is] a new form`. Bu örnek aşağıdaki söyleylerini sağlar: 

|İç içe isteğe bağlı söylenişi örneği|Açıklama|
|--|--|
|Bu yeni bir formdur|Düzendeki tüm kelimeleri eşleştirir|
|Yeni bir form|desenli dış isteğe bağlı sözcük ve isteğe bağlı olmayan sözcüklerden eşleşir|
|Yeni bir form|yalnızca gerekli kelimeleri eşleştirir|

**Gruplama** sözdizimi, parantez ile iç içe iki düzey olabilir. Örneğin: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Bu özellik üç varlığın eşleştirilmesini sağlar. 

Entity1, Origin (Seattle) ve hedef (Cairo) gibi rollere sahip bir konum ise ve varlık 2 bir liste varlığındaki bilinen bir bina adı (Redbatı-C) ise, aşağıdaki utterlikler bu düzene eşlenir:

|İç içe gruplandırma söylenişi örneği|Açıklama|
|--|--|
|Redbatı-C|Dış gruplandırma varlığıyla eşleşir|
|Seattle|iç gruplandırma varlıklarından biriyle eşleşir|
|Cairo|iç gruplandırma varlıklarından biriyle eşleşir|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>İsteğe bağlı sözdizimi olan gruplar için iç içe sınırları

**İsteğe bağlı** sözdizimine sahip **gruplama** birleşimi 3 iç içe geçme düzeyi sınırına sahiptir.

|İzin Verildi|Örnek|
|--|--|
|Yes|([(test1 &#x7c; test2)] &#x7c; test3)|
|Hayır|([([test1] &#x7c; test2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Ya da tabanlı sözdizimi olan gruplar için iç içe sınırları

**OR-ing** sözdizimine sahip **gruplama** birleşimi 2 dikey çubuk sınırına sahiptir.

|İzin Verildi|Örnek|
|--|--|
|Yes|(test1 &#x7c; test2 &#x7c; (test3 &#x7c; test4))|
|Hayır|(test1 &#x7c; test2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Bir varlık için bir desen şablonu eklemek için söz dizimi
Surround gibi küme ayracı ile varlık adı deseni şablona bir varlık eklemek için `Who does {Employee} manage?`. 

|Varlık deseni|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Bir varlık ve rol için bir desen şablonu eklemek için söz dizimi
Bir varlık rolü olarak gösterilir `{entity:role}` ile bir iki nokta üst üste ve ardından rol adı tarafından izlenen varlık adı. Desen şablona bir role sahip bir varlık eklemek için varlık adı ve küme ayracı ile rol adı gibi çevreleyen `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Varlık rolleriyle deseni|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Bir pattern.any deseni şablonuna eklemek için söz dizimi
Pattern.any varlık varlığın değişen uzunluktaki desen eklemenizi sağlar. Düzen şablonu ve ardından sürece pattern.any herhangi bir uzunlukta olabilir. 

Eklemek için bir **Pattern.any** deseni şablon varlığa saran ve küme ayraçlarının Pattern.any varlıkla gibi `How much does {Booktitle} cost and what format is it available in?`.  

|Pattern.any varlık deseni|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Deseninde bir kitap adları|
|--|
|Ne kadardır **kitabın çalabilir** maliyet ve hangi biçimde, kullanılabilir?|
|Ne kadardır **isteyin** maliyet ve hangi biçimde, kullanılabilir?|
|Ne kadardır **gece zamanlı Mandal, merak olay** maliyet ve hangi biçimde, kullanılabilir?| 

Book 'ın, kitap başlığının, bu modele bağlı olarak, kitap başlığının nerede bittiğini öğrendiği için, kitap başlığının kelimeleri LUO 'ya kafa karıştırıcı değildir.

## <a name="explicit-lists"></a>Açık listeler

Şunu yaparken özel duruma izin vermek için yazma API 'SI aracılığıyla [açık bir liste](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) oluşturun:

* Düzeniniz bir model içeriyor [. any](luis-concept-entity-types.md#patternany-entity)
* Bu desen söz dizimi, utterance temelinde yanlış bir varlık ayıklama olasılığının kullanılmasına izin verir. 

Örneğin, hem isteğe bağlı söz dizimi içeren bir düzeni olduğunu varsayalım `[]`ve varlık sözdizimi `{}`, birleştirilmiş bir şekilde hatalı verileri ayıklamak için.

Desen '[Bul] e-postadan hakkında {subject} [{person}]'.

Aşağıdaki konuşma içinde **konu** ve **kişi** varlık doğru ve hatalı bir şekilde ayıklanır:

|Konuşma|Varlık|Doğru ayıklama|
|--|--|:--:|
|Chris köpekler hakkında e-posta|Konu köpekler =<br>kişi Chris =|✔|
|ADAM La Mancha gelen e-posta|Konu man =<br>kişi La Mancha =|X|

Yukarıdaki tabloda, konu `the man from La Mancha` (kitap başlığı) olmalıdır, ancak konu isteğe bağlı sözcük `from`içerdiğinden başlık yanlış tahmin edilir. 

Bu özel durumun desen için düzeltmek için ekleme `the man from la mancha` {subject} varlık kullanan bir açık listesi eşleşme olarak [açık bir listesi için API geliştirme](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>İsteğe bağlı bir metin şablonu utterance olarak işaretlemek için söz dizimi
İsteğe bağlı normal ifade köşeli parantez sözdizimini kullanarak utterance metni işaretleme `[]`. İsteğe bağlı bir metin, köşeli ayraç yalnızca iki ayraçlar en fazla iç içe yerleştirebilirsiniz.

|İsteğe bağlı metin deseni|Anlamı|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` ve `from {person}` isteğe bağlıdır|
|' Bana [?] yardımcı olabilir|Noktalama işareti isteğe bağlıdır|

Noktalama işaretleri (`?`, `!`, `.`) göz ardı edilmelidir ve desenlerdeki köşeli ayraç sözdizimini kullanarak bunları yok saymanız gerekir. 

## <a name="next-steps"></a>Sonraki adımlar

Desenler hakkında daha fazla bilgi edinin:

* [Desenler ekleme](luis-how-to-model-intent-pattern.md)
* [Nasıl eklenir. herhangi bir varlık](luis-how-to-add-entities.md##add-a-patternany-entity)
* [Desenler kavramları](luis-concept-patterns.md)

. JSON yanıtında [yaklaşım nasıl döndürüleceğini](luis-reference-prebuilt-sentiment.md) anlayın.