---
title: Grup sohbeti Hero örneği
titleSuffix: An Azure Communication Services sample overview
description: Azure Iletişim Hizmetleri ile sohbet Hero örneğine genel bakış geliştiricilerin, örneğin iç işleyişi hakkında daha fazla bilgi edinmek ve nasıl değiştirileceğini öğrenmek için.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5547e49cd7ea7f6c0cc97b5559564b64e0deda7c
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168850"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>Grup sohbeti Hero örneği ile çalışmaya başlama

[!INCLUDE [Public Preview Notice](../includes/public-preview-include-chat.md)]

> [!IMPORTANT]
> [Bu örnek **GitHub ' da** kullanılabilir.](https://github.com/Azure-Samples/communication-services-web-chat-hero)


Azure Iletişim Hizmetleri **grubu sohbeti Hero örneği** , Iletişim Hizmetleri sohbet web SDK 'sının bir grup çağırma deneyimi oluşturmak için nasıl kullanılabileceğini gösterir.

Bu örnek hızlı başlangıçta, örneği yerel makinenizde çalıştırmadan önce örneğin nasıl çalıştığını öğreneceksiniz. Daha sonra Azure Iletişim Hizmetleri kaynaklarınızı kullanarak örneği Azure 'a dağıtırsınız.


## <a name="overview"></a>Genel Bakış

Örnekte hem istemci tarafı uygulaması hem de sunucu tarafı uygulaması vardır. **İstemci tarafı uygulama** , Microsoft 'un akıcı Kullanıcı arabirimi çerçevesini kullanan bir yanıt veren/Redux Web uygulamasıdır. Bu uygulama, istemci tarafı uygulamanın Azure 'a bağlanmasına yardımcı olan bir ASP.NET Core **sunucu tarafı uygulamasına** istek gönderir.

Örnek şöyle görünür:

:::image type="content" source="./media/chat/landing-page.png" alt-text="Örnek uygulamanın giriş sayfasını gösteren ekran görüntüsü.":::

"Sohbet Başlat" düğmesine bastığınızda Web uygulaması, sunucu tarafı uygulamadan bir Kullanıcı erişim belirteci getirir. Bu belirteç daha sonra istemci uygulamasını Azure Communication Services 'a bağlamak için kullanılır. Belirteç alındıktan sonra, sohbet 'te sizi temsil edecek adınızı ve Emoji belirtmeniz istenecektir.

:::image type="content" source="./media/chat/pre-chat.png" alt-text="Uygulamanın sohbet öncesi ekranını gösteren ekran görüntüsü.":::

Görünen adınızı ve Emoji yapılandırıldıktan sonra sohbet oturumuna katılabilir. Artık çekirdek sohbet deneyiminin yaşadığı ana sohbet tuvali görüntülenir.

:::image type="content" source="./media/chat/main-app.png" alt-text="Örnek uygulamanın ana ekranını gösteren ekran görüntüsü.":::

Ana sohbet ekranının bileşenleri:

- **Ana sohbet alanı**: Bu, kullanıcıların ileti gönderebildiği ve aldığı temel sohbet deneyimidir. İleti göndermek için, giriş alanını kullanabilir ve ENTER tuşuna basabilir (ya da Gönder düğmesini kullanabilirsiniz). Alınan sohbet iletileri, doğru ad ve Emoji ile gönderici tarafından kategorilere ayrılmıştır. Sohbet alanında iki tür bildirim görürsünüz: 1) bir Kullanıcı yazarken ve 2) iletiler için gönderme ve okuma bildirimleri yazma.
- **Üst bilgi**: Bu, kullanıcının sohbet iş parçacığının başlığını ve katılımcı ve ayarlar yan çubuklarının konumunu değiştirmeye yönelik denetimleri ve sohbet oturumundan çıkmak için bir Bırak düğmesini göremeyeceği yerdir.
- **Yan çubuk**: burada katılımcılar ve ayar bilgileri, üstbilgideki denetimler kullanılarak açıldığında gösterilir. Katılımcılar yan çubuğu, sohbetteki katılımcılar listesini ve katılımcıları sohbet oturumuna davet etmek için bir bağlantı içerir. Ayarlar yan çubuğu, sohbet iş parçacığı başlığını yapılandırmanıza olanak tanır.

Aşağıda, önkoşulları ve örneği ayarlama adımlarını hakkında daha fazla bilgi bulacaksınız.

## <a name="prerequisites"></a>Önkoşullar

