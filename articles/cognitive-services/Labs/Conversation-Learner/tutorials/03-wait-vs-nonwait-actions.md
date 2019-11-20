---
title: Conversation Learner modeliyle bekleme ve bekleme olmayan eylemleri kullanma-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner modeliyle bekleme ve bekleme olmayan eylemleri nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 5d9a67f0b413b624baec369a219547cf18e9dbd2
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705595"
---
# <a name="wait-and-non-wait-actions"></a>Bekleme ve bekleme olmayan eylemler

Bu öğretici, Conversation Learner bekleme eylemleri ve bekleme olmayan eylemler arasındaki farkı gösterir.

## <a name="video"></a>Video

[![Bekleme ve bekleme olmayan öğreticinin önizlemesi](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir

    npm run tutorial-general

## <a name="details"></a>Ayrıntılar

- Bekleme eylemi: Sistem "bekleme" eylemini gerçekleştirdikten sonra, eylem almayı durdurur ve Kullanıcı girişini bekler.
- Bekleme olmayan eylem: Sistem "bekleme olmayan" bir eylem gerçekleştirdikten sonra, hemen başka bir eylem seçer (Kullanıcı girişini beklemeden).

## <a name="steps"></a>Adımlar

### <a name="create-a-new-model"></a>Yeni model oluştur

1. Web Kullanıcı arabiriminde yeni model ' e tıklayın.
2. "Ad" alanında "bekleme dışı bekleme" yazın, ENTER tuşuna basın veya "Oluştur" düğmesine tıklayın.

### <a name="create-the-first-two-wait-actions"></a>İlk iki bekleme eylemini oluşturun

1. Sol bölmede, "eylemler" e ve ardından "yeni eylem" düğmesine tıklayın.
2. "Bot 'un yanıtı..." alanına "hangi pizza istersiniz?" yazın.
    - Bu bir bekleme eylemlidir, bu nedenle "yanıt bekle" kutusunu işaretli olarak bırakın.
3. "Oluştur" düğmesine tıklayın.
4. Bu adımları tekrarlayarak, "Şu şekilde pizza" ile başka bir eylem oluşturun. Bot 'un yanıtı olarak.

### <a name="train-using-those-wait-actions"></a>Bu bekleme eylemlerini kullanarak eğitme

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Merhaba" yazın. 
    - Bu, kullanıcının konuşmanın tarafına benzetir.
3. "Eylemleri puan" düğmesine tıklayın.
4. "Hangi pizza istersiniz?" yanıtını seçin.
5. Kullanıcı olarak, "Margherita" ile yanıtlayın.
6. "Eylemleri puan" düğmesine tıklayın.
7. "Şu şekilde pizza!" yanıtını seçin.
8. "Kaydet" düğmesine tıklayın.

### <a name="create-a-non-wait-action-while-training"></a>Eğitim sırasında bekleme olmayan bir eylem oluşturun
Daha önce yaptığınız gibi bekleme olmayan eylemi oluşturabilseniz de, bir eğitim oturumu içinden de oluşturabilirsiniz.
1. "Yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Kullanıcı olarak, "Merhaba" yazın.
3. "Eylemleri puan" düğmesine tıklayın.
4. "+ Action" düğmesine tıklayın. 
    - Bu, sizi tanıdık "eylem oluşturma" iletişim kutusuna götürür.
5. Bot 'un yanıtını ", pizza bot 'a hoş geldiniz!" olarak yazın
6. "Yanıt bekle" onay kutusunun işaretini kaldırın.
7. "Oluştur" düğmesine tıklayın.
    - Bot 'ın "Pizza bot 'a hoş geldiniz!" ile hemen yanıt verdiğini unutmayın. daha önce başka bir bot yanıtı girmeniz istenir. Bunun nedeni, bot 'un yanıtının yeni oluşturduğumuz bekleme olmayan bir Işlemdir.
9. "Hangi pizza istersiniz?" yanıtını seçin.
10. Kullanıcı olarak, "Margherita" ile yanıtlayın.
11. "Eylemleri puan" düğmesine tıklayın.
12. "Şu şekilde pizza!" yanıtını seçin.
13. "Kaydet" düğmesine tıklayın.

> [!NOTE]
> Bekleme ve bekleme olmayan eylemlere karşılık gelen bot yanıtlarının sırası.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Varlıklara giriş](./04-introduction-to-entities.md)
