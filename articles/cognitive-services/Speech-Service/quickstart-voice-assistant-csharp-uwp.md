---
title: 'Hızlı başlangıç: özel ses Yardımcısı C# , (UWP)-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Bu makalede bilişsel hizmetler konuşma C# yazılım geliştirme seti 'NI (SDK) kullanarak bir evrensel WINDOWS platformu (UWP) uygulaması oluşturacaksınız. İstemci uygulamanızı, doğrudan hat konuşma kanalını kullanacak şekilde yapılandırılmış daha önce oluşturulmuş bir bot Framework bot 'a bağlanırsınız. Uygulama, konuşma SDK 'Sı NuGet paketiyle oluşturulmuştur ve 2019 Microsoft Visual Studio.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 75ecbe8351e68b77a59b40709a2beb15d09b16df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504091"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-uwp"></a>Hızlı başlangıç: konuşma SDK 'Sı ile bir ses Yardımcısı oluşturma, UWP

Hızlı başlangıçlara [konuşma tanıma](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp), [konuşma birleştirmesinin](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=uwp)ve [konuşma çevirisi](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)için de erişilebilir.

Bu makalede, [konuşma SDK 'sını](speech-sdk.md)kullanarak bir C# Evrensel Windows platformu (UWP) uygulaması geliştirirsiniz. Program, istemci uygulamasından bir ses Yardımcısı deneyimini etkinleştirmek için önceden yazılmış ve yapılandırılmış bir bot 'a bağlanır. Uygulama, [konuşma SDK 'Sı NuGet paketi](https://aka.ms/csspeech/nuget) ve Microsoft Visual Studio 2019 (herhangi bir sürüm) ile oluşturulmuştur.

> [!NOTE]
> Evrensel Windows Platformu; PC, Xbox, Surface Hub ve diğer cihazlar dahil olmak üzere Windows 10 destekleyen tüm cihazlarda çalışan uygulamalar geliştirmenize olanak tanır.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç şunları gerektirir:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Konuşma Hizmetleri için bir Azure abonelik anahtarı. [Ücretsiz olarak bir tane alın](get-started.md) veya [Azure Portal](https://portal.azure.com)oluşturun.
* [Doğrudan hat konuşma kanalı](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)ile yapılandırılmış daha önce oluşturulmuş bir bot.

  > [!NOTE]
  > Lütfen [sesli Yardımcılar için desteklenen bölgeler listesine](regions.md#voice-assistants) başvurun ve kaynaklarınızın bu bölgelerden birinde dağıtıldığından emin olun.

## <a name="optional-get-started-fast"></a>İsteğe bağlı: hızlı başlangıç

Bu hızlı başlangıçta, bir istemci uygulamasını konuşma özellikli bot 'a nasıl bağlayacaksınız, adım adım açıklanır. Hemen ' yi kullanmayı tercih ediyorsanız, bu hızlı başlangıçta kullanılan tam, kullanıma hazır kaynak kodu `quickstart` klasörü altındaki [konuşma SDK örneklerinde](https://aka.ms/csspeech/samples) bulunur.

## <a name="create-a-visual-studio-project"></a>Visual Studio projesi oluşturma

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Örnek kodu ekleyin

Şimdi uygulamanın kullanıcı arabirimini tanımlayan XAML kodunu ekleyin ve arka plan C# kod uygulamasını ekleyin.

### <a name="xaml-code"></a>XAML kodu

Önce, XAML kodunu ekleyerek uygulamanın kullanıcı arabirimini oluşturacaksınız:

1. **Çözüm Gezgini**' de `MainPage.xaml`açın.

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
                        Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" 
                        Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" 
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

### <a name="c-code-behind-source"></a>C#arka plan kod kaynağı

Daha sonra uygulamanın beklendiği gibi çalışması için arka plan kod kaynağı eklersiniz. Arka plan kod kaynağı şunları içerir:

- `Speech` ve `Speech.Dialog` ad alanları için `using` deyimleri
- Bir düğme işleyicisine kablolu, mikrofon erişimi sağlamak için basit bir uygulama
- Uygulamada ileti ve hata sunmak için temel kullanıcı arabirimi yardımcıları
- Daha sonra doldurulacak başlatma kodu yolu için bir giriş noktası
- Metin okuma (akış desteği olmadan) çalmak için bir yardımcı
- Daha sonra doldurulacak dinlemeye başlamak için boş bir düğme işleyicisi

Arka plan kod kaynağını eklemek için aşağıdaki adımları izleyin:

1. **Çözüm Gezgini**, arka plan kod kaynak dosyası `MainPage.xaml.cs`açın. (`MainPage.xaml`altında gruplandırılır.)

1. Dosyanın içeriğini aşağıdaki kod parçacığı ile değiştirin:

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
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

1. Aşağıdaki kod parçacığını `InitializeDialogServiceConnector`yöntem gövdesine ekleyin. Bu kod, abonelik bilgilerinizi içeren `DialogServiceConnector` oluşturur.

    ```csharp
    // Create a BotFrameworkConfig by providing a Speech Service subscription key
    // the RecoLanguage property is optional (default en-US)
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
    const string region = "YourServiceRegion"; // Your subscription service region.

    var botConfig = BotFrameworkConfig.FromSubscription(speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

   > [!NOTE]
   > Lütfen [sesli Yardımcılar için desteklenen bölgeler listesine](regions.md#voice-assistants) başvurun ve kaynaklarınızın bu bölgelerden birinde dağıtıldığından emin olun.

   > [!NOTE]
   > Botunuzu yapılandırma ve Kanal gizliliğini alma hakkında daha fazla bilgi için, [doğrudan hat konuşma kanalının](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)bot Framework belgelerine bakın.

1. `YourChannelSecret`, `YourSpeechSubscriptionKey`ve `YourServiceRegion` dizelerini, bot, konuşma aboneliğiniz ve [bölgeniz](regions.md)için kendi değerlerinizle değiştirin.

1. `InitializeDialogServiceConnector`yöntemi gövdesinin sonuna aşağıdaki kod parçacığını ekleyin. Bu kod, bot etkinliklerini, konuşma tanıma sonuçlarını ve diğer bilgileri iletmek için `DialogServiceConnector` tarafından bir bağımlılık olayları ayarlar.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client 
    // and uses bot framework activities
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

1. Aşağıdaki kod parçacığını `MainPage` sınıfındaki `ListenButton_ButtonClicked` yönteminin gövdesine ekleyin. Bu kod, yapılandırmayı zaten kurup olay işleyicilerini kaydettirdiğiniz için dinlemek üzere `DialogServiceConnector` ayarlar.

    ```csharp
    if (connector == null)
    {
        InitializeDialogServiceConnector();
        // Optional step to speed up first interaction: if not called, 
        // connection happens automatically on first use
        var connectTask = connector.ConnectAsync();
    }

    try
    {
        // Start sending audio to your speech-enabled bot
        var listenTask = connector.ListenOnceAsync();

        // You can also send activities to your bot as JSON strings -- 
        // Microsoft.Bot.Schema can simplify this
        string speakActivity = 
            @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
        await connector.SendActivityAsync(speakActivity);

    }
    catch (Exception ex)
    {
        NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
    }
    ```

1. Değişikliklerinizi kaydetmek için menü çubuğundan **dosya** > **Tümünü Kaydet** ' i seçin.

## <a name="build-and-run-the-application"></a>Uygulamayı derleme ve çalıştırma

Artık uygulamanızı derlemek ve test etmek için hazırsınız.

1. Menü çubuğundan, uygulamayı derlemek için **build** > **Build Solution** öğesini seçin. Kodun artık hatasız derlenmesi gerekir.

1. Uygulamayı başlatmak için hata **ayıklamayı başlatın** > **Hata Ayıkla** ' yı seçin (veya **F5**tuşuna basın). **HelloWorld** penceresi görüntülenir.

   ![Hızlı başlangıçta örnek UWP ses Yardımcısı C# uygulaması](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. **Mikrofonu etkinleştir**' i seçin ve erişim izni Isteği açıldığında **Evet**' i seçin.

   ![Mikrofon erişimi izin isteği](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **Botunuz Için konuşun**' ı seçin ve cihazınızın mikrofonuna bir İngilizce tümcecik veya cümle konuşun. Konuşma, doğrudan hat konuşma kanalına iletilir ve pencerede görüntülenen metne kaydedilir.
<!--
    ![Successful bot response](media/voice-assistants/quickstart-cs-uwp-bot-successful-turn.png)
-->
## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Temel bot oluşturma ve dağıtma](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Ayrıca bkz.

- [Sesli yardımcılar hakkında](voice-assistants.md)
- [Ücretsiz bir konuşma Hizmetleri abonelik anahtarı alın](get-started.md)
- [Özel anahtar sözcükler](speech-devices-sdk-create-kws.md)
- [Doğrudan hat konuşmayı bot 'a bağlama](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [GitHub C# 'daki örnekleri keşfet](https://aka.ms/csspeech/samples)
