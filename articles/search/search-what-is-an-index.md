---
title: Dizin tanımı ve kavramlar oluşturma
titleSuffix: Azure Cognitive Search
description: Bileşen bölümleri ve fiziksel yapı dahil olmak üzere Azure Bilişsel Arama 'daki Dizin koşullarına ve kavramlarına giriş.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 30fffa6264411238c3ff0a5e829e1567c00f4f97
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794205"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>Azure Bilişsel Arama temel dizin oluşturma

Azure Bilişsel Arama, *Dizin* , Azure bilişsel arama hizmetinde filtrelenmiş ve tam metin araması için kullanılan bir *belge* ve diğer yapıların kalıcı bir deposudur. Kavramsal olarak, bir belge, dizininizdeki aranabilir verilerin tek bir birimidir. Örneğin, bir e-ticaret satıcısında sattığı her bir öğe için bir belge, bir haber kuruluşunda her bir makale için bir belge, vb. olabilir. Bu kavramları daha çok bilinen veritabanı eşdeğerlerine eşleyen bir *dizin*, kavramsal olarak bir *tabloya* benzer ve *belgeler* de bir tablodaki *satırlarla* kabaca eşdeğerdir.

Bir dizin eklediğinizde veya karşıya yüklediğinizde, Azure Bilişsel Arama sağladığınız şemaya göre fiziksel yapılar oluşturur. Örneğin, dizininizdeki bir alan aranabilir olarak işaretlenmişse, bu alan için ters bir dizin oluşturulur. Daha sonra, belgeleri eklediğinizde veya karşıya yüklediğinizde ya da Azure Bilişsel Arama arama sorguları gönderdiğinizde, istekleri arama hizmetinizde belirli bir dizine gönderiyorsunuz. Belge değerleriyle alanları yüklemeye *dizinleme* veya veri alımı denir.

