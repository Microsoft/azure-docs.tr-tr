---
title: Özel Konuşma Tanıma doğruluğu değerlendirin ve geliştirir-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Bu belgede, konuşmadan metin modelimizin veya özel modelinizin kalitesini nasıl ölçeceğinizi ve artıracağınızı öğreneceksiniz. Ses + insan etiketli Döküm verileri, doğruluk sınaması için gereklidir ve 5 saatlik temsili ses sağlanmalıdır.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: b7e4ea586098ea3eb0dfd684650f798d7988e18b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100634592"
---
# <a name="evaluate-and-improve-custom-speech-accuracy"></a>Özel Konuşma Tanıma’nın doğruluğunu değerlendime ve iyileştirme

Bu makalede, Microsoft 'un konuşma-metin modellerinin veya kendi özel modellerinizin doğruluğunu nasıl ölçeceğinizi ve artıracağınızı öğreneceksiniz. Ses + insan etiketli Döküm verileri, doğruluk sınaması için gereklidir ve 5 saatlik temsili ses sağlanmalıdır.

## <a name="evaluate-custom-speech-accuracy"></a>Özel Konuşma doğruluğunu değerlendirme

Model doğruluğunu ölçmek için sektör standardı, [sözcük hata oranı](https://en.wikipedia.org/wiki/Word_error_rate) (WER). WER, tanıma sırasında tanımlanan yanlış sözcüklerin sayısını sayar ve sonra, insan tarafından etiketlenmiş döküm betiğine (aşağıda N olarak gösterilmiştir) verilen toplam sözcük sayısına böler. Son olarak, bu sayı WER 'i hesaplamak için %100 ile çarpılır.

![WER formülü](./media/custom-speech/custom-speech-wer-formula.png)

Yanlış tanımlanmış kelimeler üç kategoride yer almalıdır:

* Ekleme (ı): varsayıma betiğine yanlış eklenen kelimeler
* Silme (D): varsayım dökümü 'nde algılanmayan sözcükler
* Değiştirme (ler): başvuru ve varsayım arasında değiştirilen kelimeler

Aşağıda bir örnek verilmiştir:

![Yanlış tanımlanmış sözcüklerin örneği](./media/custom-speech/custom-speech-dis-words.png)

WER ölçümlerini yerel olarak çoğaltmak isterseniz [Sctk](https://github.com/usnistgov/SCTK)'den sclite kullanabilirsiniz.

## <a name="resolve-errors-and-improve-wer"></a>Hataları çözün ve WER 'i geliştirebilirsiniz

Uygulamanızı, aracınız veya ürününüzle kullandığınız modelin kalitesini değerlendirmek için makine tanıma sonuçlarından WER ' i kullanabilirsiniz. %5 oranında bir WER, iyi bir kalite gibi kabul edilir ve kullanıma hazır hale gelir. %20 oranında bir WER kabul edilebilir, ancak ek eğitime göz önüne almanız gerekebilir. %30 veya daha fazla bir WER, kalite ve eğitim gerektirir.

Hataların dağıtılması önemlidir. Birçok silme hatası ile karşılaşıldığında, genellikle zayıf ses sinyali gücü nedeniyle oluşur. Bu sorunu çözmek için, kaynağa daha yakın ses verileri toplamanız gerekir. Ekleme hataları, sesin gürültülü bir ortama kaydedildiği ve Crosstalk mevcut olabileceği ve tanınma sorunlarına neden olduğu anlamına gelir. Değiştirme hatalarıyla genellikle, etki alanına özgü koşulların yetersiz bir örneği, insan etiketli döküm veya ilgili metin olarak sağlandıysa, bu durum oluşur.

Tek tek dosyaları çözümleyerek, hangi tür hataların var olduğunu ve belirli bir dosyaya hangi hataların benzersiz olduğunu belirleyebilirsiniz. Dosya düzeyindeki sorunları anlamak, iyileştirmeleri hedefetmenize yardımcı olur.

## <a name="create-a-test"></a>Test oluşturma

Microsoft 'un konuşmaya metin taban çizgisi modeli veya eğitilen özel bir model kalitesini test etmek isterseniz doğruluğu değerlendirmek için iki modeli yan yana karşılaştırabilirsiniz. Karşılaştırma, WER ve tanıma sonuçlarını içerir. Genellikle, özel bir model Microsoft 'un temel modeliyle karşılaştırılır.

Modelleri yan yana değerlendirmek için:

1. [Özel konuşma tanıma portalında](https://speech.microsoft.com/customspeech)oturum açın.
2. **Test > > [proje adı] Özel Konuşma Tanıma > konuşmadan metne** gidin.
3. **Test Ekle**' ye tıklayın.
4. **Doğruluğu değerlendir**' i seçin. Teste bir ad, açıklama verin ve ses + insan etiketli döküm veri kümenizi seçin.
5. Test etmek istediğiniz en fazla iki model seçin.
6. **Oluştur**’a tıklayın.

Testiniz başarıyla oluşturulduktan sonra sonuçları yan yana karşılaştırabilirsiniz.

### <a name="side-by-side-comparison"></a>Yan yana karşılaştırma

Test tamamlandıktan sonra durum değişikliği *başarılı* olarak belirtilmişse, testinizde her iki model için de bir wer numarası bulacaksınız. Test ayrıntısı sayfasını görüntülemek için test adına tıklayın. Bu ayrıntı sayfasında, veri kümenizdeki tüm bildirimler listelenir. Bu, gönderilen veri kümesinden gelen döküm ile birlikte iki modelin tanınma sonuçlarını gösterir. Yan yana karşılaştırmayı incelemeye yardımcı olması için ekleme, silme ve değiştirme gibi çeşitli hata türlerini değiştirebilirsiniz. Her sütunda sesli dinleyerek ve iki konuşmadan metne modellerle ilgili sonuçları gösteren her bir sütundaki tanıma sonuçlarını karşılaştıran şekilde, hangi modelin ihtiyaçlarınıza uygun olduğunu ve ek eğitim ve iyileştirmeler gerektiğini belirleyebilirsiniz.

## <a name="improve-custom-speech-accuracy"></a>Özel Konuşma doğruluğunu iyileştirme

Konuşma tanıma senaryoları ses kalitesine ve dile (sözlük ve konuşma stili) göre farklılık gösterir. Aşağıdaki tabloda dört yaygın senaryo incelenir:

| Senaryo | Ses kalitesi | Sözlük | Konuşma stili |
|----------|---------------|------------|----------------|
| Çağrı merkezi | Düşük, 8 kHz, 1 ses kanalında 2 insana olabilir, sıkıştırılabilir | Dar, benzersiz etki alanı ve ürünler | Konuşma, gevşek yapılandırılmış |
| Ses Yardımcısı (Cortana veya bir sürücü aracılığıyla bir pencere) | Yüksek, 16 kHz | Varlık ağır (şarkı başlıkları, ürünler, konumlar) | Açıkça belirtilen sözcükler ve ifadeler |
| Dikte (anında ileti, notlar, arama) | Yüksek, 16 kHz | Değiştirilecek | Göz önünde bulundurularak |
| Video kapalı açıklamalı altyazı | Değişen mikrofon kullanımı, müzik eklendi gibi değişen | Toplantılar, yeniden dağıtılmış konuşma, müzik sözleri | Okuma, hazır veya gevşek yapılandırılmış |

Farklı senaryolar farklı kalite sonuçları üretir. Aşağıdaki tabloda, bu dört senaryodan alınan içeriğin, [sözcük hata oranı 'nda (WER)](how-to-custom-speech-evaluate-data.md)nasıl hızlarının yapılacağı incelenir. Tablo, her senaryo için en yaygın olarak kullanılan hata türlerini gösterir.

| Senaryo | Konuşma tanıma kalitesi | Ekleme hataları | Silme hataları | Değiştirme hataları |
|----------|----------------------------|------------------|-----------------|---------------------|
| Çağrı merkezi | Orta (<% 30 WER) | Düşük, diğer kişilerin arka planda konuştuğu durumlar dışında | Yüksek olabilir. Çağrı merkezleri gürültülü olabilir ve çakışan hoparlörler modeli karıştırabilirler | Orta. Ürünlerin ve kişilerin adları bu hatalara neden olabilir |
| Ses asistanı | Yüksek (<% 10 WER) | Düşük | Düşük | Orta, şarkı başlıkları, ürün adları veya konumlar nedeniyle |
| Etme | Yüksek (<% 10 WER) | Düşük | Düşük | Yüksek |
| Video kapalı açıklamalı altyazı | Video türüne bağlıdır (<% 50) | Düşük | Müzik, önemli, mikrofon kalitesi nedeniyle yüksek olabilir | Jargon bu hatalara neden olabilir |

WER bileşenlerinin belirlenmesi (ekleme, silme ve değiştirme hatalarının sayısı) modeli geliştirmek için hangi tür verilerin ekleneceğini belirlemeye yardımcı olur. Temel bir modelin kalitesini görüntülemek için [özel konuşma tanıma portalını](https://speech.microsoft.com/customspeech) kullanın. Portal, WER kalite ücretine bağlı ekleme, değiştirme ve silme hata oranlarını raporlar.

## <a name="improve-model-recognition"></a>Model tanımayı geliştirme

[Özel konuşma tanıma portalına](https://speech.microsoft.com/customspeech)eğitim verileri ekleyerek, tanınma hatalarını azaltabilirsiniz. 

Kaynak malzemeleri düzenli aralıklarla ekleyerek özel modelinizin korunmasını planlayın. Özel modelinize, varlıklarınızda yapılan değişiklikleri haberdar etmek için ek eğitim gerekir. Örneğin, ürün adlarına, şarkı adlarına veya yeni hizmet konumlarına yönelik güncelleştirmelere ihtiyacınız olabilir.

Aşağıdaki bölümlerde her bir ek eğitim verisinin hata düzeyini nasıl azaltabileceğini açıklamaktadır.

### <a name="add-related-text-sentences"></a>İlgili metin cümleleri ekleyin

Yeni bir özel modeli eğitedığınızda, etki alanına özgü sözcüklerin ve deyimlerin tanınmasını geliştirmek için ilgili metni ekleyerek ' ı başlatın. İlgili metin cümleleri öncelikle, ortak sözcüklerin ve etki alanına özgü sözcüklerin yanlış tanınmasına ilişkin değiştirme hatalarını bağlamda göstererek azaltabilirsiniz. Etki alanına özgü sözcükler seyrek olabilir veya bir sözcük oluşturabilir, ancak telaffuz tanınmak için basit olmalıdır.

> [!NOTE]
> Tanınmayan karakterler veya sözcükler gibi gürültü içeren ilgili metin cümlelerini önleyin.

### <a name="add-audio-with-human-labeled-transcripts"></a>İnsan etiketli yazılı betikler ile ses ekleme

İnsan etiketli dökümü olan ses, ses hedef kullanım durumundan geliyorsa en büyük doğruluk geliştirmelerini sunar. Örnekler, tam konuşma kapsamını kapsamalıdır. Örneğin, bir perakende mağaza için çağrı merkezi, yaz ayları sırasında swimwear ve güneşlük hakkındaki çağrıların çoğunu alır. Örneğin, algılamak istediğiniz tam konuşma kapsamını içerdiğinden emin olmanız gerekir.

Bu ayrıntıları göz önünde bulundurun:

* Ses ile eğitim sayesinde ses, insanların anlaşılması zor olursa en avantajlara sahip olur. Çoğu durumda, yalnızca ilgili metni kullanarak eğitime başlamanız gerekir.
* ABD Ingilizcesi gibi en çok kullanılan dillerden birini kullanırsanız, ses verileriyle eğitmenize gerek kalmaz. Bu tür diller için, temel modeller çoğu senaryoda zaten çok iyi bir tanıma sonuçları sunar; büyük olasılıkla ilgili metinle eğecek kadar fazla.
* Özel Konuşma Tanıma değiştirme hatalarını azaltmak, ekleme veya silme hatalarını azaltmak için yalnızca sözcük bağlamını yakalayabilir.
* Döküm hataları içeren örneklerden kaçının, ancak ses kalitesi çeşitliliğe sahiptir.
* Sorun etki alanınız ile ilgili olmayan cümlelerden kaçının. İlişkisiz cümleler modelinize zarar verebilir.
* Döküm kalitesi farklılık gösterir, ağırlığın artması için, hariç tutulan ve çok iyi cümleler (anahtar tümceleri içeren harika döküm gibi) çoğaltabilirsiniz.
* Konuşma hizmeti, ilgili metin olarak eklendikleri gibi, etki alanına özgü sözcüklerin ve tümceciklerin tanınmasını geliştirmek için otomatik olarak dökümünü kullanacaktır.
* Eğitim işleminin tamamlanması birkaç gün sürebilir. Eğitimin hızını artırmak için konuşma hizmeti aboneliğinizi eğitim için [adanmış donanıma sahip bir bölgede](custom-speech-overview.md#set-up-your-azure-account) oluşturmayı unutmayın.

> [!NOTE]
> Tüm temel modeller ses ile eğitimi desteklemez. Bir temel model bunu desteklemiyorsa, konuşma hizmeti bu metni yalnızca dökümleri 'ten kullanır ve sesi yoksayar. Ses verileriyle eğitimi destekleyen temel modellerin listesi için bkz. [dil desteği](language-support.md#speech-to-text) . Bir temel model, ses verileriyle eğitimleri desteklese de, hizmet sesin yalnızca bir bölümünü kullanabilir. Yine de tüm döküm dosyalarını kullanacaktır.

> [!NOTE]
> Eğitim için kullanılan temel modeli değiştirirken ve eğitim veri kümesinde seslerinizi değiştirdiğinizde, yeni seçilen temel modelin [ses verileriyle eğitimi destekleyip desteklemediğini](language-support.md#speech-to-text) *her zaman* denetleyin. Daha önce kullanılan temel model, ses verileriyle eğitimi desteklemeiyorsa ve eğitim veri kümesi ses içeriyorsa, yeni temel modele sahip eğitim süresi büyük **ölçüde** artar ve birkaç saat ile birkaç güne ve daha fazlasına kolayca gidebilirler. Konuşma hizmeti aboneliğiniz eğitim için [adanmış donanıma sahip](custom-speech-overview.md#set-up-your-azure-account) bir bölgede **değilse** bu özellikle doğrudur.
>
> Yukarıdaki paragrafta açıklanan sorunu ortaya çıkardıysanız, veri kümesindeki ses miktarını azaltarak veya tamamen yalnızca metni bırakarak eğitim süresini hızla azaltabilirsiniz. Konuşma hizmeti aboneliğiniz eğitim için [adanmış donanıma sahip bir bölgede](custom-speech-overview.md#set-up-your-azure-account) **değilse** , ikinci seçenek kesinlikle önerilir.

### <a name="add-new-words-with-pronunciation"></a>Telaffuz ile yeni sözcükler ekleme

Oluşturulan veya yüksek ölçüde özelleştirilmiş kelimelerin benzersiz söylenişleri olabilir. Bu sözcükler, sözcüğün pronounce daha küçük bir sözcüğe bölüneceği durumlarda tanınabilmesi için tanınabilir. Örneğin, **Xbox**, Pronounce as **X kutusunu** tanımak için. Bu yaklaşım genel doğruluğu artırmaz, ancak bu anahtar sözcüklerin tanınmasını artırabilir.

> [!NOTE]
> Bu teknik, şu anda yalnızca bazı diller için kullanılabilir. Ayrıntılar için [konuşmayı metne](language-support.md) dönüştürme bölümüne bakın.

## <a name="sources-by-scenario"></a>Senaryoya göre kaynaklar

Aşağıdaki tabloda, ses tanıma senaryoları gösterilmektedir ve yukarıda listelenen üç eğitim içeriği kategorisi dahilinde göz önünde bulundurmanız gereken kaynak malzemeler listelenmiştir.

| Senaryo | İlgili metin cümleleri | Ses + insan etiketli yazılı betikler | Söylenişi olan yeni sözcükler |
|----------|------------------------|------------------------------|------------------------------|
| Çağrı merkezi             | pazarlama belgeleri, Web sitesi, Çağrı Merkezi etkinliğiyle ilgili ürün incelemeleri | Arama Merkezi, insanların yaptığı çağrıları | belirsiz söylenişleri olan terimler (yukarıdaki Xbox 'a bakın) |
| Ses asistanı         | komutların ve varlıkların tüm birleşimlerini kullanarak cümleleri listeleyin | Seslendirmeyi bir cihaza kaydetme ve metin yerleştirme | benzersiz söylenişleri olan adlar (Filmler, şarkılar, ürünler) |
| Etme               | anlık iletiler veya e-postalar gibi yazılı giriş | yukarıdakine benzer | yukarıdakine benzer |
| Video kapalı açıklamalı altyazı | TV görüntüleme betikleri, Filmler, pazarlama içeriği, video özetleri | videoların tam yazılı betikleri | yukarıdakine benzer |

## <a name="next-steps"></a>Sonraki adımlar

* [Modeli eğitme ve dağıtma](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Ek kaynaklar

* [Verilerinizi hazırlayın ve test edin](./how-to-custom-speech-test-and-train.md)
* [Verilerinizi inceleyin](how-to-custom-speech-inspect-data.md)