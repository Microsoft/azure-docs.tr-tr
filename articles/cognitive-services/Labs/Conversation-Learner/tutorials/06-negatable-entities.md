---
title: Conversation Learner modeliyle, Microsoft bilişsel hizmetler ile Negatiftablo varlıklarını kullanma | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner modeliyle bir Negatiftablo varlıklarını nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 4edbfc396d4ab32b991b084a7b738f04b5205418
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704100"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Conversation Learner modeliyle Negatiftablo varlıklarını kullanma

Bu öğreticide varlıkların "Negatiftable" özelliği gösterilmektedir.

## <a name="video"></a>Video

[![Negatiftablo varlıkları öğreticisi önizlemesi](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir

    npm run tutorial-general

## <a name="details"></a>Ayrıntılar
Bir varlığın "Negatiftable" özelliği, varlığın hem normal (olumlu) hem de negatif örneklerinin etiketlemesini, olumlu ve negatif modellere göre öğretebilir ve var olan bir varlığın değerini temizlemenizi sağlar. "Negatiftable" özelliği ayarlanmış varlıklara sahip varlıklar, konuşma anlayıcı içindeki Negatiftablo varlıkları olarak adlandırılır.

## <a name="steps"></a>Adımlar

Web Kullanıcı arabirimindeki giriş sayfasında başlatın.

### <a name="create-the-model"></a>Model oluşturma

1. **Yeni model**' i seçin.
2. **Ad**Için bir **negatiftableentity** girin.
3. **Oluştur**’u seçin.

### <a name="entity-creation"></a>Varlık oluşturma

1. Sol panelde **varlıklar** ' ı ve sonra **yeni varlık**' ı seçin.
2. **Varlık türü**Için **özel eğitimli** ' i seçin.
3. **Varlık adı**için **ad** girin.
4. Kullanıcıların bir varlık değeri sağlamasını sağlamak için **negatifi** işaretleyin veya bir şeyi bir varlık değeri olmadığını söyleyin, bu nedenle eşleşen varlık değerini silin.
5. **Oluştur**’u seçin.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Ilk eylemi oluşturma

1. Sol panelde **Eylemler** ' i ve sonra **yeni eylem**' i seçin.
2. **Adınızı bilmiyorum yazın.** **bot 'un yanıtı için...** .
3. Ayırt **edici sahibine**için **ad** girin.
4. **Oluştur**’u seçin.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Ikinci eylemi oluşturma

1. Sol panelde **Eylemler** ' i ve sonra **yeni eylem**' i seçin.
2. Adınızı **biliyorum yazın. $Name.** **bot 'un yanıtı için...** .
3. **Oluştur**’u seçin.

> [!NOTE]
> **Ad** varlığı, yanıt ututundaki başvuruya göre **gerekli varlıklar** olarak otomatik olarak eklenmiştir.

Artık iki eylemleriniz vardır.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Modeli eğitme

1. Sol panelde iletişim **kutularını eğitme** ' yi ve ardından **Yeni eğitme iletişim kutusunu**seçin.
2. Sol sohbet panelinde kullanıcının utterliği için **Merhaba** yazın.
3. **Puan eylemlerini**seçin.
4. **Adınızı bilmiyorum ' ı seçin.** Eylemler listesinden. Yüzdebirlik değeri, kısıtlamalara göre geçerli olan tek eylem olarak% 100 ' dir.
5. Kullanıcının sol sohbet panelinde UTİ olarak **adı filiz** 'e yazın.
6. **Filiz** ' i vurgulayın ve **+ ad**' ı seçin. Negatiftablo varlıklarının iki örneği vardır: (+) artı değeri ekler veya üzerine yazar; (-) eksi değeri kaldırır.
7. **Puan eylemlerini**seçin. **Ad** varlığı şimdi modelin belleğinde **filiz** olarak tanımlanmıştır **, bu nedenle adınızı biliyorum. $Name** eylem kullanılabilir.
8. Adınızı **biliyorum ' ı seçin. $Name.** Eylemler listesinden.
9. **Ad My adı filiz değil.** kullanıcının, sol sohbet paneli ' ne kadar.
10. **Ad** varlığındaki değeri temizlemek için **filiz** ' i ve Select **-Name** ' i vurgulayın.
11. **Puan eylemlerini**seçin.
12. **Adınızı bilmiyorum ' ı seçin.** Eylemler listesinden.
13. **My Name to çiğdem yazın.** Sol sohbet panelinde kullanıcının üçüncü utterliği için.
14. **Çiğdem** ve **+ ad** Vurgula 

![](../media/T06_training.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Çoklu değerli varlıklar](./07-multi-value-entities.md)
