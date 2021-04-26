---
title: "Öğretici: konuşma SDK 'sını kullanarak botunuzu etkinleştirme-konuşma hizmeti"
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, Microsoft bot Framework kullanarak bir Echo bot oluşturacak, bunu Azure 'a dağıtacaksınız ve bunu bot Framework Direct Line konuşma kanalına kaydedeceksiniz. Daha sonra, Windows için bir örnek istemci uygulaması oluşturacaksınız ve bu, sizin için bot 'a konuşmanıza ve size yanıt vermenizi sağlamanıza olanak tanır.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: fa449ad3d9a0e26bd0754a67581c8d63fa025e55
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552317"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Öğretici: konuşma SDK 'sını kullanarak bot uygulamanızı etkinleştirin

Bir sohbet bot 'ı sesli etkinleştirmek için konuşma hizmetini kullanabilirsiniz.

Bu öğreticide, size söylediklerinizi tekrardan yineleme sağlayan bir bot oluşturacaksınız.
Microsoft bot Framework kullanarak botunuzu oluşturacak, Azure 'a dağıtırsınız ve bot Framework Direct Line konuşma kanalına kaydetmelisiniz.
Daha sonra, Windows için bir örnek istemci uygulaması oluşturacaksınız ve bu da, bot 'unuza konuşmanıza ve sizi dinleyebilmenizi sağlar.

Bu öğretici, Azure, bot Framework botları, doğrudan konuşma veya konuşma SDK 'Sı için yeni olan geliştiriciler için tasarlanmıştır ve sınırlı kodlamaya sahip çalışan bir sistemi hızlıca oluşturmak ister. Bu hizmetlerle ilgili deneyim veya benzerlik yok.

Bu öğreticide yaptığınız ses özellikli sohbet bot şu adımları izler:

1. Örnek istemci uygulaması, doğrudan hat konuşma kanalına ve yankı bot 'a bağlanacak şekilde yapılandırılmıştır.
1. Kullanıcı bir düğmeye bastığında, mikrofondan sesli ses akışları. (Veya özel bir anahtar sözcük kullanıldığında ses sürekli olarak kaydedilir.)
1. Özel bir anahtar sözcük kullanılırsa, yerel cihazda anahtar sözcük algılama gerçekleşir ve bu, buluta ses akışını ele alır.
1. Konuşma SDK 'sını kullanarak, örnek istemci uygulaması doğrudan hat konuşma kanalına bağlanır ve ses akışı sağlar.
1. İsteğe bağlı olarak, hizmette daha yüksek doğruluk anahtar sözcüğü doğrulaması gerçekleşir.
1. Ses, konuşma tanıma hizmetine geçirilir ve metne kaydedilir.
1. Tanınan metin, bir bot Framework etkinliği olarak Echo bot 'a geçirilir.
1. Yanıt metni, metinden konuşmaya (TTS) hizmeti tarafından sese açılır ve kayıttan yürütme için istemci uygulamasına geri akışı yapılır.

<!-- svg src in User Story 1754106 -->
![Diyagram-etiket](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "Konuşma kanalı akışı")

> [!NOTE]
> Bu öğreticideki adımlarda ücretli bir hizmet gerekmez. Yeni bir Azure kullanıcısı olarak, ücretsiz Azure deneme aboneliğinizdeki kredileri ve bu öğreticiyi tamamlayabilmeniz için konuşma hizmetinin ücretsiz katmanını kullanabilirsiniz.

Bu öğreticinin şu şekilde ele alınmaktadır:
> [!div class="checklist"]
> * Yeni Azure kaynakları oluşturma
> * Yankı Bot örneğini bir Azure App Service oluşturun, test edin ve dağıtın
> * Doğrudan hat konuşma kanalı ile botunuzu kaydetme
> * Yankı Botunuzla etkileşim kurmak için Windows Voice Yardımcısı Istemcisini derleyin ve çalıştırın
> * Özel anahtar sözcük etkinleştirme Ekle
> * Tanınan ve konuşulan konuşma dilini değiştirmeyi öğrenin

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için gerekenler aşağıda verilmiştir:

- Çalışan bir mikrofona ve hoparlörlere (veya kulaklıktan) sahip bir Windows 10 PC
- **ASP.net ve Web geliştirme** iş yükü yüklüyken [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) veya üzeri
- [.NET Framework Runtime 4.6.1](https://dotnet.microsoft.com/download) veya üzeri
- Azure hesabı. [Ücretsiz kaydolun](https://azure.microsoft.com/free/cognitive-services/)
- Bir [GitHub](https://github.com/) hesabı
- [Windows için Git](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Bu öğreticide oluşturacağınız istemci uygulaması, el ile Azure hizmetlerini kullanır. Botunuzun yanıtlarının gidiş dönüş süresini azaltmak için, bu hizmetlerin aynı Azure bölgesinde bulunduğundan emin olmak isteyeceksiniz. Bu bölümde, **Batı ABD** bölgesinde bir kaynak grubu oluşturacaksınız. Bu kaynak grubu, bot Framework, doğrudan hat konuşma kanalı ve konuşma hizmeti için ayrı kaynaklar oluştururken kullanılacaktır.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Kaynak grubu oluşturma </a>
1. Sizden bazı bilgiler sağlamanız istenecektir:
   * **Aboneliği** **ücretsiz denemeye** ayarlayın (mevcut bir aboneliği de kullanabilirsiniz).
   * **Kaynak grubunuz** için bir ad girin. **SpeechEchoBotTutorial-ResourceGroup** önerilir.
   * **Bölge** açılır listesinden **Batı ABD**' yi seçin.
1. **Gözden geçir ve oluştur**’a tıklayın. Okuma **doğrulamasının geçtiğini** belirten bir başlık görmeniz gerekir.
1. **Oluştur**’a tıklayın. Kaynak grubunun oluşturulması birkaç dakika sürebilir.
1. Bu öğreticide daha sonra oluşturacağınız kaynaklarda olduğu gibi, kolay erişim için bu kaynak grubunu panonuza sabitlemek iyi bir fikirdir. Bu kaynak grubunu sabitlemek isterseniz, kaynak grubu adının sağ sabitleme simgesine tıklayın.

### <a name="choosing-an-azure-region"></a>Azure bölgesi seçme

Bu öğretici için farklı bir bölge kullanmak istiyorsanız, bu faktörler seçimlerinizle sınırlı olabilir:

* [Desteklenen bir Azure bölgesi](regions.md#voice-assistants)kullandığınızdan emin olun.
* Doğrudan hat konuşma kanalı, sinir ve standart sesde bulunan metin okuma hizmetini kullanır. Sinir ve standart seslerin hepsi bu [Azure bölgelerinde](regions.md#neural-and-standard-voices)kullanılabilir.

Bölgeler hakkında daha fazla bilgi için bkz. [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Kaynak oluşturma

Artık desteklenen bir bölgede bir kaynak grubunuz olduğuna göre, bir sonraki adım, bu öğreticide kullanacağınız her bir hizmet için ayrı kaynaklar oluşturmaktır.

### <a name="create-a-speech-service-resource"></a>Konuşma hizmeti kaynağı oluşturma

Bir konuşma kaynağı oluşturmak için aşağıdaki yönergeleri izleyin:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Konuşma hizmeti kaynağı oluşturma </a>
4. Sizden bazı bilgiler sağlamanız istenecektir:
   * Kaynağınız için bir **ad** verin. **SpeechEchoBotTutorial-Speech** önerilir
   * **Abonelik** Için **ücretsiz deneme sürümünün** seçili olduğundan emin olun.
   * **Konum** için **Batı ABD**' yi seçin.
   * **Fiyatlandırma katmanı** için **F0** öğesini seçin. Bu, ücretsiz katmandır.
   * **Kaynak grubu** için **SpeechEchoBotTutorial-ResourceGroup** öğesini seçin.
5. Gerekli tüm bilgileri girdikten sonra **Oluştur**' a tıklayın. Kaynağınızın oluşturulması birkaç dakika sürebilir.
6. Bu öğreticide daha sonra bu hizmet için abonelik anahtarları gerekir. Bu anahtarlara, kaynağınızın **genel bakış** (anahtarları Yönet) veya **anahtarlar** aracılığıyla istediğiniz zaman erişebilirsiniz.

Bu noktada, kaynak grubunuzun (**SpeechEchoBotTutorial-ResourceGroup**) bir konuşma kaynağına sahip olup olmadığını kontrol edin:

| Ad | Tür  | Konum |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Bilişsel Hizmetler | Batı ABD |

### <a name="create-an-azure-app-service-plan"></a>Azure App Service planı oluşturma

Sonraki adım App Service bir plan oluşturmaktır. App Service planı, bir web uygulamasının birlikte çalıştırılacağı işlem kaynakları kümesini tanımlar.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Azure App Service planı oluşturma </a>
4. Sizden bazı bilgiler sağlamanız istenecektir:
   * **Aboneliği** **ücretsiz denemeye** ayarlayın (mevcut bir aboneliği de kullanabilirsiniz).
   * **Kaynak grubu** için **SpeechEchoBotTutorial-ResourceGroup** öğesini seçin.
   * Kaynağınız için bir **ad** verin. **SpeechEchoBotTutorial-AppServicePlan** önerilir
   * **Işletim sistemi** için **Windows**' u seçin.
   * **Bölge** için **Batı ABD**' yi seçin.
   * **Fiyatlandırma katmanı** Için **Standart S1** ' in seçildiğinden emin olun. Bu, varsayılan değer olmalıdır. Değilse, **Işletim sistemini** yukarıda açıklandığı gibi **Windows** 'a ayarladığınızdan emin olun.
5. **Gözden geçir ve oluştur**’a tıklayın. Okuma **doğrulamasının geçtiğini** belirten bir başlık görmeniz gerekir.
6. **Oluştur**’a tıklayın. Kaynak grubunun oluşturulması birkaç dakika sürebilir.

Bu noktada, kaynak grubunuz (**SpeechEchoBotTutorial-ResourceGroup**) iki kaynak olduğunu kontrol edin:

| Ad | Tür  | Konum |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service Planı | Batı ABD |
| SpeechEchoBotTutorial-Speech | Bilişsel Hizmetler | Batı ABD |

## <a name="build-an-echo-bot"></a>Yankı bot oluştur

Bazı kaynaklar oluşturduğunuza göre artık bir bot oluşturalım. Adın gösterdiği gibi, yanıt olarak girdiğiniz metni yankılayan yankı bot örneğiyle çalışmaya başlayacağız. Endişelenmeyin, örnek kod herhangi bir değişiklik yapılmadan kullanabileceğiniz şekilde kullanıma yöneliktir. Bu, botu Azure 'a dağıttıktan sonra bağlanacağımız doğrudan hat konuşma kanalı ile çalışmak üzere yapılandırılmıştır.

> [!NOTE]
> Aşağıdaki yönergeler ve yankı bot ile ilgili ek bilgiler, [GitHub 'daki örnek Benioku](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)dosyasında bulunur.

### <a name="run-the-bot-sample-on-your-machine"></a>Makinenizde Bot örneğini çalıştırın

1. Örnek deposunu kopyalayın:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Visual Studio 'Yu başlatın.
3. Araç çubuğundan **Dosya**  >  **Aç**  >  **Proje/çözüm**' ü seçin ve Echo bot proje çözümünü açın:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Proje yüklendikten sonra, projeyi derlemek ve çalıştırmak için <kbd>F5</kbd> ' e basın.
5. Bir tarayıcı başlatılır ve aşağıdakine benzer bir ekran görürsünüz.
    > [!div class="mx-imgBorder"]
    > [![Ekran görüntüsü, botunuzun hazırlanmaya yönelik yankı bot sayfasını gösterir!](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "Localhost üzerinde çalışan yankı bot")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Bot Framework öykünücüsü ile bot örneğini test etme

[Bot Framework öykünücüsü](https://github.com/microsoft/botframework-emulator) , bot geliştiricilerinin botlarını yerel olarak test etme ve hata ayıklama olanağı sağlayan bir masaüstü uygulamasıdır (veya bir tünel üzerinden uzaktan). Öykünücü, girilen metni giriş (ses değil) olarak kabul eder. Bot aynı zamanda metinle yanıt verir. Metin girişi ve metin çıktısı ile yerel olarak çalışan yankı botunuzu test etmek için, bot Framework öykünücüsünü kullanmak üzere bu adımları izleyin. Bot 'ı Azure 'a dağıttıktan sonra, bu dosyayı sesli giriş ve ses çıkışıyla test edeceğiz.

1. [Bot Framework öykünücü](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) sürümü 4.3.0 veya üstünü yükler
2. Bot Framework öykünücüsünü başlatın ve botunuzu açın:
   * **Dosya**  ->  **Bot 'ı açın**.
3. Bot 'unuzun URL 'sini girin. Örnek:

   ```
   http://localhost:3978/api/messages
   ```
   ve "Bağlan" düğmesine basın.
4. Bot sizi "Merhaba ve hoş geldiniz!" ile Grele iletisi döndürmektedir. Herhangi bir metin iletisini yazın ve bot 'tan bir yanıt aldığınızı onaylayın.
5. Bu, bir Echo bot örneğiyle iletişim alışverişi yapmak gibi görünebilir: [ ![ekran görüntüsü, bot Framework öykünücüsünü gösterir.](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot Framework öykünücüsü")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Botunuzu bir Azure App Service dağıtma

Sonraki adım, yankı bot 'ı Azure 'a dağıtmaktır. Bir bot dağıtmanın birkaç yolu vardır, ancak bu öğreticide doğrudan Visual Studio 'dan yayımlamaya odaklanacağız.

> [!NOTE]
> Alternatif olarak, [Azure CLI](/azure/bot-service/bot-builder-deploy-az-cli) ve [dağıtım şablonlarını](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/03.core-bot)kullanarak bir bot dağıtabilirsiniz.

> [!NOTE]
> **Yayımla...** aşağıdaki adımları gerçekleştirirken görünmez, **ASP.net ve Web geliştirme** iş yükünü eklemek için Visual Studio yükleyicisi kullanın.

1. Visual Studio 'da, doğrudan hat konuşma kanalı ile kullanılmak üzere yapılandırılmış yankı botnu açın:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. **Çözüm Gezgini**, **yankı bot** projesine sağ tıklayın ve Yayımla ' yı seçin **...**
1. **Yayımla** adlı yeni bir pencere açılır.
1. **Azure**' ı seçin, **İleri**' ye tıklayın, **Azure App Service (Windows)** öğesini seçin, **İleri**' ye tıklayın ve yeşil artı Işaretine göre **Yeni bir Azure App Service... oluştur** seçeneğine tıklayın
1. **App Service (Windows)** penceresi göründüğünde:
   * **Hesap Ekle**' ye tıklayın ve Azure hesabı kimlik bilgilerinizle oturum açın. Zaten oturum açtıysanız, açılan listeden istediğiniz hesabı seçin.
   * **Ad** Için, bot için genel olarak benzersiz bir ad girmeniz gerekir. Bu ad, benzersiz bir bot URL 'SI oluşturmak için kullanılır. Tarih ve saat dahil olmak üzere varsayılan bir değer doldurulur (örneğin: "EchoBot20190805125647"). Bu öğretici için varsayılan adı kullanabilirsiniz.
   * **Abonelik** Için, **ücretsiz deneme** olarak ayarlayın
   * **Kaynak grubu** için **SpeechEchoBotTutorial-ResourceGroup** öğesini seçin
   * **Barındırma planı** için **SpeechEchoBotTutorial-appserviceplan** ' ı seçin.
1. **Oluştur**’a tıklayın. Son sihirbaz ekranında **son**' a tıklayın.
1. Yayımla ekranının sağ tarafında **Yayımla** ' ya tıklayın. Visual Studio, bot 'ı Azure 'a dağıtır.
1. Visual Studio 'nun çıkış penceresinde şuna benzer bir başarı iletisi görmeniz gerekir:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Varsayılan tarayıcınızın açılması ve "bot 'Niz hazır!" yazan bir sayfa görüntülemesi gerekir.
1. Bu noktada, Azure portal kaynak grubunuzu **SpeechEchoBotTutorial-ResourceGroup** ' u kontrol edin ve bu üç kaynağı onaylayın:

| Ad | Tür  | Konum |
|------|-------|----------|
| EchoBot20190805125647 | App Service | Batı ABD |
| SpeechEchoBotTutorial-AppServicePlan | App Service planı | Batı ABD |
| SpeechEchoBotTutorial-Speech | Bilişsel Hizmetler | Batı ABD |

## <a name="enable-web-sockets"></a>Web yuvalarını etkinleştir

Botunuzun Web yuvalarını kullanarak doğrudan hat konuşma kanalıyla iletişim kurabilmesi için küçük bir yapılandırma değişikliği yapmanız gerekir. Web yuvalarını etkinleştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com)gidin ve App Service tıklayın. Kaynak, **EchoBot20190805125647** (benzersiz uygulamanızın adı) ile aynı olmalıdır.
2. Sol gezinti bölmesindeki **Ayarlar** altında **yapılandırma**' ya tıklayın.
3. **Genel ayarlar** sekmesini seçin.
4. **Web Yuvaları** geçiş biçimini bulun ve **Açık** olarak ayarlayın.
5. **Kaydet**’e tıklayın.

> [!TIP]
> Hizmeti durdurmak veya yeniden başlatmak için Azure App Service sayfanızın en üstündeki denetimleri kullanabilirsiniz. Bu sorun giderirken yararlı olabilir.

## <a name="create-a-channel-registration"></a>Kanal kaydı oluşturma

Botunuzu barındırmak için bir Azure App Service oluşturduğunuza göre, sonraki adım bir **bot kanalları kaydı** oluşturmaktır. Kanal kaydı oluşturma, botunuzu doğrudan hat konuşma kanalı dahil, bot Framework kanallarıyla kaydetmeye yönelik bir önkoşuldur. Botların kanalları kullanma hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [kanallara bir bot bağlama](/azure/bot-service/bot-service-manage-channels).

1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Azure bot kanalları kaydı oluşturma </a>
2. Sizden bazı bilgiler sağlamanız istenecektir:
   * **Bot tutamacı** Için, **SpeechEchoBotTutorial-botregistration-# # # #** girin ve ile değiştirin, **####** sizin tercih ettiğiniz bir sayıdır. Bot tanıtıcısının genel olarak benzersiz olması gerektiğini unutmayın. Bir bot tanıtıcısı girer, ancak şu hata iletisini alırsanız, _istenen bot kimliği kullanılabilir değil_, farklı bir sayı seçin. Aşağıdaki örneklerde 8726 kullandık
   * **Abonelik** Için **ücretsiz deneme**' yı seçin.
   * **Kaynak grubu** için **SpeechEchoBotTutorial-ResourceGroup** öğesini seçin.
   * **Konum** için **Batı ABD**' yi seçin.
     * **Fiyatlandırma katmanı** için **F0** öğesini seçin.
     * **Mesajlaşma uç noktası** için, yolun sonuna eklendiği Web uygulamanızın URL 'sini girin `/api/messages` . Örneğin: genel olarak benzersiz uygulamanızın adı **EchoBot20190805125647** ise, mesajlaşma uç noktanız şöyle olacaktır: `https://EchoBot20190805125647.azurewebsites.net/api/messages/` .
     * **Application Insights** Için bunu **kapalı** olarak ayarlayabilirsiniz. Daha fazla bilgi için bkz. [bot Analytics](/azure/bot-service/bot-service-manage-analytics).
     * **Otomatik uygulama kimliği ve parola oluşturmayı** yoksay.
5. **Bot kanalları kayıt** dikey penceresinin alt kısmındaki **Oluştur**' a tıklayın.

Bu noktada, Azure portal kaynak grubunuzu **SpeechEchoBotTutorial-ResourceGroup** ' u kontrol edin. Şimdi en az dört kaynak göstermesi gerekir:

| Ad | Tür  | Konum |
|------|-------|----------|
| EchoBot20190805125647 | App Service | Batı ABD |
| SpeechEchoBotTutorial-AppServicePlan | App Service planı | Batı ABD |
| SpeechEchoBotTutorial-BotRegistration-8726 | Bot kanalları kaydı | Genel |
| SpeechEchoBotTutorial-Speech | Bilişsel Hizmetler | Batı ABD |

> [!IMPORTANT]
> Batı ABD seçmiş olsanız da, bot kanalları kayıt kaynağı genel bölgeyi gösterir. Bu beklenen bir durumdur.

## <a name="optional-test-in-web-chat"></a>İsteğe bağlı: Web sohbetinde test

Azure bot kanalları kayıt sayfasında, **bot Management** altında **Web sohbeti testi** seçeneği vardır. Web sohbetin bot 'unuza göre kimlik doğrulaması yapması gerektiğinden, bu, varsayılan olarak, bot 'unuzla çalışmaz. Dağıtılan bot 'ı metin girişi ile test etmek istiyorsanız aşağıdaki adımları izleyin. Bu adımların isteğe bağlı olduğuna ve öğreticinin sonraki adımlarına devam edebilmek için gerekli değildir. 

1. [Azure Portal](https://portal.azure.com) **yankı Botöğreticisi-botkayıt-# # # #** kaynağını bulup açın
1. **Bot yönetimi** gezinmede **Ayarlar**' ı seçin. **Microsoft uygulama kimliği** altındaki değeri kopyalayın
1. Visual Studio Yankıbot çözümünü açın. Çözüm Gezgini 'nde, üzerinde **appsettings.js** bulun ve çift tıklayın
1. JSON dosyasında, kopyalanmış KIMLIK değeri ile **Microsoftappıd** yanındaki boş dizeyi değiştirin
1. Azure portal geri dönüp, **bot yönetimi** gezinmede **Ayarlar**' ı SEÇIN ve **Microsoft uygulama kimliği** ' nin yanındaki **(Yönet)** seçeneğine tıklayın.
1. **Yeni istemci parolası**' na tıklayın. Bir açıklama ekleyin (örn. "Web sohbeti") ve **Ekle**' ye tıklayın. Yeni parolayı Kopyala
1. JSON dosyasında, kopyalanmış gizli değeri ile birlikte **Microsoftapppassword** yanındaki boş dizeyi değiştirin
1. JSON dosyasını kaydedin. Şuna benzer şekilde görünecektir:
```json
{
  "MicrosoftAppId": "3be0abc2-ca07-475e-b6c3-90c4476c4370",
  "MicrosoftAppPassword": "-zRhJZ~1cnc7ZIlj4Qozs_eKN.8Cq~U38G"
}
```
9. Uygulamayı yeniden yayımlayın (Visual Studio Çözüm Gezgini 'nde **Yankıbot** projesine sağ tıklayın, **Yayımla...** ' yı seçin ve **Yayımla** düğmesine tıklayın)
10. Şimdi Web sohbetinde bot 'ı test etmeye hazırsınız!

## <a name="register-the-direct-line-speech-channel"></a>Doğrudan hat konuşma kanalını kaydetme

Şimdi doğrudan hat konuşma kanalı ile botunuzu kaydetme zamanı. Bu kanal, bot ile konuşma SDK 'Sı ile derlenen bir istemci uygulaması arasında bir bağlantı oluşturur.

1. [Azure Portal](https://portal.azure.com) **SpeechEchoBotTutorial-botregistration-# # # #** kaynağını bulup açın.
1. **Bot yönetimi** gezinmede **Kanallar**' ı seçin.
   * **Daha fazla kanal** altında **doğrudan hat konuşmayı**' na tıklayın.
   * Sayfadaki **doğrudan konuşmayı Yapılandır**' ı, ardından bilişsel **hizmet hesabı** açılır menüsünü genişleterek sayfada bulunan metni gözden geçirin.
   * Daha önce oluşturduğunuz konuşma kaynağını (ör. **SpeechEchoBotTutorial-Speech**) seçerek bot 'unuzu konuşma abonelik anahtarınızla ilişkilendirin.
   * İsteğe bağlı alanların geri kalanını yoksayın.
   * **Kaydet**’e tıklayın.

1. **Bot yönetimi** gezinmede **Ayarlar**' a tıklayın.
   * **Akış uç noktasını etkinleştir** etiketli kutuyu işaretleyin. Bu, bot ve doğrudan hat konuşma kanalı arasında Web Yuvaları üzerinde oluşturulmuş bir iletişim protokolü oluşturmak için gereklidir.
   * **Kaydet**’e tıklayın.

> [!TIP]
> Daha fazla bilgi edinmek istiyorsanız, bkz. [konuşmayı doğrudan konuşmaya bağlamak için bir bot bağlama](/azure/bot-service/bot-service-channel-connect-directlinespeech). Bu sayfa, ek bilgi ve bilinen sorunları içerir.

## <a name="run-the-windows-voice-assistant-client"></a>Windows Voice Yardımcısı Istemcisini çalıştırma

Bu adımda, Windows Voice Yardımcısı Istemcisini çalıştıracaksınız. İstemci, doğrudan hat konuşma kanalını kullanarak bot 'unuzla iletişimi yönetmek için [konuşma SDK 'sını](./speech-sdk.md) kullanan C# ' de bir WINDOWS PRESENTATION FOUNDATION (WPF) uygulamasıdır. Özel bir istemci uygulaması yazmadan önce botunuzu etkileşmek ve test etmek için kullanın. Açık kaynak olduğundan, yürütülebilir dosyayı indirebilir ve çalıştırabilir ya da kendiniz oluşturabilirsiniz.

Windows Voice Yardımcısı Istemcisinde, bot ile kurulan bağlantıyı yapılandırmanıza, metin konuşmasını görüntülemenize, bot Framework etkinliklerini JSON biçiminde görüntülemenize ve Uyarlamalı kartlar görüntülemenize olanak tanıyan basit bir kullanıcı arabirimi vardır. Ayrıca özel anahtar sözcüklerin kullanımını destekler. Bu istemciyi, bot ile konuşmak ve bir sesli yanıt almak için kullanacaksınız.

> [!NOTE]
> Bu noktada, mikrofonunuzun ve hoparlörlerinizin etkin ve çalışır olduğunu doğrulayın.

1. [Windows Voice Yardımcısı Istemcisinin](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)GitHub deposuna gidin.
1. Her biri için belirtilen yönergeleri izleyin
   * çalıştırmak için bir ZIP paketinde önceden oluşturulmuş bir yürütülebiliri indirin veya
   * Depoyu kopyalayarak ve projeyi oluşturarak çalıştırılabilir dosyayı kendiniz oluşturun.

1. `VoiceAssistantClient.exe`GitHub deposundaki yönergeleri izleyerek istemci uygulamasını başlatın ve bu uygulamayı bot 'unuza bağlanacak şekilde yapılandırın.
1. **Yeniden bağlan** ' a tıklayın ve **yeni konuşma başladı iletisini gördüğdiğinizden emin olun-yazın veya mikrofon düğmesine basın**.
1. Şimdi test edin. Mikrofon düğmesine tıklayın ve Ingilizce 'de birkaç sözcükten konuşun. Konuşma sırasında tanınan metin görüntülenir. Konuşmayı tamamladığınızda, bot kendi sesinden yanıt vererek "Echo" ve ardından tanınan kelimeleri söyleyerek.
1. Ayrıca, bot ile iletişim kurmak için metin de kullanabilirsiniz. Metni alt çubuğa yazmanız yeterlidir. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Windows Ses Yardımcısı Istemcisinde sorun giderme hataları

Ana uygulama pencerenizde bir hata iletisi alırsanız, hatayı belirlemek ve gidermek için bu tabloyu kullanın:

| Hata | Ne yapmanız gerekir? |
|-------|----------------------|
|Hata (AuthenticationFailure): WebSocket yükseltmesi bir kimlik doğrulama hatasıyla başarısız oldu (401). Doğru abonelik anahtarını (veya yetkilendirme belirtecini) ve bölge adını denetleyin| Uygulamanın Ayarlar sayfasında, konuşma abonelik anahtarını ve bölgesini doğru şekilde girdiğinizden emin olun.<br>Konuşma anahtarınızın ve anahtar bölgenizin doğru girildiğinden emin olun. |
|Hata (ConnectionFailure): bağlantı uzak ana bilgisayar tarafından kapatıldı. Hata kodu: 1011. Hata ayrıntıları: İleti göndermeden önce bot 'a bağlanamıyoruz | ["Akış uç noktasını etkinleştir"](#register-the-direct-line-speech-channel) kutusunu ve/veya [ **Web yuvalarını**](#enable-web-sockets) açık olarak iade ettiğinizden emin olun.<br>Azure App Service çalıştığından emin olun. Varsa App Service yeniden başlatmayı deneyin.|
|Hata (ConnectionFailure): bağlantı uzak ana bilgisayar tarafından kapatıldı. Hata kodu: 1002. Hata ayrıntıları: ' 101 ' durum kodu beklenirken sunucu ' 503 ' durum kodunu döndürdü | ["Akış uç noktasını etkinleştir"](#register-the-direct-line-speech-channel) kutusunu ve/veya [ **Web yuvalarını**](#enable-web-sockets) açık olarak iade ettiğinizden emin olun.<br>Azure App Service çalıştığından emin olun. Varsa App Service yeniden başlatmayı deneyin.|
|Hata (ConnectionFailure): bağlantı uzak ana bilgisayar tarafından kapatıldı. Hata kodu: 1011. Hata ayrıntıları: yanıt durum kodu başarıyı göstermiyor: 500 (ınternalservererror)| Bot, çıkış etkinliği [konuş](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) alanında bir sinir sesi belirtti, ancak konuşma abonelik anahtarınızla ilişkili Azure bölgesi sinir seslerini desteklemez. Bkz. [sinir ve standart sesler](./regions.md#neural-and-standard-voices).|

Sorununuz tabloda giderilmemişse, bkz. [sesli Yardımcılar: sık sorulan sorular](faq-voice-assistants.md). Bu öğreticideki tüm adımları tamamladıktan sonra sorununuzu çözemezseniz, lütfen  [sesli yardım GitHub sayfasında](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/issues)yeni bir sorun girin.

#### <a name="a-note-on-connection-time-out"></a>Bağlantı zaman aşımı üzerine bir göz atın

Bir bot 'a bağlıysanız ve son 5 dakika içinde hiçbir etkinlik gerçekleştiyse, hizmet, istemci ve bot ile WebSocket bağlantısını otomatik olarak kapatır. Bu tasarım gereğidir. Alt çubukta bir ileti görüntülenir: *"etkin bağlantı zaman aşımına uğradı ancak isteğe bağlı olarak yeniden bağlanmaya hazır"*. "Yeniden bağlan" düğmesine basmanız gerekmez. mikrofon düğmesine basmanız ve konuşmaya başlamak, bir kısa mesaj yazmanız veya (etkinse) anahtar sözcüğünü söylemek yeterlidir. Bağlantı otomatik olarak yeniden yüklenecek.  
### <a name="view-bot-activities"></a>Bot etkinliklerini görüntüle

Her bot **etkinlik** iletilerini gönderir ve alır. Windows Voice Yardımcısı Istemcisinin **etkinlik günlüğü** penceresinde, istemcinin bot 'tan aldığı her bir etkinlikle birlikte zaman damgalınılan Günlükler görürsünüz. Ayrıca, istemcinin, yöntemini kullanarak bot 'a gönderdiği etkinlikleri görebilirsiniz [`DialogServiceConnector.SendActivityAsync`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync)  . Bir günlük öğesini seçtiğinizde, ilişkili etkinliğin ayrıntılarını JSON olarak gösterir.

İstemcinin aldığı bir etkinliğin örnek JSON 'si aşağıda verilmiştir:

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration-8726"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

JSON çıktısında döndürülen şeyler hakkında daha fazla bilgi için bkz. [etkinteki alanlar](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Bu öğreticinin amacı doğrultusunda [metin](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) ve [konuş](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) alanlarına odaklanabilirsiniz.

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Konuşma SDK 'Sı çağrıları için istemci kaynak kodunu görüntüleme

Windows Ses Yardımcısı Istemcisi, konuşma SDK 'sını içeren [Microsoft. Biliveservices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)NuGet paketini kullanır. Örnek kodu gözden geçirmeye başlamak için iyi bir yer vardır: dosyadaki InitSpeechConnector () yöntemi, [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) Bu Iki konuşma SDK nesnesini oluşturur:
- [`DialogServiceConfig`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) -Yapılandırma ayarları için (örneğin, konuşma abonelik anahtarı, anahtar bölgesi)
- [`DialogServiceConnector`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) -Tanınan konuşma ve bot yanıtlarını işlemek için kanal bağlantısını ve istemci abonelik olaylarını yönetmek için.

## <a name="add-custom-keyword-activation"></a>Özel anahtar sözcük etkinleştirme Ekle

Konuşma SDK 'Sı özel anahtar sözcük etkinleştirmesini destekler. Microsoft 'un Yardımcısı için "Hey Cortana" a benzer şekilde, tercih ettiğiniz bir anahtar sözcüğü sürekli olarak dinleyebileceğiniz bir uygulama yazabilirsiniz. Bir anahtar sözcüğünün tek sözcük veya çok sözcüklü bir tümcecik olabileceğini aklınızda bulundurun.

> [!NOTE]
> Terim *anahtar sözcüğü* genellikle *uyanma sözcüğünün* teriminin yerine kullanılır ve her ikisini de Microsoft belgelerinde kullanabilirsiniz.

Anahtar sözcük algılama, istemci uygulamasında yapılır. Anahtar sözcük kullanılıyorsa, ses yalnızca anahtar sözcük algılanırsa doğrudan hat konuşma kanalına akıtıdır. Doğrudan hat konuşma kanalı, seçtiğiniz anahtar sözcüğünün ses akışının başlangıcında olduğunu doğrulamak için bulutta daha karmaşık işlem yapan *anahtar sözcük doğrulaması (KWV)* adlı bir bileşeni içerir. Anahtar sözcük doğrulaması başarılı olursa kanal, bot ile iletişim kurar.

Anahtar sözcük modeli oluşturmak için bu adımları izleyin, Windows Voice Yardımcısı Istemcisini bu modeli kullanacak şekilde yapılandırın ve son olarak, bunu bot 'ınızla test edin.

1. [Konuşma hizmetini kullanarak özel bir anahtar sözcük oluşturmak](./custom-keyword-basics.md)için bu yönergeleri izleyin.
2. Önceki adımda indirdiğiniz model dosyasını sıkıştırmasını açın. Anahtar kelime için adlandırılmalıdır. Adında bir dosya arıyorsunuz `kws.table` .
3. Windows Ses Yardımcısı Istemcisinde **Ayarlar** menüsünü bulun (sağ üst köşedeki dişli simgesine bakın). **Model dosya yolunu** bulun ve adım 2 ' deki dosyanın tam yol adını girin `kws.table` .
4. **Etkin** etiketli kutuyu denetlediğinizden emin olun. Bu iletiyi onay kutusunun yanında görmeniz gerekir: "sonraki bağlantıdan sonra anahtar sözcüğü dinler". Yanlış dosya veya geçersiz bir yol sağladıysanız bir hata iletisi görmeniz gerekir.
5. Konuşma **abonelik anahtarınızı**, **abonelik anahtarı bölgenizi** girip **Tamam** ' a tıklayarak **Ayarlar** menüsünü kapatın.
6. **Yeniden bağlan**' a tıklayın. Şunu okuyan bir ileti görmelisiniz: "yeni konuşma başlatıldı-yazın, mikrofon düğmesine basın veya" anahtar sözcüğünü söyleyin ". Uygulama artık sürekli dinliyor.
7. Anahtar kelimesiyle başlayan herhangi bir tümceciği konuşun. Örneğin: "**{anahtar kelime}**, ne zaman var?". Anahtar sözcüğü alındıktan sonra duraklamanız gerekmez. İşiniz bittiğinde iki şey meydana gelir:
   * Bağlı olduğunuz kadar bir komut dosyası görürsünüz
   * Kısa süre sonra, bot 'un yanıtını duydunuz
8. Bot 'unuzun desteklediği üç giriş türüyle denemeler yapmaya devam edin:
   * Alt çubukta yazılan metin
   * Mikrofon simgesine ve konuşmaya basma
   * Anahtar kelimesiyle başlayan bir tümceciği belirten

### <a name="view-the-source-code-that-enables-keyword"></a>Anahtar sözcüğü sağlayan kaynak kodunu görüntüleme

Windows Voice Yardımcısı Istemci kaynak kodunda, anahtar sözcük algılamayı etkinleştirmek için kullanılan kodu gözden geçirmek için şu dosyalara göz atın:

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs) , bir konuşma SDK yöntemine bir çağrı içerir [`KeywordRecognitionModel.fromFile()`](/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel#fromfile-string-) ve bu, modeli diskteki yerel bir dosyadan oluşturmak için kullanılır.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) sürekli anahtar sözcük algılamayı etkinleştiren konuşma SDK yöntemine bir çağrı içerir [`DialogServiceConnector.StartKeywordRecognitionAsync()`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync) .

## <a name="optional-change-the-language-and-bot-voice"></a>Seçim Dili ve bot sesini değiştirme

Oluşturduğunuz bot, varsayılan ABD Ingilizcesi metin okuma sesiyle Ingilizce 'yi dinler ve bu şekilde yanıt verir. Ancak, Ingilizce veya varsayılan bir ses ile sınırlı değilsiniz. Bu bölümde, bot 'unuzun dinleyeceği ve yanıt verdiği dilin nasıl değiştirileceğini öğreneceksiniz. Ayrıca, bu dil için farklı bir ses seçme hakkında bilgi edineceksiniz.

### <a name="change-the-language"></a>Dili değiştirme

[Konuşma-metin](language-support.md#speech-to-text) tablosunda belirtilen dillerden herhangi birini seçebilirsiniz. Aşağıdaki örnekte, dili Almanca olarak değiştirecağız.

1. Windows Ses Yardımcısı Istemci uygulamasını açın, Ayarlar düğmesine (sağ üst dişli simgesine) tıklayın ve `de-de` dil alanına girin (Bu, [konuşmadan metne](language-support.md#speech-to-text) tabloda belirtilen yerel ayar değeridir). Bu, varsayılan olarak geçersiz kılan konuşulan dili tanınmak üzere ayarlar `en-us` . Bu ayrıca doğrudan hat konuşma kanalına, bot yanıtı için varsayılan bir Alman sesi kullanmasını söyler.
2. Ayarlar sayfasını kapatın ve yankı bot 'a yeni bir bağlantı kurmak için yeniden Bağlan düğmesine tıklayın.
3. Mikrofon düğmesine tıklayın ve Almanya 'da bir ifade söyleyin. Tanınan metin ve yankı bot 'ın varsayılan Alman sesiyle yanıt olarak olduğunu görürsünüz.

### <a name="change-the-default-bot-voice"></a>Varsayılan bot sesini değiştirme

Metin okuma sesinin belirlenmesi ve söylenişi, bir konuşmayı basit metin yerine bir [konuşma Sensimi biçimlendirme dili](speech-synthesis-markup.md) (SSML) biçiminde belirtiyorsa, telaffuz denetimi yapılabilir. Yankı botu SSML kullanmaz, ancak bunu yapmak için kodu kolayca değiştirebiliriz. Aşağıdaki örnekte, varsayılan kadın sesi yerine Almanya sesli Stefan Apollo (erkek ses) kullanılacak şekilde SSML 'yi Echo bot yanıtına ekledik. Diliniz için desteklenen [standart sesler](language-support.md#standard-voices) ve [sinir seslerinizin](language-support.md#neural-voices) listesini görüntüleyin.

1. Açılarak başlayalım `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs` .
2. Şu iki satırı bulun:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Bunları ile değiştirin:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Visual Studio 'da çözümünüzü derleyin ve tüm derleme hatalarını düzeltin.

' MessageFactory. Text ' yöntemindeki ikinci bağımsız değişken, bot yanıtında [etkinlik konuş alanını](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) ayarlar. Yukarıdaki değişiklik ile, varsayılan olmayan bir Almanya sesi belirtmek için basit metinden SSML 'ye değiştirilmiştir.

### <a name="redeploy-your-bot"></a>Botunuzu yeniden dağıtın

Şimdi de gerekli bir değişikliği yapmış olduğunuza göre, bir sonraki adım, Azure App Service yeniden yayımlamanız ve denemek için:

1. Çözüm Gezgini penceresinde, **yankı bot** projesine sağ tıklayın ve **Yayımla**' yı seçin.
2. Önceki dağıtım yapılandırmanız varsayılan olarak zaten yüklenmiştir. **EchoBot20190805125647-Web dağıtımı**' nin yanında **Yayımla** ' ya tıklamanız yeterlidir.
3. **Yayımla başarılı** Iletisi Visual Studio çıktı penceresinde görünür ve bir Web sayfası, "bot 'niz hazırlanıyor!" iletisiyle başlatılır.
4. Windows Ses Yardımcısı Istemci uygulamasını açın, Ayarlar düğmesine (sağ üst dişli simgesi) tıklayın ve dil alanında hala bulunduğundan emin olun `de-de` .
5. Yeni dağıtılan bot ile yeniden bağlanmak için [Windows Voice Yardımcısı Istemcisini çalıştırma](#run-the-windows-voice-assistant-client) bölümündeki yönergeleri izleyin, yeni dilde konuşun ve yeni sesle bu dilde her bir bot yanıtı dinleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide dağıtılan Echo-bot 'ı kullanmaya devam edemeyecekseniz, **SpeechEchoBotTutorial-ResourceGroup** Azure kaynak grubunu silerek bu uygulamayı ve onunla Ilişkili tüm Azure kaynaklarını kaldırabilirsiniz.

1. [Azure Portal](https://portal.azure.com) **Azure hizmetleri** gezinmesinin **kaynak grupları** ' na tıklayın.
2. Şu ada sahip kaynak grubunu bulun: **SpeechEchoBotTutorial-ResourceGroup**. Üç noktaya (...) tıklayın.
3. **Kaynak grubunu sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Konuşma SDK 'Sı ile kendi istemci uygulamanızı derleme](./quickstarts/voice-assistants.md?pivots=programming-language-csharp)

## <a name="see-also"></a>Ayrıca bkz.

* Bot yanıt süresi iyileştirmesini görmek için [size yakın bir Azure bölgesine](https://azure.microsoft.com/global-infrastructure/locations/) dağıtım
* [Yüksek kaliteli sınır TTS seslerini destekleyen bir Azure bölgesine](./regions.md#neural-and-standard-voices) dağıtım
* Doğrudan hat konuşma kanalı ile ilişkili fiyatlandırma:
  * [Bot hizmeti fiyatlandırması](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Konuşma hizmeti](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Kendi seslendirmeyi kullanan bot 'ı derleme ve dağıtma:
  * Bir [bot Framework bot](https://dev.botframework.com/)oluşturun. [Doğrudan hat konuşma kanalına](/azure/bot-service/bot-service-channel-connect-directlinespeech) kaydolun ve [botunuzu ses için özelleştirin](/azure/bot-service/directline-speech-bot)
  * Mevcut [bot Framework çözümlerini](https://microsoft.github.io/botframework-solutions/index)keşfet: sanal bir [yardımcı](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) oluşturun ve [doğrudan konuşmaya konuşma için genişletin](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
