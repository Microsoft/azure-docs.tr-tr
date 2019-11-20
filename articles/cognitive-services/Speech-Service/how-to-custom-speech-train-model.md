---
title: Özel Konuşma Tanıma konuşma hizmeti için model eğitme
titleSuffix: Azure Cognitive Services
description: Konuşmaya metin modeli eğitimi, Microsoft 'un temel modeli veya özel bir model için tanıma doğruluğunu iyileştirebilir. Model, insan etiketli döküm ve ilgili metin kullanılarak eğitilir.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 7630659deeece7fbf8d0ca1fd00b539a8de83b0e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072489"
---
# <a name="train-a-model-for-custom-speech"></a>Özel Konuşma Tanıma için model eğitme

Konuşmaya metin modeli eğitimi, Microsoft 'un temel modeli veya oluşturmayı planladığınız özel bir model için tanıma doğruluğunu iyileştirebilir. Model, insan etiketli döküm ve ilgili metin kullanılarak eğitilir. Daha önce karşıya yüklenen ses verileriyle birlikte bu veri kümeleri, sözcükleri, tümceleri, kısaltmaları, adları ve ürüne özgü diğer koşulları tanımak üzere konuşmayı metne dönüştürme ve eğitim için kullanılır. Sağladığınız daha fazla etki alanı veri kümesi (kullanıcıların söyledikleri ve tanımak istediğiniz veriler ile ilgili veriler), modelinizin daha doğru olur ve bu da daha iyi bir tanıma sonucu elde edilir. İlgisiz verileri eğitimlere besleyerek, modelinize ait doğruluğu azaltabilir veya zarar verebilir.

## <a name="use-training-to-resolve-accuracy-issues"></a>Doğruluk sorunlarını gidermek için eğitim kullanma

Modelinize yönelik tanınma sorunlarıyla karşılaşdıysanız, insan etiketli döküm ve ek eğitim için ilgili verilerin kullanılması doğruluğu artırmaya yardımcı olabilir. Bu tabloyu kullanarak, sorununuzu gidermek için hangi veri kümesinin kullanılacağını saptayın:

| Kullanım örneği | Veri türü |
| -------- | --------- |
| Sektöre özgü sözlük ve dilbilgisinde tıp terminolojisi veya It jarggibi tanıma doğruluğunu geliştirir. | İlgili metin (cümleler/utterer) |
| Ürün adları veya kısaltmalar gibi standart olmayan telaffuz içeren bir sözcüğün veya bir terimin fonetik ve görüntülenen formunu tanımlayın. | İlgili metin (telaffuz) |
| Konuşma stillerinde, aksanların veya belirli arka plan noseslerdeki tanıma doğruluğunu geliştirir. | Ses + insan etiketli yazılı betikler |

> [!IMPORTANT]
> Bir veri kümesini karşıya yüklemediyseniz lütfen [verilerinizi hazırlayın ve test](how-to-custom-speech-test-data.md)edin. Bu belge, verileri karşıya yüklemeye yönelik yönergeler ve yüksek kaliteli veri kümeleri oluşturma yönergeleri sağlar.

## <a name="train-and-evaluate-a-model"></a>Bir modeli eğitme ve değerlendirme

Bir modeli eğiten ilk adım eğitim verilerini karşıya yüklemedir. İnsan etiketli onayları ve ilgili metinleri (dıklılık ve söylenişleri) hazırlamak için [verilerinizi hazırlama ve test](how-to-custom-speech-test-data.md) etme adımları için adım adım yönergeler kullanın. Eğitim verilerini karşıya yükledikten sonra modelinize eğitime başlamak için aşağıdaki yönergeleri izleyin:

1. [Özel konuşma tanıma portalında](https://speech.microsoft.com/customspeech)oturum açın.
2. **Konuşmayı metne > Özel Konuşma Tanıma > eğitimi**'ne gidin.
3. **Modeli eğitme**' ye tıklayın.
4. Ardından, eğitime bir **ad** ve **Açıklama**sağlayın.
5. **Senaryo ve taban çizgisi modeli** açılan menüsünde, etki alanına en uygun senaryoyu seçin. Hangi senaryonun seçeceğinizden emin değilseniz, **genel**' i seçin. Temel model, eğitimin başlangıç noktasıdır. Tercih ediyorsanız, en son ' u kullanabilirsiniz.
6. **Eğitim verilerini seçin** sayfasında, eğitim için kullanmak istediğiniz bir veya daha fazla ses + insan etiketli döküm veri kümesi seçin.
7. Eğitim tamamlandıktan sonra, yeni eğitilen modelde doğruluk testi gerçekleştirmeyi tercih edebilirsiniz. Bu adım isteğe bağlıdır.
8. Özel modelinizi derlemek için **Oluştur** ' u seçin.

Eğitim tablosu, bu yeni oluşturulan modele karşılık gelen yeni bir giriş görüntüler. Tabloda Ayrıca durum görüntülenir: Işlem, başarılı, başarısız.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Eğitilen bir modelin doğruluğunu değerlendirin

Bu belgeleri kullanarak verileri inceleyebilir ve model doğruluğunu değerlendirebilirsiniz:

- [Verilerinizi inceleyin](how-to-custom-speech-inspect-data.md)
- [Verilerinizi değerlendirin](how-to-custom-speech-evaluate-data.md)

Doğruluk sınamasını yapmayı seçerseniz, modelin performansını gerçekçi bir şekilde anlamak için modelinizden farklı bir akustik veri kümesi seçmeniz önemlidir.

## <a name="next-steps"></a>Sonraki adımlar

- [Modelinizi dağıtın](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ek kaynaklar

- [Verilerinizi hazırlayın ve test edin](how-to-custom-speech-test-data.md)
- [Verilerinizi inceleyin](how-to-custom-speech-inspect-data.md)
- [Verilerinizi değerlendirin](how-to-custom-speech-evaluate-data.md)
- [Modelinize eğitme](how-to-custom-speech-train-model.md)
