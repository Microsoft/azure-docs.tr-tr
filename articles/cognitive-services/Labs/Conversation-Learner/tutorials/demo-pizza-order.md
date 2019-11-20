---
title: Demo Conversation Learner modeli, pizza sıralaması-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Demo Conversation Learner modeli oluşturmayı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 4d42121468b8a875ecd13fb936d810728bd4b644
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703434"
---
# <a name="demo-pizza-order"></a>Tanıtım: Pizza sırası
Bu tanıtımda, tek bir pizza sıralamayı destekleyen bir pizza sıralama bot gösterilmektedir:

- Kullanıcı utslarından pizza toppings 'i tanıma
- toppings envanterini yönetme ve uygun şekilde yanıt verme
- önceki siparişleri hatırlama ve özdeş bir pizza 'nin yeniden sıralanmasını hızlandırma

## <a name="video"></a>Video

[![Demo pizza önizlemesi](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, pizza sıra bot 'ın çalışıyor olması gerekir

    npm run demo-pizza

### <a name="open-the-demo"></a>Tanıtımı açın

Web Kullanıcı arabiriminin model listesinde, TutorialDemo pizza Order öğesine tıklayın. 

## <a name="entities"></a>Varlıklar

Model üç varlık içerir:

- "Toppings", varsa kullanıcının belirtilen toppings birikir.
- "OutofStock", seçilen kullanıcının stok dışı olduğunu bildirir
- "LastToppings", önceki sıralarından geçmiş toppings içerir

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Eylemler

Model, kullanıcının seçim, birikmiş toppings ve daha fazlası için kullanıcıya ait olduğunu soran bir eylemler kümesi içerir.

İki API çağrısı da sağlanır:

- "FinalizeOrder" sırayı tamamlama sırasını işler
- "UseLastToppings", geçmiş toppings bilgilerini işler

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Eğitim Iletişim kutuları

Modelde birçok eğitim Iletişim kutusu bulunur.

![](../media/tutorial_pizza_dialogs.PNG)

Başka bir eğitme Iletişim kutusu oluşturarak modeli biraz daha eğelim.

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Sohbet panelinde, "iletinizi yazın..." ifadesini yazın, "yazla bir pizza sıralama" yazın.
    - "Cheese" sözcüğü, varlık ayıklayıcısı tarafından ayıklandı.
3. "Eylemleri puan" düğmesine tıklayın.
4. "Pizza sahipsiniz" yanıtını seçin.
5. "Başka bir şey ister misiniz?" yanıtını seçin
6. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "mushodalar ve peş Ekle" yazın.
7. "Eylemleri puan" düğmesine tıklayın.
8. "Bu yanıtı," sizin pizza, musları ve soyteneçlerinizi) seçin.
9. "Başka bir şey ister misiniz?" yanıtını seçin
10. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "peş leri kaldır ve Sausage Ekle" yazın.
11. "Eylemleri puan" düğmesine tıklayın.
12. "Bu yanıtı seçin," Pizza, mushodalar ve Sausage sahipsiniz. "
13. "Başka bir şey ister misiniz?" yanıtını seçin
14. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "yaı Ekle" yazın.
15. "Eylemleri puan" düğmesine tıklayın.
    - "Yae" değeri, varlık algılama geri çağırma kodu tarafından "OutofStock" olarak eklenmiştir, çünkü metin desteklenen herhangi bir malzemeler ile eşleşmez.
16. "OutOfStock" yanıtını seçin
17. "Başka bir şey ister misiniz?" yanıtını seçin
18. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Hayır" yazın.
    - "Hayır" işlemi herhangi bir tür amaç olarak işaretlenmez. Bunun yerine, geçerli bağlam temelinde ilgili eylemi seçeceğiz.
19. "Eylemleri puan" düğmesine tıklayın.
20. "FinalizeOrder" yanıtını seçin
    - Bu eylemin seçilmesi, müşterinin güncel toppings "LastToppings" varlığına kaydedilmesi ve "toppings" varlığının Sonlandırizeorder geri çağırma kodu tarafından silinmesini sonuçlanmış.
21. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "başka bir sıralama" yazın
22. "Eylemleri puan" düğmesine tıklayın.
23. "Kimese, mushodalar ve Sausage ister misiniz?" yanıtını seçin
    - Bu eylem, "LastToppings" varlığı ayarlanmakta olduğundan şimdi kullanılabilir.
24. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Evet" yazın
25. "Eylemleri puan" düğmesine tıklayın.
26. "UseLastToppings" yanıtını seçin
27. "Bu yanıtı seçin," Pizza, mushodalar ve Sausage sahipsiniz. "
28. "Başka bir şey ister misiniz?" yanıtını seçin

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Conversation Learner bot dağıtma](../deploy-to-bf.md)
