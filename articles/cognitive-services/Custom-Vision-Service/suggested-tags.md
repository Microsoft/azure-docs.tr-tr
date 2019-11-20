---
title: Akıllı etiketleyici ile görüntüleri daha hızlı etiketleme
titleSuffix: Azure Cognitive Services
description: Bu kılavuzda, görüntüler için önerilen Etiketler oluşturmak üzere akıllı etiketleyici 'yi nasıl kullanacağınızı öğreneceksiniz. Bu, bir Özel Görüntü İşleme modeline eğitim yaparken çok sayıda görüntüyü daha hızlı etiketlemenize olanak sağlar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647762"
---
# <a name="label-images-faster-with-smart-labeler"></a>Akıllı etiketleyici ile görüntüleri daha hızlı etiketleme

Bu kılavuzda, görüntüler için önerilen Etiketler oluşturmak üzere akıllı etiketleyici 'yi nasıl kullanacağınızı öğreneceksiniz. Bu, bir Özel Görüntü İşleme modeline eğitim yaparken çok sayıda görüntüyü daha hızlı etiketlemenize olanak sağlar.

Bir Özel Görüntü İşleme modeli için resimleri etiketlediğinizde, hizmet etiketlenmemiş görüntülerin etiketlerini tahmin etmek için modelin en son eğitilen yinelemesini kullanır. Daha sonra, seçilen güven eşiğine ve tahmine dayalı olarak bu tahminleri önerilen Etiketler olarak gösterir. Daha sonra önerileri onaylayıp değiştirebilir ve görüntüleri eğitim için el ile etiketleme sürecini hızlandırın.

## <a name="when-to-use-smart-labeler"></a>Akıllı etiketleyici ne zaman kullanılır?

Aşağıdaki kısıtlamaları göz önünde bulundurun:

* Yalnızca içeriği zaten eğitilen görüntüler için önerilen Etiketler istemeniz gerekir. Yeni bir etiket için henüz eğitmeniz gereken öneriler alın.

> [!IMPORTANT]
> Akıllı etiket özelliği, normal tahminlerde aynı [fiyatlandırma modelini](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) kullanır. Bir görüntü kümesi için önerilen etiketleri ilk kez tetikleyişinizde, tahmin aramalarıyla aynı şekilde ücretlendirilirsiniz. Bundan sonra hizmet, seçilen görüntülerin sonuçlarını 30 gün boyunca bir veritabanında depolar ve bu süre içinde her zaman ücretsiz olarak erişebilirsiniz. 30 gün sonra önerilen etiketlerini yeniden istemeniz durumunda ücretlendirilirsiniz.

## <a name="smart-labeler-workflow"></a>Akıllı etiketleyici iş akışı

Aşağıdaki adımlarda akıllı etiketleyici 'nin nasıl kullanılacağı gösterilmektedir:

1. Tüm eğitim görüntülerinizi Özel Görüntü İşleme projenize yükleyin.
1. Veri kümesinin bir bölümünü etiketleyerek her etiket için eşit sayıda görüntü seçebilirsiniz.
    > [!TIP]
    > Önerilerini istediğiniz etiketlerin tümünü daha sonra kullandığınızdan emin olun.
1. Eğitim sürecini başlatın.
1. Eğitim tamamlandığında, **etiketlenmemiş** görünüme gidin ve sol bölmedeki **Önerilen etiketleri al** düğmesini seçin.
    > [!div class="mx-imgBorder"]
    > ![önerilen Etiketler düğmesi etiketsiz görüntüler sekmesi altında gösterilir.](./media/suggested-tags/suggested-tags-button.png)
1. Görüntülenen açılan pencerede, önerileri istediğiniz görüntü sayısını ayarlayın. Etiketlenmemiş görüntülerin bir bölümü için yalnızca başlangıç etiketi önerilerini almalısınız. Bu işlem boyunca yineleme yaparken daha iyi etiket önerileri alacaksınız.
1. Önerilen etiketleri doğrulayın ve düzeltme doğru değil.
    > [!TIP]
    > Önerilen etiketlere sahip görüntüler, kendi tahmine göre sıralanır (düşük değerler daha yüksek güvenilirliğe işaret ediyor). Sıralama düzenini, **belirsizlik Ile Sırala** seçeneğiyle değiştirebilirsiniz. Sıralamayı **yüksek-düşük**olarak ayarlarsanız, önce yüksek belirsizlik tahminlerini düzeltebilir, sonra da düşük belirsizlik ' ı hızla doğrulayabilirsiniz.
    * Görüntü sınıflandırma projelerinde, toplu işlemler içindeki etiketleri seçebilir ve doğrulayabilirsiniz. Görünümü belirtilen önerilen bir etikete göre filtreleyin, yanlış etiketlenmiş görüntülerin seçimini kaldırın ve ardından bir toplu işte geri kalanı onaylayın.
        > [!div class="mx-imgBorder"]
        > ![önerilen Etiketler, filtreler içeren c için Batch modunda görüntülenir.](./media/suggested-tags/ic-batch-mode.png)

        Galerinin bir görüntüsünü seçerek, önerilen etiketleri ayrı görüntü modunda da kullanabilirsiniz.

        ![Önerilen Etiketler, c için ayrı görüntü modunda görüntülenir.](./media/suggested-tags/ic-individual-image-mode.png)
    * Nesne algılama projelerinde toplu iş onayları desteklenmez, ancak daha düzenli bir etiketleme deneyimi için önerilen etiketlere göre filtrelemeye ve sıralamaya devam edebilirsiniz. Etiketlenmemiş görüntülerinizin küçük resimleri, önerilen etiketlerin konumlarını gösteren bir sınırlayıcı kutular kaplaması gösterir. Önerilen bir etiket filtresi seçmezseniz, etiketlenmemiş tüm görüntüleriniz sınırlayıcı kutuların üzerine yerleştirmeden görüntülenir.
        > [!div class="mx-imgBorder"]
        > ![önerilen Etiketler, komut dosyası filtreleri içeren bir toplu iş modunda görüntülenir.](./media/suggested-tags/od-batch-mode.png)

        Nesne algılama etiketlerini onaylamak için, bunları galerideki her bir görüntüye uygulamanız gerekir.

        ![Önerilen Etiketler, bir OD için ayrı görüntü modunda görüntülenir.](./media/suggested-tags/od-individual-image-mode.png)
1. Eğitim işlemini yeniden başlatın.
1. Öneri kalitesindeki memnun olana kadar yukarıdaki adımları tekrarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Bir Özel Görüntü İşleme projesi oluşturmaya ve eğitimlere başlamak için hızlı başlangıcı izleyin.

* [Sınıflandırıcı oluşturma](getting-started-build-a-classifier.md)
* [Nesne algılayıcısı oluşturma](get-started-build-detector.md)