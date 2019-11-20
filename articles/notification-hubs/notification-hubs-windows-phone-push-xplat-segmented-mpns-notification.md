---
title: Azure Notification Hubs kullanarak belirli Windows phone’lara anında iletme bildirimleri gönderme | Microsoft Docs
description: Bu öğreticide, uygulama arka ucuna kayıtlı belirli (tümü değil) Windows Phone 8 veya Windows Phone 8.1 cihazlarına anında iletme bildirimleri göndermek için Azure Notification Hubs’ın nasıl kullanılacağını öğrenirsiniz.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 42726bf5-cc82-438d-9eaa-238da3322d80
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 88326b07a96dcc8ce7a72f8709a88c2662f48707
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213401"
---
# <a name="tutorial-push-notifications-to-specific-windows-phone-devices-by-using-azure-notification-hubs"></a>Öğretici: Azure Notification Hubs kullanarak belirli Windows Phone cihazlara anında iletme bildirimleri gönderin

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

Bu öğreticide, belirli Windows Phone 8 veya Windows Phone 8.1 Scihazlara anında iletme bildirimleri göndermek için Azure Notification Hubs’ın nasıl kullanılacağı gösterilmektedir. Windows Phone 8.1’i (Silverlight olmayan) hedefliyorsanız bu öğreticinin [Windows Evrensel](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) sürümüne bakın.

