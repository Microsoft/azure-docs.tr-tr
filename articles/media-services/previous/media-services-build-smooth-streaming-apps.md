---
title: Windows Mağazası uygulaması öğreticisini Kesintisiz Akış | Microsoft Docs
description: Kesintisiz akış içeriğini kayıttan yürütmek için XML MediaElement C# denetimiyle bir Windows Mağazası uygulaması oluşturmak üzere Azure Media Services nasıl kullanacağınızı öğrenin.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 9ff961638aa170948d51793a21e86d18dd7e1d80
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "69016785"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Kesintisiz Akış Windows Mağazası uygulaması oluşturma  

Windows 8 için Kesintisiz Akış Istemci SDK 'Sı, geliştiricilerin talep üzerine ve canlı Kesintisiz Akış içeriği oynatacak Windows Mağazası uygulamaları oluşturmalarına olanak sağlar. SDK, Kesintisiz Akış içeriğin temel oynatımına ek olarak Microsoft PlayReady koruması, kalite düzeyi kısıtlaması, canlı DVR, ses akışı değiştirme, durum güncelleştirmelerini dinleme, (kalite düzeyi değişiklikler gibi) gibi zengin özellikler de sağlar ve hata olayları vb. Desteklenen özellikler hakkında daha fazla bilgi için [sürüm notlarına](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes)bakın. Daha fazla bilgi için bkz. [Windows 8 Için oynatıcı çerçeve](https://playerframework.codeplex.com/). 

Bu öğretici dört ders içerir:

1. Temel bir Kesintisiz Akış Mağazası uygulaması oluşturma
2. Medya Ilerlemesini denetlemek için kaydırıcı çubuğu ekleme
3. Kesintisiz Akış akışları seçin
4. Kesintisiz Akış Izleri seçin

## <a name="prerequisites"></a>Önkoşullar
> [!NOTE]
> Windows Mağazası projeleri sürüm 8,1 ve önceki sürümleri Visual Studio 2017 ' de desteklenmez.  Daha fazla bilgi için bkz. [Visual Studio 2017 Platform Desteği ve Uyumluluk](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

