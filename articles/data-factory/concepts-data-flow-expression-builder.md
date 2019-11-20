---
title: Azure Data Factory eşleme veri akışı Ifade Oluşturucusu
description: Azure Data Factory eşleme veri akışları için Ifade Oluşturucusu
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 872c7ce6a0c39ab19165a5f16ea3e4f6ef8bd6a5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388042"
---
# <a name="mapping-data-flow-expression-builder"></a>Eşleme veri akışı Ifade Oluşturucusu



Azure Data Factory eşleme veri akışı ' nda, veri dönüştürme için ifadeler girebileceğiniz ifade kutuları bulacaksınız. Bu kutularda veri akışınızdan sütunları, alanları, değişkenleri, parametreleri, işlevleri kullanın. İfadeyi oluşturmak için, dönüştürme içindeki ifade metin kutusuna tıklanarak başlatılan Ifade oluşturucusunu kullanın. Ayrıca, dönüştürme için sütun seçerken bazen "hesaplanan sütun" seçeneklerini görürsünüz. Bu öğesine tıkladığınızda, Ifade oluşturucusunun başlatılmış olduğunu da görürsünüz.

![İfade Oluşturucusu](media/data-flow/xpb1.png "İfade Oluşturucusu")

Ifade Oluşturucu aracı varsayılan olarak metin düzenleyici seçeneğini belirler. Otomatik tamamlanma özelliği, sözdizimi denetimi ve vurgulaması ile tüm Azure Data Factory veri akışı nesne modelinden okur.

![İfade Oluşturucu otomatik tamamlamayı](media/data-flow/expb1.png "İfade Oluşturucu otomatik tamamlamayı")

## <a name="build-schemas-in-output-schema-pane"></a>Çıktı şeması bölmesinde şema oluşturma

![Karmaşık sütun Ekle](media/data-flow/complexcolumn.png "Sütun Ekle")

Sol taraftaki çıktı şeması bölmesinde, değiştirdiğiniz ve şemanıza eklemekte olduğunuz sütunları görürsünüz. Etkileşimli olarak basit ve karmaşık veri yapıları oluşturabilirsiniz. "Sütun Ekle" öğesini kullanarak ek alanlar ekleyin ve "alt sütun Ekle" öğesini kullanarak hiyerarşileri oluşturun.

![Alt sütun Ekle](media/data-flow/addsubcolumn.png "Alt sütun Ekle")

## <a name="data-preview-in-debug-mode"></a>Hata ayıklama modundaki veri önizlemesi

![İfade Oluşturucusu](media/data-flow/exp4b.png "İfade verileri önizlemesi")

Veri akışı ifadeleriniz üzerinde çalışırken, Azure Data Factory veri akışı tasarım yüzeyinden hata ayıklama modunda geçiş yapın, böylece oluşturmakta olduğunuz ifadeden veri sonuçlarının canlı devam eden önizlemesi etkinleştiriliyor. Deyimleriniz için gerçek zamanlı canlı hata ayıklama etkinleştirilir.

![Hata ayıklama modu](media/data-flow/debugbutton.png "Hata ayıklama düğmesi")

Deyimizin sonuçlarını gerçek zamanlı olarak kaynağınızın canlı bir örneğine göre güncelleştirmek için Yenile düğmesine tıklayın.

![İfade Oluşturucusu](media/data-flow/exp5.png "İfade verileri önizlemesi")

## <a name="comments"></a>Yorumlar

Tek satırlı ve çok satırlı açıklama söz dizimini kullanarak ifadelerinizi açıklama ekleyin:

![Açıklamalar](media/data-flow/comments.png "Yorumlar")

## <a name="regular-expressions"></a>Normal Ifadeler

Azure Data Factory veri akışı ifade dili, [burada tam başvuru belgeleri](https://aka.ms/dataflowexpressions), normal ifade söz dizimini içeren işlevleri sunar. Normal ifade işlevleri kullanılırken, Ifade Oluşturucusu ters eğik çizgiyi (\\) bir kaçış karakter dizisi olarak yorumlamaya çalışır. Normal ifadenizde ters eğik çizgi kullandığınızda, tüm Regex ' ı (\`) içine alın ya da çift ters eğik çizgi kullanın.

Tick kullanarak örnek

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

veya çift eğik çizgi kullanma

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Dizi dizinlerini adresleme

Dizi döndüren ifade işlevleri ile, dönüş dizisi nesnesinin içindeki belirli dizinleri adresleyen köşeli parantezleri [] kullanın. Dizi, tabanlıdır.

![İfade Oluşturucu dizisi](media/data-flow/expb2.png "İfade verileri önizlemesi")

## <a name="handling-names-with-special-characters"></a>Özel karakterlerle adları işleme

Özel karakterler veya boşluklar içeren sütun adlarınız varsa, adı küme ayraçları ile çevreleyin.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Sonraki adımlar

[Veri dönüştürme ifadeleri oluşturmaya başla](data-flow-expression-functions.md)
