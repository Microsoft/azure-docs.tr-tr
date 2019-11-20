---
title: 'Örnek: çok düzeyli modeller'
titleSuffix: Azure Cognitive Search
description: Birden çok düzeyli Taksonomiler için, uygulama sayfalarına dahil ettiğiniz iç içe geçmiş bir gezinti yapısı oluşturma hakkında bilgi edinin.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792953"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>Örnek: Azure Bilişsel Arama 'de çok düzeyli modeller

Azure Bilişsel Arama şemaları, çok düzeyli taksonomi kategorilerini açık bir şekilde desteklemez, ancak dizin oluşturma işleminden önce içeriği düzenleyerek ve sonra sonuçlara özel bir işleme uygulayarak bunları yaklaşık olarak yapabilirsiniz. 

## <a name="start-with-the-data"></a>Verilerle başlayın

Bu makaledeki örnek, Azure Bilişsel Arama 'de çok düzeyli bir şekilde tam olarak göstermek için [AdventureWorks envanter veritabanını modelleyen](search-example-adventureworks-modeling.md)önceki bir örneği oluşturur.

AdventureWorks 'in üst-alt ilişkisi olan basit bir iki düzeyli bir sınıflandırması vardır. Bu yapının sabit uzunluklu taksonomi derinlikleri için, bir basit SQL JOIN sorgusu, taksonomiyi gruplamak için kullanılabilir:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Sorgu sonuçları](./media/search-example-adventureworks/prod-query-results.png "Sorgu sonuçları")

## <a name="indexing-to-a-collection-field"></a>Koleksiyon alanına dizin oluşturma

Bu yapıyı içeren dizinde, bu verileri depolamak için Azure Bilişsel Arama şemasında bir **koleksiyon (EDM. String)** alanı oluşturun ve alan özniteliklerinin aranabilir, filtrelenebilir, çok yönlü tablo ve alınabilir olduğundan emin olun.

Artık, belirli bir taksonomi kategorisine başvuran içeriği dizinlerken, taksonomiyi taksonomi düzeyinden metin içeren bir dizi olarak gönderir. Örneğin, `ProductCategoryId = 5 (Mountain Bikes)`bir varlık için, alanı `[ "Bikes", "Bikes|Mountain Bikes"]` olarak gönder

"Sıradağlar bisikletleri" alt kategori değerindeki "Bisiklet" ana kategorisinin eklenmesine dikkat edin. Her alt kategori, tüm yolunu kök öğesine göre katıştırmalıdır. Kanal karakter ayırıcısı rastgele, ancak tutarlı olmalıdır ve kaynak metinde görünmemelidir. Ayırıcı karakter, uygulama kodunda, model sonuçlarından taksonomi ağacını yeniden oluşturmak için kullanılır.

## <a name="construct-the-query"></a>Sorguyu oluşturun

Sorgu verirken, aşağıdaki model belirtimini (Taksonomi, çok yönlü tablo taksonomi alanınız) dahil edin: `facet = taxonomy,count:50,sort:value`

Count değeri, tüm olası taksonomi değerlerini döndürecek kadar yüksek olmalıdır. AdventureWorks verileri 41 farklı taksonomi değerleri içerir, bu nedenle `count:50` yeterlidir.

  ![Çok yönlü filtre](./media/search-example-adventureworks/facet-filter.png "Çok yönlü filtre")

## <a name="build-the-structure-in-client-code"></a>Yapıyı istemci kodunda oluştur

İstemci uygulama kodunuzda, kanal karakteri üzerindeki her bir model değerini bölerek taksonomi ağacını yeniden yapılandırma.

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

**Kategoriler** nesnesi artık, doğru sayımlar içeren daraltılabilir bir taksonomi ağacını işlemek için kullanılabilir:

  ![çok yönlü çok yönlü filtre](./media/search-example-adventureworks/multi-level-facet.png "çok yönlü çok yönlü filtre")

 
Ağaçtaki her bağlantı ilgili filtreyi uygulamalıdır. Örnek:

+ **taksonomi/any** `(x:x eq 'Accessories')`, aksesuarlar dalındaki tüm belgeleri döndürür
+ **taksonomi/any** `(x:x eq 'Accessories|Bike Racks')`, yalnızca aksesuar dalında Bisiklet raflarının alt kategorisini içeren belgeleri döndürür.

Bu teknik, daha derin taksonomi ağaçları ve farklı üst kategorilerde gerçekleşen yinelenen alt kategoriler gibi daha karmaşık senaryoları kapsayacak şekilde ölçeklendirecektir (örneğin, `Bike Components|Forks` ve `Camping Equipment|Forks`).

> [!TIP]
> Sorgu hızı döndürülen model sayısından etkilenir. Çok büyük taksonomi kümelerini desteklemek için, her belge için en üst düzey taksonomi değerini tutacak bir çok yönlü **Edm. String** alanı eklemeyi göz önünde bulundurun. Daha sonra aynı tekniği uygulayın, ancak kullanıcı üst düzey bir düğümü genişlediğinde koleksiyon modeli sorgusunu (kök taksonomi alanında filtrelenmiş) gerçekleştirin. Ya da %100 geri çekme gerekli değilse, model sayısını makul bir sayıya küçültün ve model girişlerinin sayıma göre sıralandığına emin olun.

## <a name="see-also"></a>Ayrıca bkz.

[Örnek: AdventureWorks Inventory Database for Azure Bilişsel Arama modelleme](search-example-adventureworks-modeling.md)