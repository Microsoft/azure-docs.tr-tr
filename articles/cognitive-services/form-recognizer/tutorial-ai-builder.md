---
title: 'Öğretici: AI Oluşturucu-form tanıyıcı ile form işleme uygulaması oluşturma'
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, bir form işleme uygulaması oluşturmak ve eğitme yapmak için AI oluşturucuyu kullanacaksınız.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: lajanuar
ms.openlocfilehash: 30c44ac60783132344cc018135d2c248f91f46bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467128"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>Öğretici: AI Oluşturucu ile form işleme uygulaması oluşturma

[AI Oluşturucu](/ai-builder/overview) , iş performansını geliştirmek için işlemleri otomatikleştirmenize ve sonuçları tahmin etmenize olanak tanıyan bir güç platformu özelliğidir. Form belgelerinden anahtar-değer çiftlerini ve tablo verilerini tanımlayan ve ayıklayan AI modelleri oluşturmak için AI Oluşturucu form işleme ' yı kullanabilirsiniz.

> [!NOTE]
> Bu proje bir [Microsoft Learn modülü](/learn/modules/get-started-with-form-processing/)olarak da kullanılabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Form işleme AI modeli oluşturma
> * Modelinizi eğitme
> * Azure Power Apps veya Power otomatikleştirmek 'de kullanmak için modelinizi yayımlayın

## <a name="prerequisites"></a>Önkoşullar

