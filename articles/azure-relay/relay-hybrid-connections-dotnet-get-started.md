---
title: .NET WebSockets Azure Relay Karma Bağlantılar-
description: Azure Relay Karma Bağlantılar WebSockets için bir C# konsol uygulaması yazın.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: bf22b8b11dc386644803b43ee4e3a51d04b70419
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90527437"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>.NET’te Geçiş Karma Bağlantıları Web Yuvaları ile çalışmaya başlama
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Bu hızlı başlangıçta, Azure Relay içinde Karma Bağlantılar WebSockets kullanarak ileti gönderen ve alan .NET gönderici ve alıcı uygulamaları oluşturacaksınız. Genel olarak Azure Relay hakkında bilgi edinmek için bkz. [Azure Relay](relay-what-is-it.md). 

Bu hızlı başlangıçta aşağıdaki adımları uygulayın:

1. Azure portalını kullanarak Geçiş ad alanı oluşturma.
2. Azure portalını kullanarak o ad alanında karma bağlantı oluşturma.
3. İleti almak için bir sunucu (dinleyici) konsol uygulaması yazma.
4. İleti göndermek için bir istemci (gönderen) konsol uygulaması yazma.
5. Uygulamaları çalıştırın. 

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdaki önkoşulları karşılamanız gerekir:

* [Visual Studio 2015 veya üzeri](https://www.visualstudio.com). Bu öğreticideki örneklerde Visual Studio 2017 kullanılmaktadır.
* Azure aboneliği. Bir tane yoksa, başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .

## <a name="create-a-namespace"></a>Ad alanı oluşturma
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Karma bağlantı oluşturma
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Sunucu uygulaması (dinleyici) oluşturma
Geçiş hizmetinden ileti dinleyip almak için Visual Studio kullanarak bir C# konsol uygulaması yazın.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>İstemci uygulaması (gönderici) oluşturma
Geçiş hizmetine ileti göndermek Visual Studio kullanarak bir C# konsol uygulaması yazın.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Uygulamaları çalıştırma
1. Sunucu uygulamasını çalıştırın.
2. İstemci uygulamasını çalıştırın ve metin girin.
3. Sunucu uygulama konsolunun istemci uygulamasına girilen metni görüntülediğinden emin olun.

    ![Konsol pencereleri, hem sunucu hem de istemci uygulamalarını test ediyor.](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Tebrikler, tamamlanmış bir Karma Bağlantılar uygulaması oluşturdunuz!

## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta, ileti göndermek ve almak için WebSockets kullanan .NET istemci ve sunucu uygulamaları oluşturdunuz. Azure Relay Karma Bağlantılar özelliği, ileti göndermek ve almak için HTTP kullanmayı da destekler. Azure Relay Karma Bağlantılar ile HTTP 'yi nasıl kullanacağınızı öğrenmek için bkz. [http hızlı](relay-hybrid-connections-http-requests-dotnet-get-started.md)başlangıcı.

Bu hızlı başlangıçta, istemci ve sunucu uygulamaları oluşturmak için .NET Framework kullandınız. Node.js kullanarak istemci ve sunucu uygulamalarının nasıl yazılacağını öğrenmek için [Node.js WebSockets hızlı başlangıç](relay-hybrid-connections-node-get-started.md) veya [Node.js http hızlı başlangıç](relay-hybrid-connections-http-requests-dotnet-get-started.md)başlığına bakın.

