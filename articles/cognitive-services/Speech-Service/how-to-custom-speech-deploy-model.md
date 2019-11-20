---
title: Özel Konuşma Tanıma konuşma hizmeti için model dağıtma
titleSuffix: Azure Cognitive Services
description: Bu belgede, Özel Konuşma Tanıma portalını kullanarak bir uç nokta oluşturmayı ve dağıtmayı öğreneceksiniz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: fcd3e5fd6db0d7c18a5f9bfba030cd3d809b452f
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802526"
---
# <a name="deploy-a-custom-model"></a>Özel bir modeli dağıtma

Verileri karşıya yükledikten ve inceledikten, doğruluğu değerlendirdikten ve özel bir model eğitilene kadar, uygulamalarınız, araçlarınızla ve ürünlerinizle kullanmak üzere özel bir uç nokta dağıtabilirsiniz. Bu belgede, [özel konuşma tanıma portalını](https://speech.microsoft.com/customspeech)kullanarak bir uç nokta oluşturmayı ve dağıtmayı öğreneceksiniz.

## <a name="create-a-custom-endpoint"></a>Özel uç nokta oluşturma

Yeni bir özel uç nokta oluşturmak için [özel konuşma tanıma portalında](https://speech.microsoft.com/customspeech) oturum açın ve sayfanın üst kısmındaki özel konuşma tanıma menüsünden **dağıtım** ' yi seçin. İlk kez çalıştırıldıysanız tabloda listelenen bitiş noktaları olmadığını fark edeceksiniz. Bir uç nokta oluşturduktan sonra, bu sayfayı dağıtılan her bitiş noktasını izlemek için kullanacaksınız.

Sonra, **uç nokta Ekle** ' yi seçin ve özel uç noktanız Için bir **ad** ve **Açıklama** girin. Ardından, bu uç nokta ile ilişkilendirmek istediğiniz özel modeli seçin. Bu sayfadan günlüğe kaydetmeyi de etkinleştirebilirsiniz. Günlüğe kaydetme, uç nokta trafiğini izlemenizi sağlar. Devre dışı bırakılırsa trafik depolanmaz.

![Model dağıtma](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Kullanım koşullarını ve fiyatlandırma ayrıntılarını kabul etmek zorunda kalmazsınız.

Sonra **Oluştur**' u seçin. Bu eylem sizi **dağıtım** sayfasına döndürür. Tablo artık özel uç noktanıza karşılık gelen bir giriş içerir. Uç noktanın durumu geçerli durumunu gösterir. Özel modellerinizi kullanarak yeni bir uç noktanın örneklendirilecek 30 dakikaya kadar zaman alabilir. Dağıtımın durumu **tamamlandığında**, uç nokta kullanıma kullanılır.

Uç noktanız dağıtıldıktan sonra, uç nokta adı bir bağlantı olarak görünür. Uç nokta anahtarı, uç nokta URL 'SI ve örnek kod gibi uç noktanıza özgü bilgileri göstermek için bağlantıya tıklayın.

## <a name="view-logging-data"></a>Günlük verilerini görüntüleme

Günlüğe kaydetme verileri, **uç nokta > ayrıntıları**altında indirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Konuşma SDK 'sı](speech-sdk.md) ile özel uç noktanızı kullanın

## <a name="additional-resources"></a>Ek kaynaklar

* [Verilerinizi hazırlayın ve test edin](how-to-custom-speech-test-data.md)
* [Verilerinizi inceleyin](how-to-custom-speech-inspect-data.md)
* [Verilerinizi değerlendirin](how-to-custom-speech-evaluate-data.md)
* [Modelinize eğitme](how-to-custom-speech-train-model.md)
* [Modelinizi dağıtın](how-to-custom-speech-deploy-model.md)
