---
title: Eşleme veri akışı 'nda ifade Oluşturucu
description: Azure Data Factory veri akışlarını eşleme bölümünde Ifade Oluşturucu kullanarak ifadeler oluşturun
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 753f201fbde5d9e7100b6e257f8dc79e4462d7b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584932"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Eşleme veri akışında derleme ifadeleri

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Eşleme veri akışı ' nda birçok dönüştürme özelliği ifadeler olarak girilir. Bu ifadeler, çalışma zamanında Spark veri türü sonucunu değerlendiren sütun değerleri, parametreler, işlevler, işleçler ve değişmez değerlerdir. Veri akışları eşleme, **Ifade Oluşturucusu** olarak adlandırılan bu ifadeleri oluşturma konusunda size yardımcı olmak için adanmış bir deneyimle sahiptir. Vurgulama, sözdizimi denetimi ve otomatik tamamlama için  [IntelliSense](/visualstudio/ide/using-intellisense) kod tamamlamayı kullanarak, ifade Oluşturucusu, veri akışı oluşturmayı kolay hale getirmek için tasarlanmıştır. Bu makalede, iş mantığınızı etkin bir şekilde oluşturmak için ifade oluşturucusunun nasıl kullanılacağı açıklanmaktadır.

![İfade Oluşturucusu](media/data-flow/expresion-builder.png "İfade Oluşturucusu")

## <a name="open-expression-builder"></a>Ifade Oluşturucuyu Aç

İfade oluşturucuyu açmak için birden çok giriş noktası var. Bunlar, veri akışı dönüşümünün belirli bağlamına bağımlıdır. En yaygın kullanım örneği, [türetilmiş sütun](data-flow-derived-column.md) ve [toplama](data-flow-aggregate.md) gibi, kullanıcıların veri akışı ifade dilini kullanarak sütun oluşturup güncelleştirmediği bir şekilde dönüştürmelerinden alınmıştır. İfade Oluşturucusu, sütun listesinin üstünde, **ifade Oluşturucu aç** seçilerek açılabilir. Ayrıca, bir sütun bağlamına tıklayıp deyim oluşturucuyu doğrudan bu ifadeye açabilirsiniz.

![Açık Ifade Oluşturucu türet](media/data-flow/open-expression-builder-derive.png "Açık Ifade Oluşturucu türet")

[Filtre](data-flow-filter.md)gibi bazı dönüşümlerdeki mavi ifade metin kutusuna tıkladığınızda ifade Oluşturucusu açılır. 

![Mavi ifade kutusu](media/data-flow/expressionbox.png "Mavi ifade kutusu")

Bir eşleşen veya gruplandırma koşulunda sütunlara başvuru yaptığınızda bir ifade, sütunlardaki değerleri ayıklayabilir. Bir ifade oluşturmak için, **hesaplanan sütun**' u seçin.

![Hesaplanan sütun seçeneği](media/data-flow/computedcolumn.png "Hesaplanan sütun seçeneği")

Bir ifadenin veya değişmez değerin geçerli girişler olduğu durumlarda, değişmez değer olarak değerlendirilen bir ifade oluşturmak için **dinamik Içerik Ekle** ' yi seçin.

![Dinamik içerik Ekle seçeneği](media/data-flow/add-dynamic-content.png "Dinamik içerik Ekle seçeneği")

## <a name="expression-elements"></a>İfade öğeleri

Veri akışlarını eşleme bölümünde, ifadeler sütun değerlerinden, parametrelerden, işlevlerden, yerel değişkenlerden, işleçlerden ve değişmez değerlerden oluşabilir. Bu ifadeler String, Boolean veya integer gibi bir Spark veri türü olarak değerlendirilmelidir.

![İfade öğeleri](media/data-flow/expression-elements.png "İfade öğeleri")

### <a name="functions"></a>İşlevler

Veri akışları eşleme, ifadelerde kullanılabilecek yerleşik işlevlere ve işleçlere sahiptir. Kullanılabilir işlevlerin listesi için bkz. [eşleme veri akışı dili başvurusu](data-flow-expression-functions.md).

#### <a name="address-array-indexes"></a>Adres dizisi dizinleri

Dizi türleri döndüren sütunlarla veya işlevlerle ilgilenirken, belirli bir öğeye erişmek için köşeli ayraç ([]) kullanın. Dizin yoksa, ifade NULL olarak değerlendirilir.

