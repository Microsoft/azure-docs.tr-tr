---
title: İçerik çevirisini önleme-Translator Metin Çevirisi API'si
titleSuffix: Azure Cognitive Services
description: Translator Metin Çevirisi API'si içerik çevirisini önleyin. Translator Metin Çevirisi API'si, içeriği çevrilemeyecek şekilde etiketleyebilmenizi sağlar.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f3bf784898f7f51beea890d8d2a8401af1403fbc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888126"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Translator Metin Çevirisi API'si içerik çevirisini önleme

Translator Metin Çevirisi API'si, içeriği çevrilemeyecek şekilde etiketleyebilmenizi sağlar. Örneğin, kod, marka adı veya yerelleştirmeden anlamlı olmayan bir sözcük/tümcecik etiketlemek isteyebilirsiniz.

## <a name="methods-for-preventing-translation"></a>Çeviriyi önlemek için Yöntemler
1. Twitter etiketine kaçış @somethingtopassthrough veya #somethingtopassthrough. Çeviri sonrasında kaçış işaretini kaldır.

2. İçeriğinizi `notranslate`etiketleyin.

   Örnek:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. Belirli bir çeviri yapmak için [dinamik sözlüğü](dynamic-dictionary.md) kullanın.

4. Dizeyi çeviri için Translator Metin Çevirisi API'si iletmeyin.

5. Özel çevirici: bir tümceciği %100 olasılığa sahip bir ifadenin çevirisi yapmak için [özel çevirmende bir sözlük](custom-translator/what-is-dictionary.md) kullanın.


## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Translator API çağrısındaki çeviriyi önleyin](reference/v3-0-translate.md)
