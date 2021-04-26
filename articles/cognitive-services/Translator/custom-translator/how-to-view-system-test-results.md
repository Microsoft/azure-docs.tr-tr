---
title: Sistem testi sonuçlarını ve dağıtımı görüntüleme-özel çevirici
titleSuffix: Azure Cognitive Services
description: Eğitim başarılı olduğunda, eğitim sonuçlarınızı çözümlemek için Sistem testlerini gözden geçirin. Eğitim sonuçlarıyla memnunsanız eğitilen model için bir dağıtım isteği koyun.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: cae2c95e56312c58d396d1e578f4677ce2b14aa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895943"
---
# <a name="view-system-test-results"></a>Sistem test sonuçlarını görüntüleme

Eğitim başarılı olduğunda, eğitim sonuçlarınızı çözümlemek için Sistem testlerini gözden geçirin. Eğitim sonuçlarıyla memnunsanız eğitilen model için bir dağıtım isteği koyun.

## <a name="system-test-results-page"></a>Sistem test sonuçları sayfası

Bir proje seçin, ardından projenin modeller sekmesini seçin, kullanmak istediğiniz modeli bulun ve son olarak test sekmesini seçin.

Test sekmesi şunları gösterir:

1.  **Sistem test sonuçları:** Test sürecinin sonuçları. Test süreci BLEU Puanını üretir.

    **Tümce sayısı:** Test kümesinde kaç tane paralel tümce kullanıldı.

     **Bleu puanı:** Eğitim tamamlandıktan sonra bir model için üretilen BLEU puanı.

    **Durum:** Test işleminin tamamlanıp tamamlanmadığını veya devam ettiğini gösterir.

    ![Sistem test sonuçları](media/how-to/how-to-system-test-results.png)

2.  Sistem test sonuçlarına tıklayın ve bu işlem sizi test sonucu ayrıntıları sayfasına götürür. Bu sayfada, test veri kümesinin parçası olan Tümcelerin makine çevirisi gösterilmektedir.

3.  Test sonucu ayrıntıları sayfasındaki tabloda iki sütun bulunur. çiftteki her dil için bir tane. Kaynak dilin sütunu, çevrilecek tümceyi gösterir. Hedef dilin sütunu her satırda iki cümle içerir.

    **Başvuru:** Bu cümle, kaynak cümlenin test veri kümesinde verilen başvuru dönüştürmesidir.

    **Mt:** Bu cümle, eğitim gerçekleştirildikten sonra oluşturulan model tarafından gerçekleştirilen kaynak cümlenin otomatik dönüştürmesidir.

    ![Sistem test sonuçları karşılaştırması](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Testi indir

Bir zip dosyası indirmek için çevirileri Indir bağlantısına tıklayın. ZIP, test veri kümesindeki kaynak cümlelerin makine çevirilerini içerir.

![Testi indir](media/how-to/how-to-system-test-download.png)

Bu indirilen ZIP arşivi üç dosya içerir.

1.  **custom.mt.txt:** Bu dosya, kullanıcının verileriyle eğitilen model tarafından gerçekleştirilen hedef dilde kaynak dil cümlelerini makine çevirileri içerir.

2.  **ref.txt:** Bu dosya, hedef dilde kaynak dil cümlelerini Kullanıcı tarafından sağlanmış çevirileri içerir.

3.  **source.txt:** Bu dosya, kaynak dilde cümleler içeriyor.

    ![İndirilen sistem testi sonuçları](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Model dağıtma

Dağıtım istemek için:

1.  Bir proje seçin, modeller sekmesine gidin.

2. Başarılı bir eğitilen modelde, dağıtılmamışsa "Dağıt" düğmesi görüntülenir.

    ![Bir modelin dağıtılması için dağıt düğmesini vurgulayan ekran görüntüsü.](media/how-to/how-to-deploy-model.png)

3.  Dağıt ' a tıklayın.
4.  Modelinizin **dağıtılmasını istediğiniz** bölge (ler) i seçin ve Kaydet ' e tıklayın. Birden çok bölge için **Dağıtılmış** ' ı seçebilirsiniz.

    ![Bir modelin dağıtımını veya dağıtımını geri dağıtmayı gösteren ekran görüntüsü.](media/how-to/how-to-deploy-model-regions.png)

5.  Modelinizin durumunu "durum" sütununda görüntüleyebilirsiniz.

>[!Note]
>Özel çevirmen, herhangi bir zamanda bir çalışma alanı içinde 10 dağıtılan modeli destekler.

## <a name="update-deployment-settings"></a>Dağıtım ayarlarını Güncelleştir

Dağıtım ayarlarını güncelleştirmek için:

1.  Bir proje seçin ve **modeller** sekmesine gidin.

2. Başarıyla dağıtılan bir model için bir **güncelleştirme** düğmesi gösterir.

    ![Dağıtım ayarlarını güncelleştirmek için Güncelleştir düğmesini vurgulayan ekran görüntüsü.](media/how-to/how-to-update-undeploy-model.png)

3.  **Güncelleştir**’i seçin.
4.  Modelinizin dağıtılmasını veya dağıtılmasını istediğiniz bölge (ler) i için **dağıtılan** veya **dağıtılan** ' ı seçin ve ardından **Kaydet**' e tıklayın.

    ![Modeli dağıt](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Tüm bölgeler için **dağıtımı geri al** ' ı seçerseniz, model tüm bölgelerden dağıtılır ve Dağıtılmamış duruma konur. Artık kullanım için kullanılamaz.

## <a name="next-steps"></a>Sonraki adımlar

- Dağıtılmış özel çeviri modelinizi [Microsoft Translator metin çevirisi API'si v3](../reference/v3-0-translate.md?tabs=curl)aracılığıyla kullanmaya başlayın.
- Çalışma alanınızı paylaşmak için [ayarları yönetmeyi](how-to-manage-settings.md) öğrenin, abonelik anahtarını yönetin.
- [Çalışma alanınızı ve projenizi](how-to-migrate.md) [Microsoft Translator hub 'ından](https://hub.microsofttranslator.com) nasıl geçirebileceğinizi öğrenin