* Windows 8 32-bit veya 64 bit.
* Visual Studio 2012 ile 2015 arası sürümler.
* [Windows 8 Için Microsoft kesintisiz akış CLIENT SDK](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

Her ders için tamamlanmış çözüm, MSDN Geliştirici kod örneklerinden (kod Galerisi) indirilebilir: 

* [1. ders](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) -basit bir Windows 8 kesintisiz akış Media Player, 
* [2. ders](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) -kaydırıcı çubuğu denetimiyle basit bir Windows 8 kesintisiz akış Media Player 
* [Ders 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) -akış seçimi Ile bir Windows 8 kesintisiz akış Media Player,  
* [4. ders](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) -kesintisiz akış bir Windows 8 Media Player izleme seçimi.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Ders 1: Temel bir Kesintisiz Akış Mağazası uygulaması oluşturma

Bu derste, sorunsuz akış içeriğini oynatmak için MediaElement denetimiyle bir Windows Mağazası uygulaması oluşturacaksınız.  Çalışan uygulama şöyle görünür:

![Kesintisiz Akış Windows Mağazası uygulaması örneği][PlayerApplication]

Windows Mağazası uygulaması geliştirme hakkında daha fazla bilgi için bkz. [Windows 8 Için harika uygulamalar geliştirme](https://msdn.microsoft.com/windows/apps/br229512.aspx). Bu ders aşağıdaki yordamları içerir:

1. Windows Mağazası projesi oluşturma
2. Kullanıcı arabirimini tasarlama (XAML)
3. Arka plan kodu dosyasını değiştirme
4. Uygulamayı derleyin ve test edin

### <a name="to-create-a-windows-store-project"></a>Windows Mağazası projesi oluşturmak için

1. Visual Studio 'Yu çalıştırma; 2012 ile 2015 arasındaki sürümler desteklenir.
1. **DOSYA** menüsünde **Yeni**’ye ve sonra **Proje**’ye tıklayın.
1. Yeni proje iletişim kutusunda, aşağıdaki değerleri yazın veya seçin:

    | Ad | Value |
    | --- | --- |
    | Şablon grubu |Yüklü/şablonlar/Visual C#/Windows Mağazası |
    | Şablon |Boş uygulama (XAML) |
    | Ad |SSPlayer |
    | Location |C:\SSTutorials |
    | Çözüm adı |SSPlayer |
    | Çözüm için dizin oluştur |seçildiğinde |

1.           **Tamam**'ı tıklatın.

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>Kesintisiz Akış Istemci SDK 'sına bir başvuru eklemek için

1. Çözüm Gezgini, **Ssplayer**öğesine sağ tıklayın ve ardından **Başvuru Ekle**' ye tıklayın.
1. Aşağıdaki değerleri yazın veya seçin:

    | Ad | Value |
    | --- | --- |
    | Başvuru grubu |Pencereler/uzantılar |
    | Başvuru |Windows 8 ve Microsoft Visual C++ Runtime paketi için Microsoft kesintisiz akış istemci SDK 'sını seçin |

1.           **Tamam**'ı tıklatın. 

Başvuruları ekledikten sonra, hedeflenen platformu (x64 veya x86) seçmeniz gerekir. başvuruları eklemek herhangi bir CPU platformu yapılandırması için çalışmaz.  Çözüm Gezgini 'nde, bu eklenen başvurular için sarı bir uyarı işareti görürsünüz.

### <a name="to-design-the-player-user-interface"></a>Oynatıcı Kullanıcı arabirimini tasarlamak için

1. Çözüm Gezgini ' den, **MainPage. xaml** ' ye çift tıklayarak Tasarım görünümünde açın.
2. Grid ve **/Gridetiketlerini&gt; xaml dosyasında bulun ve aşağıdaki kodu iki etiket arasına yapıştırın: &lt;** **&lt;&gt;**

   ```xml
         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="https://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
         </StackPanel>

         <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
         </StackPanel>

         <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
         </StackPanel>
   ```
   MediaElement denetimi medyayı kayıttan yürütmek için kullanılır. SliderProgress adlı kaydırıcı denetimi, sonraki derste medya ilerlemesini denetlemek için kullanılacaktır.
3. Dosyayı kaydetmek için **CTRL + S** tuşlarına basın.

MediaElement denetimi Kesintisiz Akış içeriği kullanıma hazır olarak desteklemiyor. Kesintisiz Akış desteğini etkinleştirmek için, Kesintisiz Akış Byte-Stream işleyicisini dosya adı uzantısına ve MIME türüne göre kaydetmeniz gerekir.  Kaydolmak için, Windows. Media ad alanının MediaExtensionManager. RegisterByteStreamHandler yöntemini kullanırsınız.

Bu XAML dosyasında, bazı olay işleyicileri denetimlerle ilişkilendirilir.  Bu olay işleyicilerini tanımlamanız gerekir.

### <a name="to-modify-the-code-behind-file"></a>Arka plan kodu dosyasını değiştirmek için

1. Çözüm Gezgini, **MainPage. xaml**öğesine sağ tıklayın ve ardından **kodu görüntüle**' ye tıklayın.
2. Dosyasının en üstüne aşağıdaki using ifadesini ekleyin:
   
        using Windows.Media;
3. **MainPage** sınıfının başlangıcında aşağıdaki veri üyesini ekleyin:
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. **MainPage** oluşturucusunun sonunda aşağıdaki iki satırı ekleyin:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. **MainPage** sınıfının sonunda, aşağıdaki kodu yapıştırın:
   ```csharp
         # region UI Button Click Events
         private void btnPlay_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Play();
         txtStatus.Text = "MediaElement is playing ...";
         }
         private void btnPause_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Pause();
         txtStatus.Text = "MediaElement is paused";
         }
         private void btnSetSource_Click(object sender, RoutedEventArgs e)
         {

         sliderProgress.Value = 0;
         mediaElement.Source = new Uri(txtMediaSource.Text);

         if (chkAutoPlay.IsChecked == true)
         {
             txtStatus.Text = "MediaElement is playing ...";
         }
         else
         {
             txtStatus.Text = "Click the Play button to play the media source.";
         }
         }
         private void btnStop_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Stop();
         txtStatus.Text = "MediaElement is stopped";
         }
         private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
         {

         txtStatus.Text = "Seek to position " + sliderProgress.Value;
         mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
         }
         # endregion
   ```
   SliderProgress_PointerPressed olay işleyicisi burada tanımlanmıştır.  Bu öğreticinin bir sonraki dersinde ele alınacak olan çalışmaya yönelik daha fazla çalışma vardır.
6. Dosyayı kaydetmek için **CTRL + S** tuşlarına basın.

Arka plan kodu dosyasının bitmesi şuna benzer:

![Windows Mağazası uygulaması Kesintisiz Akış Visual Studio 'da CodeView][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>Uygulamayı derlemek ve test etmek için

1. **BUILD** menüsünden **Configuration Manager**' ye tıklayın.
2. **Etkin çözüm platformunu** geliştirme Platformunuzla eşleşecek şekilde değiştirin.
3. Projeyi derlemek için **F6** tuşuna basın. 
4. Uygulamayı çalıştırmak için **F5**'e basın.
5. Uygulamanın üst kısmında, varsayılan Kesintisiz Akış URL 'sini kullanabilir ya da farklı bir tane girebilirsiniz. 
6. **Kaynağı ayarla**' ya tıklayın. **Otomatik yürütme** varsayılan olarak etkinleştirildiğinden, medya otomatik olarak oynatılır.  **Oynat**, **Duraklat** ve **Durdur** düğmelerini kullanarak medyayı kontrol edebilirsiniz.  Dikey kaydırıcıyı kullanarak medya birimini kontrol edebilirsiniz.  Ancak medya ilerlemesini denetlemeye yönelik yatay kaydırıcı henüz tam olarak uygulanmamıştır. 

Lesson1 tamamladınız.  Bu derste, Kesintisiz Akış içeriğini oynatmak için bir MediaElement denetimi kullanırsınız.  Sonraki derste, Kesintisiz Akış içeriğin ilerlemesini denetlemek için bir kaydırıcı ekleyeceksiniz.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>2\. ders: Medya Ilerlemesini denetlemek için kaydırıcı çubuğu ekleme

1\. derste, Kesintisiz Akış medya içeriğini kayıttan yürütmek için MediaElement XAML denetimiyle bir Windows Mağazası uygulaması oluşturdunuz.  Başlat, durdur ve Duraklat gibi bazı temel medya işlevlerini sunar.  Bu derste, uygulamaya bir kaydırıcı çubuğu denetimi ekleyeceksiniz.

Bu öğreticide, MediaElement denetiminin geçerli konumuna göre kaydırıcı konumunu güncelleştirmek için bir Zamanlayıcı kullanacağız.  Kaydırıcı başlangıç ve bitiş saatinin de canlı içerik olması durumunda güncelleştirilmeleri gerekir.  Bu, uyarlamalı kaynak güncelleştirme olayında daha iyi işlenebilir.

Medya kaynakları, medya verileri oluşturan nesnelerdir.  Kaynak çözümleyici bir URL veya bayt akışı alır ve bu içerik için uygun medya kaynağını oluşturur.  Kaynak çözümleyici, uygulamaların medya kaynakları oluşturması için standart bir yoldur. 

Bu ders aşağıdaki yordamları içerir:

1. Kesintisiz Akış işleyicisini kaydetme 
2. Uyarlamalı Kaynak Yöneticisi düzeyi olay işleyicilerini ekleme
3. Uyarlamalı kaynak düzeyi olay işleyicilerini ekleme
4. MediaElement olay işleyicileri ekleme
5. Kaydırıcı çubukla ilgili kodu ekle
6. Uygulamayı derleyin ve test edin

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>Kesintisiz Akış Byte-Stream işleyicisini kaydetmek ve PropertySet 'i geçirmek için

1. Çözüm Gezgini, **MainPage. xaml**öğesine sağ tıklayın ve ardından **kodu görüntüle**' ye tıklayın.
2. Dosyanın başlangıcında, aşağıdaki using ifadesini ekleyin:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. MainPage sınıfının başlangıcında aşağıdaki veri üyelerini ekleyin:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. **MainPage** oluşturucusunun içinde, bundan sonra aşağıdaki kodu ekleyin **. Bileşenleri başlatın ();** önceki derste yazılan satır ve kayıt kodu satırları:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. **MainPage** oluşturucusunun içinde, Iki RegisterByteStreamHandler yöntemini değiştirerek ileri parametrelerini ekleyin:

   ```csharp
         // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
         // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
         // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
         propertySet);
   ```
6. Dosyayı kaydetmek için **CTRL + S** tuşlarına basın.

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>Uyarlamalı Kaynak Yöneticisi düzeyi olay işleyicisini eklemek için

1. Çözüm Gezgini, **MainPage. xaml**öğesine sağ tıklayın ve ardından **kodu görüntüle**' ye tıklayın.
2. **MainPage** sınıfının içinde aşağıdaki veri üyesini ekleyin:

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. **MainPage** sınıfının sonunda aşağıdaki olay işleyicisini ekleyin:

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. **Ana sayfa** oluşturucusunun sonunda, uyarlamalı kaynak aç olayına abone olmak için aşağıdaki satırı ekleyin:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Dosyayı kaydetmek için **CTRL + S** tuşlarına basın.

### <a name="to-add-adaptive-source-level-event-handlers"></a>Uyarlamalı kaynak düzeyi olay işleyicileri eklemek için

1. Çözüm Gezgini, **MainPage. xaml**öğesine sağ tıklayın ve ardından **kodu görüntüle**' ye tıklayın.
2. **MainPage** sınıfının içinde aşağıdaki veri üyesini ekleyin:

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. **MainPage** sınıfının sonunda aşağıdaki olay işleyicilerini ekleyin:

   ```csharp
         # region Adaptive Source Level Events
         private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
         }

         private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
         {

            adaptiveSourceStatusUpdate = args;
         }

         private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
         {

            txtStatus.Text = "Error: " + args.HttpResponse;
         }

         # endregion Adaptive Source Level Events
   ```
4. **MediaElement AdaptiveSourceOpened** yönteminin sonunda olaylara abone olmak için aşağıdaki kodu ekleyin:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Dosyayı kaydetmek için **CTRL + S** tuşlarına basın.

Aynı olaylar, uyarlamalı Kaynak Yöneticisi düzeyinde de mevcuttur ve bu, uygulamadaki tüm medya öğelerinde ortak işlevselliği işlemek için kullanılabilir. Her AdaptiveSource kendi olaylarını içerir ve tüm AdaptiveSource olayları AdaptiveSourceManager altında basamaklandıralınacaktır.

### <a name="to-add-media-element-event-handlers"></a>Medya öğesi olay işleyicileri eklemek için

1. Çözüm Gezgini, **MainPage. xaml**öğesine sağ tıklayın ve ardından **kodu görüntüle**' ye tıklayın.
2. **MainPage** sınıfının sonunda aşağıdaki olay işleyicilerini ekleyin:

   ```csharp
         # region Media Element Event Handlers
         private void MediaOpened(object sender, RoutedEventArgs e)
         {

            txtStatus.Text = "MediaElement opened";
         }

         private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
         {

            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
         }

         private void MediaEnded(object sender, RoutedEventArgs e)
         {

            txtStatus.Text ="MediaElement ended.";
         }

         # endregion Media Element Event Handlers
   ```
3. **MainPage** oluşturucusunun sonunda, olaylara alt indis eklemek için aşağıdaki kodu ekleyin:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Dosyayı kaydetmek için **CTRL + S** tuşlarına basın.

### <a name="to-add-slider-bar-related-code"></a>Kaydırıcı çubukla ilgili kodu eklemek için

1. Çözüm Gezgini, **MainPage. xaml**öğesine sağ tıklayın ve ardından **kodu görüntüle**' ye tıklayın.
2. Dosyanın başlangıcında, aşağıdaki using ifadesini ekleyin:

   ```csharp
        using Windows.UI.Core;
   ```
3. **MainPage** sınıfının içinde aşağıdaki veri üyelerini ekleyin:

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. **MainPage** oluşturucusunun sonunda aşağıdaki kodu ekleyin:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. **MainPage** sınıfının sonunda aşağıdaki kodu ekleyin:

   ```csharp
         # region sliderMediaPlayer
         private double SliderFrequency(TimeSpan timevalue)
         {

            long absvalue = 0;
            double stepfrequency = -1;

            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }

            TimeSpan totalDVRDuration = new TimeSpan(absvalue);

            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }

            return stepfrequency;
         }

         void updateSliderPositionoNTicks(object sender, object e)
         {

            sliderProgress.Value = mediaElement.Position.TotalSeconds;
         }

         public void setupTimer()
         {

            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
         }

         public void startTimer()
         {

            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderStartTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderEndTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
         }

         # endregion sliderMediaPlayer
   ```

   > [!NOTE]
   > CoreDispatcher, UI olmayan Iş parçacığından kullanıcı arabirimi iş parçacığında değişiklik yapmak için kullanılır. Bir dağıtıcı iş parçacığı üzerinde performans sorunu olması durumunda, geliştirici, güncelleştirmek istedikleri Kullanıcı arabirimi öğesi tarafından sunulan Dispatcher 'ı kullanmayı seçebilir.  Örneğin:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. **MediaElement_AdaptiveSourceStatusUpdated** yönteminin sonuna aşağıdaki kodu ekleyin:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. **Mediaaçıldı** yönteminin sonunda aşağıdaki kodu ekleyin:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Dosyayı kaydetmek için **CTRL + S** tuşlarına basın.

### <a name="to-compile-and-test-the-application"></a>Uygulamayı derlemek ve test etmek için

1. Projeyi derlemek için **F6** tuşuna basın. 
2. Uygulamayı çalıştırmak için **F5**'e basın.
3. Uygulamanın üst kısmında, varsayılan Kesintisiz Akış URL 'sini kullanabilir ya da farklı bir tane girebilirsiniz. 
4. **Kaynağı ayarla**' ya tıklayın. 
5. Kaydırıcı çubuğunu test edin.

2\. ders tamamladınız.  Bu derste, uygulamaya bir kaydırıcı eklediniz. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Ders 3: Kesintisiz Akış akışları seçin
Kesintisiz Akış, görüntüleyiciler tarafından seçilebilen birden çok dil ses parçalarıyla içerik akışına olanak sağlar.  Bu derste, görüntüleyicilerin akış seçmesini sağlayacak şekilde etkinleştirecektir. Bu ders aşağıdaki yordamları içerir:

1. XAML dosyasını değiştirme
2. Arka plan kodu dosyasını değiştirme
3. Uygulamayı derleyin ve test edin

### <a name="to-modify-the-xaml-file"></a>XAML dosyasını değiştirmek için

1. Çözüm Gezgini, **MainPage. xaml**öğesine sağ tıklayın ve ardından **tasarımcıyı görüntüle**' ye tıklayın.
2. Grid &lt;. RowDefinitions&gt;' ı bulun ve RowDefinitions gibi görünen satır tanımlarını değiştirin:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. &lt;Kılavuz&gt;/Grid&gt; etiketleri içinde, bir ListBox denetimini tanımlamak için aşağıdaki kodu ekleyin, böylece kullanıcılar kullanılabilir akışlar listesini görebilir ve akışlar ' ı seçin:&lt;

   ```xml
         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
         </Grid>
   ```
4. Değişiklikleri kaydetmek için **CTRL + S** tuşlarına basın.

### <a name="to-modify-the-code-behind-file"></a>Arka plan kodu dosyasını değiştirmek için

1. Çözüm Gezgini, **MainPage. xaml**öğesine sağ tıklayın ve ardından **kodu görüntüle**' ye tıklayın.
2. SSPlayer ad alanı içinde yeni bir sınıf ekleyin:

   ```csharp
        #region class Stream
   
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
   
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
   
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
   
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream
   ```
3. MainPage sınıfının başlangıcında aşağıdaki değişken tanımlarını ekleyin:

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. MainPage sınıfının içinde aşağıdaki bölgeyi ekleyin:
   ```csharp
        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
   
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
   
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
   
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
   
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
   
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
   
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
   
            // Select the first video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
   
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
   
            // Select the first audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
   
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
   
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection
   ```
5. MediaElement_ManifestReady metodunu bulun, işlevin sonuna aşağıdaki kodu ekleyin:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Bu nedenle, MediaElement bildirimi hazır olduğunda, kod kullanılabilir akışlarının bir listesini alır ve UI liste kutusunu listeyle doldurur.
6. MainPage sınıfının içinde, UI düğmeleri ' ni tıklatın olaylar Bölgesi ' ni bulun ve aşağıdaki işlev tanımını ekleyin:
   ```csharp
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }
   ```

### <a name="to-compile-and-test-the-application"></a>Uygulamayı derlemek ve test etmek için

1. Projeyi derlemek için **F6** tuşuna basın. 
2. Uygulamayı çalıştırmak için **F5**'e basın.
3. Uygulamanın üst kısmında, varsayılan Kesintisiz Akış URL 'sini kullanabilir ya da farklı bir tane girebilirsiniz. 
4. **Kaynağı ayarla**' ya tıklayın. 
5. Varsayılan dil audio_eng ' dir. Audio_eng ve audio_es arasında geçiş yapmayı deneyin. Her seferinde yeni bir akış seçersiniz, Gönder düğmesine tıklamanız gerekir.

3\. ders tamamladınız.  Bu derste, akışları seçmek için işlevselliği eklersiniz.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Ders 4: Kesintisiz Akış izleri seçin

Kesintisiz Akış sunusu, farklı kalite düzeyleri (bit hızları) ve çözünürlüklerle kodlanmış birden çok video dosyası içerebilir. Bu derste, kullanıcıların izleri seçmesini sağlayacak. Bu ders aşağıdaki yordamları içerir:

1. XAML dosyasını değiştirme
2. Arka plan kodu dosyasını değiştirme
3. Uygulamayı derleyin ve test edin

### <a name="to-modify-the-xaml-file"></a>XAML dosyasını değiştirmek için

1. Çözüm Gezgini, **MainPage. xaml**öğesine sağ tıklayın ve ardından **tasarımcıyı görüntüle**' ye tıklayın.
2. **Gridstreadikbitrateselection**adlı&gt; Gridetiketinibulun,etiketinsonunaaşağıdakikoduekleyin:&lt;
   ```xml
         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
         </StackPanel>
   ```
3. Değişiklikleri kaydetmek için **CTRL + S** tuşlarına basın

### <a name="to-modify-the-code-behind-file"></a>Arka plan kodu dosyasını değiştirmek için

1. Çözüm Gezgini, **MainPage. xaml**öğesine sağ tıklayın ve ardından **kodu görüntüle**' ye tıklayın.
2. SSPlayer ad alanı içinde yeni bir sınıf ekleyin:
   ```csharp
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
   
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
   
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
   
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track
   ```
3. MainPage sınıfının başlangıcında aşağıdaki değişken tanımlarını ekleyin:
   ```csharp
        private List<Track> availableTracks;
   ```
4. MainPage sınıfının içinde aşağıdaki bölgeyi ekleyin:
   ```csharp
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>
   
        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();
   
            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;
   
            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;
   
                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }
   
        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }
   
        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }
   
        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }
   
        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection
   ```
5. MediaElement_ManifestReady metodunu bulun, işlevin sonuna aşağıdaki kodu ekleyin:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. MainPage sınıfının içinde, UI düğmeleri ' ni tıklatın olaylar Bölgesi ' ni bulun ve aşağıdaki işlev tanımını ekleyin:
   ```csharp
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }
   ```
   
### <a name="to-compile-and-test-the-application"></a>Uygulamayı derlemek ve test etmek için

1. Projeyi derlemek için **F6** tuşuna basın. 
2. Uygulamayı çalıştırmak için **F5**'e basın.
3. Uygulamanın üst kısmında, varsayılan Kesintisiz Akış URL 'sini kullanabilir ya da farklı bir tane girebilirsiniz. 
4. **Kaynağı ayarla**' ya tıklayın. 
5. Varsayılan olarak, video akışının tüm parçaları seçilidir. Bit hızı değişikliklerini denemek için kullanılabilir en düşük bit hızını seçip kullanılabilir en yüksek bit oranını seçebilirsiniz. Her değişiklikten sonra Gönder ' e tıklamanız gerekir.  Video kalitesi değişikliklerini görebilirsiniz.

4\. ders tamamladınız.  Bu derste, parçaları seçmek için işlevselliği eklersiniz.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Diğer kaynaklar:
* [Gelişmiş özelliklerle Kesintisiz Akış Windows 8 JavaScript uygulaması oluşturma](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Kesintisiz Akış teknik genel bakış](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

