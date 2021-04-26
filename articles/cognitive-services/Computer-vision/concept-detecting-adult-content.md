---
title: Yetişkin, korcy, Gori içeriği-görüntü işleme
titleSuffix: Azure Cognitive Services
description: Görüntü İşleme API'si kullanarak görüntülerde yetişkinlere yönelik içeriği algılamayla ilgili kavramlar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ceef604fe07a11be89376e26c6fecc49298ebacf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778868"
---
# <a name="detect-adult-content"></a>Yetişkin içeriğini algıla

Görüntü İşleme, geliştiricilerin bu görüntülerin yazılımda görüntülenmesini kısıtlayabilmeleri için resimlerde yetişkinlere yönelik malzemeleri algılayabilir. İçerik bayrakları sıfır ile diğeri arasında bir puan ile uygulanır, böylece geliştiriciler sonuçları kendi tercihlerine göre yorumlayabilir.

> [!NOTE]
> Bu işlevlerin çoğu [Azure Content moderator](../content-moderator/overview.md) hizmeti tarafından sunulur. Metin denetleme ve insan incelemesi iş akışları gibi daha ayrıntılı içerik denetleme senaryolarına yönelik çözümler için bu alternatif bölümüne bakın.

## <a name="content-flag-definitions"></a>İçerik bayrağı tanımları

"Yetişkin" sınıflandırması çeşitli farklı kategoriler içerir:

- **Yetişkinlere yönelik** görüntüler açık bir şekilde cinsel ve genellikle çıplaklık ve cinsel işlemler gösterir.
- Kışkırtıcı görüntüler, açık cinsel içerikli ve genellikle **yetişkin** olarak **etiketlenen görüntülerden daha** az cinsel içerikli içerik içerir.
- **Gory** görüntüleri kan/gore gösterir.

## <a name="use-the-api"></a>API’yi kullanma

[Görüntü analizi](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API 'siyle yetişkinlere yönelik içeriği algılayabilirsiniz. Değerini `Adult` **visualfeatures** sorgu parametresine eklediğinizde, API üç Boole özelliği, &mdash; `isAdultContent` `isRacyContent` ve `isGoryContent` &mdash; JSON yanıtında döndürür. Yöntemi ayrıca karşılık gelen özellikleri döndürür &mdash; `adultScore` `racyScore` ve ilgili `goreScore` &mdash; her kategori için sıfır ile bir arasındaki güven puanlarını temsil eder.

- [Hızlı başlangıç: Görüntü İşleme REST API veya istemci kitaplıkları](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
