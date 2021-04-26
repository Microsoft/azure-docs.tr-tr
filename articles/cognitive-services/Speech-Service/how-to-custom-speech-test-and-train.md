---
title: Özel Konuşma Tanıma konuşma hizmeti için veri hazırlama
titleSuffix: Azure Cognitive Services
description: Microsoft konuşma tanımanın doğruluğunu test ederken veya özel modellerinizi eğitmek için ses ve metin verileri gerekir. Bu sayfada, veri türlerini ve bunların nasıl kullanılacağını ve bunların nasıl yönetileceğini ele aldık.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: 2c98546d20e9f977a605ccbac21010aa9b1dbadc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232503"
---
# <a name="prepare-data-for-custom-speech"></a>Özel Konuşma için verileri hazırlama

Microsoft konuşma tanımanın doğruluğunu test ederken veya özel modellerinizi eğitmek için ses ve metin verileri gerekir. Bu sayfada, özel bir konuşma modeli gerektiren veri türlerini ele aldık.

## <a name="data-diversity"></a>Veri çeşitliliği

Özel bir modeli test etmek ve eğitmek için kullanılan metin ve ses, modelinizin tanıması gereken farklı bir hoparlör ve senaryo kümesinden örnek içermelidir.
Özel model testi ve eğitimi için veri toplanırken bu faktörleri göz önünde bulundurun:

* Metin ve konuşma ses verilerinin, kullanıcılarınızın modelinizle etkileşim kurarken yapacağız izin türlerini kapsaması gerekir. Örneğin, insanların bu tür değişiklikleri talep etmek için yapması gerekebilecek deyimler için sıcaklığın bir eğitimi oluşturan ve alçaltır.
* Verilerinizin, modelinizin tanıması gereken tüm konuşma farklarını içermesi gerekir. Birçok etken, vurgulu, dialet, dil karıştırma, yaş, cinsiyet, ses kullanımı, stres düzeyi ve günün saati dahil olmak üzere konuşmayı farklılık gösterebilir.
* Modelinizin kullanılacağı farklı ortamların (ınkapısı, dış mek, yol gürültüsü) örneklerini dahil etmeniz gerekir.
* Ses, üretim sisteminin kullanacağı donanım cihazları kullanılarak toplanmalıdır. Modeliniz, farklı kalitedeki cihazları kaydetmek için kaydedilen konuşmayı tanımlamak istiyorsanız, modelinize eğitebilmeniz için sağladığınız ses verileri de bu farklı senaryoları temsil etmelidir.
* Daha sonra modelinize daha fazla veri ekleyebilirsiniz, ancak veri kümesini proje gereksinimlerinize göre farklı ve temsilciyle aynı tutmaya özen gösterin.
* Özel model tanıma gereksinimleriniz içinde *olmayan* verilerin dahil edilmesi, tanınma kalitesini genelleyebilir, bu nedenle modelinizin vermesi gerekmeyen verileri eklemeyin.

Senaryoların bir alt kümesi üzerinde eğitilen bir model yalnızca bu senaryolarda iyi bir şekilde gerçekleştirilebilir. Özel modelinize yönelik olarak tanımak için gereken tüm senaryolar kapsamını belirten verileri dikkatle seçin.

> [!TIP]
> Modelinize ve Acoustics ile eşleşen küçük örnek veri kümeleri ile başlayın.
> Örneğin, aynı donanımda ve modelinizin üretim senaryolarında bulabileceği aynı akustik ortamda daha küçük ancak temsili bir ses örneği kaydedin.
> Eğitim için çok daha büyük veri kümeleri toplamayı yatırmadan önce, temsilci verilerinin küçük veri kümelerinde sorunlar ortaya çıkabilir.
>
> Hızlı bir şekilde başlamak için örnek verileri kullanmayı göz önünde bulundurun. <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">Örnek özel konuşma tanıma verileri</a> Için bu GitHub deposuna bakın

## <a name="data-types"></a>Veri türleri

Bu tabloda, kabul edilen veri türleri, her veri türü ne zaman kullanılmalı ve önerilen miktar listelenir. Her veri türü bir model oluşturmak için gerekmez. Veri gereksinimleri, bir modeli test etme veya eğitim oluşturmaya bağlı olarak değişir.

