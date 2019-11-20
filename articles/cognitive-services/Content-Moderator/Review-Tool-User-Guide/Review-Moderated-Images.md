---
title: Inceleme aracında içerik incelemeleri kullanma-Content Moderator
titleSuffix: Azure Cognitive Services
description: Inceleme aracının bir Web portalındaki görüntüleri gözden geçirmesine nasıl izin verdiğini öğrenin.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: b02324923e3f004395105b8e04165390cb950fe7
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044122"
---
# <a name="create-human-reviews"></a>İnsan incelemeleri oluşturma

Bu kılavuzda Inceleme Aracı Web sitesinde [İncelemeleri](../review-api.md#reviews) ayarlamayı öğreneceksiniz. , İnsan moderatör 'nin değerlendirilmesine yönelik depolamayı ve görüntüleme içeriğini gözden geçirir. Moderatör, uygulanan etiketleri değiştirebilir ve kendi özel etiketlerini uygun şekilde uygulayabilir. Bir Kullanıcı bir gözden geçirmeyi tamamladığında, sonuçlar belirtilen geri çağırma uç noktasına gönderilir ve içerik siteden kaldırılır.

## <a name="prerequisites"></a>Önkoşullar

- Content Moderator [İnceleme aracı](https://contentmoderator.cognitive.microsoft.com/) sitesinde oturum açın veya hesap oluşturun.

## <a name="image-reviews"></a>Görüntü incelemeleri

1. [İnceleme aracına](https://contentmoderator.cognitive.microsoft.com/)gidin, **dene** sekmesini seçin ve gözden geçirmek için bazı görüntüleri karşıya yükleyin.
1. Karşıya yüklenen görüntülerin işlenmesi tamamlandıktan sonra, **gözden geçir** sekmesine gidin ve **görüntü**öğesini seçin.

    ![Görüntüyü gözden geçir seçeneğinin vurgulandığı İnceleme aracını gösteren Chrome tarayıcısı](images/review-images-1.png)

    Görüntüler, otomatik denetleme işlemi tarafından atanan etiketlerle birlikte görüntülenir. Inceleme aracı aracılığıyla gönderdiğiniz görüntüler diğer gözden geçirenler için görünür değildir.

1. İsteğe bağlı olarak, ekranda görüntülenen görüntü sayısını ayarlamak için Incelemeleri (1) **ekran** kaydırıcısının üzerine taşıyın. Görüntüleri uygun şekilde sıralamak için **etiketli** veya **etiketlenmemiş** düğmelere (2) tıklayın. Bir etiket paneline (3) tıklayarak açın veya kapatın.

    ![İnceleme için etiketli görüntülerle Inceleme aracını gösteren Chrome tarayıcısı](images/review-images-2.png)

1. Bir görüntüyle ilgili daha fazla bilgi görmek için küçük resimdeki üç noktaya tıklayın ve **Ayrıntıları görüntüle**' yi seçin. **Git** seçeneğiyle bir alt takıma bir görüntü atayabilirsiniz (alt takımlar hakkında daha fazla bilgi edinmek için [takımlar](./configure.md#manage-team-and-subteams) bölümüne bakın).

    ![Ayrıntıları görüntüle seçeneğinin vurgulandığı bir görüntü](images/review-images-3.png)

1. Ayrıntılar sayfasında görüntü denetleme bilgilerine bakın.

    ![Ayrı bir bölmede, denetleme ayrıntıları içeren bir görüntü](images/review-images-4.png)

1. Etiket atamalarını gerektiğinde inceledikten ve güncelleştirdikten sonra, gözden geçirlerinizi göndermek için **İleri** ' ye tıklayın. ' İ gönderdikten sonra **, önceki ekrana** dönüp görüntüleri yeniden gözden geçirmek için beş saniyelik bir süre daha görürsünüz. Bundan sonra, görüntüler artık gönderme kuyruğunda değildir ve **önceki** düğmesi artık kullanılamaz.

## <a name="text-reviews"></a>Metin incelemeleri

Metin İncelemeleri, görüntü incelemeleriyle benzer şekilde çalışır. İçeriği karşıya yüklemek yerine yalnızca metin (en fazla 1.024 karakter) yazmanız veya yapıştırmanız gerekir. Ardından Content Moderator, metni analiz eder ve etiketleri uygular (küfür ve kişisel veriler gibi diğer denetleme bilgilerine ek olarak). Metin incelemelerinde, uygulanan etiketleri açıp/veya gözden geçirmeyi göndermeden önce özel etiketler uygulayabilirsiniz.

![Chrome tarayıcı penceresinde bayraklı metni gösteren İnceleme aracının ekran görüntüsü](../images/reviewresults_text.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, Content Moderator [Gözden geçirme aracından](https://contentmoderator.cognitive.microsoft.com)İncelemeleri ayarlamayı ve kullanmayı öğrendiniz. Ardından, programlama yoluyla nasıl İnceleme oluşturacağınızı öğrenmek için [REST API Kılavuzu](../try-review-api-review.md) veya [.NET SDK hızlı](../dotnet-sdk-quickstart.md) başlangıcı bölümüne bakın.