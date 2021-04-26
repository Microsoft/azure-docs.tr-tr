---
title: Puanlama profillerini kullanarak arama derecesini artırma
titleSuffix: Azure Cognitive Search
description: Puanlama profilleri ekleyerek Azure Bilişsel Arama sonuçları için arama derecelendirme puanlarını artırın.
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: afe56bb8637c9b2a88bda23944fd5097413fce97
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077729"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Azure Bilişsel Arama dizinine puanlama profilleri ekleme

*Puanlama* , sıralı bir sonuç kümesindeki her öğe için bir arama puanı hesaplar. Bir arama sonuç kümesindeki her öğeye bir arama puanı atanır ve en yüksek düzeye en düşüğe göre sıralanır.

 Azure Bilişsel Arama, bir başlangıç Puanını hesaplamak için varsayılan Puanlama kullanır, ancak hesaplamayı bir *Puanlama profili* aracılığıyla özelleştirebilirsiniz. Puanlama profilleri, arama sonuçlarında öğelerin derecelendirmesi üzerinde daha fazla denetim sağlar. Örneğin, gelir potansiyelini temel alarak öğeleri artırmak, daha yeni öğeleri yükseltmek ya da envanterdeki çok uzun olan öğeleri artırmak isteyebilirsiniz.  

 Aşağıdaki video segmenti, Puanlama profillerinin Azure Bilişsel Arama nasıl çalıştığı konusunda hızlı bir şekilde iletir.
 
> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="scoring-profile-definitions"></a>Puanlama profili tanımları

 Puanlama profili, Dizin tanımının ağırlıklı alanlar, işlevler ve parametrelerden oluşan bir parçasıdır.  

 Bir Puanlama profilinin nasıl görüneceğine ilişkin bir fikir vermek için aşağıdaki örnekte ' coğrafi ' adlı basit bir profil gösterilmektedir. Bu bir tane, **Hotelname** alanında arama terimi olan öğeleri alır. Ayrıca, `distance` geçerli konumun on kilometre içindeki öğeleri tercih etmek için işlevini kullanır. Birisi ' Inn ' terimini aradığında ve ' inn ', otel adının bir parçası olduğunda, geçerli konumun 10 KM yarıçapı içindeki ' Inn ' ile oteller içeren belgeler, arama sonuçlarında daha yüksek görünür.  


