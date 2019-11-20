---
title: "Öğretici: konuşma SDK 'sını kullanarak botunuzu etkinleştirme"
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, Microsoft bot-Framework ' u kullanarak bir Echo bot oluşturacak, bunu Azure 'a dağıtabileceğiniz ve bunu, bot-Framework Direct Line konuşma kanalına kaydedeceksiniz. Daha sonra, Windows için bir örnek istemci uygulaması oluşturacaksınız ve bu, sizin için bot 'a konuşmanıza ve size yanıt vermenizi sağlamanıza olanak tanır.
services: cognitive-services
author: dargilco
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: dcohen
ms.openlocfilehash: c95bc7b58f3883fee54aaa8095cb187eaefdb3e0
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836955"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Öğretici: konuşma SDK 'sını kullanarak bot uygulamanızı etkinleştirin

Artık bir sohbet bot 'ı kolayca sesli olarak etkinleştirmek için konuşma hizmetlerinin gücünü kullanabilirsiniz.

Bu öğreticide, Microsoft bot-Framework ' u kullanarak bir Echo bot oluşturacak, bunu Azure 'a dağıtabileceğiniz ve bunu, bot-Framework Direct Line konuşma kanalına kaydedeceksiniz. Daha sonra, Windows için bir örnek istemci uygulaması oluşturacaksınız ve bu, sizin için bot 'a konuşmanıza ve size yanıt vermenizi sağlamanıza olanak tanır.

Bu öğretici, Azure, bot-Framework botları, doğrudan hat konuşmayı veya konuşma SDK 'Sı ile yolculuğunu başlatan geliştiriciler için tasarlanmıştır ve sınırlı kodlamaya sahip bir çalışma sistemini hızlıca oluşturmak ister. Bu hizmetlerle ilgili deneyim veya benzerlik yok.

Bu alıştırmanın sonunda, şu şekilde çalışacak bir sistem ayarlacaksınız:

1. Örnek istemci uygulaması, doğrudan hat konuşma kanalına ve yankı bot 'a bağlanacak şekilde yapılandırılmıştır
1. Ses, düğmeye basıldığında varsayılan mikrofondan kaydedilir (veya özel anahtar sözcük etkinleştirildiyse sürekli kaydedilir)
1. İsteğe bağlı olarak, özel anahtar sözcük algılama gerçekleşir ve buluta ses akışı geçişi yapılır
1. Uygulama, konuşma SDK 'sını kullanarak doğrudan hat konuşma kanalına bağlanır ve ses akışı sağlar
1. İsteğe bağlı olarak, hizmette daha yüksek doğruluk anahtar sözcüğü doğrulaması gerçekleşir
1. Ses, konuşma tanıma hizmetine geçirilir ve metne kaydedilir
1. Tanınan metin, bir bot Framework etkinliği olarak Echo-bot 'a geçirilir 
1. Yanıt metni, metinden konuşmaya (TTS) hizmeti tarafından sese açılır ve kayıttan yürütme için istemci uygulamasına geri akışı yapılır

![Diyagram-etiket](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "Konuşma kanalı akışı")

> [!NOTE]
> Bu öğreticideki adımlarda ücretli bir hizmet gerekmez. Yeni bir Azure kullanıcısı olarak, ücretsiz Azure deneme aboneliğinizdeki kredileri ve bu öğreticiyi tamamlayabilmeniz için ücretsiz konuşma Hizmetleri katmanını kullanabilirsiniz.

Bu öğreticinin şu şekilde ele alınmaktadır:
> [!div class="checklist"]
> * Yeni Azure kaynakları oluşturma
> * Yankı Bot örneğini bir Azure App Service oluşturun, test edin ve dağıtın
> * Doğrudan hat konuşma kanalı ile botunuzu kaydetme
> * Yankı bot 'unuzla etkileşim kurmak için doğrudan hat konuşma Istemcisini derleyin ve çalıştırın
> * Özel anahtar sözcük etkinleştirme Ekle
> * Tanınan ve konuşulan konuşma dilini değiştirmeyi öğrenin

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlayabilmeniz için gerekenler aşağıda verilmiştir:

- Çalışan bir mikrofona ve hoparlörlere (veya kulaklıktan) sahip bir Windows 10 PC
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) veya üzeri
- [.NET Core SDK](https://dotnet.microsoft.com/download) sürüm 2,1 veya üzeri
- Bir Azure hesabı. [Ücretsiz kaydolun](https://azure.microsoft.com/free/ai/).
- [GitHub](https://github.com/) hesabı
- [Windows için Git](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Bu öğreticide oluşturacağınız istemci uygulaması, el ile Azure hizmetlerini kullanır. Botunuzun yanıtlarının gidiş dönüş süresini azaltmak için, bu hizmetlerin aynı Azure bölgesinde bulunduğundan emin olmak isteyeceksiniz. Bu bölümde, **Batı ABD** bölgesinde bir kaynak grubu oluşturacaksınız. Bu kaynak grubu, bot-Framework, doğrudan hat konuşma kanalı ve konuşma Hizmetleri için ayrı kaynaklar oluştururken kullanılacaktır.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol gezinti bölmesinde **kaynak grupları**' nı seçin. Yeni bir kaynak grubu eklemek için **Ekle** ' ye tıklayın.
1. Sizden bazı bilgiler sağlamanız istenecektir:
   * **Aboneliği** **ücretsiz denemeye** ayarlayın (mevcut bir aboneliği de kullanabilirsiniz).
   * **Kaynak grubunuz**için bir ad girin. **SpeechEchoBotTutorial-ResourceGroup**önerilir.
   * **Bölge** açılır listesinden **Batı ABD**' yi seçin.
1. **Gözden geçir ve oluştur**' a tıklayın. Okuma **doğrulamasının geçtiğini**belirten bir başlık görmeniz gerekir.
1. **Oluştur**'a tıklayın. Kaynak grubunun oluşturulması birkaç dakika sürebilir.
1. Bu öğreticide daha sonra oluşturacağınız kaynaklarda olduğu gibi, kolay erişim için bu kaynak grubunu panonuza sabitlemek iyi bir fikirdir. Bu kaynak grubunu sabitlemek istiyorsanız panonun sağ üst köşesindeki sabitleme simgesine tıklayın.

### <a name="choosing-an-azure-region"></a>Azure bölgesi seçme

Bu öğretici için farklı bir bölge kullanmak istiyorsanız, bu faktörler seçimlerinizle sınırlı olabilir:

* [Desteklenen bir Azure bölgesi](regions.md#voice-assistants)kullandığınızdan emin olun.
* Doğrudan hat konuşma kanalı, standart ve sinir seslendirmeyi içeren metin okuma hizmetini kullanır. Sinir seslerini [belirli Azure bölgeleriyle sınırlıdır](regions.md#standard-and-neural-voices).
* Ücretsiz deneme anahtarları belirli bir bölgeyle sınırlı olabilir.

Bölgeler hakkında daha fazla bilgi için bkz. [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Kaynak oluşturma

Artık **Batı ABD** bölgesinde bir kaynak grubunuz olduğuna göre, bir sonraki adım, bu öğreticide kullanacağınız her bir hizmet için ayrı kaynaklar oluşturmaktır.

### <a name="create-a-speech-services-resource"></a>Konuşma Hizmetleri kaynağı oluşturma

Bir konuşma kaynağı oluşturmak için aşağıdaki yönergeleri izleyin:

1. [Azure Portal](https://portal.azure.com) gidin ve sol gezinmede **kaynak oluştur** ' u seçin.
2. Arama çubuğuna **konuşma**yazın.
3. **Konuşma**' yı seçin ve ardından **Oluştur**' a tıklayın.
4. Sizden bazı bilgiler sağlamanız istenecektir:
   * Kaynağınız için bir **ad**verin. **SpeechEchoBotTutorial-Speech** önerilir
   * **Abonelik**Için **ücretsiz deneme sürümünün** seçili olduğundan emin olun.
   * **Konum**için **Batı ABD**' yi seçin.
   * **Fiyatlandırma katmanı**için **F0**öğesini seçin. Bu, ücretsiz katmandır.
   * **Kaynak grubu**için **SpeechEchoBotTutorial-ResourceGroup**öğesini seçin.
5. Gerekli tüm bilgileri girdikten sonra **Oluştur**' a tıklayın. Kaynağınızın oluşturulması birkaç dakika sürebilir.
6. Bu öğreticide daha sonra bu hizmet için abonelik anahtarları gerekir. Bu anahtarlara, kaynağınızın **genel bakış** (anahtarları Yönet) veya **anahtarlar**aracılığıyla istediğiniz zaman erişebilirsiniz.

Bu noktada, kaynak grubunuzun (**SpeechEchoBotTutorial-ResourceGroup**) bir konuşma kaynağına sahip olup olmadığını kontrol edin:

| ADA | TÜR  | KONUMUNA |
|------|-------|----------|
| SpeechEchoBotTutorial-konuşma | Bilişsel Hizmetler | Batı ABD |

### <a name="create-an-azure-app-service-plan"></a>Azure App Service planı oluşturma

Sonraki adım App Service bir plan oluşturmaktır. App Service planı, bir web uygulamasının birlikte çalıştırılacağı işlem kaynakları kümesini tanımlar.

1. [Azure Portal](https://portal.azure.com) gidin ve sol gezinmede **kaynak oluştur** ' u seçin.
2. Arama çubuğuna **App Service plan**yazın. Sonra, arama sonuçlarından **App Service plan** kartını bulun ve seçin.
3. **Oluştur**'a tıklayın.
4. Sizden bazı bilgiler sağlamanız istenecektir:
   * **Aboneliği** **ücretsiz denemeye** ayarlayın (mevcut bir aboneliği de kullanabilirsiniz).
   * **Kaynak grubu**için **SpeechEchoBotTutorial-ResourceGroup**öğesini seçin.
   * Kaynağınız için bir **ad**verin. **SpeechEchoBotTutorial-AppServicePlan** önerilir
   * **Işletim sistemi**için **Windows**' u seçin.
   * **Bölge**için **Batı ABD**' yi seçin.
   * **Fiyatlandırma katmanı**Için **Standart S1** ' in seçildiğinden emin olun. Bu, varsayılan değer olmalıdır. Değilse, **Işletim sistemini** yukarıda açıklandığı gibi **Windows** 'a ayarladığınızdan emin olun.
5. **Gözden geçir ve oluştur**' a tıklayın. Okuma **doğrulamasının geçtiğini**belirten bir başlık görmeniz gerekir.
6. **Oluştur**'a tıklayın. Kaynak grubunun oluşturulması birkaç dakika sürebilir.

Bu noktada, kaynak grubunuz (**SpeechEchoBotTutorial-ResourceGroup**) iki kaynak olduğunu kontrol edin:

| ADA | TÜR  | KONUMUNA |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service Planı | Batı ABD |
| SpeechEchoBotTutorial-konuşma | Bilişsel Hizmetler | Batı ABD |

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
3. Araç çubuğundan **dosya** >  > **Proje/çözüm** **Aç** ' ı seçin ve Echo bot proje çözümünü açın:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. Proje yüklendikten sonra, projeyi derlemek ve çalıştırmak için `F5` ' a basın.

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Bot Framework öykünücüsü ile bot örneğini test etme

[Bot Framework öykünücüsü](https://github.com/microsoft/botframework-emulator) , bot geliştiricilerinin kendi botlarını yerel olarak veya bir tünel üzerinden uzaktan test etmesine ve hatalarını ayıklamalarına olanak tanıyan bir masaüstü uygulamasıdır. Öykünücü, girilen metni giriş (ses değil) olarak destekler. Bot metinle yanıt verecek. Metin girişi ve metin çıktısı ile yerel olarak çalışan yankı botunuzu test etmek için, bot Framework öykünücüsünü kullanmak üzere bu adımları izleyin. Botu dağıttıktan sonra Azure 'ı sesli giriş ve ses çıkışıyla test edeceğiz.

1. [Bot Framework öykünücü](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) sürümü 4.3.0 veya üstünü yükler
2. Bot Framework öykünücüsünü başlatın ve botunuzu açın:
   * **Dosya** -> **bot 'ı açın**.
3. Bot 'unuzun URL 'sini girin. Örneğin:

   ```
   http://localhost:3978/api/messages
   ```
   ve "Bağlan" düğmesine basın.
4. Bot, sizi "Merhaba ve hoş geldiniz!" ile hemen grejet İleti. Herhangi bir metin iletisini yazın ve bot 'tan bir yanıt aldığınızı onaylayın.

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Botunuzu bir Azure App Service dağıtma

Sonraki adım, yankı bot 'ı Azure 'a dağıtmaktır. Bir bot dağıtmanın birkaç yolu vardır, ancak bu öğreticide doğrudan Visual Studio 'dan yayımlamaya odaklanacağız.

> [!NOTE]
> Alternatif olarak, [Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) ve [dağıtım şablonlarını](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates)kullanarak bir bot dağıtabilirsiniz.

1. Visual Studio 'da, doğrudan hat konuşma kanalı ile kullanılmak üzere yapılandırılmış yankı botnu açın:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. **Çözüm Gezgini**, **yankı bot** çözümüne sağ tıklayın ve **Yayımla...** ' yı seçin.
1. **Bir yayımlama hedefi seç** başlıklı yeni bir pencere açılır.
1. Sol gezinti **App Service** seçin, **Yeni oluştur**' u ve ardından **Yayımla**' yı seçin.
1. **App Service oluştur** penceresi göründüğünde:
   * **Hesap Ekle**' ye tıklayın ve Azure hesabı kimlik bilgilerinizle oturum açın. Zaten oturum açtıysanız, açılan listeden istediğiniz hesabı seçin.
   * **Uygulama adı**Için, bot için genel olarak benzersiz bir ad girmeniz gerekir. Bu ad, benzersiz bir bot URL 'SI oluşturmak için kullanılır. Tarih ve saat dahil olmak üzere varsayılan bir değer doldurulur (örneğin: "EchoBot20190805125647"). Bu öğretici için varsayılan adı kullanabilirsiniz.
   * **Abonelik**Için, **ücretsiz deneme** olarak ayarlayın
   * **Kaynak grubu**için **SpeechEchoBotTutorial-ResourceGroup** öğesini seçin
   * **Barındırma planı**için **SpeechEchoBotTutorial-appserviceplan** ' ı seçin.
1. **Oluştur**'a tıklayın
1. Visual Studio 'da şuna benzer bir başarı iletisi görmeniz gerekir:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Varsayılan tarayıcınızın açılması ve "bot 'Niz hazır!" yazan bir sayfa görüntülemesi gerekir.
1. Bu noktada, Azure portal kaynak grubunuzu **SpeechEchoBotTutorial-ResourceGroup** ' u kontrol edin ve üç kaynak olduğunu onaylayın:

| ADA | TÜR  | KONUMUNA |
|------|-------|----------|
| EchoBot20190805125647 | App Service | Batı ABD |
| SpeechEchoBotTutorial-AppServicePlan | App Service planı | Batı ABD |
| SpeechEchoBotTutorial-konuşma | Bilişsel Hizmetler | Batı ABD |

## <a name="enable-web-sockets"></a>Web yuvalarını etkinleştir

Botunuzun Web yuvalarını kullanarak doğrudan hat konuşma kanalıyla iletişim kurabilmesi için küçük bir yapılandırma değişikliği yapmanız gerekir. Web yuvalarını etkinleştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com)gidin ve App Service bulun. Kaynak, **EchoBot20190805125647** (benzersiz uygulamanızın adı) ile aynı olmalıdır.
2. Sol gezinti bölmesinde **Ayarlar**altında **yapılandırma**' ya tıklayın.
3. **Genel ayarlar** sekmesini seçin.
4. **Web Yuvaları** geçiş biçimini bulun ve **Açık**olarak ayarlayın.
5. **Kaydet** düğmesine tıklayın.

> [!TIP]
> Hizmeti durdurmak veya yeniden başlatmak için Azure App Service sayfanızın en üstündeki denetimleri kullanabilirsiniz. Bu sorun giderirken yararlı olabilir.

## <a name="create-a-channel-registration"></a>Kanal kaydı oluşturma

Botunuzu barındırmak için bir Azure App Service oluşturduğunuza göre, sonraki adım bir **bot kanalları kaydı**oluşturmaktır. Kanal kaydı oluşturma, doğrudan hat konuşma kanalı dahil olmak üzere bot-Framework kanallarıyla botunuzu kaydetmek için bir önkoşuldur.

> [!NOTE]
> Botların kanallardan yararlanma hakkında daha fazla bilgi edinmek istiyorsanız bkz. [bir bot 'ı kanallara bağlama](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0).

1. İlk adım kayıt için yeni bir kaynak oluşturmaktır. [Azure Portal](https://portal.azure.com) **kaynak oluştur ' a**tıklayın.
2. Arama çubuğunda, Sonuçlar görüntülendikten **sonra,** **bot kanalları kaydı**' nı seçin.
3. **Oluştur**'a tıklayın.
4. Sizden bazı bilgiler sağlamanız istenecektir:
   * **Bot adı**için **SpeechEchoBotTutorial-botregistration**yazın.
   * **Abonelik**Için **ücretsiz deneme**' yı seçin.
   * **Kaynak grubu**için **SpeechEchoBotTutorial-ResourceGroup**öğesini seçin.
   * **Konum**için **Batı ABD**' yi seçin.
     * **Fiyatlandırma katmanı**için **F0**öğesini seçin.
     * **Mesajlaşma uç noktası**için, sonuna `/api/messages` yolu eklenmiş Web uygulamanızın URL 'sini girin. Örneğin: genel olarak benzersiz uygulamanızın adı **EchoBot20190805125647**ise, mesajlaşma uç noktanız şöyle olacaktır: `https://EchoBot20190805125647.azurewebsites.net/api/messages/`.
     * **Application Insights**Için bunu **kapalı**olarak ayarlayabilirsiniz. Daha fazla bilgi için bkz. [bot Analytics](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0).
     * **Otomatik uygulama kimliği ve parola oluşturmayı**yoksay.
5. **Bot kanalları kaydına** geri gidin ve **Oluştur**' a tıklayın.

Bu noktada, Azure portal kaynak grubunuzu **SpeechEchoBotTutorial-ResourceGroup** ' u kontrol edin. Şimdi dört kaynak göstermesi gerekir:

| ADA | TÜR  | KONUMUNA |
|------|-------|----------|
| EchoBot20190805125647 | App Service | Batı ABD |
| SpeechEchoBotTutorial-AppServicePlan | App Service planı | Batı ABD |
| SpeechEchoBotTutorial-BotRegistration | Bot kanalları kaydı | Genel |
| SpeechEchoBotTutorial-konuşma | Bilişsel Hizmetler | Batı ABD |

> [!IMPORTANT]
> Batı ABD seçmiş olsanız da, bot kanalları kayıt kaynağı genel bölgeyi gösterir. Bu beklenen bir durumdur.

## <a name="register-the-direct-line-speech-channel"></a>Doğrudan hat konuşma kanalını kaydetme

Şimdi doğrudan hat konuşma kanalı ile botunuzu kaydetme zamanı. Bu kanal, yankı bot 'niz ile konuşma SDK 'Sı ile derlenen bir istemci uygulaması arasında bağlantı oluşturmak için kullanılır.

1. [Azure Portal](https://portal.azure.com) **SpeechEchoBotTutorial-botregistration** kaynağınızı bulun ve açın.
1. Sol gezinmede **Kanallar**' ı seçin.
   * **Daha fazla kanal**arayın, **doğrudan hat konuşmayı**bulun ve tıklatın.
   * Sayfadaki **doğrudan konuşmayı Yapılandır**' ı, ardından "bilişsel hizmet hesabı" olarak etiketlenen açılan menüyü genişleterek sayfada bulunan metni gözden geçirin.
   * Daha önce oluşturduğunuz konuşma kaynağını (ör. **SpeechEchoBotTutorial-Speech**) seçerek bot 'unuzu konuşma abonelik anahtarınızla ilişkilendirin.
   * **Kaydet** düğmesine tıklayın.

1. Sol gezinti bölmesinde **Ayarlar**' a tıklayın.
   * **Akış uç noktasını etkinleştir**etiketli kutuyu işaretleyin. Bu, bot ve doğrudan hat konuşma kanalı arasında Web Yuvaları üzerinde oluşturulmuş bir iletişim protokolünü etkinleştirmek için gereklidir.
   * **Kaydet** düğmesine tıklayın.

> [!TIP]
> Daha fazla bilgi edinmek istiyorsanız, bkz. [konuşmayı doğrudan konuşmaya bağlamak için bir bot bağlama](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0). Bu sayfa, ek bilgi ve bilinen sorunları içerir.

## <a name="build-the-direct-line-speech-client"></a>Doğrudan hat konuşma Istemcisini oluşturma

Bu adımda, doğrudan hat konuşma Istemcisini yapılandıracağız. İstemci, ' deki C# bir WINDOWS PRESENTATION FOUNDATION (WPF) uygulamasıdır ve doğrudan hat konuşma kanalını kullanarak bot ile iletişimi yönetmek için [konuşma SDK 'sını](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) kullanır. Özel bir istemci uygulaması yazmadan önce botunuzu etkileşmek ve test etmek için kullanın.

Doğrudan hat konuşma Istemcisinde, bot ile kurulan bağlantıyı yapılandırmanıza, metin konuşmasını görüntülemenize, bot-Framework etkinliklerini JSON biçiminde görüntülemenize ve Uyarlamalı kartlar görüntülemenize olanak tanıyan basit bir kullanıcı arabirimi vardır. Ayrıca özel anahtar sözcüklerin kullanımını destekler. Bu istemciyi, bot ile konuşmak ve bir sesli yanıt almak için kullanacaksınız.

Üzerinde geçiş yapmadan önce, mikrofonunuzun ve hoparlörlerinizin etkinleştirildiğinden ve çalıştığından emin olun.

1. [Doğrudan hat konuşma Istemcisinin](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md)GitHub deposuna gidin.
2. Depoyu kopyalamak, projeyi derlemek, istemcisini yapılandırmak ve istemcisini başlatmak için belirtilen yönergeleri izleyin.
3. **Yeniden bağlan** ' a tıklayın ve **mikrofonunuza konuşmaya başlamak Için mikrofon düğmesine basın veya yazarak**emin olun.
4. Şimdi test edin. Mikrofon düğmesine tıklayın ve Ingilizce 'de birkaç sözcükten konuşun. Konuşma sırasında tanınan metin görüntülenir. Konuşmayı tamamladığınızda, bot kendi sesinden yanıt vererek "Echo" ve ardından tanınan kelimeleri söyleyerek.
5. Ayrıca, bot ile iletişim kurmak için metin de kullanabilirsiniz. Metni alt çubuğa yazmanız yeterlidir. 

### <a name="troubleshooting-errors-in-direct-line-speech-client"></a>Doğrudan hat konuşma Istemcisinde sorun giderme hataları

Ana uygulama pencerenizde bir hata iletisi alırsanız, hatayı belirlemek ve gidermek için bu tabloyu kullanın:

| Hata | Ne yapmalısınız? |
|-------|----------------------|
|Hata AuthenticationFailure: WebSocket yükseltmesi bir kimlik doğrulama hatasıyla başarısız oldu (401). Doğru abonelik anahtarını (veya yetkilendirme belirtecini) ve bölge adını denetleyin| Uygulamanın Ayarlar sayfasında, konuşma abonelik anahtarını ve bölgesini doğru şekilde girdiğinizden emin olun.<br>Konuşma anahtarınızın ve anahtar bölgenizin doğru girildiğinden emin olun. |
|ConnectionFailure hatası: bağlantı uzak ana bilgisayar tarafından kapatıldı. Hata kodu: 1011. Hata ayrıntıları: İleti göndermeden önce bot 'a bağlanamıyoruz | ["Akış uç noktasını etkinleştir"](#register-the-direct-line-speech-channel) kutusunu ve/veya [ **Web yuvalarını** ](#enable-web-sockets) açık olarak iade ettiğinizden emin olun.<br>Azure App Service çalıştığından emin olun. Varsa App Service yeniden başlatmayı deneyin.|
|ConnectionFailure hatası: bağlantı uzak ana bilgisayar tarafından kapatıldı. Hata kodu: 1011. Hata ayrıntıları: yanıt durum kodu başarıyı göstermiyor: 500 (ınternalservererror)| Bot, çıkış etkinliği [konuş](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) alanında bir sinir sesi belirtti, ancak konuşma abonelik anahtarınızla ilişkili Azure bölgesi sinir seslerini desteklemez. Bkz. [Standart ve sinir sesleri](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|ConnectionFailure hatası: bağlantı uzak ana bilgisayar tarafından kapatıldı. Hata kodu: 1000. Hata ayrıntıları: maksimum Web yuvası bağlantısı boşta kalma süresi aşıldı (> 300000 MS)| Bu, kanalla bir bağlantı açık bırakıldığında ve beş dakikadan uzun süre boyunca etkin olmadığında beklenen bir hatadır. |

Sorununuz tabloda giderilmemişse, bkz. [sesli Yardımcılar: sık sorulan sorular](faq-voice-assistants.md).

### <a name="view-bot-activities"></a>Bot etkinliklerini görüntüle

Her bot **etkinlik** iletilerini gönderir ve alır. Doğrudan hat konuşma Istemcisinin **etkinlik günlüğü** penceresinde, istemcinin bot 'tan aldığı her bir etkinlikle birlikte zaman damgalınılan Günlükler görürsünüz. Ayrıca [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) yöntemi kullanılarak istemcinin bot 'a gönderdiği etkinlikleri görebilirsiniz. Bir günlük öğesini seçtiğinizde, ilişkili etkinliğin ayrıntılarını JSON olarak gösterir.

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
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

JSON çıktısında döndürülen şeyler hakkında daha fazla bilgi için bkz. [etkinteki alanlar](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Bu öğreticinin amacı doğrultusunda [metin](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) ve [konuş](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) alanlarına odaklanabilirsiniz.

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Konuşma SDK 'Sı çağrıları için istemci kaynak kodunu görüntüleme

Doğrudan hat konuşma Istemcisi, konuşma SDK 'sını içeren [Microsoft. Biliveservices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)NuGet paketini kullanır. Örnek kodu incelemeye başlamak için iyi bir yer vardır: dosya [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs)içinde, bu ıkı konuşma SDK nesnesini oluşturan InitSpeechConnector () yöntemi.
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) -yapılandırma ayarları için (örneğin, konuşma abonelik anahtarı, anahtar bölgesi)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) , tanınan konuşma ve bot yanıtlarını işlemek için kanal bağlantısını ve istemci abonelik olaylarını yönetmek için.

## <a name="add-custom-keyword-activation"></a>Özel anahtar sözcük etkinleştirme Ekle

Konuşma SDK 'Sı özel anahtar sözcük etkinleştirmesini destekler. Microsoft 'un Yardımcısı için "Hey Cortana" a benzer şekilde, tercih ettiğiniz bir anahtar sözcüğü sürekli olarak dinleyebileceğiniz bir uygulama yazabilirsiniz. Bir anahtar sözcüğünün tek sözcük veya çok sözcüklü bir tümcecik olabileceğini aklınızda bulundurun.

> [!NOTE]
> Terim *anahtar sözcüğü* genellikle *uyanma sözcüğünün*teriminin yerine kullanılır ve her ikisini de Microsoft belgelerinde kullanabilirsiniz.

Anahtar sözcük algılama, istemci uygulamasında yapılır. Anahtar sözcük kullanılıyorsa, ses yalnızca anahtar sözcük algılanırsa doğrudan hat konuşma kanalına akıtıdır. Doğrudan hat konuşma kanalı, seçtiğiniz anahtar sözcüğünün ses akışının başlangıcında olduğunu doğrulamak için bulutta daha karmaşık işlem yapan *anahtar sözcük doğrulaması (KWV)* adlı bir bileşeni içerir. Anahtar sözcük doğrulaması başarılı olursa kanal, bot ile iletişim kurar.

Anahtar sözcük modeli oluşturmak için bu adımları izleyin, doğrudan hat konuşma Istemcisini bu modeli kullanacak şekilde yapılandırın ve son olarak, bunu bot 'unuzla test edin.

1. [Konuşma hizmetini kullanarak özel bir anahtar sözcük oluşturmak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)için bu yönergeleri izleyin.
2. Önceki adımda indirdiğiniz model dosyasını sıkıştırmasını açın. Anahtar kelime için adlandırılmalıdır. `kws.table`adlı bir dosya arıyorsunuz.
3. Doğrudan hat konuşma istemcisinde, **Ayarlar** menüsünü bulun (sağ üst köşedeki dişli simgesine bakın). **Model dosya yolunu** bulun ve adım 2 ' den `kws.table` dosyası için tam yol adını girin.
4. **Etkin**etiketli kutuyu denetlediğinizden emin olun. Bu iletiyi onay kutusunun yanında görmeniz gerekir: "sonraki bağlantıdan sonra anahtar sözcüğü dinler". Yanlış dosya veya geçersiz bir yol sağladıysanız bir hata iletisi görmeniz gerekir.
5. Konuşma **abonelik anahtarınızı**, **abonelik anahtarı bölgenizi**girip **Tamam** ' a tıklayarak **Ayarlar** menüsünü kapatın.
6. **Yeniden bağlan**' a tıklayın. Şunu okuyan bir ileti görmelisiniz: "yeni konuşma başlatıldı-yazın, mikrofon düğmesine basın veya" anahtar sözcüğünü söyleyin ". Uygulama artık sürekli dinliyor.
7. Anahtar kelimesiyle başlayan herhangi bir tümceciği konuşun. Örneğin: " **{anahtar kelime}** , ne zaman var?". Anahtar sözcüğü alındıktan sonra duraklamanız gerekmez. İşiniz bittiğinde iki şey meydana gelir:
   * Bağlı olduğunuz kadar bir komut dosyası görürsünüz
   * Kısa süre sonra, bot 'un yanıtını duydunuz
8. Bot 'unuzun desteklediği üç giriş türüyle denemeler yapmaya devam edin:
   * Alt çubukta yazılan metin
   * Mikrofon simgesine ve konuşmaya basma
   * Anahtar kelimesiyle başlayan bir tümceciği belirten

### <a name="view-the-source-code-that-enables-keyword"></a>Anahtar sözcüğü sağlayan kaynak kodunu görüntüleme

Doğrudan hat konuşma Istemci kaynak kodunda, anahtar sözcük algılamayı etkinleştirmek için kullanılan kodu gözden geçirmek için şu dosyalara göz atın:

1. [`DLSpeechClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/Models.cs) , BIR konuşma SDK yöntemine [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)bir çağrı içerir. Bu, bir dosyanın diskteki yerel bir dosyadan örneğini oluşturmak için kullanılır.
1. [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs) , sürekli anahtar sözcük algılamayı ETKINLEŞTIREN konuşma SDK yöntemine [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)bir çağrı içerir.

## <a name="optional-change-the-language-and-redeploy-your-bot"></a>Seçim Dili değiştirme ve bot uygulamanızı yeniden dağıtma

Oluşturduğunuz bot, Ingilizce 'yi dinleyecektir ve yanıt vermelidir. Ancak, Ingilizce 'yi kullanma sınırlı değildir. Bu bölümde, bot 'unuzun dinlemesi ve yanıt verdiği dilin nasıl değiştirileceğini ve bot 'ın yeniden dağıtılması hakkında bilgi edineceksiniz.

### <a name="change-the-language"></a>Dili değiştirme

1. `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`açarak başlayalım.
2. Ardından SSML 'yi bulun. `<speak></speak>` etiketlere yerleştirildiğinden, kolayca bulabilirsiniz.
3. SSML dizesinde `<voice name>` etiketini bulun, `<voice name='de-DE-Stefan-Apollo'>`ile değiştirin ve kaydedin. Bu biçimli dize, metin okuma hizmetine, Almanca için en iyi duruma getirilmiş ses `de-DE-Stefan-Apollo`kullanarak sentezlenmiş bir konuşma yanıtı döndürmesini söyler.

>[!NOTE]
> Almanca ile sınırlı değildir ve [konuşma hizmetinden](language-support.md#text-to-speech)kullanılabilir sesler listesinden seçim yapabilirsiniz.

### <a name="redeploy-your-bot"></a>Botunuzu yeniden dağıtın

Şimdi de gerekli bir değişikliği yapmış olduğunuza göre, bir sonraki adım, Azure App Service yeniden yayımlamanız ve denemek için:

1. Visual Studio 'da çözümünüzü derleyin ve tüm derleme hatalarını düzeltin.
2. Çözüm Gezgini penceresinde, **yankı bot** projesine sağ tıklayın ve **Yayımla**' yı seçin.
3. Önceki dağıtım yapılandırmanız varsayılan olarak zaten yüklenmiştir. **EchoBot20190805125647-Web dağıtımı**' nin yanında **Yayımla** ' ya tıklamanız yeterlidir.
4. **Yayımla başarılı** Iletisi Visual Studio çıktı penceresinde görünür ve bir Web sayfası, "bot 'niz hazırlanıyor!" iletisiyle başlatılır.
5. Doğrudan hat konuşma Istemci uygulamasını açın, Ayarlar düğmesine (sağ üst dişli simgesine) tıklayın ve dil alanına `de-de` girin. Bu, varsayılan `en-us`geçersiz kılmak için konuşulan dili tanınmak üzere ayarlar.
6. Yeni dağıtılan bot ile yeniden bağlanmak için [doğrudan hat konuşma Istemcisini oluşturma](#build-the-direct-line-speech-client) bölümünde yer alan yönergeleri izleyin, yeni dilde konuşun ve yeni sesle bu dilde her bir bot yanıtı dinleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide dağıtılan Echo-bot 'ı kullanmaya devam edemeyecekseniz, **SpeechEchoBotTutorial-ResourceGroup**Azure kaynak grubunu silerek bu uygulamayı ve onunla Ilişkili tüm Azure kaynaklarını kaldırabilirsiniz.

1. [Azure Portal](https://portal.azure.com), sol gezinti bölmesinde **kaynak grupları** ' na tıklayın.
2. Şu ada sahip kaynak grubunu bulun: **SpeechEchoBotTutorial-ResourceGroup**. Üç noktaya (...) tıklayın.
3. **Kaynak grubunu sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Konuşma SDK 'Sı ile kendi istemci uygulamanızı derleme](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>Ayrıca bkz.

* Bot yanıt süresi iyileştirmesini görmek için [size yakın bir Azure bölgesine](https://azure.microsoft.com/global-infrastructure/locations/) dağıtım
* [Yüksek kaliteli sınır TTS seslerini destekleyen bir Azure bölgesine](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices) dağıtım
* Doğrudan hat konuşma kanalı ile ilişkili fiyatlandırma:
  * [Bot hizmeti fiyatlandırması](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Konuşma Hizmetleri](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Kendi seslendirmeyi kullanan bot 'ı derleme ve dağıtma:
  * Bir [bot Framework bot](https://dev.botframework.com/)oluşturun. [Doğrudan hat konuşma kanalına](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) kaydolun ve [botunuzu ses için özelleştirin](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * Mevcut bir [bot Framework çözümlerini](https://microsoft.github.io/botframework-solutions/index)keşfet: sanal bir [yardımcı](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) oluşturun ve [konuşmayı doğrudan konuşmaya yönlendirmek için genişletin](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
