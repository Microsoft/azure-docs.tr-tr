---
title: Veri akışı betiği eşleniyor
description: Data Factory veri akışı betiği arka plan koduna genel bakış
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2021
ms.openlocfilehash: 7dd58a7d4a94b832e52930f8ac6507cdd8f7a20e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100534830"
---
# <a name="data-flow-script-dfs"></a>Veri akışı betiği (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Veri akışı betiği (DFS), bir eşleme veri akışında bulunan dönüştürmeleri yürütmek için kullanılan bir kodlama diline benzer şekilde temel alınan meta verileridir. Her dönüşüm, işi düzgün şekilde çalıştırmak için gerekli bilgileri sağlayan bir dizi özellik tarafından temsil edilir. Betik, tarayıcı kullanıcı arabiriminin üst şeridinde "komut dosyası" düğmesine tıklanarak ADF 'den görünür ve düzenlenebilir.

![Betik düğmesi](media/data-flow/scriptbutton.png "Betik düğmesi")

Örneğin, `allowSchemaDrift: true,` bir kaynak dönüşümünde, bu hizmet, şema projeksiyonsuz olmasalar bile veri akışındaki kaynak veri kümesinden tüm sütunları içermesini söyler.

## <a name="use-cases"></a>Uygulama alanları
DFS, Kullanıcı arabirimi tarafından otomatik olarak üretilir. Betiği görüntülemek ve özelleştirmek için betik düğmesine tıklayabilirsiniz. Ayrıca ADF Kullanıcı arabirimi dışında betikler oluşturabilir ve ardından bunu PowerShell cmdlet 'ine geçirebilirsiniz. Karmaşık veri akışları hata ayıklarken, akışlarınızın UI grafik gösterimini taramak yerine, arka plan kod dosyasını taramayı daha kolay bulabilirsiniz.

Örnek kullanım örnekleri aşağıda verilmiştir:
- Oldukça benzer, yani "damgalama" veri akışları gibi çok sayıda veri akışı program aracılığıyla.
- Kullanıcı arabiriminde yönetilmesi zor olan karmaşık ifadeler veya doğrulama sorunlarına neden olur.
- Hata ayıklama ve yürütme sırasında döndürülen çeşitli hataları daha iyi anlama.

PowerShell veya API ile kullanmak üzere bir veri akışı betiği oluşturduğunuzda, biçimlendirilen metni tek bir satıra daraltmanız gerekir. Sekmeleri ve newlines kaçış karakterleri olarak tutabilirsiniz. Ancak metin bir JSON özelliğinin içine sığacak şekilde biçimlendirilmelidir. Alt kısımdaki komut dosyası düzenleyici Kullanıcı arabiriminde, betiği sizin için tek bir satır olarak biçimlendirilecek bir düğme vardır.

![Kopyala düğmesini](media/data-flow/copybutton.png "Kopyala düğmesini")

## <a name="how-to-add-transforms"></a>Dönüşümler ekleme
Dönüşümler eklemek için üç temel adım gerekir: çekirdek dönüştürme verilerini ekleme, giriş akışını yeniden yönlendirme ve ardından çıkış akışını yeniden yönlendirme. Bu, bir örnekte en kolay şekilde görülebilir.
Aşağıdaki gibi veri akışını havuza almak için basit bir kaynakla başlayabiliriz:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Türeme dönüştürmesi eklemeye karar verirse, ilk olarak, adlı yeni bir büyük sütun eklemek için basit bir ifadeye sahip olan temel dönüşüm metnini oluşturmanız gerekir `upperCaseTitle` :
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Ardından, var olan DFS 'yi aldık ve dönüşümü ekliyoruz:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Şimdi de, yeni dönüştürmenin ne kadar sonra gelmesini istediğini (Bu durumda `source1` ) ve akışın adını yeni dönüştürmeye kopyalamak istediğimiz dönüştürmeyi tanımlayarak gelen akışı yeniden yönlendirdik:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Son olarak, bu yeni dönüşümden sonra gelmesi istediğimiz dönüşümü tanımlıyoruz ve giriş akışını (Bu durumda, `sink1` ) yeni dönüştürtiğimiz çıkış akışı adıyla değiştirin:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>DFS temelleri
DFS, kaynaklar, havuzlar ve yeni sütun ekleyebilen, verileri filtreleyip verileri birleştirebilen ve çok daha fazlasını içeren bir dizi bağlantılı dönüşümden oluşur. Genellikle, bir veya daha fazla kaynak ile başlayan betik, bir veya daha fazla dönüştürme ile izlenir ve bir veya daha fazla havuz ile sona eriyor.

