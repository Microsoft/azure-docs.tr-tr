---
title: include dosyası
description: include dosyası
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 0504467e387c0875a8e00bd1cf638ec437232092
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554352"
---
**Hero örneğini çağıran** Azure Iletişim Hizmetleri grubu, bir grup çağırma deneyimi oluşturmak için Web SDK 'Sını çağıran iletişim hizmetleri 'nin nasıl kullanılabileceğini gösterir.

Bu örnek hızlı başlangıçta, örneği yerel makinenizde çalıştırmadan önce örneğin nasıl çalıştığını öğreneceksiniz. Daha sonra Azure Iletişim Hizmetleri kaynaklarınızı kullanarak örneği Azure 'a dağıtırsınız.

## <a name="download-code"></a>Kodu indirin

[GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero)'da bu örneğe yönelik projeyi bulun. [Ekiplerin birlikte çalışması](../../concepts/teams-interop.md) ile örnek bir sürümü ayrı bir [dalda](https://github.com/Azure-Samples/communication-services-web-calling-hero/tree/teams-interop)bulunabilir.

## <a name="overview"></a>Genel Bakış

Örnekte hem istemci tarafı uygulaması hem de sunucu tarafı uygulaması vardır. **İstemci tarafı uygulama** , Microsoft 'un akıcı Kullanıcı arabirimi çerçevesini kullanan bir yanıt veren/Redux Web uygulamasıdır. Bu uygulama, istemci tarafı uygulamanın Azure 'a bağlanmasına yardımcı olan bir ASP.NET Core **sunucu tarafı uygulamasına** istek gönderir.

Örnek şöyle görünür:

:::image type="content" source="../media/calling/landing-page.png" alt-text="Örnek uygulamanın giriş sayfasını gösteren ekran görüntüsü.":::

"Çağrı Başlat" düğmesine bastığınızda Web uygulaması, sunucu tarafı uygulamadan bir Kullanıcı erişim belirteci getirir. Bu belirteç daha sonra istemci uygulamasını Azure Communication Services 'a bağlamak için kullanılır. Belirteç alındıktan sonra, kullanmak istediğiniz kamerayı ve mikrofonu belirtmeniz istenecektir. İki durumlu denetimlerle cihazlarınızı devre dışı bırakabileceksiniz/etkinleştirebilirsiniz:

:::image type="content" source="../media/calling/pre-call.png" alt-text="Örnek uygulamanın çağrı öncesi ekranını gösteren ekran görüntüsü.":::

Görüntü adınızı ve cihazlarınızı yapılandırdıktan sonra, çağrı oturumuna katılabilir. Ardından, çekirdek çağırma deneyiminin yaşadığı ana çağrı tuvalini görürsünüz.

:::image type="content" source="../media/calling/main-app.png" alt-text="Örnek uygulamanın ana ekranını gösteren ekran görüntüsü.":::

Ana arama ekranının bileşenleri:

- **Medya Galerisi**: katılımcıların gösterildiği ana aşama. Bir katılımcının Kamerası etkinse, video akışları burada gösterilir. Her katılımcının, görünen adını ve video akışını (bir tane olduğunda) gösteren bir tek kutucuğu vardır
- **Üst bilgi**: Bu, birincil çağrı denetimlerinin ayarları ve katılımcı tarafı çubuğunu açıp, video ve karışımı açma/kapatma, ekran paylaşma ve çağrıyı bırakma olarak bulunduğu yerdir.
- **Yan çubuk**: Bu, üst bilgi üzerindeki denetimler kullanılarak, katılımcılar ve ayar bilgilerinin gösterildiği yerdir. Bileşen sağ üst köşedeki ' X ' kullanılarak kapatılabilir. Katılımcılar yan çubuğu, katılımcıların bir listesini ve sohbet 'e daha fazla kullanıcı davet etmek için bir bağlantı gösterir. Ayarlar yan çubuğu, mikrofon ve kamera ayarlarını yapılandırmanıza olanak tanır.

> [!NOTE]
> Web 'i çağıran SDK kısıtlamalarına bağlı olarak yalnızca bir adet uzak video akışı işlenir. Daha fazla bilgi için bkz. [SDK akış desteğini çağırma](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features#calling-sdk-streaming-support).

