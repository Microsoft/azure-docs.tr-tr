---
title: "\"Merhaba Dünya\" Conversation Learner modeli oluşturma-Microsoft bilişsel hizmetler | Microsoft Docs"
titleSuffix: Azure
description: Bir "Merhaba Dünya" Conversation Learner modeli oluşturmayı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: bb1d053af8813f05872c56d3b5609f2d7d7d5d8c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705633"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Conversation Learner sahip bir "Merhaba Dünya" modeli oluşturma

Bu öğreticide, eylemler oluşturma, bot 'u etkileşimli bir şekilde öğretme ve son kullanıcılardan gelen günlüğe yazılan iletişim kutularında düzeltme yapma dahil olmak üzere Conversation Learner ile çalışmaya başlama gösterilmektedir.

## <a name="video"></a>Video

[![Merhaba Dünya öğretici önizlemesi](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Gereksinimler
Henüz yapmadıysanız, lusıs yazma anahtarınızla bir `.env` dosya oluşturma da dahil olmak üzere, ilk olarak tüm Kurulum adımlarının tamamlandığından emin olun.  Ayrıntılar için bkz. [hızlı başlangıç](../quickstart.md) .

Bu öğreticide, genel öğretici bot 'ın çalışıyor olması gerekir

    npm run tutorial-general

## <a name="steps"></a>Adımlar

Web Kullanıcı arabirimindeki giriş sayfasında başlatın.

### <a name="create-the-model"></a>Model oluşturma
1. "Yeni model" düğmesine tıklayın.
2. "Ad" alanına "Merhaba Dünya" yazın.
3. "Oluştur" düğmesine tıklayın.

Şimdi oluşturduğunuz modelin görünümünü görmeniz gerekir.

### <a name="create-an-action"></a>Eylem oluşturma
1. Sol bölmede, "eylemler" e ve ardından "yeni eylem" düğmesine tıklayın.
    - Bir eylem, Conversation Learner kullanıcıya, API çağrısına veya karta döndüren bir kısa mesaj olabilir.
2. "Bot 'un yanıtı..." "Hello" alan türü.
    - Bu, bot 'ın döndüreceği yanıt olur.
3. "Oluştur" düğmesine tıklayın.

Bot 'un gerçekleştirebileceği ilk eylemi oluşturdunuz, yani bir metin yanıtı döndürün.

### <a name="train-dialogs"></a>Iletişim kutularını eğitme
Bu, modeli kullanıcıya nasıl yanıt verileceğini öğrenmek için eğitebileceğiniz yerdir.

#### <a name="first-training-dialog"></a>İlk eğitim Iletişim kutusu

1. Sol bölmede "Iletişim kutularını eğitme" düğmesine ve ardından "yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. "Hi" yazın, ENTER tuşuna basın.
    - Kullanıcının görüşmenin başlangıcında söyleme hakkında bir örnek olarak.
3. "Eylemleri puan" düğmesine tıklayın.
4. "Merhaba" yı seçin.
    - Bu örnek iletişim kutusunu yalnızca bir tam olarak tamamladınız. 
5. "Güle" Kullanıcı yanıtı yazın.
6. "Eylemleri puan" düğmesine tıklayın.
7. "+ Action" düğmesine tıklayın.
8. "Güle!" yazın "bot 'ın yanıtı..." alanı, ardından "Oluştur" düğmesine tıklayın.
    - Bot 'un yeni oluşturduğunuz eylemle yanıt verdiğini unutmayın.
9. "Kaydet" düğmesine tıklayın. 
    - Bu, bu eğitim Iletişim kutusunu sona erdirmek ve kaydeder.

Artık modelde tek bir varlık ve iki eylem ile birlikte bir eğitim Iletişim kutusu vardır.

#### <a name="second-training-dialog"></a>İkinci eğitim Iletişim kutusu
Şimdi bir eğitim yapın ve bot 'ın nasıl yanıt verdiğini görebilirsiniz.

1. "Yeni eğitme Iletişim kutusu" düğmesine tıklayın.
2. Yazın, "Hi"
    - Bu, ilk iletişim kutusuna benzerdir ve bot 'tan iyi bir puan almanızı bekler.
3. "Eylemleri puan" düğmesine tıklayın.
    - Konum ve puan yine yeterince doğru olmayabilir ve ek eğitim gerekebilir.
4. "Merhaba" yı seçin.
5. "Bye" Kullanıcı yanıtı yazın.
6. "Eylemleri puan" düğmesine tıklayın.
7. "Güle!" seçeneğini belirleyin
8. "Kaydet" düğmesine tıklayın.

### <a name="log-dialogs"></a>Günlük Iletişim kutuları
Bu, sizin veya gerçek kullanıcılarınızın bot 'unuzla sahip olduğu konuşmaları test ettiğiniz, görüntülediğiniz ve düzeltebileceğiniz yerdir.

#### <a name="test-the-model-as-an-end-user"></a>Modeli son kullanıcı olarak test etme
1. Sol bölmede "günlük Iletişimleri" ve ardından "yeni günlük Iletişim" düğmesine tıklayın.
2. "Merhaba buraya" yazın.
3. Kısa bir süre bekleyin, bot "Hello" ile otomatik olarak yanıt vermelidir
4. ' Byebye ' girin
5. Kısa bir süre bekledikten sonra, "Hello" ile otomatik olarak yanıt vermelidir.
6. "Test bitti" düğmesine tıklayın.

#### <a name="view-and-correct-a-user-conversation"></a>Kullanıcı Iletişimini görüntüleme ve düzeltme
Günlük Iletişim kutularını kullanarak, bot 'unuzla birlikte tutulan konuşmaları listesini görüntüleyebilirsiniz. Ayrıca, bot 'un yanıtlarını düzeltmek ve etkileşimleri eğitim Iletişimleri olarak kaydetmek için de düzenleyebilirsiniz. Bunu yapmak için:
1. Kılavuzda, görüşmenin günlüğüne tıklayın.
2. Son bot eylemine (ör.) tıklayın. "Merhaba".
3. "Güle!" seçeneğini belirleyin Bot 'ı düzeltmek için.
4. "Tren olarak kaydet Iletişim kutusu" düğmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Eğitimlere giriş](./02-intro-to-training.md)
