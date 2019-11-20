---
title: Conversation Learner modeliyle oturum geri çağırmaları kullanma-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner modeliyle oturum geri çağırmaları kullanmayı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 656d028082d56b8f094e83363e5189b163581c53
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703955"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Conversation Learner modeliyle oturum geri çağırmaları kullanma

Bu öğretici oturumları, bunların nasıl işlendiğini ve Conversation Learner onSessionStart ve onSessionEnd geri çağırmaları tanıtır.

## <a name="video"></a>Video

[![Oturum geri çağırmaları öğretici Önizleme](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, "tutorialSessionCallbacks" bot 'ın çalışıyor olması gerekir.

    npm run tutorial-session-callbacks

## <a name="details"></a>Ayrıntılar
Bu öğreticide bir oturum kavramı, oturumların varsayılan olarak nasıl işlendiği ve bu davranışı nasıl geçersiz kılabileceğiniz ele alınmaktadır.

Conversation Learner bir oturum, bot ile birlikte, kesintisiz etkileşimli bir Exchange 'i temsil eder. Oturumlar birden çok dönüşe sahip olabilir, ancak Otuz dakikadan büyük bir süre sonra işlem yapılmadan bu nedenle program aracılığıyla sona erer.  Bu varsayılan oturum zaman aşımı süresini değiştirme hakkında bilgi için "sınırlar" sayfasındaki yardım sayfasına bakın.

Bu uzun süre eylemsizlik süresi, bot 'ın yeni bir oturum oluşturmasına ve yinelenen sinir ağını ilk durumuna sıfırlamasına neden olur. Varsayılan olarak, tüm varlık değerleri temizlenir. Bu varsayılan, varlık değerlerini temizleme davranışı aşağıda görüldüğü şekilde değiştirilebilir.

### <a name="load-the-demo-model"></a>Demo modelini yükleme

Web Kullanıcı arabiriminde "öğreticileri Içeri aktar" düğmesine tıklayın ve "öğretici-13-SessionCallbacks" adlı modeli seçin.

### <a name="code-for-the-callbacks"></a>Geri çağırmalar için kod

Bu modelin iki geri çağırma kodu için örnek kod içinde bulunabilir: `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: Bu yöntem BotName varlığını ayarlar.
- OnSessionEndCallback: neleri korumak istediğinizi belirtebilirsiniz. Bu, Kullanıcı adı ve Kullanıcı telefonu dışındaki tüm varlıkları temizler.

Her geri çağırma isteğe bağlıdır.

### <a name="actions"></a>Eylemler

Modelde dört eylem tanımlanmıştır. Mevcut eylemler, "eylemler" için kılavuz görünümünde görüntülenir

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Son oturum eylemi oluşturma (geri çağırma çağrısı için)

1. Sol bölmede, "eylemler" e ve ardından "yeni eylem" düğmesine tıklayın.
2. "Varlık türü" için "ENDSESSION" seçeneğini belirleyin.
3. "Veri..." alan, "bitti" yazın
4. "Oluştur" düğmesine tıklayın.

### <a name="edit-an-existing-action"></a>Mevcut bir eylemi düzenleme

1. Kılavuz görünümünden "$UserName, $UserLocation olursunuz" eylemini seçin.
2. "Yanıt bekle" onay kutusunun işaretini kaldırın.
3. "Kaydet" düğmesine tıklayın.

### <a name="chaining-actions"></a>Zincirleme eylemleri

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Merhaba" yazın.
3. "Eylemleri puan" düğmesine tıklayın.
4. "Merhaba, I. Botty" yanıtını seçin. Adınız nedir? "
5. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "
6. Yanıt, "Merhaba" i seçin. Telefon numaranız nedir? "
7. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "555-555-5555" yazın.
8. "Eylemleri puan" düğmesine tıklayın.
9. Yanıt ' ı seçin, "konumunuzu, sizin konumlarınızı söyleyebilir misiniz?"
10. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Seattle" yazın
11. "Eylemleri puan" düğmesine tıklayın.
12. "Bu durumda," Seattle 'da olduğunuz) yanıtı seçin
13. "Bitti" yanıtını seçin
14. "Kaydet" düğmesine tıklayın.

### <a name="testing-the-model"></a>Modeli test etme

1. Sol bölmede "günlük Iletişimleri" ve ardından "yeni günlük Iletişim" düğmesine tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Merhaba" yazın
3. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "
4. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "555-555-5555" yazın.
5. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Seattle" yazın
    - Bu noktada, konum hariç tüm varlık değerleri korunmuş olmalıdır.
6. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Merhaba" yazın
7. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Detroit" yazın
8. "Oturum zaman aşımı" düğmesine tıklayın.
    - Bu düğmeye tıkladığınızda, bot 'ın uzun dönem sürelerine olan yanıtı alıştırmaları yapılır
9. "Tamam" düğmesine tıklayın.
10. "Test bitti" düğmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [API çağrıları](./14-api-calls.md)
