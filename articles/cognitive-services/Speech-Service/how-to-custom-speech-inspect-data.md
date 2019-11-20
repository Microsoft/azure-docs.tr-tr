---
title: Özel Konuşma Tanıma konuşma hizmeti için veri kalitesini İnceleme
titleSuffix: Azure Cognitive Services
description: Özel Konuşma Tanıma, ses verilerini karşılık gelen tanıma sonucuyla karşılaştırarak bir modelin tanınma kalitesini görsel olarak incelemenize olanak sağlayan araçlar sağlar. Karşıya yüklenen sesi kayıttan yürütebilir ve belirtilen tanıma sonucunun doğru olup olmadığını belirleyebilirsiniz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: cf0deffb6fe484f7e3ee38b95f285bb4b7cc809c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072571"
---
# <a name="inspect-custom-speech-data"></a>Özel Konuşma Tanıma verilerini İnceleme

> [!NOTE]
> Bu sayfa, [özel konuşma tanıma için test verilerini hazırlama](how-to-custom-speech-test-data.md) okuduğunuzu ve İnceleme için bir veri kümesi yüklediğinizi varsayar.

Özel Konuşma Tanıma, ses verilerini karşılık gelen tanıma sonucuyla karşılaştırarak bir modelin tanınma kalitesini görsel olarak incelemenize olanak sağlayan araçlar sağlar. [Özel konuşma tanıma portalından](https://speech.microsoft.com/customspeech)karşıya yüklenen sesi kayıttan yürütebilir ve belirtilen tanınma sonucunun doğru olup olmadığını belirleyebilirsiniz. Bu araç, herhangi bir ses verisi almak zorunda kalmadan Microsoft 'un temel konuşma konuşmadan metin modeli veya eğitilen özel bir modelin kalitesini hızlıca incelemenizi sağlar.

Bu belgede, daha önce karşıya yüklediğiniz eğitim verilerini kullanarak bir modelin kalitesini görsel olarak incelemeyi öğreneceksiniz.

Bu sayfada, Microsoft 'un temel konuşma konuşmadan metin modeli ve/veya eğitilen özel bir modelin kalitesini görsel olarak incelemeyi öğreneceksiniz. Test için **veri** sekmesine yüklediğiniz verileri kullanacaksınız.

## <a name="create-a-test"></a>Test oluşturma

Bir test oluşturmak için aşağıdaki yönergeleri izleyin:

1. [Özel konuşma tanıma portalında](https://speech.microsoft.com/customspeech)oturum açın.
2. **> konuşmadan metne git Özel Konuşma Tanıma > testi**yapın.
3. **Test Ekle**' ye tıklayın.
4. **İnceleme kalitesi (yalnızca ses verileri)** seçeneğini belirleyin. Teste bir ad, açıklama verin ve ses veri kümenizi seçin.
5. Test etmek istediğiniz en fazla iki model seçin.
6. **Oluştur**’ tıklayın.

Bir test başarıyla oluşturulduktan sonra modelleri yan yana karşılaştırabilirsiniz.

## <a name="side-by-side-model-comparisons"></a>Yan yana model karşılaştırmaları

Test durumu _başarılı_olduğunda testin ayrıntılarını görmek için test öğesi adına tıklayın. Bu ayrıntı sayfasında, veri kümenizdeki tüm bildirimler listelenir. Bu, gönderilen veri kümesinden gelen döküm ile birlikte iki modelin tanınma sonuçlarını gösterir.

Yan yana karşılaştırmayı incelemeye yardımcı olması için ekleme, silme ve değiştirme gibi çeşitli hata türlerini değiştirebilirsiniz. Her sütunda ses dinlemek ve tanıma sonuçlarını karşılaştırmak yoluyla (insan tarafından etiketlenmiş bir konum ve iki konuşmadan metne ait sonuçları gösterir), hangi modelin ihtiyaçlarınıza uygun olduğunu ve iyileştirmelerin gerekli olduğunu belirtebilirsiniz.

Bir konuşma tanıma uç noktası kalitesinin bir uygulama için yeterli olup olmadığını doğrulamak için kalite testi inceleniyor yararlı olur. Bir doğruluğun bir amaç ölçüsü için, daha fazla ses kullanılmasını gerektiren [doğruluğu değerlendir](how-to-custom-speech-evaluate-data.md)bölümünde bulunan yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Verilerinizi değerlendirin](how-to-custom-speech-evaluate-data.md)
- [Modelinize eğitme](how-to-custom-speech-train-model.md)
- [Modelinizi dağıtın](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ek kaynaklar

- [Özel Konuşma Tanıma için test verilerini hazırlama](how-to-custom-speech-test-data.md)
