---
title: Basit sorgu oluşturma-Azure Search
description: Tam metin araması, filtre arama, coğrafi arama ve çok yönlü arama için Azure Search bir dizinde basit söz dizimi temelinde sorgu çalıştırarak örnek olarak bilgi edinin.
author: HeidiSteen
manager: nitinme
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6f3f0e0b8b5098784359e7703c4a165654ff9894
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808198"
---
# <a name="create-a-simple-query-in-azure-search"></a>Azure Search basit sorgu oluşturma

Azure Search, [basit sorgu söz dizimi](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) bir dizine karşı tam metin arama sorguları yürütmek için varsayılan sorgu ayrıştırıcısını çağırır. Bu ayrıştırıcı hızlıdır ve tam metin araması, filtrelenmiş ve çok yönlü arama ve coğrafi arama gibi yaygın senaryoları işler. 

Bu makalede, basit sözdizimini göstermek için örnekleri kullanırız.

Alternatif sorgu söz dizimi, belirsiz ve joker karakter araması gibi daha karmaşık sorgu yapılarını destekleyerek, işlemek için ek süre sürebilen [tam Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)' dir. Tam sözdizimini gösteren daha fazla bilgi ve örnekler için bkz. [tam Lucene sözdizimini kullanma](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>İstekleri Postman 'da formülleştirmek

