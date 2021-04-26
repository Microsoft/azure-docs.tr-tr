---
title: 'Ön işlem metni: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Metni temizlemek ve basitleştirmek için Azure Machine Learning tasarımcısında önceden Işlem metin modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/16/2020
ms.openlocfilehash: 366b30df677a5b74bc7d70e1aea60e05b4df0152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94659316"
---
# <a name="preprocess-text"></a>Metni Ön İşleme

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Metni temizlemek ve basitleştirmek için **ön Işlem metin** modülünü kullanın. Bu ortak metin işleme işlemlerini destekler:

* Durma sözcüklerini kaldırma
* Belirli hedef dizeleri aramak ve değiştirmek için normal ifadeleri kullanma
* Birden çok ilgili sözcüğü tek bir kurallı forma dönüştüren limon
* Örnek olay normalleştirme
* Sayılar, özel karakterler ve "aaaa" gibi yinelenen karakterlerin dizileri gibi belirli karakter sınıflarının kaldırılması
* E-postaların ve URL 'Lerin tanımlanması ve kaldırılması

**Preprocess metin** modülü şu anda yalnızca İngilizce 'yi destekliyor.

## <a name="configure-text-preprocessing"></a>Metin ön Işleme yapılandırma  

1.  Azure Machine Learning, **işlem hattınızı ön Işle metin** modülünü ekleyin. Bu modülü **metin analizi** altına bulabilirsiniz.

1. Metin içeren en az bir sütunu olan bir veri kümesini bağlayın.

1. **Dil** açılan listesinden dili seçin.

1. **Temizleyen metin sütunu**: önceden işlemek istediğiniz sütunu seçin.

1. **Durdurma sözcüklerini kaldır**: metin sütununa önceden tanımlanmış bir durma sözcüğü listesi uygulamak istiyorsanız bu seçeneği belirleyin. 

    Stopword listeleri dile bağlı ve özelleştirilebilir.

1. Su **kullanımı: sözcüklerin** kurallı biçimde temsil edilebilmesi istiyorsanız bu seçeneği belirleyin. Bu seçenek, başka türlü benzer metin belirteçlerinin benzersiz oluşum sayısını azaltmak için yararlıdır.

    Katileştirme işlemi yüksek oranda dile bağımlıdır.

1. **Cümleleri Algıla**: modülün analiz gerçekleştirirken bir cümle sınır işareti eklemesini istiyorsanız bu seçeneği belirleyin.

    Bu modül `|||` , cümle sonlandırıcıyı temsil etmek için üç kanal karakter serisi kullanır.

1. Normal ifadeler kullanarak isteğe bağlı bulma ve değiştirme işlemleri gerçekleştirin. Normal ifade ilk başta, diğer yerleşik seçeneklerin önünde işlenir.

    * **Özel normal ifade**: aramakta olduğunuz metni tanımlayın.
    * **Özel değiştirme dizesi**: tek bir değiştirme değeri tanımlayın.

1. **Büyük/küçük harf karakterlerini Normalleştir**: ASCII büyük harfleri küçük formlarına dönüştürmek istiyorsanız bu seçeneği belirleyin.

    Karakterler normalleştirilmemişse, büyük ve küçük harflerle aynı sözcük iki farklı sözcük olarak değerlendirilir.

