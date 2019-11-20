---
title: 'Ön işlem metni: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Metni temizlemek ve basitleştirmek için Azure Machine Learning 'de ön Işlem metin modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 9bf81b4e4c305c864592c32a8737d1a5eeb18651
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497674"
---
# <a name="preprocess-text"></a>Metni Ön İşleme

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Metni temizlemek ve basitleştirmek için **ön Işlem metin** modülünü kullanın. Bu ortak metin işleme işlemlerini destekler:

* Durma sözcüklerini kaldırma
* Belirli hedef dizeleri aramak ve değiştirmek için normal ifadeleri kullanma
* Birden çok ilgili sözcüğü tek bir kurallı forma dönüştüren limon
* Örnek olay normalleştirme
* Sayılar, özel karakterler ve "aaaa" gibi yinelenen karakterlerin dizileri gibi belirli karakter sınıflarının kaldırılması
* E-postaların ve URL 'Lerin tanımlanması ve kaldırılması

**Preprocess metin** modülü şu anda yalnızca İngilizce 'yi destekliyor.

## <a name="configure-text-preprocessing"></a>Metin ön Işleme yapılandırma  

1.  Azure Machine Learning, **işlem hattınızı ön Işle metin** modülünü ekleyin. Bu modülü **metin analizi**altına bulabilirsiniz.

1. Metin içeren en az bir sütunu olan bir veri kümesini bağlayın.

1. **Dil** açılan listesinden dili seçin.

1. **Temizleyen metin sütunu**: önceden işlemek istediğiniz sütunu seçin.

1. **Durdurma sözcüklerini kaldır**: metin sütununa önceden tanımlanmış bir durma sözcüğü listesi uygulamak istiyorsanız bu seçeneği belirleyin. 

    Stopword listeleri dile bağlı ve özelleştirilebilir.

1. Su **kullanımı: sözcüklerin**kurallı biçimde temsil edilebilmesi istiyorsanız bu seçeneği belirleyin. Bu seçenek, başka türlü benzer metin belirteçlerinin benzersiz oluşum sayısını azaltmak için yararlıdır.

    Katileştirme işlemi yüksek oranda dile bağımlıdır.

1. **Cümleleri Algıla**: modülün analiz gerçekleştirirken bir cümle sınır işareti eklemesini istiyorsanız bu seçeneği belirleyin.

    Bu modül, cümle sonlandırıcıyı temsil etmek için `|||` üç kanal karakter serisi kullanır.

1. Normal ifadeler kullanarak isteğe bağlı bulma ve değiştirme işlemleri gerçekleştirin.

    * **Özel normal ifade**: aramakta olduğunuz metni tanımlayın.
    * **Özel değiştirme dizesi**: tek bir değiştirme değeri tanımlayın.

1. **Büyük/küçük harf karakterlerini Normalleştir**: ASCII büyük harfleri küçük formlarına dönüştürmek istiyorsanız bu seçeneği belirleyin.

    Karakterler normalleştirilmemişse, büyük ve küçük harflerle aynı sözcük iki farklı sözcük olarak değerlendirilir.

1. Ayrıca, işlenen çıkış metninde aşağıdaki karakter veya karakter dizileri türlerini de kaldırabilirsiniz:

    * **Sayıları kaldır**: belirtilen dile ait tüm sayısal karakterleri kaldırmak için bu seçeneği belirleyin. Kimlik numaraları, etki alanına bağımlıdır ve dile bağımlıdır. Sayısal karakterler bilinen bir sözcüğün integral parçasıysa, sayı kaldırılmayabilir.
    
    * **Özel karakterleri kaldır**: alfasayısal olmayan özel karakterleri kaldırmak için bu seçeneği kullanın.
    
    * **Yinelenen karakterleri kaldır**: iki kez tekrarlanacak herhangi bir dizilerden fazla karakter kaldırmak için bu seçeneği belirleyin. Örneğin, "aaaaa" gibi bir sıra "AA" olarak düşürülür.
    
    * **E-posta adreslerini kaldır**: `<string>@<string>`biçim sırasını kaldırmak için bu seçeneği belirleyin.  
    * **URL 'Leri kaldır**: aşağıdaki URL öneklerini içeren herhangi bir sırayı kaldırmak için bu seçeneği belirleyin: `http`, `https`, `ftp`, `www`
    
1. **Fiil kodlamalarını Genişlet**: Bu seçenek yalnızca fiil kodlamalarını kullanan diller için geçerlidir; Şu anda yalnızca Ingilizce. 

    Örneğin, bu seçeneği belirleyerek "hiçbir zaman" *kalmazın* "olmaz" ifadesini " *Not*edin" olarak değiştirebilirsiniz.

1. **Ters eğik çizgileri eğik çizgi Ile Normalleştir**: tüm `\\` örneklerini `/`eşlemek için bu seçeneği belirleyin.

1. **Belirteçleri özel karakterlere Böl**: `&`, `-`vb. gibi karakterlerden sözcükleri kesmek istiyorsanız bu seçeneği belirleyin. Bu seçenek ayrıca, iki kez yinelendiğinde özel karakterleri de azaltabilir. 

    Örneğin, `MS---WORD` dize üç belirtece ayrılabilir, `MS`, `-`ve `WORD`.

1. İşlem hattını çalıştırma.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 