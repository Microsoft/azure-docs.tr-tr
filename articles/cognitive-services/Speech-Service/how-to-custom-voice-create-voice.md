---
title: Özel bir sesli konuşma hizmeti oluşturma
titleSuffix: Azure Cognitive Services
description: Verilerinizi karşıya yüklemeye hazırsanız, özel sesli portala gidin. Özel bir ses projesi oluşturun veya seçin. Projenin, doğru dil/yerel ayar ve cinsiyet özelliklerini, ses eğitimine yönelik olarak kullanmayı istediğiniz verilerle paylaşmalıdır.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 370b5005f27fbfe6ee8fc96d6dd7e467a581ec67
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464601"
---
# <a name="create-a-custom-voice"></a>Özel bir ses oluşturun

[Özel ses için verileri hazırlama](how-to-custom-voice-prepare-data.md)bölümünde, özel bir sesi ve farklı biçim gereksinimlerini eğitebilmeniz için kullanabileceğiniz farklı veri türlerini açıklıyoruz. Verilerinizi hazırladıktan sonra, bunları [özel ses portalına](https://aka.ms/custom-voice-portal)veya özel ses eğitimi API 'sine yüklemeye başlayabilirsiniz. Burada, Portal üzerinden özel bir sesli eğitim adımlarını açıklanmaktadır.

> [!NOTE]
> Bu sayfada özel [sesle çalışmaya](how-to-custom-voice.md) başlayın ve özel [Ses için verileri hazırlayın](how-to-custom-voice-prepare-data.md)ve özel bir ses projesi oluşturmuş olursunuz.

Özel ses için desteklenen dilleri denetleyin: [Özelleştirme dili](language-support.md#customization).

## <a name="upload-your-datasets"></a>Veri kümelerinizi karşıya yükleyin

Verilerinizi karşıya yüklemeye hazırsanız, [özel sesli portala](https://aka.ms/custom-voice-portal)gidin. Özel bir ses projesi oluşturun veya seçin. Projenin, doğru dil/yerel ayar ve cinsiyet özelliklerini, ses eğitimine yönelik olarak kullanmayı istediğiniz verilerle paylaşmalıdır. Örneğin, bir UK vurgu ile Ingilizce olarak işiniz durumunda `en-GB` ' yi seçin.

**Veri** sekmesine gidin ve **verileri karşıya yükle**' ye tıklayın. Sihirbazda, hazırladığınız verilerle eşleşen doğru veri türünü seçin.

Karşıya yüklediğiniz her veri kümesinin seçtiğiniz veri türü için gereksinimleri karşılaması gerekir. Karşıya yüklenmeden önce verilerinizi doğru şekilde biçimlendirmek önemlidir. Bu, verilerin özel ses hizmeti tarafından doğru şekilde işlenmesini sağlar. [Özel ses için veri hazırlama](how-to-custom-voice-prepare-data.md) sayfasına gidin ve verilerinizin rightly biçimlendirildiğinden emin olun.

> [!NOTE]
> Ücretsiz abonelik (F0) kullanıcıları iki veri kümesini eşzamanlı olarak karşıya yükleyebilir. Standart abonelik (S0) kullanıcıları beş veri kümesini eşzamanlı olarak karşıya yükleyebilir. Sınıra ulaştıysanız, veri kümelerinden en az biri içeri aktarmayı bitirene kadar bekleyin. Sonra yeniden deneyin.

> [!NOTE]
> Abonelik başına içeri aktarılmaya izin verilen en fazla veri kümesi sayısı 10. ücretsiz abonelik (F0) kullanıcıları ve Standart abonelik (S0) kullanıcıları için 500. zip dosyalarıdır.

Veri kümeleri, karşıya yükle düğmesine ulaştıktan sonra otomatik olarak onaylanır. Veri doğrulama, dosya biçimlerini, boyutunu ve örnekleme oranını doğrulamak için ses dosyalarındaki denetim dizilerini içerir. Varsa hataları düzeltin ve yeniden gönder. Veri alma isteği başarıyla başlatıldığında, yeni karşıya yüklediğiniz veri kümesine karşılık gelen veri tablosunda bir giriş görmeniz gerekir.

Aşağıdaki tabloda, içeri aktarılan veri kümeleri için işleme durumları gösterilmektedir:

| Durum | Anlamı |
| ----- | ------- |
| İşleniyor | Veri kümeniz alındı ve işleniyor. |
| Başarılı oldu | Veri kümeniz doğrulandıktan sonra artık bir ses modeli oluşturmak için kullanılabilir. |
| Başarısız | Veri kümeniz, çok sayıda nedenden dolayı işleme sırasında başarısız oldu, örneğin dosya hataları, veri sorunları veya ağ sorunları. |

Doğrulama işlemi tamamlandıktan sonra, **utterslar** sütunundaki veri kümelerinizin her biri için eşleşen Masların toplam sayısını görebilirsiniz. Seçtiğiniz veri türü uzun ses segmentlemesini gerektiriyorsa, bu sütun yalnızca kendi betiklerinizi temel alarak veya konuşma dökümü hizmeti aracılığıyla sizin için segmentettiğimiz göz asyonları yansıtır. Başarılı bir şekilde içeri aktarılan ve bunların eşleme dökümlerinin ayrıntı sonuçlarını görüntülemek için doğrulanan veri kümesini daha fazla indirebilirsiniz. İpucu: uzun ses segmentlenmesi, veri işlemeyi tamamlayacak bir saatten uzun sürebilir.

En-US ve zh-CN veri kümeleri için, bir raporu daha fazla indirebilir ve kayıtlarınızın her biri için telaffuz puanlarını ve gürültü düzeyini kontrol edebilirsiniz. Söyleniş puanı 0 ile 100 arasındadır. 70 altındaki bir puan, normalde bir konuşma hatası veya betik uyuşmazlığını gösterir. Ağır bir vurgu, telaffuz puanınızı azaltabilir ve üretilen dijital sesi etkileyebilir.

Daha yüksek sinyalden gürültü oranı (SNR), Sesinizdeki küçük paraziti gösterir. Profesyonel Studios adresinde kayıt yaparak genellikle 50 + SNR 'ye ulaşabilirsiniz. 20 ' nin altında bir SNR ile ses, üretilen sesinize açık gürültü oluşmasına neden olabilir.

Düşük telaffuz puanlarla veya kötü bir sinyal gürültüsü oranlarına sahip tüm sinyalleri yeniden kaydetmeyi düşünün. Yeniden kaydedemeyebilirsiniz, bu ilgili söz konusu söz konusu söz konusu söz konusu söz konusu söz konusu söz konusu söz konusu

## <a name="build-your-custom-voice-model"></a>Özel ses modelinizi oluşturma

Veri kümeniz doğrulandıktan sonra, özel ses modelinizi oluşturmak için kullanabilirsiniz.

1.  **Metinden konuşmaya > özel sesli > eğitimi**' ne gidin.

2.  **Modeli eğitme**' ye tıklayın.

3.  Ardından, bu modeli tanımlamanızı sağlayacak bir **ad** ve **Açıklama** girin.

    Bir adı dikkatle seçin. Buraya girdiğiniz ad, SSML girişinin bir parçası olarak konuşma sensimi için isteğiniz için kullandığınız adı kullanacaktır. Yalnızca harfler, rakamlar ve-, \_ve (', ') gibi birkaç noktalama karakteri kullanılabilir. Farklı ses modelleri için farklı adlar kullanın.

    **Description** alanının yaygın bir kullanımı, modeli oluşturmak için kullanılan veri kümelerinin adlarını kaydetmek olur.

4.  **Eğitim verilerini seçin** sayfasında, eğitim için kullanmak istediğiniz bir veya birden çok veri kümesi seçin. Göndermeden önce vurdıklarla sayısını denetleyin. En-US ve zh-CN ses modelleri için herhangi bir sayıda utterde başlayabilirsiniz. Diğer yerel ayarlarda, bir sesi eğitebilmek için 2.000 'den fazla yer seçmelisiniz.

    > [!NOTE]
    > Yinelenen ses adları eğitiminden kaldırılacak. Seçtiğiniz veri kümelerinin birden çok. zip dosyası arasında aynı ses adlarını içermediğinden emin olun.

    > [!TIP]
    > Kalite sonuçları için aynı konuşmacının veri kümelerini kullanmak gereklidir. Eğitim için gönderdiğiniz veri kümeleri 6.000 farklı dıklardan toplam sayısı içeriyorsa, Istatistiksel parametrik Sensit tekniği aracılığıyla ses modelinizi eğitecaksınız. Eğitim verilerinizin toplam 6.000 farklı kuralı aşması durumunda, birleştirme birleştirme tekniğinin bulunduğu bir eğitim işlemini kapatıcaksınız. Normalde birleştirme teknolojisi, daha doğal ve daha yüksek uygunlukta sesli sonuçlara neden olabilir. En son sinir TTS teknolojisine sahip bir modeli eğitebilmeniz istiyorsanız, genel kullanıma açık [sinir sesine](language-support.md#neural-voices)bir dijital sesli eşdeğer üretebilmeniz Için [özel ses ekibine başvurun](https://go.microsoft.com/fwlink/?linkid=2108737) .

5.  Ses modelinizi oluşturmaya başlamak için **eğitme** ' ye tıklayın.

Eğitim tablosu, bu yeni oluşturulan modele karşılık gelen yeni bir giriş görüntüler. Tabloda Ayrıca durum görüntülenir: Işlem, başarılı, başarısız.

Gösterilen durum, burada gösterildiği gibi, veri kümenizi bir ses modeline dönüştürme sürecini yansıtır.

| Durum | Anlamı |
| ----- | ------- |
| İşleniyor | Ses modeliniz oluşturuluyor. |
| Başarılı oldu | Ses modeliniz oluşturuldu ve dağıtılabilir. |
| Başarısız | Ses modeliniz birçok nedenden dolayı eğitiminde başarısız oldu, örneğin görülmeyen veri sorunları veya ağ sorunları. |

Eğitim süresi, işlenen ses verilerinin hacmine bağlı olarak değişir. Tipik saatler, yaklaşık olarak 30 dakika boyunca yaklaşık 40 saat, 20.000 utterlerin saati kadar arasındadır. Model eğitiminizi başarılı olduktan sonra test edebilirsiniz.

> [!NOTE]
> Ücretsiz abonelik (F0) kullanıcıları aynı anda bir ses yazı tipi eğitebilir. Standart abonelik (S0) kullanıcıları aynı anda üç ses de eğitebilir. Sınıra ulaştıysanız, en az bir ses yazı tipinden eğitim bitene kadar bekleyip yeniden deneyin.

> [!NOTE]
> Abonelik başına eğitime izin verilen maksimum ses modeli sayısı, ücretsiz abonelik (F0) kullanıcıları için 10 modeldir ve Standart abonelik (S0) kullanıcıları için 100.

Sinir Voice eğitim özelliğini kullanıyorsanız, gerçek zamanlı akış senaryoları için en iyi duruma getirilmiş bir modeli eğitme veya zaman uyumsuz [Long-Audio birleştirmede](long-audio-api.md)en iyi duruma GETIRILMIŞ bir HD sinir modeli seçebilirsiniz.  

## <a name="test-your-voice-model"></a>Ses modelinizi test etme

Ses yazı tipi başarıyla derlendikten sonra, kullanım için dağıtılmadan önce test edebilirsiniz.

1.  **Metinden konuşmaya > özel sesli > teste**gidin.

2.  **Test Ekle**' ye tıklayın.

3.  Test etmek istediğiniz bir veya daha fazla modeli seçin.

4.  Sesinizin konuşmasını istediğiniz metni sağlayın. Tek seferde birden çok modeli test etmeyi seçtiyseniz, farklı modeller için test için aynı metin kullanılacaktır.

    > [!NOTE]
    > Metninizin dili, ses yazı tipinin diliyle aynı olmalıdır. Yalnızca başarılı eğitilen modeller test edilebilir. Bu adımda yalnızca düz metin desteklenir.

5.  **Oluştur**'a tıklayın.

Test isteğinizi gönderdikten sonra, sınama sayfasına dönersiniz. Tablo artık yeni isteğinize ve durum sütununa karşılık gelen bir giriş içerir. Konuşmayı senberleştirmek birkaç dakika sürebilir. Durum sütunu **başarılı**olduğunda, sesi yürütebilir veya metin girişi (bir. txt dosyası) ve ses çıkışını (bir. wav dosyası) indirebilir ve daha sonra kalite için daha fazla sesleme yapabilirsiniz.

Test sonuçlarını, test için seçtiğiniz her modellerin ayrıntı sayfasında de bulabilirsiniz. **Eğitim** sekmesine gidin ve model ayrıntısı sayfasına girmek için model adına tıklayın.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Özel bir ses uç noktası oluşturma ve kullanma

Ses modelinizi başarıyla oluşturup test ettikten sonra, özel bir metin okuma uç noktasına dağıtırsınız. Sonra bu uç noktayı, REST API aracılığıyla metinden konuşmaya istekleri yaparken her zamanki uç noktanın yerine kullanırsınız. Özel uç noktanız yalnızca yazı tipini dağıtmak için kullandığınız abonelik tarafından çağrılabilir.

Yeni bir özel ses uç noktası oluşturmak için, **metinden konuşmaya > özel sesli > dağıtımına**gidin. **Uç nokta Ekle** ' yi seçin ve özel uç noktanız Için bir **ad** ve **Açıklama** girin. Ardından, bu uç nokta ile ilişkilendirmek istediğiniz özel ses modelini seçin.

**Ekle** düğmesine tıkladıktan sonra, uç nokta tablosunda, yeni uç noktanız için bir giriş görürsünüz. Yeni bir uç noktanın örneklendirilecek birkaç dakika sürebilir. Dağıtımın durumu **başarılı**olduğunda, uç nokta kullanıma hazırdır.

> [!NOTE]
> Ücretsiz abonelik (F0) kullanıcılarının yalnızca bir modeli dağıtılmış olabilir. Standart abonelik (S0) kullanıcıları, her biri kendi özel sesiyle en fazla 50 uç nokta oluşturabilir.

> [!NOTE]
> Özel sesinizi kullanmak için, ses modeli adını belirtmeniz, özel URI 'yi doğrudan bir HTTP isteğinde kullanmanız ve aynı aboneliği kullanarak TTS hizmetinin kimlik doğrulamasından geçmesi gerekir.

Uç noktanız dağıtıldıktan sonra, uç nokta adı bir bağlantı olarak görünür. Uç nokta anahtarı, uç nokta URL 'SI ve örnek kod gibi uç noktanıza özgü bilgileri göstermek için bağlantıya tıklayın.

Uç noktanın çevrimiçi testi, özel ses portalı aracılığıyla da kullanılabilir. Uç noktanızı test etmek için **uç nokta ayrıntısı** sayfasından **uç noktayı denetle** ' yi seçin. Uç nokta testi sayfası görüntülenir. Söylenen metni (metin kutusunda düz metin veya [SSML biçiminde](speech-synthesis-markup.md) ) girin. Özel ses yazı tipinde konuşulan metni dinlemek için **Yürüt**' ü seçin. Bu test özelliği, özel konuşma sensıs kullanımınıza göre ücretlendirilir.

Özel uç nokta, metinden konuşmaya istekleri için kullanılan standart uç nokta ile aynıdır. Daha fazla bilgi için bkz. [REST API](rest-text-to-speech.md) .

## <a name="next-steps"></a>Sonraki adımlar

* [Kılavuz: ses örneklerinizi kaydetme](record-custom-voice-samples.md)
* [Metinden konuşmaya API başvurusu](rest-text-to-speech.md)
* [Uzun ses API 'SI](long-audio-api.md)
