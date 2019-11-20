---
title: Conversation Learner-Microsoft bilişsel hizmetler ile alternatif girdileri kullanma | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner ile alternatif girişleri nasıl kullanacağınızı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7416939db8d6552f79fba700b8432de2ad228846
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704209"
---
# <a name="how-to-use-alternative-inputs"></a>Alternatif girişleri kullanma

Bu öğreticide, eğitim arabirimindeki Kullanıcı utsları için alternatif girişler alanının nasıl kullanılacağı gösterilmektedir.

## <a name="video"></a>Video

[![Alternatif girişler öğretici önizlemesi](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Gereksinimler
Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir

    npm run tutorial-general

## <a name="details"></a>Ayrıntılar
Alternatif girişler, kullanıcının eğitim iletişim kutusunda belirli bir noktada söylebileceği, anlamsal olarak eşdeğer Kullanıcı dıklardır. Bu alternatif girişler, her bir çeşitlemeyi ayrı eğitim iletişim kutularında ele almanız gerekmeden daha fazla sıkı çeşitliliğe sahip olmanızı sağlar.

## <a name="steps"></a>Adımlar

### <a name="create-the-model"></a>Model oluşturma

1. Web Kullanıcı arabiriminde "yeni model" e tıklayın.
2. "Ad" alanına "Alternativegirişleri" yazın ve ENTER tuşuna basın.
3. "Oluştur" düğmesine tıklayın.

### <a name="entity-creation"></a>Varlık oluşturma

1. Sol bölmede, "varlıklar" ve ardından "yeni varlık" düğmesine tıklayın.
2. "Varlık türü" için "özel eğitimli" seçeneğini belirleyin.
3. "Varlık adı" için "şehir" yazın.
4. "Oluştur" düğmesine tıklayın.

![](../media/T10_actions.png)

Şimdi üç eylem oluşturalım.

### <a name="create-the-first-action"></a>Ilk eylemi oluşturma

1. Sol bölmede, "eylemler" e ve ardından "yeni eylem" düğmesine tıklayın.
2. "Bot 'un yanıtı..." alanına "hangi şehir?" yazın
3. "Kullanıcı yanıtında beklenen varlık..." alanına "şehir" yazın.
4. "Ayırt edici sahibine" alanına "şehir" yazın.
5. "Oluştur" düğmesine tıklayın.

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>Ikinci eylemi oluşturma

1. Sol bölmede, "eylemler" e ve ardından "yeni eylem" düğmesine tıklayın.
2. "Bot 'un yanıtı..." alanına, "$city Hava durumu büyük olasılıkla Sunny." yazın.
3. "Oluştur" düğmesine tıklayın.

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>Üçüncü eylemi oluşturma

1. Sol bölmede, "eylemler" e ve ardından "yeni eylem" düğmesine tıklayın.
2. "Bot 'un yanıtı..." alanına "hava durumunu sormayı deneyin" yazın.
3. "Ayırt edici sahibine" alanına "şehir" yazın.
4. "Oluştur" düğmesine tıklayın.

![](../media/T10_action_create_3.png)

Artık üç eylemleriniz vardır.

![](../media/T10_actions.png)

### <a name="train-the-model"></a>Modeli eğitme

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Hava durumu nedir?" yazın.
3. "Eylemleri puan" düğmesine tıklayın.
4. "Hangi şehir?" yanıtını seçin
5. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Denver" yazın
6. "Eylemleri puan" düğmesine tıklayın.
7. Yanıt ' ı seçin, "Denver 'teki Hava durumu büyük olasılıkla Sunny."
8. "Değişiklikleri Gönder" düğmesine tıklayın.

![](../media/T10_training_1.png)

Başka bir tren iletişim kutusu oluşturarak modeli daha fazla eğelim.

### <a name="second-model-train-dialog"></a>İkinci model eğitimi Iletişim kutusu

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "ne yapabilirsiniz?" yazın.
3. "Eylemleri puan" düğmesine tıklayın.
4. "Hava durumu sorulmayı dene" yanıtını seçin.
5. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Seattle 'daki Hava durumu nedir?" yazın.
6. "Seattle" düğmesine ve ardından varlık listesinden "şehir" e tıklayın.
7. "Eylemleri puan" düğmesine tıklayın.
8. Yanıt ' ı seçin, "Seattle 'daki Hava durumu büyük olasılıkla sunank ' dır.
9. "Değişiklikleri Gönder" düğmesine tıklayın.

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>Alternatif giriş kullanarak üçüncü model eğitme Iletişim kutusu

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "yardım" yazın.
3. "Eylemleri puan" düğmesine tıklayın.
    - Model en iyi seçeneğe göre belirlenir, bu nedenle varsayılan olarak en yüksek yüzdebirlik 'ü seçer.
4. "Öğretme" düğmesine ve ardından "Onayla" düğmesine tıklayın.

![](../media/T10_training_3.png)

Alternatif girişleri kullanarak sistemi daha iyi ayarlayalim. Eğitim sırasında veya daha sonra alternatif giriş ekleyebilirsiniz.

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine tıklayın ve ardından "ne yapabilirsiniz?" i seçin. Eğitim Iletişim kutusu listesinden.
1. "Ne yapabilirsiniz?" düğmesine tıklayın. sohbet panelinde söylenişi.
1. "Alternatif giriş Ekle..." alanına "yardım" yazın ve ENTER tuşuna basın.
1. "Değişiklikleri Kaydet" düğmesine tıklayın.

![](../media/T10_training_4.png)

Houston işlemek için başka bir alternatif giriş ekleyelim.

1. "Seattle 'daki Hava durumu nedir?" düğmesine tıklayın. sohbet panelinde söylenişi.
1. "Alternatif giriş Ekle..." alanına "Houston için tahmin" yazın ve ENTER tuşuna basın.
   - Hata iletisi, olgu alternatif girişlerinin anlam ile eşdeğer olması ve özgün utterde ile aynı varlıkları içermesi gerektiğini vurgular; yalnızca aynı varlık değerleri değil. Aynı varlıkların varlığı gereklidir.
1. "Houston" düğmesine tıklayın ve varlıklar listesinden "şehir" i seçin.
1. "Alternatif giriş Ekle..." alanına "Seattle için tahmin" yazın ve ENTER tuşuna basın.
1. "Seattle" düğmesine tıklayın ve varlıklar listesinden "şehir" i seçin.
1. "Değişiklikleri Kaydet" düğmesine tıklayın.
1. "Düzenlemeyi Kaydet" düğmesine tıklayın.

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>Modeli test etme

1. Sol bölmede "günlük Iletişimleri" ve ardından "yeni günlük Iletişim kutusu" na tıklayın.
2. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "yardım bana" yazın
3. Sohbet panelinde "iletinizi yazın..." ifadesini yazıp "Denver için tahmin" yazın.

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Günlük iletişim kutuları](./11-log-dialogs.md)
