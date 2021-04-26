---
title: Konuşma SDK’sını kullanarak bir istemci uygulamasıyla tümleştirme
titleSuffix: Azure Cognitive Services
description: bir UWP uygulamasında çalışan konuşma SDK 'sından yayımlanmış özel komutlar uygulamasına istek yapma.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: fa3a6d16b79800043bdcd3f183dd86fa278dd1a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026036"
---
# <a name="integrate-with-a-client-application-using-speech-sdk"></a>Konuşma SDK 'sını kullanarak bir istemci uygulamasıyla tümleştirme

Bu makalede, bir UWP uygulamasında çalışan konuşma SDK 'sından yayımlanmış bir özel komutlar uygulamasına istek yapmayı öğreneceksiniz. Özel komutlar uygulamasıyla bağlantı kurmak için şunlar gerekir:

- Özel bir komut uygulaması yayımlama ve uygulama tanımlayıcısı (uygulama KIMLIĞI)
- Özel komutlar uygulamanızla iletişim kurmasını sağlamak için konuşma SDK 'sını kullanarak bir Evrensel Windows Platformu (UWP) istemci uygulaması oluşturun

## <a name="prerequisites"></a>Önkoşullar

Bu makalenin tamamlanabilmesi için özel bir komutlar uygulaması gereklidir. Özel bir komutlar uygulaması oluşturmadıysanız, hızlı başlangıçlardan sonra bunu yapabilirsiniz:
> [!div class = "checklist"]
> * [Özel komut uygulaması oluşturma](quickstart-custom-commands-application.md)