Aşağıda, önkoşulları ve örneği ayarlama adımlarını hakkında daha fazla bilgi bulacaksınız.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. Ayrıntılar için bkz. [ücretsiz hesap oluşturma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 ve üzeri)](https://nodejs.org/en/download/)
- [Visual Studio (2019 ve üstü)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Visual Studio örneğinize karşılık gelen sürümü yüklediğinizden emin olun, 32 vs 64 bit)
- Bir Azure Iletişim Hizmetleri kaynağı. Ayrıntılar için bkz. [Azure Iletişim kaynağı oluşturma](../../quickstarts/create-communication-resource.md). Bu hızlı başlangıç için kaynak **Bağlantı dizenizi** kaydetmeniz gerekir.

## <a name="locally-deploy-the-service--client-applications"></a>Hizmet & istemci uygulamalarını yerel olarak dağıtın

Örnek çağıran grup temelde iki uygulamadır: ClientApp ve Service.NET uygulaması.

Yerel olarak dağıtmak istiyoruz, her iki uygulamayı da başlatmanız gerekir. Sunucu uygulaması tarayıcıdan ziyaret edildiğinde, Kullanıcı deneyimi için yerel olarak dağıtılan ClientApp kullanır.

Çoklu Kullanıcı çağrısının benzetimini yapmak için çağrın URL 'siyle birden çok tarayıcı oturumu açarak örneği yerel olarak test edebilirsiniz.

### <a name="before-running-the-sample-for-the-first-time"></a>Örneği ilk kez çalıştırmadan önce

1. PowerShell, Windows Terminal, komut Istemi veya eşdeğer bir örnek açın ve örneği kopyalamak istediğiniz dizine gidin.
2. `git clone https://github.com/Azure-Samples/communication-services-web-calling-hero.git`
3. Azure portal alın `Connection String` . Bağlantı dizeleri hakkında daha fazla bilgi için bkz. [Azure Iletişim kaynakları oluşturma](../../quickstarts/create-communication-resource.md).
4. ' I aldıktan sonra `Connection String` , bağlantı dizesini hizmet .NET klasörü altında bulunan **çağrı/appsetting.js** dosyasına ekleyin. Bağlantı dizenizi şu değişkende girin: `ResourceConnectionString` .

### <a name="local-run"></a>Yerel çalıştırma

1. Çağırma klasörü ' ne gidin ve `Calling.csproj` Visual Studio 'da çözümü açın.
2. `Calling`Projeyi Çalıştır. Tarayıcı ' da açılır `localhost:5001` .

## <a name="publish-the-sample-to-azure"></a>Örneği Azure 'da yayımlayın

1. Projeye sağ tıklayın `Calling` ve Yayımla ' yı seçin.
2. Yeni bir yayımlama profili oluşturun ve Azure aboneliğinizi seçin.
3. Yayımlamadan önce, Bağlantı dizenizi ile ekleyin `Edit App Service Settings` ve `ResourceConnectionString` anahtar olarak girin ve Bağlantı dizenizi (appsettings.jsüzerinde kopyalanmış) değer olarak girin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir Iletişim Hizmetleri aboneliğini temizleyip kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler. [Kaynakları Temizleme](../../quickstarts/create-communication-resource.md#clean-up-resources)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

>[!div class="nextstepaction"]
>[GitHub 'dan örneği indirin](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Çağıran SDK 'yı kullanma](../../quickstarts/voice-video-calling/calling-client-samples.md) hakkında bilgi edinin
- [Nasıl çalıştığını çağırma](../../concepts/voice-video-calling/about-call-types.md) hakkında daha fazla bilgi edinin

### <a name="additional-reading"></a>Ek okuma

- [Örnekler](./../overview.md) -örneklere genel bakış sayfamızda daha fazla örnek ve örnek bulun.
- [Redux](https://redux.js.org/) -istemci tarafı durum yönetimi
- [Floentuı](https://aka.ms/fluent-ui) -MICROSOFT Powered UI kitaplığı
- Kullanıcı arabirimleri oluşturmak için [tepki](https://reactjs.org/) verme kitaplığı
- Web uygulamaları oluşturmak için [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) çerçeve
