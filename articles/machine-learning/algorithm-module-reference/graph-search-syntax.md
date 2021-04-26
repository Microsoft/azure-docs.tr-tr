---
title: Graph arama sorgusu söz dizimi
titleSuffix: Azure Machine Learning
description: İşlem hattı grafiğinde düğümleri aramak için Azure Machine Learning tasarımcısında arama sorgusu sözdizimini nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/24/2021
ms.openlocfilehash: 74cf0b897529e8bb198b6f82a57e187662a4a285
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259239"
---
# <a name="graph-search-query-syntax"></a>Graph arama sorgusu söz dizimi

Bu makalede, Azure Machine Learning grafik arama işlevi hakkında bilgi edineceksiniz. 

Graph Search, hata ayıklarken veya bir işlem hattı oluştururken bir düğümde hızlı bir şekilde gezinmenize olanak tanır. Arama yapmak için araç çubuğundaki giriş kutusuna ya da sol paneldeki ara sekmesine anahtar sözcüğünü veya sorguyu yazabilirsiniz. Tüm eşleşen sonuçlar tuvalde sarı renkle vurgulanır ve sol panelde bir sonuç seçerseniz, tuvaldeki düğüm kırmızı renkle vurgulanır.

![Örnek grafik arama deneyimini gösteren ekran görüntüsü](media/search/graph-search-0322.png)

Graph Search, düğüm adı ve açıklamalarında tam metin anahtar sözcük aramasını destekler. RunStatus, Duration, computeTarget gibi düğüm özelliği üzerine de filtre uygulayabilirsiniz. Anahtar sözcük arama, Lucene sorgusunu temel alır. Tüm arama sorgusu şöyle görünür:  

**[[Lucene sorgu] | [filtre sorgusu]]** 

Her iki Lucene sorgu veya filtre sorgusu kullanabilirsiniz. Her ikisini de kullanmak için **|** ayırıcıyı kullanın. Filtre sorgusunun sözdizimi, Lucene sorgusundan daha katı. Bu nedenle, müşteri girişi her ikisi olarak ayrıştırılamıyorsa, filtre sorgusu uygulanır.

Örneğin, `data OR model | compute in {cpucluster}` Bu, ad veya açıklamanın içerdiği düğümleri `data` ve işlem için cpucluster olduğunu gösteren düğümlerde arama yapmak içindir `model` .
 

## <a name="lucene-query"></a>Lucene sorgusu

Graph Search, "ad" ve "Açıklama" düğümünde tam metin arama söz dizimi olarak Lucene Simple sorgusunu kullanır. Aşağıdaki Lucene işleçleri desteklenir:

 
- VE/VEYA
- Joker karakterle eşleşen **?** ve **\*** işleçleri.

### <a name="examples"></a>Örnekler

- Basit arama: `JSON Data`

- VE/VEYA: `JSON AND Validation`

- Birden çok ve/veya: `(JSON AND Validation) OR (TSV AND Training)`

 
- Joker karakter eşleştirme: 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> Bir Lucene sorgusunu "*" karakteriyle başlatamazsınız.

##  <a name="filter-query"></a>Filtre sorgusu

 
Filtre sorguları aşağıdaki kalıbı kullanır:
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
Aşağıdaki düğüm özelliklerini anahtar olarak kullanabilirsiniz:

- runStatus
- işlem
- süre
- pencereleri
- publish
- etiketler

Ve aşağıdaki işleçleri kullanın:

- Daha büyük veya eşit: `>=`
- Daha az veya eşit: `<=`
- İlerisi `>`
- Büyüktür `<`
- Sıfıra `==`
- Bulunduramaz `=`
- Not qual: `!=`
- 'Ndaki `in`

 
 

### <a name="example"></a>Örnek

- süre > 100;
- {Başarısız, NotStarted} içinde durum
- {GPU-küme}; içinde işlem {Completed} içinde runStatus

## <a name="technical-notes"></a>Teknik notlar

- Birden çok filtre arasındaki ilişki "ve"
- Seçilirse `>=,  >,  <, or  <=` , değerler otomatik olarak sayı türüne dönüştürülür. Aksi takdirde, dize türleri karşılaştırma için kullanılır.
- Tüm dize türü değerleri için, karşılaştırma sırasında büyük/küçük harfe duyarlıdır.
- "In" işleci bir koleksiyonu değer olarak bekliyor, koleksiyon sözdizimi `{name1, name2, name3}`
- Anahtar sözcükler arasında boşluk yok sayılacak