Ayrıca şunları da yapmanız gerekir:
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) veya üzeri. Bu kılavuz, Visual Studio 2019 ' i temel alır.
> * Konuşma Hizmetleri için bir Azure abonelik anahtarı. [Bir tane ücretsiz alın](overview.md#try-the-speech-service-for-free) veya [Azure Portal](https://portal.azure.com) oluşturun
> * [Cihazınızı geliştirme için etkinleştirme](/windows/uwp/get-started/enable-your-device-for-development)

## <a name="step-1-publish-custom-commands-application"></a>1. Adım: özel komutlar uygulaması yayımlama

1. Önceden oluşturulmuş özel komutlar uygulamanızı açın
1. **Ayarlar**' a gidin, **Luo kaynağı** ' nı seçin.
1. **Tahmin kaynağı** atanmamışsa, bir sorgu tahmin tuşu seçin veya yeni bir tane oluşturun

    Bir uygulamayı yayımlamadan önce sorgu tahmin anahtarı her zaman gereklidir. LUSıS kaynakları hakkında daha fazla bilgi için bkz. [Lusıs kaynağı oluşturma](../luis/luis-how-to-azure-subscription.md)

1. Düzenle komutlarına geri dönün, **Yayımla** ' yı seçin.

   > [!div class="mx-imgBorder"]
   > ![Uygulama yayımlama](media/custom-commands/setup-speech-sdk-publish-application.png)

1. Uygulama KIMLIĞINI daha sonra kullanmak üzere yayımlama bildiriminden Kopyala
1. Daha sonra kullanmak üzere konuşma kaynak anahtarını kopyalama

## <a name="step-2-create-a-visual-studio-project"></a>2. Adım: Visual Studio projesi oluşturma

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>3. Adım: örnek kod ekleme

Bu adımda, uygulamanın kullanıcı arabirimini tanımlayan XAML kodunu ekler ve C# arka plan kod uygulamasını eklersiniz.

### <a name="xaml-code"></a>XAML kodu

XAML kodunu ekleyerek uygulamanın kullanıcı arabirimini oluşturun.

1. **Çözüm Gezgini**' de açın`MainPage.xaml`

1. Tasarımcının XAML görünümünde, tüm içeriği aşağıdaki kod parçacığı ile değiştirin:

   ```xml
   <Page
       x:Class="helloworld.MainPage"
       xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       xmlns:local="using:helloworld"
       xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
       xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
       mc:Ignorable="d"
       Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

       <Grid>
           <StackPanel Orientation="Vertical" HorizontalAlignment="Center"
                       Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
               <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"
                       Margin="0,10,10,0" Click="EnableMicrophone_ButtonClicked"
                       Height="35"/>
               <Button x:Name="ListenButton" Content="Talk"
                       Margin="0,10,10,0" Click="ListenButton_ButtonClicked"
                       Height="35"/>
               <StackPanel x:Name="StatusPanel" Orientation="Vertical"
                           RelativePanel.AlignBottomWithPanel="True"
                           RelativePanel.AlignRightWithPanel="True"
                           RelativePanel.AlignLeftWithPanel="True">
                   <TextBlock x:Name="StatusLabel" Margin="0,10,10,0"
                              TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                   <Border x:Name="StatusBorder" Margin="0,0,0,0">
                       <ScrollViewer VerticalScrollMode="Auto"
                                     VerticalScrollBarVisibility="Auto" MaxHeight="200">
                           <!-- Use LiveSetting to enable screen readers to announce
                                the status update. -->
                           <TextBlock
                               x:Name="StatusBlock" FontWeight="Bold"
                               AutomationProperties.LiveSetting="Assertive"
                               MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}"
                               Margin="10,10,10,20" TextWrapping="Wrap"  />
                       </ScrollViewer>
                   </Border>
               </StackPanel>
           </StackPanel>
           <MediaElement x:Name="mediaElement"/>
       </Grid>
   </Page>
   ```

Tasarım görünümü, uygulamanın kullanıcı arabirimini gösterecek şekilde güncelleştirilir.

### <a name="c-code-behind-source"></a>C# arka plan kod kaynağı

Uygulamanın beklendiği gibi çalışması için arka plan kod kaynağını ekleyin. Arka plan kod kaynağı şunları içerir:

- `using` `Speech` Ve ad alanları için gerekli `Speech.Dialog` deyimler
- Bir düğme işleyicisine kablolu, mikrofon erişimi sağlamak için basit bir uygulama
- Uygulamada ileti ve hata sunmak için temel kullanıcı arabirimi yardımcıları
- Daha sonra doldurulacak başlatma kodu yolu için bir giriş noktası
- Metin okuma (akış desteği olmadan) çalmak için bir yardımcı
- Daha sonra doldurulacak dinlemeye başlamak için boş bir düğme işleyicisi

Arka plan kod kaynağını aşağıdaki gibi ekleyin:

1. **Çözüm Gezgini**' de, arka plan kod kaynak dosyasını açın `MainPage.xaml.cs` (altında gruplanır `MainPage.xaml` )

1. Dosyanın içeriğini aşağıdaki kodla değiştirin: 

   ```csharp
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Audio;
   using Microsoft.CognitiveServices.Speech.Dialog;
   using System;
   using System.IO;
   using System.Text;
   using Windows.UI.Xaml;
   using Windows.UI.Xaml.Controls;
   using Windows.UI.Xaml.Media;

   namespace helloworld
   {
       public sealed partial class MainPage : Page
       {
           private DialogServiceConnector connector;

           private enum NotifyType
           {
               StatusMessage,
               ErrorMessage
           };

           public MainPage()
           {
               this.InitializeComponent();
           }

           private async void EnableMicrophone_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               bool isMicAvailable = true;
               try
               {
                   var mediaCapture = new Windows.Media.Capture.MediaCapture();
                   var settings =
                       new Windows.Media.Capture.MediaCaptureInitializationSettings();
                   settings.StreamingCaptureMode =
                       Windows.Media.Capture.StreamingCaptureMode.Audio;
                   await mediaCapture.InitializeAsync(settings);
               }
               catch (Exception)
               {
                   isMicAvailable = false;
               }
               if (!isMicAvailable)
               {
                   await Windows.System.Launcher.LaunchUriAsync(
                       new Uri("ms-settings:privacy-microphone"));
               }
               else
               {
                   NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
               }
           }

           private void NotifyUser(
               string strMessage, NotifyType type = NotifyType.StatusMessage)
           {
               // If called from the UI thread, then update immediately.
               // Otherwise, schedule a task on the UI thread to perform the update.
               if (Dispatcher.HasThreadAccess)
               {
                   UpdateStatus(strMessage, type);
               }
               else
               {
                   var task = Dispatcher.RunAsync(
                       Windows.UI.Core.CoreDispatcherPriority.Normal,
                       () => UpdateStatus(strMessage, type));
               }
           }

           private void UpdateStatus(string strMessage, NotifyType type)
           {
               switch (type)
               {
                   case NotifyType.StatusMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Green);
                       break;
                   case NotifyType.ErrorMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Red);
                       break;
               }
               StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text)
                   ? strMessage : "\n" + strMessage;

               if (!string.IsNullOrEmpty(StatusBlock.Text))
               {
                   StatusBorder.Visibility = Visibility.Visible;
                   StatusPanel.Visibility = Visibility.Visible;
               }
               else
               {
                   StatusBorder.Visibility = Visibility.Collapsed;
                   StatusPanel.Visibility = Visibility.Collapsed;
               }
               // Raise an event if necessary to enable a screen reader
               // to announce the status update.
               var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
               if (peer != null)
               {
                   peer.RaiseAutomationEvent(
                       Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
               }
           }

           // Waits for and accumulates all audio associated with a given
           // PullAudioOutputStream and then plays it to the MediaElement. Long spoken
           // audio will create extra latency and a streaming playback solution
           // (that plays audio while it continues to be received) should be used --
           // see the samples for examples of this.
           private void SynchronouslyPlayActivityAudio(
               PullAudioOutputStream activityAudio)
           {
               var playbackStreamWithHeader = new MemoryStream();
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
               playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
               playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

               byte[] pullBuffer = new byte[2056];

               uint lastRead = 0;
               do
               {
                   lastRead = activityAudio.Read(pullBuffer);
                   playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
               }
               while (lastRead == pullBuffer.Length);

               var task = Dispatcher.RunAsync(
                   Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                   mediaElement.SetSource(
                       playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                   mediaElement.Play();
               });
           }

           private void InitializeDialogServiceConnector()
           {
               // New code will go here
           }

           private async void ListenButton_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               // New code will go here
           }
       }
   }
   ```
    > [!NOTE]
    > Hata görürseniz: "Object ' türü, başvurulmayan bir derlemede tanımlanmıştır"
    > 1. Çözümünüz için sağ istemci.
    > 1. **Çözüm Için NuGet Paketlerini Yönet**' i seçin, **güncelleştirmeler** ' i seçin 
    > 1. Güncelleştirme listesinde **Microsoft. netcore. üniversalwindowsplatform** görürseniz, **Microsoft. Netcore. üniversalwindowsplatform** ' ı en yeni sürüme güncelleştirin

1. Aşağıdaki kodu ' ın yöntem gövdesine ekleyin `InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. Dize, `YourApplicationId` `YourSpeechSubscriptionKey` `YourServiceRegion` konuşma aboneliğiniz ve [bölgeniz](regions.md) için, ve değerlerini kendi değerlerinizle değiştirin

1. Aşağıdaki kod parçacığını, öğesinin Yöntem gövdesinin sonuna ekleyin `InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += (sender, activityReceivedEventArgs) =>
   {
       NotifyUser(
           $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

       if (activityReceivedEventArgs.HasAudio)
       {
           SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
       }
   };

   // Canceled will be signaled when a turn is aborted or experiences an error condition
   connector.Canceled += (sender, canceledEventArgs) =>
   {
       NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
       if (canceledEventArgs.Reason == CancellationReason.Error)
       {
           NotifyUser(
               $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
       }
   };

   // Recognizing (not 'Recognized') will provide the intermediate recognized text
   // while an audio stream is being processed
   connector.Recognizing += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
   };

   // Recognized (not 'Recognizing') will provide the final recognized text
   // once audio capture is completed
   connector.Recognized += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
   };

   // SessionStarted will notify when audio begins flowing to the service for a turn
   connector.SessionStarted += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
   };

   // SessionStopped will notify when a turn is complete and
   // it's safe to begin listening again
   connector.SessionStopped += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
   };
   ```

1. Aşağıdaki kod parçacığını `ListenButton_ButtonClicked` sınıfındaki yönteminin gövdesine ekleyin `MainPage`

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. Değişikliklerinizi kaydetmek için menü çubuğundan **Dosya**  >  **Tümünü Kaydet** ' i seçin

## <a name="try-it-out"></a>Deneyin

1. Menü çubuğundan   >  uygulamayı derlemek için derleme **Build Solution** ' ı seçin. Kodun hatasız derlenmesi gerekir.

1.   >  Uygulamayı başlatmak için hata **ayıklamayı Başlat** ' ı seçin (veya **F5** tuşuna basın). **HelloWorld** penceresi görüntülenir.

   ![C# ' de örnek UWP Sanal Yardımcısı uygulaması-hızlı başlangıç](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. **Mikrofonu etkinleştir**' i seçin. Erişim izni isteği açılır ise, **Evet**' i seçin.

   ![Mikrofon erişimi izin isteği](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **Konuş**' u seçin ve cihazınızın mikrofonuna bir İngilizce tümcecik veya cümle konuşun. Konuşma, doğrudan hat konuşma kanalına iletilir ve pencerede görüntülenen metne kaydedilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Nasıl yapılır: etkinliği istemci uygulamasına gönderme (Önizleme)](./how-to-custom-commands-send-activity-to-client.md)