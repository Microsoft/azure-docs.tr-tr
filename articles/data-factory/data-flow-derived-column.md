---
title: Eşleme veri akışında türetilmiş sütun dönüşümü
description: Eşleme veri akışı türetilmiş sütun dönüşümüyle birlikte Azure Data Factory ölçekli verileri nasıl dönüştürebileceğinizi öğrenin.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 2e90a8779322cf8967ca9a194c6cc760f7c8b8f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532090"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Eşleme veri akışında türetilmiş sütun dönüşümü

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Veri akışınızda yeni sütunlar oluşturmak veya mevcut alanları değiştirmek için türetilmiş sütun dönüşümünü kullanın.

## <a name="create-and-update-columns"></a>Sütunları oluştur ve Güncelleştir

Türetilmiş bir sütun oluştururken yeni bir sütun oluşturabilir veya var olan bir sütunu güncelleştirebilirsiniz. **Sütun** metin kutusuna oluşturmakta olduğunuz sütunu girin. Şemanızda varolan bir sütunu geçersiz kılmak için, sütun açılan listesini kullanabilirsiniz. Türetilmiş sütunun ifadesini oluşturmak için **Ifade girin** metin kutusuna tıklayın. Mantığınızı oluşturmak için ifadenizi yazmaya başlayabilir veya ifade Oluşturucusu 'nu açabilirsiniz.

![Türetilmiş sütun ayarları](media/data-flow/create-derive-column.png "Türetilmiş sütun ayarları")

Daha fazla türetilmiş sütun eklemek için sütun listesini yukarıya **Ekle** ' ye veya varolan bir türetilmiş sütunun yanındaki artı simgesine tıklayın. **Sütun Ekle** veya **sütun Ekle düzenlerini** seçin.

![Yeni türetilmiş sütun seçimi](media/data-flow/add-derived-column.png "Yeni türetilmiş sütun seçimi")

### <a name="column-patterns"></a>Sütun desenleri

Şemanızın açıkça tanımlanmadığı durumlarda veya bir sütun kümesini toplu olarak güncelleştirmek istiyorsanız, sütun patlayıcıları oluşturmak isteyeceksiniz. Sütun desenleri, sütun meta verilerini temel alan kuralları kullanarak sütunları eşleştirerek, eşleşen her sütun için türetilmiş sütunlar oluşturmanıza izin verir. Daha fazla bilgi için, türetilmiş sütun dönüşümünde [sütun desenleri oluşturmayı](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate) öğrenin.

![Sütun desenleri](media/data-flow/column-pattern-derive.png "Sütun desenleri")

## <a name="building-schemas-using-the-expression-builder"></a>İfade oluşturucuyu kullanarak şemalar oluşturma

Eşleme veri akışı [ifade Oluşturucusu](concepts-data-flow-expression-builder.md)kullanılırken, türetilmiş **sütunlar** bölümünde türetilmiş sütunlarınızı oluşturabilir, düzenleyebilir ve yönetebilirsiniz. Dönüşümde oluşturulan veya değiştirilen tüm sütunlar listelenir. Sütun adına tıklayarak, düzenlemekte olduğunuz sütunu veya kalıbı etkileşimli olarak seçin. Ek bir sütun eklemek için **Yeni oluştur** ' u seçin ve tek bir sütun veya bir kalıp eklemek isteyip istemediğinizi seçin.

![Yeni sütun oluştur](media/data-flow/derive-add-column.png "Yeni sütun oluştur")

Karmaşık sütunlarla çalışırken alt sütunlar oluşturabilirsiniz. Bunu yapmak için herhangi bir sütunun yanındaki artı simgesine tıklayın ve **alt sütun Ekle**' yi seçin. Veri akışındaki karmaşık türleri işleme hakkında daha fazla bilgi için bkz. [eşleme veri akışında JSON işleme](format-json.md#mapping-data-flow-properties).

![Alt sütun Ekle](media/data-flow/derive-add-subcolumn.png "Alt sütun Ekle")

Veri akışındaki karmaşık türleri işleme hakkında daha fazla bilgi için bkz. [eşleme veri akışında JSON işleme](format-json.md#mapping-data-flow-properties).

![Karmaşık sütun Ekle](media/data-flow/derive-complex-column.png "Sütun ekleme")

### <a name="locals"></a>Ayarlanmalıdır

Mantığı birden çok sütunda paylaşıyorsanız veya mantığınızı eklemek istiyorsanız, türetilmiş bir sütun dönüştürmesi içinde yerel bir oluşturabilirsiniz. Yerel, aşağıdaki dönüşüme aşağı yönde yayılmamış bir mantık kümesidir. Locals, ifade **öğelerine** gidip **Yereller** seçilerek ifade Oluşturucu içinde oluşturulabilir. **Yeni oluştur**' a tıklayarak yeni bir tane oluşturun.

![Yerel oluştur](media/data-flow/create-local.png "Yerel oluştur")

Yereller, işlevler, giriş şeması, parametreler ve diğer Yereller dahil olmak üzere türetilmiş bir sütuna herhangi bir ifade öğesine başvurabilir. Diğer Yereller 'e başvurulduğunda, sıralama, başvurulan yerel düzeyin geçerli bir "üzerinde" olması gerekir.

![Yerel 2 oluştur](media/data-flow/create-local-2.png "Yerel 2 oluştur")

Türetilmiş bir sütunda bir yerel öğeye başvurmak için, **ifade öğeleri** görünümünden yerel öğesine tıklayın veya bunun adının önünde bir iki nokta üst üste ile başvuru yapın. Örneğin, local1 adlı bir yerel, tarafından başvurulmalıdır `:local1` . Yerel bir tanımı düzenlemek için, ifade öğeleri görünümünde üzerine gelin ve kalem simgesine tıklayın.

![Yereller kullanma](media/data-flow/using-locals.png "Yereller kullanma")

## <a name="data-flow-script"></a>Veri akışı betiği

### <a name="syntax"></a>Syntax

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Örnek

Aşağıdaki örnek, `CleanData` gelen akış alan `MoviesYear` ve iki türetilmiş sütun oluşturan adlı bir türetilmiş sütundur. İlk türetilmiş sütun, sütunun `Rating` , derecelendirme değeri olan sütunu bir tamsayı türü olarak değiştirir. İkinci türetilmiş sütun, adı ' Filmler ' ile başlayan her sütunla eşleşen bir modeldir. Eşleşen her sütun için, `movie` ' movie_ ' önekli eşleşen sütunun değerine eşit olan bir sütun oluşturur. 

Data Factory UX 'de, bu dönüşüm aşağıdaki görüntüye benzer şekilde görünür:

![Örnek türet](media/data-flow/derive-script.png "Örnek türet")

Bu dönüşüm için veri akışı betiği aşağıdaki kod parçacığında verilmiştir:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Sonraki adımlar

- [Eşleme veri akışı ifade dili](data-flow-expression-functions.md)hakkında daha fazla bilgi edinin.
