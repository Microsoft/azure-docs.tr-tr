---
title: Özel Konuşma Tanıma konuşma hizmeti için test verilerini hazırlama
titleSuffix: Azure Cognitive Services
description: Microsoft konuşma tanımanın doğru olup olmadığını görmek için test etmeksizin veya kendi modellerinizi nasıl eğtireceğiz, verilerin (ses ve/veya metin biçiminde) olması gerekir. Bu sayfada, veri türlerini, nasıl kullanıldığını ve bunların nasıl yönetileceğini ele aldık.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 577a76b628e40b7651345698a46cba255b16a828
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464552"
---
# <a name="prepare-data-for-custom-speech"></a>Özel Konuşma Tanıma için veri hazırlama

Microsoft konuşma tanımanın doğru olup olmadığını görmek için test yapıp görmeksizin, ses ve metin biçiminde verilerin olması gerekir. Bu sayfada, veri türlerini, nasıl kullanıldığını ve bunların nasıl yönetileceğini ele aldık.

## <a name="data-types"></a>Veri türleri

Bu tabloda, kabul edilen veri türleri, her veri türü ne zaman kullanılmalı ve önerilen miktar listelenir. Her veri türü bir model oluşturmak için gerekmez. Veri gereksinimleri, bir modeli test etme veya eğitim oluşturmaya bağlı olarak değişir.

