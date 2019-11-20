---
title: Azure Data Factory eşleme veri akışı betiği
description: Data Factory veri akışı betiği arka plan koduna genel bakış
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/10/2019
ms.openlocfilehash: 4ff5a05fd40ef086c1f2332443ca03d5e872e9a8
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010154"
---
# <a name="data-flow-script-dfs"></a>Veri akışı betiği (DFS)

Veri akışı betiği (DFS), bir eşleme veri akışında bulunan dönüştürmeleri yürütmek için kullanılan bir kodlama diline benzer şekilde temel alınan meta verileridir. Her dönüşüm, işi düzgün şekilde çalıştırmak için gerekli bilgileri sağlayan bir dizi özellik tarafından temsil edilir. Betik, tarayıcı kullanıcı arabiriminin üst şeridinde "komut dosyası" düğmesine tıklanarak ADF 'den görünür ve düzenlenebilir.

![Betik düğmesi](media/data-flow/scriptbutton.png "Betik düğmesi")

Örneğin, bir kaynak dönüşümünde `allowSchemaDrift: true,`, hizmetin, şema projeksiyonda içerilmese bile veri akışındaki kaynak veri kümesinden tüm sütunları içermesini söyler.

## <a name="use-cases"></a>Uygulama alanları
DFS, Kullanıcı arabirimi tarafından otomatik olarak üretilir. Betiği görüntülemek ve özelleştirmek için betik düğmesine tıklayabilirsiniz. Ayrıca ADF Kullanıcı arabirimi dışında betikler oluşturabilir ve ardından bunu PowerShell cmdlet 'ine geçirebilirsiniz. Karmaşık veri akışları hata ayıklarken, akışlarınızın UI grafik gösterimini taramak yerine, arka plan kod dosyasını taramayı daha kolay bulabilirsiniz.

Örnek kullanım örnekleri aşağıda verilmiştir:
- Oldukça benzer, yani "damgalama" veri akışları gibi çok sayıda veri akışı program aracılığıyla.
- Kullanıcı arabiriminde yönetilmesi zor olan karmaşık ifadeler veya doğrulama sorunlarına neden olur.
- Hata ayıklama ve yürütme sırasında döndürülen çeşitli hataları daha iyi anlama.

PowerShell veya API ile kullanmak üzere bir veri akışı betiği oluşturduğunuzda, biçimlendirilen metni tek bir satıra daraltmanız gerekir. Sekmeleri ve newlines kaçış karakterleri olarak tutabilirsiniz. Ancak metin bir JSON özelliğinin içine sığacak şekilde biçimlendirilmelidir. Alt kısımdaki komut dosyası düzenleyici Kullanıcı arabiriminde, betiği sizin için tek bir satır olarak biçimlendirilecek bir düğme vardır.

![Kopyala düğmesi](media/data-flow/copybutton.png "Kopyala düğmesini")

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

Türeme dönüştürmesi eklemeye karar verirse, öncelikle `upperCaseTitle`adlı yeni bir büyük sütun eklemek için basit bir ifadeye sahip olan temel dönüşüm metnini oluşturmanız gerekir:
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

Şimdi, yeni dönüştürmenin ne kadar (Bu durumda `source1`) gelmesini istediğinizi tanımlayarak ve akışın adını yeni dönüştürmeye kopyalayarak gelen akışı yeniden yönlendirdik:
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

Son olarak, bu yeni dönüşümden sonra gelmesi istediğimiz dönüşümü tanımlıyoruz ve giriş akışını (Bu durumda `sink1`) yeni dönüştürtiğimiz çıkış akışı adıyla değiştirin:
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

## <a name="next-steps"></a>Sonraki adımlar

[Veri akışlarına genel bakış makalesini](concepts-data-flow-overview.md) kullanarak veri akışlarını inceleyin