```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  


 Bu puanlama profilini kullanmak için sorgunuz Sorgu dizesinde profil belirlemek üzere formüle eklenir. Aşağıdaki sorguda, istekteki sorgu parametresine dikkat edin `scoringProfile=geo` .  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2020-06-30 
```  

 Bu sorgu, ' Inn ' terimini arar ve geçerli konumda geçirir. Bu sorgunun gibi diğer parametreleri içerdiğine dikkat edin `scoringParameter` . Sorgu parametreleri, [Azure Bilişsel Arama REST API&#41;&#40;arama belgelerinde ](/rest/api/searchservice/Search-Documents)açıklanmıştır.  

 Puanlama profilinin daha ayrıntılı bir örneğini gözden geçirmek için [örnek](#bkmk_ex) ' e tıklayın.  

## <a name="what-is-default-scoring"></a>Varsayılan skor nedir?  
 Puanlama, sıralı bir sonuç kümesindeki her öğe için bir arama puanı hesaplar. Bir arama sonuç kümesindeki her öğeye bir arama puanı atanır ve en yüksek düzeye en düşüğe göre sıralanır. Daha yüksek puanları olan öğeler uygulamaya döndürülür. Varsayılan olarak, ilk 50 döndürülür, ancak `$top` daha küçük veya daha fazla sayıda öğe (tek bir yanıtta 1000 ' e kadar) döndürmek için parametresini kullanabilirsiniz.  

Arama puanı, verilerin istatistiksel özelliklerine ve sorgusuna göre hesaplanır. Azure Bilişsel Arama, arama terimlerinin çok sayıda örneğini içeren belgeleri sorgu dizesinde (bazıları veya tümü, bağlı olarak) içeren belgeleri bulur `searchMode` . Terim veri dizini genelinde nadir ise ve belge içinde ortak olduğunda arama puanı daha da artar. Bu yaklaşıma yönelik bu yaklaşımın temelini, [tf-ıDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) veya Term Frequency-ters belge sıklığı olarak bilinir.  

 Özel sıralama olmadığı varsayılarak, sonuçlar çağıran uygulamaya döndürülmeden önce arama puanına göre sıralanır. $Top belirtilmemişse, en yüksek arama puanına sahip 50 öğe döndürülür.  

 Arama puanı değerleri, bir sonuç kümesi boyunca yinelenebilir. Örneğin, 1,2 puanı olan 10 öğe, 1,0 puanına sahip 20 öğe ve 0,5 puanı ile 20 öğe olabilir. Birden çok isabetle aynı arama puanı varsa, aynı puanlanmış öğelerin sıralaması tanımlanmamıştır ve kararlı değildir. Sorguyu yeniden çalıştırın ve öğelerin vardiya konumunu görebilirsiniz. Aynı puan ile iki öğe verildiğinde, ilk olarak bir tane görünecek garanti yoktur.  

## <a name="when-to-add-scoring-logic"></a>Puanlama mantığı ne zaman eklenir 
 Varsayılan derecelendirme davranışı iş amaçlarınızı toplantıda yeterince fazla olmadığında bir veya daha fazla Puanlama profili oluşturmanız gerekir. Örneğin, arama ilgisi yeni eklenen öğeleri tercih etmelidir. Benzer şekilde, kar marjı içeren bir alanınız ya da gelir potansiyelini belirten başka bir alan olabilir. İşletmenizin avantajlarından faydalanmaya yönelik isabet artırma, Puanlama profillerinin kullanılmasına karar verirken önemli bir faktör olabilir.  

 Relet tabanlı sıralama, Puanlama profilleri aracılığıyla da uygulanır. Geçmişte kullandığınız arama sonuçları sayfalarını fiyata, tarihe, derecelendirmeye veya ilgiye göre sıralamanıza izin veren şekilde değerlendirin. Azure Bilişsel Arama 'de, Puanlama profilleri ' ilgi ' seçeneğini sürücüdür. İlgi tanımı sizin tarafınızdan denetlenir, iş hedeflerine ve teslim etmek istediğiniz arama deneyimi türüne göre belirlenir.  

##  <a name="example"></a><a name="bkmk_ex"></a> Örneğinde  
 Daha önce belirtildiği gibi, özelleştirilmiş Puanlama bir dizin şemasında tanımlanan bir veya daha fazla Puanlama profili aracılığıyla uygulanır.  

 Bu örnekte, iki Puanlama profili (,) olan bir dizinin şeması `boostGenre` gösterilmektedir `newAndHighlyRated` . Bir sorgu parametresi olarak profil içeren bu dizine yönelik herhangi bir sorgu, sonuç kümesini Puanlama için profili kullanır.  

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="workflow"></a>İş akışı  
 Özel Puanlama davranışı uygulamak için, dizini tanımlayan şemaya bir Puanlama profili ekleyin. Bir dizin içinde en fazla 100 Puanlama profiline sahip olabilirsiniz (bkz. [hizmet limitleri](search-limits-quotas-capacity.md)), ancak belirli bir sorguda zaman içinde yalnızca bir profil belirtebilirsiniz.  

 Bu konuda belirtilen [şablonla](#bkmk_template) başlayın.  

 Bir ad sağlayın. Puanlama profilleri isteğe bağlıdır, ancak bir tane eklerseniz ad gereklidir. Alanlar için adlandırma kurallarını izlediğinizden emin olun (bir harfle başlar, özel karakterleri ve ayrılmış kelimeleri önler). Listenin tamamı için bkz. [Azure Bilişsel Arama&#41;&#40;adlandırma kuralları ](/rest/api/searchservice/naming-rules) .  

 Puanlama profili gövdesi ağırlıklı alanlar ve işlevlerden oluşturulur.  

|Özellikler |Description|  
|-|-|  
|**Çizgi**|Bir alana göreli ağırlık atayan ad-değer çiftlerini belirtin. [Örnekte](#bkmk_ex), albümbaşlık, tarz ve artistName alanları sırasıyla 1,5, 5 ve 2 ' dir. Tarzı neden diğerlerinden çok daha yüksek maliyetli? Arama, biraz hogenou olan veriler üzerinde yürütülmüştür (' de ' tarz ' ile ilgili olduğu gibi `musicstoreindex` ), göreli ağırlıklarla daha büyük bir varyans olması gerekebilir. Örneğin, `musicstoreindex` ' Rock ' içinde hem bir tarz hem de özdeş phrased tarzı açıklamalarıyla birlikte görünür. Tarz 'ın tarz açıklamasına göre daha yüksek bir değere sahip olmasını istiyorsanız, tarz alanı için çok daha yüksek bir göreli ağırlık gerekir.|  
|**İşlevler**|Belirli bağlamlar için ek hesaplamalar gerektiğinde kullanılır. Geçerli değerler `freshness`, `magnitude`, `distance` veya `tag` değerleridir. Her işlevin benzersiz parametreleri vardır.<br /><br /> -   `freshness` bir öğenin yeni mi yoksa eski mi olduğunu artırmak istediğinizde kullanılmalıdır. Bu işlev yalnızca alanlarla birlikte kullanılabilir `datetime` (EDM. Veri Timesapmayı). `boostingDuration`Özniteliğin yalnızca işleviyle kullanıldığını unutmayın `freshness` .<br />-   `magnitude` bir sayısal değerin yüksek veya düşük olduğu bir değere göre artırmak istediğinizde kullanılmalıdır. Bu işlevi çağıran senaryolar, kar marjına, en yüksek fiyata, en düşük fiyata veya bir indirme sayısına göre yükseltme içerir. Bu işlev yalnızca Double ve Integer alanları ile kullanılabilir.<br />     Bu işlev için, `magnitude` ters deseninin (örneğin, daha yüksek fiyatlı öğelerden daha fazlasını artırmak için) (örneğin, daha fazla fiyatlandırıltı olan öğelerden daha fazla) isterseniz aralığı ters çevirebilirsiniz. $100 ile $1 arasında bir dizi fiyat verildiğinde, `boostingRangeStart` `boostingRangeEnd` düşük fiyatlı öğeleri artırmak için 100 ve 1 ' de ayarlanır.<br />-   `distance` yakınlığa veya coğrafi konuma göre artırmak istediğinizde kullanılmalıdır. Bu işlev, yalnızca alanlarla birlikte kullanılabilir `Edm.GeographyPoint` .<br />-   `tag` belgeler ve arama sorguları arasında ortak olan etiketlere göre artırmak istediğinizde kullanılmalıdır. Bu işlev yalnızca ve alanları ile kullanılabilir `Edm.String` `Collection(Edm.String)` .<br /><br /> **İşlevleri kullanma kuralları**<br /><br /> İşlev türü ( `freshness` , `magnitude` , `distance` ), `tag` küçük harf olmalıdır.<br /><br /> İşlevler null veya boş değerler içeremez. Özellikle, FieldName eklerseniz, onu bir şeye ayarlamanız gerekir.<br /><br /> İşlevler yalnızca filtrelenebilir alanlara uygulanabilir. Filtrelenebilir alanlar hakkında daha fazla bilgi için bkz. [Create ındex &#40;Azure Bilişsel Arama REST API&#41;](/rest/api/searchservice/create-index) .<br /><br /> İşlevler, yalnızca bir dizinin alanlar koleksiyonunda tanımlanan alanlara uygulanabilir.|  

 Dizin tanımlandıktan sonra, Dizin şemasını karşıya yükleyerek ve ardından belgeler ile dizini oluşturun. Bu işlemler hakkında yönergeler için bkz. [azure Bilişsel Arama&#41;REST API dizin oluşturma &#40;](/rest/api/searchservice/create-index) ve Azure &#40;bilişsel arama REST API Için [belge ekleme, güncelleştirme veya silme ](/rest/api/searchservice/addupdate-or-delete-documents) . Dizin oluşturulduktan sonra, arama verileriyle birlikte çalışan işlevsel bir Puanlama profiline sahip olmanız gerekir.  

##  <a name="template"></a><a name="bkmk_template"></a> Şablonlarını  
 Bu bölümde, Puanlama profillerinin sözdizimi ve şablonu gösterilmektedir. Özniteliklerin açıklamaları için sonraki bölümde [dizin öznitelikleri başvurusuna](#bkmk_indexref) bakın.  

```  
. . .   
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
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...",   
. . .  
```  

##  <a name="index-attributes-reference"></a><a name="bkmk_indexref"></a> Dizin öznitelikleri başvurusu  

> [!NOTE]  
>  Puanlama işlevi yalnızca filtrelenebilir alanlara uygulanabilir.  

|Öznitelik|Açıklama|  
|---------------|-----------------|  
|`name`|Gereklidir. Bu, Puanlama profilinin adıdır. Bir alanın adlandırma kurallarına uyar. Harf ile başlaması gerekir, nokta, iki nokta üst üste veya @ sembolleri içeremez ve ' azureSearch ' (büyük/küçük harfe duyarlı) tümcesiyle başlayamaz.|  
|`text`|Ağırlıklar özelliğini içerir.|  
|`weights`|İsteğe bağlı. Her biri bir alan adı ve göreli ağırlık belirten ad-değer çiftleri içerir. Göreli ağırlık pozitif bir tamsayı veya kayan noktalı sayı olmalıdır.<br /><br /> Ağırlıklar, bir aranabilir alanın diğerine göre önemini göstermek için kullanılır.|  
|`functions`|İsteğe bağlı. Puanlama işlevi yalnızca filtrelenebilir alanlara uygulanabilir.|  
|`type`|Puanlama işlevleri için gereklidir. Kullanılacak işlevin türünü gösterir. Geçerli değerler arasında büyüklük, yenilik, uzaklık ve etiket bulunur. Her Puanlama profiline birden fazla işlev ekleyebilirsiniz. İşlev adı küçük harf olmalıdır.|  
|`boost`|Puanlama işlevleri için gereklidir. Ham puan çarpanı olarak kullanılan pozitif bir sayı. 1 değerine eşit olamaz.|  
|`fieldname`|Puanlama işlevleri için gereklidir. Puanlama işlevi yalnızca dizinin alan koleksiyonunun parçası olan ve filtrelenebilir olan alanlara uygulanabilir. Bunlara ek olarak, her bir işlev türü ek kısıtlamalar getirir (yeniliği DateTime alanları, tamsayı veya çift alanlarla büyüklüğü ve konum alanlarıyla mesafe) sağlar. Her işlev tanımı için yalnızca tek bir alan belirtebilirsiniz. Örneğin, aynı profilde iki kez büyüklük kullanmak için, her bir alan için bir tane olmak üzere iki tanım büyüklüğü dahil etmeniz gerekir.|  
|`interpolation`|Puanlama işlevleri için gereklidir. Aralığın başından aralığın sonuna kadar olan puanının arttığı eğimi tanımlar. Geçerli değerler doğrusal (varsayılan), sabit, Ikinci dereceden ve Logaritmik ' i içerir. Ayrıntılar için bkz. [enterpolasyonlarını ayarlama](#bkmk_interpolation) .|  
|`magnitude`|Büyüklük Puanlama işlevi, sayısal bir alan için değer aralığına göre rantısallar değiştirmek için kullanılır. Bunun en yaygın kullanım örneklerinden bazıları şunlardır:<br /><br /> -   **Yıldız derecelendirmeleri:** "Yıldız derecelendirmesi" alanı içindeki değere göre Puanlama değiştirin. İki öğe ilgili olduğunda, öncelikle daha yüksek derecelendirme olan öğe görüntülenir.<br />-   **Kenar boşluğu:** İki belge uygun olduğunda, bir satıcı önce daha yüksek kenar boşluklarına sahip olan belgeleri artırmak isteyebilir.<br />-   **Tıklama sayısı:** Ürünlerin veya sayfaların eylemleri arasında tıklama işlemlerini izleyen uygulamalarda, en fazla trafiği almak için olan öğeleri artırmak üzere büyüklük kullanabilirsiniz.<br />-   **İndirme sayısı:** İndirme işlemleri izleyen uygulamalar için, büyüklük işlevi en çok indirmelere sahip öğeleri artırmanıza olanak tanır.|  
|`magnitude` &#124; `boostingRangeStart`|Aralığın puanlanması için başlangıç değerini ayarlar. Değer bir tamsayı veya kayan noktalı sayı olmalıdır. 1 ile 4 arasında yıldız dereceleri için bu 1 olur. %50 üzerindeki kenar boşlukları için bu 50 olacaktır.|  
|`magnitude` &#124; `boostingRangeEnd`|Büyüklük puanlanması gereken aralığın bitiş değerini ayarlar. Değer bir tamsayı veya kayan noktalı sayı olmalıdır. 1 ile 4 arasında yıldız dereceleri için bu 4 olur.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Geçerli değerler true veya false (varsayılan) şeklindedir. True olarak ayarlandığında, tam artırma, aralığın üst ucundan daha yüksek olan hedef alan için bir değer olan belgelere uygulanmaya devam edecektir. False ise, bu işlevin artışı aralığın dışında kalan hedef alan için bir değere sahip belgelere uygulanmaz.|  
|`freshness`|Yenilik Puanlama işlevi, alanlardaki değerlere göre öğelerin derecelendirme puanlarını değiştirmek için kullanılır `DateTimeOffset` . Örneğin, son tarihi daha eski öğelerden daha yüksek bir öğe olabilir.<br /><br /> Ayrıca, gelecekteki tarihlerle birlikte takvim olayları gibi öğeleri derecelendirmek de mümkündür. bu öğeler, gelecekte de daha fazla öğeden daha fazla derecelendirilir.<br /><br /> Geçerli hizmet sürümünde, aralığın bir sonu geçerli saate göre düzeltilecektir. Diğer son, ' a göre geçmişte bir zaman olur `boostingDuration` . Gelecekte bir zaman aralığını artırmak için negatif bir değer kullanın `boostingDuration` .<br /><br /> En yüksek ve en düşük aralıktan yükseltme yaptığı oran, Puanlama profiline uygulanan enterpolasyon tarafından belirlenir (aşağıdaki şekle bakın). Uygulanan yükseltme faktörünü tersine çevirmek için 1 ' den küçük bir artırma faktörü seçin.|  
|`freshness` &#124; `boostingDuration`|Belirli bir belge için yükseltme durdurulacak süre sonu süresini ayarlar. Sözdizimi ve örnekler için aşağıdaki bölümde bulunan [boostingDuration ayarlama](#bkmk_boostdur) bölümüne bakın.|  
|`distance`|Uzaklık Puanlama işlevi, bir başvuru coğrafi konumuyla ne kadar yakın ve ne kadar ilerleeceğine göre belgelerin Puanını etkilemek için kullanılır. Başvuru konumu, bir parametresindeki sorgunun parçası olarak ( `scoringParameterquery` dize seçeneği kullanılarak) bir Lon, Lat bağımsız değişkeni olarak verilir.|  
|`distance` &#124; `referencePointParameter`|Başvuru konumu olarak kullanılacak sorgularda geçirilecek bir parametre. `scoringParameter` bir sorgu parametresidir. Sorgu parametrelerinin açıklamaları için bkz. [Azure Bilişsel Arama &#40;arama belgeleri REST API&#41;](/rest/api/searchservice/Search-Documents) .|  
|`distance` &#124; `boostingDistance`|Yükseltme aralığının bittiği başvuru konumundan kilometre cinsinden mesafeyi gösteren bir sayı.|  
|`tag`|Etiket Puanlama işlevi, belge ve arama sorgularında etiketlere göre belgelerin Puanını etkilemek için kullanılır. Arama sorgusuyla ortak etiketlerin bulunduğu belgeler de artıralınacaktır. Arama sorgusunun etiketleri, her arama isteğinde bir Puanlama parametresi olarak sağlanır ( `scoringParameterquery` dize seçeneği kullanılarak).|  
|`tag` &#124; `tagsParameter`|Belirli bir istek için etiketleri belirtmek üzere sorgulara geçirilecek bir parametre. `scoringParameter` bir sorgu parametresidir. Sorgu parametrelerinin açıklamaları için bkz. [Azure Bilişsel Arama &#40;arama belgeleri REST API&#41;](/rest/api/searchservice/Search-Documents) .|  
|`functionAggregation`|İsteğe bağlı. Yalnızca işlevler belirtildiğinde geçerlidir. Geçerli değerler şunlardır: Sum (varsayılan), ortalama, minimum, maksimum ve Firsteþleþen. Arama puanı, birden çok işlev dahil olmak üzere birden çok değişkenden hesaplanan tek değerdir. Bu öznitelik, tüm işlevlerin tümünün, temel belge puanına uygulanan tek bir toplam Boost içinde nasıl birleştirildiğini gösterir. Taban puanı, belgeden ve Arama sorgusundan hesaplanan [tf-IDF](http://www.tfidf.com/) değerine göre belirlenir.|  
|`defaultScoringProfile`|Bir arama isteği yürütürken, Puanlama profili belirtilmemişse, varsayılan Puanlama kullanılır (yalnızca[tf-IDF](http://www.tfidf.com/) ).<br /><br /> Varsayılan bir Puanlama profili adı buradan ayarlanabilir, bu, arama isteğinde belirli bir profil verilmediğinde Azure Bilişsel Arama bu profili kullanmasına neden olur.|  

##  <a name="set-interpolations"></a><a name="bkmk_interpolation"></a> Enterpolasyonlarını ayarla  
 Ara değer, Puanlama için kullanılan eğim şeklini ayarlamanıza olanak sağlar. Puanlama yüksek ve düşük olduğundan, eğim her zaman azalabilir, ancak ilişkilendirme, aşağı doğru eğim eğrisini belirler. Aşağıdaki enterpolasyonda kullanılabilir:  

| İlişkilendirme | Description |  
|-|-|  
|`linear`|En büyük ve en düşük aralıktaki öğeler için, öğeye uygulanan arttırma sürekli azalan bir miktarda yapılır. Doğrusal, bir Puanlama profili için varsayılan enterpolasyon.|  
|`constant`|Başlangıç ve bitiş aralığı içinde olan öğeler için, derece sonuçlarına sabit bir artırma uygulanır.|  
|`quadratic`|Sürekli azalmış bir yükseği olan doğrusal bir ilişkilendirme ile karşılaştırıldığında, karesel olarak daha küçük bir hızda azaltılır ve ardından bitiş aralığına yaklaşıyorsa, çok daha yüksek bir aralığa yaklaşacaktır. Etiket Puanlama işlevlerinde Bu enterpolasyon seçeneğine izin verilmez.|  
|`logarithmic`|Sürekli azalmış bir yükseği olan doğrusal bir ilişkilendirme ile karşılaştırıldığında, logaritmik bir süre önce daha yüksek bir hızda azaltılır ve sonra son aralığa yaklaşırsa, çok daha küçük bir aralığa yaklaşacaktır. Etiket Puanlama işlevlerinde Bu enterpolasyon seçeneğine izin verilmez.|  

 ![Grafik üzerinde sabit, doğrusal, karesel, log10 çizgileri](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="set-boostingduration"></a><a name="bkmk_boostdur"></a> BoostingDuration 'ı ayarlama  
 `boostingDuration` , işlevinin bir özniteliğidir `freshness` . Belirli bir belge için yükseltme durdurulacak bir süre sonu dönemi ayarlamak için bunu kullanırsınız. Örneğin, 10 günlük promosyon dönemi için bir ürün satırını veya markasını artırmak üzere, bu belgeler için 10 günlük dönemi "P10D" olarak belirtmeniz gerekir.  

 `boostingDuration` XSD "dayTimeDuration" değeri (ISO 8601 Duration değerinin kısıtlı bir alt kümesi) olarak biçimlendirilmelidir. Bunun için model: "P [nD] [T [nH] [nM] [nS]]".  

 Aşağıdaki tabloda birkaç örnek verilmiştir.  

|Süre|boostingDuration|  
|--------------|----------------------|  
|1 gün|"P1D"|  
|2 gün ve 12 saat|"P2DT12H"|  
|15 dakika|"PT15M"|  
|30 gün, 5 saat, 10 dakika ve 6,334 saniye|"P30DT5H10M 6.334 S"|  

 Daha fazla örnek için bkz. [XML şeması: veri türleri (w3.org web sitesi)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Ayrıca bkz.  

+ [REST API başvurusu](/rest/api/searchservice/)
+ [Dizin API 'SI oluştur](/rest/api/searchservice/create-index)
+ [Azure Bilişsel Arama .NET SDK](/dotnet/api/overview/azure/search?)
