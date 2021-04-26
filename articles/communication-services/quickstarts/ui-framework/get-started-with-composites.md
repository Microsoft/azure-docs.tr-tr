---
title: Azure Communication Services kullanıcı arabirimi çerçevesi SDK 'Sı bileşik bileşenleri ile çalışmaya başlama
titleSuffix: An Azure Communication Services quickstart
description: Bu hızlı başlangıçta, UI çerçevesi bileşik bileşenlerini kullanmaya nasıl başlayacağınızı öğreneceksiniz.
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 7d6b0d01ec3de1cb4ce91b1c81cd8a0ccd73b1d1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105825807"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>Hızlı başlangıç: UI çerçevesi bileşik bileşenlerini kullanmaya başlama

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

İletişim deneyimlerini uygulamalarınıza hızlıca bütünleştirmek için UI çerçevesini kullanarak Azure Iletişim Hizmetleri ile çalışmaya başlayın. Bu hızlı başlangıçta, iletişim deneyimleri oluşturmak için UI çerçevesi bileşik bileşenlerinin uygulamanıza nasıl tümleştirileceğini öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Etkin LTS ve bakım LTS sürümleri (node 12 önerilir).
- Etkin bir Iletişim Hizmetleri kaynağı. [Iletişim Hizmetleri kaynağı oluşturun](./../create-communication-resource.md).
- Bileşik çağrının örneğini oluşturmak için bir Kullanıcı erişim belirteci. [Kullanıcı erişim belirteçleri oluşturmayı ve yönetmeyi](./../access-tokens.md)öğrenin.

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

//Private Preview install tarball

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
| GroupCall | Katılımcı Galerisi ve denetimleriyle bir grup çağırma deneyimi işleyen bileşik bileşen. |
| GroupChat | Sohbet iş parçacığı ve girişli bir grup sohbeti deneyimi işleyen bileşik bileşen |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>Grup çağrısı ve grup sohbeti karma bileşenlerini Başlat

`src`İçindeki klasörüne gidin `my-app` ve dosyayı bulun `app.js` . Burada, Grup sohbeti ve çağrılması için Birleşik bileşenlerimizi başlatmak üzere aşağıdaki kodu ekleyeceğiz. Oluşturmakta olduğunuz iletişim deneyiminin türüne göre hangisini kullanacağınızı seçebilirsiniz. Gerekirse, her ikisini de aynı anda kullanabilirsiniz. Bileşenleri başlatmak için Azure Iletişim hizmetlerinden alınan bir erişim belirtecine ihtiyacınız olacaktır. Erişim belirteçleri alma hakkında daha fazla bilgi için bkz: [Kullanıcı erişim belirteçleri oluşturma ve yönetme](./../access-tokens.md).

> [!NOTE]
> Bileşenler, erişim belirteçleri, grup kimlikleri veya iş parçacığı kimlikleri oluşturmaz. Bu öğeler, bu kimlikleri oluşturmak ve bunları istemci uygulamasına geçirmek için doğru adımları geçen hizmetlerden gelir. Daha fazla bilgi için bkz: [Istemci sunucu mimarisi](./../../concepts/client-and-server-architecture.md).
> 
> Örneğin: sohbet Composite Grup, `userId` `token` onu başlatmak için kullanılan ile ilişkilendirilen, zaten sağlanmakta olan öğesine katılmış olmasını bekliyor `threadId` . Belirteç iş parçacığı KIMLIĞINE katılmamışsa, sohbet Composite grubu başarısız olur. Sohbet hakkında daha fazla bilgi için bkz. [sohbet Ile çalışmaya](./../chat/get-started.md) başlama


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

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
> [UI çerçevesi temel bileşenlerini deneyin](./get-started-with-components.md)

Daha fazla bilgi için aşağıdaki kaynaklara bakın:
- [UI çerçevesine genel bakış](../../concepts/ui-framework/ui-sdk-overview.md)
- [UI çerçevesi özellikleri](./../../concepts/ui-framework/ui-sdk-features.md)
