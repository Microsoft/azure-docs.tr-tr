---
title: Conversation Learner modeliyle dallandırma ve geri alma işlemlerini kullanma-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner modeliyle dallandırma ve geri alma işlemlerini nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f4f3024451696dbd0244d9da39cba67b49447af1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703634"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Dallanma ve geri alma Işlemlerini kullanma
Bu öğreticide, geri alma ve dallanma işlemlerine gidecağız.


## <a name="details"></a>Ayrıntılar
### <a name="undo"></a>Geri alma
Geliştiricinin Son Kullanıcı girişini veya eylem seçeneğini "geri almasına" izin verir. Arka planda "geri al", aslında yeni bir iletişim kutusu oluşturur ve önceki adıma yeniden çalar.  Bu, iletişim kutusundaki varlık algılama geri çağrısının ve API çağrılarının yeniden çağrıldığı anlamına gelir.

### <a name="branch"></a>Dal
Mevcut eğitme iletişim kutusuyla aynı şekilde başlayan yeni bir eğitme iletişim kutusu oluşturur. Bu, el ile yeniden girme çabalarının, iletişim kutusunun dönüşmesini sağlar. Arka planda, "dal" yeni bir iletişim kutusu oluşturur ve mevcut eğitme iletişim kutusunu seçilen adıma yeniden çalar.  Bu, iletişim kutusundaki varlık algılama geri çağrısının ve API çağrılarının yeniden çağrıldığı anlamına gelir.


## <a name="requirements"></a>Gereksinimler
Bu öğreticide, pizza siparişi alan bot 'ın çalışıyor olması gerekir:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Tanıtımı açın veya Içeri aktarın

Pizza sıralama öğreticisiyle zaten çalıştıysanız, bu modeli Web Kullanıcı arabirimindeki listeden açmanız yeterlidir. Aksi takdirde "öğreticileri Içeri aktar" seçeneğine tıklamanız ve "demo-PizzaOrder" adlı modeli seçmeniz gerekir.

## <a name="undo"></a>Geri alma

İşte `Undo` özelliğin nasıl görübir örneği aşağıda verilmiştir:

### <a name="training-dialogs"></a>Eğitim Iletişim kutuları
1. Sol bölmede "iletişim kutularını eğitme" `New Train Dialog` düğmesine tıklayın ve ardından düğmeye tıklayın.
2. "Bir pizza Sırala" yazın.
3. `Score Actions` düğmesine tıklayın.
4. "Pizza ne istersiniz?" seçeneğini belirlemek için tıklayın.
5. "Her şeyi" yazın.
6. `Undo` düğmesine tıklayın.
    - Son giriş kaldırılır ve "en son bot yanıtı," pizza ne istersiniz? "

## <a name="branch"></a>Dal

Bu tanıtımda, var olan bir eğitme Iletişim kutusunu açacak ve dallandırarak yeni bir tren Iletişim kutusu oluşturacağız.

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine tıklayın.
2. Kılavuza dikkat edin, "yeni sipariş" ile başlayan yalnızca bir eğitim görmeniz gerekir.
3. Kılavuzda, mevcut eğitme Iletişim kutusunu açmak için "yeni sıra" düğmesine tıklayın.
4. İletişim kutusunda son "Hayır" düğmesine tıklayın.
5. "Dal" simgesine tıklayın, bu görüntüde kırmızı renkte bir daire içinde bulunur:
    - ![](../media/tutorial15_branch.PNG)
    - "Hayır" öncesinde tren Iletişim kutusunun tamamı yeni bir eğitme Iletişim kutusuna kopyalanır.
    - Bu, önceki ' bir konuşmayı bu noktadan "dalı" keşfetmeye yönelik olarak yeniden girmeyi kaydeder.
6. "Yes" yazın, ENTER tuşuna basın.
7. `Score Actions` düğmesine tıklayın.
    - Bu noktada, bot otomatik olarak bir yanıt seçer, ancak bunu değiştirebilmemiz için yanıtı beğenmedik.
8. Son bot yanıtına tıklayın.
    - Bu, farklı bir yanıt seçmemizi sağlar.
9. "UseLastToppings" öğesini seçin.
10. `Score Actions` düğmesine tıklayın.
    - Daha sonra, bot otomatik olarak bir yanıt seçer. "Bu," Pizza Ihtiyacınız olan "sakullanımınız, Cheese ve mushodalarınız" olmalıdır. 
    - Bu süre yanıt veririz.
11. `Score Actions` düğmesine tıklayın.
    - Bot otomatik olarak bir yanıt seçer, "başka bir şey ister misiniz?" deyin.
12. "Hayır" yazın.
13. `Save Branch` düğmesine tıklayın.
14. Kılavuzun artık "yeni sipariş" ile başlayan iki taş olduğuna dikkat edin.
    - Bunlardan biri, dallandırmak için kullandığınız bir biridir.
    - Diğeri ise yeni kaydettiğiniz dallanmış sürümdür.
    - Bu beklentileri doğrulamak için her birine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Sürüm oluşturma ve etiketleme](./18-version-tag.md)