Bildirim hub’ında bir kayıt oluştururken bir veya daha fazla *etiketi* dahil ederek bu senaryoyu etkinleştirirsiniz. Bir etikete bildirimler gönderildiğinde, etikete kaydolan tüm cihazlar bildirimi alır. Etiketler hakkında daha fazla bilgi için bkz. [Kayıtlardaki etiketler](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Notification Hubs Windows Phone SDK'sı, Windows Anında Bildirim Hizmeti'ni (WNS) Windows Phone 8.1 Silverlight uygulamaları ile kullanmayı desteklemez. WNS’yi (MPNS yerine) Windows Phone 8.1 Silverlight uygulamaları ile kullanmak için REST API’ler kullanan [Notification Hubs - Windows Phone Silverlight öğreticisini] izleyin.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Mobil uygulamaya kategori seçimi ekleme
> * Etiketlerle bildirimlere kaydolma
> * Etiketli bildirimler gönderme
> * Uygulamayı test etme

## <a name="prerequisites"></a>Önkoşullar

[Öğreticiyi doldurun: Azure Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md)kullanarak Windows Phone uygulamalara anında iletme bildirimleri gönderin. Bu öğreticide, ilginizi çeken son dakika haberi kategorilerine kaydolabilmeniz ve yalnızca bu kategoriler için anında iletme bildirimleri alabilmeniz için mobil uygulamayı güncelleştirirsiniz.

## <a name="add-category-selection-to-the-mobile-app"></a>Mobil uygulamaya kategori seçimi ekleme

İlk adım, mevcut ana sayfanıza kullanıcının kaydolunacak kategorileri seçmesini sağlayan UI öğeleri eklemektir. Bir kullanıcı tarafından seçilen kategoriler cihazda depolanır. Uygulama başlatıldığında, etiketler olarak seçilen kategorilerle bildirim hub’ınızda bir cihaz kaydı oluşturulur.

1. Dosyasını açın, `TitlePanel` ve `Grid` adlı`ContentPanel` öğeleri aşağıdaki kodla değiştirin: `MainPage.xaml`

    ```xml
    <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
        <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
        <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
    </StackPanel>

    <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
        <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
        <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
        <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
        <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
        <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
2. Projeye adlı `Notifications` bir sınıf ekleyin. `public` Değiştirici sınıf tanımına ekleyin. Ardından, aşağıdaki `using` deyimlerini yeni dosyaya ekleyin:

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    using System.IO.IsolatedStorage;
    using System.Windows;
    ```
3. Aşağıdaki kodu yeni `Notifications` sınıfa kopyalayın:

    ```csharp
    private NotificationHub hub;

    // Registration task to complete registration in the ChannelUriUpdated event handler
    private TaskCompletionSource<Registration> registrationTask;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
        return categories != null ? categories.Split(',') : new string[0];
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        var categoriesAsString = string.Join(",", categories);
        var settings = IsolatedStorageSettings.ApplicationSettings;
        if (!settings.Contains("categories"))
        {
            settings.Add("categories", categoriesAsString);
        }
        else
        {
            settings["categories"] = categoriesAsString;
        }
        settings.Save();

        return await SubscribeToCategories();
    }

    public async Task<Registration> SubscribeToCategories()
    {
        registrationTask = new TaskCompletionSource<Registration>();

        var channel = HttpNotificationChannel.Find("MyPushChannel");

        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
            channel.ChannelUriUpdated += channel_ChannelUriUpdated;

            // This is optional, used to receive notifications while the app is running.
            channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
        }

        // If channel.ChannelUri is not null, complete the registrationTask here.  
        // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
        if (channel.ChannelUri != null)
        {
            await RegisterTemplate(channel.ChannelUri);
        }

        return await registrationTask.Task;
    }

    async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
    {
        await RegisterTemplate(e.ChannelUri);
    }

    async Task<Registration> RegisterTemplate(Uri channelUri)
    {
        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                            "<wp:Toast>" +
                                                "<wp:Text1>$(messageParam)</wp:Text1>" +
                                            "</wp:Toast>" +
                                        "</wp:Notification>";

        // The stored categories tags are passed with the template registration.

        registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(),
            templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

        return await registrationTask.Task;
    }

    // This is optional. It is used to receive notifications while the app is running.
    void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
    {
        StringBuilder message = new StringBuilder();
        string relativeUri = string.Empty;

        message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

        // Parse out the information that was part of the message.
        foreach (string key in e.Collection.Keys)
        {
            message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

            if (string.Compare(
                key,
                "wp:Param",
                System.Globalization.CultureInfo.InvariantCulture,
                System.Globalization.CompareOptions.IgnoreCase) == 0)
            {
                relativeUri = e.Collection[key];
            }
        }

        // Display a dialog of all the fields in the toast.
        System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
        {
            MessageBox.Show(message.ToString());
        });
    }
    ```

    Bu sınıf, bu cihazın alacağı haber kategorilerini depolamak için yalıtılmış depolamayı kullanır. Ayrıca bir [şablon](notification-hubs-templates-cross-platform-push-messages.md) bildirim kaydı kullanılarak bu kategorilere kaydolma yöntemlerini de içerir.
4. Proje dosyasında aşağıdaki özelliği `App` sınıfına ekleyin. `App.xaml.cs` `<hub name>` ve `<connection string with listen access>` yer tutucularını, daha önce edindiğiniz bildirim hub'ı adınız ve *DefaultListenSharedAccessSignature* bağlantı dizeniz ile değiştirin.

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

   > [!NOTE]
   > Bir istemci uygulaması ile dağıtılmış kimlik bilgileri genellikle güvenli olmadığından yalnızca istemci uygulamanızla dinleme erişimi için anahtarı dağıtmanız gerekir. Dinleme erişimi, uygulamanızın bildirimlere kaydolmasını sağlar, ancak mevcut kayıtlar değiştirilemez ve bildirimler gönderilemez. Tam erişim anahtarı, güvenli bir arka uç hizmetinde bildirimler göndermek ve mevcut kayıtları değiştirmek için kullanılır.

5. `MainPage.xaml.cs`İçinde, aşağıdaki satırı ekleyin:

    ```csharp
    using Windows.UI.Popups;
    ```
6. MainPage.xaml.cs proje dosyasına aşağıdaki yöntemi ekleyin:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldCheckBox.IsChecked == true) categories.Add("World");
        if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
        if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
        if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
        if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
        if (SportsCheckBox.IsChecked == true) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
            result.RegistrationId);
    }
    ```

    Bu yöntem, kategorilerin bir listesini oluşturur ve bu `Notifications` sınıfı kullanarak listeyi yerel depoda depolar ve Bildirim Hub 'ınızla ilgili etiketleri kaydeder. Kategoriler değiştirildiğinde kayıt yeni kategorilerle yeniden oluşturulur.

Uygulamalarınız artık bir kategori kümesini cihazdaki yerel depolama alanında depolayabilir ve kullanıcının kategori seçimini her değiştirmesinde bildirim hub’ına kaydolabilir.

## <a name="register-for-notifications"></a>Bildirimlere kaydolma

Bu adımlar, yerel depolama alanında depolanan kategorileri kullanarak başlatma sırasında bildirim hub’ına kaydolur.

> [!NOTE]
> Microsoft Anında Bildirim Hizmeti (MPNS) tarafından atanan kanal URI’si her zaman değişebileceğinden, bildirim hatalarını önlemek için sık sık bildirimlere kaydolmanız gerekir. Bu örnek, uygulama her başlatıldığında bildirimlere kaydolur. Sık sık çalıştırılan uygulamalar için, önceki kayıttan bu yana bir günden az zaman geçtiyse bant genişliğini korumak için günde birkaç kere kaydı atlayabilirsiniz.

1. App.xaml.cs dosyasını açın ve `async` `Application_Launching` yöntemine değiştiricisini ekleyin ve aşağıdaki kodla [Notification Hubs’ı kullanmaya başlama] bölümünde eklediğiniz Notification Hubs kayıt kodunu değiştirin:

    ```csharp
    private async void Application_Launching(object sender, LaunchingEventArgs e)
    {
        var result = await notifications.SubscribeToCategories();

        if (result != null)
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
    }
    ```

    Bu kod, uygulama her başlatıldığında uygulamanın yerel depolama alanından kategorileri aldığından ve bu kategorilere kayıt isteğinde bulunduğundan emin olur.
2. MainPage.xaml.cs proje dosyasında, `OnNavigatedTo` yöntemi uygulayan aşağıdaki kodu ekleyin:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
        if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
        if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
        if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
        if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
        if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
    }
    ```

    Bu kod, önceden kaydedilmiş kategorilerin durumuna göre ana sayfayı güncelleştirir.

