---
title: Microsoft bilişsel hizmetler 'e Conversation Learner modele önceden eğitilen varlıklar ekleme | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner modeline önceden eğitilen varlıkların nasıl ekleneceğini öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: fceca459b80725e1a9c8b7dbc6a4387ea98dbb7b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704132"
---
# <a name="how-to-add-pre-trained-entities"></a>Önceden eğitilen varlıklar ekleme
Bu öğreticide, Conversation Learner modelinize önceden eğitilen varlıkların nasıl ekleneceği gösterilmektedir.

## <a name="video"></a>Video

[![Eğitilen varlıklar öğretici Önizleme](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir

    npm run tutorial-general

## <a name="details"></a>Ayrıntılar

Önceden eğitilen varlıklar, sayılar, tarihler, parasal tutarlar ve diğerleri gibi ortak varlık türlerini tanır.  "Kullanıma hazır", herhangi bir eğitim gerektirmez ve davranışları özel varlıkların aksine değiştirilemez.  Varsayılan olarak, önceden eğitilen varlıklar, varlığın tanımlanan her örneğini birikerek çok değerli bir varlıktır.

## <a name="steps"></a>Adımlar

Web Kullanıcı arabirimindeki giriş sayfasında başlatın.

### <a name="create-the-model"></a>Model oluşturma

1. **Yeni model**' i seçin.
2. **Ad**için **ön ödeme özellikleri** girin.
3. **Oluştur**’u seçin.

### <a name="entity-creation"></a>Varlık oluşturma

1. Sol panelde **varlıklar** ' ı ve sonra **yeni varlık**' ı seçin.
2. **Varlık türü**Için **önceden eğitimli/datetimeV2** seçeneğini belirleyin.
3. Varlığın bir veya daha fazla değer birikmesini sağlamak için **Çoklu değerli** ' i işaretleyin. Bu şekilde, önceden eğitilen varlıkların negatifi tablo olamaz.
4. **Oluştur**’u seçin.

![](../media/T08_entity_create.png)

1. Sol panelde **Eylemler** ' i ve sonra **yeni eylem**' i seçin.
2. Bot 'un yanıtı için **$Builtin-datetimev2** girin **...** .
3. **Oluştur**’u seçin.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Ikinci eylemi oluşturma

1. Sol panelde **Eylemler** ' i ve sonra **yeni eylem**' i seçin.
2. Botonun **yanıtı**için **ne tarihi girin?** Önceden eğitilen varlıklar, tüm söyler için varsayılan olarak tanındıklarından **gerekli varlıklar** olamaz.
3. **Niteleyen varlıklar**için **Builtin-datetimev2** girin.
4. **Oluştur**’u seçin.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Modeli eğitme

1. Sol panelde iletişim **kutularını eğitme** ' yi ve ardından **Yeni eğitme iletişim kutusunu**seçin.
2. Sol sohbet panelinde kullanıcının utterliği için **Merhaba** yazın.
3. **Puan eylemlerini**seçin.
4. Eylemler listesinden **ne tarih??** i seçin
5. Sol sohbet panelinde kullanıcının utterliği için **bugün** girin.
    - **Bugün** , lusıs 'de önceden eğitilen modeller tarafından otomatik olarak tanınır.
    - Önceden eğitilen varlıkların değerlerinin üzerine gelindiğinde, LUO tarafından sunulan ek veriler gösterilir.

![](../media/T08_training.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Varlık çözücüler](./09-entity-resolvers.md)
