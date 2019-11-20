---
title: Conversation Learner eylemlerinin "beklenen varlık" özelliğini kullanma-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Bir Conversation Learner modelinin "beklenen varlık" özelliğini nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 5fbe4f09d377b9f157368184ab26341782e9aed1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707316"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Eylemlerin "beklenen varlık" özelliğini kullanma

Bu öğreticide, eylemlerin "beklenen varlık" özelliği gösterilmektedir.

## <a name="video"></a>Video

[![Beklenen varlık öğreticisi önizlemesi](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir

    npm run tutorial-general

## <a name="details"></a>Ayrıntılar
Kullanıcının bu eyleme olan yanıtını bir varlığa kaydetmek için bir eylemin "beklenen varlık" özelliğini kullanın.

Bir eylemin "beklenen varlık" özelliğine varlık eklerken, sistem şunları olur:

1. Makine öğrenimi tabanlı varlık ayıklama modelini kullanarak varlıkları eşleştirmeye çalışırken başlayın
2. Hiçbir varlık bulunmazsa, buluşsal yöntemler temelinde $entity tüm Kullanıcı utterliğini atayın
3. Çağırın `EntityDetectionCallback`ve eylem seçimine devam edin.

## <a name="steps"></a>Adımlar

### <a name="create-the-model"></a>Model oluşturma

1. Web Kullanıcı arabiriminde "yeni model" e tıklayın.
2. "Ad" alanına "ExpectedEntities" yazın ve ENTER tuşuna basın.
3. "Oluştur" düğmesine tıklayın.

### <a name="entity-creation"></a>Varlık oluşturma

1. Sol bölmede, "varlıklar" ve ardından "yeni varlık" düğmesine tıklayın.
2. "Varlık türü" için "özel eğitimli" seçeneğini belirleyin.
3. "Varlık adı" için "ad" yazın.
4. "Oluştur" düğmesine tıklayın.

> [!NOTE]
> ' Özel eğitilen ' varlık türü, diğer varlık türlerinin aksine bu varlığın eğitilbileceği anlamına gelir.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Ilk eylemi oluşturma

1. Sol bölmede, "eylemler" e ve ardından "yeni eylem" düğmesine tıklayın.
2. "Bot 'un yanıtı..." alanına "adınız nedir?" yazın.
3. "Beklenen varlıklar" alanına "ad" yazın.
4. "Oluştur" düğmesine tıklayın.

> [!NOTE]
> Bu eylem seçilirse, kullanıcının yanıtından algılanan ve ayıklanan varlıklar "ad" varlığına kaydedilir. Hiçbir varlık algılanmazsa, tüm yanıt bu varlığa kaydedilir.

### <a name="create-the-second-action"></a>Ikinci eylemi oluşturma

1. Sol bölmede, "eylemler" e ve ardından "yeni eylem" düğmesine tıklayın.
2. "Bot 'un yanıtı..." alan, "Hi $name!" yazın
3. "Oluştur" düğmesine tıklayın.

> [!NOTE]
> "Ad" varlığı yanıtta başvuruya göre otomatik olarak "gerekli varlıklar" olarak eklenmiştir.

Artık iki eylemleriniz vardır.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>Modeli eğitme

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Merhaba" yazın.
    - Bu, kullanıcının konuşmanın tarafına benzetir.
3. "Eylemleri puan" düğmesine tıklayın.
4. "Adınız nedir?" yanıtını seçin
    - "Hi $name!" Bu yanıt "ad" varlığının modelin belleğinde şimdi tanımlanmasını gerektirdiğinden yanıt seçilemiyor.
5. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "filiz" yazın.
    - "Filiz", yanıtı varlık olarak kaydetmek için daha önce ayarladığımız buluşsal yöntemi temel alarak bir varlık olarak vurgulanır.
6. "Eylemleri puan" düğmesine tıklayın.
    - "Ad" varlığı artık modelin belleğinde "filiz" olarak tanımlanmıştır, bu nedenle "Merhaba $name" eylemi bir eylem olarak seçilebilir.
7. "Hi $name!" yanıtını seçin
8. "Kaydet" düğmesine tıklayın.

Alternatif girişleri ekleme modeli daha fazla.

1. "Alternatif giriş Ekle..." alanına "I Ari" yazın.
    - Model, adı bir varlık olarak tanımıyor, bu nedenle tüm metin bloğunu varlığın değeri olarak seçer
2. "I neşeli" tümceciğinin ardından çöp kutusu simgesine tıklayın.
3. "Joo" düğmesine tıklayın ve ardından varlık listesinden "ad" a tıklayın.
4. Puanlama eylemleri ' ne tıklayın.
5. "Merhaba filiz!" yanıtını seçin
6. "Kaydet" düğmesine tıklayın.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Negatiftablo varlıkları](./06-negatable-entities.md)
