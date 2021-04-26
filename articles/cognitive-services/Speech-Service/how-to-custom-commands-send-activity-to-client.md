---
title: İstemci uygulamasına özel komutlar etkinliği gönder
titleSuffix: Azure Cognitive Services
description: Bu makalede, özel komutlar uygulamasından konuşma SDK 'sını çalıştıran bir istemci uygulamasına nasıl etkinlik gönderileceğini öğreneceksiniz.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52e0b750f02044afafe233a76e4f43755d9ed303
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725107"
---
# <a name="send-custom-commands-activity-to-client-application"></a>İstemci uygulamasına özel komutlar etkinliği gönder

Bu makalede, özel komutlar uygulamasından konuşma SDK 'sını çalıştıran bir istemci uygulamasına nasıl etkinlik gönderileceğini öğreneceksiniz.

Aşağıdaki görevleri tamamlayabilirsiniz:

- Özel komutlar uygulamanızdan özel bir JSON yükü tanımlama ve gönderme
- C# UWP konuşma SDK 'Sı istemci uygulamasından özel JSON yükü içeriğini alma ve görselleştirme

## <a name="prerequisites"></a>Önkoşullar
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) veya üzeri. Bu kılavuzda Visual Studio 2019 kullanılmaktadır
> * Konuşma hizmeti için Azure abonelik anahtarı: [Ücretsiz edinin](overview.md#try-the-speech-service-for-free) veya [Azure portalından](https://portal.azure.com) oluşturun
> * Önceden oluşturulmuş [Özel Komutlar uygulaması](quickstart-custom-commands-application.md)
> * Bir konuşma SDK 'Sı etkin istemci uygulaması: [nasıl yapılır: konuşma SDK 'sını kullanarak istemci uygulamasıyla tümleştirme](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Etkinliği istemciye Gönder ' i ayarla 
1. Daha önce oluşturduğunuz özel komutlar uygulamasını açın
1. **Turnonoff** komutunu seçin, tamamlanma kuralı altında **ConfirmationResponse** öğesini seçin, sonra **Eylem Ekle** ' yi seçin.
1. **Yeni eylem türü** altında, **etkinliği istemciye gönder** ' i seçin
1. Aşağıdaki JSON 'ı **etkinlik içeriğine** Kopyala
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
1. Etkinlik Gönder eylemiyle yeni bir kural oluşturmak için **Kaydet** ' e tıklayın, değişikliği **eğitme** ve **yayımlayın**

   > [!div class="mx-imgBorder"]
   > ![Etkinlik tamamlama kuralını gönder](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>İstemci uygulamasıyla tümleştirme

[Nasıl yapılır: istemci uygulamasını konuşma SDK 'sı (Önizleme) Ile ayarlama](./how-to-custom-commands-setup-speech-sdk.md)bölümünde, konuşma SDK 'sı ile gibi komutları ele alan bir UWP istemci uygulaması oluşturdunuz `turn on the tv` `turn off the fan` . Bazı görseller eklendikçe, bu komutların sonucunu görebilirsiniz.

**Açık** veya **kapalı** şeklinde metin içeren etiketli kutular eklemek Için, aşağıdaki XML StackPanel bloğunu öğesine ekleyin `MainPage.xaml` .

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>Başvuru kitaplıkları ekleme

Bir JSON yükü oluşturduğunuzdan, seri durumdan çıkarmayı işlemek için [JSON.net](https://www.newtonsoft.com/json) kitaplığına bir başvuru eklemeniz gerekir.

1. Çözümünüz için sağ istemci.
1. **Çözüm Için NuGet Paketlerini Yönet**' i seçin, **Araştır** ' ı seçin 
1. **ÜzerindeNewtonsoft.js** zaten yüklüyse sürümünün en az 12.0.3 olduğundan emin olun. Aksi takdirde, **çözüm Için NuGet Paketlerini Yönet-güncelleştirmeler**' e gidin, güncelleştirmek için **üzerindeNewtonsoft.js** arayın. Bu kılavuz, 12.0.3 sürümünü kullanıyor.

    > [!div class="mx-imgBorder"]
    > ![Etkinlik yükünü gönder](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Ayrıca, **Microsoft. NETCore. Üniversalwindowsplatform** adlı NuGet paketinin en az 6.2.10 olduğundan emin olun. Bu kılavuz, 6.2.10 sürümünü kullanıyor.

' MainPage. xaml. cs ' içinde, Ekle

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>Alınan yükü işle

İçinde `InitializeDialogServiceConnector` , `ActivityReceived` olay işleyicisini aşağıdaki kodla değiştirin. Değiştirilen `ActivityReceived` olay işleyicisi yükü etkinlikten çıkaracaktır ve TV veya fanı 'ın görsel durumunu sırasıyla değiştirir.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.value?.state != null ? activity.value.state.ToString() : string.Empty;
        var device = activity?.value?.device != null ? activity.value.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Deneyin

1. Uygulamayı başlatma
1. Mikrofonu etkinleştir'i seçin
1. Konuş düğmesini seçin
1. `turn on the tv` deyin
1. TV 'nin görsel durumu "açık" olarak değiştirilmelidir
   > [!div class="mx-imgBorder"]
   > ![T V 'nin görsel durumunun artık açık olduğunu gösteren ekran görüntüsü.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Nasıl yapılır: Web uç noktalarını ayarlama](./how-to-custom-commands-setup-web-endpoints.md)
