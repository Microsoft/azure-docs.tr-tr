---
title: Conversation Learner bot 'ı dağıtma-Microsoft bilişsel hizmetler | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner bot 'ı dağıtmayı öğrenin.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 05fd83506aac26df33f18bec83dcadac8dee2d90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705280"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Conversation Learner bot nasıl dağıtılır

Bu belgede, yerel olarak veya Azure 'a Conversation Learner bir bot 'ın nasıl dağıtılacağı açıklanmaktadır.

## <a name="prerequisite-determine-the-model-id"></a>Önkoşul: model KIMLIĞINI belirleme 

Conversation Learner UI dışında bir bot çalıştırmak için, bot 'ın kullanacağı Conversation Learner model KIMLIĞINI (örneğin, Conversation Learner buluttaki makine öğrenimi modelinin KIMLIĞINI) ayarlamanız gerekir.  (Buna karşılık, Conversation Learner Kullanıcı arabirimi aracılığıyla bot çalıştırırken, Kullanıcı arabirimi hangi model KIMLIĞINI seçer.).  

Model KIMLIĞINI elde etmek için şu adımları uygulayın:

1. Bot ' u ve Conversation Learner Kullanıcı arabirimini başlatın.  Tam yönergeler için hızlı başlangıç kılavuzuna bakın; özetlemek için:

    Tek bir komut penceresinde:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    Oluşturulduğu komut penceresinde

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Tarayıcıyı açmak için`http://localhost:5050` 

3. KIMLIĞI almak istediğiniz Conversation Learner modeline tıklayın

4. Sol taraftaki Gezinti çubuğunda "Ayarlar" a tıklayın.

5. "Model KIMLIĞI" GUID 'SI sayfanın en üstüne yakın bir şekilde görüntülenir.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Seçenek 1: Yerel olarak çalıştırmak için Conversation Learner bot dağıtma

Bu, yerel makinenize bir bot dağıtır ve bot Framework öykünücüsünü kullanarak nasıl erişekullanabileceğinizi gösterir.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Conversation Learner Kullanıcı arabirimi dışında erişim için bot 'ı yapılandırma

Bir bot 'ı yerel olarak çalıştırırken, bot 'ın `.env` dosyasına uygulama kimliği ekleyin:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Ardından, bot uygulamanızı başlatın:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Bot artık yerel olarak çalışıyor.  Bot Framework öykünücüsü ile erişebilirsiniz.

### <a name="download-and-install-the-emulator"></a>Öykünücüyü indirme ve yükleme

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Öykünücüyü bot 'a bağlama

1. Öykünücüsünün sol üst kısmında, "uç nokta URL 'nizi girin" kutusuna girin `http://127.0.0.1:3978/api/messages`.  Diğer alanları boş bırakın ve "Bağlan" a tıklayın.

2. Artık bot 'unuza söyleşolursunuz.

## <a name="option-2-deploy-to-azure"></a>Seçenek 2: Azure’a dağıtma

Conversation Learner bot uygulamanızı, diğer tüm botların yayımlanacağı şekilde yayımlayın. Yüksek düzeyde, kodunuzu barındırılan bir Web sitesine yükler, uygun yapılandırma değerlerini ayarlar ve ardından bot 'ı çeşitli kanallarla kaydedersiniz. Bu videoda, Azure bot hizmeti kullanılarak botunuzu yayımlamayı gösteren ayrıntılı yönergeler bulunmaktadır.

Bot dağıtıldıktan ve çalışmaya başladıktan sonra, Facebook, takımlar, Skype vb. gibi farklı kanallarla bağlantı sağlayabilirsiniz. Azure bot kanal kaydını kullanma. Bu işlem hakkındaki belgeler için bkz.: https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

Aşağıda, Azure 'a Conversation Learner bot dağıtmaya yönelik adım adım yönergeler verilmiştir.  Bu yönergelerde, bot kaynağınızın Azure DevOps Services, GitHub, BitBucket veya OneDrive gibi bulut tabanlı bir kaynaktan kullanılabileceğini ve botunuzu sürekli dağıtım için yapılandıracağına dair varsayılmaktadır.

1. Azure portal şurada oturum açın: https://portal.azure.com

2. Yeni bir "Web uygulaması bot" kaynağı oluşturma 

    1. Bot 'a bir ad verin
    2. "Bot şablonu" na tıklayın, "Node. js" öğesini seçin, "temel" i seçin ve ardından "Seç" düğmesine tıklayın
    3. Web uygulaması bot 'u oluşturmak için "Oluştur" düğmesine tıklayın.
    4. Web uygulaması bot kaynağının oluşturulmasını bekleyin.

3. Azure portal, az önce oluşturduğunuz Web App bot kaynağını düzenleyin.

   1. Soldaki "uygulama ayarları" gezinti öğesini tıklatın
   1. Aşağı kaydırarak "uygulama ayarları" bölümüne gidin
   2. Bu ayarları ekleyin:

       Ortam değişkeni | value
       --- | --- 
       CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
       CONVERSATION_LEARNER_MODEL_ID      | Model için "Ayarlar" altındaki Conversation Learner kullanıcı arabiriminden elde edilen uygulama kimliği GUID >
       LUIS_AUTHORING_KEY               | Bu model için LUSıS yazma anahtarı
       LUIS_SUBSCRIPTION_KEY            | Gerekli değildir, ancak yazma kotayı kullanmaktan kaçınmak için yayımlanmış botların kullanılması önerilir.
    
   4. Sayfanın üst kısmındaki "Kaydet" e tıklayın
   5. Sol taraftaki "derleme" gezinti öğesini aç
   6. "Sürekli dağıtımı Yapılandır" düğmesine tıklayın 
   7. Dağıtımlar altında "Kurulum" simgesine tıklayın
   8. "Gerekli ayarlar" a tıklayın
   9. Bot kodunuzun kullanılabildiği kaynağı seçin ve kaynağı yapılandırın.
