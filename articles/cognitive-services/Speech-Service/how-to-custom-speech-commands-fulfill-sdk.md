---
title: Konuşma SDK 'Sı ile istemcideki özel komutları yerine getirmek
titleSuffix: Azure Cognitive Services
description: Bu makalede konuşma SDK 'Sı ile istemcideki özel komut etkinliklerini işleyin
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 2da8ef2c29bd6afdaf49e000bf964d119f1e99f1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74110080"
---
# <a name="how-to-fulfill-commands-on-the-client-with-the-speech-sdk-preview"></a>Nasıl yapılır: konuşma SDK 'Sı ile istemcide komutları yerine alma (Önizleme)

Özel bir komut uygulaması kullanarak görevleri gerçekleştirmek için, bağlı bir istemci cihazına özel yük gönderebilirsiniz.

Bu makalede şunları yapmanız gerekir:

- Özel komutlar uygulamanızdan özel bir JSON yükü tanımlama ve gönderme
- C# UWP konuşma SDK 'sı istemci UYGULAMASıNDAN özel JSON yükü içeriğini alma ve görselleştirme

## <a name="prerequisites"></a>Önkoşullar

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Konuşma Hizmetleri için bir Azure abonelik anahtarı
  - [Bir tane ücretsiz alın](get-started.md) veya [Azure Portal](https://portal.azure.com) oluşturun
- Önceden oluşturulmuş özel komutlar uygulaması
  - [Hızlı başlangıç: parametrelerle özel komut oluşturma (Önizleme)](./quickstart-custom-speech-commands-create-parameters.md)
- Bir konuşma SDK 'Sı etkin istemci uygulaması
  - [Hızlı başlangıç: konuşma SDK 'Sı ile özel bir komut uygulamasına bağlanma (Önizleme)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>İsteğe bağlı: hızlı başlangıç

Bu makalede, adım adım, bir istemci uygulamanın özel komutlar uygulamanızla nasıl konuştuğu nasıl yapılacağı açıklanmaktadır. Hemen ' yi kullanmayı tercih ediyorsanız, bu makalede kullanılan tam, kullanıma hazır kaynak kodu [konuşma SDK örnekleri](https://aka.ms/csspeech/samples)bölümünde bulunur.

## <a name="fulfill-with-json-payload"></a>JSON yükü ile yerine

1. Daha önce oluşturduğunuz özel komutlar uygulamanızı [konuşma Studio](https://speech.microsoft.com/) 'dan açın
1. Kullanıcıya geri yanıt veren daha önce oluşturulmuş kurala sahip olduğunuzdan emin olmak için **tamamlama kuralları** bölümüne bakın
1. Doğrudan istemciye yük göndermek için, etkinlik Gönder eylemiyle yeni bir kural oluşturun

   > [!div class="mx-imgBorder"]
   > Etkinlik tamamlanma kuralını gönder ![](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Ayar | Önerilen değer | Açıklama |
   | ------- | --------------- | ----------- |
   | Kural Adı | Güncelleştirme Devemlak | Kuralın amacını açıklayan bir ad |
   | Koşullar | Gerekli parametre-`OnOff` ve `SubjectDevice` | Kuralın ne zaman çalıştırılabilmesine belirleme koşulları |
   | Eylemler | `SendActivity` (aşağıya bakın) | Kural koşulu true olduğunda gerçekleştirilecek eylem |

   > [!div class="mx-imgBorder"]
   > Etkinlik yükünü gönder](media/custom-speech-commands/fulfill-sdk-send-activity-action.png) ![

   ```json
   {
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Cihaz üzerinde veya Kapalı durumuna görsel oluşturma

[Hızlı başlangıç: konuşma SDK 'sı (Önizleme) Ile özel bir komut uygulamasına bağlanma](./quickstart-custom-speech-commands-speech-sdk.md) `turn on the tv`, `turn off the fan`gibi komutları ele alan bir konuşma SDK 'sı istemci uygulaması oluşturdunuz. Şimdi bu komutların sonucunu görebilmeniz için bazı görseller ekleyin.

Aşağıdaki **XML 'nin eklenmiş olduğunu belirten** metin içeren etiketli kutular ekleyin `MainPage.xaml.cs`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Özelleştirilebilir yükü işle

Artık bir JSON yükü oluşturduğunuza göre, seri durumdan çıkarmayı işlemek için [JSON.net](https://www.newtonsoft.com/json) kitaplığına bir başvuru ekleyebilirsiniz.

> [!div class="mx-imgBorder"]
> Etkinlik yükünü gönder](media/custom-speech-commands/fulfill-sdk-json-nuget.png) ![

`InitializeDialogServiceConnector` `ActivityReceived` olay işleyicinizde aşağıdakileri ekleyin. Ek kod, etkinliğin yükünü ayıklar ve TV veya fan 'nin görsel durumunu uygun şekilde değiştirir.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var payload = activity?.Value;

    if(payload?.name == "SetDeviceState")
    {
        var state = payload?.state;
        var device = payload?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
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
1. Mikrofonu etkinleştir ' i seçin
1. Konuşma düğmesini seçin
1. `turn on the tv` söyleyin
1. TV 'nin görsel durumu "açık" olarak değiştirilmelidir

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Nasıl yapılır: özel komut parametrelerine doğrulama ekleme (Önizleme)](./how-to-custom-speech-commands-validations.md)
