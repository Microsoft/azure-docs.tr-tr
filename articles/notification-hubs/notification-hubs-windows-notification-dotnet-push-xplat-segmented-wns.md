---
title: Belirli cihazlara bildirim gönderme (Evrensel Windows Platformu) | Microsoft Docs
description: Bir Evrensel Windows Platformu uygulamasına son dakika haberleri göndermek için kayıtta etiketlerle birlikte Azure Notification Hubs kullanın.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 9151870836b1a616a79e54275ed185a425c11f0c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72385613"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>Öğretici: Evrensel Windows Platformu uygulamaları çalıştıran belirli cihazlara bildirimler gönderme

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Genel Bakış

Bu öğreticide, son haberler bildirimleri yayınlamak için Azure Notification Hubs nasıl kullanılacağı gösterilmektedir. Bu öğretici, Windows Mağazası veya Windows Phone 8,1 (Silverlight olmayan) uygulamalarını içerir. Windows Phone 8,1 Silverlight 'ı hedefliyorsanız, bkz. [Azure Notification Hubs kullanarak belirli Windows Phone cihazlara anında iletme bildirimleri gönderme](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

Bu öğreticide, bir Evrensel Windows Platformu (UWP) uygulaması çalıştıran belirli Windows cihazlarına bildirimleri göndermek için Azure Notification Hubs kullanmayı öğreneceksiniz. Öğreticiyi tamamladıktan sonra ilgilendiğiniz son haber kategorilerine kaydolabilirsiniz. Yalnızca bu kategoriler için anında iletme bildirimleri alacaksınız.

Yayın senaryolarını etkinleştirmek için, Bildirim Hub 'ında bir kayıt oluştururken bir veya daha fazla *etiket* ekleyin. Bildirimler bir etikete gönderildiğinde, etiket için kaydedilen tüm cihazlar bildirimi alır. Etiketler hakkında daha fazla bilgi için bkz. [Yönlendirme ve etiket ifadeleri](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Windows Mağazası ve Windows Phone proje sürümleri 8,1 ve önceki sürümleri Visual Studio 2019 ' de desteklenmez. Daha fazla bilgi için bkz. [Visual Studio 2019 Platform hedefleme ve uyumluluk](/visualstudio/releases/2019/compatibility).

Bu öğreticide, aşağıdaki görevleri gerçekleştirebilirsiniz:

> [!div class="checklist"]
> * Mobil uygulamaya kategori seçimi ekleme
> * Bildirimlere kaydolma
> * Etiketli bildirimler gönderme
> * Uygulamayı çalıştırma ve bildirimler oluşturma

## <a name="prerequisites"></a>Önkoşullar

Öğreticiyi doldurun: Bu Öğreticiyi başlatmadan önce [Azure Notification Hubs kullanarak Evrensel Windows platformu uygulamalara bildirim gönderin][get-started] .  

## <a name="add-category-selection-to-the-app"></a>Uygulamaya kategori seçimi ekleme

İlk adım, mevcut ana sayfanıza kullanıcının kaydolmak için kategorileri seçebileceği UI öğeleri eklemektir. Seçilen kategoriler cihazda depolanır. Uygulama başlatıldığında, Bildirim Hub 'ında seçili kategorileri Etiketler olarak içeren bir cihaz kaydı oluşturur.

1. *MainPage. xaml* proje dosyasını açın ve sonra `Grid` öğesinde aşağıdaki kodu kopyalayın:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
    </Grid>
    ```

1. **Çözüm Gezgini**, projeye sağ tıklayın,  >  sınıfı **Ekle**' yi seçin. **Yeni öğe Ekle**' de, sınıf *bildirimlerini*adlandırın ve **Ekle**' yi seçin. Gerekirse, sınıf tanımına `public` değiştiricisini ekleyin.

1. Aşağıdaki `using` deyimlerini yeni dosyaya ekleyin:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. Aşağıdaki kodu yeni `Notifications` sınıfına kopyalayın:

    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```

    Bu sınıf, bu cihazın alması gereken haber kategorilerini depolamak için yerel depolamayı kullanır. @No__t-0 yöntemini çağırmak yerine, bir şablon kaydı kullanarak kategorilere kaydolmak için `RegisterTemplateAsync` ' i çağırın.

    Birden fazla şablon kaydetmek istiyorsanız, bir şablon adı sağlayın (örneğin, *simpleWNSTemplateExample*). Şablonları güncelleştirebilmeniz veya silebilmeniz için adlandırmanız gerekir. Bildirim için bir tane ve bir kutucuk için bir tane olmak üzere birden fazla şablon kaydedebilirsiniz.

    >[!NOTE]
    > Notification Hubs, bir cihaz aynı etiketi kullanarak birden çok şablonu kaydedebilir. Bu durumda, etiketi hedefleyen bir gelen ileti, her şablon için bir tane olmak üzere cihaza birden çok bildirim teslim edildiğinde oluşur. Bu işlem, bir Windows Mağazası uygulamasında hem rozet hem de bildirim olarak aynı iletiyi birden çok görsel bildirimde görüntülemenizi sağlar.

    Daha fazla bilgi için bkz. [Şablonlar](notification-hubs-templates-cross-platform-push-messages.md).

