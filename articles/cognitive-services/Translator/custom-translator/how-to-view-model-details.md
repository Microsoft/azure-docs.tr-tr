---
title: Model ayrıntılarını görüntüleme-özel çevirici
titleSuffix: Azure Cognitive Services
description: Herhangi bir proje altındaki modeller sekmesi model adı, model durumu, BLEU puanı, eğitim, ayarlama ve test tümcesi sayısı gibi her bir modelin ayrıntılarını gösterir.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 64f446c3b331c1aa6ddaae9081b7f61943f74ab2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595573"
---
# <a name="view-model-details"></a>Model ayrıntılarını görüntüleme

Proje altındaki modeller sekmesi, bu projedeki tüm modelleri gösterir. Bu proje için eğitilen tüm modeller bu sekmede listelenmiştir.

Projedeki her model için bu ayrıntılar görüntülenir.

1.  Model adı: Belirli bir modelin model adını gösterir.

2.  Durum: Belirli bir modelin durumunu gösterir. Yeni eğitiminizi kabul edilene kadar gönderildi durumuna sahip olur. Hizmet belgelerinizin içeriğini değerlendirirken durum veri işleme olarak değişir. Belgelerinizin değerlendirmesi tamamlandığında durum çalışır olarak değişir ve sizin için otomatik olarak oluşturulan ayarlama ve test kümeleri de dahil olmak üzere eğitimin parçası olan Tümcelerin sayısını görebilirsiniz. Modellerin durumunu açıklayan model durumunun listesi aşağıda verilmiştir.

    -  Gönderme Arka ucun bu modelin belgelerini işleme olduğunu belirtir.

    -  Sıraya alındı: Eğitimin bu model için MT sistemine sıraya konmakta olduğunu belirtir.

    -  Çalışıyor: Eğitimin bu model için MT sisteminde çalıştığını belirtir.

    -  Baarı MT sisteminde ve bir modelde eğitimin başarılı olduğunu belirtir. Bu durumda, bu model için bir BLEU puanı görüntülenir.

    -  Dağıtılan Başarılı eğitilen modelin dağıtım için MT sistemine gönderildiğini belirtir.

    -  Dağıtım kaldırılıyor: Dağıtılan modelin dağıtmadığını belirtir.

    -  Dağıtılmamış Bir modelin dağıtım kaldırma işleminin başarıyla tamamlandığını belirtir.

    -  Eğitim başarısız oldu: Eğitimin başarısız olduğunu belirtir. Bir eğitim hatası oluşursa, eğitim işini yeniden deneyin. Hata devam ederse bizimle iletişim kurun. Başarısız modeli silmeyin.

    - DataProcessingFailed: Modele ait bir veya daha fazla belge için veri işlemenin başarısız olduğunu belirtir.

    - DeploymentFailed Model dağıtımının başarısız olduğunu belirtir.

    - MigratedDraft: Hub 'dan özel çevirmene geçişten sonra modelin taslak durumunda olduğunu belirtir.

4.  BLEU puanı: çeviri sisteminizin kalitesini belirten, modelin BLEU (Iki dilli değerlendirme düşük Incelemesi) sayısını gösterir. Bu puan, bu eğitimin sonucu olan çeviri sistemi tarafından yapılan çevirilerin, test veri kümesindeki başvuru cümleleriyle ne kadar yakın olduğunu söyler. Eğitim başarıyla tamamlandıysanız BLEU puanı görüntülenir. Eğitim tamamlanmamış/başarısız olursa, herhangi bir BLEK puanı görmezsiniz.

5.  Eğitim tümcesi sayısı: Eğitim kümesi olarak kullanılan Tümcelerin toplam sayısını gösterir.

6.  Ayarlama tümce sayısı: Ayarlama kümesi olarak kullanılan Tümcelerin toplam sayısını gösterir.

7.  Eğitim tümcesi sayısı: Test kümesi olarak kullanılan Tümcelerin toplam sayısını gösterir.

8.  Mono tümce sayısı: Mono kümesi olarak kullanılan Tümcelerin toplam sayısını gösterir.

9.  Dağıtım eylemi düğmesi: Başarılı bir eğitilen modelde, dağıtılmamışsa "Dağıt" düğmesi görüntülenir. Bir model dağıtılmışsa, "dağıtım geri al" düğmesi gösterilir.

10. Silmeli Modeli silmek istiyorsanız bu düğmeyi kullanabilirsiniz. Bir modelin silinmesi, bu modeli oluşturmak için kullanılan belgelerden hiçbirini silmez.

    ![Model ayrıntılarını görüntüleme](media/how-to/how-to-view-model-details.png)

>[!Note]
>Aynı sistemler için ardışık işlemleri karşılaştırmak için ayarlama kümesi ve test kümesi sabitinden haberdar olmanız önemlidir.

## <a name="view-model-training-details"></a>Model eğitimi ayrıntılarını görüntüleme

Eğitim tamamlandığında, Ayrıntılar sayfasından eğitim hakkındaki ayrıntıları gözden geçirebilirsiniz. Bir proje seçin, modeller sekmesini bulun ve seçin ve bir model seçin.

Model sayfası iki sekmeye sahiptir: Eğitim ayrıntıları ve test.

1.  **Eğitim ayrıntıları:** Bu sekme, eğitiminde kullanılan belge (ler) i listesini gösterir:

    -  Belge adı: Bu alan belgenin adını gösterir

    -  Belge türü: Bu alan, bu belgenin paralel/mono olduğunu gösterir.

    -  Kaynak dildeki tümce sayısı: Bu alan, kaynak dilin bir parçası olan Tümcelerin sayısını gösterir.

    -  Hedef dilde tümce sayısı: Bu alan, hedef dilin bir parçası olan Tümcelerin sayısını gösterir.

    -  Hizalanmış cümleler: Bu alan, hizalama işlemi sırasında özel çevirmen tarafından hizalanan cümle sayısını gösterir.

    -  Kullanılan cümleler: Bu alan, bu eğitim sırasında özel çevirmen tarafından kullanılan Tümcelerin sayısını gösterir.

    ![Model eğitimi ayrıntıları](media/how-to/how-to-model-training-details.png)

2.  **Sınamanız** Bu sekmede başarılı bir eğitimin test ayrıntıları gösterilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Test sonuçlarını](how-to-view-system-test-results.md) gözden geçirin ve eğitim sonuçlarını çözümleyin.
