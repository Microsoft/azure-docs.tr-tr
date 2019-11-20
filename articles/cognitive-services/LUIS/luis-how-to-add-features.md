---
title: Tanımlayıcılar-LUSıS
titleSuffix: Azure Cognitive Services
description: Bu kategori ve desenleri algılama veya hedefleri ve varlıkların tahmin iyileştirebilir uygulama özelliklerini eklemek için Language Understanding (LUIS) kullanın
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123128"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Sözcük listesinin sinyalini artırmak için tanımlayıcıları kullanın

LUIS uygulamanızı kendi doğruluğunu artırmak için özellikler ekleyebilirsiniz. Bu belirli kelimeleri ipuçları sağlayarak Yardım LUIS özellikleri ve ifadeleri bir uygulama etki alanı sözlüğü bir parçasıdır. 

Bir [tanımlayıcı](luis-concept-feature.md) (tümcecik listesi), aynı sınıfa ait olan ve benzer şekilde (örneğin, şehirlerin veya ürünlerin adları) bir grup değer (sözcükler veya ifadeler) içerir. LUIS bunları biri hakkında ne öğrenir otomatik olarak başkaları için de uygulanır. Bu liste, eşleşen sözcüklerin bir [liste varlığıyla](reference-entity-list.md) (tam metin eşleşmeleri) aynı şey değildir.

Bir tanımlayıcı, bu kelimeyle ilgili ikinci bir sinyal olarak uygulama etki alanının sözlüğüne ekler.

Bir tanımlayıcının ne zaman ve neden kullanılacağını anlamak için [özellik kavramlarını](luis-concept-feature.md) gözden geçirin. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Tanımlayıcı Ekle

1. **Uygulamalarım** sayfasında adına tıklayarak uygulamanızı açın ve ardından **Oluştur**' a tıklayın ve ardından uygulamanızın sol panelinde **tanımlayıcılar** ' a tıklayın. 

1. **Tanımlayıcılar** sayfasında **+ tanımlayıcı Ekle**' ye tıklayın. 
 
1. **Yeni tümcecik listesi tanımlayıcısı oluştur** iletişim kutusunda, tanımlayıcı için `Cities` gibi bir ad girin. **Değer** kutusuna, `Seattle`gibi tanımlayıcılarının değerlerini yazın. Bir saat veya virgülle ayrılmış değerler kümesi tek bir değer yazın ve sonra basın **Enter**.

    > [!div class="mx-imgBorder"]
    > ![tanımlayıcı şehir ekleyin](./media/luis-add-features/add-phrase-list-cities.png)

    LUO için yeterli değer girdikten sonra öneriler görünür. Önerilen değerlerin **Tümünü ekleyebilir** veya tek tek terimleri seçebilirsiniz.

1. Bu değerleri, eklenen tanımlayıcı değerleri birbirinin yerine kullanılabilen alternatifler ise, **Bu değerleri, değiştirilebilir olarak** bırakın.

1. **Done** (Bitti) öğesini seçin. Yeni tanımlayıcı, **tanımlayıcılar** sayfasına eklenir.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> **Tanımlayıcılar** sayfasında bağlamsal araç çubuğundan tanımlayıcıyı silebilir veya devre dışı bırakabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bir tanımlayıcıyı ekledikten, düzenledikten, sildikten veya devre dışı bırakarak, performansın gelişip artmediğini görmek için [uygulamayı yeniden eğitin ve test](luis-interactive-test.md) edin.