1. *App.xaml.cs* proje dosyasında, `App` sınıfına aşağıdaki özelliği ekleyin:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Bu özelliği `Notifications` örneği oluşturmak ve erişmek için kullanırsınız.

    Kod içindeki `<hub name>` ve `<connection string with listen access>` yer tutucularını, daha önce edindiğiniz bildirim hub'ı adınız ve **DefaultListenSharedAccessSignature** bağlantı dizeniz ile değiştirin.

   > [!NOTE]
   > Bir istemci uygulaması ile dağıtılmış kimlik bilgileri genellikle güvenli olmadığından yalnızca istemci uygulamanızla *dinleme* erişimi için anahtarı dağıtın. Dinleme erişimi ile uygulamanızın bildirimlere kaydolmasını sağlar, ancak mevcut kayıtlar değiştirilemez ve bildirimler gönderilemez. Tam erişim anahtarı, güvenli bir arka uç hizmetinde bildirimler göndermek ve mevcut kayıtları değiştirmek için kullanılır.

1. *MainPage.xaml.cs* dosyasında, aşağıdaki satırı ekleyin:

    ```csharp
    using Windows.UI.Popups;
    ```

1. *MainPage.xaml.cs* dosyasında aşağıdaki yöntemi ekleyin:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    Bu yöntem, kategorilerin bir listesini oluşturur ve listeyi yerel depolama alanında depolamak için `Notifications` sınıfını kullanır. Ayrıca ilgili etiketleri bildirim hub’ınıza kaydeder. Kategoriler değiştiğinde, kayıt yeni kategoriler ile yeniden oluşturulur.

Uygulamanız artık cihazın yerel depolama alanında bir kategori kümesini depolayabilir. Uygulama, kullanıcılar kategori seçimini her değiştirdiğinde bildirim hub’ına kaydolur.

## <a name="register-for-notifications"></a>Bildirimlere kaydolma

Bu bölümde, yerel depolama alanında depoladığınız kategorileri kullanarak başlatma sırasında bildirim hub’ına kaydolursunuz.

> [!NOTE]
> Windows Bildirim Hizmeti (WNS) tarafından atanan kanal URI’si her zaman değişebileceğinden, bildirim hatalarını önlemek için sık sık bildirimlere kaydolmanız gerekir. Bu örnek, uygulama her başlatıldığında bildirimlere kaydolur. Sıklıkla çalıştırdığınız uygulamalar için günde bir kereden fazla, bir günden daha az bir kez geçtiğinde bant genişliğini korumak için kayıt işlemini atlayabilirsiniz.

1. Kategoriler temelinde abone olmak için `notifications` sınıfını kullanmak için, *app.xaml.cs* dosyasını açın ve sonra `InitNotificationsAsync` metodunu güncelleştirin.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Bu işlem, uygulama başlatıldığında yerel depolama alanından kategorileri alan bir zaman sağlar. Daha sonra bu kategorilerin kaydını ister. [Azure Notification Hubs öğreticisini kullanarak Evrensel Windows platformu uygulamalara gönderilen bildirimlerin][get-started] bir parçası olarak `InitNotificationsAsync` yöntemini oluşturdunuz.
2. *MainPage.xaml.cs* proje dosyasında, `OnNavigatedTo` yöntemine aşağıdaki kodu ekleyin:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    Bu kod, önceden kaydedilmiş kategorilerin durumuna göre ana sayfayı güncelleştirir.

Uygulamanız artık tamamlandı. Cihaz yerel depolama alanında bir kategori kümesini depolayabilirler. Kullanıcılar kategori seçimini değiştirmişken, kaydedilen Kategoriler Bildirim Hub 'ına kaydolmak için kullanılır. Sonraki bölümde, bu uygulamaya kategori bildirimleri gönderebilen bir arka uç tanımlayacaksınız.

## <a name="run-the-uwp-app"></a>UWP uygulamasını çalıştırma

1. Visual Studio 'da F5 ' i seçerek uygulamayı derleyip başlatın. Uygulama kullanıcı arabirimi, abone olunacak kategorileri seçmenize olanak sağlayan iki durumlu düğmeler sağlar.

   ![Son Dakika Haberleri uygulaması](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Bir veya daha fazla kategori modunu etkinleştirin ve sonra **abone ol**' u seçin.

   Uygulama, seçilen kategorileri etiketlere dönüştürür ve bildirim hub’ından seçilen etiketler için yeni bir cihaz kaydı ister. Uygulama, kayıtlı kategorileri bir iletişim kutusunda görüntüler.

    ![Kategori iki durumlu düğmeleri ve Abone ol düğmesi](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Etiketli bildirimler göndermek için bir konsol uygulaması oluşturma

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>Etiketli bildirimler göndermek için konsol uygulamasını çalıştırma

Önceki bölümde oluşturulan uygulamayı çalıştırın. Seçili kategorilere ait bildirimler, bildirim olarak görünür.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, son dakika haberlerini kategoriye göre yayınlamayı öğrendiniz. Arka uç uygulaması, etiketlenmiş bildirimleri bu etikete yönelik bildirimler almak üzere kayıtlı cihazlara gönderir. Bildirimlerin kullandıkları cihazdan bağımsız olarak belirli kullanıcılara nasıl göndereceğinizi öğrenmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Azure Notification Hubs kullanarak Windows uygulamalarına yerelleştirilmiş bildirimler gönderme](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
