---
title: 'Örnek: AdventureWorks envanter veritabanını modelleme'
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de dizin oluşturma ve tam metin araması için, ilişkisel verileri nasıl modelleyeceğinizi ve bunları düzleştirilmiş bir veri kümesine dönüştürmeyi öğrenin.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: edb6162724938962df8a7340afea6e930a0b1049
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793001"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-cognitive-search"></a>Örnek: AdventureWorks Inventory Database for Azure Bilişsel Arama modelleme

Azure Bilişsel Arama, [Dizin oluşturma (veri alımı) ardışık düzenine](search-what-is-an-index.md)giriş olarak düzleştirilmiş bir satır kümesini kabul eder. Kaynak verileriniz SQL Server ilişkisel bir veritabanından kaynaklanıyorsa, bu makalede, bir örnek olarak AdventureWorks örnek veritabanını kullanarak dizin oluşturma öncesinde düzleştirilmiş bir satır kümesi oluşturmaya yönelik bir yaklaşım gösterilmektedir.

## <a name="about-adventureworks"></a>AdventureWorks hakkında

Bir SQL Server örneğiniz varsa, [AdventureWorks örnek veritabanı](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)hakkında bilgi sahibi olabilirsiniz. Bu veritabanına dahil olan tablolar arasında ürün bilgilerini açığa çıkaran beş tablo bulunur.

+ **ProductModel**: Name
+ **Ürün**: ad, renk, maliyet, boyut, ağırlık, resim, kategori (her satır belirli bir ProductModel 'e katılır)
+ **ProductDescription**: Açıklama
+ **Productmodelproductdescription**: locale (her satır bir ProductModel 'e belirli bir dil için belirli bir ProductDescription öğesine katılır)
+ **ProductCategory**: ad, üst kategori

Bu verilerin tümünün bir arama dizinine girebileceği bir düzleştirilmiş satır kümesine birleştirilmesi, bu örneğin amacı olur. 

## <a name="considering-our-options"></a>Seçeneklerimizi göz önünde bulundurarak

Ürün tablosu en belirli bilgilere sahip olduğundan, Naïve yaklaşımı, ürün tablosundaki (uygun olduğunda) tüm satırları dizinlemek olacaktır. Ancak, bu yaklaşım arama dizinini bir sonuç kümesinde algılanan yinelemeleri ortaya çıkarır. Örneğin, Road-650 modeli iki renkli ve altı boyut olarak kullanılabilir. Daha sonra, "Road Bisiklet" sorgusu, yalnızca boyut ve renge göre ayırt edilecek şekilde, aynı modelin her iki örneği tarafından uzlaştırılır. Diğer altı Road 'e özgü modelin her biri arama: sayfa 2 ' nin doğrulayamadı dünyasına eşit olarak dağıtılır.

  ![Ürün listesi](./media/search-example-adventureworks/products-list.png "Ürün listesi")
 
Road-650 modelinin on iki seçeneğe sahip olduğuna dikkat edin. Bire çok varlık satırları en iyi, arama dizininde çok değerli alanlar veya önceden toplanmış değer alanları olarak temsil edilir.

Bu sorunu çözmek, hedef dizini ProductModel tablosuna taşımak kadar basit değildir. Bunun yapılması, ürün tablosundaki arama sonuçlarında hala temsil edilmesi gereken önemli verileri yoksayar.

## <a name="use-a-collection-data-type"></a>Koleksiyon veri türü kullan

"Doğru yaklaşım" veritabanı modelinde doğrudan paralel olmayan bir arama şeması özelliğinden yararlanmadır: **koleksiyon (EDM. String)** . Bu yapı Azure Bilişsel Arama dizin şemasında tanımlanmıştır. Bir koleksiyon veri türü, çok uzun (tek) bir dize yerine ayrı dizelerin bir listesini temsil etmeniz gerektiğinde kullanılır. Etiketlerde veya anahtar sözcüklerinizle karşılaşırsanız, bu alan için bir koleksiyon veri türü kullanırsınız.

"Color", "size" ve "image" için **koleksiyonun (EDM. String)** çok değerli dizin alanlarını tanımlayarak, çift yönlü bilgiler, dizini yinelenen girdilerle yoklamadan ve filtrelemeye yönelik olarak tutulur. Benzer şekilde, her bir ürün **ListPrice**yerine **minlistprice** dizini oluşturmak Için toplama işlevlerini sayısal ürün alanlarına uygulayın.

Bu yapılarla bir dizin verildiğinde, "Sıradağlar bisikletleri" araması ayrı Bisiklet modellerini gösterir ve renk, boyut ve en düşük fiyat gibi önemli meta verileri korur. Aşağıdaki ekran görüntüsünde bir çizim sunulmaktadır.

  ![Sıradağlar Bisiklet arama örneği](./media/search-example-adventureworks/mountain-bikes-visual.png "Sıradağlar Bisiklet arama örneği")

## <a name="use-script-for-data-manipulation"></a>Veri işleme için betiği kullan

Ne yazık ki, bu tür modellemenin tek başına SQL deyimleri aracılığıyla kolayca elde edilemez. Bunun yerine, verileri yüklemek için basit bir NodeJS betiği kullanın ve ardından bunu arama dostu JSON varlıklarına eşleyin.

Son veritabanı arama eşlemesi şöyle görünür:

+ Model (EDM. String: aranabilir, filtrelenebilir, alınabilir) "ProductModel.Name" adresinden
+ Kültür = ' en ' olduğunda model için "ProductDescription" kaynağından description_en (EDM. String: aranabilir)
+ Color (koleksiyon (EDM. String): aranabilir, filtrelenebilir, çok yönlü tablo, alınabilir): model için "Product. Color" öğesinden benzersiz değerler
+ Boyut (koleksiyon (EDM. String): aranabilir, filtrelenebilir, çok yönlü tablo, alınabilir): model için "Product. size" benzersiz değerleri
+ Image (koleksiyon (EDM. String): alınabilir): model için "Product. ThumbnailPhoto" öğesinden benzersiz değerler
+ Mınstandardcost (EDM. Double: filtrelenebilir, çok yönlü tablo, sıralanabilir, alınabilir): model için en az "Product. StandardCost" öğesinin toplamı
+ minListPrice (EDM. Double: filtrelenebilir, çok yönlü tablo, sıralanabilir, alınabilir): model için en az "Product. ListPrice" öğesinin toplamı
+ minWeight (EDM. Double: filtrelenebilir, çok yönlü tablo, sıralanabilir, alınabilir): model için en az "Product. Weight" öğesinin toplamı
+ Ürünler (koleksiyon (EDM. String): aranabilir, filtrelenebilir, alınabilir): model için "Product.Name" öğesinden benzersiz değerler

ProductModel tablosunu Product ve ProductDescription ile katıldıktan sonra, sonuç kümesi 'ni hızlıca dönüştürmek için [lodash](https://lodash.com/) ( C#veya LINQ ın) kullanın:

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

Sonuçta elde edilen JSON şöyle görünür:

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

Son olarak, ilk kayıt kümesini döndürmek için SQL sorgusu aşağıda verilmiştir. Verileri NodeJS uygulamama yüklemek için [MSSQL](https://www.npmjs.com/package/mssql) NPM modülünü kullandım.

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Örnek: Azure Bilişsel Arama 'de çok düzeyli model sınıflandırmaları](search-example-adventureworks-multilevel-faceting.md)