Kaynakların hepsi aynı temel yapıya sahiptir:
```
source(
  source properties
) ~> source_name
```

Örneğin, üç sütunlu (Movieıd, title, tarzlar) bir basit kaynak şöyle olacaktır:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Kaynaklar dışındaki tüm dönüşümler aynı temel yapıya sahiptir:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Örneğin, bir sütunu (başlık) alan ve büyük harfli bir sürümle üzerine yazan basit bir türet dönüşümü aşağıdaki gibidir:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

Ve şemaya sahip olmayan bir havuz yalnızca şu şekilde olabilir:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Betik parçacıkları

Betik parçacıkları, veri akışları genelinde paylaşmak için kullanabileceğiniz, paylaşılabilir veri akışı komut dosyası kodudur. Aşağıdaki videoda, komut dosyası parçacıklarının nasıl kullanılacağı ve veri akışı grafiklerinizin arkasındaki betiğin parçalarını kopyalamak ve yapıştırmak için veri akışı betiği kullanılarak ilgili bilgi verilmiştir:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Toplu Özet istatistikleri
Veri akışınıza "SummaryStats" adlı bir toplam dönüşüm ekleyin ve ardından, var olan SummaryStats öğesini değiştirerek betiğinizdeki toplama işlevi için aşağıdaki kodu yapıştırın. Bu, veri profili Özet istatistikleri için genel bir model sağlar.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Ayrıca, verilerinizin benzersiz sayısını ve farklı satır sayısını saymak için aşağıdaki örneği de kullanabilirsiniz. Aşağıdaki örnek, ValueDistAgg adlı toplu dönüşümle bir veri akışına yapıştırılamaz. Bu örnek, "title" adlı bir sütun kullanır. "Title" değerini, değer sayılarını almak için kullanmak istediğiniz verilerinizde dize sütunuyla değiştirdiğinizden emin olun.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Tüm sütunları bir toplamaya dahil et
Bu, toplamalar oluştururken çıktı meta verilerinde kalan sütunları nasıl tutabileceğinizi gösteren genel bir toplama modelidir. Bu durumda, ```first()``` adı "film" olmayan her sütunda ilk değeri seçmek için işlevini kullanırız. Bunu kullanmak için, DistinctRows adlı bir toplam dönüşüm oluşturun ve ardından bunu, var olan DistinctRows toplama betiğinin üst kısmına yapıştırın.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Satır karması parmak izi oluştur 
```DWhash```Üç sütun karması üreten adlı yeni bir türetilmiş sütun oluşturmak için veri akışı betiğinizdeki bu kodu kullanın ```sha1``` .

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

Bu komut dosyasını, her sütuna ad vermek zorunda kalmadan akışınız içindeki tüm sütunları kullanarak bir satır karması oluşturmak için de kullanabilirsiniz:

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg eşdeğeri
Bu kod T-SQL işlevi gibi davranır ```string_agg()``` ve dize değerlerini bir dizi olarak toplar. Daha sonra bu diziyi SQL hedefleri ile kullanmak üzere bir dizeye çevirebilirsiniz.

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>Güncelleştirme sayısı, yukarı serts, ekler, siler
Alter Row dönüşümünü kullanırken, bu sonucu alter Row ilkelerinizin içinde silen güncelleştirme sayısını saymak isteyebilirsiniz. Değişiklik satırınızda bir toplama dönüştürmesi ekleyin ve bu veri akışı betiğini bu sayımlar için toplama tanımına yapıştırın.

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>Tüm sütunları kullanan ayrı satır
Bu kod parçacığı, veri akışınıza tüm gelen sütunları alacak yeni bir toplam dönüşüm ekler, yinelemeleri ortadan kaldırmak için gruplandırma için kullanılan bir karma oluşturur ve ardından her tekrarın her yinelenmesinin çıkış olarak sağlanması gerekir. Sütunları açıkça yazmanız gerekmez, bunlar otomatik olarak gelen veri akışınızdan oluşturulur.

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>Tüm sütunlarda null değerleri denetle
Bu, veri akışınıza yapıştırabileceğiniz ve tüm sütunlarınızın boş değerler için genel olarak denetlenmesi için bir kod parçacıbiridir. Bu teknik, tüm satırlardaki tüm sütunları bulmak için şema değişikliklerini 'i kullanır ve null değerleri olmayan satırlardan satırları null değeriyle ayırmak için koşullu bir bölme kullanır. 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