1. Ayrıca, işlenen çıkış metninde aşağıdaki karakter veya karakter dizileri türlerini de kaldırabilirsiniz:

    * **Sayıları kaldır**: belirtilen dile ait tüm sayısal karakterleri kaldırmak için bu seçeneği belirleyin. Kimlik numaraları, etki alanına bağımlıdır ve dile bağımlıdır. Sayısal karakterler bilinen bir sözcüğün integral parçasıysa, sayı kaldırılmayabilir. [Teknik notlarda](#technical-notes)daha fazla bilgi edinin.
    
    * **Özel karakterleri kaldır**: alfasayısal olmayan özel karakterleri kaldırmak için bu seçeneği kullanın.
    
    * **Yinelenen karakterleri kaldır**: iki kez tekrarlanacak herhangi bir dizilerden fazla karakter kaldırmak için bu seçeneği belirleyin. Örneğin, "aaaaa" gibi bir sıra "AA" olarak düşürülür.
    
    * **E-posta adreslerini kaldır**: biçimin herhangi bir dizisini kaldırmak için bu seçeneği belirleyin `<string>@<string>` .  
    * **URL 'Leri kaldır**: aşağıdaki URL öneklerini içeren herhangi bir sırayı kaldırmak için bu seçeneği belirleyin: `http` , `https` , `ftp` , `www`
    
1. **Fiil kodlamalarını Genişlet**: Bu seçenek yalnızca fiil kodlamalarını kullanan diller için geçerlidir; Şu anda yalnızca Ingilizce. 

    Örneğin, bu seçeneği belirleyerek "hiçbir zaman" *kalmazın* "olmaz" ifadesini " *Not* edin" olarak değiştirebilirsiniz.

1. **Ters eğik çizgileri eğik çizgiyle Normalleştir**: tüm örneklerini öğesine eşlemek için bu seçeneği belirleyin `\\` `/` .

1. **Belirteçleri özel karakterlere Böl**:,, vb. karakterler üzerinde sözcükler kesmek istiyorsanız bu seçeneği belirleyin `&` `-` . Bu seçenek ayrıca, iki kez yinelendiğinde özel karakterleri de azaltabilir. 

    Örneğin, dize `MS---WORD` üç belirtece ayrılmıştır,, `MS` `-` , ve `WORD` .

1. İşlem hattını gönderme.

## <a name="technical-notes"></a>Teknik notlar

Studio 'da (klasik) ve tasarımcıda **önceden işleme metin** modülü farklı dil modelleri kullanır. Tasarımcı, [Spipb](https://spacy.io/models/en)'den bir çoklu görev CNN eğitilen modeli kullanır. Farklı modeller, farklı sonuçlara yol gösteren farklı Simgeleştirici ve konuşma bölümü etiketi sağlar.

Aşağıda bazı örnekler verilmiştir:

| Yapılandırma | Çıkış sonucu |
| --- | --- |
|Tüm seçenekler seçiliyken </br> Açıklama: </br> ' WC-3 3Test 4Test ' içinde ' 3Test ' gibi durumlarda, tasarımcı bu bağlamda ' 3Test ' tüm sözcüğü kaldırır, bu bağlamda konuşma bölümü etiketi ' 3Test ' ' i ' 3Test ' olarak belirtir, bu da modülün bir parçası olarak bu belirteci kaldırır.| :::image type="content" source="./media/module/preprocess-text-all-options-selected.png" alt-text="Tüm seçenekler seçiliyken" border="True"::: |
|Yalnızca `Removing number` Seçili </br> Açıklama: </br> ' 3Test ', ' 4-EC ' gibi durumlarda, tasarımcı Simgeleştirici dovağı bu durumları bölmez ve bunları tüm belirteçler olarak değerlendirir. Bu nedenle, bu sözcüklerdeki sayıları kaldırmaz.| :::image type="content" source="./media/module/preprocess-text-removing-numbers-selected.png" alt-text="Yalnızca ' kaldırma numarası ' seçiliyken" border="True"::: |

Normal ifadeyi, özelleştirilmiş sonuçları çıkarmak için de kullanabilirsiniz:

| Yapılandırma | Çıkış sonucu |
| --- | --- |
|Tüm seçenekler seçiliyken </br> Özel normal ifade: `(\s+)*(-|\d+)(\s+)*` </br> Özel değiştirme dizesi: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-all-options-selected.png" alt-text="Tüm seçenekler seçiliyken ve normal ifade" border="True"::: |
|Yalnızca `Removing number` Seçili </br> Özel normal ifade: `(\s+)*(-|\d+)(\s+)*` </br> Özel değiştirme dizesi: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-removing-numbers-selected.png" alt-text="Seçili sayıları ve normal ifadeyi kaldırma ile" border="True"::: |


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 