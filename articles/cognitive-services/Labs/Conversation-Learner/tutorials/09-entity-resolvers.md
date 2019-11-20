---
title: Conversation Learner modelinde varlık çözücüler-Azure bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner içinde varlık çözümleyicilerine nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 60c4abf1590cb91fd460cc6a2a5ba75a225ebd80
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704035"
---
# <a name="entity-resolvers"></a>Varlık çözücüler

Bu öğreticide varlık çözümleyicilerine Conversation Learner ' de nasıl kullanılacağı gösterilmektedir

## <a name="video"></a>Video

[![Varlık çözücüler öğretici Önizleme](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir

    npm run tutorial-general

## <a name="details"></a>Ayrıntılar

- Çözümleyici türü, özel varlıkların isteğe bağlı bir özelliğidir.
- Varlık Çözümleyicileri, özel varlığınızda ek ayrıntı ve açıklık sağlamak için LUSıS 'de önceden eğitilen varlık tanıyıcılarının gücünü kullanır.

## <a name="steps"></a>Adımlar

Web Kullanıcı arabirimindeki giriş sayfasında başlatın.

### <a name="create-the-model"></a>Model oluşturma

1. **Yeni model**' i seçin.
2. **Ad**Için **varlık çözümleyicilerine** girin.
3. **Oluştur**’u seçin.

### <a name="create-a-pair-of-entities"></a>Varlık çifti oluşturma

1. Sol panelde **varlıklar** ' ı ve sonra **yeni varlık**' ı seçin.
2. **Varlık adı**için bir **ayrılma** girin.
3. **Çözümleyici türü**için **datetimeV2** öğesini seçin.
4. **Oluştur**’u seçin. **Tamam ' ı**seçerek bilgilendirici açılan pencereyi kapatın.
5. **DatetimeV2** çözümleyici türüyle **Return** adlı ikinci bir varlık oluşturmak için 1-4 arası adımları tekrarlayın.

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Eylem çifti oluşturma

1. Sol panelde **Eylemler** ' i ve sonra **yeni eylem**' i seçin.
2. **$Departure ayrıldığınızda ve** **bot 'ın yanıtı**için $return döndürülüyor....
    - ÖNEMLI-$ [entityName] içine yazarken ENTER tuşuna basın veya açılan kutuda varlığa tıklamalıdır, aksi takdirde Conversation Learner bunu bir varlık yerine metin olarak kabul eder.
    - **Gerekli varlıklar** alanının bu varlıkları da alacak olduğunu ve kaldırılmadığını unutmayın. Bu işlem, gerekli varlıkların her ikisi de mevcut olana kadar bu eylemin kullanılabilir hale gelmesini engeller.
3. **Oluştur**’u seçin.
4. İkinci bir eylem oluşturmak için **yeni eylem** ' i seçin.
5. Bot 'un yanıtı için **ne zaman seyahat planlaması yapmanız** gerektiğini girin **...**
6. Ayrılan **varlıklar**için **ayrılma** ve **döndürme** girin. Bu, bu varlıklardan biri bir değer içeriyorsa, bu eylemi uygulamamıza götürür.
7. **Oluştur**’u seçin.

![](../media/T09_actions.png)

### <a name="training"></a>Eğitim

1. **Tamamlandı**için sol üst köşedeki **eğitimi Izleyin: [durum]** .
    - Bu çok uzun sürerse "Yenile" bağlantısına tıklayabilirsiniz.
    - "Tamamlandı" eğitim durumu gereklidir, böylelikle varlık çözümleyiciler modeli eğittiğimiz sırada çalışır.

2. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
3. İlk Kullanıcı "yazın," bir uçuşla "yazın. 
4. "Eylemleri puan" düğmesine tıklayın.
5. "Yolculuğu planlarken ne zaman?" yanıtını seçin.
6. Kullanıcı olarak, "Yarın çıkılıyor ve sonraki hafta Pazar günü döndürülüyor" ile yanıtlayın.
    - Conversation Learner, bu kullanıcı tarafından açılan iki "önceden eğitilen tarih" varlığı nasıl algıladığına dikkat edin.
7. "Varlık algılama" panelinde "Yarın" metnini seçin ve bunu "ayrılma" olarak etiketleyin
8. Ayrıca "Pazar sonraki Haftası" metnini "return" olarak etiketle
9. "Eylemleri puan" düğmesine tıklayın.
    - "Bellek" bölmesinin, ayrılma ve dönüş tarihlerini nasıl içerdiğini unutmayın.
    - Her birinin üzerine gelin ve varlıkların "Pazar" veya "Yarın" yerine gerçek takvim tarihini açıkça yakaladığı Tarih nesneleri olduğunu gözlemleyin.
10. "Çıkılıyor..." seçeneğini belirleyin Bot yanıtı.
11. "Kaydet" düğmesine tıklayın.

![](../media/T09_training.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Sabit Listesi varlıkları](./tutorial-enum-set-entity.md)