![İfade Oluşturucu dizisi](media/data-flow/expression-array.png "İfade verileri önizlemesi")

> [!IMPORTANT]
> Veri akışlarını eşleme bölümünde, diziler tek tabanlı anlamına gelir ve ilk öğeye birinci dizin tarafından başvurulur. Örneğin, myArray [1], ' myArray ' adlı bir dizinin ilk öğesine erişir.

### <a name="input-schema"></a>Giriş şeması

Veri akışınız, herhangi bir kaynağında tanımlı bir şema kullanıyorsa, birçok ifadede ada göre bir sütuna başvurabilirsiniz. Şema DRI kullanıyorsanız, `byName()` veya işlevlerini kullanarak sütunlara veya `byNames()` sütun düzenlerini kullanarak eşleştirebilirsiniz.

#### <a name="column-names-with-special-characters"></a>Özel karakterlerle sütun adları

Özel karakterler veya boşluk içeren sütun adlarınız varsa, bu adı bir ifadede başvurmak için küme ayraçları ile çevreleyin.

```{[dbo].this_is my complex name$$$}```

### <a name="parameters"></a>Parametreler

Parametreler, bir işlem hattından çalışma zamanında bir veri akışına geçirilen değerlerdir. Bir parametreye başvurmak için, **ifade öğeleri** görünümünden parametreye tıklayın veya adın önünde bir dolar işareti ile başvuru yapın. Örneğin, parametre1 adlı bir parametreye tarafından başvurulur `$parameter1` . Daha fazla bilgi edinmek için bkz. [eşleme veri akışlarını parametrize](parameters-data-flow.md)etme.

### <a name="cached-lookup"></a>Önbelleğe alınan arama

