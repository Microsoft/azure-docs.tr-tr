---
title: Inceleme aracı ile içerik iş akışlarını tanımlama ve kullanma-Content Moderator
titleSuffix: Azure Cognitive Services
description: İçerik ilkelerinize göre özel iş akışlarını ve eşikleri tanımlamak için Azure Content Moderator iş akışı Tasarımcısı ' nı kullanabilirsiniz.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754249"
---
# <a name="define-and-use-moderation-workflows"></a>Denetleme iş akışlarını tanımlama ve kullanma

Bu kılavuzda, [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com) Web sitesinde [iş akışlarını](../review-api.md#workflows) ayarlamayı ve kullanmayı öğreneceksiniz. İş akışları, içeriği daha verimli bir şekilde işlemek için kullanabileceğiniz bulut tabanlı özelleştirilmiş filtrelerdir. İş akışları, farklı yollarla içerik filtrelemek için çeşitli hizmetlere bağlanabilir ve ardından uygun eylemi gerçekleştirebilir. Bu kılavuzda, içeriği filtrelemek ve tipik bir denetleme senaryosunda insan incelemelerini ayarlamak için Content Moderator bağlayıcısının (varsayılan olarak dahil) nasıl kullanılacağı gösterilmektedir.

## <a name="create-a-new-workflow"></a>Yeni iş akışı oluşturma

[Content moderator gözden geçirme aracına](https://contentmoderator.cognitive.microsoft.com/) gidin ve oturum açın. **Ayarlar** sekmesinde **iş akışları**' nı seçin.

![İş akışları ayarı](images/2-workflows-0.png)

Sonraki ekranda, **Iş akışı Ekle**' yi seçin.

![İş akışı ekleme](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Ad ve açıklama ata

İş akışınızı adlandırın, bir açıklama girin ve iş akışının görüntüleri veya metinleri işlemesini seçin.

![İş akışı adı ve açıklaması](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Değerlendirme ölçütlerini tanımlama

Sonraki ekranda, **IF** bölümüne gidin. Üstteki açılan menüden **koşul**' ı seçin. Bu, iş akışının işlem yapması için gereken koşulu yapılandırmanıza olanak tanır. Birden çok koşul kullanmak istiyorsanız bunun yerine **bileşim** ' ı seçin. 

Sonra bir bağlayıcı seçin. Bu örnek **Content moderator**kullanır. Seçtiğiniz bağlayıcıya bağlı olarak, veri çıktısı için farklı seçenekler alacaksınız. Diğer bağlayıcıları ayarlamayı öğrenmek için araç ayarlarını Inceleyin kılavuzunun [Bağlayıcılar](./configure.md#connectors) bölümüne bakın.

![İş akışı bağlayıcısını seçin](images/image-workflow-connect-to.PNG)

Kullanmak için istediğiniz çıktıyı seçin ve denetlenecek koşulları ayarlayın.

![İş akışı koşulunu tanımla](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Eylemi tanımlayın

Bir eylem seçtiğinizde, **ardından** bölümüne gidin. Aşağıdaki örnek bir resim incelemesi oluşturur ve bir etiketi atar. İsteğe bağlı olarak, alternatif (Else) bir yol ekleyebilir ve bu yolu da bir eylem olarak ayarlayabilirsiniz.

![İş akışı eylemini tanımla](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>İş akışını kaydetme

İş akışı adına göz önünde Iş akışı API 'SI ile bir denetleme işi başlatmak için adın olması gerekir (aşağıya bakın). Son olarak, iş akışını sayfanın üst kısmındaki **Kaydet** düğmesini kullanarak kaydedin.

## <a name="test-the-workflow"></a>İş akışını test etme

Özel bir iş akışı tanımladığınıza göre, bunu örnek içerikle test edin. **Iş akışları** ' na gidin ve Ilgili **iş akışını Yürüt** düğmesini seçin.

![İş akışı testi](images/image-workflow-execute.PNG)

Bu [örnek görüntüyü](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) yerel sürücünüze kaydedin. Sonra **Dosya Seç** ' i seçin ve görüntüyü iş akışına yükleyin.

![Görüntüye bir teklife uygulanan bir çalıştırıcı](images/sample-text.jpg)

### <a name="track-progress"></a>İlerleme izleme

Bir sonraki açılan pencerede iş akışının ilerleme durumunu görüntüleyebilirsiniz.

![İş akışı yürütmeyi izleme](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>İş akışı eylemini doğrula

**İnceleme** ' nin altındaki **görüntü** sekmesine gidin ve yeni oluşturulan bir görüntü incelemesi olduğunu doğrulayın.

![Görüntüleri inceleme](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, Content Moderator [Gözden geçirme aracından](https://contentmoderator.cognitive.microsoft.com)denetleme iş akışlarını ayarlamayı ve kullanmayı öğrendiniz. Ardından, programlama yoluyla iş akışları oluşturmayı öğrenmek için [REST API kılavuzuna](../try-review-api-workflow.md) bakın.
