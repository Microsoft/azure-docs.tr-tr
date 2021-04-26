---
title: Windows ML ile ONNX modeli kullanma-Özel Görüntü İşleme Hizmeti
titleSuffix: Azure Cognitive Services
description: Azure Bilişsel Hizmetler’den dışarı aktarılan bir ONNX modelini kullanan Windows UWP uygulamasının nasıl oluşturulacağını öğrenin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: 5ae333d0693071f1a5a5d12e87d6aa155ceccd3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055578"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Windows ML ile Özel Görüntü İşleme bir ONNX modeli kullanma (Önizleme)

Windows ML (önizleme) ile Özel Görüntü İşleme hizmetinden dışarı aktarılan bir ONNX modelinin nasıl kullanılacağını öğrenin.

Bu kılavuzda, Windows ML ile Özel Görüntü İşleme Hizmeti aktarılmış bir ONNX dosyasını nasıl kullanacağınızı öğreneceksiniz. Örnek UWP uygulamasını, eğitilen görüntü sınıflandırıcınızla kullanacaksınız.

## <a name="prerequisites"></a>Önkoşullar

* Windows 10 sürüm 1809 veya üzeri
* 17763 veya üzeri derleme Windows SDK
* __Evrensel Windows Platformu geliştirmesi__ iş yükü etkinleştirilmiş Visual Studio 2017 15.7 veya sonraki bir sürüm.
* Bilgisayarınızda geliştirici modu etkin. Daha fazla bilgi için bkz. [cihazınızı geliştirme Için etkinleştirme](/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Örnek uygulama hakkında

Dahil edilen uygulama, genel bir Windows UWP uygulamasıdır. Bundan sonra yerel olarak depolanmış bir sınıflandırma modeliyle işlenen bilgisayarınızdan bir görüntü seçmenizi sağlar. Model tarafından döndürülen etiketler ve puanlar görüntünün yanında görüntülenir.

## <a name="get-the-example-code"></a>Örnek kodunu alma

Örnek uygulama, GitHub 'da bulunan bilişsel [Hizmetler ONNX özel görüntü işleme örnek](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) depositesinde bulunur. Bunu yerel makinenize kopyalayın ve Visual Studio 'da *SampleOnnxEvaluationApp. sln* ' i açın.

## <a name="test-the-application"></a>Uygulamayı test edin

1. Visual Studio’dan uygulamayı başlatmak için `F5` tuşunu kullanın. Geliştirici modunu etkinleştirmeniz istenebilir.
1. Uygulama başlatıldığında düğmeyi kullanarak puanlanacak bir görüntü seçin. Varsayılan ONNX modeli, farklı tipte plankton sınıflandırmasına yönelik olarak tasarlanmıştır.

## <a name="use-your-own-model"></a>Kendi modelinizi kullanma

Kendi resim sınıflandırıcı modelinizi kullanmak için şu adımları izleyin:

1. Özel Görüntü İşleme Hizmeti ile bir sınıflandırıcı oluşturun ve eğitin. Bunun nasıl yapılacağı hakkında yönergeler için bkz. [sınıflandırıcı oluşturma ve eğitme](./getting-started-build-a-classifier.md). **Genel (Compact)** gibi **Compact** etki alanlarından birini kullanın. 
   * Farklı bir etki alanı kullanan mevcut bir sınıflandırıcınızı varsa, proje ayarlarında onu **düzenlemeye** dönüştürebilirsiniz. Ardından, devam etmeden önce projenizi yeniden eğitme.
1. Modelinizi dışarı aktarın. Performans sekmesine geçin ve bir **Compact** etki alanı ile eğitilen bir yineleme seçin. Görüntülenen **dışarı aktar** düğmesini seçin. Sonra **Onnx**' i ve ardından **dışarı aktar**' ı seçin. Dosya hazır duruma geldikten sonra **İndir** düğmesini seçin. Dışarı aktarma seçenekleri hakkında daha fazla bilgi için bkz. [modelinizi dışarı aktarma](./export-your-model.md).
1. İndirilen *. zip* dosyasını açın ve *modelden model. onnx* dosyasını ayıklayın. Bu dosya, sınıflandırıcı modelinizi içerir.
1. Visual Studio 'daki Çözüm Gezgini **varlıklar** klasörüne sağ tıklayın ve __Varolan öğe Ekle__' yi seçin. ONNX dosyanızı seçin.
1. Çözüm Gezgini, ONNX dosyasına sağ tıklayın ve **Özellikler**' i seçin. Dosya için aşağıdaki özellikleri değiştirin:
   * __Derleme eylemi__  ->  __İçerik__
   * __Çıkış Dizinine Kopyala__  ->  __Daha yeniyse kopyala__
1. Ardından _MainPage. xaml. cs_ dosyasını açın ve değerini `_ourOnnxFileName` onnx dosyanızın adıyla değiştirin.
1. `F5`Projeyi derlemek ve çalıştırmak için öğesini kullanın.
1. Değerlendirilecek görüntüyü seçmek için düğmeye tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Başka dışarı aktarma yollarını keşfetmek ve Özel Görüntü İşleme modelini kullanmak için aşağıdaki belgelere bakın:

* [Verilerinizi dışarı aktarma](./export-your-model.md)
* [Android uygulamasında dışarı aktarılan Tensorflow modelini kullanma](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Swift iOS uygulamasında dışarı aktarılan CoreML modelini kullanma](https://go.microsoft.com/fwlink/?linkid=857726)
* [Xamarin ile iOS uygulamasında dışarı aktarılan CoreML modelini kullanma](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Windows ML ile ONNX modellerini kullanma hakkında daha fazla bilgi için bkz. [WINDOWS ml ile bir modeli uygulamanıza tümleştirme](/windows/ai/windows-ml/integrate-model).