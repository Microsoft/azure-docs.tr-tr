---
title: 'Öğretici: Windows ML ile ONNX modeli kullanma - Özel Görüntü İşleme Hizmeti'
titleSuffix: Azure Cognitive Services
description: Azure Bilişsel Hizmetler’den dışarı aktarılan bir ONNX modelini kullanan Windows UWP uygulamasının nasıl oluşturulacağını öğrenin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 7877f24ee77fa694745d0af0a99778fc53cd71e3
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754143"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Öğretici: Windows ML (önizleme) ile Özel Görüntü İşleme hizmetinden ONNX modeli kullanma

Windows ML (önizleme) ile Özel Görüntü İşleme hizmetinden dışarı aktarılan bir ONNX modelinin nasıl kullanılacağını öğrenin.

Bu belgedeki bilgiler, Windows ML ile Özel Görüntü İşleme Hizmeti’nden dışarı aktarılan bir ONNX dosyasının nasıl kullanılacağını göstermektedir. Örnek bir Windows UWP uygulaması sağlanmıştır. Örneğe, tanıyabilen bir eğitimli model eklenmiştir. Bu örnek ile kendi modelinizi nasıl kullanabileceğinize ilişkin adımlar da sağlanmıştır.

> [!div class="checklist"]
> * Örnek uygulama hakkında
> * Örnek kodunu alma
> * Örneği çalıştırma
> * Kendi modelinizi kullanma

## <a name="prerequisites"></a>Önkoşullar

* Windows 10 sürüm 1809 veya üzeri

* 17763 veya üzeri derleme Windows SDK

* __Evrensel Windows Platformu geliştirmesi__ iş yükü etkinleştirilmiş Visual Studio 2017 15.7 veya sonraki bir sürüm.

* Etkinleştirilmiş geliştirici modu. Daha fazla bilgi için [Geliştirme için cihazınızı etkinleştirme](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) belgesine bakın.

## <a name="about-the-example-app"></a>Örnek uygulama hakkında

Uygulama, genel bir Windows UWP uygulamasıdır. Bilgisayarınızdan bir görüntü seçerek modele göndermenizi sağlar. Model tarafından döndürülen etiketler ve puanlar görüntünün yanında görüntülenir.

## <a name="get-the-example-code"></a>Örnek kodunu alma

Örnek uygulamaya [https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) konumundan erişilebilir.

## <a name="run-the-example"></a>Örneği çalıştırma

1. Visual Studio’dan uygulamayı başlatmak için `F5` tuşunu kullanın. Geliştirici modunu etkinleştirmeniz istenebilir. Daha fazla bilgi için [Geliştirme için cihazınızı etkinleştirme](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development) belgesine bakın.

1. Uygulama başlatıldığında düğmeyi kullanarak puanlanacak bir görüntü seçin.

## <a name="use-your-own-model"></a>Kendi modelinizi kullanma

Kendi modelinizi kullanmak için aşağıdaki adımları uygulayın:

1. Özel Görüntü İşleme Hizmeti ile bir sınıflandırıcı [oluşturun ve eğitin](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier). Modeli dışarı aktarmak için **Genel (sıkıştırılmış)** gibi bir __sıkıştırılmış__ etki alanı seçin. Var olan bir sınıflandırıcıyı dışarı aktarmak için sağ üstteki dişli simgesini seçerek etki alanını sıkıştırın. __Ayarlar__ sayfasında sıkıştırılmış bir model seçin, projenizi kaydedin ve eğitin.  

1. Performans sekmesine giderek [modelinizi dışarı aktarın](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) . bir Compact etki alanı ile eğitilen bir yineleme seçin, "dışarı aktar" düğmesi görünür. *Dışarı aktar*, *onnx*ve ardından *dışarı aktar*' ı seçin. Dosya hazır duruma geldikten sonra *İndir* düğmesini seçin.

1. ONNX dosyasını projenizin __Varlıklar__ klasörüne bırakın. 

1. Çözüm Gezgini'nde Varlıklar klasörüne sağ tıklayın ve __Var Olan Öğe Ekle__'yi seçin. ONNX dosyasını seçin.

1. Çözüm Gezgini'nde Varlıklar klasöründen ONNX dosyasını seçin. Dosya için aşağıdaki özellikleri değiştirin:

    * __Derleme Eylemi__ -> __İçerik__
    * __Çıkış Dizinine Kopyala__ -> __Daha yeniyse kopyala__

1. `_onnxFileNames` değişkenini ONNX dosyasının adıyla değiştirin. Ayrıca `ClassLabel` değerini de modelde bulunan etiket sayısına göre değiştirin.

1. Derleyin ve çalıştırın.

1. Değerlendirilecek görüntüyü seçmek için düğmeye tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Başka dışarı aktarma yollarını keşfetmek ve Özel Görüntü İşleme modelini kullanmak için aşağıdaki belgelere bakın:

* [Verilerinizi dışarı aktarma](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Android uygulamasında dışarı aktarılan Tensorflow modelini kullanma](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Swift iOS uygulamasında dışarı aktarılan CoreML modelini kullanma](https://go.microsoft.com/fwlink/?linkid=857726)
* [Xamarin ile iOS uygulamasında dışarı aktarılan CoreML modelini kullanma](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Windows ML ile ONNX modellerini kullanma hakkında daha fazla bilgi için [Windows ML ile uygulamanızda bir modeli tümleştirme](/windows/ai/windows-ml/integrate-model) belgesine bakın.