### <a name="automap-schema-drift-with-a-select"></a>Bir select ile AutoMap şeması DRFT
Bilinmeyen veya dinamik bir gelen sütun kümesinden mevcut bir veritabanı şemasını yüklemeniz gerektiğinde, havuz dönüşümünde sağ taraftaki sütunları eşlemeniz gerekir. Bu yalnızca var olan bir tabloyu yüklerken gereklidir. Sütunlarınızın otomatik olarak eşlendiği bir seçim oluşturmak için havuzunuzu önce Bu kod parçacığını ekleyin. Havuz eşlemenizi otomatik eşlenecek şekilde bırakın.

```
select(mapColumn(
        each(match(true()))
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> automap
```

### <a name="persist-column-data-types"></a>Sütun veri türlerini kalıcı yap
Veri akışınızdan sütun adlarını ve veri türlerini havuz kullanarak kalıcı bir depoya depolamak için bu betiği türetilmiş bir sütun tanımı içine ekleyin.

```
derive(each(match(type=='string'), $$ = 'string'),
    each(match(type=='integer'), $$ = 'integer'),
    each(match(type=='short'), $$ = 'short'),
    each(match(type=='complex'), $$ = 'complex'),
    each(match(type=='array'), $$ = 'array'),
    each(match(type=='float'), $$ = 'float'),
    each(match(type=='date'), $$ = 'date'),
    each(match(type=='timestamp'), $$ = 'timestamp'),
    each(match(type=='boolean'), $$ = 'boolean'),
    each(match(type=='long'), $$ = 'long'),
    each(match(type=='double'), $$ = 'double')) ~> DerivedColumn1
```

### <a name="fill-down"></a>Aşağı Doldur
NULL değerleri dizideki önceki NULL olmayan değerden değeri ile değiştirmek istediğinizde, veri kümeleriyle ortak "doldur" sorununun nasıl uygulanacağı aşağıda verilmiştir. Tüm veri kümesi genelinde bir "kukla" kategori değeri ile yapay bir pencere oluşturmanız gerektiğinden, bu işlemin olumsuz performans etkilerine sahip olabileceğini unutmayın. Ayrıca, önceki NULL olmayan değeri bulmak için uygun veri sırasını oluşturmak üzere bir değere göre sıralama yapmanız gerekir. Aşağıdaki kod parçacığı yapay kategoriyi "kukla" olarak oluşturur ve bir yedek anahtara göre sıralar. Vekil anahtarı kaldırabilir ve kendi verilerinize özgü sıralama anahtarınızı kullanabilirsiniz. Bu kod parçacığı, zaten adlı bir kaynak dönüşümünü eklediğinizi varsayar ```source1```

```
source1 derive(dummy = 1) ~> DerivedColumn
DerivedColumn keyGenerate(output(sk as long),
    startAt: 1L) ~> SurrogateKey
SurrogateKey window(over(dummy),
    asc(sk, true),
    Rating2 = coalesce(Rating, last(Rating, true()))) ~> Window1
```

### <a name="moving-average"></a>Hareketli Ortalama
Hareketli ortalama, veri akışlarında bir Windows dönüşümü kullanılarak kolayca uygulanabilir. Aşağıdaki örnekte, Microsoft için stok fiyatlarının 15 günlük hareketli ortalaması oluşturulur.

```
window(over(stocksymbol),
    asc(Date, true),
    startRowOffset: -7L,
    endRowOffset: 7L,
    FifteenDayMovingAvg = round(avg(Close),2)) ~> Window1
```

## <a name="next-steps"></a>Sonraki adımlar

[Veri akışlarına genel bakış makalesini](concepts-data-flow-overview.md) kullanarak veri akışlarını inceleyin