- Etkin abonelikle bir Azure hesabı oluşturun. Ayrıntılar için bkz. [ücretsiz hesap oluşturma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js (8.11.2 ve üzeri)](https://nodejs.org/en/download/)
- [Visual Studio (2017 ve üstü)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Visual Studio örneğinize karşılık gelen sürümü yüklediğinizden emin olun, 32 vs 64 bit)
- Bir Azure Iletişim Hizmetleri kaynağı oluşturun. Ayrıntılar için bkz. [Azure Iletişim kaynağı oluşturma](../quickstarts/create-communication-resource.md). Bu hızlı başlangıç için kaynak **Bağlantı dizenizi** kaydetmeniz gerekir.

## <a name="locally-deploying-the-service--client-app"></a>Hizmet & istemci uygulamasını yerel olarak dağıtma

Tek iş parçacıklı sohbet örneği temelde istemci ve sunucu uygulaması olmak üzere iki "uygulama" dır.

Visual Studio 'Yu sohbet. csproj üzerinde açın ve hata ayıklama modunda çalıştırın, bu işlem sohbet ön uç hizmetini başlatır. Sunucu uygulaması tarayıcıdan ziyaret edildiğinde, trafiği yerel olarak dağıtılan sohbet ön uç hizmetine yönlendiren şekilde yeniden yönlendirecektir.

Birden çok kullanıcı sohbetini benzetirmek için sohbet URL 'siyle birden fazla tarayıcı oturumu açarak örneği yerel olarak test edebilirsiniz.

## <a name="before-running-the-sample-for-the-first-time"></a>Örneği ilk kez çalıştırmadan önce

1. PowerShell, Windows Terminal, komut Istemi veya eşdeğer bir örnek açın ve örneği kopyalamak istediğiniz dizine gidin.
2. `git clone https://github.com/Azure-Samples/communication-services-web-chat-hero.git`
3. Azure portal alın `Connection String` . Bağlantı dizeleri hakkında daha fazla bilgi için bkz. [Azure Iletişim kaynakları oluşturma](../quickstarts/create-communication-resource.md)
4. ' I aldıktan sonra `Connection String` , sohbet klasörünün altında bulunan dosyadaki **sohbet/appsettings.js** dosyasına bağlantı dizesini ekleyin. Bağlantı dizenizi şu değişkende girin: `ResourceConnectionString` .

### <a name="local-run"></a>Yerel çalıştırma

1. Sohbet klasörüne gidin ve `Chat.csproj` Visual Studio 'da çözümü açın
2. Projeyi çalıştırın. Tarayıcı localhost: 5000 olarak açılır.

#### <a name="troubleshooting"></a>Sorun giderme

- Çözüm derlenmez, NPM yüklemesi/derlemesi sırasında hata veriyor

   C# çözümünü temizleme/yeniden derleme

## <a name="publish-the-sample-to-azure"></a>Örneği Azure 'da yayımlayın

1. Projeye sağ tıklayın `Chat` ve Yayımla ' yı seçin.
2. Yeni bir yayımlama profili oluşturun ve Azure aboneliğinizi seçin.
3. Yayımlamadan önce, Bağlantı dizenizi ile ekleyin `Edit App Service Settings` ve `ResourceConnectionString` anahtar olarak girin ve Bağlantı dizenizi (appsettings.jsüzerinde kopyalanmış) değer olarak girin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir Iletişim Hizmetleri aboneliğini temizleyip kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler. [Kaynakları Temizleme](../quickstarts/create-communication-resource.md#clean-up-resources)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

>[!div class="nextstepaction"]
>[GitHub 'dan örneği indirin](https://github.com/Azure-Samples/communication-services-web-chat-hero)

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Sohbet kavramları](../concepts/chat/concepts.md) hakkında bilgi edinin
- [Sohbet SDK](../concepts/chat/sdk-features.md) 'umuzu öğrenmeye
- [Contoso Med uygulama](https://github.com/Azure-Samples/communication-services-contoso-med-app) örneğini gözden geçirme

## <a name="additional-reading"></a>Ek okuma

- [Örnekler](./overview.md) -örneklere genel bakış sayfamızda daha fazla örnek ve örnek bulun.
- [Redux](https://redux.js.org/) -istemci tarafı durum yönetimi
- [Floentuı](https://aka.ms/fluent-ui) -MICROSOFT Powered UI kitaplığı
- Kullanıcı arabirimleri oluşturmak için [tepki](https://reactjs.org/) verme kitaplığı
- Web uygulamaları oluşturmak için [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) çerçeve
