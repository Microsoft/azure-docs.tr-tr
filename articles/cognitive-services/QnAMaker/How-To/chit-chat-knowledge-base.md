---
title: Soru-Cevap Oluşturma Bilgi Bankası 'na sohbet ekleme
titleSuffix: Azure Cognitive Services
description: Botunuza kişisel Chit sohbeti eklemek, bir KB oluşturduğunuzda daha fazla konuşma ve ilgi çekici hale getirir. Soru-Cevap Oluşturma, en üst chit-sohbet ' i KB 'nize kolayca eklemenizi sağlar.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 1208d95a282ca6e236d9d6be8013f51dead90d13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94376663"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Bilgi Bankası 'na sohbet ekleme

Botunuza sohbet eklemek, daha fazla konuşma ve ilgi çekici hale getirir. Soru-cevap Oluşturucu 'daki CHIT-chat özelliği, bilgi Tabanınızla (KB) en yüksek bir üst baskı sohbetini kolay bir şekilde eklemenizi sağlar. Bu, bot 'ın kişiliğine yönelik bir başlangıç noktası olabilir ve bu, bunları sıfırdan yazmanın zaman ve maliyetini kaydeder.

Bu veri kümesinde, profesyonel, kolay ve WIT gibi birden çok kişinin sesinde, tek BT-sohbet için 100 senaryo vardır. Bot sesinize en yakından benzeyen kişiyi seçin. Bir Kullanıcı sorgusu verildiğinde Soru-Cevap Oluşturma, en yakın bilinen CHIT-chat QnA ile eşleştirmeye çalışır.

Farklı kişiselleştirimler için bazı örnekler aşağıda verilmiştir. Tüm kişilik [veri kümelerini](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) , kişiselleştirimler hakkındaki ayrıntılarla birlikte görebilirsiniz.

Kullanıcı sorgusu için `When is your birthday?` her kişiliğinizi biçimlendirilmiş bir yanıt vardır:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Kişilik|Örnek|
|--|--|
|Professional|Yaş gerçekten bana uygulanmaz.|
|Uygun|Gerçekten bir yaşı yok.|
|Wtty|Kullanım süresi ücretsizdir.|
|Giriş|Yaşı yok.|
|Heyecanlandırdı|Ben bir bot kullanıyorum, bu yüzden bir yaşı yok.|
||


## <a name="language-support"></a>Dil desteği

CHIT-chat veri kümeleri aşağıdaki dillerde desteklenmektedir:

|Dil|
|--|
|Çince|
|İngilizce|
|Fransızca|
|Almanya|
|İtalyanca|
|Japonca|
|Korece|
|Portekizce|
|İspanyolca|


## <a name="add-chit-chat-during-kb-creation"></a>KB oluşturma sırasında CHIT-chat ekleme
Bilgi Bankası oluşturma sırasında, kaynak URL 'Leri ve dosyalarınız eklendikten sonra, CHIT-chat ekleme seçeneği vardır. Tek bir sohbet için istediğiniz kişiliğinizi seçin. CHIT-chat eklemek istemiyorsanız veya veri kaynaklarınızda zaten bir sohbet desteğine sahipseniz, **hiçbiri**' ni seçin.

## <a name="add-chit-chat-to-an-existing-kb"></a>Var olan bir KB 'ye sohbet ekleyin
KB 'nizi seçin ve **Ayarlar** sayfasına gidin. Uygun **. tsv** biçimindeki tüm CHIT-chat veri kümelerine bir bağlantı vardır. İstediğiniz kişiliğinizi indirin, sonra dosyayı bir dosya kaynağı olarak karşıya yükleyin. Dosyayı indirip karşıya yüklerken biçimi veya meta verileri düzenlemediğinizden emin olun.

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

![Var olan KB 'ye sohbet ekleme](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

![Mevcut KB önizleme sürümüne tek bir sohbet ekleyin](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset-v2.png)

---

## <a name="edit-your-chit-chat-questions-and-answers"></a>CHIT-chat sorularınızı ve yanıtlarınızı düzenleyin
KB 'nizi düzenlediğinizde, seçtiğiniz kişiliğinizi temel alarak, tek bir sohbet için yeni bir kaynak görürsünüz. Artık başka bir kaynakla olduğu gibi, değiştirilmiş sorular ekleyebilir veya yanıtları düzenleyebilirsiniz.

![Chit-sohbet QnAs 'yi Düzenle](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Meta verileri görüntülemek için, araç çubuğunda **seçenekleri görüntüle** ' yi seçin ve **meta verileri göster**' i seçin.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Ek CHIT-chat soruları ve yanıtları ekleyin
Önceden tanımlanmış veri kümesinde olmayan yeni bir CHIT-chat QnA çifti ekleyebilirsiniz. CHIT-chat kümesinde zaten kapsanan bir QnA çiftini çoğalttığınızdan emin olun. Yeni bir GIT-chat QnA eklediğinizde, **düzenleme** kaynağınıza eklenir. Derecelendiricisini 'ın bu tek bir sohbet olduğunu anlaması için, aşağıdaki görüntüde görüldüğü gibi "düzenleme: chitchat" meta veri anahtarını/değer çiftini ekleyin:

:::image type="content" source="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png" alt-text="CHIT-chat QnAs ekleyin" lightbox="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png":::

## <a name="delete-chit-chat-from-an-existing-kb"></a>Var olan bir KB 'den gelen sohbeti Sil
KB 'nizi seçin ve **Ayarlar** sayfasına gidin. Belirli bir CHIT-chat kaynağınız, seçilen kişilik adına sahip bir dosya olarak listelenir. Bunu kaynak dosya olarak silebilirsiniz.

![Kıt 'yi Sil-KB 'den sohbet](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bilgi Bankası içeri aktarma](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Ayrıca bkz.

[Soru-Cevap Oluşturma’ya genel bakış](../Overview/overview.md)