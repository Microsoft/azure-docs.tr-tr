---
title: İkiz grafiğini sorgulama
titleSuffix: Azure Digital Twins
description: Bilgi için bkz. Azure dijital TWINS ikizi grafiğini sorgulama.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 6d15e2b8bfcddfd1f554ab2a27083fe5256e9e2b
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226337"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Azure Digital TWINS ikizi grafiğini sorgulama

Bu makalede, **Azure Digital TWINS sorgu dili** hakkında bilgi için [ikizi grafiğinizde](concepts-twins-graph.md) sorgulama yapmak üzere sorgu örnekleri ve daha ayrıntılı yönergeler sunulmaktadır. (Sorgu diline giriş ve özelliklerinin tam listesi için bkz. [*Kavramlar: sorgu dili*](concepts-query-language.md).)

Bu makale, dijital TWINS için sorgu dili yapısını ve ortak sorgu işlemlerini gösteren örnek sorgularla başlar. Daha sonra, Azure Digital TWINS [sorgu API](/rest/api/digital-twins/dataplane/query) 'sini veya bir [SDK 'yı](how-to-use-apis-sdks.md#overview-data-plane-apis)kullanarak, yazdıktan sonra sorgularınızı nasıl çalıştıracağınızı açıklar.

> [!NOTE]
> Örnek sorguları bir API veya SDK çağrısıyla birlikte çalıştırıyorsanız, sorgu metnini tek bir satıra sıkıştırmak gerekir.

## <a name="show-all-digital-twins"></a>Tüm dijital TWINS 'i göster

Örnekteki tüm dijital TWINS listesini döndürecek temel sorgu aşağıda verilmiştir:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>Özelliğe göre sorgu

**Özelliklere** göre dijital TWINS al (kimlik ve meta veriler dahil):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty1":::

Yukarıdaki sorguda gösterildiği gibi, dijital bir ikizi KIMLIĞI, meta veri alanı kullanılarak sorgulanır `$dtId` .

>[!TIP]
> İle başlayan meta veri alanlarıyla bir sorgu çalıştırmak için Cloud Shell kullanıyorsanız, `$` `$` bir değişken olmadığını ve sorgu metninde bir sabit değer olarak kullanılması gerektiğini bildirmek Cloud Shell için geri alma ile kaçış yapmalısınız.

Ayrıca, **belirli bir özelliğin tanımlanıp tanımlanmadığına** göre TWINS de alabilirsiniz. Tanımlı bir *konum* özelliği olan TWINS 'i alan bir sorgu aşağıda verilmiştir:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty2":::

Bu, [dijital TWINS 'e etiket ekleme](how-to-use-tags.md)başlığı altında açıklandığı gibi, kendi *etiket* özelliklerine göre TWINS 'leri almanıza yardımcı olabilir. *Kırmızı* ile etiketlenmiş tüm TWINS 'leri alan bir sorgu aşağıda verilmiştir:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Ayrıca, **bir özelliğin türüne** göre TWINS de edinebilirsiniz. *Sıcaklık* özelliği bir sayı olan TWINS 'i alan bir sorgu aşağıda verilmiştir:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty3":::

>[!TIP]
> Bir özellik türünde ise `Map` , eşleme anahtarlarını ve değerlerini doğrudan sorguda, şöyle kullanabilirsiniz:
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty4":::

## <a name="query-by-model"></a>Modele göre sorgu

`IS_OF_MODEL`İşleci, ikizi 'in [**modeline**](concepts-models.md)göre filtrelemek için kullanılabilir.

[Devralma](concepts-models.md#model-inheritance) ve model [sürüm oluşturmayı](how-to-manage-model.md#update-models)düşünür ve ikizi şu koşullardan birini karşılıyorsa, belirli bir ikizi için **true** olarak değerlendirilir:

* İkizi, için belirtilen modeli doğrudan uygular `IS_OF_MODEL()` ve ikizi üzerindeki modelin sürüm numarası, belirtilen modelin sürüm numarasından *büyük veya bu değere eşit*
* İkizi, için belirtilen modeli *genişleten* bir model uygular `IS_OF_MODEL()` ve ikizi 'ın genişletilmiş modeli sürüm numarası, belirtilen modelin sürüm numarasından *büyük veya bu değere eşittir*

Örneğin, modelin TWINS 'i için sorgulama yaparsanız `dtmi:example:widget;4` , sorgu tüm TWINS 'leri **sürüm 4** ' ü veya **pencere** öğesi modeli ' ne daha büyük ' i ve ayrıca pencere öğesi ' ne ait sürüm 4 ' ü **ya** da **pencere öğesini öğeden kalıtımla** alan herhangi bir modeli temel alarak döndürür.

`IS_OF_MODEL` birkaç farklı parametre alabilir ve bu bölümün geri kalanı farklı aşırı yükleme seçeneklerine ayrılmıştır.

En basit kullanımı `IS_OF_MODEL` yalnızca bir parametre alır `twinTypeName` : `IS_OF_MODEL(twinTypeName)` .
Bu parametreye bir değer geçiren sorgu örneği aşağıda verilmiştir:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel1":::

Birden fazla olduğunda (örneğin kullanıldığında) arama yapılacak bir ikizi koleksiyonu belirtmek için `JOIN` , `twinCollection` parametresini ekleyin: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Bu parametre için bir değer ekleyen bir sorgu örneği aşağıda verilmiştir:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel2":::

Tam eşleşme yapmak için şu `exact` parametreyi ekleyin: `IS_OF_MODEL(twinTypeName, exact)` .
Bu parametre için bir değer ekleyen bir sorgu örneği aşağıda verilmiştir:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel3":::

Üç bağımsız değişkeni de bir araya geçirebilirsiniz: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Aşağıda, üç parametre için bir değer belirten bir sorgu örneği verilmiştir:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>İlişkiye göre sorgulama

Dijital TWINS ' **ilişkilerine** göre sorgulama yaparken Azure Digital TWINS sorgu dilinin özel bir sözdizimi vardır.

İlişkiler, `FROM` yan tümcesinde sorgu kapsamına çekilir. "Klasik" SQL türü dillerden farklı olarak, bu `FROM` yan tümcesindeki her bir ifade bir tablo değildir; Bunun yerine `FROM` yan tümce bir çapraz varlık ilişki geçişini ifade eder. Azure Digital TWINS, ilişkilerde çapraz geçiş yapmak için özel bir sürümünü kullanır `JOIN` .

Azure dijital TWINS [modeli](concepts-models.md) özellikleri ile, ilişkilerin, TWINS 'den bağımsız olarak mevcut olmadığını geri çekin. Bu, buradaki ilişkilerin bağımsız olarak sorgulanamadığından ve bir ikizi bağlı olması gerektiği anlamına gelir.
Bunu işlemek için anahtar sözcüğü, `RELATED` `JOIN` ikizi koleksiyonundan gelen belirli bir ilişki türünün kümesini çekmek için yan tümcesinde kullanılır. Sorgu daha sonra `WHERE` ilişki sorgusunda (TWINS ' değerlerini kullanarak) kullanılacak belirli ikizi (ler) in yan tümcesini filtrelemeniz gerekir `$dtId` .

Aşağıdaki bölümlerde bunun nasıl göründüğü hakkında örnekler verilmektedir.

### <a name="basic-relationship-query"></a>Temel ilişki sorgusu

Örnek bir ilişki tabanlı sorgu aşağıda verilmiştir. Bu kod parçacığı, ' ABC ' öğesinin *ID* özelliğine sahip tüm dijital TWINS 'leri ve bu dijital TWINS ile ilgili tüm dijital TWINS 'leri, bir *içerir* ilişki aracılığıyla seçer.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship1":::

> [!NOTE]
> Geliştiricinin `JOIN` yan tümcesindeki anahtar değeriyle ilişkilendirilmesi gerekmez `WHERE` (veya tanımıyla birlikte satır içi bir anahtar değeri belirtebilirsiniz `JOIN` ). İlişki özellikleri hedef varlığı tanımladığından bu bağıntı sistem tarafından otomatik olarak hesaplanır.

### <a name="query-by-the-source-or-target-of-a-relationship"></a>Bir ilişkinin kaynağına veya hedefine göre sorgulama

İlişki sorgusu yapısını, bir ilişkinin kaynağı veya hedefi olan dijital bir ikizi tanımlamak için kullanabilirsiniz.

Örneğin, kaynak ikizi ile başlayabilir ve ilişkilerin hedef TWINS 'sini bulmak için ilişkilerini takip edebilirsiniz. İşte ikizi *Source-ikizi* öğesinden gelen *akışlar* ilişkilerinin hedef TWINS 'sini bulan bir sorgu örneği aşağıda verilmiştir.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationshipSource":::

Ayrıca, ilişkinin hedefi ile başlayabilir ve kaynak ikizi bulmak için ilişkiyi geri izleyebilirsiniz. İşte ikizi *target-ikizi* ile bir *akışlar* ilişkisinin kaynak ikizi bulduğu bir sorgu örneği.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationshipTarget":::

### <a name="query-the-properties-of-a-relationship"></a>Bir ilişkinin özelliklerini sorgulama

Dijital ikizlerde DTDL aracılığıyla tanımlanan özelliklere benzer şekilde ilişkiler de özelliklere sahip olabilir. , **İlişkilerinin özelliklerine göre** TWINS 'i sorgulayabilirsiniz.
Azure Digital TWINS sorgu dili, yan tümce içindeki ilişkiye bir diğer ad atayarak ilişkilerin filtrelenmesini ve projeksiyonunu sağlar `JOIN` .

Örnek olarak, *Reportedcondition* özelliği olan bir *servicedBy* ilişkisini göz önünde bulundurun. Aşağıdaki sorguda, özelliğine başvurmak için bu ilişkiye ' R ' diğer adı verilir.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship2":::

Yukarıdaki örnekte, *Reportedcondition* 'ın *servicedBy* ilişkisinin kendisini ( *servicedBy* ilişkisine sahip bir Digital ikizi değil) nasıl bir özelliği olduğunu not edin.

### <a name="query-with-multiple-joins"></a>Birden çok birleştirme içeren sorgu

Tek bir sorguda en fazla beş adet `JOIN` desteklenir. Bu, aynı anda birden çok ilişki seviyelerine çapraz geçiş yapmanıza olanak sağlar. 

Birden çok ilişki düzeyinde sorgulama yapmak için tek bir deyim kullanın `FROM` `JOIN` , burada N deyimlerini kullanın, burada `JOIN` deyimler bir Previous veya deyimin sonucu üzerinde ilişki alır `FROM` `JOIN` .

Burada, Oda 1 ve 2 ' deki açık panellerde bulunan tüm hafif bultların yer aldığı çok sayıda JOIN sorgusuna bir örnek verilmiştir.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>Öğe sayısı

Bir sonuç kümesindeki öğelerin sayısını yan tümcesini kullanarak saymanız gerekir `Select COUNT` :

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount1":::

`WHERE`Belirli bir ölçütü karşılayan öğelerin sayısını saymak için bir yan tümce ekleyin. İkizi modelinin türüne göre uygulanan bir filtre ile saymaya yönelik bazı örnekler aşağıda verilmiştir (Bu söz dizimi hakkında daha fazla bilgi için, aşağıdaki [*modele göre sorgulama*](#query-by-model) bölümüne bakın):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount2":::

`COUNT`Yan tümcesiyle birlikte de kullanabilirsiniz `JOIN` . Burada Oda 1 ve 2 ' nin açık panellerinde bulunan tüm hafif bultları sayan bir sorgu verilmiştir:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>Sonuçları filtrele: en üstteki öğeleri seçin

Yan tümcesini kullanarak bir sorgudaki birkaç "üst" öğeyi seçebilirsiniz `Select TOP` .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>Sonuçları filtrele: tahminlerle birlikte dönüş kümesi belirtme

Deyimdeki projeksiyonları kullanarak `SELECT` , bir sorgunun döndüreceği sütunları seçebilirsiniz.

>[!NOTE]
>Şu anda karmaşık özellikler desteklenmez. Projeksiyon özelliklerinin geçerli olduğundan emin olmak için projeksiyonu bir denetim ile birleştirin `IS_PRIMITIVE` .

Aşağıda, TWINS ve ilişkiler döndürmek için projeksiyonu kullanan bir sorgu örneği verilmiştir. Aşağıdaki *sorgu, bir* *üretici kimliği* olan *fabrikasının* bir *Factory. Customer* ilişkisi aracılığıyla *tüketiciyle* *ilgili olduğu ve* bu ilişki *kenar* olarak sunulur. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections1":::

Ayrıca, bir ikizi özelliğini döndürmek için projeksiyonu de kullanabilirsiniz. Aşağıdaki sorgu, *Factory. Customer* ile ilgili bir ilişki aracılığıyla BIR *ABC* kimliğiyle *fabrikaya* ilişkin *tüketicilerle* ilgili *ad* özelliğini projeler.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections2":::

Ayrıca, bir ilişkinin özelliğini döndürmek için projeksiyonu de kullanabilirsiniz. Önceki örnekte olduğu gibi, aşağıdaki sorgu, Factory ile ilgili *tüketicilerinin* *Name* özelliğini, *Factory. Customer* ilişkisi aracılığıyla bir *ABC* *kimliğiyle birlikte projeler* ; Ancak artık, *Prop1* ve *Prop2* ilişkisinin iki özelliğini de döndürür. Bu, ilişki *ucunu* adlandırarak ve özelliklerini toplarken bunu yapar.  

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections3":::

Ayrıca, projeksiyonlarla sorguları basitleştirmek için takma adlar kullanabilirsiniz.

Aşağıdaki sorgu, önceki örnekle aynı işlemleri yapar, ancak özellik adlarının,,, ve olarak diğer adı `consumerName` olur `first` `second` `factoryArea` .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections4":::

Yukarıdaki gibi aynı kümeyi sorgulayan, ancak yalnızca *Consumer.Name* özelliğini projeler `consumerName` ve bir Ikizi olarak tüm *fabrikalara* bağlayan benzer bir sorgu aşağıda verilmiştir.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>IN işleci ile verimli sorgular oluşturun

Bir TWINS dizisi oluşturup işleçle sorgulama yaparak ihtiyacınız olan sorguların sayısını önemli ölçüde azaltabilirsiniz `IN` . 

Örneğin, *binaların* *katlara* ve *katlara* *Oda* içerdiği bir senaryoyu düşünün. Sık kullanılan bir bina içindeki odaları aramak için bu adımları takip etmenin bir yolu vardır.

1. İlişkiye göre binanın yüzleri bulun `contains` .

    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWithout":::

2. Odaları bulmak için, tek tek bir kat katınızı göz önünde bulundurarak ve `JOIN` her birinin odasını bulmak üzere bir sorgu çalıştırırken, binadaki katların bir koleksiyonu ile sorgulayabilirsiniz (aşağıdaki sorguda yer alarak adlandırılmış *taban* ).

    İstemci uygulaması:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    Sorguda:
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>Diğer bileşik sorgu örnekleri

Tek bir sorguda daha fazla ayrıntı dahil etmek için, birleşim işleçlerini kullanarak yukarıdaki sorgu türlerinden herhangi birini **birleştirebilirsiniz** . Aynı anda birden fazla ikizi tanımlayıcısı için sorgu oluşturan bileşik sorguların bazı ek örnekleri aşağıda verilmiştir.

* *Oda 123* ' nin sahip olduğu cihazların dışında, işleç rolüne sunan mxyonga cihazlarını döndürün
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples1":::
* KIMLIĞI *ID1* olan başka bir Ikizi ile *Contains* adlı bir ilişkiye sahip olan TWINS 'i alma
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples2":::
* Bu oda modelinin *floor11* tarafından bulunan tüm odalarına ulaşın
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>API ile sorguları çalıştırma

Bir sorgu dizesine karar verdikten sonra [**sorgu API**](/rest/api/digital-twins/dataplane/query)'sine bir çağrı yaparak onu yürütün.

API 'yi doğrudan çağırabilir veya Azure dijital TWINS için kullanılabilen [SDK 'lardan](how-to-use-apis-sdks.md#overview-data-plane-apis) birini kullanabilirsiniz.

Aşağıdaki kod parçacığı, bir istemci uygulamasından [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) çağrısını gösterir:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

Bu çağrıda kullanılan sorgu, yukarıdaki örnek [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin) nesneleriyle temsil eden dijital TWINS listesini döndürür. Her sorgu için verilerinizin dönüş türü, ifadesiyle belirttiğiniz koşullara bağlıdır `SELECT` :
* İle başlayan sorgular `SELECT * FROM ...` , dijital TWINS 'in bir listesini döndürür (nesneler olarak seri hale getirilebilir `BasicDigitalTwin` veya oluşturmuş olduğunuz diğer özel dijital ikizi türleri).
* Biçimde başlayan sorgular `SELECT <A>, <B>, <C> FROM ...` , ve anahtarlarına sahip bir sözlük döndürür `<A>` `<B>` `<C>` .
* Diğer deyim biçimleri `SELECT` özel verileri döndürmek için oluşturulabilir. Çok özelleştirilmiş sonuç kümelerini işlemek için kendi sınıflarınızı oluşturmayı düşünebilirsiniz. 

### <a name="query-with-paging"></a>Sayfalama ile sorgulama

Sorgu çağrıları sayfalama destekler. İşte `BasicDigitalTwin` hata işleme ve sayfalama içeren sorgu sonuç türü olarak kullanılan bir örnek:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>Sonraki adımlar

Bu makaledeki sorguları çalıştırmak için kullanılan sorgu API 'SI de dahil olmak üzere [Azure Digital TWINS API 'leri ve SDK 'ları](how-to-use-apis-sdks.md)hakkında daha fazla bilgi edinin.
