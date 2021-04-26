---
title: Web uç noktalarını ayarlama
titleSuffix: Azure Cognitive Services
description: Özel komutlar için Web uç noktalarını ayarlama
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5f1d5318140dd14c5024e8dd3ad0def0afc7f378
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725917"
---
# <a name="set-up-web-endpoints"></a>Web uç noktalarını ayarlama

Bu makalede istemci uygulamasından HTTP isteği oluşturmak için Özel Komutlar uygulamasında web uç noktası ayarlamayı öğreneceksiniz. Şu görevleri tamamlayacaksınız:

- Özel Komutlar uygulamasında web uç noktalarını ayarlama
- Özel Komutlar uygulamasındaki web uç noktalarını çağırma
- Web uç noktalarından yanıt alma
- Web uç noktalarından gelen yanıtı özel bir JSON yüküyle tümleştirme, gönderme ve C# UWP Konuşma SDK'sı istemci uygulamasından görselleştirme

## <a name="prerequisites"></a>Önkoşullar

> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Konuşma hizmeti için Azure abonelik anahtarı: [Ücretsiz edinin](overview.md#try-the-speech-service-for-free) veya [Azure portalından](https://portal.azure.com) oluşturun
> * Özel komutlar uygulaması (bkz. [özel komutları kullanarak bir ses Yardımcısı oluşturma](quickstart-custom-commands-application.md))
> * Bir konuşma SDK 'Sı etkin istemci uygulaması (bkz. [konuşma SDK kullanarak istemci uygulamasıyla tümleştirme](how-to-custom-commands-setup-speech-sdk.md))

## <a name="deploy-an-external-web-endpoint-using-azure-function-app"></a>Azure Işlev uygulamasını kullanarak dış Web uç noktası dağıtma

Bu öğreticide, özel komutlar uygulamanızın **Türolmayan** komutunda ayarladığınız tüm cihazların durumlarını tutan bir HTTP uç noktası gerekir.

Çağırmak istediğiniz bir Web uç noktanız zaten varsa, [sonraki bölüme](#setup-web-endpoints-in-custom-commands)atlayın. Alternatif olarak, sonraki bölümde bu bölümü atlamak istiyorsanız kullanabileceğiniz varsayılan bir barındırılan Web uç noktası hakkında ayrıntılar sağlanmaktadır.

### <a name="input-format-of-azure-function"></a>Azure işlevinin giriş biçimi

Ardından, [Azure işlevleri](../../azure-functions/index.yml)'ni kullanarak bir uç nokta dağıtacaksınız.
Aşağıda, Azure işlevinizin geçirildiği özel komutlar olayının biçimi verilmiştir. Azure Işlev uygulamanızı yazarken bu bilgileri kullanın.

```json
{
  "conversationId": "string",
  "currentCommand": {
    "name": "string",
    "parameters": {
      "SomeParameterName": "string",
      "SomeOtherParameterName": "string"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "string",
      "SomeOtherGlobalParameterName": "string"
  }
}
```

    
Aşağıdaki tabloda bu girişin anahtar öznitelikleri açıklanmaktadır:
        
| Öznitelik | Açıklama |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **ConversationId** | Görüşmenin benzersiz tanımlayıcısı. Bu KIMLIğIN istemci uygulaması tarafından oluşturulup oluşturulmadığını unutmayın. |
| **currentCommand** | Şu anda konuşmada etkin olan komut. |
| **ada** | Komutun adı. `parameters`Öznitelik, parametrelerin geçerli değerlerini içeren bir eşlemedir. |
| **currentGlobalParameters** | Gibi bir eşlem `parameters` , ancak genel parametreler için kullanılır. |


**Devicemlak** Azure işlevi için, örnek bir özel komutlar olayı aşağıdaki gibi görünür. Bu, işlev uygulamasına **giriş** görevi görür.
    
```json
{
  "conversationId": "someConversationId",
  "currentCommand": {
    "name": "TurnOnOff",
    "parameters": {
      "item": "tv",
      "value": "on"
    }
  }
}
```

### <a name="azure-function-output-for-a-custom-command-app"></a>Özel komut uygulaması için Azure Işlevi çıkışı

Azure Işlevinizden çıkış bir özel komutlar uygulaması tarafından tüketilmişse, aşağıdaki biçimde görünmelidir. Ayrıntılar için bkz. [Web uç noktasından komut güncelleştirme](./how-to-custom-commands-update-command-from-web-endpoint.md) .

```json
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

### <a name="azure-function-output-for-a-client-application"></a>İstemci uygulaması için Azure Işlev çıkışı

Azure işlevinizdeki çıkış bir istemci uygulaması tarafından kullanılıyorsa, çıkış, istemci uygulamanın gerektirdiği her türlü formu alabilir.

**Devicme** uç noktası için Azure işlevinizin çıkışı, özel komutlar uygulaması yerine bir istemci uygulaması tarafından kullanılır. Azure işlevinin örnek çıktısı aşağıdaki gibi görünmelidir:
    
```json
{
  "TV": "on",
  "Fan": "off"
}
``` 

Cihazların durumunu koruyabilmeniz için bu çıkışın bir dış depolamaya yazılması gerekir. Dış depolama durumu aşağıdaki [istemci uygulamasıyla tümleştirin](#integrate-with-client-application) bölümünde kullanılacaktır.


### <a name="deploy-azure-function"></a>Azure işlevini dağıtma

Azure Işlevleri uygulaması olarak yapılandırabileceğiniz ve dağıtabileceğiniz bir örnek sunuyoruz. Örneğimiz için bir depolama hesabı oluşturmak için aşağıdaki adımları izleyin.
 
1. Cihaz durumunu kaydetmek için tablo depolaması oluşturun. Azure portal, ad **devicgöre** **depolama hesabı** türünde yeni bir kaynak oluşturun.
1. **Bağlantı dizesi** değerini **devicemlak-> erişim tuşlarından** kopyalayın. Bu dize gizli dizesini, indirilen örnek İşlev Uygulaması koduna eklemeniz gerekir.
1. Örnek [işlev uygulaması kodunu](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/custom-commands/quick-start)indirin.
1. İndirilen çözümü Visual Studio 2019 ' de açın. **Connections.js**, **STORAGE_ACCOUNT_SECRET_CONNECTION_STRING** 2. adımdaki gizli anahtar ile değiştirin.
1.  **DeviceStateAzureFunction** kodunu indirin.

Örnek uygulamayı Azure Işlevlerine dağıtmak için aşağıdaki adımları izleyin.

1. Azure Işlevleri uygulamasını [dağıtın](../../azure-functions/index.yml) .
1. Dağıtımın başarılı olmasını bekleyin ve Azure portal dağıtılan kaynağa gidin. 
1. Sol bölmedeki **işlevler** ' i seçin ve ardından **devic'** ı seçin.
1.  Yeni pencerede, **kod + test** ' i seçin ve ardından **Işlev URL 'sini al**' ı seçin.
 
## <a name="setup-web-endpoints-in-custom-commands"></a>Özel komutlarda Web uç noktalarını ayarlama

Azure işlevini var olan özel komutlar uygulamasıyla yedeklim.
Bu bölümde, mevcut bir varsayılan **devicme** uç noktası kullanacaksınız. Azure Işlevini kullanarak kendi web uç noktanızı oluşturduysanız veya aksi takdirde, varsayılan yerine bunu kullanın `https://webendpointexample.azurewebsites.net/api/DeviceState` .

1. Önceden oluşturduğunuz Özel Komutlar uygulamasını açın.
1. Web uç **noktalarına** gidin, **Yeni Web uç noktası**' na tıklayın.

   > [!div class="mx-imgBorder"]
   > ![Yeni web uç noktası](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Ayar | Önerilen değer | Açıklama |
   | ------- | --------------- | ----------- |
   | Ad | UpdateDeviceState | Web uç noktasının adı. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | Özel komut uygulamanızın iletişim kurmasını istediğiniz uç noktanın URL'si. |
   | Yöntem | POST | Uç noktanız için izin verilen etkileşimler (GET, POST gibi).|
   | Üst Bilgiler | Anahtar: app, Değer: applicationId değerinin ilk 8 basamağını kullanın | İstek üst bilgisine eklenecek üst bilgi parametreleri.|

    > [!NOTE]
    > - [Azure işlevleri](../../azure-functions/index.yml)kullanılarak oluşturulan örnek Web uç noktası, TV ve fanı 'nin cihaz durumunu kaydeden veritabanıyla birlikte kanca oluşturur.
    > - Önerilen üst bilgi yalnızca örnek uç nokta için gereklidir.
    > - Üstbilgi değerinin örnek uç noktanızda benzersiz olduğundan emin olmak için, **ApplicationId**'nizin ilk 8 basamağını alın.
    > - Gerçek dünyada Web uç noktası, cihazlarınızı yöneten [IoT Hub 'ına](../../iot-hub/about-iot-hub.md) ait uç nokta olabilir.

1. **Kaydet**’e tıklayın.

## <a name="call-web-endpoints"></a>Web uç noktalarını çağırma

1. **TurnOnOff** komutuna gidin, tamamlama kuralının altında **ConfirmationResponse** öğesini ve ardından **Eylem ekle**'yi seçin.
1. **Yeni eylem türü**'nün altında **Web uç noktasını çağır**'ı seçin
1. **Eylemi Düzenle - Uç Noktaları** bölümünde oluşturduğumuz web uç noktası olan **UpdateDeviceState** girişini seçin.  
1. **Yapılandırma** alanına aşağıdaki değerleri girin:
   > [!div class="mx-imgBorder"]
   > ![Web uç noktası çağırma - eylem parametreleri](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Ayar | Önerilen değer | Açıklama |
   | ------- | --------------- | ----------- |
   | Uç Noktalar | UpdateDeviceState | Bu eylemde çağırmak istediğiniz web uç noktası. |
   | Sorgu parametreleri | item={SubjectDevice}&&value={OnOff} | Web uç noktası URL'sine eklenecek sorgu parametreleri.  |
   | Gövde içeriği | Yok | İsteğin gövde içeriği. |

    > [!NOTE]
    > - Önerilen sorgu parametreleri yalnızca örnek uç nokta için gereklidir

1. **Başarılı Olduğunda - Yürütülecek eylem** bölümünde **Konuşma yanıtı gönder**'i seçin.

    **Basit düzenleyici**'ye `{SubjectDevice} is {OnOff}` yazın.

   > [!div class="mx-imgBorder"]
   > ![Yürütme için başarı eylemini gösteren ekran görüntüsü.](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Ayar | Önerilen değer | Açıklama |
   | ------- | --------------- | ----------- |
   | Yürütülecek eylem | Konuşma yanıtı gönder | Web uç noktasına gönderilen isteğin başarılı olması durumunda yürütülecek eylem |

   > [!NOTE]
   > - Dilerseniz `{YourWebEndpointName.FieldName}` kullanarak HTTP yanıtındaki alanlara doğrudan erişim sağlayabilirsiniz. Örnek: `{UpdateDeviceState.TV}`

1. **Başarısız Olduğunda - Yürütülecek eylem** bölümünde **Konuşma yanıtı gönder**'i seçin

    **Basit düzenleyici**'ye `Sorry, {WebEndpointErrorMessage}` yazın.

   > [!div class="mx-imgBorder"]
   > ![Web uç noktası çağırma - Başarısız Olduğunda](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Ayar | Önerilen değer | Açıklama |
   | ------- | --------------- | ----------- |
   | Yürütülecek eylem | Konuşma yanıtı gönder | Web uç noktasına gönderilen isteğin başarısız olması durumunda yürütülecek eylem |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` isteğe bağlıdır. Hata iletisi görmek istemiyorsanız bunu kaldırabilirsiniz.
   > - Örnek uç noktamızda eksik üst bilgi parametreleri gibi yaygın hatalar için ayrıntılı hata iletisi içeren bir HTTP yanıtı gönderiyoruz.

### <a name="try-it-out-in-test-portal"></a>Test portalında deneyin
- Başarı yanıtı, Kaydet, eğitme ve test.
   > [!div class="mx-imgBorder"]
   > ![Başarı yanıtını gösteren ekran görüntüsü.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Başarısız yanıt durumunda sorgu parametrelerinden birini kaldırın, kaydedin, yeniden eğitme ve test edin.
   > [!div class="mx-imgBorder"]
   > ![Web uç noktası çağırma - Başarılı Olduğunda](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>İstemci uygulamasıyla tümleştirme

[Özel komutları istemci uygulamasına gönder](./how-to-custom-commands-send-activity-to-client.md)bölümünde, istemci eylemine bir **etkinlik Gönder** eylemi eklediniz. Eylem, **Web uç noktasını çağırma** eyleminin başarılı olup olmadığına bakılmaksızın istemci uygulamasına gönderilir.
Ancak, genellikle Web uç noktasına yapılan çağrı başarılı olduğunda yalnızca istemci uygulamasına etkinlik göndermek istersiniz. Bu örnekte bu işlem, cihazın durumunun başarıyla güncelleştirilmesidir.

1. Önceden eklediğiniz **Etkinliği istemciye gönder** eylemini silin.
1. Çağrı web uç noktasını düzenleyin:
    1. **Yapılandırma** bölümünde **Sorgu Parametreleri** değerinin `item={SubjectDevice}&&value={OnOff}` olduğundan emin olun
    1. **Başarılı Olduğunda** bölümünde **Yürütülecek eylem** değerini **Etkinliği istemciye gönder** olarak değiştirin
    1. Aşağıdaki JSON kodunu **Etkinlik İçeriği**'ne kopyalayın
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
Artık, Web uç noktasına yönelik istek başarılı olduğunda yalnızca istemciye etkinlik gönderirsiniz.

### <a name="create-visuals-for-syncing-device-state"></a>Cihaz durumunu eşitlemek için görsel oluşturma

Aşağıdaki XML 'i `MainPage.xaml` **Enablemikro phonebutton** bloğunun üzerine ekleyin.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Cihaz durumunu eşitleme

`MainPage.xaml.cs` dosyasına `using Windows.Web.Http;` başvurusunu ekleyin. Aşağıdaki kodu `MainPage` sınıfına ekleyin. Bu yöntem, örnek uç noktaya bir GET isteği gönderir ve uygulamanız için geçerli cihaz durumunu ayıklar. `<your_app_name>`Özel komut uç noktasındaki **üst bilgide** kullandığınız şekilde değişdiğinizden emin olun.

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request.
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Deneyin

1. Uygulamayı başlatın.
1. Cihaz Eşitleme Durumu'na tıklayın.\
Uygulamayı önceki bölümde test ederseniz TV programlarını `turn on tv` **Açık** olarak görürsünüz.
    > [!div class="mx-imgBorder"]
    > ![Cihaz durumunu eşitleme](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. **Mikrofonu etkinleştir**' i seçin.
1. **Konuş** düğmesini seçin.
1. Söyleyin `turn on the fan` . Fanı 'ın görsel durumu **Açık** olarak değişir.
    > [!div class="mx-imgBorder"]
    > ![Vantilatörü aç](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Özel komutlar uygulamasını uzak bir yetenek olarak dışarı aktar](./how-to-custom-commands-integrate-remote-skills.md)
