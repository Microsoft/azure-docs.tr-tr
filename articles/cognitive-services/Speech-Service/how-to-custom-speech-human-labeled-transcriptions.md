---
title: İnsan etiketli döküm yönergeleri-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Sözcüklerin ne zaman silindiği ya da yanlış kullanıldığı gibi konuşma tanıma doğruluğunu artırmak için, insan etiketli döküm 'yi ses verilerinize birlikte kullanabilirsiniz. İnsan etiketli döküm, bir ses dosyasının sözcük-sözcük, tam olarak yazılı dökümlerdir.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 1eeb2e7ccf5c365fedd02a8de4c6b442dd3d5bc8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075807"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>İnsan etiketlendirmeleri oluşturma

Tanıma doğruluğunu artırmak istiyorsanız, özellikle sözcüklerin silindiği veya yanlış kullanıldığı durumlarda oluşan sorunlar, insan etiketli dökümlerini ses verilerinize birlikte kullanmak isteyeceksiniz. İnsan etiketli döküm nedir? Bu çok kolay bir deyişle, bir ses dosyasının sözcük sözcük, tam olarak yazılı olduğu bir işlemdir.

Tanımayı geliştirmek için büyük bir döküm verileri örneği gerekir, bu da 10 ila 1.000 saatlik Döküm verileri sağlamayı öneririz. Bu sayfada, yüksek kaliteli dökümler oluşturmanıza yardımcı olmak için tasarlanan yönergeleri inceleyeceğiz. Bu kılavuz, ABD Ingilizcesi, MANDARIN Çince ve Almanca bölümleri ile yerel ayar tarafından bölünmüştür.

## <a name="us-english-en-us"></a>ABD Ingilizcesi (en-US)

Ingilizce ses için insan etiketli dökümlerde yalnızca ASCII karakterler kullanılarak düz metin olarak sağlanmalıdır. Latin-1 veya Unicode noktalama karakterleri kullanmaktan kaçının. Bu karakterler genellikle bir sözcük işleme uygulamasından metin kopyalanırken veya Web sayfalarından veri koruma verilerinden yanlışlıkla eklenir. Bu karakterler varsa, bunları uygun ASCII değiştirme ile güncelleştirdiğinizden emin olun.

İşte birkaç örnek:

| Kaçınacak karakterler | Değiştirme | Notlar |
| ------------------- | ------------ | ----- |
| "Merhaba Dünya" | "Merhaba Dünya" | Açma ve kapatma tırnak işaretleri, uygun ASCII karakterleriyle değiştirilmiştir. |
| John 'un günü | John 'un günü | Kesme işareti uygun ASCII karakteriyle değiştirildi. |
| Her şey iyi değildi. harika! | iyi--Hayır, harika! | Em Dash iki kısa çizgi ile değiştirildi. |

### <a name="text-normalization-for-us-english"></a>ABD Ingilizcesi için metin normalleştirme

Metin normalleştirme, bir modeli eğitmek için kullanılan tutarlı bir biçime sözcüklerin dönüştürülmesine sahiptir. Bazı normalleştirme kuralları metne otomatik olarak uygulanır; ancak, insan etiketli döküm verilerini hazırlarken bu yönergelerin kullanılmasını öneririz:

- Sözcüklerdeki kısaltmaları yazın.
- Sözcüklerdeki standart olmayan sayısal dizeleri (muhasebe terimleri gibi) yazın.
- Alfabetik olmayan karakterler veya karışık alfasayısal karakterler aynı şekilde yerleştirilmelidir.
- Sözcüklerin düzenlenmemesi gereken kısaltmalar ("Radar", "lazer", "RAM" veya "NATO" gibi).
- Boşlukla ayrılmış olarak, her bir harfle ayrı harfler olarak uygulanan kısaltmalar yazın.

Aşağıda, bir dökümde gerçekleştirmeniz gereken normalleştirmenin birkaç örneği verilmiştir:

| Özgün metin               | Normalleştirme sonrası metin              |
| --------------------------- | ------------------------------------- |
| Dr. Bruce başlığı            | Doctor Bruce başlığı                   |
| James Bononu, 007             | James bonu, Çift Oh           |
| Ke $ ha                       | Kesha                                 |
| 2x4 ne kadar süre         | Ne kadar ikisi dört ile           |
| Toplantı 1-3pm 'den geçer | Toplantı bir ile üç PM arasında gider |
| Kan türü O +         | Kan türü O pozitif           |
| Su H20                | Su, H 2 O                        |
| Van Halen tarafından OU812 Yürüt     | Van Halen tarafından e U 8 1 2 çal           |
| BOM ile UTF-8              | BOM ile U T F 8                      |

Aşağıdaki normalleştirme kuralları, dökümlere otomatik olarak uygulanır:

- Küçük harfler kullanın.
- Sözcüklerin içinde kesme işareti dışında tüm noktalama işaretlerini kaldırın.
- Sayıları, dolar tutarları gibi sözcükler/konuşulan bir biçimde genişletin.

Bu, otomatik olarak dökümde gerçekleştirilen normalleştirme örnekleri aşağıda verilmiştir:

| Özgün metin                          | Normalleştirme sonrası metin          |
| -------------------------------------- | --------------------------------- |
| "Kutsal Cow!" Batman diyor.               | Kutsal inek diyor Batman              |
| "Ne?" Batman 'ın sidekick, deneme olduğunu diyor. | Batman 'ın Sidekick 'in bir kez deneme olduğu söylenebilir |
| Go al-em!                            | Git Get em                         |
| Ben Double Jointed                     | Ben Double Jointed                |
| 104 ağaç Caddesi                         | 1 0 4 ağaç Caddesi            |
| 102,7 olarak ayarla                          | 1 0 2 noktası yedi olarak ayarlayın    |
| PI yaklaşık 3,14                       | Pi üç nokta 1 4  |
| BT maliyetleri \$3,14                        | BT maliyetleri 3 14           |

## <a name="mandarin-chinese-zh-cn"></a>Mandarin Çince (zh-CN)

Mandarin Çin seslerinin insan etiketli dökümlerinin, bir bayt sırası işaretleyicisi ile UTF-8 kodlu olması gerekir. Yarı genişlikte noktalama karakterleri kullanmaktan kaçının. Bu karakterler, verileri bir kelime işleme programında veya Web sayfalarından bir atık olarak hazırlarken, yanlışlıkla dahil edilebilir. Bu karakterler varsa, bunları uygun tam genişlikteki değiştirme ile güncelleştirdiğinizden emin olun.

İşte birkaç örnek:

| Kaçınacak karakterler | Değiştirme   | Notlar |
| ------------------- | -------------- | ----- |
| "你好" | "你好" | Açma ve kapatma tırnak işaretleri, uygun karakterlerle değiştirildi. |
| 需要什么帮助? | 需要什么帮助?| Soru işareti uygun karakterle değiştirildi. |

### <a name="text-normalization-for-mandarin-chinese"></a>Mandarin Çince için metin normalleştirme

Metin normalleştirme, bir modeli eğitmek için kullanılan tutarlı bir biçime sözcüklerin dönüştürülmesine sahiptir. Bazı normalleştirme kuralları metne otomatik olarak uygulanır; ancak, insan etiketli döküm verilerini hazırlarken bu yönergelerin kullanılmasını öneririz:

- Sözcüklerdeki kısaltmaları yazın.
- Sayısal dizeleri konuşulan biçimde yazın.

Aşağıda, bir dökümde gerçekleştirmeniz gereken normalleştirmenin birkaç örneği verilmiştir:

| Özgün metin | Normalleştirme sonrası metin |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

Aşağıdaki normalleştirme kuralları, dökümlere otomatik olarak uygulanır:

- Tüm noktalama işaretlerini kaldır
- Rakamları konuşulan biçime Genişlet
- Tam genişlikli harfleri yarı genişlik mektuplarına Dönüştür
- Tüm Ingilizce kelimeler için büyük harfler kullanma

