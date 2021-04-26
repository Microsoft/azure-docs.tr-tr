---
title: Ses Içeriği oluşturma-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Ses Içeriği oluşturma, Microsoft 'un Uygulamalarınız ve ürünleriniz için kendi metin okuma çıktısını özelleştirmenizi ve ayarlamanıza olanak tanıyan bir çevrimiçi araçtır.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 7e43c472c939049e5dfd4ec4df909a3178ef8e2d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553269"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Ses Içeriği oluşturma aracı ile sensıs 'yi geliştirme

[Ses Içeriği oluşturma](https://aka.ms/audiocontentcreation) , Audiobooks, haber yayınları, görüntülü konuşmalar ve sohbet botları gibi çeşitli senaryolar için yüksek oranda doğal ses içeriği oluşturmanızı sağlayan kullanımı kolay ve güçlü bir araçtır. Ses Içeriği oluşturma sayesinde, metin okuma seslerinin ince ayarlarını yapabilir ve özelleştirilmiş ses deneyimlerini verimli ve düşük maliyetli bir şekilde tasarlayabilirsiniz.

Araç, [konuşma sen, biçimlendirme dilini (SSML)](speech-synthesis-markup.md)temel alır. Metin okuma çıkış özniteliklerini gerçek zamanlı olarak ayarlamanıza olanak sağlar veya ses karakterleri, ses stilleri, konuşma hızı, telaffuz ve Prosody gibi Batch senkçlerini ayarlayabilirsiniz.

150 ' den fazla önceden oluşturulmuş sese, en son teknoloji sinir TTS seslerini ve bir tane oluşturduysanız özel sesinize yönelik daha fazla 50 ' e daha kolay erişim sağlayabilirsiniz. 

Ses Içeriği oluşturma için [video öğreticisine](https://www.youtube.com/watch?v=O1wIJ7mts_w) bakın.

## <a name="how-to-get-started"></a>Nasıl başlıyorsunuz?

Ses Içeriği oluşturma ücretsiz bir araçtır, ancak kullandığınız Azure konuşma hizmeti için ödeme yaparsınız. Araçla çalışmak için bir Azure hesabıyla oturum açmanız ve bir konuşma kaynağı oluşturmanız gerekir. Her bir Azure hesabı için, sinir TTS seslendirme için 500.000 karakter (ayda), standart ve özel sesler (ayda) için 5.000.000 karakterleri ve 1 özel ses uç noktası barındırma hizmeti (ayda) dahil olmak üzere aylık ücretsiz konuşma kotaları vardır. Aylık olarak ayrılan miktar genellikle 3-5 kişinin etrafında küçük bir içerik ekibi için yeterlidir. Azure hesabı oluşturma ve bir konuşma kaynağı alma adımları aşağıda verilmiştir. 

### <a name="step-1---create-an-azure-account"></a>1. adım-Azure hesabı oluşturma

Ses Içeriği oluşturma ile çalışmak için bir [Microsoft hesabı](https://account.microsoft.com/account) ve bir [Azure hesabınız](https://azure.microsoft.com/free/ai/)olması gerekir. [Hesabı ayarlamak](./overview.md#try-the-speech-service-for-free)için bu yönergeleri izleyin. 

[Azure Portal](https://portal.azure.com/) , Azure hesabınızı yönetebileceğiniz merkezi bir yerdir. Konuşma kaynağını oluşturabilir, ürün erişimini yönetebilir ve basit Web uygulamalarından karmaşık bulut dağıtımlarına kadar her şeyi izleyebilirsiniz. 

### <a name="step-2---create-a-speech-resource"></a>2. adım-konuşma kaynağı oluşturma

Azure hesabına kaydolduktan sonra, konuşma hizmetlerine erişmek için Azure hesabınızda bir konuşma kaynağı oluşturmanız gerekir. [Konuşma kaynağı oluşturma](./overview.md#create-the-azure-resource)yönergelerini görüntüleyin. 

Yeni konuşma kaynağınızın dağıtılması birkaç dakika sürer. Dağıtım tamamlandıktan sonra, ses Içeriği oluşturma yolculuğunu başlatabilirsiniz. 

 >[!NOTE]
   > Sinir seslerinizi kullanmayı planlıyorsanız, kaynağınızı [sinir seslerini destekleyen bir bölgede](regions.md#neural-and-standard-voices)oluşturduğunuzdan emin olun.
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>3. adım-Azure hesabınızla ve konuşma kaynağınız ile ses Içeriği oluşturma 'da oturum açın

1. Azure hesabı ve konuşma kaynağını aldıktan sonra, **kullanmaya** başlayın ' a tıklayarak [ses içeriği oluşturma](https://aka.ms/audiocontentcreation) ' da oturum açabilirsiniz.
2. **Konuşma kaynağı** sayfası size görüntülenecektir. Üzerinde çalışmak istediğiniz konuşma kaynağını seçin. Ses oluşturmaya başlamak için **Studio 'Ya git** ' e tıklayın. Ayrıca, **Yeni oluştur**' a tıklayarak yeni bir konuşma kaynağı da oluşturabilirsiniz. Ses Içeriği oluşturma aracında bir sonraki sefer oturum açtığınızda, sizi doğrudan geçerli konuşma kaynağı altındaki ses çalışma dosyalarına bağlayacağız. 
3. Konuşma kaynağınızı, üst gezinti bölmesinde bulunan **Ayarlar** seçeneğiyle istediğiniz zaman değiştirebilirsiniz.

## <a name="how-to-use-the-tool"></a>Aracı nasıl kullanılır?

Bu diyagramda, metinden konuşmaya okuma çıktıları üzerinde ince ayar yapmak için gereken adımlar gösterilir. Her adım hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kullanın.

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="Metin okuma çıktıları için ince ayar yapmak için gereken adımların diyagramı":::


1. Üzerinde çalışmak istediğiniz konuşma kaynağını seçin.
2. Düz metin veya SSML betikleri kullanarak [bir ses ayarlama dosyası oluşturun](#create-an-audio-tuning-file) . İçeriğinizi ses Içeriği oluşturmak için yazın veya karşıya yükleyin.
3. Komut dosyası içeriğiniz için ses ve dili seçin. Ses Içeriği oluşturma, tüm [Microsoft metin okuma seslerini](language-support.md#text-to-speech)içerir. Standart, sinir veya kendi özel sesinizi kullanabilirsiniz.
   >[!NOTE]
   > Geçitli erişim, doğal bir konuşma konuşmayla benzer yüksek tanımlı sesler oluşturmanıza olanak sağlayan özel sinir sesleri için kullanılabilir. Daha fazla bilgi için bkz. [işlem işleme](./text-to-speech.md).

4. Varsayılan sensıs çıkışının önizlemesi için **oynat** simgesine (bir üçgen) tıklayın. Ardından, telaffuz, kesme, sıklık, oran, intonation, ses stili ve daha fazlasını ayarlayarak çıktıyı geliştirebilirsiniz. Seçeneklerin tamamı listesi için bkz. [konuşma sen, biçimlendirme dili](speech-synthesis-markup.md). İşte ses Içeriği oluşturma ile konuşma çıkışının nasıl ince ayarlanacağını gösteren bir [video](https://www.youtube.com/watch?v=O1wIJ7mts_w) . 
5. [Ayarlanmış sesinizi kaydedin ve dışarı aktarın](#export-tuned-audio). Ayarlama parçasını sisteme kaydettiğinizde, çalışmaya ve çıkışta yineleme yapmaya devam edebilirsiniz. Çıktıyı karşıladığınızda, dışa aktarma özelliği ile bir ses oluşturma görevi oluşturabilirsiniz. Dışa aktarma görevinin durumunu gözlemleyebilirsiniz ve uygulama ve ürünlerinizle kullanım için çıktıyı indirebilirsiniz.

## <a name="create-an-audio-tuning-file"></a>Ses ayarlama dosyası oluşturma

İçeriğinizi ses Içeriği oluşturma aracına almanın iki yolu vardır.

**Seçenek 1:**

1. Yeni bir ses ayarlama dosyası oluşturmak için sağ üst köşedeki **yeni dosya** simgesine tıklayın.
2. İçeriğinizi, Düzen penceresine yazın veya yapıştırın. Her bir dosyanın karakterleri 20.000 ' e kadar olur. Betiğinizin 20.000 karakterden uzun olması durumunda, içeriğinizi otomatik olarak birden çok dosyaya bölmek için 2 seçeneğini kullanabilirsiniz. 
3. Kaydetmeyi unutmayın.

**Seçenek 2:**

1. Bir veya daha fazla metin dosyasını içeri aktarmak için **Yükle** ' ye tıklayın. Hem düz metin hem de SSML desteklenir. Betik dosyanız 20.000 karakterden büyükse, lütfen dosyayı paragraflara, karaktere veya normal ifadelerle ayırın. 
3. Metin dosyalarınızı karşıya yüklediğinizde, dosyanın bu gereksinimleri karşıladığından emin olun.

   | Özellik | Değer/notlar |
   |----------|---------------|
   | Dosya biçimi | Düz metin (. txt)<br/> SSML metni (. txt)<br/> ZIP dosyaları desteklenmiyor |
   | Kodlama biçimi | UTF-8 |
   | Dosya adı | Her dosyanın benzersiz bir adı olmalıdır. Yinelemeler desteklenmez. |
   | Metin uzunluğu | Metin dosyasının karakter sınırlaması 20.000 ' dir. Dosyalarınız sınırlamayı aşarsa, lütfen dosyaları araçtaki yönergelerle ayırın. |
   | SSML kısıtlamaları | Her SSML dosyası yalnızca tek bir SSML parçası içerebilir. |

**Düz metin örneği**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```
**SSML metin örneği**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Ayarlanmış sesi dışa aktarma

Ses çıktlarınızı inceledikten ve ayarlama ve ayarlamasıyla memnun olduktan sonra, sesi dışa aktarabilirsiniz.

1. Ses oluşturma görevi oluşturmak için **dışarı aktar** ' a tıklayın. Uzun ses çıkışını ve tam ses çıkış deneyimini desteklediğinden, **Ses kitaplığına dışa aktarma** önerilir. Ayrıca, sesi yerel diskinize doğrudan indirebilirsiniz, ancak yalnızca ilk 10 dakika kullanılabilir.
2. Ayarlanmış sesinizi için çıkış biçimini seçin. Desteklenen biçimlerin ve örnek hızların listesi aşağıda verilmiştir.
3. Görevi **dışarı aktar** sekmesinde görevin durumunu görüntüleyebilirsiniz. Görev başarısız olursa, tam bir rapor için ayrıntılı bilgi sayfasına bakın.
4. Görev tamamlandığında ses **kitaplığı** sekmesinde Sesinizdeki indirilebilir.
5. **İndir**’e tıklayın. Artık uygulama veya ürünleriniz için özel olarak ayarlanmış sesinizi kullanmaya hazırsınız.

**Desteklenen ses biçimleri**

| Biçimlendir | 16 kHz örnek hızı | 24 kHz örnek hızı |
|--------|--------------------|--------------------|
| WAV | Riff-16khz-16bit-mono-PCM | Riff-24khz-16bit-mono-PCM |
| ça | Ses-16khz-128kbit hızı-mono-MP3 | Ses-24khz-160kbit hızı-mono-MP3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>Ses Içeriği oluşturma kullanıcı ekleme/kaldırma

Birden fazla Kullanıcı ses Içeriği oluşturmayı kullanmak isterse, Azure aboneliğine ve konuşma kaynağına yönelik kullanıcı erişimi verebilirsiniz. Bir Azure aboneliğine Kullanıcı eklerseniz, Kullanıcı Azure aboneliğindeki tüm kaynaklara erişebilir. Ancak bir konuşma kaynağına yalnızca bir Kullanıcı eklerseniz, kullanıcının yalnızca konuşma kaynağına erişimi olur ve bu Azure aboneliği kapsamındaki diğer kaynaklara erişemez. Konuşma kaynağına erişimi olan bir Kullanıcı, ses Içeriği oluşturmayı kullanabilir.

### <a name="add-users-to-a-speech-resource"></a>Konuşma kaynağına Kullanıcı ekleme

Ses Içeriği oluşturmayı kullanabilmesi için bir konuşma kaynağına Kullanıcı eklemek için bu adımları izleyin.

1. Bilişsel **Hizmetler** için arama [Azure Portal](https://portal.azure.com/), Kullanıcı eklemek istediğiniz konuşma kaynağını seçin.
2. **Erişim denetimi (IAM)** öğesine tıklayın. Bu aboneliğin tüm rol atamalarını görüntülemek için **Rol atamaları** sekmesine tıklayın.
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="Rol atama sekmesi":::
1. **Ekle** > **Rol ataması ekle** seçeneğine tıklayarak Rol ataması ekle bölmesini açın. Rol açılan listesinde bilişsel **hizmet Kullanıcı** rolünü seçin. Bu konuşma kaynağının Kullanıcı sahipliğini vermek istiyorsanız, **sahip** rolünü seçebilirsiniz.
1. Listeden bir kullanıcı seçin. Kullanıcıyı listede görmüyorsanız, görünen ad ve e-posta adresleri için dizini aramak üzere seç kutusunu yazabilirsiniz. Kullanıcı bu dizinde değilse, kullanıcının [Microsoft hesabı](https://account.microsoft.com/account) (Azure Active Directory tarafından güvenilen) girişi yapabilirsiniz.
1. Rolü atamak için **Kaydet**’e tıklayın. Birkaç dakika sonra, Kullanıcı, konuşma kaynağı kapsamında bilişsel hizmet kullanıcı rolüne atanır.

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="Rol Ekle iletişim kutusu":::

1. Eklediğiniz kullanıcılara bir davet e-postası gönderilir. Azure 'a   >  **katılması için davet kabulü** kabul et 'e tıkladıktan sonra, [ses içeriği oluşturma](https://aka.ms/audiocontentcreation)'yı kullanabilirler.

Aynı konuşma kaynağına sahip kullanıcılar, her birinin ses Içeriği oluşturma Studio 'da çalışmasını görür. Her bir kullanıcının ses Içeriği oluşturmada benzersiz ve özel çalışma alanına sahip olmasını istiyorsanız, lütfen her bir kullanıcı için [Yeni bir konuşma kaynağı oluşturun](#step-2---create-a-speech-resource) ve her kullanıcıya konuşma kaynağına benzersiz erişim verin. 

### <a name="remove-users-from-a-speech-resource"></a>Bir konuşma kaynağından kullanıcıları kaldırma
1. Bilişsel **Hizmetler** için arama Azure Portal, kullanıcıları kaldırmak istediğiniz konuşma kaynağını seçin.
2. **Erişim denetimi (IAM)** öğesine tıklayın. Bu konuşma kaynağına yönelik tüm rol atamalarını görüntülemek için **rol atamaları** sekmesine tıklayın.
3. Kaldırmak istediğiniz kullanıcıları seçin, **Kaldır**  >  **Tamam**' a tıklayın.
    :::image source="media/audio-content-creation/remove-user.png" alt-text="Kaldır düğmesi":::

### <a name="enable-users-to-grant-access"></a>Kullanıcıların erişim vermesini sağlama
Kullanıcılardan birinin diğer kullanıcılara erişim izni vermesini istiyorsanız, kullanıcıya konuşma kaynağı için sahip rolünü vermeniz ve kullanıcıyı Azure Dizin okuyucusu olarak ayarlamanız gerekir. 
1. Kullanıcıyı konuşma kaynağının sahibi olarak ekleyin. Bkz. [bir konuşma kaynağına Kullanıcı ekleme](#add-users-to-a-speech-resource).
    :::image source="media/audio-content-creation/add-role.png" alt-text="Rol sahibi alanı":::
1. Sol üstteki daraltılmış menüyü seçin. **Azure Active Directory**' a ve ardından **Kullanıcılar**' a tıklayın.
1. Kullanıcının Microsoft hesabı arayın ve kullanıcının ayrıntılar sayfasına gidin. **Atanan roller**' e tıklayın.
1. **Atama**  ->  **Dizin okuyucuları** Ekle ' ye tıklayın.

## <a name="see-also"></a>Ayrıca bkz.

* [Uzun ses API 'SI](./long-audio-api.md)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
