---
title: Sık sorulan sorular-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Azure bilişsel hizmetler 'deki Görüntü İşleme API'si hakkında sık sorulan soruların yanıtlarını alın.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564595"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Görüntü İşleme API'si sık sorulan sorular

> [!TIP]
> Bu SSS 'de sorularınıza yanıt bulamazsanız, [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) 'de görüntü işleme API'si Community 'yi sormayı deneyin veya [UserVoice üzerinde yardım ve desteğe](https://cognitive.uservoice.com/) başvurun

---

**Soru**: *Özel etiketleri kullanmak için Görüntü İşleme API'si eğitme yapabilir miyim?  Örneğin, kedi breeds resimlerinde AI ' eğitme ' ' i ' eğitmek ' ve ardından bir AI isteğinde iyisi değerini almak istiyorum.*

**Cevap**: Bu işlev şu anda kullanılamıyor. Ancak, mühendislerimiz bu işlevselliği Görüntü İşleme getirmek için çalışmaktadır.

---

**Soru**: *Internet bağlantısı olmadan yerel olarak Görüntü İşleme kullanılabilir mi?*

**Cevap**: Şu anda şirket içi veya yerel bir çözüm sunmuyoruz.

---

**Soru**: *Görüntü İşleme, lisans kalıplarını okumak için kullanılabilir mi?*

**Cevap**: Vision API 'SI, OCR ile iyi metin algılama olanağı sunar, ancak şu anda lisans levhaları için en iyi duruma getirilmiştir. Hizmetlerimizi sürekli olarak iyileştirmeye çalışıyoruz ve özellik istekleri listemize otomatik lisans levha tanıma için OCR ekledik.

---

**Soru**: *El yazısı tanıma için hangi tür yazma yüzeyleri destekleniyor?*

**Cevap**: Teknoloji, beyaz tahta, Teknik İnceleme ve sarı yapışkan notlar gibi farklı türlerde yüzeyler ile birlikte kullanılabilir.

---

**Soru**: *El yazısı tanıma işlemi ne kadar sürer?*

**Cevap**: Aldığı sürenin miktarı metnin uzunluğuna bağlıdır. Uzun metinler için birkaç saniye sürebilir. Bu nedenle, El Ile yazılmış metin işlemi tamamlandıktan sonra, el yazısı metin Işlem sonucunu Al işlemini kullanarak sonuçları alabilmek için beklemeniz gerekebilir.

---

**Soru**: *El yazısı tanıma teknolojisi, bir satır ortasında bir giriş işareti kullanılarak eklenen metni nasıl işler?*

**Cevap**: Bu tür metinler, el yazısı tanıma işlemi tarafından ayrı bir satır olarak döndürülür.

---

**Soru**: *El yazısı tanıma teknolojisi, çapraz sözcükleri veya satırları nasıl işler?*

**Cevap**: Sözcüklerin tanınabilmesi için birden çok satır varsa, el yazısı tanıma işlemi bunları kullanıma almaz. Ancak, sözcükler tek bir çizgi kullanılarak ele alınalıyorsa, bu geçiş gürültü olarak kabul edilir ve kelimeler yine de el yazısı tanıma işlemi tarafından alınır.

---

**Soru**: *El yazısı tanıma teknolojisi için hangi metin yönleri desteklenir?*

**Cevap**: En fazla 30 derece 40 dereceye kadar olan metin yönelimli metinler, el yazısı tanıma işlemi tarafından alınmayabilir.

---