Portal, [REST API](search-create-index-rest-api.md)veya [.NET SDK 'sında](search-create-index-dotnet.md)bir dizin oluşturabilirsiniz.

## <a name="recommended-workflow"></a>Önerilen iş akışı

Doğru dizin tasarımına ulaşan genellikle birden çok yineleme aracılığıyla elde edilir. Araçların ve API 'lerin bir birleşimini kullanmak, tasarımınızı hızlı bir şekilde sonuçlamanızı sağlamanıza yardımcı olabilir.

1. Bir [Dizin Oluşturucu](search-indexer-overview.md#supported-data-sources)kullanıp kullanamayacağını belirleme. Dış verileriniz desteklenen veri kaynaklarından biri ise, [**verileri Içeri aktarma**](search-import-data-portal.md) Sihirbazı 'nı kullanarak bir dizini prototip ve yükleyebilirsiniz.

2. **Içeri aktarma verilerini**kullanamıyoruz, [portalda bir başlangıç dizini oluşturabilir](search-create-index-portal.md), alanları, veri türlerini **ekleyebilir ve Dizin Ekle** sayfasında denetimleri kullanarak öznitelikleri atayabilirsiniz. Portal, farklı veri türleri için hangi özniteliklerin kullanılabildiğini gösterir. Dizin tasarımına yeni başladıysanız, bu yararlı olur.

   ![Öznitelikleri veri türüne göre gösteren Dizin sayfası ekle](media/search-create-index-portal/field-attributes.png "Öznitelikleri veri türüne göre gösteren Dizin sayfası ekle")
  
   **Oluştur**' a tıkladığınızda, dizininizi destekleyen tüm fiziksel yapılar arama hizmetinizde oluşturulur.

3. [Get ındex REST API](https://docs.microsoft.com/rest/api/searchservice/get-index) ve [Postman](search-get-started-postman.md)gibi bir Web testi aracı kullanarak Dizin şemasını indirin. Artık portalda oluşturduğunuz dizinin JSON gösterimine sahipsiniz. 

   Bu noktada kod tabanlı bir yaklaşıma geçiyorsunuz. Zaten oluşturulmuş bir dizini düzenleyemezsiniz, Portal yineleme için uygun değildir. Ancak geri kalan görevler için Postman ve REST kullanabilirsiniz.

4. [Dizininizi verilerle yükleyin](search-what-is-data-import.md). Azure Bilişsel Arama JSON belgelerini kabul eder. Verilerinizi programlı bir şekilde yüklemek için, istek yükünde JSON belgeleriyle Postman kullanabilirsiniz. Verileriniz JSON olarak kolayca ifade edideğilse, bu adım en yoğun işgücü olacaktır.

5. İstediğiniz sonuçları görene kadar dizininizin dizinini sorgulayın, sonuçları inceleyin ve dizin şemasında daha fazla yineleme yapın. Dizininizi sorgulamak için [**Arama Gezgini**](search-explorer.md) 'Ni veya Postman 'ı kullanabilirsiniz.

6. Tasarımınızı yinelemek için kodu kullanmaya devam edin.  

Fiziksel yapılar hizmette oluşturulduğundan, var olan bir alan tanımında her madde değişikliği yaptığınızda [dizinleri bırakıp yeniden oluşturmak](search-howto-reindex.md) gereklidir. Bu, geliştirme sırasında sık sık yeniden oluşturmanız planlanmalıdır. Yeniden oluşturma işlemini daha hızlı hale getirmek için verilerinizin bir alt kümesiyle çalışmayı düşünebilirsiniz. 

Bir portal yaklaşımı yerine kod, yinelemeli tasarım için önerilir. Dizin tanımı için portala sahipseniz, her yeniden oluşturma sırasında dizin tanımını doldurmanız gerekir. Alternatif olarak, [Postman ve REST API](search-get-started-postman.md) gibi araçlar, geliştirme projeleri hala erken aşamalarındayken kavram kanıtı testi için yararlıdır. Bir istek gövdesinde Dizin tanımında artımlı değişiklikler yapabilir ve sonra güncelleştirilmiş bir şemayı kullanarak bir dizini yeniden oluşturmak için isteği hizmetinize gönderebilirsiniz.

## <a name="components-of-an-index"></a>Bir dizinin bileşenleri

Şemaya göre, Azure Bilişsel Arama dizini aşağıdaki öğelerden oluşur. 

[*Alanlar koleksiyonu*](#fields-collection) genellikle bir dizinin en büyük bölümüdür; burada her bir alan adlandırılmış, yazılır ve nasıl kullanılacağını belirleyecek izin verilen davranışlar ile adlandırılır. Diğer öğeler [Öneri araçları](#suggesters), [Puanlama profillerini](#scoring-profiles), özelleştirme, [CORS](#cors) ve [şifreleme anahtarı](#encryption-key) seçeneklerini destekleyen bileşen bölümlerine sahip [Çözümleyicileri](#analyzers) içerir.

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Alanlar koleksiyonu ve alan öznitelikleri

Şemanızı tanımlarken, dizininizdeki her bir alan için ad, tür ve öznitelikler belirtmeniz gerekir. Alan türü, bu alanda depolanan verileri sınıflandırır. Öznitelikler, alanın nasıl kullanıldığını belirtmek için tek tek alanlarda ayarlanır. Aşağıdaki tablolar belirtebileceğiniz türleri ve öznitelikleri numaralandırır.

### <a name="data-types"></a>Veri türleri
| Tür | Açıklama |
| --- | --- |
| *Edm.String* |İsteğe bağlı olarak tam metin araması için simgeleştirilmiş olabilecek metin (sözcük ayırma, sözcük kökü oluşturma vb.). |
| *Collection(Edm.String)* |Tam metin araması için isteğe bağlı olarak belirteç haline getirilebilen dize listesi. Bir koleksiyondaki öğelerin sayısında teorik bir üst sınır yoktur ancak yük boyutundaki 16 MB'lık üst sınır, koleksiyonlar için geçerlidir. |
| *Edm.Boolean* |True/false değerlerini içerir. |
| *Edm.Int32* |32 bit tamsayı değerleri. |
| *Edm.Int64* |64 bit tamsayı değerleri. |
| *Edm.Double* |Çift duyarlıklı sayısal veriler. |
| *Edm.DateTimeOffset* |OData v4 biçiminde temsil edilen tarih saat değerleri (örneğin, `yyyy-MM-ddTHH:mm:ss.fffZ` veya `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Dünya üzerindeki bir coğrafi konumu temsil eden bir nokta. |

Azure Bilişsel Arama tarafından [desteklenen veri türleri](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)hakkında daha ayrıntılı bilgi edinmek için burada bulabilirsiniz.

### <a name="index-attributes"></a>Dizin öznitelikleri

Dizininizdeki tek bir alan, her belgeyi benzersiz bir şekilde tanımlayan **anahtar** alanı olarak belirlenmiş olmalıdır.

Diğer öznitelikler, bir alanın uygulamada nasıl kullanıldığını belirleme. Örneğin, **aranabilir** özniteliği tam metin aramasına dahil edilecek her alana atanır. 

Bir dizin oluşturmak için kullandığınız API 'Ler, farklı varsayılan davranışlara sahiptir. [REST API 'leri](https://docs.microsoft.com/rest/api/searchservice/Create-Index)için çoğu öznitelik varsayılan olarak etkindir (örneğin, **aranabilir** ve **alınabilir** , dize alanları için geçerlidir) ve genellikle bunları kapatmak istiyorsanız ayarlamanız gerekir. .NET SDK için, tersi doğrudur. Açıkça ayarlamadığınız herhangi bir özellikte, özel olarak etkinleştirmediğiniz takdirde, karşılık gelen arama davranışının devre dışı bırakılması varsayılan olur.

| Öznitelik | Açıklama |
| --- | --- |
| `key` |Her bir belgenin belge araması için kullanılan benzersiz kimliğini sağlayan bir dize. Tüm dizinlerin bir anahtarı olması gerekir. Yalnızca bir alan anahtar olabilir ve bunun türü Edm.String olarak ayarlanmalıdır. |
| `retrievable` |Bir arama sonucunda bir alanın döndürülüp döndürülemeyeceğini belirtir. |
| `filterable` |Alanın filtre sorgularında kullanılmasını sağlar. |
| `Sortable` |Bu alanı kullanarak bir sorgunun arama sonuçlarını sıralamasını sağlar. |
| `facetable` |Kullanıcının bağımsız filtrelemesi için [modellenmiş bir gezinmede](search-faceted-navigation.md) bir alanın kullanılmasını sağlar. Genellikle birden çok belgeyi bir araya gruplamak için kullanabileceğiniz yinelemeli değerler içeren alanlar (örneğin, tek bir marka veya hizmet kategorisine denk gelen birden çok belge) model olarak en iyi şekilde işler. |
| `searchable` |Alanı tam metin aranabilir şeklinde işaretler. |


## <a name="storage-implications"></a>Depolama etkileri

Seçtiğiniz özniteliklerin depolama üzerinde bir etkisi vardır. Aşağıdaki ekran görüntüsünde, çeşitli öznitelik birleşimlerinden kaynaklanan dizin depolama desenleri gösterilmektedir.

Dizin, portalda dizin oluşturup sorgulayabilmeniz için [yerleşik gerçek emlak örnek](search-get-started-portal.md) veri kaynağını temel alır. Dizin şemaları gösterilmese de, dizin adına göre öznitelikleri çıkarsyükleyebilirsiniz. Örneğin, *reatastate ile aranabilir* Dizin, **aranabilir** bir özniteliğe sahiptir ve başka hiçbir şey yoktur, *reatastate-alınabilir* Dizin, **alınabilir** özniteliğe ve başka hiçbir şey ve bu şekilde devam eder.

![Öznitelik seçimine dayalı Dizin boyutu](./media/search-what-is-an-index/realestate-index-size.png "Öznitelik seçimine dayalı Dizin boyutu")

Bu dizin çeşitleri yapay olsa da, özniteliklerin depolamayı nasıl etkilediği hakkında geniş karşılaştırmalar için bunlara başvurabiliyoruz. , **Alınabilir** artış dizini boyutu ayarı yapılsın mı? Hayır. **Öneri aracı** bir dizin boyutunu artırmak için alan ekleme Evet.

Filtre ve sıralamayı destekleyen dizinler, tam metin aramasını destekleyen dizinlerden nispeten daha büyüktür. Bunun nedeni, filtre ve sıralama sorgusunun tam eşleştirmelerle, belgelerin bozulmadan depolanmasıdır. Buna karşılık, tam metin ve belirsiz aramayı destekleyen aranabilir alanlar, tüm belgelerden daha az alan tüketen simgeleştirilmiş koşullarla doldurulmuş ters dizinler kullanır.

> [!Note]
> Depolama mimarisi Azure Bilişsel Arama uygulama ayrıntısı olarak değerlendirilir ve bildirimde bulunulmadan değiştirilebilir. Geçerli davranışın gelecekte devam edeceğini garanti vermez.

## <a name="suggesters"></a>Öneri Araçları
Bir öneri aracı, aramalardaki otomatik tamamlamayı veya tür ön sorguları desteklemek için bir dizindeki hangi alanların kullanıldığını tanımlayan şemanın bir bölümüdür. Genellikle kısmi arama dizeleri, Kullanıcı bir arama sorgusu yazarken [(REST API) önerilere](https://docs.microsoft.com/rest/api/searchservice/suggestions) GÖNDERILIR ve API önerilen bir tümcecik kümesi döndürür. 

Bir öneri aracı eklenen alanlar, tür öncelikli arama terimleri oluşturmak için kullanılır. Tüm arama terimleri dizin oluşturma sırasında oluşturulur ve ayrı olarak depolanır. Öneri aracı yapısı oluşturma hakkında daha fazla bilgi için bkz. [Add öneri araçları](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Puanlama modelleri

[Puanlama profili](index-add-scoring-profiles.md) , arama sonuçlarında hangi öğelerin göründüğünü etkileyen özel Puanlama davranışlarını tanımlayan şemanın bir bölümüdür. Puanlama profilleri alan ağırlıklarının ve işlevlerden oluşur. Bunları kullanmak için sorgu dizesinde ada göre bir profil belirtirsiniz.

Varsayılan bir Puanlama profili, bir sonuç kümesindeki her öğe için bir arama puanı hesaplamak üzere arka planda çalışır. Dahili, adlandırılmamış Puanlama profilini kullanabilirsiniz. Alternatif olarak, sorgu dizesinde özel bir profil belirtilmediğinde çağrılan varsayılan olarak özel bir profil kullanmak için **defaultScoringProfile** ayarlayın.

## <a name="analyzers"></a>Çözümleyiciler

Çözümleyiciler öğesi, alan için kullanılacak dil Çözümleyicisi adını ayarlar. Kullanabileceğiniz çözümleyiciler aralığı hakkında daha fazla bilgi için bkz. [Azure bilişsel arama dizinine çözümleyiciler ekleme](search-analyzers.md). Çözümleyiciler yalnızca aranabilir alanlarla kullanılabilir. Çözümleyici bir alana atandıktan sonra, dizini yeniden oluşturmadığınız takdirde değiştirilemez.

## <a name="cors"></a>CORS

Tarayıcı tüm çapraz kaynak isteklerini önleyediğinden, istemci tarafı JavaScript varsayılan olarak herhangi bir API 'yi çağıramaz. Dizininizdeki çapraz kaynak sorgularına izin vermek için **Corsoptions** ÖZNITELIĞINI ayarlayarak CORS 'yi etkinleştirin (çıkış noktaları arası kaynak paylaşımı). Güvenlik nedenleriyle, yalnızca sorgu API 'Leri CORS 'yi destekler. 

CORS için aşağıdaki seçenekler ayarlanabilir:

+ **allowederlerin** (gerekli): Bu, dizininiz için erişim verilecek olan kaynakları içeren bir listesidir. Bu, bu kaynaklardan sunulan tüm JavaScript kodunun dizininizi sorgulayabileceği anlamına gelir (doğru api anahtarını sağladığını varsayarak). Her kaynak genellikle `protocol://<fully-qualified-domain-name>:<port>`, ancak `<port>` genellikle atlanamaz. Daha fazla ayrıntı için bkz. [çıkış noktaları arası kaynak paylaşımı (Vikipedi)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) .

  Tüm kaynaklardan erişime izin vermek istiyorsanız, `*` **allowedgıas** dizisinde tek bir öğe olarak ekleyin. *Bu, üretim arama hizmetleri için önerilen bir uygulamadır,* ancak geliştirme ve hata ayıklama için genellikle yararlıdır.

+ **Maxageınseconds** (isteğe bağlı): TARAYıCıLAR, CORS ön denetim yanıtlarını önbelleğe almak için süreyi (saniye cinsinden) belirlemede bu değeri kullanır. Bu negatif olmayan bir tamsayı olmalıdır. Bu değer arttıkça, daha iyi performans olacaktır, ancak CORS ilke değişikliklerinin etkili olması için daha uzun sürer. Ayarlanmamışsa, varsayılan 5 dakikalık bir süre kullanılacaktır.

## <a name="encryption-key"></a>Şifreleme anahtarı

Tüm Azure Bilişsel Arama dizinleri Microsoft tarafından yönetilen anahtarlar kullanılarak varsayılan olarak şifrelense de, dizinler Key Vault **müşteri tarafından yönetilen anahtarlarla** şifrelenecek şekilde yapılandırılabilir. Daha fazla bilgi için bkz. [Azure 'da şifreleme anahtarlarını yönetme bilişsel arama](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Sonraki adımlar

Dizin kompozisyonunun anlaşılmasından sonra, ilk dizininizi oluşturmak için portalda devam edebilirsiniz.

> [!div class="nextstepaction"]
> [Dizin ekleme (portal)](search-create-index-portal.md)
