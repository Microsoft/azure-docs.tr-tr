---
title: Node. js kullanarak Conversation Learner modeli oluşturma-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Node. js kullanarak Conversation Learner modeli oluşturmayı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7ab32fb421a2c0db72652d1bbf12d312bffd5d1e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706545"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Node. js kullanarak Conversation Learner modeli oluşturma

Conversation Learner, botların oluşturulmasına ilişkin karmaşıklığı azaltır. Bir karma geliştirme iş akışını, el ile yazılmış kod ve makine öğrenimine izin vererek botları yazmak için gereken kod miktarını azaltır. Modelinizin, kullanıcının oturum açıp açmamakta olup olmadığını denetleme veya mağaza envanterini denetlemek için bir API isteği oluşturma gibi belirli sabit bölümleri yine de kodlanmalıdır. Ancak, durum ve eylem seçiminde diğer değişiklikler, etki alanı uzmanı veya geliştiricisi tarafından verilen örnek iletişim kutularından öğrenilebilir.

## <a name="invitation-required"></a>Davetiye gerekiyor

*Proje Conversation Learner erişmek için davetiye gereklidir.*

Project Conversation Learner, bot 'a eklediğiniz bir SDK ve makine öğrenimine SDK 'nin eriştiği bir bulut hizmeti içerir.  Mevcut olduğunda, proje konuşması Taaner bulut hizmetine erişim için bir davet gerekir.  Henüz davet edilmediyse [davetiye isteyin](https://aka.ms/conversation-learner-request-invite).  Davet almadıysanız, bulut API 'sine erişemeyecektir.

## <a name="prerequisites"></a>Önkoşullar

- Node 8.5.0 veya üzeri ve NPM 5.3.0 veya üzeri. Buradan ' [https://nodejs.org](https://nodejs.org)den ' a.
  
- LUSıS yazma anahtarı:

  1. Oturum açın [https://www.luis.ai](https://www.luis.ai).

  2. Sağ üst köşedeki ad ' a tıklayın ve ardından "Ayarlar" a tıklayın.

  3. Yazma anahtarı sonuçtaki sayfada gösterilir

  (LUSıS yazma anahtarınız 2 rol sunar.  İlk olarak, Conversation Learner yazma anahtarınız olarak görev yapar.  İkincisi, Conversation Learner varlık ayıklama için LUO kullanır; LUSıS yazma anahtarı, sizin adınıza LUSıS modelleri oluşturmak için kullanılır)

- Google Chrome Web tarayıcısı. Buradan ' [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html)den ' a.

- Git. Buradan ' [https://git-scm.com/downloads](https://git-scm.com/downloads)den ' a.

- VSCode. Buradan ' [https://code.visualstudio.com/](https://code.visualstudio.com/)den ' a. Not Bu gerekli değildir, bu önerilmez.

## <a name="quick-start"></a>Hızlı başlangıç 

1. Install ve Build:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Sırasında `npm install`, meydana gelirse bu hatayı yoksayabilirsiniz:`gyp ERR! stack Error: Can't find Python executable`

2. Yapılandır:

   `.env` Dizininde`cl-bot-01`adlı bir dosya oluşturun.  Dosyanın içeriği şu olmalıdır:

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Bot 'ı Başlat:

    ```
    npm start
    ```

    Bu, içinde `cl-bot-01/src/app.ts`genel boş bot 'ı çalıştırır.

3. Tarayıcıyı açmak için`http://localhost:3978`

Artık Conversation Learner kullanıyorsunuz ve bir Conversation Learner modeli oluşturabilir ve öğretebiliriz.  

> [!NOTE]
> Başlatmada, proje Conversation Learner davet tarafından kullanılabilir.  Bir http`403` hatası gösteriyorsa bu, hesabınızın davet edilmediği anlamına gelir. `http://localhost:3978/ui`  Lütfen [bir davetiye isteyin](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Bu gibi öğreticiler, tanıtımlar ve geçiş

Yukarıdaki yönergeler, genel boş bot 'ı başlattı.  Bunun yerine bir öğretici veya tanıtım bot 'ı çalıştırmak için:

1. Conversation Learner Web Kullanıcı arabirimi açıksa, içindeki modeller `http://localhost:3978/ui/home`listesine geri dönün.
    
2. Başka bir bot çalışıyorsa (veya `npm start` `npm run demo-pizza`gibi), bunu durdurun.  UI işlemini durdurmanız veya Web tarayıcısını kapatmanız gerekmez.

3. Komut satırından bir demo bot çalıştırın (yukarıdaki 2. adım).  Tanıtımlar şunlardır:

   ```bash
   npm run tutorial-general
   npm run tutorial-entity-detection
   npm run tutorial-session-callbacks
   npm run tutorial-api-calls
   npm run tutorial-hybrid
   npm run demo-password
   npm run demo-pizza
   npm run demo-storage
   ```

4. Henüz açmadıysanız, ' yi yükleyerek `http://localhost:3978/ui/home`Conversation Learner Web Kullanıcı arabirimine geçiş yapın. 

5. "Öğreticileri Içeri aktar" düğmesine tıklayın ve başlattığınız tanıtım öğesine karşılık gelen Conversation Learner Kullanıcı arabiriminde demo modeli seçin.

Tanıtımlar için kaynak dosyalar`cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Arka uç kodunu içeren bir bot oluştur

1. Conversation Learner Web Kullanıcı arabirimi açıksa, içindeki modeller `http://localhost:3978/ui/home`listesine geri dönün.
    
2. Bir bot çalışıyorsa (gibi `npm run demo-pizza`), bunu durdurun.  UI işlemini durdurmanız veya Web tarayıcısını kapatmanız gerekmez.

3. İsterseniz kodu ' de `cl-bot-01/src/app.ts`düzenleyin.

4. Bot 'ı yeniden derleyin ve yeniden başlatın:

    ```bash    
    npm run build
    npm start
    ```

5. Henüz açmadıysanız, ' yi yükleyerek `http://localhost:3978/ui/home`Conversation Learner Web Kullanıcı arabirimine geçiş yapın. 

6. Kullanıcı arabiriminde yeni bir Conversation Learner modeli oluşturun ve öğretme başlatın.

7. İçinde `cl-bot-01/src/app.ts`kod değişikliği yapmak için adım 2 ' den başlayarak yukarıdaki adımları tekrarlayın.

## <a name="vscode"></a>VSCode

VSCode 'da, her demo için ve içindeki `cl-bot-01/src/app.ts`"boş bot" için çalışma yapılandırması vardır.  `cl-bot-01` Klasörü vscode 'da açın.

## <a name="advanced-configuration"></a>Gelişmiş yapılandırma

Şablon `.env.example` dosyası, örnekleri yapılandırmak için ayarlayabileceğiniz ortam değişkenlerini gösterir.

Bu bağlantı noktalarını, projenin köküne bir `.env` dosya ekleyerek makinenizde çalışan diğer hizmetler arasındaki çakışmaları önlemek için ayarlayabilirsiniz:

```bash
cp .env.example .env
```

Bu, bot uygulamanızı yerel olarak çalıştırmanızı sağlayan Standart yapılandırmayı kullanır ve Conversation Learner kullanmaya başlayabilirsiniz.  (Daha sonra, bot ' ı bot Framework 'e dağıtmak için bu dosyada yapılan bazı düzenlemeler gerekecektir.)

## <a name="support"></a>Destek

- "Microsoft bilişsel" ile [Stack Overflow](https://stackoverflow.com) ilgili soruları etiketleme
- [Kullanıcı ses sayfamızda](https://aka.ms/conversation-learner-uservoice) bir özellik isteyin
- [GitHub](https://github.com/Microsoft/ConversationLearner-Samples) deponuzda bir sorun açın

## <a name="contributing"></a>Bulunan

Bu proje [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Microsoft Açık Kaynak Kullanım Kuralları) belgesinde listelenen kurallara uygundur. Daha fazla bilgi için [Kullanım Kuralları SSS](https://opensource.microsoft.com/codeofconduct/faq/) sayfasına bakın. Başka sorularınız ya da yorumlarınız varsa bunları [opencode@microsoft.com](mailto:opencode@microsoft.com) adresine gönderebilirsiniz.

## <a name="source-repositories"></a>Kaynak depoları

- [konuşma-örnekler](https://github.com/Microsoft/ConversationLearner-Samples)
- [konuşma öğrenici-SDK](https://github.com/Microsoft/ConversationLearner-SDK)
- [konuşma ve modeller](https://github.com/Microsoft/ConversationLearner-Models)
- [konuşma, Kullanıcı arabirimi](https://github.com/Microsoft/ConversationLearner-UI)
- [konuşma ve Webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Merhaba Dünya](./tutorials/01-hello-world.md)
