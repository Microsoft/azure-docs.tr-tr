---
title: Konuşma tanıma hizmeti nedir?
titleSuffix: Azure Cognitive Services
description: Konuşma hizmeti, konuşmadan metne, metinden konuşmaya ve konuşma çevirisi için tek bir Azure aboneliğine sahip değildir. Konuşma SDK 'Sı, konuşma cihazları SDK 'Sı veya REST API 'Leri ile uygulamalarınıza, araçlara ve cihazlara konuşma ekleyin.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/23/2020
ms.author: trbye
ms.openlocfilehash: ddb61a79e6af402b06fe8c18df8c62f9287437a3
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209723"
---
# <a name="what-is-the-speech-service"></a>Konuşma tanıma hizmeti nedir?

Konuşma hizmeti, konuşmadan metne, metinden konuşmaya ve konuşma çevirisi için tek bir Azure aboneliğine yönelik bir birleşme hizmetidir. Konuşma [CLI](spx-overview.md), [konuşma SDK 'Sı](./speech-sdk.md), [konuşma cihazları SDK 'Sı](./speech-devices-sdk-quickstart.md?pivots=platform-android), [konuşma Studio](https://speech.microsoft.com/)veya [REST API 'leri](#reference-docs)ile uygulamalarınızı, araçları ve cihazlarınızı kolayca yapabilirsiniz.

> [!IMPORTANT]
> Konuşma hizmeti Bing Konuşma API'si ve Translator Konuşma Çevirisi değiştirdi. Geçiş yönergeleri için _geçiş_ bölümüne bakın.

Aşağıdaki özellikler konuşma hizmetinin bir parçasıdır. Her bir özellik için genel kullanım örnekleri hakkında daha fazla bilgi edinmek için bu tablodaki bağlantıları kullanın veya API başvurusuna gidin.

| Hizmet | Özellik | Açıklama | SDK | REST |
|---------|---------|-------------|-----|------|
| [Konuşmayı metne dönüştürme](speech-to-text.md) | Gerçek zamanlı konuşmayı metne dönüştürme | Konuşmadan metne dönüştürme veya ses akışlarını veya yerel dosyaları, uygulamalarınızın, araçlarınızın veya cihazlarınızın tüketebileceği veya görüntüleyeceği gerçek zamanlı metne dönüştürür. Kullanıcı amaçlarını [Language Understanding (lusıs)](../luis/index.yml) ile birlikte kullanarak konuşma ve ses komutları üzerinde Kullanıcı hedefleri türetebilirsiniz. | [Evet](./speech-sdk.md) | [Evet](#reference-docs) |
| | [Toplu iş konuşmayı metne dönüştürme](batch-transcription.md) | Toplu iş konuşmayı metne dönüştürme, Azure Blob depolamada depolanan büyük hacimde konuşma ses verilerinin zaman uyumsuz konuşmadan metne dökümünü sağlar. Konuşma sesini metne dönüştürmenin yanı sıra, toplu konuşma tanıma özelliği de, yaklaşım ve yaklaşım analizi için de izin verir. | No | [Evet](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [Çok cihazlı konuşma](multi-device-conversation.md) | Konuşma ve çeviri için kolay destek sayesinde konuşma veya metin tabanlı iletiler göndermek için bir konuşmadaki birden çok cihaza veya istemciye bağlanın| Yes | Hayır |
| | [Konuşma dökümü](./conversation-transcription.md) | Gerçek zamanlı konuşma tanımayı, konuşmacı tanımlamayı ve okumayı mümkün hale getirme. Hoparlörleri, konuşmacıları ayırt etme imkanına sahip olmak için bir kişi içi toplantılar için mükemmeldir. | Yes | Hayır |
| | [Özel Konuşma Tanıma modelleri oluşturma](#customize-your-speech-experience) | Benzersiz bir ortamda tanıma ve döküm için konuşmaya metin kullanıyorsanız, çevresel gürültü veya sektöre özgü sözlük için özel akustik, dil ve telaffuz modeller oluşturup eğitebilirsiniz. | No | [Evet](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| [Metin okuma](text-to-speech.md) | Metin okuma | Metin okuma [, konuşma birleştirme biçimlendirme dili (SSML)](speech-synthesis-markup.md)kullanarak giriş metnini insan benzeri sentezleştirilmiş konuşmaya dönüştürür. Derin sinir ağları tarafından desteklenen insan benzeri sesler olan sinir seslerini kullanın. Bkz. [dil desteği](language-support.md). | [Evet](./speech-sdk.md) | [Evet](#reference-docs) |
| | [Özel sesler oluşturma](#customize-your-speech-experience) | Markanızı veya ürününüzün benzersiz özel ses yazı tiplerini oluşturun. | No | [Evet](#reference-docs) |
| [Konuşma Çevirisi](speech-translation.md) | Konuşma çevirisi | Konuşma çevirisi, uygulamalarınıza, araçlara ve cihazlara gerçek zamanlı, çok dilli konuşma çevirisi sağlar. Konuşmayı konuşmaya dönüştürme ve konuşmadan metne çevirme için bu hizmeti kullanın. | [Evet](./speech-sdk.md) | No |
| [Ses yardımcıları](voice-assistants.md) | Ses yardımcıları | Konuşma hizmetini kullanan sesli yardımcılar, geliştiricilerin uygulamalar ve deneyimleri için doğal ve insan benzeri konuşma arabirimleri oluşturmalarına olanak sağlar. Ses Yardımcısı hizmeti, bir cihaz ile bir yardımcı uygulama arasında, bot çerçevesinin doğrudan hat konuşma kanalını veya görev tamamlama için tümleşik özel komutlar hizmetini kullanan hızlı ve güvenilir bir etkileşim sağlar. | [Evet](voice-assistants.md) | No |
| [Konuşmacı Tanıma](speaker-recognition-overview.md) | Konuşmacı doğrulama & kimliği | Konuşmacı Tanıma hizmeti, kendi benzersiz ses özelliklerine göre hoparlörleri doğrulayan ve tanımlayan algoritmalar sağlar. Konuşmacı Tanıma, "konuşuyor kim?" sorusunu yanıtlamak için kullanılır. | Yes | [Evet](/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Konuşma tanıma hizmetini ücretsiz olarak deneyin

Aşağıdaki adımlar için hem Microsoft hesabı hem de bir Azure hesabınızın olması gerekir. Bir Microsoft hesabı yoksa, [Microsoft hesabı portalında](https://account.microsoft.com/account)ücretsiz bir ücret karşılığında kaydolabilirsiniz. **Microsoft hesabıyla oturum açın** ' yi seçin ve ardından oturum açmak Istendiğinde **Microsoft hesabı oluştur**' u seçin. Yeni Microsoft hesabı oluşturup doğrulamak için adımları izleyin.

Microsoft hesabı aldıktan sonra [Azure kaydolma sayfasına](https://azure.microsoft.com/free/ai/)gidin, **ücretsiz Başlat**' ı seçin ve Microsoft hesabı kullanarak yeni bir Azure hesabı oluşturun. [Azure Ücretsiz hesabına kaydolma hakkında](https://www.youtube.com/watch?v=GWT2R1C_uUU)bir video aşağıda verilmiştir.

> [!NOTE]
> Ücretsiz bir Azure hesabı için kaydolduğunuzda, 30 güne kadar geçerli olan ücretli bir konuşma hizmeti aboneliğine uygulayabileceğiniz hizmet kredisi $200 ile gelir. Krediniz 30 gün sonunda çalıştırıldığında veya sona erdiğinde Azure hizmetleriniz devre dışı bırakılır. Azure hizmetlerini kullanmaya devam etmek için hesabınızı yükseltmeniz gerekir. Daha fazla bilgi için bkz. [Azure ücretsiz hesabınızı yükseltme](../../cost-management-billing/manage/upgrade-azure-subscription.md). 
>
> Konuşma hizmetinde iki hizmet katmanı vardır: ücretsiz (F0) ve abonelik (S0), farklı sınırlamalara ve avantajlara sahiptir. Ücretsiz, düşük hacimli konuşma hizmeti katmanını kullanıyorsanız, ücretsiz deneme veya hizmet kredinizin süresi dolduktan sonra bile bu ücretsiz aboneliği koruyabilirsiniz. Daha fazla bilgi için bkz. bilişsel [Hizmetler fiyatlandırması-konuşma hizmeti](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-azure-resource"></a>Azure kaynağı oluşturma

Azure hesabınıza bir konuşma hizmeti kaynağı (ücretsiz veya ücretli katman) eklemek için:

1. Microsoft hesabınızı kullanarak [Azure portalında](https://portal.azure.com/) oturum açın.

1. Portalın sol üst kısmında **kaynak oluştur** ' u seçin. **Kaynak oluştur**' u görmüyorsanız, ekranın sol üst köşesindeki daraltılan menüyü seçerek her zaman bulabilirsiniz.

1. **Yeni** pencerede, arama kutusuna "konuşma" YAZıN ve ENTER tuşuna basın.

1. Arama sonuçlarında **konuşma**' yı seçin.

   ![Konuşma Arama sonuçları](media/index/speech-search.png)

1. **Oluştur**' u seçin, sonra:

   - Yeni kaynağınız için benzersiz bir ad verin. Ad, aynı hizmete bağlı birden çok abonelik arasında ayrım yapmanıza yardımcı olur.
   - Ücretlerin nasıl faturalandırıldığını belirlemek için yeni kaynağın ilişkilendirildiği Azure aboneliğini seçin. Azure portal [Azure aboneliğinin nasıl oluşturulacağı hakkında](../../cost-management-billing/manage/create-subscription.md#create-a-subscription-in-the-azure-portal) giriş aşağıda verilmiştir.
   - Kaynağın kullanılacağı [bölgeyi](regions.md) seçin. Azure, dünya çapındaki birçok bölgede genel olarak kullanılabilen küresel bir bulut platformudur. En iyi performansı elde etmek için, size en yakın olan veya uygulamanızın çalıştığı yerde bir bölge seçin. Konuşma hizmeti kullanılabilirliği farklı bölgelerden farklılık gösterir. Kaynağı desteklenen bir bölgede oluşturduğunuzdan emin olun. Bkz. [konuşma Hizmetleri için bölge desteği](./regions.md#speech-to-text-text-to-speech-and-translation).
   - Ücretsiz (F0) ya da ücretli (S0) fiyatlandırma katmanını seçin. Her katmana yönelik fiyatlandırma ve kullanım kotaları hakkında tam bilgi için, **tam fiyatlandırma ayrıntılarını görüntüle** ' yi seçin veya [konuşma Hizmetleri fiyatlandırmasına](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)bakın. Kaynaklarla ilgili sınırlar için bkz. Azure bilişsel [Hizmetler sınırları](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cognitive-services-limits).
   - Bu konuşma aboneliği için yeni bir kaynak grubu oluşturun veya aboneliği var olan bir kaynak grubuna atayın. Kaynak grupları, çeşitli Azure aboneliklerinizi düzenli halde tutmanıza yardımcı olur.
   - **Oluştur**’u seçin. Bu işlem sizi dağıtıma genel bakış ve dağıtım ilerleme durumu iletilerini görüntüleyecek şekilde götürür.  
<!--
> [!NOTE]
> You can create an unlimited number of standard-tier subscriptions in one or multiple regions. However, you can create only one free-tier subscription. Model deployments on the free tier that remain unused for 7 days will be decommissioned automatically.
-->
Yeni konuşma kaynağınızın dağıtılması birkaç dakika sürer. 

### <a name="find-keys-and-region"></a>Anahtar ve bölge bulma

Tamamlanmış bir dağıtımın anahtarlarını ve bölgesini bulmak için şu adımları izleyin:

1. Microsoft hesabınızı kullanarak [Azure portalında](https://portal.azure.com/) oturum açın.

2. **Tüm kaynaklar**' ı seçin ve bilişsel hizmetler kaynağınızın adını seçin.

3. Sol bölmede, **kaynak yönetimi** altında **anahtarlar ve uç nokta**' ı seçin.

Her abonelik iki anahtara sahiptir; uygulamanızda herhangi bir anahtarı kullanabilirsiniz. Kod düzenleyicinize veya başka bir konuma bir anahtar kopyalamak/yapıştırmak için, her anahtarın yanındaki Kopyala düğmesini seçin, pencereler arasında Pano içeriğini istenen konuma yapıştırın.

Ayrıca, `LOCATION` bölge Kimliğiniz (örn.) değerini kopyalayın. `westus`, `westeurope` ) SDK çağrıları için.

> [!IMPORTANT]
> Bu abonelik anahtarları bilişsel hizmet API 'nize erişmek için kullanılır. Anahtarlarınızı paylaşmayın. Bunları güvenli bir şekilde depolayın (örneğin, Azure Key Vault kullanarak). Ayrıca, bu anahtarların düzenli olarak yeniden oluşturulması önerilir. API çağrısı yapmak için yalnızca bir anahtar gereklidir. İlk anahtarı yeniden oluştururken, hizmete devam eden erişim için ikinci anahtarı kullanabilirsiniz.

## <a name="complete-a-quickstart"></a>Hızlı başlangıç tamamlamayı

Her biri temel tasarım desenleri öğretmek ve kodu 10 dakikadan kısa bir süre içinde çalıştırmak için tasarlanan en popüler programlama dillerinde hızlı başlangıçlar sunuyoruz. Her bir özelliğin hızlı başlangıcı için aşağıdaki listeye bakın.

* [Konuşmayı metne dönüştürmeye yönelik hızlı başlangıç](get-started-speech-to-text.md)
* [Metin okuma için hızlı başlangıç](get-started-text-to-speech.md)
* [Konuşma çevirisine hızlı başlangıç](./get-started-speech-translation.md)
* [Amaç tanıma hızlı başlangıcı](./get-started-intent-recognition.md)
* [Konuşmacı Tanıma hızlı başlangıç](./get-started-speaker-recognition.md)

Konuşma hizmetini kullanmaya başlama şansınız olduktan sonra, çeşitli senaryoları nasıl çözeceğinizi gösteren öğreticilerimizi deneyin.

- [Öğretici: konuşma SDK 'Sı ve LUU, C ile konuşma amaçlarını tanıma #](how-to-recognize-intents-from-speech-csharp.md)
- [Öğretici: konuşma SDK 'Sı ile sesinizi etkinleştirme, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Öğretici: metin çevirmek, yaklaşımı çözümlemek ve çevrilmiş metni konuşmaya dönüştürmek için bir Flask uygulaması oluşturun, REST](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json%252c%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json%252c%2fen-us%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json)

## <a name="get-sample-code"></a>Örnek kodu alma

Örnek kod, konuşma hizmeti için GitHub 'da kullanılabilir. Bu örnekler, bir dosya veya akıştan ses okuma, sürekli ve tek kararlı bir tanıma ve özel modellerle çalışma gibi yaygın senaryoları kapsar. SDK ve REST örneklerini görüntülemek için bu bağlantıları kullanın:

- [Konuşmadan metne, metinden konuşmaya ve konuşma çevirisi örnekleri (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Toplu iş dökümü örnekleri (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Metinden konuşmaya örnekleri (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Ses Yardımcısı örnekleri (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Konuşma deneyiminizi özelleştirin

Konuşma hizmeti yerleşik modellerle iyi çalışmaktadır, ancak ürününüzün veya ortamınızın deneyimini daha fazla özelleştirmek ve ayarlamak isteyebilirsiniz. Özelleştirme seçenekleri, akustik model ayarlamalarından markanızla ilgili benzersiz ses yazı tiplerine kadar değişir.

Diğer ürünler, sağlık veya sigorta gibi belirli amaçlar için ayarlanan konuşma modellerini sunar, ancak herkese eşit olarak erişilebilir. Azure konuşma 'da özelleştirme, başka bir kullanıcı veya müşteri tarafından kullanılamayan *benzersiz rekabet avantajlarınızın* bir parçası haline gelir. Diğer bir deyişle, modelleriniz özel ve yalnızca kullanım için özel olarak ayarlanmıştır.

| Konuşma Hizmeti | Platform | Description |
| -------------- | -------- | ----------- |
| Konuşmayı Metne Dönüştürme | [Özel Konuşma Tanıma](https://aka.ms/customspeech) | Konuşma tanıma modellerini gereksinimlerinize ve kullanılabilir verilerinize göre özelleştirin. Konuşma stili, sözlük ve arka plan gürültüsü gibi konuşma tanıma engellerinin üstesinden gelme. |
| Metin Okuma | [Özel ses](https://aka.ms/customvoice) | Kullanılabilir konuşma verilerinizle Metin Okuma uygulamalarınız için tanınabilir, kendine özgü bir ses oluşturun. Ses çıkışlarını bir dizi parametre kümesini ayarlayarak daha fazla hassas ayarlamalar yapabilirsiniz. |

## <a name="deploy-on-premises-using-docker-containers"></a>Docker kapsayıcılarını kullanarak şirket içinde dağıtma

Şirket içi API özelliklerini dağıtmak için [konuşma hizmeti kapsayıcıları kullanın](speech-container-howto.md) . Bu Docker kapsayıcıları, uyumluluk, güvenlik veya diğer işletimsel nedenlerle hizmeti verilerinize yaklaştırmayı sağlar. Konuşma hizmeti aşağıdaki kapsayıcıları sunar:

* Standart konuşmayı metne dönüştürme
* Özel Konuşma Tanıma metin
* Standart metin okuma
* Sinir metin okuma
* Özel metin okuma (Önizleme)
* Konuşma Dil Algılama (Önizleme)

## <a name="reference-docs"></a>Başvuru belgeleri

- [Konuşma SDK'sı](./speech-sdk.md)
- [Konuşma Cihazları SDK’sı](speech-devices-sdk.md)
- [REST API: konuşmayı metne dönüştürme](rest-speech-to-text.md)
- [REST API: metinden konuşmaya](rest-text-to-speech.md)
- [REST API: toplu Iş dökümü ve özelleştirme](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Konuşmaya metne Başlarken](./get-started-speech-to-text.md) 
>  [Metinden konuşmaya çalışmaya başlama](get-started-text-to-speech.md)