Önbelleğe alınmış bir arama, önbelleğe alınmış bir havuzun çıktısının satır içi aramasını yapmanıza olanak sağlar. Her havuzda kullanabileceğiniz iki işlev bulunur `lookup()` ve `outputs()` . Bu işlevlere başvuran sözdizimi `cacheSinkName#functionName()` . Daha fazla bilgi için bkz. [önbellek havuzları](data-flow-sink.md#cache-sink).

`lookup()` geçerli dönüşümdeki eşleşen sütunları parametreler olarak alır ve önbellek havuzunda anahtar sütunlarla eşleşen satıra eşit bir karmaşık sütun döndürür. Döndürülen karmaşık sütun, önbellek havuzunda eşlenen her sütun için bir alt sütun içerir. Örneğin, `errorCodeCache` kod üzerinde ve adlı bir sütunda eşleşen bir anahtar sütunu olan bir hata kodu önbellek havuzunuzla karşılaşırsanız `Message` . Çağırmak, `errorCodeCache#lookup(errorCode).Message` iletilen kodla eşleşen iletiyi döndürür. 

`outputs()` Hiçbir parametre alır ve tüm önbellek havuzunu karmaşık sütun dizisi olarak döndürür. Bu, havuzda anahtar sütunları belirtilmişse ve yalnızca önbellek havuzunda az sayıda satır varsa kullanılmalıdır. Yaygın kullanım durumu, bir artırma anahtarının en büyük değerini ekleme. Önbelleğe alınmış tek toplanmış bir satır `CacheMaxKey` bir sütun içeriyorsa `MaxKey` , çağırarak ilk değere başvurabilirsiniz `CacheMaxKey#outputs()[1].MaxKey` .

![Önbelleğe alınan arama](media/data-flow/cached-lookup-example.png "Önbelleğe alınan arama")

### <a name="locals"></a>Ayarlanmalıdır

Mantığı birden çok sütunda paylaşıyorsanız veya mantığınızı eklemek istiyorsanız, türetilmiş bir column\içinde yerel bir oluşturabilirsiniz. Yerel bir başvuruda bulunmak için, **ifade öğeleri** görünümünden yerel öğesine tıklayın veya bunun adının önünde bir iki nokta üst üste ile başvuru yapın. Örneğin, local1 adlı bir yerel, tarafından başvurulmalıdır `:local1` . [Türetilmiş sütun belgelerindeki](data-flow-derived-column.md#locals)Yereller hakkında daha fazla bilgi edinin.

## <a name="preview-expression-results"></a>Önizleme ifadesi sonuçları

[Hata ayıklama modu](concepts-data-flow-debug-mode.md) açık ise, ifadenizde hangi amaçla değerlendirileceğini önizlemek için hata ayıklama kümesini etkileşimli olarak kullanabilirsiniz. Veri önizlemesinin sonuçlarını güncelleştirmek için veri Önizlemesi ' nin yanındaki **Yenile** ' yi seçin. Giriş sütunları verilen her bir satırın çıktısını görebilirsiniz.

![Devam eden Önizleme](media/data-flow/preview-expression.png "İfade verileri önizlemesi")

## <a name="string-interpolation"></a>Dize ilişkilendirme

İfade öğeleri kullanan uzun dizeler oluştururken, karmaşık dize mantığını kolayca oluşturmak için dize ilişkilendirmeyi kullanın. Dize ilişkilendirme, sorgu dizelerine parametreler dahil edildiğinde dize bitişinin kapsamlı kullanımını önler. Sabit dize metnini ifadelerle birlikte tırnak içine almak için çift tırnak işareti kullanın. İfade işlevleri, sütunlar ve parametreler ekleyebilirsiniz. İfade sözdizimini kullanmak için, küme ayraçları içine alın

Dize ilişkilendirme için bazı örnekler:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

> [!NOTE]
> SQL kaynak sorgularında dize ilişkilendirme söz dizimi kullanılırken, sorgu dizesinin '/n ' olmadan tek bir satırda olması gerekir.

## <a name="commenting-expressions"></a>Açıklama ifadeleri

Tek satırlı ve çok satırlı açıklama söz dizimini kullanarak deyimlerinizi açıklama ekleyin.

Aşağıdaki örnekler geçerli açıklamalardır:

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

İfadeniz üzerine bir yorum koyarsanız, dönüştürme ifadelerinizi belgelemek için dönüşüm metin kutusunda görünür.

![Dönüştürme metin kutusunda açıklama](media/data-flow/comment-expression.png "Yorumlar")

## <a name="regular-expressions"></a>Normal ifadeler

Birçok ifade dili işlevi normal ifade söz dizimini kullanır. Normal ifade işlevleri kullandığınızda, Ifade Oluşturucusu bir ters eğik çizgi ( \\ ) karakterini bir kaçış karakter sırası olarak yorumlamaya çalışır. Normal ifadenizde ters eğik çizgi kullandığınızda, tüm Regex geri işaretleri () içine alın ya da \` çift ters eğik çizgi kullanın.

Geri işaretleri kullanan bir örnek:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Çift eğik çizgi kullanan bir örnek:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="keyboard-shortcuts"></a>Klavye kısayolları

Aşağıda, ifade Oluşturucusu 'nda bulunan kısayolların bir listesi verilmiştir. İfade oluştururken çoğu IntelliSense kısayolu kullanılabilir.

* CTRL + K CTRL + C: tüm satırı açıklama.
* CTRL + K Ctrl + U: Açıklama kaldır.
* F1: Düzenleyici Yardım komutları sağlar.
* Alt + aşağı ok tuşu: geçerli satırı aşağı taşı.
* Alt + yukarı ok tuşu: geçerli satırı yukarı taşı.
* CTRL + Ara çubuğu: bağlam yardımını göster.

## <a name="commonly-used-expressions"></a>Yaygın olarak kullanılan ifadeler

### <a name="convert-to-dates-or-timestamps"></a>Tarihlere veya zaman damgalarına Dönüştür

Zaman damgası çıkışındaki dize sabit değerlerini dahil etmek için, dönüştürmeyi ' de sarın ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Süreyi dönem 'den bir tarih veya zaman damgasına dönüştürmek için kullanın `toTimestamp(<number of milliseconds>)` . Süre saniyeler içinde geliyorsa 1.000 ile çarpın.

```toTimestamp(1574127407*1000l)```

Önceki ifadenin sonundaki "l" ifadesi, uzun bir tür için satır içi sözdizimi olarak dönüştürmeyi belirtir.

### <a name="find-time-from-epoch-or-unix-time"></a>Dönem veya Unix saatinden zaman bulma

toLong (currentTimestamp ()-toTimestamp (' 1970-01-01 00:00:00.000 ', ' yyyy-AA-GG SS: DD: ss. SSS ')) * 1000L

## <a name="next-steps"></a>Sonraki adımlar

[Veri dönüştürme ifadeleri oluşturmaya başla](data-flow-expression-functions.md)
