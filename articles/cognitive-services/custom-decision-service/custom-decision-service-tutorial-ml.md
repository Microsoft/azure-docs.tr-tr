---
title: 'Öğretici: Korleştirme ve özellik belirtimi-Özel Karar Alma Hizmeti'
titlesuffix: Azure Cognitive Services
description: Özel Karar Alma Hizmeti’nde makine öğrenimi özellik kazandırma ve özellik belirtimine yönelik bir öğretici.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 409dcf7dc224eaf2a3f51325010507b9182fadf2
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707052"
---
# <a name="tutorial-featurization-and-feature-specification"></a>Öğretici: Korleştirme ve özellik belirtimi

Bu öğreticide, Özel Karar Alma Hizmeti’ndeki gelişmiş makine öğrenimi özelliği ele alınmaktadır. Öğretici iki bölümden oluşur: [özellik kazandırma](#featurization-concepts-and-implementation) ve [özellik belirtimi](#feature-specification-format-and-apis). Özellik kazandırma, verilerinizin makine öğrenimi için “özellikler” olarak temsil edilmesi anlamına gelir. Özellik belirtimi, özellikleri belirtmek için JSON biçimini ve yardımcı API’leri kapsar.

Özel Karar Alma Hizmeti’nde makine öğrenimi varsayılan olarak müşterilere açıktır. Özellikler içeriğinizden otomatik olarak ayıklanır ve pekiştirmeye dayalı standart öğrenme kullanılır. Özellik ayıklama diğer birçok Azure bilişsel hizmetten yararlanır: [Varlık bağlama](../entitylinking/home.md), [metin analizi](../text-analytics/overview.md), [duygu](../emotion/home.md)ve [görüntü işleme](../computer-vision/home.md). Bu öğretici yalnızca varsayılan özellik kullanılıyorsa atlanabilir.

## <a name="featurization-concepts-and-implementation"></a>Özellik kazandırma: kavramlar ve uygulama

Özel Karar Alma Hizmeti kararları tek tek alır. Her kararda çeşitli alternatifler (eylemler) arasından seçim yapılır. Uygulamaya bağlı olarak, karar tek bir eylemi veya eylemlerin sıralı bir listesini (kısa) seçebilir.

Örneğin, bir web sitesinin ön sayfasındaki makale seçkisini kişiselleştirmek gibi. Burada, eylemler makalelerle ilgilidir ve her karar hangi makalelerin belirli bir kullanıcıya gösterileceğini belirtir.

Her eylem bir özellikler vektörüyle temsil edilir ve bundan sonra onlara *özellikler* denecektir. Otomatik olarak ayıkladığınız özelliklere ek olarak yeni özellikler belirtebilirsiniz. Ayrıca, Özel Karar Alma Hizmeti’nin bazı özellikleri günlüğe kaydetmesini, ancak makine öğrenimi için bunları yok saymasını sağlayabilirsiniz.

### <a name="native-vs-internal-features"></a>Yerel ve iç özellikler

Özellikleri uygulamanız için en doğal olan biçimde (sayı, dize veya dizi) belirtebilirsiniz. Bu özelliklere “yerel özellikler” denir. Özel Karar Alma Hizmeti, her yerel özelliği “iç özellikler” denen bir veya daha fazla sayısal özelliğe dönüştürür.

İç özelliklere dönüştürme aşağıdaki gibidir:

- sayısal özellikleri aynı kalır.
- bir sayısal dizi, dizinin her öğesi için bir adet olmak üzere birkaç sayısal özelliğe dönüşür.
- dize değerli bir özellik `"Name":"Value"` varsayılan olarak `"NameValue"` ve değer 1 adlı bir özelliğe dönüştürülür.
- İsteğe bağlı olarak, bir dize [sözcük çantası](https://en.wikipedia.org/wiki/Bag-of-words_model) olarak temsil edilebilir. Daha sonra dizedeki her sözcük için değeri bu sözcükteki tekrar sayısı olan bir iç özellik oluşturulur.
- Sıfır değerli iç özellikler atlanır.

### <a name="shared-vs-action-dependent-features"></a>Paylaşılan ve eylem bağımlı özellikler

Bazı özellikler kararın tamamına başvurur ve tüm eylemler için aynıdır. Bunlara *paylaşılan özellikler* denir. Bazı diğer özellikler belirli bir eyleme özeldir. Bunlara *eylem bağımlı özellikler* (ADF’ler) denir.

Örnekte, paylaşılan özellikler kullanıcıyı ve/veya dünyanın durumunu açıklayabilir. Coğrafi konum, kullanıcının yaşı, cinsiyeti ve şu anda gerçekleşen önemli olaylar gibi özellikler. Eylem bağımlı özellikler belirli bir makalenin özelliklerini açıklayabilir (bu makalenin kapsamındaki konu başlıkları gibi).

### <a name="interacting-features"></a>Etkileşim kuran özellikler

Özellikler çoğu zaman “etkileşim kurar”: birinin etkisi diğerlerine bağlıdır. Örneğin, X özelliği kullanıcının sporla ilgili olup olmadığını belirtsin. Y özelliği belirli bir makalenin sporlar hakkında olduğunu belirtsin. Bu durumda Y özelliğinin etkisi X özelliğine yüksek derecede bağımlıdır.

X ve Y özellikleri arasındaki etkileşimi açıklamak için değeri X\*Y olan bir *ikinci derece* oluşturun. (X ve Y “kesiştirme” de deriz.) Hangi özellik çiftlerinin kesiştiğini seçebilirsiniz.

> [!TIP]
> Paylaşılan bir özellik, sıralarını etkilemek için eylem bağımlı özelliklerle kesişmelidir. Eylem bağımlı bir özellik, kişiselleştirmeye katkıda bulunmak için paylaşılan özelliklerle kesişmelidir.

Diğer bir deyişle, hiçbir ADF ile kesişmeyen paylaşılan bir özellik her eylemi aynı şekilde etkiler. Hiçbir paylaşılan özellikle kesişmeyen bir ADF de her kararı etkiler. Bu özellik tipleri ödül tahminlerinin farkını azaltabilir.

### <a name="implementation-via-namespaces"></a>Ad alanları aracılığıyla uygulama

Kesişen özellikleri (ve diğer özellik kazandırma kavramlarını) Portaldaki “VW komut satırı” aracılığıyla uygulayabilirsiniz. Sözdizimi [Vowpal Wabbit](http://hunch.net/~vw/) komut satırını temel alır.

Uygulamanın merkezinde *ad alanı* kavramı vardır: özelliklerin adlandırılmış bir alt kümesi. Her özellik tam olarak bir ad alanına aittir. Ad alanı, özellik değeri Özel Karar Alma Hizmeti’ne açıkça sağlanırken belirtilebilir. VW komutunda bir özelliğe başvurmanın tek yolu budur.

Bir ad alanı “paylaşılır” veya “eylem bağımlıdır”: yalnızca paylaşılan özelliklerden oluşur veya aynı eylemin eylem bağımlı özelliklerinden oluşur.

> [!TIP]
> Özelliklerin belirtilen ad alanlarında açıkça sarmalanması iyi bir uygulamadır. İlgili özellikleri aynı ad alanında gruplayın.

Ad alanı sağlanmazsa, özellik otomatik olarak varsayılan ad alanına atanır.

> [!IMPORTANT]
> Özelliklerin ve ad alanlarının eylemler genelinde tutarlı olması gerekmez. Özellikle, bir ad alanının farklı eylemler için farklı özellikleri olabilir. Ayrıca, belirli bir ad alanı bazı eylemler için tanımlanabilir ve bazıları için tanımlanamayabilir.

Aynı dize değerli yerel özellikten gelen birden çok iç özellik, aynı ad alanında gruplandırılır. Farklı ad alanlarında yer alan iki yerel özellik, aynı özellik adına sahip olsalar bile ayrı olarak ele alınır.

> [!IMPORTANT]
> Uzun ve açıklayıcı ad alanı kimlikleri yaygın olsa da, VW komut satırı kimlikleri aynı harfle başlayan ad alanları arasında ayrım yapmaz. Ad alanı kimlikleri tek harftir, örneğin `x` ve `y`.

Uygulama detayları aşağıdaki gibidir:

- `x` ve `y` ad alanlarını kesiştirmek için `-q xy` veya `--quadratic xy` yazın. Sonra `x` içindeki her özellik `y` içindeki her özellikle kesiştirilir. `x` öğesini her bir ad alanıyla kesiştirmek için `-q x:` ve tüm ad alanı çiftlerini kesiştirmek için `-q ::` kullanın.

- `x` ad alanındaki tüm özellikleri yok saymak için `--ignore x` yazın.

Bu komutlar, ad alanları her tanımlandığında her eyleme ayrı olarak uygulanır.

### <a name="estimated-average-as-a-feature"></a>Özellik olarak tahmini ortalama

Düşünce deneyi olarak, belirli bir eylem tüm kararlar için seçilseydi bunun için ortalama ödül ne olurdu? Bu ortalama ödül, bu eylemin “genel kalitesi” için bir ölçü olarak kullanılabilirdi. Bazı kararlarda eylemlerin diğerleri yerine ne zaman tercih edildiği tam olarak bilinmez. Ancak, pekiştirmeye dayalı teknikler kullanılarak tahmin edilebilir. Bu tahminin kalitesi genellikle zaman içinde artar.

Bu “tahmini ortalama ödülü” belirli bir eylem için bir özellik olarak eklemeyi seçebilirsiniz. Özel Karar Alma Hizmeti yeni veriler geldiğinde bu tahmini otomatik olarak günceller. Bu özellik, eylemin *marjinal özelliği* olarak bilinir. Marjinal özellikler makine öğrenimi ve denetim için kullanılabilir.

Marjinal özellikleri eklemek için VW komut satırına `--marginal <namespace>` yazın. `<namespace>` öğesini JSON dilinde aşağıdaki gibi tanımlayın:

```json
{<namespace>: {"mf_name":1 "action_id":1}
```

Bu ad alanını belirli bir eyleme yönelik eylem bağımlı özelliklerle birlikte ekleyin. Tüm kararlarda aynı `mf_name` ve `action_id` öğesini kullanarak her karar için bu tanımı sağlayın.

Marjinal özellik her eylem için `<namespace>` ile eklenir. `action_id`, eylemi benzersiz olarak tanımlayan herhangi bir özellik adı olabilir. Özellik adı `mf_name` olarak ayarlanır. Özellikle, farklı `mf_name` öğesine sahip marjinal özellikler farklı özellikler olarak ele alınır (her `mf_name` için farklı bir ağırlık öğrenilir).

Varsayılan kullanımda `mf_name` tüm eylemler için aynıdır. Daha sonra tüm marjinal özellikler için bir ağırlık öğrenilir.

Aynı eylem için aynı değerlere ancak farklı özellik adlarına sahip birden çok marjinal özellik de belirtebilirsiniz.

```json
{<namespace>: {"mf_name1":1 "action_id":1 "mf_name2":1 "action_id":1}}
```

### <a name="1-hot-encoding"></a>1-hot kodlama

Bazı özellikleri, her bitin olası değerler aralığına karşılık geldiği bit vektörleri olarak göstermeyi seçebilirsiniz. Bu bit yalnızca özelliğin bu aralıkta olması durumunda 1 olarak ayarlanır. Bu nedenle, 1 olarak ayarlanan bir “hot” bit vardır ve diğerleri 0 olarak ayarlanır. Bu gösterim yaygın olarak *1 hot kodlama* olarak bilinir.

1 hot kodlama, “coğrafi bölge” gibi doğası gereği anlamlı sayısal gösterimi olmayan kategorik özellikler için normaldir. Ayrıca, ödüldeki etkisi muhtemelen doğrusal olmayan sayısal özellikler için de önerilir. Örneğin, belirli bir makale belirli bir yaş grubuyla ilgili ve daha yaşlı veya daha genç kişilerle alakasız olabilir.

Tüm dize değerli özellikler varsayılan olarak 1-hot kodludur: her olası değer için ayrı bir iç özellik oluşturulur. Sayısal özellikler için ve/veya özelleştirilmiş aralıklarla otomatik 1-hot kodlama şu anda sağlanmamaktadır.

> [!TIP]
> Makine öğrenimi algoritmaları, belirli bir iç özelliğin tüm olası değerlerini tek bir yolla ele alır: ortak bir “ağırlıkla”. 1-hot kodlama, her değer aralığı için ayrı bir “ağırlığa” izin verir. Aralıkların küçültülmesi, yeterli veri toplandıktan sonra daha iyi ödüllere neden olur, ancak daha iyi ödüller için gerekli veri tutarını artırabilir.

## <a name="feature-specification-format-and-apis"></a>Özellik belirtimi: biçim ve API’ler

Özellikleri birkaç yardımcı API aracılığıyla belirtebilirsiniz. Tüm API’ler ortak bir JSON biçimi kullanır. Aşağıda, API’ler ve biçim kavramsal bir düzeyde sağlanmıştır. Belirtim bir Swagger şeması ile desteklenir.

Özellik belirtimi için temel JSON şablonu aşağıdaki gibidir:

```json
{
"<name>":<value>, "<name>":<value>, ... ,
"namespace1": {"<name>":<value>, ... },
"namespace2": {"<name>":<value>, ... },
...
}
```

Burada `<name>` ve `<value>` sırasıyla özellik adı ve özellik değeri içindir. `<value>` bir dize, tamsayı, kayan değer, boole veya dizi olabilir. Ad alanında paketlenmeyen bir özellik otomatik olarak varsayılan ad alanına atanır.

Bir dizeyi sözcük çantası olarak göstermek için `"<name>":<value>` yerine `"_text":"string"` özel sözdizimini kullanın. Sonuç olarak, dizedeki her değer için ayrı bir iç özellik oluşturulur. Özelliğin değeri bu sözcüğün oluşum sayısıdır.

`<name>` "_" ile başlarsa (ve `"_text"` değilse) özellik yok sayılır.

> [!TIP]
> Bazen birden çok JSON kaynağındaki özellikleri birleştirirsiniz. Kolaylık olması için bunları aşağıdaki şekilde gösterebilirsiniz:
>
> ```json
> {
> "source1":<features>,
> "source2":<features>,
> ...
> }
> ```

Burada `<features>`, daha önce tanımlanan temel özellik belirtimini ifade eder. “İç içe yerleştirmenin” daha derin düzeylerine de izin verilir. Özel Karar Alma Hizmeti `<features>` olarak yorumlanabilecek “en derin” JSON nesnelerini otomatik olarak bulur.

#### <a name="feature-set-api"></a>Özellik Kümesi API’si

Özellik Kümesi API’si, daha önce açıklanan JSON formatında bir özellik listesi döndürür. Birden çok Özellik Kümesi API’si uç noktası kullanabilirsiniz. Her uç nokta, özellik kümesi kimliği ve bir URL ile tanımlanır. Özellik kümesi kimlikleri ve URL’ler arasındaki eşleme Portalda ayarlanır.

JSON’da uygun konuma ilgili özellik kümesi kimliğini ekleyerek Özellik Kümesi API’sini çağırın. Eylem bağımlı özellikler için çağrı, eylem kimliği tarafından otomatik olarak parametre haline getirilir. Aynı eylem için birden çok özellik kümesi kimliği belirtebilirsiniz.

#### <a name="action-set-api-json-version"></a>Eylem Kümesi API’si (JSON sürümü)

Eylem Kümesi API’si, eylemlerin ve özelliklerin JSON dilinde belirtildiği bir sürüme sahiptir. Özellikler açıkça ve/veya Özellik Kümesi API’leri kullanılarak belirtilebilir. Paylaşılan özellikler tüm eylemler için bir kez belirtilebilir.

#### <a name="ranking-api-http-post-call"></a>Sıralama API’si (HTTPS POST çağrısı)

Sıralama API’si, HTTP POST çağrısı kullanan bir sürüme sahiptir. Bu çağrının gövdesi, eylemleri ve özellikleri esnek bir JSON sözdizimi kullanarak belirtir.

Eylemler açıkça ve/veya eylem kümesi kimlikleri kullanılarak belirtilebilir. Bir eylem kümesi kimliğiyle her karşılaşıldığında, ilgili Eylem Kümesi API’si uç noktasına bir çağrı yapılır.

Eylem Kümesi API’si için özellikler açıkça veya Özellik Kümesi API’leri kullanılarak belirtilebilir. Paylaşılan özellikler tüm eylemler için bir kez belirtilebilir.
