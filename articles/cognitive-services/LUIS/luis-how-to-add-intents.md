---
title: Amaç Ekle-LUSıS
titleSuffix: Azure Cognitive Services
description: Aynı amaçları içeren soruların veya komutların gruplarını belirlemek için LUSıS uygulamanıza amaçlar ekleyin.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 66a3350dee60772ce706af8995179dcd8c485b64
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904302"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Kullanıcı amaçlarını belirleme amacını ekleyin

Aynı amaca sahip olan soruların veya komutların gruplarını belirlemek için LUSıS uygulamanıza [amaçlar](luis-concept-intent.md) ekleyin. 

Amaçlar, üst gezinti çubuğunun **Build** bölümünden ve ardından sol bölmenin amaçlarından **yönetilir.** 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Amaç ekleme

1. [Lusıs Önizleme portalında](https://preview.luis.ai), amaçları görüntülemek için **Oluştur** ' u seçin. 
1. **Amaçlar** sayfasında **+ Oluştur**' u seçin.
1. **Yeni amaç oluştur** iletişim kutusunda, amaç adını girin, örneğin `ModifyOrder`ve **bitti**' yi seçin.

    > [!div class="mx-imgBorder"]
    > ![amaç Ekle](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Amaç, örnek söyleyeni gerektirir.

## <a name="add-an-example-utterance"></a>Örnek bir mekan ekleyin

Örnek söyleyler, Kullanıcı sorularının veya komutlarının metin örnekleridir. Bu amacı tahmin etmek için Language Understanding (LUSıS) öğretmek için bir amaca yönelik örnek ekleme yapmanız gerekir. Amacınızı anlamak için, en fazla 15 ila 30 örnek araslar aralığında LUYA gerekir. Toplu olarak örnek eklemeyin. Her bir söylemeyle, zaten amaç içinde olan örneklerden farklı olduğu için dikkatle seçilmelidir. 

1. Amaç ayrıntıları sayfasında, kullanıcılarınızın altındaki metin kutusunda `Deliver a large cheese pizza` gibi, kullanıcılarınız için beklediğinizi ilgili bir yer girin ve ardından ENTER tuşuna basın.
 
    > [!div class="mx-imgBorder"]
    > Amaç Ayrıntıları sayfasının ekran görüntüsünü ![, söylenişi vurgulu](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUO, tüm aralıkları küçük harfe dönüştürür ve kısa çizgiler gibi [belirteçlerin](luis-language-support.md#tokenization) etrafına boşluk ekler.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Amaç tahmin hataları 

Bir amaca göre örnek, bu örnekte yer aldığı amaç ve eğitim sırasında belirlenen amaç arasında bir amaç tahmin hatası olabilir. 

Söylenişi tahmin hatalarını bulmak ve bunları sabitlemek için, **ayrıntılı görünüm**' ün **Görünüm** seçeneğiyle yanlış ve belirsiz olarak birleştirilmiş **filtre** seçeneklerini kullanın. 

![Söylenişi tahmin hatalarını bulmak ve bunları onarmak için filtre seçeneğini kullanın.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Filtreler ve görünüm uygulandığında ve hatalar ile ilgili örnek bir sorun oluşursa, örnek bir liste, söz konusu konuları ve sorunları gösterir.

> [!div class="mx-imgBorder"]
> ![! [Filtreler ve görünüm uygulandığında ve hatalar ile ilgili örnekler varsa, örnek utterler listesi, bu noktalar ve sorunları gösterir.] (./Media/Luis-How-to-add-BITS/Find-Errors-ın-utterances.exe)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Her satır, bu iki puandaki fark olan en yakın Rival 'in puanı olan utterance için geçerli eğitimin tahmin Puanını gösterir. 

### <a name="fixing-intents"></a>Amaçlar düzeltiliyor

Amaç tahmin hatalarını nasıl düzelteceğinizi öğrenmek için [Özet panosunu](luis-how-to-use-dashboard.md)kullanın. Özet Panosu, etkin sürümün son eğitimi için analiz sağlar ve modelinizi gidermeye yönelik en önemli önerileri sunar.  

## <a name="using-the-contextual-toolbar"></a>Bağlamsal araç çubuğunu kullanma

Bağlam araç çubuğu diğer eylemleri sağlar:

* Örnek, düzenleme veya silme
* Farklı bir amaca örnek olarak yeniden atama
* Filtreler ve görünümler: yalnızca filtrelenmiş varlıkları içeren detersliği göster veya isteğe bağlı ayrıntıları görüntüle
* Örnek söyleylerini arayın

## <a name="train-your-app-after-changing-model-with-intents"></a>Bir modeli amaçlar ile değiştirdikten sonra uygulamanızı eğitme

Hedefleri ekledikten, düzenledikten veya kaldırdıktan sonra, değişikliklerinizin uç nokta sorgularına uygulanması için uygulamanızı [eğitin](luis-how-to-train.md) ve [yayımlayın](luis-how-to-publish-app.md) . Her tek değişiklikten sonra eğmeyin. Bir değişiklik grubundan sonra eğitme. 

## <a name="next-steps"></a>Sonraki adımlar

Varlıklarla [örnek](luis-how-to-add-example-utterances.md) ekleme hakkında daha fazla bilgi edinin. 
