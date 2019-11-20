---
title: Conversation Learner modeline yönelik eğitime giriş-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner aracılığıyla önceki eğitimi dallandırma ve düzenlemeyle bir modeli eğitme hakkında bilgi edinin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c657025ce588363cf76ce10868d809a9aff69222
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705622"
---
# <a name="introduction-to-training"></a>Eğitimlere giriş

Bu öğreticide, bir modeli eğitmek, önceki eğitimi temel alan yeni bir eğitimin nasıl dallanması ve bir bot yanıtının onu değiştirmek için düzenlenmesinin temelleri gösterilmektedir.

## <a name="video"></a>Video

[![Eğitim öğreticisi önizlemesine giriş](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir

    npm run tutorial-general

## <a name="details"></a>Ayrıntılar

- Eylemler: Kullanıcı girişine bir bot yanıtı.
- Eğit Bir bot 'ın Kullanıcı girişine yanıt vermesini öğretireceğiz.
- Dallanma Yeni bir tren Iletişim kutusu içinde, orijinalle aynı şekilde başlayan, ancak konuşmayı farklı bir yönde alan bir Kullanıcı girişinin değiştirilmesi.

## <a name="steps"></a>Adımlar

### <a name="create-a-new-model"></a>Yeni model oluştur

1. Web Kullanıcı arabiriminde yeni model ' e tıklayın.
2. "Ad" için "esire bot" yazın. Sonra Oluştur ' a tıklayın.

### <a name="create-an-action"></a>Eylem oluşturma

1. Sol bölmede, "eylemler" e ve ardından "yeni eylem" düğmesine tıklayın.
2. "Bot 'un yanıtı" alanında "Merhaba!" yazın. Bugün bir fikir vermek istiyor musunuz?
    - Tüm diğer alanları ve onay kutularını varsayılan ayarında bırak.
3. Oluştur’a tıklayın.

### <a name="first-training-and-creating-another-action-while-training"></a>Eğitim sırasında ilk eğitim ve başka bir eylem oluşturma

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Merhaba" yazın. 
    - Bu, kullanıcının konuşmanın tarafına benzetir.
3. "Eylemleri Puanlama" düğmesine tıklayın.
4. "Merhaba!" yanıtını seçin Bugün bir fikir vermek istiyor musunuz?
5. Kullanıcı olarak, "Evet" ile yanıtlayın.
6. "Eylemleri Puanlama" düğmesine tıklayın.
7. "+ Action" düğmesine tıklayın. 
    - Bu, sizi tanıdık "eylem oluşturma" iletişim kutusuna götürür.
8. Bot 'un yanıtını "harika!" olarak yazın
9. Oluştur’a tıklayın.
10. Bot 'ın hemen yanıt verdiğini unutmayın.
11. "Kaydet" düğmesine tıklayın.

### <a name="branch-a-second-training-off-of-the-first-training"></a>Birinci eğitimin Ikinci bir eğitimi dalına dallan
1. İlk eğitimi özetleyen kılavuz satırına tıklayın. 
    - Bu, mevcut eğitimi görüntülemenizi ve düzenlemenizi sağlar.
2. "Evet" Kullanıcı yanıtı ' na tıklayın. 
    - Bu, düzenlemenin denetimini kullanıma sunar.
3. Dal simgesine tıklayın. 
    - Bu, yeni bir konuşmaya yönelik farklı kullanıcı girişi için bir istem getirir.
4. "Hayır" yazın, ENTER tuşuna basın veya "Oluştur" düğmesine tıklayın. 
    - Bu noktada, tren Iletişim kutusunun yeni bir örneğine sahip olursunuz, özgün bir tane değişmeden kalır.
5. "Eylemleri Puanlama" düğmesine tıklayın.
6. Botunuzun yanlış yanıtı üzerine tıklayın.
7. "+ Action" düğmesine tıklayın 
    - Böylece, bot 'ın ile yanıt vermesi için yeni bir eylem oluşturarız.
8. Bot 'un yanıtını "sorun yok!" olarak yazın. Harika bir gün! "
9. Oluştur’a tıklayın
10. Bot 'ın hemen yanıt verdiğini unutmayın.
11. "Kaydet" düğmesine tıklayın.

### <a name="test-the-trainings"></a>Eğitimleri test etme
1. Sol bölmede "günlük Iletişimleri" ve ardından "yeni günlük Iletişim" seçeneğine tıklayın.
2. "Merhaba" iletisini yazın. 
3. Bot 'ın eğitildiğimiz şekilde otomatik olarak yanıt verdiğini unutmayın.
4. Kullanıcı yanıtı "Evet" yazın.
5. Bot yanıtını fark edin, ilk eğitimin çalıştığını gösterir.
6. "Oturum zaman aşımı" düğmesine tıklayın. Bu, Conversation Learner yeniden başlamak istiyoruz ve bu işlemin gerçekleşmesinin gerçekleşmesinin göz ardı etmesinin ne olduğunu söyler.
7. "Merhaba" iletisini yazın. 
8. Bot 'ın eğitildiğimiz şekilde otomatik olarak yanıt verdiğini unutmayın.
9. Kullanıcı yanıtı "Hayır" yazın.
10. Bot yanıtını fark edin; ikinci eğitimin çalıştığını gösterir.
11. "Test bitti" düğmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bekleme ve bekleme olmayan eylemler](./03-wait-vs-nonwait-actions.md)