* Eğitim/test verileri için kullanılan aynı türden en az beş form kümesi. Eğitim veri kümesini birlikte yerleştirmeye yönelik ipuçları ve seçenekler için bkz. [eğitim verileri kümesi oluşturma](./build-training-data-set.md) . Bu hızlı başlangıçta, [örnek veri kümesinin](https://go.microsoft.com/fwlink/?linkid=2128080) **eğitme** klasörü altındaki dosyaları kullanabilirsiniz.
* Bir Power Apps veya güç otomatikleştirme lisansı- [lisanslama kılavuzuna](https://go.microsoft.com/fwlink/?linkid=2085130)bakın. Lisansın [Common Data Service](https://powerplatform.microsoft.com/common-data-service/)içermesi gerekir.
* Bir AI Oluşturucu [eklentisi veya denemesi](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409).


## <a name="create-a-form-processing-project"></a>Form işleme projesi oluşturma

1. [Power Apps](https://make.powerapps.com/) veya [Power otomatikleştirmeye](https://flow.microsoft.com/signin)gidin ve kuruluş hesabınızla oturum açın.
1. Sol bölmede **AI Oluşturucu**  >  **derlemesi**' ni seçin.
1. **Form işleme** kartını seçin.
1. Modelinize bir ad yazın.
1. **Oluştur**’u seçin.

## <a name="upload-and-analyze-documents"></a>Belgeleri karşıya yükleme ve analiz etme

**Belge Ekle** sayfasında, modelinize bilgi ayıklamak istediğiniz form türünü eğitebilmeniz için örnek belgeler sağlamanız gerekir. Belgelerinizi karşıya yükledikten sonra AI Oluşturucu, bir modeli eğitme konusunda yeterli olup olmadığını kontrol etmek için bunları analiz eder.

> [!NOTE]
> AI Builder şu an için aşağıdaki form işleme giriş verisi türlerini desteklememektedir:
>
> - Karmaşık tablolar (iç içe tablolar, birleştirilmiş üst bilgiler veya hücreler vb.)
> - Onay kutuları veya radyo düğmeleri
> - 50 sayfadan daha uzun PDF belgeleri
> - Doldurulabilir PDF'ler
>
> Giriş belgelerinin gereksinimleri hakkında daha fazla bilgi için bkz. [giriş gereksinimleri](./overview.md#input-requirements).

### <a name="upload-your-documents"></a>Belgelerinizi karşıya yükleme

1. **Belge ekle**'yi seçin, en az beş belge seçin ve ardından **Karşıya yükle**'yi seçin.
1. Karşıya yükleme tamamlandıktan sonra **Kapat**' ı seçin.
1. Ardından **Çözümle**' yi seçin.

> [!NOTE] 
> Bu belgeleri karşıya yükledikten sonra aralarından bazılarını kaldırabilir veya yenilerini yükleyebilirsiniz.

> [!div class="mx-imgBorder"]
> ![Belge Ekle düğmesini vurgulayan ekran görüntüsü.](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>Belgelerinizi analiz etme

Analiz adımı sırasında, AI Oluşturucu karşıya yüklediğiniz belgeleri inceler ve alanları ve tabloları algılar. Bu işlemin tamamlanması için gereken süre, belirtilen belge sayısına bağlıdır, ancak çoğu durumda yalnızca birkaç dakika sürer.

Analiz bittiğinde, alan seçimi deneyimini açmak için küçük resmi seçin.

> [!IMPORTANT]
> Analiz başarısız olursa, büyük olasılıkla AI oluşturucusunun belgelerinizdeki yapılandırılmış metinleri algılamamasından kaynaklanıyor olabilir. Güncelleştirdiğiniz belgelerin [giriş gereksinimlerini](./overview.md#input-requirements)izlediğini doğrulayın.

## <a name="select-your-form-fields"></a>Form alanlarınızı seçin

Alan seçimi sayfasında, sizin için gereken alanları seçersiniz:

1. Bir alanı seçmek için, belgede algılanan bir alanı gösteren dikdörtgene tıklayın veya tıklayıp sürükleyerek birden çok alan seçin. Ayrıca, sağ taraftaki bölmedeki alanları da seçebilirsiniz.
1. Gereksinimlerinize göre hizalanacak veya ayıklanan etiketleri normalleştirmek üzere yeniden adlandırmak istiyorsanız Seçili alanın adına tıklayın.

    Algılanan bir alana tıkladığınızda, aşağıdaki bilgiler görüntülenir:

    - **Alan adı**: algılanan alanın etiketinin adı.
    - **Alan değeri**: algılanan alanın değeri.

> [!div class="mx-imgBorder"]
> ![etiketleme sayfası](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>Algılanmayan alanları etiketle

Etiketlemek istediğiniz alan model tarafından otomatik olarak algılanmadıysa, değeri etrafında bir dikdörtgen çizebilir ve görüntülenen iletişim kutusunda bir etiket adı yazabilirsiniz.

## <a name="train-your-model"></a>Modelinizi eğitme

1. Seçtiğiniz form alanlarını denetlemek için **İleri**'yi seçin. Her şey yolunda görünüyorsa modelinizi eğitmek için **Eğit**'i seçin.

    > [!div class="mx-imgBorder"]
    > ![Eğitim sayfası](./media/tutorial-ai-builder/summary-train-page.png)
1. Eğitim tamamlandıktan sonra **Eğitim tamamlandı** ekranında **Ayrıntılar sayfasına git**'i seçin.
## <a name="quick-test-your-model"></a>Modelinizi hızlıca test etme

Ayrıntılar sayfasında modelinizi yayımlamadan veya kullanmadan önce test edebilirsiniz:

1. Ayrıntılar sayfasında **Hızlı test**'i seçin.
2. Test dosyanızı karşıya yüklemek için bir belgeyi sürükleyip bırakabilir veya **cihazımın karşıya yükle** ' yi seçebilirsiniz. Hızlı test sonuçları birkaç saniye içinde görüntüler.
3. Başka bir testi çalıştırmak veya işiniz bittiğinde **kapatmak** Için **baştan başla** ' yı seçebilirsiniz.

### <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

Belirli alanlar için hatalı sonuçlar veya düşük güvenilirlik puanları alıyorsanız, aşağıdaki ipuçlarını deneyin:

- Her alanda farklı değerlere sahip formları kullanarak yeniden eğitme.
- Daha büyük bir eğitim belgeleri kümesi kullanarak yeniden eğitme. Ne kadar çok belge etiketlerseniz, daha fazla AI Oluşturucusu alanları daha iyi tanımanıza daha fazla bilgi sağlar.
- PDF dosyalarını yalnızca ile eğitelecek belirli sayfaları seçerek iyileştirebilirsiniz.   >  Belgenizdeki belirli sayfaları seçmek için **PDF 'ye Yazdır** seçeneğini kullanın.

## <a name="publish-your-model"></a>Modelinizi yayımlama

Modelinize memnunsanız yayımlamak için **Yayımla**  ' yı seçin. Yayımlama işlemi tamamlandığında modeliniz **Yayımlandı** durumuna geçer ve kullanıma hazır hale gelir.

> [!div class="mx-imgBorder"]
> ![modeli Yayımla sayfası](./media/tutorial-ai-builder/model-page.png)

Form işleme modelinizi yayımladıktan sonra, bunu bir [Power Apps tuval](/ai-builder/form-processor-component-in-powerapps) uygulamasında veya [Power otomatikleştirmede](/ai-builder/form-processing-model-in-flow)kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Form işleme modeli kullanmaya yönelik AI Oluşturucu belgelerini izleyin.

* [Power Apps 'teki form işlemcisi bileşenini kullanma](/ai-builder/form-processor-component-in-powerapps)
* [Power otomatikleştirmek 'de form işleme modeli kullanma](/ai-builder/form-processing-model-in-flow)