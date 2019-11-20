---
title: Sözlük nedir? -Özel çevirici
titleSuffix: Azure Cognitive Services
description: Sözlük, Microsoft Translator 'ın aynı şekilde çevirisini her zaman çevirebilmeniz için bir tümcecik veya tümce (ve bunların çevirileri) listesini belirten hizalanmış bir belgedir. Sözlüklerde aynı zamanda ışlar veya terim esaları da denir.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 36b449c4c4ca30eb658c9519ce8e870a4f1fab32
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970736"
---
# <a name="what-is-a-dictionary"></a>Sözlük nedir?

Sözlük, deyimlerin veya Tümcelerin bir listesini ve bunlara karşılık gelen çevirileri belirten, hizalanmış bir belge çiftidir. Microsoft Translator 'ın, sözlükte sağladığınız çeviriyi kullanarak her zaman kaynak tümceciği veya cümle örneklerini çevirmesini istediğinizde, öğreticinizdeki bir sözlüğü kullanın. Sözlüklerde bazen ışlar veya terim tabanı denir. Sözlüğü, listenizin tüm terimleri için "Kopyala ve Değiştir" zorlaması olarak düşünebilirsiniz. Ayrıca, Microsoft özel çevirmen hizmeti, çevirisi kalitesini artırmak için kendi genel amaçlı sözlüklerini oluşturur ve kullanır. Bununla birlikte, bir müşteri tarafından sağlanmış sözlük bu şekilde görünür ve öncelikle kelimeleri veya cümleleri aramak için aranır.

Sözlükler yalnızca kendi arkasındaki tam olarak desteklenen bir Microsoft genel sinir Network modeline sahip olan dil çiftlerindeki projeler için geçerlidir. [Dillerin tüm listesini görüntüleyin](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Tümcecik sözlüğü
Modelinize eğitim altına bir tümcecik sözlüğü eklediğinizde, listelenen herhangi bir sözcük veya tümcecik, belirttiğiniz şekilde çevrilir. Cümlenin geri kalanı her zamanki gibi çevrilir. Sözlükteki kaynak ve hedef dosyada aynı çevrilmemiş tümceciği sağlayarak çevrilmemesi gereken tümcecikleri belirtmek için bir tümcecik sözlüğü kullanabilirsiniz.

## <a name="sentence-dictionary"></a>Tümce sözlüğü
Tümce sözlüğü, kaynak tümce için tam bir hedef çevirisi belirtmenize olanak tanır. Bir cümle sözlüğü eşleşmesi gerçekleşmesi için, gönderilen tümcenin tamamının kaynak sözlük girdisiyle eşleşmesi gerekir.  Cümlenin yalnızca bir kısmı eşleşiyorsa giriş eşleşmez.  Bir eşleşme algılandığında, tümce sözlüğünün hedef girdisi döndürülür.

## <a name="dictionary-only-trainings"></a>Sözlük-yalnızca insanlar
Yalnızca Sözlük verilerini kullanarak bir modeli eğitebilirsiniz. Bunu yapmak için, dahil etmek istediğiniz Sözlük belgesi (veya birden fazla Sözlük belgesi) seçin ve model oluştur ' a dokunun. Bu yalnızca bir sözlük eğitimi olduğundan, en az sayıda eğitim cümleleri gerekli değildir. Modelinize, genellikle standart bir eğitimin daha hızlı bir şekilde eğitim tamamlanmıştır.  Elde edilen modeller, eklediğiniz sözlüklerin eklenmesiyle çeviri için Microsoft taban çizgisi modellerini kullanacaktır.  Test raporu alacaksınız.

>[!Note]
>Özel çevirici, sözlük dosyalarını tümce halinde hizalamaz, bu nedenle sözlük belgelerinizde eşit sayıda kaynak ve hedef tümcecik/cümle olması ve tam olarak hizalanmaları önemlidir.

## <a name="recommendations"></a>Öneriler

- Sözlükler eğitim verilerini kullanarak bir modeli eğitmek için bir alternatif değildir. Bunların oluşmaması ve sistemin eğitim verilerinden öğrenmelerine izin vermek önerilir. Ancak, cümleler veya bileşik isimler olduğu gibi işlenmelidir, bir sözlük kullanın.
- Tümcecik sözlüğü gelişigüzel kullanılmalıdır. Bu nedenle, tümce içindeki bir ifade değiştirildiğinde, tümcenin geri kalanını çevirmek için bu tümce içindeki bağlamın kaybedilmesi veya sınırlı olduğunu unutmayın. Sonuç olarak, tümce içindeki ifade veya sözcük sağlanan sözlüğe göre çevirirken, tümcenin genel çeviri kalitesi genellikle zarar görür.
- Tümcecik sözlüğü ürün adları ("Microsoft SQL Server"), uygun adlar ("şehir Hamburg") veya ürünün özellikleri ("Pivot tablosu") gibi bileşik adlarla iyi bir şekilde çalışacaktır. Bunlar genellikle kaynakta veya hedef dilde oldukça büyük olduklarından, fiiller veya sıfatlar için eşit bir şekilde çalışmaz. En iyi uygulamalar, tümcecik sözlüğü girişlerinin her şeyi, ancak bileşik isimleri önlemeyi önler.
- Tümcecik sözlüğü kullanılırken, büyük harfler ve noktalama önemli. Sözlük girişleri yalnızca kaynak sözlük dosyasında belirtilen büyük/küçük harfleri ve noktalama işaretlerini kullanan giriş cümlesinde sözcüklerle ve tümceciklerden eşleşir. Ayrıca Çeviriler, hedef sözlük dosyasında belirtilen büyük/küçük harfleri ve noktalama işaretlerini yansıtır. Örneğin, kaynak dosyada "US" ve "EE" belirten bir tümcecik sözlüğü kullanan, Ingilizce bir Ispanyolca sisteme eğitim yaptıysanız. UU. " hedef dosyada. "ABD" (büyük değil) sözcüğünü içeren bir tümcenin çevirisini istediğinizde, bu sözlük eşleşmez. Ancak "ABD" (büyük harfli) sözcüğünü içeren bir tümcenin çevirisini istemeniz durumunda sözlük eşleşir ve çeviri "EE" içerir. UU. " Çevirinin büyük küçük harf ve noktalama işaretlerinin, sözlük hedef dosyasında belirtilenden farklı olabileceğini ve kaynaktaki büyük harfle ve noktalama işaretlerinden farklı olabileceğini unutmayın. Hedef dilin kurallarını izler.
- Bir sözcük, sözlük dosyasında birden çok kez görünürse, sistem her zaman belirtilen son girişi kullanır. Bu nedenle, sözlüğünüz aynı kelimenin birden fazla çevirisi içermemelidir.

## <a name="next-steps"></a>Sonraki adımlar

- [Belge biçimlerinde yönergeler](document-formats-naming-convention.md)hakkında bilgi edinin.