Bu, otomatik olarak dökümde gerçekleştirilen normalleştirme örnekleri aşağıda verilmiştir:

| Özgün metin | Normalleştirme sonrası metin |
| ------------- | ------------------------ |
| 3,1415 | 三 点 一 四 一 五 |
| ¥3,5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 年 8 月 8 日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Almanca (de-DE) ve diğer diller

Almanya ses (ve Ingilizce olmayan veya Mandarin Çince dilleri) için insan etiketli döküm, bir bayt düzeni işaretleyicisi ile UTF-8 ile kodlanmış olmalıdır. Her ses dosyası için bir insan etiketli yazılı döküm sağlanmalıdır.

### <a name="text-normalization-for-german"></a>Almanca için metin normalleştirme

Metin normalleştirme, bir modeli eğitmek için kullanılan tutarlı bir biçime sözcüklerin dönüştürülmesine sahiptir. Bazı normalleştirme kuralları metne otomatik olarak uygulanır; ancak, insan etiketli döküm verilerini hazırlarken bu yönergelerin kullanılmasını öneririz:

- Ondalık noktaları "," olarak yazın ve "." değil.
- Zaman ayırıcıları ":" olarak yazın ve "." olarak yazın. (örneğin: 12:00 Uhr).
- "CA" gibi kısaltmalar. değiştirilmez. Tam konuşulan formu kullanmanızı öneririz.
- Dört ana matematik işleci (+,-, \*ve/) kaldırılır. "Plus", "", "" yanlış "ve" geteilt "yazılı formuyla değiştirmeniz önerilir.
- Karşılaştırma işleçleri kaldırılır (=, < ve >). Bunları "Gleich," "Kleiner ALS" ve "grösser ALS" ile değiştirmeniz önerilir.
- Yazılan biçimde 3/4 gibi kesirleri yazın (örneğin, 3/4 yerine "Drei Viertel").
- "€" Sembolünü "Euro" yazılı formuyla değiştirin.

Aşağıda, bir dökümde gerçekleştirmeniz gereken normalleştirmenin birkaç örneği verilmiştir:

| Özgün metin    | Kullanıcı normalleştirdikten sonra metin | Sistem normalleştirme sonrası metin       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12,23 Uhr | Es ist 12:23 Uhr              | es ist zwölf Uhr Drei und Zwanzig Uhr |
| {12,45}          | {12,45}                       | zwölf komma Vier Fünf                 |
| 2 + 3-4        | 2 artı 3 eksi 4              | zwei plus drei minus vier             |

Aşağıdaki normalleştirme kuralları, dökümlere otomatik olarak uygulanır:

- Tüm metinler için küçük harfler kullanın.
- Çeşitli tırnak işareti türleri ("test", "test", "test" ve «test») dahil tüm noktalama işaretlerini kaldırın.
- Bu kümeden özel karakterler içeren satırları at: ¢ ¤ ¥ ¦ § © ª ¬® ° ± ² μ × ÿ Ø ¬ ¬.
- Dolar veya Euro miktarları dahil olmak üzere, konuşulan rakamları genişletin.
- Yalnızca bir, o ve için kabul edin. Diğerleri "TH" ile değiştirilirler veya atılır.

Bu, otomatik olarak dökümde gerçekleştirilen normalleştirme örnekleri aşağıda verilmiştir:

| Özgün metin    | Normalleştirme sonrası metin |
| ---------------- | ------------------------ |
| Frankfurter halkası | Frankfurter halkası         |
| ¡ Eine Frage!     | Eine Frage               |
| WIR, haben       | WIR haben                |

## <a name="next-steps"></a>Sonraki Adımlar

- [Verilerinizi hazırlayın ve test edin](how-to-custom-speech-test-data.md)
- [Verilerinizi inceleyin](how-to-custom-speech-inspect-data.md)
- [Verilerinizi değerlendirin](how-to-custom-speech-evaluate-data.md)
- [Modelinize eğitme](how-to-custom-speech-train-model.md)
- [Modelinizi dağıtın](how-to-custom-speech-deploy-model.md)
