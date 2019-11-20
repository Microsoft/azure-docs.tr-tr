---
title: Özel Konuşma Tanıma konuşma hizmeti ile çalışmaya başlama
titleSuffix: Azure Cognitive Services
description: Özel Konuşma Tanıma, uygulamalarınız, araçlarınız ve ürünleriniz için konuşmadan metne doğruluğu değerlendirmenize ve iyileştirmenize olanak tanıyan çevrimiçi araçlar kümesidir. Kullanmaya başlamak her şey en çok bir test ses dosyası. Özel bir konuşmaya metin deneyimi oluşturmaya başlamak için aşağıdaki bağlantıları izleyin.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 0f3b7826fdcc16d3094785507ec8263da39d6284
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881519"
---
# <a name="what-is-custom-speech"></a>Özel Konuşma Tanıma nedir?

[Özel konuşma tanıma](https://aka.ms/customspeech) , Microsoft 'un uygulamalarınız, araçları ve ürünleriniz için konuşmayı metne doğruluğu değerlendirmenize ve iyileştirmenize olanak tanıyan çevrimiçi araçlar kümesidir. Kullanmaya başlamak her şey en çok bir test ses dosyası. Özel bir konuşmaya metin deneyimi oluşturmaya başlamak için aşağıdaki bağlantıları izleyin.

## <a name="whats-in-custom-speech"></a>Özel Konuşma Tanıma neler?

Özel Konuşma Tanıma herhangi bir şey yapabilmeniz için önce bir Azure hesabı ve bir konuşma Hizmetleri aboneliğine sahip olmanız gerekir. Bir hesap aldıktan sonra verilerinizi dağıtabilir, modellerinizi eğitebilir ve test edebilir, tanınma kalitesini inceleyebilir, doğruluğu değerlendirebilir ve son olarak özel konuşmayı metin modelini kullanabilirsiniz.

Bu diyagramda [özel konuşma tanıma portalını](https://aka.ms/customspeech)oluşturan parçalar vurgulanmıştır. Her adım hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kullanın.

![Özel Konuşma Tanıma portalını oluşturan farklı bileşenleri vurgular.](./media/custom-speech/custom-speech-overview.png)

1. [Abone olun ve proje oluşturun](#set-up-your-azure-account) -bir Azure hesabı oluşturun ve konuşma hizmetlerini abone olun. Bu Birleşik abonelik, konuşmadan metne, metinden konuşmaya, konuşma çevirisine ve [özel konuşma tanıma portalına](https://speech.microsoft.com/customspeech)erişmenizi sağlar. Ardından, konuşma Hizmetleri aboneliğinizi kullanarak ilk Özel Konuşma Tanıma projenizi oluşturun.

2. [Test verilerini karşıya yükleme](how-to-custom-speech-test-data.md) -Microsoft 'un uygulamalarınız, araçları ve ürünleriniz için konuşma-metin tekliflerini değerlendirmek için test verilerini (ses dosyaları) karşıya yükleyin.

3. [Tanıma kalitesini inceleme](how-to-custom-speech-inspect-data.md) -karşıya yüklenen ses çalmak ve test verilerinizin konuşma tanıma kalitesini incelemek için [özel konuşma tanıma portalını](https://speech.microsoft.com/customspeech) kullanın. Nicel ölçümleri için bkz. [verileri İnceleme](how-to-custom-speech-inspect-data.md).

4. [Doğruluğu değerlendirin](how-to-custom-speech-evaluate-data.md) -konuşmaya metin modelinin doğruluğunu değerlendirin. [Özel konuşma tanıma Portal](https://speech.microsoft.com/customspeech) , ek eğitim gerekip gerekmediğini belirlemede kullanılabilecek bir *sözcük hata oranı*sağlar. Doğruluğun memnun kaldıysanız, konuşma hizmeti API 'Lerini doğrudan kullanabilirsiniz. % 5 oranında% 20 oranında doğru bir ortalama değeri artırmak isterseniz, insan etiketli döküm ve ilgili metin gibi ek eğitim verilerini karşıya yüklemek için portaldaki **eğitim** sekmesini kullanın.

5. [Modeli eğitme](how-to-custom-speech-train-model.md) -ses test verilerinize birlikte yazılan yazılı betikler (10-1000 saat) ve ilgili metin (< 200 MB) sağlayarak konuşmadan metne ait doğruluğun doğruluğunu geliştirir. Bu veriler, konuşmadan metne modeli eğmenize yardımcı olur. Eğitim ve yeniden test ettikten sonra, bu sonuçtan memnun kaldıysanız modelinize dağıtım yapabilirsiniz.

6. [Modeli dağıtma](how-to-custom-speech-deploy-model.md) -konuşma-metin modeliniz için özel bir uç nokta oluşturun ve bunları uygulamalarınızda, araçlarınızdan veya ürünlerde kullanın.

## <a name="set-up-your-azure-account"></a>Azure hesabınızı ayarlama

Özel bir model oluşturmak için [özel konuşma tanıma portalını](https://speech.microsoft.com/customspeech) kullanabilmeniz Için bir konuşma Hizmetleri aboneliği gerekir. Standart bir konuşma Hizmetleri aboneliği oluşturmak için aşağıdaki yönergeleri izleyin: [Konuşma aboneliği oluşturun](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account).

> [!NOTE]
> Lütfen standart (S0) abonelikler oluşturmanız, ücretsiz deneme (F0) aboneliklerinin desteklenmediğinden emin olun.

Bir Azure hesabı ve bir konuşma Hizmetleri aboneliği oluşturduktan sonra, [özel konuşma tanıma Portal](https://speech.microsoft.com/customspeech) 'da oturum açmanız ve aboneliğinizi bağlamanız gerekir.

1. Azure portal konuşma Hizmetleri abonelik anahtarınızı alın.
2. [Özel konuşma tanıma portalında](https://aka.ms/custom-speech)oturum açın.
3. Üzerinde çalışmanız gereken aboneliği seçin ve bir konuşma projesi oluşturun.
4. Aboneliğinizi değiştirmek isterseniz, üstteki gezinmede bulunan **dişli** simgesini kullanın.

## <a name="how-to-create-a-project"></a>Proje oluşturma

Veriler, modeller, testler ve uç noktalar gibi içerikler [özel konuşma tanıma portalındaki](https://speech.microsoft.com/customspeech) **Projeler** halinde düzenlenir. Her proje bir etki alanı ve ülke/dil için özeldir. Örneğin, Birleşik Devletler Ingilizce kullanan çağrı merkezleri için bir proje oluşturabilirsiniz.

İlk projenizi oluşturmak için **konuşmayı metne/özel konuşmayı**seçin ve ardından **Yeni proje**' ye tıklayın. Projenizi oluşturmak için sihirbaz tarafından sunulan yönergeleri izleyin. Bir projeyi oluşturduktan sonra dört sekme görmeniz gerekir: **Veri**, **Test**, **eğitim**ve **dağıtım**. Her bir sekmeyi nasıl kullanacağınızı öğrenmek için [sonraki adımlarda](#next-steps) sunulan bağlantıları kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [Verilerinizi hazırlayın ve test edin](how-to-custom-speech-test-data.md)
* [Verilerinizi inceleyin](how-to-custom-speech-inspect-data.md)
* [Verilerinizi değerlendirin](how-to-custom-speech-evaluate-data.md)
* [Modelinize eğitme](how-to-custom-speech-train-model.md)
* [Modelinizi dağıtın](how-to-custom-speech-deploy-model.md)
