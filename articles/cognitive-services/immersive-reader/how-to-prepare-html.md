---
title: HTML içeriğini modern okuyucu için hazırlama
titleSuffix: Azure Cognitive Services
description: HTML, JavaScript, Python, Android veya iOS kullanarak modern okuyucuyu nasıl başlatacağınızı öğrenin. Modern okuyucu, öğrenme farklılığı olan dil öğrenimi, gelişmekte olan okuyucular ve öğrenciler için okuma kavramasını geliştirmek üzere kanıtlanmış teknikler kullanır.
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 40aab9510eb90c405f92be49a3b4c0a3f756c872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620163"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>HTML içeriğini modern okuyucu için hazırlama

Bu makalede, HTML 'nizi nasıl yapılandıracağınıza ve tam ekran okuyucu tarafından kullanılabilmesi için içeriğin nasıl alınacağını gösterilmektedir.

## <a name="prepare-the-html-content"></a>HTML içeriğini hazırlama

İşlemek istediğiniz içeriği bir kapsayıcı öğesinin içindeki tam ekran okuyucusuna yerleştirin. Kapsayıcı öğesinin benzersiz olduğundan emin olun `id` . Modern okuyucu, temel HTML öğeleri için destek sağlar, daha fazla bilgi için [başvuruya](reference.md#html-support) bakın.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>JavaScript 'te HTML içeriğini al

`id`JavaScript KODUNUZDA HTML içeriğini almak için kapsayıcı öğesinin öğesini kullanın.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>HTML içeriğinizdeki tam ekran okuyucuyu başlatın

`ImmersiveReader.launchAsync`' I çağırırken, `mimeType` `text/html` HTML oluşturmayı etkinleştirmek için öbek özelliğini olarak ayarlayın.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Sonraki adımlar

* [Tam ekran okuyucu SDK başvurusunu](reference.md) keşfet