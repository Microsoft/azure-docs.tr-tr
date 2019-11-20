---
title: Yetişkin, korcy, Gori içeriği-görüntü işleme
titleSuffix: Azure Cognitive Services
description: Görüntü İşleme API 'sini kullanarak görüntülerde yetişkinlere yönelik içeriği algılamayla ilgili kavramlar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718524"
---
# <a name="detect-adult-content"></a>Yetişkin içeriğini algıla

Görüntü İşleme, geliştiricilerin bu görüntülerin yazılımda görüntülenmesini kısıtlayabilmeleri için resimlerde yetişkinlere yönelik malzemeleri algılayabilir. İçerik bayrakları, geliştiricilerin sonuçları kendi tercihlerine göre yorumlayabilmesi için sıfır ile diğeri arasında bir puan ile uygulanır.

> [!NOTE]
> Bu işlevlerin çoğu [Azure Content moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) hizmeti tarafından sunulur. Metin denetleme ve insan incelemesi iş akışları gibi daha ayrıntılı içerik denetleme senaryolarına yönelik çözümler için bu alternatif bölümüne bakın.

## <a name="content-flag-definitions"></a>İçerik bayrağı tanımları

"Yetişkinlere yönelik" sınıflandırma içinde birkaç farklı kategoride:

- **Yetişkinlere yönelik** görüntüler, doğası gereği açıkça cinsel ve genellikle çıplaklık ve cinsel işlemler belirleyen bir şekilde tanımlanır.
- Kışkırtıcı görüntüler, açık cinsel **içerikli görüntüler olarak** tanımlanır ve genellikle **yetişkin**olarak etiketlenen görüntülerden daha az cinsel içerikli içerik içerir.
- **Gory** görüntüleri, gore ' i belirleyen şekilde tanımlanır.

## <a name="use-the-api"></a>API’yi kullanma

[Görüntü analizi](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 'siyle yetişkinlere yönelik içeriği algılayabilirsiniz. @No__t-0 değerini **Visualfeatures** sorgu parametresine EKLEDIĞINIZDE, API, JSON yanıtında @ no__t-2 @ no__t-3, `isRacyContent` ve `isGoryContent` @ no__t-6Üç Boole özelliği döndürür. Bu yöntem aynı zamanda, karşılık gelen her kategori için sıfır ile bir arasındaki güven puanlarını temsil eden @ no__t-0 @ no__t-1, `racyScore` ve `goreScore` @ no__t-4özelliklerine karşılık gelen özellikleri döndürür.

- [Hızlı başlangıç: bir görüntüyü çözümleme (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Hızlı başlangıç: bir görüntüyü çözümleme (REST API)](./quickstarts/csharp-analyze.md)