Uygulama artık tamamlanmıştır ve kullanıcının kategori seçimini her değiştirmesinde bildirim hub’ına kaydolmak için kullanılan cihazın yerel depolama alanında bir kategori kümesini depolayabilir. Daha sonra bu uygulamaya kategori bildirimleri gönderebilen bir arka uç tanımlayın.

## <a name="send-tagged-notifications"></a>Etiketli bildirimler gönderme

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Uygulamayı test etme

1. Visual Studio’da F5 tuşuna basarak uygulamayı derleyin ve başlatın.

    ![Kategoriler ile mobil uygulama][1]

    Uygulama kullanıcı arabirimi, abone olunacak kategorileri seçmenize olanak sağlayan iki durumlu düğmeler sağlar.
2. Bir veya daha fazla kategori iki durumlu düğmesini etkinleştirin ve **Abone ol**’a tıklayın.

    Uygulama, seçilen kategorileri etiketlere dönüştürür ve bildirim hub’ından seçilen etiketler için yeni bir cihaz kaydı ister. Kayıtlı kategoriler döndürülür ve bir iletişim kutusunda görüntülenir.

    ![Abone olunan ileti][2]
3. Kategorilerin aboneliğinin tamamlandığına dair bir onay aldıktan sonra, her kategoriye yönelik bildirimler göndermek için konsol uygulamasını çalıştırın. Yalnızca abone olduğunuz kategorileri için bir bildirim aldığınızı doğrulayın.

    ![Bildirim iletisi][3]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, kayıtlarıyla ilişkili etiketleri olan belirli cihazlara anında iletme bildirimleri göndermeyi öğrendiniz. Birden fazla cihaz kullanıyor olabilecek belirli kullanıcılara nasıl anında iletme bildirimleri gönderileceğini öğrenmek için aşağıdaki öğreticiye ilerleyin: 

> [!div class="nextstepaction"]
>[Belirli kullanıcılara anında iletme bildirimleri gönderme](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png

<!-- URLs.-->
[Notification Hubs’ı kullanmaya başlama]: notification-hubs-windows-mobile-push-notifications-mpns.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??
