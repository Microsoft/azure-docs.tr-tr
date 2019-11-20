---
title: Conversation Learner modeliyle API çağrılarını kullanma-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner modeliyle API çağrılarını nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 261536932cc82a28ad4ee3ffc3575ea41fe9ec5b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703920"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Conversation Learner modeline API çağrıları ekleme

Bu öğretici, modelinize API çağrılarının nasıl ekleneceğini gösterir. API çağrıları, bot 'ta tanımladığınız ve yazdığınız ve Conversation Learner çağırabileceği işlevlerdir.

## <a name="video"></a>Video

[![API çağrıları öğretici önizlemesi](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, "tutorialAPICalls. TS" bot 'ın çalışıyor olması gerekir.

    npm run tutorial-api-calls

## <a name="details"></a>Ayrıntılar

- API çağrıları, varlıkları okuyabilir ve işleyebilir.
- API çağrılarının bellek Yöneticisi nesnesine erişimi vardır.
- API çağrıları bağımsız değişkenler alabilir.

### <a name="open-the-demo"></a>Tanıtımı açın

Web Kullanıcı arabiriminde "öğreticileri Içeri aktar" düğmesine tıklayın ve "öğretici-14-APICalls" adlı modeli seçin.

### <a name="entities"></a>Varlıklar

Adlı `number`modelde bir varlık tanımlıyoruz.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API çağrıları
API çağrılarının kodu bu dosyada tanımlanmıştır: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- Geri çağırma, `greeting` dizide tanımlanmış bir rastgele selamlama döndürür. `RandomGreeting`
- `Multiply` Geri çağırma işlemi çağıran eyleme göre geçirilen iki sayıyı çarpıp Kullanıcı arabiriminde işlenebilen bir sonuç döndürür.
    - Bellek Yöneticisi 'nin ilk bağımsız değişken olduğunu fark edin. 
    - API geri çağırmaları, bu durumda `num1string` ve `num2string`' de birden çok giriş götürebileceğini unutmayın.
- `ClearEntities` Geri arama, Kullanıcı başka bir sayı girebilmesi için sayı varlığını temizler. 
    - API çağrılarının varlıkları nasıl işleyebileceği gösterilmektedir.

### <a name="actions"></a>Eylemler
Dört eylem oluşturduk. Üçüne "bekleme olmayan" API eylemleri, üçüncü olarak, diğer öğreticilerde gördük gibi bir soruyu kullanıcıya soran bir "metin" Eyleidir. Her birinin nasıl oluşturulduğunu görmek için aşağıdakileri yapın:
1. Sol bölmede, "eylemler" e tıklayın ve ardından kılavuzda listelenen dört eylemden birine tıklayın.
2. Formun açılan formundaki her alanın değerine dikkat edin.
3. API alanının yanındaki düğmeye dikkat edin. `Refresh`
    - Botu durduruyoruz ve Kullanıcı arabirimi sayfası açıkken API 'lerde değişiklik yaparsanız, en son değişiklikleri almak için `Refresh` düğmeye tıklayabilirsiniz.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, çarpma ve rastgele selamlama
Bu eylemlerin üçü de API türüdür. Bunlar, bazı işleri gerçekleştirmek için API geri çağırma işlevlerini kullanır ve muhtemelen kullanıcıya sunulacak bir değer döndürür.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"12 ile çarpmak istediğiniz sayı"
Bu, "metin" eylemi ve yalnızca kullanıcının bir sorusunu ister. Bu eylem, API geri aramalarından biriyle gerçekten etkileşime girmediği sürece, kullanıcıdan, API geri aramalarından birini kullanan "çarpma" eylemi tarafından kullanılabilecek bir varlık belleğine gidecek bir sayıyla yanıt vermesini ister.


### <a name="train-dialog"></a>Iletişim kutusunu eğitme

Bir "eğitim Iletişim kutusu" aracılığıyla yürülim.

1. Sol bölmede, ve `Train Dialogs` `New Train Dialog` ardından düğmesine tıklayın.
2. "Merhaba" yazın.
3. `Score Actions` düğmesine tıklayın.
4. `RandomGreeting` öğesini seçin. 
    - Bu, rastgele selamlama API çağrısını yürütür.
    - Bu, Kullanıcı yanıtı beklemez.
5. `What number to do you want to multiply by 12?` seçeneğini belirleyin
6. Bir sayı, herhangi bir sayı ve yalnızca bir sayı yazın.
    - Numaranız `number` varlık olarak otomatik olarak etiketlendiğine dikkat edin.
7. `Score Actions` düğmesine tıklayın.
8. `Multiply` Eylemi seçin.
    - Çarpma 'nın sonucunu 12 ' ye dikkat edin.
    - Belleğin hala girdiğiniz değeri içerdiğinden emin olun.`number`
9. `ClearEntities` Eylemi seçin.
    - İçin `number` varlık değerinin bellekten temizlendiğine dikkat edin.
10. `Save` düğmesine tıklayın.

Artık API geri çağırmaları, ortak desenlerinin nasıl kaydedileceği ve bağımsız değişkenlerin nasıl tanımlanacağını ve değerleri ile varlıkların nasıl ilişkilendirileceğini gördünüz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kartlar Bölüm 1](./15-cards.md)
