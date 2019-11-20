---
title: Özel Konuşma Tanıma konuşma hizmeti için doğruluğu değerlendirin
titleSuffix: Azure Cognitive Services
description: Bu belgede, konuşmadan metin modelimizin veya özel modelinizin kalitesini nasıl ölçeceğinizi öğreneceksiniz. Ses + insan etiketli Döküm verileri, doğruluk sınaması için gereklidir ve 5 saatlik temsili ses sağlanmalıdır.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 77dc50df7b46aeb6ddfddf92710d576f85492471
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801552"
---
# <a name="evaluate-custom-speech-accuracy"></a>Doğruluk Özel Konuşma Tanıma değerlendirin

Bu belgede, Microsoft 'un konuşmaya metin modeli veya özel modeliniz kalitesini nasıl ölçeceğinizi öğreneceksiniz. Ses + insan etiketli Döküm verileri, doğruluk sınaması için gereklidir ve 5 saatlik temsili ses sağlanmalıdır.

## <a name="what-is-word-error-rate-wer"></a>Word hata oranı (WER) nedir?

Model doğruluğunu ölçmek için sektör standardı, *sözcük hata oranı* (WER). WER, tanıma sırasında tanımlanan yanlış sözcüklerin sayısını sayar ve sonra, insan etiketli döküm dosyasında belirtilen toplam sözcük sayısına böler. Son olarak, bu sayı WER 'i hesaplamak için% 100 ile çarpılır.

![WER formülü](./media/custom-speech/custom-speech-wer-formula.png)

Yanlış tanımlanmış kelimeler üç kategoride yer almalıdır:

* Ekleme (ı): Varsayıma betiğine yanlış eklenen sözcükler
* Silme (D): Varsayım dökümü 'nde algılanmayan sözcükler
* Değiştirme (ler): Başvuru ve varsayım arasında değiştirilen kelimeler

Bir örneği aşağıda verilmiştir:

![Yanlış tanımlanmış sözcüklerin örneği](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Hataları çözün ve WER 'i geliştirebilirsiniz

Uygulamanızı, aracınız veya ürününüzle kullandığınız modelin kalitesini değerlendirmek için makine tanıma sonuçlarından WER ' i kullanabilirsiniz. % 5 oranında bir WER, iyi bir kalite gibi kabul edilir ve kullanıma hazır hale gelir. % 20 oranında bir WER kabul edilebilir, ancak ek eğitime göz önüne almanız gerekebilir. % 30 veya daha fazla bir WER, kalite ve eğitim gerektirir.

Hataların dağıtılması önemlidir. Birçok silme hatası ile karşılaşıldığında, genellikle zayıf ses sinyali gücü nedeniyle oluşur. Bu sorunu çözmek için, kaynağa daha yakın ses verileri toplamanız gerekir. Ekleme hataları, sesin gürültülü bir ortama kaydedildiği ve Crosstalk mevcut olabileceği ve tanınma sorunlarına neden olduğu anlamına gelir. Değiştirme hatalarıyla genellikle, etki alanına özgü koşulların yetersiz bir örneği, insan etiketli döküm veya ilgili metin olarak sağlandıysa, bu durum oluşur.

Tek tek dosyaları çözümleyerek, hangi tür hataların var olduğunu ve belirli bir dosyaya hangi hataların benzersiz olduğunu belirleyebilirsiniz. Dosya düzeyindeki sorunları anlamak, iyileştirmeleri hedefetmenize yardımcı olur.

## <a name="create-a-test"></a>Test oluşturma

Microsoft 'un konuşmaya metin taban çizgisi modeli veya eğitilen özel bir model kalitesini test etmek isterseniz doğruluğu değerlendirmek için iki modeli yan yana karşılaştırabilirsiniz. Karşılaştırma, WER ve tanıma sonuçlarını içerir. Genellikle, özel bir model Microsoft 'un temel modeliyle karşılaştırılır.

Modelleri yan yana değerlendirmek için:

1. [Özel konuşma tanıma portalında](https://speech.microsoft.com/customspeech)oturum açın.
2. **> konuşmadan metne git Özel Konuşma Tanıma > testi**yapın.
3. **Test Ekle**' ye tıklayın.
4. **Doğruluğu değerlendir**' i seçin. Teste bir ad, açıklama verin ve ses + insan etiketli döküm veri kümenizi seçin.
5. Test etmek istediğiniz en fazla iki model seçin.
6. **Oluştur**'a tıklayın.

Testiniz başarıyla oluşturulduktan sonra sonuçları yan yana karşılaştırabilirsiniz.

## <a name="side-by-side-comparison"></a>Yan yana karşılaştırma

Test tamamlandıktan sonra durum değişikliği *başarılı*olarak belirtilmişse, testinizde her iki model için de bir wer numarası bulacaksınız. Test ayrıntısı sayfasını görüntülemek için test adına tıklayın. Bu ayrıntı sayfasında, veri kümenizdeki tüm bildirimler listelenir. Bu, gönderilen veri kümesinden gelen döküm ile birlikte iki modelin tanınma sonuçlarını gösterir. Yan yana karşılaştırmayı incelemeye yardımcı olması için ekleme, silme ve değiştirme gibi çeşitli hata türlerini değiştirebilirsiniz. Her bir sütunda sesli dinleyerek ve iki konuşmadan metne modellerle ilgili sonuçları gösteren her bir sütundaki tanıma sonuçlarını karşılaştıran şekilde, hangi modelin ihtiyaçlarınıza uygun olduğunu ve ek eğitim ve geliştirmelerin nerede olduğunu seçebilirsiniz. Gerekli.

## <a name="next-steps"></a>Sonraki adımlar

* [Modelinize eğitme](how-to-custom-speech-train-model.md)
* [Modelinizi dağıtın](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ek kaynaklar

* [Verilerinizi hazırlayın ve test edin](how-to-custom-speech-test-data.md)
* [Verilerinizi inceleyin](how-to-custom-speech-inspect-data.md)
