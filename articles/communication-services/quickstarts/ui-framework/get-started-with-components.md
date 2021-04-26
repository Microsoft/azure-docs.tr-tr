---
title: Azure Communication Services kullanıcı arabirimi çerçevesi temel bileşenleri ile çalışmaya başlama
titleSuffix: An Azure Communication Services quickstart
description: Bu hızlı başlangıçta, UI çerçevesi temel bileşenleri ile çalışmaya başlama hakkında bilgi edineceksiniz.
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 336059007cfca40a74ad5a4395c6f9a59215bb58
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105825790"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>Hızlı başlangıç: UI çerçevesi temel bileşenlerini kullanmaya başlama

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

İletişim deneyimlerini uygulamalarınıza hızlıca bütünleştirmek için UI çerçevesini kullanarak Azure Iletişim Hizmetleri ile çalışmaya başlayın. Bu hızlı başlangıçta, iletişim deneyimleri oluşturmak için UI çerçevesi temel bileşenlerinin uygulamanıza nasıl tümleştirileceğini öğreneceksiniz. 

UI çerçevesi bileşenleri iki şekilde gelir: Base ve Composite.

- **Temel bileşenler** ayrı iletişim yeteneklerini temsil eder; Bunlar, karmaşık iletişim deneyimleri oluşturmak için kullanılabilen temel yapı taşlarıdır. 
- **Bileşik bileşenler** , derleme blokları olarak **temel bileşenler** kullanılarak derlenen ve uygulamalarla kolayca tümleştirilebilecek şekilde paketlenmiş ortak iletişim senaryoları için açılan anahtar deneyimlerdir.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Etkin LTS ve bakım LTS sürümleri (node 12 önerilir).
- Etkin bir Iletişim Hizmetleri kaynağı. [Iletişim Hizmetleri kaynağı oluşturun](./../create-communication-resource.md).
- Arama istemcisini başlatmak için bir Kullanıcı erişim belirteci. [Kullanıcı erişim belirteçleri oluşturmayı ve yönetmeyi](./../access-tokens.md)öğrenin.

## <a name="setting-up"></a>Ayarlanıyor

UI çerçevesi, kurulum için bir tepki verme ortamı gerektirir. Bundan sonra bunu yapacağız. Zaten bir tepki verme uygulamanız varsa, bu bölümü atlayabilirsiniz.

### <a name="set-up-react-app"></a>Tepki verme uygulaması ayarlama

