---
title: Dinamik sözlük-Translator Metin Çevirisi API'si
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure bilişsel hizmetler Translator Metin Çevirisi API'si 'ın dinamik Sözlük özelliğinin nasıl kullanılacağı açıklanmaktadır.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 5f007660b98a22ff9ffad072991a39cf59518b6a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837348"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Dinamik sözlük kullanma

Bir sözcüğe veya ifadeye uygulamak istediğiniz çeviriyi zaten biliyorsanız istek içinde biçimlendirme olarak sağlayabilirsiniz. Dinamik sözlük, yalnızca uygun adlar ve ürün adları gibi bileşik adlarla güvende olur.

**Sözdizimi**

< mstrans: Sözlük çevirisi = "deyimin çevirisi" > tümcecik </mstrans: sözlük >

**Gereklilik**

* `From` ve `To` dilleri farklı olmalıdır. 
* Otomatik Algıla özelliğini kullanmak yerine API çeviri isteğinize `From` parametresini eklemeniz gerekir. 

**Örnek: en-de:**

Kaynak girişi: sözcük < mstrans: Sözlük çevirisi =\"wordomatik\"> sözcük veya tümcecik </mstrans: sözlük > bir sözlük girişi.

Hedef çıktı: das "wordomatik" ist Ein Wörterbucheintrag.

Bu özellik HTML modu olmadan ve ile aynı şekilde çalışmaktadır.

Özelliğin gelişigüzel kullanılması gerekir. Çeviriyi özelleştirmenin uygun ve en iyi yolu özel çeviriciyi kullanmaktır. Özel çevirici, bağlam ve istatistiksel olasılıkların tam kullanımını sağlar. Çalışma veya tümceciğe bağlam içinde görüntülenen eğitim verileri varsa veya bu verilere sahipseniz, daha iyi sonuçlar elde edersiniz. [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)adresinden özel çevirici hakkında daha fazla bilgi edinebilirsiniz.
