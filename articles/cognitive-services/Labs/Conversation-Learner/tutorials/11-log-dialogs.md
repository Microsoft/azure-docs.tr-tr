---
title: Conversation Learner modelinde iletişim kutularını günlüğe kaydetme-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner modelinde iletişim kutularını günlüğe kaydetme hakkında bilgi edinin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 388942e8195048d7528a54e5a290f1724c8e876b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703966"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Conversation Learner modelinde iletişim kutularını günlüğe kaydetme

Bu öğreticide, gerçek dünya kullanıcıları ile kayıtlı etkileşimlerden Conversation Learner modellerini daha iyi eğitebilmeniz için günlük Iletişim kutularının nasıl çalıştığı gösterilmektedir.

## <a name="video"></a>Video

[![Günlük Iletişimleri öğretici Önizleme](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir

    npm run tutorial-general

ve daha önceki öğreticilerde oluşturulan hava durumu modeli.

## <a name="details"></a>Ayrıntılar
Günlük Iletişimleri, son kullanıcılarla botunuzun etkileşiminizi kaydeder. Bu günlük Iletişim kutularını, modelin performansını ve genel sistem performansını geliştirmek için varlık etiketlerini ve eylem seçimlerini düzeltirecek şekilde çözebilirsiniz.

## <a name="steps"></a>Adımlar

Web Kullanıcı arabiriminde "öğreticileri Içeri aktar" düğmesine tıklayın ve "öğretici-11-Logiletişimler" adlı modeli seçin.

Bu modelde "City" adlı bir varlık ve bu şehirdeki hava durumu hakkında yanıt vermek için tasarlanan eylemler bulunur. Performansı beklentileri biraz düşük olacak şekilde modeli eğitmek için iki eğitim Iletişim kutusu kullanıldı. Model, ek eğitim ve gerçek dünya kullanıcı etkileşimlerine maruz kalmayı geliştirir.

### <a name="create-a-new-conversation"></a>Yeni bir konuşma oluşturun

1. Sol bölmede "günlük Iletişimleri" ve ardından "yeni günlük Iletişim" düğmesine tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Austin Hava durumu tahmini" yazın
3. "Test bitti" düğmesine tıklayın.
4. Listeden "Austin Hava durumu tahmini" günlük iletişim kutusuna tıklayın.
5. Sohbet panelinde "Austin Hava durumu tahmini" deterine tıklayın.
6. "Austin" ye ve ardından varlık listesinden "şehir" e tıklayın.
7. "Değişiklikleri Gönder" düğmesine tıklayın.
    - Bu varlık değerindeki değişiklik, bellekte yeni varlık değerleri bulunduğundan, aşağı akış değişikliklerine neden olur. Daha sonraki eylemler büyük olasılıkla "şehir" varlığını içeren özellikle geçersiz hale geldi.
8. "Hangi şehir?" e tıklayın sohbet panelinde söylenişi.
9. Yanıt ' ı seçin, "Austin 'deki Hava durumu büyük olasılıkla sunank ' dır.
10. "Tren olarak kaydet Iletişim kutusu" düğmesine tıklayın.
    - Eğitim hemen açılır

![](../media/T11_logdialog.png)

Son bir notunuz. İş ihtiyaçlarına bağlı olarak, görüşme günlüğü özelliği ayarlar ' a giderek ve "günlük konuşmaları" olarak denetlenerek kapatılabilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Varlık algılama geri çağırma](./12-entity-detection-callback.md)
