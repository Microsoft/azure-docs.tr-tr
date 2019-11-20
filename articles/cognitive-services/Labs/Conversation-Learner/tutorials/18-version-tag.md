---
title: Conversation Learner modeliyle sürüm etiketlemesini kullanma-Azure bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner modeliyle sürüm oluşturma ve etiketleme kullanmayı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 0279363c039e3ec3c2deac3bc7f71c32c547e9d1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703373"
---
# <a name="how-to-use-version-tagging"></a>Sürüm etiketlemesini kullanma

Bu öğreticide, Conversation Learner modelinizin sürümlerinin nasıl etiketlenmiştir ve hangi sürümün "canlı" olarak ayarlanması gösterilmektedir.  

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, günlük Iletişim kutusu Web Kullanıcı arabirimi değil, günlük Iletişimleri oluşturmak için bot Framework öykünücüsünün kullanılması gerekir.  

Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir:

    npm run tutorial-general

## <a name="details"></a>Ayrıntılar

Modelin etiketli sürümleri statiktir; bunları düzenleyemez veya değiştiremezsiniz. Modelinizi düzenlediğinizde, her zaman ana sürümü düzenlensiniz. Yeni bir etiket eklediğinizde Conversation Learner bu noktada modelin anlık görüntüsünü yakalar. 

Bot, "canlı" sürüm olarak seçtiğiniz modelin sürümünü kullanacaktır, ancak sahip olduğu tüm konuşmalar yalnızca "Düzenle etiketi" "ana" olarak ayarlandığında görüntülenebilir. Modelin "biçim etiketi" özelliği "ana" dışında bir şeye ayarlandıysa, modelin anlık görüntüsünü görüntüleyebilir, ancak bunu herhangi bir şekilde değiştiremezsiniz.

## <a name="steps"></a>Adımlar

### <a name="install-the-bot-framework-emulator"></a>Bot Framework öykünücüsünü yükler

1. [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator) kısmına gidin.
2. Öykünücüyü indirin ve yükleyin.

### <a name="create-a-model"></a>Model oluşturma

1. Model listesi giriş sayfasında, `New Model` düğmesine tıklayın.
2. "Öğretici-18-sürüm oluşturma" alantüründe,ENTERtuşunabasın.`Name`
4. Sol bölmede "Ayarlar" a tıklayın.
5. CONVERSATION_LEARNER_MODEL_ID alanının içeriğini panoya kopyalayın.

### <a name="configure-the-emulator"></a>Öykünücüyü yapılandırma

1. Conversation Learner kök klasöründe, ". env" dosyasını açın.
2. ". Env" dosyasına aşağıdaki gibi bir satır ekleyin:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Conversation Learner hizmetini komut isteminden çıkıp yeniden çalıştırarak:
    - `npm run tutorial-general`
4. Bot Framework öykünücüsü ' nde, yeni bir bot yapılandırması oluşturun, uç nokta URL 'sini olarak ayarlayın`http://localhost:3978/api/messages`

### <a name="version-1"></a>Sürüm 1

Sürüm 1 için tek bir eylem oluşturacağız.

1. Web Kullanıcı arabiriminin sol panelinde, "eylemler" e tıklayın ve ardından `New Action` düğmeye tıklayın.
2. "Bot 'un yanıtı" alanında "Merhaba (sürüm 1)" yazın.
3. `Save` düğmesine tıklayın.

Şimdi bunu modelin "sürüm 1" olarak etiketliyoruz.

1. Sol panelde "Ayarlar" a tıklayın, ardından tıklamalısınız ![](../media/tutorial18_version_tags.PNG) `New Tag` düğmesini göstermek için "sürüm etiketleri" simgesine tıklayın.
    - "Sürüm 1" olarak adlandırın
1. "Canlı etiket" açılan penceresinde "sürüm 1" i seçin.  
    - Şimdi bu bot kullanan kanallar modelinizin "sürüm 1" i kullanacaktır.
    - Bu sürüm 1 modelinin varlıklar, Eylemler ve tren Iletişim kutuları artık değiştirilemez.
    - "Sürüm 1" öğesini "düzenleme etiketi" olarak seçerseniz, yalnızca modeli görüntüleyebilir ve düzenleyemezsiniz.
    - "Düzenleme etiketi" ni "ana" olarak ayarlayın, modelin düzenlenebilecek tek sürümüdür.

Şimdi "sürüm etiketleri" kılavuzunda "sürüm 1" i göreceksiniz.

### <a name="version-2"></a>Sürüm 2

Şimdi, modelimizi 1. sürümden ayırt etmek için düzenliyoruz.

1. Sol panelde "eylemler" e tıklayın.
2. Eylemler kılavuzunda "Merhaba (sürüm 1)" seçeneğine tıklayın.
3. "Bot 'un Response" alanını "Merhaba (sürüm 2)" olarak değiştirin.
4. `Save` düğmesine tıklayın.
5. `New Action` düğmesine tıklayın.
6. "Bot 'un yanıtı" alan türünde, "bye bye (sürüm 2)".

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Bot Framework öykünücüsünün sürüm 1 kullandığını onaylayın

1. Bot Framework öykünücüsünde "Selam" iletisini yazın.
2. Bot 'un "Merhaba (sürüm 1)" ile yanıt verdiğini unutmayın.
    - Bu, sürüm 1 ' in "canlı" olduğunu doğrular.

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Conversation Learner Web Kullanıcı arabirimindeki konuşma günlüklerini görüntüleme

1. Sol panelde "günlük Iletişim kutularına" tıklayın
    - Hiçbir iletişim kutusu görmüyorsanız Yenile düğmesine tıklayın.
2. Kılavuzdaki "sürüm 1" etiketine dikkat edin.
3. Kılavuzda "Merhaba (sürüm 1)" seçeneğine tıklayın.

> [!NOTE]
> Şu anda kullanılabilir olan tüm Conversation Learner işlevlerinden seçim yaparak düzeltmeler yapabiliriz, ancak bu düzenlemeler ana sürüm 1 ' e uygulanmaz.

Artık sürüm oluşturmanın nasıl çalıştığını ve bot Framework öykünücüsünü kullanarak bot ile nasıl etkileşim kurabileceğinizi gördünüz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Varlıkları numaralandırma ve varlık eylemlerini ayarlama](./tutorial-enum-set-entity.md)
