---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: dc569050b78a5797808f2e2e000019ba516ba22e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97739251"
---
**Etkileşimli**
- Komut ve denetim senaryolarına yöneliktir.
- , Bir segment zaman aşımı değeri X ' i içerir.
- Tanınan bir zaman sonunda hizmet, bu istek KIMLIĞINDEN ses işlemeyi durduruyor ve sonra da sona erecektir. Bağlantı kapalı değil.
- Maksimum tanıma sınırı 20s 'dir.
- Invoke için tipik karbon çağrısı `RecognizeOnceAsync` .

**Konuşma**
- Daha uzun süre çalışan için tasarlanmıştır.
- , Bir segmentleme zaman aşımı değeri olan Y. (Y! = X)
- , Açmayı sonlandırmadan birden çok tamamlanmış şekilde işlem görür.
- , Çok fazla sessizlik için de sona erdir.
- Karbon, yeni bir istek KIMLIĞIYLE devam eder ve gerekirse sesi yeniden dener.
- Hizmet, 10 dakikadan kısa bir süre sonra, konuşma tanımayı zorla keser.
- Karbon, geri bağlantı kurar ve kabul edilmemiş sesi yeniden oynaacaktır.
- Karbon ile çağrıldı `StartContinuousRecognition` .

**Etme**
- Kullanıcıların konuşmayı konuşarak noktalama işaretleri belirlemesine izin verir.
- `EnableDictation` `SpeechConfig` TANıMAYı Başlatan API çağrısından bağımsız olarak nesne üzerinde belirtilerek bilgi olarak çağrılır.
- 1<sup>St</sup> taraf kümesi `speech.fragment` , 3. taraf ileti döndüren ara sonuçlara yönelik<sup></sup> iletileri döndürür `speech.hypothesis` .