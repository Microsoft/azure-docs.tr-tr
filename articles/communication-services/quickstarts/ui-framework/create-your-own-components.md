---
title: Kendi UI çerçevesi bileşeninizi oluşturma
titleSuffix: An Azure Communication Services quickstart
description: Bu hızlı başlangıçta, UI çerçevesiyle uyumlu özel bir bileşen oluşturmayı öğreneceksiniz.
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 798b898f6e2367e3096bcf985715fd48de3d394f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930226"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>Hızlı başlangıç: kendi UI çerçevesi bileşeninizi oluşturma

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

İletişim deneyimlerini uygulamalarınıza hızlıca bütünleştirmek için UI çerçevesini kullanarak Azure Iletişim Hizmetleri ile çalışmaya başlayın.

Bu hızlı başlangıçta, Kullanıcı arabirimi çerçevesi tarafından sunulan önceden tanımlanmış durum arabirimini kullanarak kendi bileşenlerinizi nasıl oluşturacağınızı öğreneceksiniz. Bu yaklaşım, daha fazla özelleştirmeye ihtiyaç duyan ve bu deneyim için kendi tasarım varlıklarını kullanmak isteyen geliştiriciler için idealdir. 

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Etkin LTS ve bakım LTS sürümleri (node 12 önerilir).
- Etkin bir Iletişim Hizmetleri kaynağı. [Iletişim Hizmetleri kaynağı oluşturun](./../create-communication-resource.md).
- Arama istemcisini başlatmak için bir Kullanıcı erişim belirteci. [Kullanıcı erişim belirteçleri oluşturmayı ve yönetmeyi](./../access-tokens.md)öğrenin.

UI çerçevesi, bir tepki verme ortamının ayarlanmasını gerektirir. Bundan sonra bunu yapacağız. Zaten bir tepki verme uygulamanız varsa, bu bölümü atlayabilirsiniz.

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
| Callingsağlayıcısı| Çağrı örneği oluşturmak için sağlayıcı çağrılıyor. Temel bileşenleri eklemek için gereklidir|
| ChatProvider | Sohbet iş parçacığı örneğini oluşturmak için sohbet sağlayıcısı. Temel bileşenleri eklemek için gereklidir|
| connectFuncsToContext | Mapsap kullanarak temel sağlayıcılar ile UI çerçevesi bileşenlerini bağlama yöntemi |
| MapToChatMessageProps | Sohbet iletisi props içeren bileşenler sağlayan sohbet iletisi veri katmanı Eşleyici |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>Azure Iletişim Hizmetleri kimlik bilgilerini kullanarak sohbet sağlayıcılarını başlatma

Bu hızlı başlangıçta bir örnek olarak sohbet kullanacağız. çağırma hakkında daha fazla bilgi için bkz. [temel bileşenler hızlı başlangıç](./get-started-with-components.md) ve [bileşik bileşen hızlı başlangıç](./get-started-with-composites.md).

`src`İçindeki klasörüne gidin `my-app` ve dosyayı bulun `app.js` . Burada, sohbet Sağlayıcımızı başlatmak için aşağıdaki kodu bırakmalısınız. Bu sağlayıcı, çağrı ve sohbet deneyimlerinin bağlamını sürdürmek için kullanılır. Bileşenleri başlatmak için Azure Iletişim hizmetlerinden alınan bir erişim belirtecine ihtiyacınız olacaktır. Erişim belirteci alma hakkında daha fazla bilgi için bkz: [erişim belirteçleri oluşturma ve yönetme](./../access-tokens.md).

UI çerçevesi bileşenleri, hizmetin geri kalanı için aynı genel mimariyi izler. Bileşenler, erişim belirteçleri, grup kimlikleri veya iş parçacığı kimlikleri oluşturmaz. Bu öğeler, bu kimlikleri oluşturmak ve bunları istemci uygulamasına geçirmek için doğru adımları geçen hizmetlerden gelir. Daha fazla bilgi için bkz. [Istemci sunucu mimarisi](./../../concepts/client-and-server-architecture.md).

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

Bu sağlayıcı başlatıldıktan sonra, Kullanıcı arabirimi çerçevesi bileşenini ve ek Düzen mantığını kullanarak kendi düzeninizi oluşturmanıza olanak sağlar. Sağlayıcı, temel alınan tüm mantığı başlatmayı ve farklı bileşenleri birbirine doğru şekilde bağlamayı üstlenir. Ardından, sohbet sağlayıcımıza bağlanmak için UI çerçeve mapgt kullanarak özel bir bileşen oluşturacağız.


## <a name="create-a-custom-component-using-mappers"></a>Mapdenetleyicileri kullanarak özel bir bileşen oluşturma

Bileşeni oluşturduğumuz yeni bir dosya oluşturarak başlayacağız `SimpleChatThread.js` . İhtiyaç duyduğumuz Kullanıcı arabirimi çerçevesi bileşenleri içeri aktarılmaya başlayacağız. Burada, HTML 'yi kullanıma sunacağız ve basit bir sohbet iş parçacığı için tam özel bir bileşen oluşturmaya tepki veririz. Yöntemini kullanarak `connectFuncsToContext` , `MapToChatMessageProps` props 'ı özel bileşenlere eşlemek için eşleştiricisini kullanacağız  `SimpleChatThread` . Bu props, gönderilen ve alınan sohbet iletilerine basit iş parçacığumuza kadar erişim sağlayacak.

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>Uygulamanıza özel bileşeninizi ekleme

Özel bileşenimizin hazır olduğuna göre, bunu içeri aktaracağız ve yerleşimimize ekleyeceğiz.

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>Hızlı başlangıcı Çalıştır

Yukarıdaki kodu çalıştırmak için komutunu kullanın:

```console

npm run start   

```

Özellikleri tam olarak test etmek için, basit sohbet Iş parçacığımız tarafından alınacak iletileri göndermek üzere bir sohbet işlevselliğine sahip ikinci bir istemciye ihtiyacınız olacaktır. [Arayan Hero örnekimize](./../../samples/calling-hero-sample.md) bakın ve olası seçenekler olarak [sohbet Hero örneği](./../../samples/chat-hero-sample.md) .

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir Iletişim Hizmetleri aboneliğini temizleyip kaldırmak istiyorsanız, kaynağı veya kaynak grubunu silebilirsiniz. Kaynak grubunun silinmesi, onunla ilişkili diğer tüm kaynakları da siler. [Kaynakları Temizleme](../create-communication-resource.md#clean-up-resources)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [UI çerçevesi bileşik bileşenlerini deneyin](./get-started-with-composites.md)

Daha fazla bilgi için aşağıdaki kaynaklara bakın:
- [UI çerçevesine genel bakış](../../concepts/ui-framework/ui-sdk-overview.md)
- [UI çerçevesi özellikleri](./../../concepts/ui-framework/ui-sdk-features.md)
- [UI çerçevesi temel bileşenleri hızlı başlangıç](./get-started-with-components.md)
