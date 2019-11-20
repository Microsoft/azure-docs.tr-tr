---
title: Conversation Learner modeliyle varlıkları kullanma-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner modeliyle varlıkların nasıl kullanılacağını öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: cba12b6c09c1bdbf4e8f7841676a609c34109d93
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707332"
---
# <a name="introduction-to-entities"></a>Varlıklara giriş

Bu öğreticide varlıklar, niteleyen varlıklar, gerekli varlıklar ve kullanımları Conversation Learner içinde kullanıma sunulmuştur.

## <a name="video"></a>Video

[![Varlıklara giriş öğreticisi Önizleme](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Gereksinimler

Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir

    npm run tutorial-general

## <a name="details"></a>Ayrıntılar

Varlıklar, Kullanıcı aralarından ayıklama veya özel kodla atama yoluyla, bot 'ın görevini gerçekleştirmesi için gereken bilgi parçalarını yakalar. Varlıklar Ayrıca, açıkça "gerekli" veya "ayırt edici" olarak sınıflandırılmak üzere eylem kullanılabilirliğini kısıtlayabilir.

- Eylemin kullanılabilmesi için gerekli varlıkların modelin belleğinde mevcut olması gerekir
- Eylemin kullanılabilir olması için uygun olmayan varlıkların modelin belleğinde mevcut *olmaması* gerekir

Bu öğretici özel varlıklara odaklanır. Önceden eğitilen, çok değerli, Negatiftablo varlıkları ve programlı varlıklar diğer öğreticilerde tanıtılmıştır.

## <a name="steps"></a>Adımlar

### <a name="create-the-model"></a>Model oluşturma

1. Web Kullanıcı arabiriminde "yeni model" e tıklayın.
2. "Ad" alanına "IntroToEntities" yazın ve ENTER tuşuna basın.
3. "Oluştur" düğmesine tıklayın.

### <a name="entity-creation"></a>Varlık oluşturma

1. Sol bölmede, "varlıklar" ve ardından "yeni varlık" düğmesine tıklayın.
2. "Varlık türü" için "özel eğitimli" seçeneğini belirleyin.
3. "Varlık adı" için "şehir" yazın.
4. "Oluştur" düğmesine tıklayın.

> [!NOTE]
> ' Özel eğitilen ' varlık türü, diğer varlık türlerinin aksine bu varlığın eğitilbileceği anlamına gelir.

### <a name="create-the-actions"></a>Eylemleri oluşturma

1. Sol bölmede, "eylemler" e ve ardından "yeni eylem" düğmesine tıklayın.
2. "Bot 'un yanıtı..." alanına "istediğiniz şehri bilmiyorum" yazın.
3. "Niteleyen varlıklar" alanına "şehir" yazın.
4. "Oluştur" düğmesine tıklayın.

> [!NOTE]
> "Şehir" varlığının "niteleyen varlıklara" eklenmesi, "şehir" varlığı bot 'un belleğinde tanımlandığında bu eylemi bot 'ın önünde bulacaktır.

Şimdi ikinci bir eylem oluşturun.

1. Sol bölmede, "eylemler" e ve ardından "yeni eylem" düğmesine tıklayın.
2. "Bot 'un yanıtı..." alanı, "$city Hava durumu büyük olasılıkla Sunny." yazın.
3. "Oluştur" düğmesine tıklayın.

> [!NOTE]
> "Şehir" varlığı, yanıtta başvuruya göre gerekli varlıklar listesinde otomatik olarak eklenmiştir.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>Modeli eğitme

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Merhaba" yazın.
    - Bu, kullanıcının konuşmanın tarafına benzetir.
3. "Eylemleri puan" düğmesine tıklayın.
4. "Hangi şehri istediğinizi bilmiyorum" yanıtını seçin.
5. Kullanıcı olarak, "Seattle" ile yanıtlayın.
6. "Eylemleri puan" düğmesine tıklayın.
7. Yanıt ' ı seçin, "$city Hava durumu büyük olasılıkla Sunny."
8. "Kaydet" düğmesine tıklayın.

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Beklenen varlık](./05-expected-entity.md)
