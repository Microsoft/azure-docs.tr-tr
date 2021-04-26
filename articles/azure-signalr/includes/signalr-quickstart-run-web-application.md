---
title: include dosyası
description: include dosyası
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 2af9c1dfd13d3aeafa7cf1ac76537117ecc15aff
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "84317649"
---
## <a name="run-the-web-application"></a>Web uygulamasını çalıştırma

1. İstemci testinizi basitleştirmek için tarayıcınızı örnek tek sayfalı web uygulamamızda açın [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/) . 

    > [!NOTE]
    > HTML dosyasının kaynağı [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html)konumunda bulunur. HTML 'yi kendiniz barındırmak istiyorsanız, lütfen */docs/demo/chat-v2* dizininde [http-Server](https://www.npmjs.com/package/http-server) gibi bir yerel http sunucusu başlatın. local.settings.js, örneğe benzer şekilde, kaynak `CORS` ayarına eklendiğinden  emin olun.
    > 
    > ```javascript
    > "Host": {
    >  "LocalHttpPort": 7071,
    >  "CORS": "http://localhost:8080,https://azure-samples.github.io",
    >  "CORSCredentials": true
    > }
    >
    > ```

1. İşlev uygulamasının temel URL'si istendiğinde `http://localhost:7071` URL’sini girin.

1. İstenildiğinde bir kullanıcı adı girin.

1. Web uygulaması, Azure SignalR Hizmetine bağlanmak amacıyla bağlantı bilgilerini almak için işlev uygulamasındaki *GetSignalRInfo* işlevini çağırır. Bağlantı tamamlandığında sohbet iletisi giriş kutusu görünür.

1. Bir ileti yazın ve Enter tuşuna basın. Uygulama, iletiyi Azure işlev uygulamasındaki *SendMessage* işlevine gönderir, bu da bağlı tüm istemcilere iletiyi yaymak için SignalR çıkış bağlamasını kullanır. Her şey düzgün çalışıyorsa iletinin uygulamada görünmesi gerekir.

    ![Uygulamayı çalıştırma](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Farklı bir tarayıcı penceresinde web uygulamasının başka bir örneğini açın. Gönderilen tüm iletilerin uygulamanın tüm örneklerinde göründüğünü görürsünüz.