| Veri türü | Test için kullanılan | Miktar | Eğitim için kullanılır | Miktar |
|-----------|-----------------|----------|-------------------|----------|
| [Müzik](#audio-data-for-testing) | Evet<br>Görsel inceleme için kullanılır | 5 + ses dosyası | Hayır | Yok |
| [Ses + ınsan etiketli yazılı betikler](#audio--human-labeled-transcript-data-for-testingtraining) | Evet<br>Doğruluğu değerlendirmek için kullanılır | 0,5-5 saat ses | Evet | 1-1.000 saat ses |
| [İlgili metin](#related-text-data-for-training) | Hayır | Yok | Evet | 1-200 MB ilgili metin |

Dosyalar bir veri kümesine türlerine göre gruplanmalı ve zip dosyası olarak karşıya yüklenir. Her veri kümesi yalnızca tek bir veri türü içerebilir.

## <a name="upload-data"></a>Karşıya veri yükleme

Verilerinizi karşıya yüklemeye hazırsanız [özel konuşma tanıma portalına](https://speech.microsoft.com/customspeech)gidin, ardından Sihirbazı başlatmak ve ilk veri kümenizi oluşturmak Için **verileri karşıya yükle** ' ye tıklayın. Verilerinizi karşıya yüklemeye izin vermeden önce veri kümeniz için bir konuşma veri türü seçmeniz istenir.

![Konuşma portalından ses seçme](./media/custom-speech/custom-speech-select-audio.png)

Karşıya yüklediğiniz her veri kümesinin seçtiğiniz veri türü için gereksinimleri karşılaması gerekir. Karşıya yüklenmeden önce verilerinizi doğru şekilde biçimlendirmek önemlidir. Bu, verilerin Özel Konuşma Tanıma hizmeti tarafından doğru şekilde işlenmesini sağlar. Gereksinimler aşağıdaki bölümlerde listelenmiştir.

Veri kümeniz karşıya yüklendikten sonra, birkaç seçeneğiniz vardır:

* **Test** sekmesine gidebilir ve yalnızca ses veya sesli + insan etiketli döküm verilerini görsel olarak inceleyebilirsiniz.
* Özel bir modeli eğitebilmeniz için **eğitim** sekmesine gidebilir ve ses + insan dökümü verilerini veya ilgili metin verilerini kullanabilirsiniz.

## <a name="audio-data-for-testing"></a>Test için ses verileri

Ses verileri, Microsoft 'un temel konuşma konuşmadan metin modelinin veya özel bir modelin doğruluğunu test etmek için idealdir. Ses verilerinin, belirli bir modelin performansına göre konuşma doğruluğunu denetlemek için kullanılması gerektiğini aklınızda bulundurun. Bir modelin doğruluğunu daha fazla bulmak istiyorsanız, [Ses + insan etiketli Döküm verileri](#audio--human-labeled-transcript-data-for-testingtraining)' ni kullanın.

Ses dosyalarınızın Özel Konuşma Tanıma ile kullanım için doğru biçimlendirildiğinden emin olmak için bu tabloyu kullanın:

| Özellik | Değer |
|----------|-------|
| Dosya biçimi | RIFF (WAV) |
| Örnek hız | 8\.000 Hz veya 16.000 Hz |
| Kanallar | 1 (mono) |
| Ses başına maksimum uzunluk | 2 saat |
| Örnek biçim | PCM, 16 bit |
| Arşiv biçimi | .zip |
| Maksimum Arşiv boyutu | 2 GB |

Sesimiz bu özellikleri karşılamaz veya bunun olup olmadığını denetlemek istiyorsanız, sesi denetlemek veya dönüştürmek için [Sox](http://sox.sourceforge.net) 'i indirmeyi öneririz. Aşağıda, bu etkinliklerin her birinin komut satırı aracılığıyla nasıl yapılabileceği hakkında bazı örnekler verilmiştir:

| Etkinlik | Açıklama | Sox komutu |
|----------|-------------|-------------|
| Ses biçimini denetle | Ses dosyası biçimini denetlemek için bu komutu kullanın. | `sox --i <filename>` |
| Ses biçimini Dönüştür | Ses dosyasını tek kanala, 16 bit, 16 KHz 'a dönüştürmek için bu komutu kullanın. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Ses + insanlar etiketli, test/eğitim için yazılı Döküm verileri

Ses dosyalarınızı işlerken Microsoft 'un konuşmadan metne doğruluğu için doğruluğu ölçmek amacıyla, karşılaştırma için insan etiketli dökümlere (Word-Word) sağlamanız gerekir. İnsanlar etiketli döküm genellikle zaman alıcı olsa da doğruluğu değerlendirmek ve modeli kullanım durumlarınız için eğitmek gerekir. Göz önünde bulundurmanız durumunda, tanınma iyileştirmeleri yalnızca belirtilen veriler kadar iyi olacaktır. Bu nedenle, yalnızca yüksek kaliteli döküm dosyalarının karşıya yüklenmesi önemlidir.  

| Özellik | Değer |
|----------|-------|
| Dosya biçimi | RIFF (WAV) |
| Örnek hız | 8\.000 Hz veya 16.000 Hz |
| Kanallar | 1 (mono) |
| Ses başına maksimum uzunluk | 60 s |
| Örnek biçim | PCM, 16 bit |
| Arşiv biçimi | .zip |
| En büyük ZIP boyutu | 2 GB |

Sözcük silme veya değiştirme gibi sorunları gidermek için, tanımayı geliştirmek için önemli miktarda veri gerekir. Genellikle, kabaca 10 ila 1.000 saatlik ses için Word sözcük dökümü sağlamanız önerilir. Tüm WAV dosyalarının transkripsiyonları tek bir düz metin dosyasına yerleştirilmelidir. Transkripsiyon dosyasının her satırında ses dosyalarından birinin adı ve transkripsiyon bulunmalıdır. Dosya adı ve transkripsiyon sekme (\t) ile ayrılmalıdır.

  Örneğin:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Transkripsiyon UTF-8 bayt sırası işareti (BOM) ile kodlanmış olmalıdır.

Transkripsiyon metinleri sistem tarafından işlenebilmesi için normalleştirilir. Ancak, veriler konuşma Studio 'ya yüklenmeden _önce_ Kullanıcı tarafından gerçekleştirilmesi gereken bazı önemli normalleştirmeler vardır. Dökümü hazırlarken kullanılacak uygun dil için, bkz. [nasıl yapılır: insan etiketli döküm oluşturma](how-to-custom-speech-human-labeled-transcriptions.md)

Ses dosyalarınızı ve bunlara karşılık gelen onayları topladıktan sonra, [özel konuşma tanıma portalına](https://speech.microsoft.com/customspeech)yüklemeden önce tek bir. zip dosyası olarak paketlenmesi gerekir. Bu, üç ses dosyası ve bir insan etiketli döküm dosyası içeren örnek bir veri kümesidir:

![Konuşma portalından ses seçme](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Eğitim için ilgili metin verileri

Benzersiz olan ürün adları veya özelliklerinizin varsa ve doğru tanındıklarından emin olmak istiyorsanız, eğitim için ilgili metin verilerini eklemek önemlidir. Tanımayı geliştirmek için iki tür ilgili metin verisi sağlanabilmesi:

| Veri türü | Bu verilerin tanınması nasıl geliştirilir |
|-----------|------------------------------------|
| Utterslar ve/veya cümleler | Bunlar, ürün adlarını veya bir cümle bağlamı içindeki sektöre özel bir sözlüğü tanımayı artıran doğruluğu iyileştirebilir. |
| Söylenişler | Bunlar, tanımsız söylenişleri olan seyrek terim, kısaltmalar veya diğer sözcüklerin telaffuz hale getirebilirsiniz. |

Söyleyme, tek veya birden çok metin dosyası olarak sağlanıyor. Metin verileri ne kadar yaklaşacaktır, bu doğruluk artar. Söylenişler tek bir metin dosyası olarak sağlanmalıdır. Her şey tek bir zip dosyası olarak paketlenebilir ve [özel konuşma tanıma portalına](https://speech.microsoft.com/customspeech)yüklenebilir.

### <a name="guidelines-to-create-an-utterances-file"></a>Utterer dosyası oluşturma yönergeleri

İlgili metni kullanarak özel bir model oluşturmak için, örnek metinlerin bir listesini sağlamanız gerekir. Bu söyleyler, tam tümceler veya dilbilgisi doğru olması gerekmez, ancak üretimde beklediğinizi konuşulan girişi doğru bir şekilde yansıtmalıdır. Belirli koşulların daha fazla ağırlığa sahip olmasını istiyorsanız, ilgili veri dosyanıza bu belirli koşulları içeren birkaç cümle ekleyebilirsiniz.

Dikkat edilecek ilgili veri dosyanızın doğru biçimlendirildiğinden emin olmak için bu tabloyu kullanın:

| Özellik | Değer |
|----------|-------|
| Metin kodlaması | UTF-8 BOM |
| Satır başına konuşma sayısı | 1 |
| En büyük dosya boyutu | 200 MB |

Ayrıca, aşağıdaki kısıtlamaları hesaba eklemek isteyeceksiniz:

* Dört defadan fazla karakter tekrarlamadan kaçının. Örneğin: "aaaa" veya "uuuu".
* U + 00A1 üzerinde özel karakterler veya UTF-8 karakterleri kullanmayın.
* URI 'Ler reddedilir.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Telaffuz dosyası oluşturma yönergeleri

Kullanıcılarınızın karşılaşabilmesi veya kullanması gereken standart telaffuz olmayan, sık olmayan koşullar varsa, tanımayı geliştirmek için özel bir telaffuz dosyası sağlayabilirsiniz.

> [!IMPORTANT]
> Ortak sözcüklerin söylenişini değiştirmek için bu özelliğin kullanılması önerilmez.

Bu, konuşulan bir utterance örnekleri ve her biri için özel bir telaffuz içerir:

| Tanınan/görüntülenmiş form | Konuşulan form |
|--------------|--------------------------|
| 3CPO | Üç c p o |  
| CNTK | c n t k |
| IEEE | Ben Üçlü e |

Konuşulan biçim fonetik bir dizidir. Bu, harf, sözcük, hecelere veya üçünün birleşiminden oluşabilir.

Özelleştirilmiş telaffuz Ingilizce (en-US) ve Almanca (de-DE) olarak kullanılabilir. Bu tabloda dile göre desteklenen karakterler gösterilmektedir:

| Dil | Yerel Ayar | Karakterle |
|----------|--------|------------|
| Türkçe | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Almanca | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Söylenişleri için ilgili veri dosyanızın doğru biçimlendirildiğinden emin olmak için bu tabloyu kullanın. Telaffuz dosyaları küçüktür ve birkaç KBs 'yi aşmamalıdır.

| Özellik | Değer |
|----------|-------|
| Metin kodlaması | UTF-8 BOM (Ingilizce için de ANSI desteklenir) |
| satır başına söylenişleri sayısı | 1 |
| En büyük dosya boyutu | 1 MB (ücretsiz katman için 1 KB) |

## <a name="next-steps"></a>Sonraki adımlar

* [Verilerinizi inceleyin](how-to-custom-speech-inspect-data.md)
* [Verilerinizi değerlendirin](how-to-custom-speech-evaluate-data.md)
* [Modelinize eğitme](how-to-custom-speech-train-model.md)
* [Modelinizi dağıtın](how-to-custom-speech-deploy-model.md)
