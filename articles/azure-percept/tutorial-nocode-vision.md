---
title: Azure Percept Studio 'da kod içermeyen bir Vision çözümü oluşturma
description: Azure Percept Studio 'da kod içermeyen bir Vision çözümü oluşturmayı ve Azure Percept DK 'nize dağıtmayı öğrenin
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 49e5db729dab7abaa440b1adf6a61e9e52a1efbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023138"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>Azure Percept Studio 'da kod içermeyen bir Vision çözümü oluşturma

Azure Percept Studio, kodlama gerekmeden özel görüntü işleme çözümleri oluşturmanıza ve dağıtmanıza olanak sağlar. Bu makalede şunları yapacaksınız:

- [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 'da bir Vision projesi oluşturma
- Devkit 'inizdeki eğitim görüntülerini toplayın
- Eğitim görüntülerinizi [özel görüntü işleme](https://www.customvision.ai/) etiketleme
- Özel nesne algılama veya sınıflandırma modelinizi eğitme
- Modelinizi devkit 'e dağıtın
- Yeniden eğitim ayarlayarak modelinizi geliştirme

Bu öğretici, çok az sayıda AI deneyimi ve Azure Percept kullanmaya başlarken geliştiriciler için uygundur.

## <a name="prerequisites"></a>Önkoşullar

- Azure Percept DK (devkit)
- [Azure aboneliği](https://azure.microsoft.com/free/)
- Azure Percept DK kurulum deneyimi: devkit 'i bir Wi-Fi ağına bağladınız, bir IoT Hub oluşturdunuz ve devkit 'e IoT Hub

## <a name="create-a-vision-prototype"></a>Bir Vision prototipi oluşturma

1. Tarayıcınızı başlatın ve [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)'ya gidin.

1. Genel Bakış sayfasında **tanıtımlar & öğreticiler** sekmesine tıklayın.  :::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Azure Percept Studio 'ya genel bakış ekranı." lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. **Vision öğreticileri ve tanıtımları** altında, **bir görüntü oluşturma prototipi oluştur ' a** tıklayın.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Azure Percept Studio gösterileri ve öğreticiler ekranı." lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. **Yeni Azure Percept özel görüntü işleme prototipi** sayfasında şunları yapın:

    1. **Proje adı** kutusuna, Vision prototipiniz için bir ad girin.

    1. **Proje açıklama** kutusunda Vision prototipin bir açıklamasını girin.

    1. **Cihaz türü** açılan menüsünde **Azure Percept dk** ' yi seçin.

    1. **Kaynak** açılan menüsünde bir kaynak seçin veya **Yeni kaynak oluştur ' a** tıklayın. Yeni bir kaynak oluşturmayı tercih ederseniz, **Oluştur** penceresinde şunları yapın:
        1. Yeni kaynağınız için bir ad girin.
        1. Azure aboneliğinizi seçin.
        1. Bir kaynak grubu seçin veya yeni bir tane oluşturun.
        1. Tercih ettiğiniz bölgeyi seçin.
        1. Fiyatlandırma katmanınızı seçin (S0 önerilir).
        1. Pencerenin alt kısmındaki **Oluştur** ' a tıklayın.

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="Kaynak penceresi oluştur.":::

    1. **Proje türü** için, Vision projenizin nesne algılama veya görüntü sınıflandırması yapıp gerçekleştirmeyeceğini seçin. Proje türleri hakkında daha fazla bilgi için seçmeme **Yardım** et ' e tıklayın.

    1. **En Iyi duruma getirme** için, projenizi doğruluk, düşük ağ gecikmesi veya her ikisinin de bir denge açısından iyileştirmek isteyip istemediğinizi seçin.

    1. **Oluştur** düğmesine tıklayın.

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="Özel Vision prototip ekranı oluşturun.":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>Bir cihazı projenize bağlama ve görüntüleri yakalama

Bir Vision çözümü oluşturduktan sonra devkit ve buna karşılık gelen IoT Hub eklemeniz gerekir.

1. Devkit üzerinde güç.

1. **IoT Hub** açılır menüsünde, DEVKIT 'in OOBE sırasında bağlandığı IoT Hub 'ını seçin.

1. **Cihazlar** açılan menüsünde devkit ' i seçin.

Daha sonra, AI modelinize eğitim için görüntüleri veya yakalama görüntülerini yüklemeniz gerekir. Etiket türü başına en az 30 görüntü yüklemeniz önerilir. Örneğin, bir köpek ve kedi algılayıcısı oluşturmak istiyorsanız, en az 30 görüntü ve kediler için 30 görüntü yüklemeniz gerekir. Devkit 'in Vision SoM ile görüntüleri yakalamak için aşağıdakileri yapın:

1. **Görüntü yakalama** penceresinde, Vision som video akışını görüntülemek için **cihaz akışını görüntüle** ' yi seçin.

1. Eğitim resimlerini almak üzere Vision SoM kameranızın doğru şekilde hizalandığından emin olmak için video akışını kontrol edin. Ayarları gerektiği gibi yapın.

1. **Görüntü yakalama** penceresinde, **Fotoğraf Al**' ı tıklatın.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="Görüntü yakalama ekranı.":::

1. Alternatif olarak, **Otomatik görüntü yakalama** kutusunu işaretleyerek tek seferde büyük miktarda görüntü toplamak için otomatik görüntü yakalama ayarlayın. **Yakalama hızı** altında tercih ettiğiniz görüntüleme hızını ve **hedef** altında toplamak istediğiniz toplam görüntü sayısını seçin. Otomatik **yakalama ayarla** ' ya tıklayarak otomatik görüntü yakalama işlemini başlatın.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="Otomatik görüntü yakalama açılan menüsü.":::

Yeterli fotoğrafla ihtiyacınız olduğunda Ileri ' ye tıklayın: ekranın alt kısmındaki **Resimleri ve model eğitimini etiketle** . Tüm görüntüler [özel görüntü işleme](https://www.customvision.ai/)kaydedilir.

> [!NOTE]
> Eğitim görüntülerini doğrudan Özel Görüntü İşleme karşıya yüklemeyi tercih ederseniz, görüntü dosyası boyutunun 6MB 'yi aşmadığını lütfen unutmayın.

## <a name="tag-images-and-train-your-model"></a>Resimleri etiketleme ve modelinizi eğitme

Modelinize eğitim vermeden önce resimlerinize Etiketler ekleyin.

1. **Etiket görüntüleri ve model eğitimi** sayfasında **özel görüntü işleme proje aç**' a tıklayın.

1. Önceki adımda topladığınız görüntüleri görüntülemek için **özel görüntü işleme** sayfasının sol tarafında **Etiketler** altında **etiketsiz** ' e tıklayın. Etiketlenmemiş görüntülerinizden birini veya birkaçını seçin.

1. **Resim ayrıntısı** penceresinde, etiketlemeyi başlamak için görüntüye tıklayın. Proje türü olarak nesne algılamayı seçtiyseniz etiketlemek istediğiniz belirli nesneler etrafında de bir [sınırlayıcı kutu](../cognitive-services/custom-vision-service/get-started-build-detector.md#upload-and-tag-images) çizmeniz gerekir. Sınırlama kutusunu gereken şekilde ayarlayın. Nesne etiketinizi yazın ve **+** etiketi uygulamak için tıklayın. Örneğin, bir mağaza rafı yeniden daha geri çekmesini gerektiren bir adım adım çözüm oluşturuyorsanız, "boş raf" etiketini boş raflamaın görüntülerine ekleyin ve "tam raf" etiketini tam olarak stoklama raflarını ekleyin. Etiketlenmemiş tüm görüntüler için tekrarlayın.

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="Özel Görüntü İşleme resim etiketleme ekranı.":::

1. Görüntülerinizi etiketledikten sonra pencerenin sağ üst köşesindeki **X** simgesine tıklayın. Yeni etiketlenmiş görüntülerinizin tümünü görüntülemek için **Etiketler** altında **etiketli** ' e tıklayın.

1. Görüntüleriniz etiketlendikten sonra, AI modelinizi eğmeye hazırlanın. Bunu yapmak için sayfanın üst kısmındaki **eğitme** ' ye tıklayın. Modelinize eğitebilmeniz için etiket türü başına en az 15 görüntünüz olmalıdır (en az 30 kullanmanızı öneririz). Eğitim genellikle yaklaşık 30 dakika sürer, ancak görüntü kümesi çok büyükse bu işlem daha uzun sürebilir.

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="Tren düğmesi vurgulanmış şekilde eğitim resmi seçimi.":::

1. Eğitim tamamlandığında, ekranınız model performansınızı gösterir. Bu sonuçları değerlendirme hakkında daha fazla bilgi için lütfen [model değerlendirme belgelerine](../cognitive-services/custom-vision-service/get-started-build-detector.md#evaluate-the-detector)bakın. Eğitiminden sonra modelinizi ek görüntülerle [Test](../cognitive-services/custom-vision-service/test-your-model.md) etmek ve gerektiğinde yeniden eğmek isteyebilirsiniz. Modelinize her eğişinizde, yeni bir yineleme olarak kaydedilir. Model performansını geliştirme hakkında daha fazla bilgi için [özel görüntü işleme belgelerine](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) başvurun.

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="Eğitim sonuçlarını modelleyin.":::

    > [!NOTE]
    > Modelinizi Özel Görüntü İşleme ek görüntülerle test etmek isterseniz, test görüntü dosyası boyutunun 4MB ' i aşmaması gerektiğini unutmayın.

Modelinizin performansını karşıladıktan sonra tarayıcı sekmesini kapatarak Özel Görüntü İşleme kapatın.

## <a name="deploy-your-ai-model"></a>AI modelinizi dağıtma

1. Azure Percept Studio sekmesine dönün ve Ileri ' ye tıklayın **: Ekranınızın altındaki değerlendir ve dağıt** .

1. **Değerlendir ve dağıt** penceresinde, seçili model yinelemesinin performansı gösterilir. **Model yineleme** açılan menüsünde devkit 'e dağıtmak istediğiniz yinelemeyi seçin ve ekranın altındaki **modeli dağıt** ' a tıklayın.

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="Model dağıtım ekranı." lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. Modelinizi dağıttıktan sonra, modelinizin işlem sırasında modelini görmek için cihazınızın video akışını görüntüleyin.

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="İşlem içinde kulaklık algılayıcısının gösterildiği cihaz akışı.":::

Bu pencereyi kapattıktan sonra, Azure Percept Studio giriş sayfasındaki **AI projeleri** **' nin altında bulunan Vision '** a tıklayarak ve Vision projenizin adını seçerek, bir daha geri dönüp Vision projenizi düzenleyebilirsiniz.

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="Vizyon Projesi sayfası." lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="improve-your-model-by-setting-up-retraining"></a>Yeniden eğitim ayarlayarak modelinizi geliştirme

Modelinizi eğitip cihaza dağıttıktan sonra, daha fazla eğitim verisi yakalamak için yeniden eğitim parametreleri ayarlayarak model performansını artırabilirsiniz. Bu özellik, bir olasılık aralığına göre görüntüleri yakalama olanağı sunarak eğitilen bir modelin performansını geliştirmek için kullanılır. Örneğin, Cihazınızı yalnızca olasılık düşük olduğunda eğitim görüntülerini yakalamaya ayarlayabilirsiniz. Daha fazla görüntü ekleme ve eğitim verileri dengeleme hakkında bazı [ek yönergeler](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) aşağıda verilmiştir.

1. Yeniden eğitimi ayarlamak için **projenize** dönün ve ardından **Proje Özeti** ' ne gidin
1. **Görüntü yakalama** sekmesinde **Otomatik görüntü yakalama** ve **yeniden eğitim ayarla**' yı seçin.
1. Otomatik görüntü yakalama kutusunu denetleyerek, tek seferde büyük miktarda görüntü toplamak için **Otomatik** görüntü yakalamayı ayarlayın.
1. **Yakalama hızı** altında tercih ettiğiniz görüntüleme hızını ve **hedef** altında toplamak istediğiniz toplam görüntü sayısını seçin.
1. **Yeniden eğitimi ayarla** bölümünde, için daha fazla eğitim verisi yakalamak istediğiniz yinelemeyi seçin ve ardından olasılık aralığını seçin. Yalnızca olasılık oranını karşılayan görüntüler projenize yüklenir.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-image-capture.png" alt-text="görüntü yakalama.":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici için yeni bir Azure kaynağı oluşturduysanız ve artık görme çözümünüzü geliştirmek veya kullanmak istemiyorsanız, kaynağı silmek için aşağıdaki adımları gerçekleştirin:

1. [Azure Portal](https://ms.portal.azure.com/) gidin.
1. **Tüm kaynaklar**' a tıklayın.
1. Bu öğretici sırasında oluşturulan kaynağın yanındaki onay kutusuna tıklayın. Kaynak türü bilişsel **Hizmetler** olarak listelenecektir.
1. Ekranın üst kısmındaki **Sil** simgesine tıklayın.

## <a name="video-walkthrough"></a>Görüntülü kılavuz

Yukarıda açıklanan adımlara görsel bir anlatım için lütfen aşağıdaki videoya bakın:

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

</br>

## <a name="next-steps"></a>Sonraki Adımlar

Daha sonra, Azure Percept Studio 'daki ek Vision çözüm özellikleri hakkında bilgi için bkz. nasıl yapılır makalelerini inceleyin.

<!--
Add links to how-to articles and oobe article.
-->