Bu hızlı başlangıç için Create-tepki-App şablonunu kullanacağız. Daha fazla bilgi için bkz. [tepki Ile çalışmaya başlama](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

Bu işlemin sonunda klasörün içinde tam bir uygulamanız olması gerekir `my-app` . Bu hızlı başlangıçta, klasörün içindeki dosyaları değiştiriyorsunuz `src` .

### <a name="install-the-package"></a>Paketi yükler

`npm install`JavaScript için SDK 'Yı çağıran Azure Iletişim hizmetlerini yüklemek için komutunu kullanın. Belirtilen tarbol (özel Önizleme) ' i My-App dizinine taşıyın.

```console

//For Private Preview install tarball

npm install --save ./{path for tarball}

```

`--save`Seçeneği, kitaplığı dosyadaki **package.js** bir bağımlılık olarak listeler.

### <a name="run-create-react-app"></a>Tepki verme uygulaması oluştur

Aşağıdakileri çalıştırarak yanıt verme uygulama yüklemesi oluştur ' a test edelim:

```console

npm run start   

```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, Azure Iletişim Hizmetleri Kullanıcı arabirimi SDK 'sının bazı önemli özelliklerini işler:

| Ad                                  | Açıklama                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Sağlayıcı| Geliştiricilerin temeldeki akıcı Kullanıcı Arabirimi bileşenlerini değiştirmesine izin veren akıcı Kullanıcı arabirimi sağlayıcısı|
| Callingsağlayıcısı| Çağrı örneği oluşturmak için sağlayıcı çağrılıyor. Ek bileşen eklemek için gereklidir|
| ChatProvider | Sohbet iş parçacığı örneğini oluşturmak için sohbet sağlayıcısı. Ek bileşen eklemek için gereklidir|
| MediaGallery | Çağrı katılımcıları ve bunların uzak video akışlarını gösteren temel bileşen |
| MediaControls | Sessiz, video, paylaşma ekranı dahil olmak üzere çağrıyı denetlemek için temel bileşen |
| ChatThread | Yazma göstergeleri, okundu alındıları vb. ile sohbet iş parçacığı işleyen temel bileşen. |
| SendBox | Kullanıcının birleştirilmiş iş parçacığına gönderilecek iletileri girmesini sağlayan temel bileşen|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>Azure Communication Services kimlik bilgilerini kullanarak çağrı ve sohbet sağlayıcılarını başlatma

`src`İçindeki klasörüne gidin `my-app` ve dosyayı bulun `app.js` . Burada, arama ve sohbet sağlayıcılarımızı başlatmak için aşağıdaki kodu ekleyeceğiz. Bu sağlayıcılar, çağrı ve sohbet deneyimlerinin bağlamını sürdürmekten sorumludur. Oluşturmakta olduğunuz iletişim deneyiminin türüne göre hangisini kullanacağınızı seçebilirsiniz. Gerekirse, her ikisini de aynı anda kullanabilirsiniz. Bileşenleri başlatmak için Azure Iletişim hizmetlerinden alınan bir erişim belirtecine ihtiyacınız olacaktır. Erişim belirteçleri alma hakkında daha fazla bilgi için bkz: [erişim belirteçleri oluşturma ve yönetme](./../access-tokens.md).

> [!NOTE]
> Bileşenler, erişim belirteçleri, grup kimlikleri veya iş parçacığı kimlikleri oluşturmaz. Bu öğeler, bu kimlikleri oluşturmak ve bunları istemci uygulamasına geçirmek için doğru adımları geçen hizmetlerden gelir. Daha fazla bilgi için bkz: [Istemci sunucu mimarisi](./../../concepts/client-and-server-architecture.md).
> 
> Örneğin: sohbet sağlayıcı, `userId` `token` onu başlatmak için kullanılan ile ilişkili olan ' ın zaten sağlanmakta olan öğesine katılmış olmasını bekler `threadId` . Belirteç iş parçacığı KIMLIĞINE katılmadıysa, sohbet sağlayıcısı başarısız olur. Sohbet hakkında daha fazla bilgi için bkz. [sohbet Ile çalışmaya](./../chat/get-started.md) başlama

Uygulamanın görünümünü ve kullanımını iyileştirmek için akıcı bir UI teması kullanacağız:

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

Bu sağlayıcı başlatıldıktan sonra, Kullanıcı arabirimi çerçevesi temel bileşenleri ve tüm ek Düzen mantığını kullanarak kendi düzeninizi oluşturmanıza olanak sağlar. Sağlayıcı, temel alınan tüm mantığı başlatmayı ve farklı bileşenleri birbirine doğru şekilde bağlamayı üstlenir. Ardından, iletişim deneyimleri oluşturmak için UI çerçevesi tarafından sunulan çeşitli temel bileşenleri kullanacağız. Bu bileşenlerin yerleşimini özelleştirebilir ve bunlarla birlikte işlemek istediğiniz diğer özel bileşenleri ekleyebilirsiniz. 

## <a name="build-ui-framework-calling-component-experiences"></a>Bileşen deneyimlerini çağıran kullanıcı arabirimi çerçevesi oluşturma

Çağırmak için `MediaGallery` ve `MediaControls` bileşenlerini kullanacağız. Bunlarla ilgili daha fazla bilgi için bkz. [UI çerçevesi özellikleri](./../../concepts/ui-framework/ui-sdk-features.md). Başlamak için, `src` klasöründe adlı yeni bir dosya oluşturun `CallingComponents.js` . Burada, temel bileşenlerimizi barındıracak bir işlev bileşeni başlatacak ve ardından içinde içeri aktaracağız `app.js` . Bileşenler etrafında ek düzen ve stil ekleyebilirsiniz. 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

Bu dosyanın en altında, çağıran  `connectFuncsToContext` Kullanıcı Arabirimi bileşenlerini eşleme işlevini kullanarak çağıran UI bileşenlerini temel duruma bağlamak için Kullanıcı arabirimi çerçevesindeki yöntemi kullanarak çağıran bileşenleri aktardık `MapToCallingSetupProps` . Bu yöntem, onun, durumunu denetlemek ve çağrıyı birleştirmek için kullandığımız, onun props 'ın doldurulmuş olduğu bileşeni verir. Özelliğini kullanarak `isCallInitialized` , ' ın olup olmadığını kontrol edin `CallAgent` ve ardından `joinCall` içine katılması için yöntemini kullanın. UI çerçevesi, geliştiricilerin verilerin bileşenlere nasıl itiltiğini denetlemek istedikleri senaryolar için kullanılacak özel eşleme işlevlerini destekler.

## <a name="build-ui-framework-chat-component-experiences"></a>UI çerçevesi sohbet bileşeni deneyimleri oluşturun

Sohbet için `ChatThread` ve `SendBox` bileşenlerini kullanacağız. Bu bileşenler hakkında daha fazla bilgi için bkz. [UI çerçevesi özellikleri](./../../concepts/ui-framework/ui-sdk-features.md). Başlamak için, `src` klasöründe adlı yeni bir dosya oluşturun `ChatComponents.js` . Burada, temel bileşenlerimizi barındıracak bir işlev bileşeni başlatacak ve ardından içinde içeri aktaracağız `app.js` .

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>Ana uygulamaya çağrı ve sohbet bileşenleri ekleme

Dosyasına geri döndüğünüzde `app.js` , artık bileşenleri `CallingProvider` `ChatProvider` aşağıda gösterildiği gibi ve öğesine ekleyeceğiz.

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

```

## <a name="run-quickstart"></a>Hızlı başlangıcı Çalıştır

Yukarıdaki kodu çalıştırmak için komutunu kullanın:

```console

npm run start   

```

Özellikleri tam olarak test etmek için çağrı ve sohbet iş parçacığına katılması için çağrı ve sohbet işlevselliğine sahip ikinci bir istemciye ihtiyacınız olacaktır. [Arayan Hero örnekimize](./../../samples/calling-hero-sample.md) bakın ve olası seçenekler olarak [sohbet Hero örneği](./../../samples/chat-hero-sample.md) .

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir Iletişim Hizmetleri aboneliğini temizleyip kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler. [Kaynakları Temizleme](../create-communication-resource.md#clean-up-resources)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [UI çerçevesi bileşik bileşenlerini deneyin](./get-started-with-composites.md)

Daha fazla bilgi için aşağıdaki kaynaklara bakın:
- [UI çerçevesine genel bakış](../../concepts/ui-framework/ui-sdk-overview.md)
- [UI çerçevesi özellikleri](./../../concepts/ui-framework/ui-sdk-features.md)
