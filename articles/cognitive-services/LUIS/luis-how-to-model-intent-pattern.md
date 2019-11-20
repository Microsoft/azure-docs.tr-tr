---
title: Desenler doğruluk Ekle-LUSıS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUSıS) uygulamalarında tahmin doğruluğunu artırmak için model şablonları ekleyin.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 39b56c5e73c8ce85a020402dafb622b90c536a1e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143872"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Tahmin doğruluğunu artırmak için desenler ekleme
Bir Lua uygulaması uç nokta dıklılığını aldıktan sonra, sözcük sırası ve Word seçimindeki bir düzeni açığa çıkarmaya yönelik tahmin doğruluğunu artırmak için bir [desen](luis-concept-patterns.md) kullanın. Desenler, konumunu belirtmek için özel [sözdizimini](luis-concept-patterns.md#pattern-syntax) kullanır: [varlıklar](luis-concept-entity-types.md), varlık [rolleri](luis-concept-roles.md)ve isteğe bağlı metin.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-template-utterance-to-create-pattern"></a>Desen oluşturmak için şablon utterance Ekle

1. Adını seçerek uygulamanızı açın **uygulamalarım** sayfasında ve ardından **desenleri** sol bölmede altında **uygulama performansını**.

    > [!div class="mx-imgBorder"]
    > Desenler listesinin ekran görüntüsünü ![](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Desen için doğru hedefini seçin. 

1. Şablon metin şablonu utterance yazın ve Enter'ı seçin. Varlık adı girmek istediğiniz zaman doğru deseni varlık sözdizimini kullanın. Varlık sözdizimi ile başlayan `{`. Varlıklar görüntüler listesi. Doğru varlığı seçin. 

    > [!div class="mx-imgBorder"]
    > ](./media/luis-how-to-model-intent-pattern/patterns-3.png) deseninin varlık ekran görüntüsünü ![

    Varlığınız bir [rol](luis-concept-roles.md)içeriyorsa, `{Location:Origin}`gibi varlık adından sonra `:`tek bir iki nokta üst üste sahip rolü belirtin. Rolleri varlıkların listesini bir liste görüntüler. Rolü seçin ve ardından Enter'ı seçin. 

    > [!div class="mx-imgBorder"]
    > rol](./media/luis-how-to-model-intent-pattern/patterns-4.png) varlığın ekran görüntüsünü ![

    Doğru varlık seçtikten sonra deseni girdikten ve ardından Enter'ı seçin. Girme desenleri, işiniz bittiğinde [eğitme](luis-how-to-train.md) uygulamanızı.

    > [!div class="mx-imgBorder"]
    > Her iki varlık türü ile girilen düzenin ekran görüntüsünü ![](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Model desenleri ile değiştirdikten sonra uygulamanızı eğitin
Sonra ekleme, düzenleme, kaldırmak veya bir desen yeniden atama [eğitme](luis-how-to-train.md) ve [yayımlama](luis-how-to-publish-app.md) uygulamanız için uç nokta sorguları etkilemek yaptığınız değişiklikleri. 

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Bağlamsal araç çubuğunu kullan

Desenler listesinin üzerindeki bağlamsal araç çubuğu şunları yapmanıza olanak sağlar:

* Desenler ara
* Bir desen Düzenle
* Farklı bir amaç için ayrı ayrı desen yeniden atama
* Farklı bir hedefi için çeşitli desenlerden yeniden atama
* Tek-stili Sil
* Çeşitli desenlerden Sil
* Varlık tarafından filtre deseni listesi
* Filtre-ölçütü-ölçütü-Listele
* Varlık veya hedefi Filtreyi Kaldır
* Desen hedefi veya varlık sayfasında mevcut utterance ekleyin

## <a name="next-steps"></a>Sonraki adımlar

* Bir modelle bir model [oluşturma](luis-tutorial-pattern.md) hakkında bilgi edinin.
* Bilgi nasıl [eğitme](luis-how-to-train.md) uygulamanızı.