Aşağıdaki örnekler, [New York OpenData girişiminin City](https://nycopendata.socrata.com/) tarafından sağlanan bir veri kümesine dayalı olarak bulunan işleri içeren bir NYC işleri arama dizininden yararlanır. Bu verilerin geçerli veya tamamlanmış olarak kabul edilmemelidir. Dizin, Microsoft tarafından sağlanmış bir korumalı alan hizmetidir ve bu sorguları denemek için bir Azure aboneliğine gerek duymayın veya Azure Search.

Ne yapmanız gerekir Postman veya GET 'te HTTP isteği vermek için eşdeğer bir araçtır. Daha fazla bilgi için bkz. [hızlı başlangıç: Postman kullanarak Azure Search REST API araştırma](search-get-started-postman.md).

### <a name="set-the-request-header"></a>İstek üst bilgisini ayarlama

1. İstek üstbilgisinde, **Içerik türünü** `application/json`olarak ayarlayın.

2. Bir **API anahtarı**ekleyin ve bu dizeye ayarlayın: `252044BE3886FE4A8E3BAA4F595114BB`. Bu, NYC Işleri dizinini barındıran korumalı alan arama hizmeti için bir sorgu anahtarıdır.

İstek üst bilgisini belirttikten sonra, bu makaledeki tüm sorgular için onu yeniden kullanabilirsiniz ve yalnızca **Search =** dizesini takas edebilirsiniz. 

  ![Postman isteği üst bilgisi](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>İstek URL 'sini ayarla

İstek, Azure Search uç noktası ve arama dizesini içeren bir URL ile eşleştirilmiş bir GET komutu.

  ![Postman isteği üst bilgisi](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL kompozisyonu aşağıdaki öğelere sahiptir:

+ **`https://azs-playground.search.windows.net/`** , Azure Search geliştirme ekibinin tuttuğu bir korumalı alan arama hizmetidir. 
+ **`indexes/nycjobs/`** , bu hizmetin dizinler koleksiyonundaki NYC işleri dizinidir. İstekte hem hizmet adı hem de dizin gereklidir.
+ **`docs`** , tüm aranabilir içeriği içeren belge koleksiyonudur. İstek üstbilgisinde belirtilen sorgu api anahtarı yalnızca belge koleksiyonunu hedefleyen okuma işlemlerinde kullanılabilir.
+ **`api-version=2019-05-06`** , her istekte gerekli bir parametre olan api sürümünü ayarlar.
+ **`search=*`** , ilk sorgu null olduğunda ilk 50 sonucunu döndüren sorgu dizesidir (varsayılan olarak).

## <a name="send-your-first-query"></a>İlk sorgunuzu gönderin

Doğrulama adımı olarak, aşağıdaki isteği al öğesine yapıştırın ve **Gönder**' e tıklayın. Sonuçlar ayrıntılı JSON belgeleri olarak döndürülür. Tüm belgeler döndürülür, bu, tüm alanları ve tüm değerleri görmenizi sağlar.

Bu URL 'YI bir REST istemcisine doğrulama adımı olarak yapıştırın ve belge yapısını görüntüleyin.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

Sorgu dizesi **`search=*`** , null veya boş aramaya bir belirtilmemiş arama eşdeğerdir. Özellikle faydalı değildir, ancak yapabileceğiniz en basit aramadır.

İsteğe bağlı olarak, arama ölçütleriyle eşleşen belgelerin sayısını döndürmek için URL 'ye **`$count=true`** ekleyebilirsiniz. Boş bir arama dizesinde bu, dizindeki tüm belgelerdir (NYC Işleri durumunda 2800 hakkında).

## <a name="how-to-invoke-simple-query-parsing"></a>Basit sorgu ayrıştırmayı çağırma

Etkileşimli sorgular için, herhangi bir şey belirtmeniz gerekmez: Simple varsayılandır. Kodda, daha önce tam sorgu söz dizimi için **QueryType = Full** olarak çağrılırsa, varsayılan değer **QueryType = Simple**ile sıfırlanabilir.

## <a name="example-1-field-scoped-query"></a>Örnek 1: alan kapsamlı sorgu

Bu ilk örnek, ayrıştırmaya özgü değildir, ancak ilk temel sorgu kavramını tanıtmaya neden olacak: kapsama. Bu örnek, yürütmeyi ve yalnızca birkaç belirli alana yanıt olarak sorgu sağlar. Araç Postman veya arama Gezgini olduğunda okunabilir bir JSON yanıtının nasıl ayarlanacağını bilmek önemlidir. 

Breçekimi için sorgu yalnızca *business_title* alanını hedefler ve yalnızca iş başlıklarının döndürüldüğünü belirtir. Sözdizimi, sorgu yürütmeyi yalnızca business_title alanıyla kısıtlamak için **Searchfields** ' dir ve yanıta hangi alanların ekleneceğini belirlemek için öğesini **seçin** .

### <a name="partial-query-string"></a>Kısmi sorgu dizesi

```http
searchFields=business_title&$select=business_title&search=*
```

Virgülle ayrılmış bir listede birden çok alan ile aynı sorgu aşağıda verilmiştir.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Tam URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

Bu sorgu için yanıt aşağıdaki ekran görüntüsüne benzer görünmelidir.

  ![Postman örnek yanıtı](media/search-query-lucene-examples/postman-sample-results.png)

Yanıtta arama puanı olduğunu fark etmiş olabilirsiniz. Arama tam metin araması olmadığı veya hiçbir ölçüt uygulanmadığı için, tek bir derecelendirme olmadığında 1 Tekdüzen puanları oluşur. Ölçüt olmadan null arama için, satırlar rastgele sırada geri gelir. Gerçek ölçütleri dahil ettiğinizde, arama puanları anlamlı değerlere geliştikçe görüntülenir.

## <a name="example-2-look-up-by-id"></a>Örnek 2: KIMLIĞE göre ara

Bu örnek bir bit normaldir, ancak arama davranışları değerlendirilirken, belirli bir belgenin tüm içeriğini incelemek isteyebilirsiniz. bu durum, sonuçların neden dahil edildiğini veya bunun dışında bırakıldığını anlayın. Tek bir belgeyi tümüyle döndürmek için, belge KIMLIĞINI geçirmek üzere bir [arama işlemi](https://docs.microsoft.com/rest/api/searchservice/lookup-document) kullanın.

Tüm belgeler benzersiz bir tanımlayıcıya sahiptir. Bir arama sorgusunun sözdizimini denemek için ilk olarak belge kimliklerinin bir listesini döndürün, böylece kullanmak üzere bir tane bulabilirsiniz. NYC Işleri için, tanımlayıcılar `id` alanında depolanır.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

Sonraki örnek, bir önceki yanıtta ilk görüntülenen `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5" temelinde belirli bir belgeyi döndüren arama sorgusudur. Aşağıdaki sorgu, yalnızca seçili alanları değil, tüm belgeyi döndürür. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Örnek 3: sorguları filtrele

[Filtre sözdizimi](https://docs.microsoft.com/azure/search/search-query-odata-filter) , **aramayla** veya kendisiyle kullanabileceğiniz bir OData deyimidir. Arama parametresi olmayan tek başına bir filtre, filtre ifadesi ilgilendiğiniz belgeleri tamamen niteleyebiliyorlarsa yararlıdır. Sorgu dizesi olmadan, sözcük temelli veya dil analizi yoktur, Puanlama yoktur (Tüm puanlar 1) ve derecelendirme yoktur. Arama dizesinin boş olduğuna dikkat edin.

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Birlikte kullanıldığında, filtre önce tüm dizine uygulanır ve sonra filtrenin sonuçlarında arama yapılır. Filtreler arama sorgusunun işlemesi gereken belge kümesini azalttığından, sorgu performansını iyileştirmeye yönelik kullanışlı bir teknik olabilir.

  ![Filtre sorgusu yanıtı](media/search-query-simple-examples/filtered-query.png)

Bunu al kullanarak Postman 'da denemek istiyorsanız, bu dizeyi yapıştırabilirsiniz:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Filtre ve arama birleştirmek için kullanabileceğiniz başka bir yöntem de, filtre içinde bir arama sorgusu kullanabileceğiniz bir filtre ifadesinde **`search.ismatch*()`** . Bu filtre ifadesi plan, Planner, planlama ve benzeri dönem dahil business_title seçmek için *planda* bir joker karakter kullanır.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

İşlev hakkında daha fazla bilgi için, ["filtre örnekleri" içinde Search. IsMatch](https://docs.microsoft.com/azure/search/search-query-odata-full-text-search-functions#examples)bölümüne bakın.

## <a name="example-4-range-filters"></a>Örnek 4: Aralık filtreleri

Aralık filtreleme, herhangi bir veri türü için **`$filter`** ifadeler aracılığıyla desteklenir. Aşağıdaki örneklerde sayısal ve dize alanları üzerinde arama yapılır. 

Veri türleri, Aralık filtrelerinde önemlidir ve sayısal veriler sayısal alanlarda ve dize alanlarında dize verilerinde en iyi şekilde çalışır. Sayısal dizeler Azure Search karşılaştırılabilir olmadığından, dize alanlarındaki sayısal veriler aralıklar için uygun değildir. 

Aşağıdaki örnekler, okunabilirlik için GÖNDERI biçimindedir (sayısal Aralık ve sonra metin aralığı):

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Sayısal aralıklar için Aralık filtresi](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Metin aralıkları için Aralık filtresi](media/search-query-simple-examples/rangefiltertext.png)

Ayrıca şunları al kullanarak Postman 'da da deneyebilirsiniz:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Değer aralıkları üzerinde her zaman, ortak bir arama uygulaması gereksinimidir. Model gezinti yapıları için filtre oluşturma hakkında daha fazla bilgi ve örnek için, bkz. ["bir aralığa göre filtrele", çok *yönlü gezintiyi uygulama*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Örnek 5: coğrafi arama

Örnek dizin, enlem ve boylam koordinatları içeren bir geo_location alanı içerir. Bu örnek, bir başlangıç noktasının çevresi içindeki belgelerde, sağladığınız rastgele bir uzaklığa (kilometre cinsinden) göre filtreleyen [coğrafi. Distance işlevini](https://docs.microsoft.com/azure/search/search-query-odata-geo-spatial-functions#examples) kullanır. Sorgunun yüzey alanını azaltmak veya büyütmek için sorgudaki son değeri ayarlayabilirsiniz (4).

Aşağıdaki örnek okunabilirlik için GÖNDERI biçimindedir:

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Daha okunabilir sonuçlar için, arama sonuçları bir iş KIMLIĞI, iş unvanı ve iş konumu içerecek şekilde kırpılır. Başlangıç koordinatları dizindeki rastgele bir belgeden (Bu durumda, Staon Adası üzerinde bir iş konumu için) alındı.

Bunu, GET ile Postman 'da da deneyebilirsiniz:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Örnek 6: arama hassasiyeti

Terim sorguları, bağımsız olarak değerlendirilen ve bağımsız olarak değerlendirilen tek koşullardır. Tümcecik sorguları, tırnak işaretleri içine alınır ve tam bir dize olarak değerlendirilir. Eşleşmenin hassasiyeti, işleçler ve searchMode tarafından denetlenir.

Örnek 1: **`&search=fire`** , tüm eşleşmelerin belgede bir yerde harekete geçen kelimeyi içerdiği 150 sonucunu döndürür.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

Örnek 2: **`&search=fire department`** 2002 sonucunu döndürür. Eşleşmeler, yangın veya departmanın bulunduğu belgeler için döndürülür.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

Örnek 3: **`&search="fire department"`** 82 sonucunu döndürür. Dizeyi tırnak işaretleriyle çevrelemek, her iki terim üzerinde de tam bir aramadır ve eşleşmeler, Birleşik terimlerin bulunduğu dizindeki simgeleştirilmiş koşullarda bulunur. Bu, **`search=+fire +department`** gibi bir aramanın neden eşit olmadığı açıklanmaktadır. Her iki terim de gereklidir, ancak bağımsız olarak taranarak yapılır. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Örnek 7: searchMode ile Boolean

Basit sözdizimi, karakter biçimindeki Boole işleçlerini destekler (`+, -, |`). SearchMode parametresi duyarlık ve geri çekme arasındaki avantajları bildirir; `searchMode=any` favoring geri çağırma (herhangi bir ölçütle eşleşen bir belgeyi sonuç kümesi için nitelendirir) ve `searchMode=all` favoring hassasiyeti (tüm kriterlerin eşleşmesi gerekir). Varsayılan değer, birden çok işleçle bir sorguyu yığınlama ve daha da dar sonuçlar yerine daha geniş bir değer elde ediyorsanız kafa karıştırıcı olabilecek `searchMode=any`. Bu durum özellikle doğru DEĞILDIR; burada sonuçlar, belirli bir terimi "içermeyen" tüm belgeleri içerir.

Varsayılan searchMode (any) kullanıldığında, 2800 belge döndürülür: çok parçalı "yangın departmanı" terimini ve ayrıca "MetroTech Center" terimine sahip olmayan tüm belgeleri içeren olanlar.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![Arama modu any](media/search-query-simple-examples/searchmodeany.png)

SearchMode ' ı `all` değiştirmek, ölçütlerde birikimli bir etkiye zorlar ve "Fire Departmanı" ifadesinin tamamını içeren belgelerden ve bu işlerin tümünü MetroTech Center adresinde içeren belgelerden oluşan daha küçük bir sonuç kümesi-21 belgesi döndürür.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![Arama modu tümü](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Örnek 8: sonuçları yapılandırma

Birçok parametre, arama sonuçlarında hangi alanların olduğunu, her toplu işte döndürülen belge sayısını ve sıralama düzenini denetler. Bu örnek, **$Select** ifadesini ve tam arama ölçütlerini kullanarak sonuçları, 82 eşleşme döndüren belirli alanlarla sınırlayan önceki örneklerden birkaç örnekten daha fazla örnektir 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Önceki örneğe eklenen başlığa göre sıralayabilirsiniz. Civil_service_title, dizinde *sıralanabilir* olduğundan bu sıralama işe yarar.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Sayfalama sonuçları, **$top** parametresi kullanılarak uygulanır, bu örnekte ilk 5 belge döndürülüyor:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Sonraki 5 ' i almak için ilk toplu işi atlayın:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Sonraki adımlar
Kodunuzda sorgular belirtmeyi deneyin. Aşağıdaki bağlantılarda, varsayılan basit sözdizimi kullanılarak hem .NET hem de REST API için arama sorgularının nasıl ayarlanacağı açıklanmaktadır.

* [.NET SDK kullanarak Azure Search dizininizi sorgulama](search-query-dotnet.md)
* [Azure Search dizininizi REST API kullanarak sorgulayın](search-create-index-rest-api.md)

Ek sözdizimi başvurusu, sorgu mimarisi ve örnekler aşağıdaki bağlantılarda bulunabilir:

+ [Lucene sözdizimi sorgu gelişmiş sorgular oluşturmaya yönelik örnekler](search-query-lucene-examples.md)
+ [Tam metin aramasının Azure Search nasıl çalıştığı](search-lucene-query-architecture.md)
+ [Basit sorgu söz dizimi](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Tam Lucene sorgusu](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Filter ve OrderBy sözdizimi](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
