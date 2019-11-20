---
title: Modelinizi mobil Özel Görüntü İşleme Hizmeti dışa aktarma
titleSuffix: Azure Cognitive Services
description: Bu makalede, modelinizi mobil uygulamalar oluşturmak veya gerçek zamanlı sınıflandırma için yerel olarak çalıştırmak üzere nasıl dışarı aktarmak için kullanabileceğiniz gösterilmektedir.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718958"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Modelinizi mobil cihazlarla kullanılmak üzere dışarı aktarma

Özel Görüntü İşleme Hizmeti, sınıflandırıcıın çevrimdışı çalışacak şekilde verilmesini sağlar. Verdiğiniz sınıflandırmanızı bir uygulamaya ekleyebilir ve gerçek zamanlı sınıflandırma için bir cihazda yerel olarak çalıştırabilirsiniz.

## <a name="export-options"></a>Dışarı aktarma seçenekleri

Özel Görüntü İşleme Hizmeti aşağıdaki dışarı aktarmaları destekler:

* __Android__Için __TensorFlow__ .
* __İOS11__Için __coreml__ .
* __WINDOWS ml__Için __onnx__ .
* __[VISION AI geliştirici seti](https://azure.github.io/Vision-AI-DevKit-Pages/)__ .
* Windows, Linux veya ARM mimarisi için bir __Docker kapsayıcısı__ . Kapsayıcı, Özel Görüntü İşleme API 'sini kullanmak için bir TensorFlow modeli ve hizmet kodu içerir.

> [!IMPORTANT]
> Özel Görüntü İşleme Hizmeti yalnızca __Compact__ etki alanlarını dışarı aktarır. Compact etki alanları tarafından oluşturulan modeller, mobil cihazlardaki gerçek zamanlı sınıflandırmanın kısıtlamaları için iyileştirilmiştir. Kompakt bir etki alanıyla oluşturulan sınıflandırıcılar, aynı miktardaki eğitim verilerine sahip standart bir etki alanından biraz daha az doğru olabilir.
>
> Sınıflandırıcılarınızı iyileştirme hakkında daha fazla bilgi için bkz. [sınıflandırıcınızı geliştirme](getting-started-improving-your-classifier.md) belgesi.

## <a name="convert-to-a-compact-domain"></a>Compact etki alanına Dönüştür

> [!NOTE]
> Bu bölümdeki adımlar yalnızca, Compact Domain olarak ayarlanmamış mevcut bir modeliniz varsa geçerlidir.

Varolan bir modelin etki alanını dönüştürmek için aşağıdaki adımları uygulayın:

1. [Özel Vision Web sitesinde](https://customvision.ai), projelerinizin listesini görüntülemek için __giriş__ simgesini seçin.

    ![Giriş simgesinin ve Projeler listesinin görüntüsü](./media/export-your-model/projects-list.png)

1. Bir proje seçin ve sayfanın sağ üst kısmındaki __dişli__ simgesini seçin.

    ![Dişli simgesinin görüntüsü](./media/export-your-model/gear-icon.png)

1. __Etki alanları__ bölümünde, __Compact__ etki alanlarından birini seçin. Değişiklikleri kaydetmek için __Değişiklikleri Kaydet__ ' i seçin. 

    > [!NOTE]
    > Vision AI Dev Kit, projenin __Genel (Compact)__ etki alanıyla oluşturulması ve **dışa aktarma ÖZELLIKLERI** bölümünde **Vision AI Dev Kit** seçeneğini belirtmeniz gerekir.

    ![Etki alanı seçiminin görüntüsü](./media/export-your-model/domains.png)

1. Sayfanın üst kısmından yeni etki alanını kullanarak yeniden eğitedenemek için __eğitme__ ' yi seçin.

## <a name="export-your-model"></a>Modelinizi dışarı aktarma

Yeniden öğreticduktan sonra modeli dışarı aktarmak için aşağıdaki adımları kullanın:

1. **Performans** sekmesine gidin ve __dışarı aktar__' ı seçin. 

    ![Dışa aktarma simgesinin görüntüsü](./media/export-your-model/export.png)

    > [!TIP]
    > __Dışarı aktarma__ girdisi kullanılamıyorsa, seçilen yineleme bir Compact etki alanı kullanmaz. Sıkıştırılmış etki alanı kullanan bir yinelemeyi seçmek için bu sayfanın __yinelemeler__ bölümünü kullanın ve ardından __dışarı aktar__' ı seçin.

1. İstediğiniz dışa aktarma biçiminizi seçin ve ardından modeli indirmek için __dışarı aktar__ ' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerden veya örneklerden birini inceleyerek, verdiğiniz modeli bir uygulamayla tümleştirin:

* [Python ile TensorFlow modelinizi kullanma](export-model-python.md)
* [Windows Machine Learning ile ONNX modelinizi kullanın](custom-vision-onnx-windows-ml.md)
* Swift ile gerçek zamanlı görüntü sınıflandırması için [bir iOS uygulamasındaki Coreml modeli](https://go.microsoft.com/fwlink/?linkid=857726) örneğine bakın.
* Android 'de gerçek zamanlı görüntü sınıflandırması için [Android uygulamasındaki TensorFlow modeli](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) örneğine bakın.
* Xamarin iOS uygulamasındaki gerçek zamanlı görüntü sınıflandırması için [Xamarin Ile Coreml modeli](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) örneğine bakın.
