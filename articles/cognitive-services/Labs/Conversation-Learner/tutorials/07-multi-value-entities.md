---
title: Conversation Learner modeliyle çok değerli varlıklar kullanma-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner modeliyle çok değerli varlıkların nasıl kullanılacağını öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7911dd561da0f1e7b62b1b457ae5b059d5d54767
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704096"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Conversation Learner modeliyle Çoklu değerli varlıklar kullanma
Bu öğreticide varlıkların çok değerli özelliği gösterilmektedir.

## <a name="video"></a>Video

[![Çok değerli varlıklar öğretici Önizleme](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir

    npm run tutorial-general

## <a name="details"></a>Ayrıntılar
Birden çok değerli varlıklar, değerleri tek bir değer depolamak yerine bir listede biriktir.  Bu varlıklar, kullanıcılar birden fazla değer belirtişinize yarayacaktır. Örneğin, bir pizza toppings.

Çoklu değer olarak işaretlenen varlıkların, her tanınan varlık örneği, bot 'ın belleğindeki bir listeye eklenir. Sonraki tanıma, varlığın değerine, üzerine yazılması yerine ekler.

## <a name="steps"></a>Adımlar

Web Kullanıcı arabirimindeki giriş sayfasında başlatın.

### <a name="create-the-model"></a>Model oluşturma

1. **Yeni model**' i seçin.
2. **Ad**Için **multivalueentities** girin.
3. **Oluştur**’u seçin.

### <a name="entity-creation"></a>Varlık oluşturma

1. Sol panelde **varlıklar** ' ı ve sonra **yeni varlık**' ı seçin.
2. **Varlık türü**Için **özel eğitimli** ' i seçin.
3. **Varlık adı**için **toppings** girin.
4. Varlığın bir veya daha fazla değer birikmesini sağlamak için **Çoklu değerli** ' i işaretleyin.
5. **Negatifi tabloyu**denetle.
6. **Oluştur**’u seçin.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Ilk eylemi oluşturma

1. Sol panelde **Eylemler** ' i ve sonra **yeni eylem**' i seçin.
2. Toppings: bot 'un yanıtı için **$toppings buraya** girin **...** Önde gelen dolar işareti bir varlık başvurusunu gösterir.
3. **Oluştur**’u seçin.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Ikinci eylemi oluşturma

1. Sol panelde **Eylemler** ' i ve sonra **yeni eylem**' i seçin.
2. Botonun **yanıtı**için **hangi toppings** istediğinizi girin?...
3. Ayırt **edici sahibine**için **toppings** girin.
4. **Oluştur**’u seçin.

Artık iki eylemleriniz vardır.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Modeli eğitme

1. Sol panelde iletişim **kutularını eğitme** ' yi ve ardından **Yeni eğitme iletişim kutusunu**seçin.
2. Sol sohbet panelinde kullanıcının utterliği için **Merhaba** yazın.
3. **Puan eylemlerini**seçin.
4. Eylemler listesinden **ne toppings** istediğinizi seçin. Yüzdebirlik değeri, kısıtlamalara göre geçerli olan tek eylem olarak% 100 ' dir.
5. Sol sohbet panelinde kullanıcının utterliği için **Cheese ve mushodalar** girin.
6. **Cheese** 'yi vurgulayın ve **+ toppings**seçeneğini belirleyin.
7. **Mushodalar** ' ı vurgulayın ve **+ toppings**öğesini seçin.
8. **Puan eylemlerini**seçin.
9. Eylemler listesinden **toppings: $toppings** seçin.
10. Sol sohbet panelinde kullanıcının sonraki utterine için **Add peber** yazın.
11. **Peber** 'yi vurgulayın ve **+ toppings**seçeneğini belirleyin.
12. **Puan eylemlerini**seçin.
13. Eylemler listesinden **toppings: $toppings** seçin.
14. Sol sohbet panelinde kullanıcının üçüncü utterliği için **Cheese kaldır** ' a katılın.
15. **Cheese** 'yi vurgulayın ve sonra **-toppings**seçeneğini belirleyin.
16. **Puan eylemlerini**seçin.
17. Eylemler listesinden **toppings: $toppings** seçin.

![](../media/T07_training.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Önceden eğitilen varlıklar](./08-pre-trained-entities.md)
