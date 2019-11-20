---
title: Ses Içeriği oluşturma-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Ses Içeriği oluşturma, Microsoft 'un Uygulamalarınız ve ürünleriniz için kendi metin okuma çıktısını özelleştirmenizi ve ayarlamanıza olanak tanıyan bir çevrimiçi araçtır.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 6ec98d1f7956ef1460909f62384a931400b596c4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579639"
---
# <a name="audio-content-creation"></a>Ses Içeriği oluşturma

[Ses Içeriği oluşturma](https://aka.ms/audiocontentcreation) , Microsoft 'un Uygulamalarınız ve ürünleriniz için kendi metin okuma çıktısını özelleştirmenizi ve ayarlamanıza olanak tanıyan bir çevrimiçi araçtır. Bu aracı, daha doğru doğal ifadeler için genel ve özel seslere ince ayar yapmak ve çıktunuzu bulutta yönetmek için kullanabilirsiniz.

Ses Içeriği oluşturma aracı, [konuşma birleştirme biçimlendirme dilini (SSML)](speech-synthesis-markup.md)temel alır. Görsel Içerik oluşturma, özelleştirmeyi ve ayarlamayı basitleştirmek için, metin okuma çıktılarınızı gerçek zamanlı olarak görsel olarak incelemenize olanak sağlar.

## <a name="how-does-it-work"></a>Nasıl çalışır?

Bu diyagramda, özelleştirilmiş konuşmaya metin çıkışlarını ayarlama ve dışarı aktarma için gereken adımlar gösterilir. Her adım hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kullanın.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. İlk adım, [bir Azure hesabı oluşturmak, bir konuşma kaynağını kaydetmek ve bir abonelik anahtarı almak](#create-a-speech-resource)için kullanılır. Abonelik anahtarınız olduktan sonra, konuşma hizmetini çağırmak ve [Ses Içeriği oluşturmaya](https://aka.ms/audiocontentcreation)erişmek için kullanabilirsiniz.
2. Düz metin veya SSML kullanarak [bir ses ayarlama dosyası oluşturun](#create-an-audio-tuning-file) .
3. Ayarlamak istediğiniz sesi ve dili seçin. Ses Içeriği oluşturma, tüm [Microsoft metin okuma seslerini](language-support.md#text-to-speech)içerir. Standart, sinir veya kendi özel sesinizi kullanabilirsiniz.
   >[!NOTE]
   > Geçitli erişim, doğal bir konuşma konuşmayla benzer yüksek tanımlı sesler oluşturmanıza olanak sağlayan özel sinir sesleri için kullanılabilir. Daha fazla bilgi için bkz. [işlem işleme](https://aka.ms/ignite2019/speech/ethics).

4. Varsayılan sonucu gözden geçirin. Daha sonra, telaffuz, sıklık, hız, giriş, ses stili ve daha fazlasını ayarlamak için ayarlama aracını kullanın. Seçeneklerin tamamı listesi için bkz. [konuşma sen, biçimlendirme dili](speech-synthesis-markup.md).
5. [Ayarlanmış sesinizi kaydedin ve dışarı aktarın](#export-tuned-audio). Ayarlama parçasını sisteme kaydettiğinizde, çalışmaya ve çıkışta yineleme yapmaya devam edebilirsiniz. Çıktıyı karşıladığınızda, dışa aktarma özelliği ile bir ses oluşturma görevi oluşturabilirsiniz. Dışarı aktarma görevinin durumunu gözlemleyebilirsiniz ve uygulama ve ürünlerinizle kullanım için çıktıyı indirebilirsiniz.
6. Son adım, uygulamalarınızda ve ürünleriniz için özel olarak ayarlanmış sesi kullanmaktır.

## <a name="create-a-speech-resource"></a>Konuşma kaynağı oluşturma

Konuşma kaynağı oluşturmak ve konuşma Studio 'ya bağlamak için bu adımları izleyin.

1. [Azure hesabına kaydolmak](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account) ve [bir konuşma kaynağı oluşturmak](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)için bu yönergeleri izleyin. Fiyatlandırma katmanınızın **S0**olarak ayarlandığından emin olun. Sinir sesinden birini kullanıyorsanız, kaynağı [desteklenen bir bölgede](regions.md#standard-and-neural-voices)oluşturduğunuzdan emin olun.
2. [Ses Içeriği oluşturma](https://aka.ms/audiocontentcreation)' da oturum açın.
3. Mevcut bir projeyi seçin veya **Yeni oluştur**' a tıklayın.
4. Aboneliğinizi istediğiniz zaman, üst gezinti bölmesinde bulunan **Ayarlar** seçeneğiyle değiştirebilirsiniz.

## <a name="create-an-audio-tuning-file"></a>Ses ayarlama dosyası oluşturma

İçeriğinizi ses Içeriği oluşturma aracına almanın iki yolu vardır.

**Seçenek 1:**

1. [Ses Içeriği oluşturma](https://aka.ms/audiocontentcreation)' da oturum açtıktan sonra yeni bir ses ayarlama dosyası oluşturmak Için **Ses ayarlama** ' ya tıklayın.
2. Düzen penceresi göründüğünde, en fazla 10.000 karakter girebilirsiniz.
3. Kaydetmeyi unutmayın.

**Seçenek 2:**

1. [Ses Içeriği oluşturma](https://aka.ms/audiocontentcreation)sırasında oturum açtıktan sonra bir veya daha fazla metin dosyasını içeri aktarmak Için **karşıya yükle** ' ye tıklayın. Hem düz metin hem de SSML desteklenir.
2. Metin dosyalarınızı karşıya yüklediğinizde, içeriğin bu gereksinimleri karşıladığından emin olun.

   | Özellik | Değer/notlar |
   |----------|---------------|
   | Dosya biçimi | Düz metin (. txt)<br/> SSML metni (. txt)<br/> ZIP dosyaları desteklenmiyor |
   | Kodlama biçimi | UTF-8 |
   | Dosya adı | Her dosyanın benzersiz bir adı olmalıdır. Yinelemeler desteklenmez. |
   | Metin uzunluğu | Metin dosyalarının 10.000 karakteri aşmaması gerekir. |
   | SSML kısıtlamaları | Her SSML dosyası yalnızca tek bir SSML parçası içerebilir. |

### <a name="plain-text-example"></a>Düz metin örneği

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>SSML metin örneği

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Ayarlanmış sesi dışa aktarma

Ses çıktlarınızı inceledikten ve ayarlama ve ayarlamasıyla memnun olduktan sonra, sesi dışa aktarabilirsiniz.

1. Ses [Içeriği oluşturma](https://aka.ms/audiocontentcreation) aracında, bir ses oluşturma görevi oluşturmak Için **dışarı aktar** ' a tıklayın.
2. Ayarlanmış sesinizi için çıkış biçimini seçin. Desteklenen biçimlerin ve örnek hızların listesi aşağıda verilmiştir.
3. Görevi **dışarı aktar** sekmesinde görevin durumunu görüntüleyebilirsiniz. Görev başarısız olursa, tam bir rapor için ayrıntılı bilgi sayfasına bakın.
4. Görev tamamlandığında ses **kitaplığı** sekmesinde Sesinizdeki indirilebilir.
5. **İndir**’e tıklayın. Artık uygulama veya ürünleriniz için özel olarak ayarlanmış sesinizi kullanmaya hazırsınız.

### <a name="supported-audio-formats"></a>Desteklenen ses biçimleri

| Biçimlendir | 16 kHz örnek hızı | 24 kHz örnek hızı |
|--------|--------------------|--------------------|
| WAV | Riff-16khz-16bit-mono-PCM | Riff-24khz-16bit-mono-PCM |
| ça | Ses-16khz-128kbit hızı-mono-MP3 | Ses-24khz-160kbit hızı-mono-MP3 |

## <a name="see-also"></a>Ayrıca bkz.

* [Uzun ses API 'SI](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Konuşma Studio](https://speech.microsoft.com)