| Veri türü | Test için kullanılan | Önerilen miktar | Eğitim için kullanılır | Önerilen miktar |
|-----------|-----------------|----------|-------------------|----------|
| [Ses](#audio-data-for-testing) | Yes<br>Görsel inceleme için kullanılır | 5 + ses dosyası | No | Yok |
| [Ses + ınsan etiketli yazılı betikler](#audio--human-labeled-transcript-data-for-testingtraining) | Yes<br>Doğruluğu değerlendirmek için kullanılır | 0,5-5 saat ses | Yes | 1-20 saat ses |
| [İlgili metin](#related-text-data-for-training) | No | Yok | Yes | 1-200 MB ilgili metin |

Dosyalar bir veri kümesine türlerine göre gruplanmalı ve bir. zip dosyası olarak karşıya yüklenir. Her veri kümesi yalnızca tek bir veri türü içerebilir.

> [!TIP]
> Yeni bir modeli eğitedığınızda [ilgili metinle](#related-text-data-for-training)başlayın. Bu veriler, özel hüküm ve tümceciklerin tanınmasını zaten iyileştirir. Metinli eğitim, ses (dakika veya gün) ile eğitimlerden çok daha hızlıdır.

> [!NOTE]
> Tüm temel modeller ses ile eğitimi desteklemez. Bir temel model bunu desteklemiyorsa, konuşma hizmeti bu metni yalnızca dökümleri 'ten kullanır ve sesi yoksayar. Ses verileriyle eğitimi destekleyen temel modellerin listesi için bkz. [dil desteği](language-support.md#speech-to-text) . Bir temel model, ses verileriyle eğitimleri desteklese de, hizmet sesin yalnızca bir bölümünü kullanabilir. Yine de tüm döküm dosyalarını kullanacaktır.
>
> Eğitim için kullanılan temel modeli değiştirirken ve eğitim veri kümesinde seslerinizi değiştirdiğinizde, yeni seçilen temel modelin [ses verileriyle eğitimi destekleyip desteklemediğini](language-support.md#speech-to-text) *her zaman* denetleyin. Daha önce kullanılan temel model, ses verileriyle eğitimi desteklemeiyorsa ve eğitim veri kümesi ses içeriyorsa, yeni temel modele sahip eğitim süresi büyük **ölçüde** artar ve birkaç saat ile birkaç güne ve daha fazlasına kolayca gidebilirler. Konuşma hizmeti aboneliğiniz eğitim için [adanmış donanıma sahip](custom-speech-overview.md#set-up-your-azure-account) bir bölgede **değilse** bu özellikle doğrudur.
>
> Yukarıdaki paragrafta açıklanan sorunu ortaya çıkardıysanız, veri kümesindeki ses miktarını azaltarak veya tamamen yalnızca metni bırakarak eğitim süresini hızla azaltabilirsiniz. Konuşma hizmeti aboneliğiniz eğitim için [adanmış donanıma sahip bir bölgede](custom-speech-overview.md#set-up-your-azure-account) **değilse** , ikinci seçenek kesinlikle önerilir.
>
> Eğitim için adanmış donanım olan bölgelerde, konuşma hizmeti eğitim için en fazla 20 saatlik ses kullanacaktır. Diğer bölgelerde, en fazla 8 saat ses kullanacaktır.

## <a name="upload-data"></a>Verileri karşıya yükleme

Verilerinizi karşıya yüklemek için <a href="https://speech.microsoft.com/customspeech" target="_blank">konuşma Studio </a>'ya gidin. Portalda **verileri karşıya yükle** ' ye tıklayarak Sihirbazı başlatın ve ilk veri kümenizi oluşturun. Verilerinizi karşıya yüklemeye izin vermeden önce veri kümeniz için bir konuşma veri türü seçmeniz istenir.

![Konuşma portalından ses karşıya yükleme seçeneğini vurgulayan ekran görüntüsü.](./media/custom-speech/custom-speech-select-audio.png)

Karşıya yüklediğiniz her veri kümesinin seçtiğiniz veri türü için gereksinimleri karşılaması gerekir. Verilerinizin karşıya yüklenebilmesi için doğru şekilde biçimlendirilmesi gerekir. Doğru biçimli veriler, Özel Konuşma Tanıma hizmeti tarafından doğru şekilde işlenmek üzere olur. Gereksinimler aşağıdaki bölümlerde listelenmiştir.

Veri kümeniz karşıya yüklendikten sonra, birkaç seçeneğiniz vardır:

* **Test** sekmesine gidebilir ve yalnızca ses veya sesli + insan etiketli döküm verilerini görsel olarak inceleyebilirsiniz.
* Özel bir modeli eğitebilmeniz için **eğitim** sekmesine gidebilir ve ses + insan dökümü verilerini veya ilgili metin verilerini kullanabilirsiniz.

## <a name="audio-data-for-testing"></a>Test için ses verileri

Ses verileri, Microsoft 'un temel konuşma konuşmadan metin modelinin veya özel bir modelin doğruluğunu test etmek için idealdir. Ses verilerinin, belirli bir modelin performansına göre konuşma doğruluğunu denetlemek için kullanılması gerektiğini aklınızda bulundurun. Bir modelin doğruluğunu daha fazla bulmak istiyorsanız, [Ses + insan etiketli Döküm verileri](#audio--human-labeled-transcript-data-for-testingtraining)' ni kullanın.

Ses dosyalarınızın Özel Konuşma Tanıma ile kullanım için doğru biçimlendirildiğinden emin olmak için bu tabloyu kullanın:

| Özellik                 | Değer                 |
|--------------------------|-----------------------|
| Dosya biçimi              | RIFF (WAV)            |
| Örnekleme hızı              | 8.000 Hz veya 16.000 Hz |
| Kanallar                 | 1 (mono)              |
| Ses başına maksimum uzunluk | 2 saat               |
| Örnek biçimi            | PCM, 16 bit           |
| Arşiv biçimi           | .zip                  |
| Maksimum Arşiv boyutu     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Eğitim ve test verileri yüklenirken. zip dosyası boyutu 2 GB 'ı aşamaz. Eğitim için daha fazla veri gerekiyorsa, bunu birkaç. zip dosyasına bölün ve ayrı olarak karşıya yükleyin. Daha sonra, *birden çok* veri kümesinden eğiteyi seçebilirsiniz. Ancak yalnızca *tek* bir veri kümesinden test edebilirsiniz.

Ses özelliklerini doğrulamak veya var olan sesleri uygun biçimlere dönüştürmek için <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">Sox </a> kullanın. Aşağıda, bu etkinliklerin her birinin SoX komut satırı aracılığıyla nasıl yapılabileceği hakkında bazı örnekler verilmiştir:

| Etkinlik | Description | SoX komutu |
|----------|-------------|-------------|
| Ses biçimini denetle | Denetlemek için bu komutu kullanın<br>ses dosyası biçimi. | `sox --i <filename>` |
| Ses biçimini Dönüştür | Dönüştürmek için bu komutu kullanın<br>ses dosyasını tek kanala, 16 bit, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Ses + insanlar etiketli, test/eğitim için yazılı Döküm verileri

Ses dosyalarınızı işlerken Microsoft 'un konuşmadan metne doğruluğu için doğruluğu ölçmek amacıyla, karşılaştırma için insan etiketli dökümlere (Word-Word) sağlamanız gerekir. İnsanlar etiketli döküm genellikle zaman alıcı olsa da doğruluğu değerlendirmek ve modeli kullanım durumlarınız için eğitmek gerekir. Göz önünde bulundurmanız durumunda, tanınma iyileştirmeleri yalnızca belirtilen veriler kadar iyi olacaktır. Bu nedenle, yalnızca yüksek kaliteli döküm dosyalarının karşıya yüklenmesi önemlidir.

Ses dosyaları, kaydın başlangıcında ve sonunda sessizlik alabilir. Mümkünse, her bir örnek dosyada konuşmayı önce ve sonra, en az bir yarı saniyelik sessizlik ekleyin. Düşük kayıt hacmi veya kesintiye uğratan arka plan gürültüsünü olan ses yararlı olmasa da özel modelinize zarar vermez. Ses örnekleri toplanmadan önce mikrofonlarınızı ve sinyal işleme donanımınızı yükseltmeyi her zaman düşünün.

| Özellik                 | Değer                               |
|--------------------------|-------------------------------------|
| Dosya biçimi              | RIFF (WAV)                          |
| Örnekleme hızı              | 8.000 Hz veya 16.000 Hz               |
| Kanallar                 | 1 (mono)                            |
| Ses başına maksimum uzunluk | 2 saat (test)/60 s (eğitim) |
| Örnek biçimi            | PCM, 16 bit                         |
| Arşiv biçimi           | .zip                                |
| En büyük ZIP boyutu         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Eğitim ve test verileri yüklenirken. zip dosyası boyutu 2 GB 'ı aşamaz. Yalnızca *tek* bir veri kümesinden test edebilirsiniz, bunu uygun dosya boyutunda tutmanız yeterlidir. Ayrıca, her eğitim dosyası 60 saniye aşılamaz, aksi takdirde hata dışarı kalır.

Sözcük silme veya değiştirme gibi sorunları gidermek için, tanımayı geliştirmek için önemli miktarda veri gerekir. Genellikle, 1 ila 20 saatlik ses için Word sözcük dökümü sağlamanız önerilir. Ancak, 30 dakikalık bir süre içinde, tanınma sonuçlarının iyileştirilmesine yardımcı olabilir. Tüm WAV dosyalarının transkripsiyonları tek bir düz metin dosyasına yerleştirilmelidir. Transkripsiyon dosyasının her satırında ses dosyalarından birinin adı ve transkripsiyon bulunmalıdır. Dosya adı ve transkripsiyon sekme (\t) ile ayrılmalıdır.

Örnek:

<!-- The following example contains tabs. Don't accidentally convert these into spaces. -->

```input
speech01.wav    speech recognition is awesome
speech02.wav    the quick brown fox jumped all over the place
speech03.wav    the lazy dog was not amused
```

> [!IMPORTANT]
> Transkripsiyon UTF-8 bayt sırası işareti (BOM) ile kodlanmış olmalıdır.

Transkripsiyon metinleri sistem tarafından işlenebilmesi için normalleştirilir. Ancak, veriler konuşma Studio 'ya yüklenmeden önce gerçekleştirilmesi gereken bazı önemli normalleştirmeler vardır. Dökümü hazırlarken kullanılacak uygun dil için, bkz. [nasıl yapılır: insan etiketli döküm oluşturma](how-to-custom-speech-human-labeled-transcriptions.md)

Ses dosyalarınızı ve bunlara karşılık gelen düzenlemeleri topladıktan sonra, <a href="https://speech.microsoft.com/customspeech" target="_blank">konuşma Studio </a>'ya yüklemeden önce bunları tek bir. zip dosyası olarak paketleyin. Aşağıda üç ses dosyası ve bir insan etiketli döküm dosyası içeren örnek bir veri kümesi verilmiştir:

> [!div class="mx-imgBorder"]
> ![Konuşma portalından ses seçme](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

Konuşma hizmeti abonelikleriniz için önerilen bölgelerin listesi için bkz. [Azure hesabınızı ayarlama](custom-speech-overview.md#set-up-your-azure-account) . Bu bölgelerden birinde konuşma aboneliklerinin kurulması, modeli eğitmek için gereken süreyi azaltacaktır. Bu bölgelerde, eğitim, günde yaklaşık 10 saatlik ses ile diğer bölgelerde günde yalnızca 1 saat arasında işlem yapabilir. Model eğitimi bir hafta içinde tamamlanamaz, model başarısız olarak işaretlenir.

Tüm temel modeller ses verileriyle eğitimi desteklemez. Temel model bunu desteklemiyorsa, hizmet sesi yoksayacak ve yalnızca döküm metniyle eğitecektir. Bu durumda, eğitim, ilgili metin ile eğitimle aynı olacaktır. Ses verileriyle eğitimi destekleyen temel modellerin listesi için bkz. [dil desteği](language-support.md#speech-to-text) .

## <a name="related-text-data-for-training"></a>Eğitim için ilgili metin verileri

Ürün adları veya benzersiz özellikler, eğitim için ilgili metin verilerini içermelidir. İlgili metin, doğru tanımanın sağlanmasına yardımcı olur. Tanımayı geliştirmek için iki tür ilgili metin verisi sağlanabilmesi:

| Veri türü | Bu verilerin tanınması nasıl geliştirilir |
|-----------|------------------------------------|
| Cümleler (utterlere) | Ürün adlarını veya bir cümle bağlamı içindeki sektöre özel sözlüğü yapılandırırken doğruluğu geliştirir. |
| Söylenişler | Tanımsız söylenlerdeki yaygın olmayan terimlerin, kısaltmalardan veya diğer sözcüklerin okunuşunu geliştirir. |

Cümleler, tek bir metin dosyası veya birden çok metin dosyası olarak bulunabilir. Doğruluğu artırmak için, beklenen söyleye daha yakın olan metin verilerini kullanın. Söylenişler tek bir metin dosyası olarak sağlanmalıdır. Her şey tek bir zip dosyası olarak paketlenebilir ve <a href="https://speech.microsoft.com/customspeech" target="_blank">konuşma Studio </a>'ya yüklenebilir.

İlgili metinle eğitim genellikle birkaç dakika içinde tamamlanır.

### <a name="guidelines-to-create-a-sentences-file"></a>Cümleler dosyası oluşturma yönergeleri

Cümleler kullanarak özel bir model oluşturmak için, örnek dıklarla bir liste sağlamanız gerekir. Söyleyler 'in tam veya dilbilgisi doğru _olması gerekmez,_ ancak üretimde bekleeceğiniz konuşulan girişi doğru şekilde yansıtmaları gerekir. Belirli koşulların daha fazla ağırlığa sahip olmasını istiyorsanız, bu belirli koşulları içeren çeşitli cümleler ekleyin.

Genel rehberlik olarak, eğitim metni üretimde beklenen gerçek metin için mümkün olduğunca yakın olduğunda model uyarlama en etkili olur. Geliştirmeye hedeflediğiniz, etki alanına özgü öğretmek ve tümceleri eğitim metnine dahil edilmelidir. Mümkün olduğunda, bir cümleden veya anahtar kelimesinin ayrı bir satıra denetlenmesini deneyin. Sizin için önemli anahtar sözcükler ve tümcecikler (örneğin, ürün adları) için, bunları birkaç kez kopyalayabilirsiniz. Ancak, çok fazla kopyalamayın, bu da genel tanıma oranını etkileyebilir.

Dikkat edilecek ilgili veri dosyanızın doğru biçimlendirildiğinden emin olmak için bu tabloyu kullanın:

| Özellik | Değer |
|----------|-------|
| Metin kodlaması | UTF-8 BOM |
| Satır başına konuşma sayısı | 1 |
| En büyük dosya boyutu | 200 MB |

Ayrıca, aşağıdaki kısıtlamaları hesaba eklemek isteyeceksiniz:

* Karakterleri, kelimeleri veya sözcük gruplarını üç defadan fazla tekrardan kaçının. Örneğin: "aaaa", "Evet Evet Evet", ya da "kendisi bu kadar olan bu. Konuşma hizmeti çok fazla tekrara sahip olan satırları bırakabilir.
* Yukarıdaki özel karakterleri veya UTF-8 karakterlerini kullanmayın `U+00A1` .
* URI 'Ler reddedilir.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Telaffuz dosyası oluşturma yönergeleri

Kullanıcılarınızın karşılaşabilmesi veya kullanması gereken standart telaffuz olmayan, sık olmayan koşullar varsa, tanımayı geliştirmek için özel bir telaffuz dosyası sağlayabilirsiniz.

> [!IMPORTANT]
> Ortak sözcüklerin okunuşunu değiştirmek için özel telaffuz dosyaları kullanılması önerilmez.

Bu, konuşulan bir utterance örnekleri ve her biri için özel bir telaffuz içerir:

| Tanınan/görüntülenmiş form | Konuşulan form |
|--------------|--------------------------|
| 3CPO | Üç c p o |
| CNTK | c n t k |
| IEEE | Ben Üçlü e |

Konuşulan biçim fonetik bir dizidir. Bu, harf, sözcük, hecelere veya üçünün birleşiminden oluşabilir.

Özelleştirilmiş telaffuz Ingilizce ( `en-US` ) ve Almanca () ile kullanılabilir `de-DE` . Bu tabloda dile göre desteklenen karakterler gösterilmektedir:

| Dil | Yerel Ayar | Karakterler |
|----------|--------|------------|
| İngilizce | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Almanca | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Söylenişleri için ilgili veri dosyanızın doğru biçimlendirildiğinden emin olmak için aşağıdaki tabloyu kullanın. Söyleniş dosyaları küçüktür ve yalnızca birkaç kilobayt boyutunda olmalıdır.

| Özellik | Değer |
|----------|-------|
| Metin kodlaması | UTF-8 BOM (Ingilizce için de ANSI desteklenir) |
| satır başına söylenişleri sayısı | 1 |
| En büyük dosya boyutu | 1 MB (ücretsiz katman için 1 KB) |

## <a name="next-steps"></a>Sonraki adımlar

* [Verilerinizi inceleyin](how-to-custom-speech-inspect-data.md)
* [Verilerinizi değerlendirin](how-to-custom-speech-evaluate-data.md)
* [Modelinizi eğitme](how-to-custom-speech-train-model.md)
* [Modelinizi dağıtın](./how-to-custom-speech-train-model.md)