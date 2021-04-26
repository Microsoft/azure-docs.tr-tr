---
title: Redsıs için Azure önbelleği ile bir ASP.NET Web uygulaması oluşturma
description: Bu hızlı başlangıçta, Redsıs için Azure önbelleği ile bir ASP.NET Web uygulaması oluşturmayı öğreneceksiniz.
author: yegu-ms
ms.service: cache
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: yegu
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 19c54ad62e45ecf6e31b46d0291f61dca8e8d9b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722472"
---
# <a name="quickstart-use-azure-cache-for-redis-with-an-aspnet-web-app"></a>Hızlı başlangıç: ASP.NET Web uygulamasıyla Redsıs için Azure önbelleğini kullanma 

Bu hızlı başlangıçta, önbellekteki verileri depolamak ve almak üzere Redsıs için Azure önbelleğine bağlanan bir ASP.NET Web uygulaması oluşturmak için Visual Studio 2019 ' u kullanırsınız. Ardından uygulamayı Azure App Service ' ye dağıtırsınız.

## <a name="skip-to-the-code-on-github"></a>GitHub 'daki koda atlayın

Koda doğrudan atlamak istiyorsanız GitHub 'da [ASP.net hızlı başlangıç](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/aspnet) bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/dotnet)
- **ASP.net ve Web geliştirme** ve **Azure geliştirme** Iş yükleriyle [Visual Studio 2019](https://www.visualstudio.com/downloads/) .

## <a name="create-the-visual-studio-project"></a>Visual Studio projesini oluşturma

1. Visual Studio 'yu açın ve **Dosya**  >  **Yeni**  >  **Proje**' yi seçin.

2. **Yeni proje oluştur** iletişim kutusunda aşağıdaki adımları uygulayın:

    ![Proje oluşturma](./media/cache-web-app-howto/cache-create-project.png)

    a. Arama kutusuna _C# ASP.NET Web uygulaması_ girin.

    b. **ASP.NET Web uygulaması (.NET Framework)** seçeneğini belirleyin.

    c. **İleri**’yi seçin.

3. **Proje adı** kutusuna projeye bir ad verin. Bu örnekte biz **ContosoTeamStats** kullandık.

4. **.NET Framework 4.6.1** veya üzeri bir sürümü seçildiğini doğrulayın.

5. **Oluştur**’u seçin.
   
6. Proje türü olarak **MVC**’yi seçin.

7. **Kimlik Doğrulama** ayarları için **Kimlik Doğrulaması Yok** seçeneğinin belirtildiğinden emin olun. Visual Studio sürümünüze bağlı olarak, varsayılan **Kimlik Doğrulama** ayarı farklı olabilir. Değiştirmek için **Kimlik Doğrulamasını Değiştir**’i ve ardından **Kimlik Doğrulaması Yok**’u seçin.

8. Projeyi oluşturmak için **Oluştur**'u seçin.

## <a name="create-a-cache"></a>Bir önbellek oluşturma

Daha sonra, uygulama için önbellek oluşturursunuz.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

#### <a name="to-edit-the-cachesecretsconfig-file"></a>*CacheSecrets.config* dosyasını düzenlemek için

1. Bilgisayarınızda *CacheSecrets.config* adlı bir dosya oluşturun. Örnek uygulamanızın kaynak koduyla iade edilmeyeceği bir konuma koyun. Bu hızlı başlangıç için *CacheSecrets.config* dosyası şu konumda bulunur: *C:\AppSecrets\CacheSecrets.config*.

1. *CacheSecrets.config* dosyasını düzenleyin. Ardından aşağıdaki içeriği ekleyin:

    ```xml
    <appSettings>
        <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
    </appSettings>
    ```

1. `<cache-name>` adını, önbellek ana bilgisayar adınızla değiştirin.

1. `<access-key>` adını, önbelleğinizin birincil anahtarıyla değiştirin.

    > [!TIP]
    > Birincil erişim anahtarını yeniden oluştururken anahtar döndürme sırasında alternatif anahtar olarak ikincil erişim anahtarını kullanabilirsiniz.
   >
1. Dosyayı kaydedin.

## <a name="update-the-mvc-application"></a>MVC uygulamasını güncelleştirme

Bu bölümde, Redsıs için Azure önbelleğine yönelik basit bir testi görüntüleyen yeni bir görünümü destekleyecek şekilde uygulamayı güncelliyoruz.

* [Önbellek için uygulama ayarı ile web.config dosyasını güncelleştirme](#update-the-webconfig-file-with-an-app-setting-for-the-cache)
* StackExchange.Redis istemcisini kullanmak için uygulamayı yapılandırma
* HomeController ve Düzeni güncelleştirme
* Yeni RedisCache görünümü ekleme

### <a name="update-the-webconfig-file-with-an-app-setting-for-the-cache"></a>Önbellek için uygulama ayarı ile web.config dosyasını güncelleştirme

Uygulamayı yerel olarak çalıştırdığınızda *CacheSecrets.config* Içindeki bilgiler redsıs örneği Için Azure önbelleğinize bağlanmak için kullanılır. Daha sonra bu uygulamayı Azure’a dağıtırsınız. O aşamada, uygulamanın bu dosya yerine önbellek bağlantı bilgilerini almak için kullandığı Azure’daki bir uygulama ayarını yapılandırırsınız. 

*CacheSecrets.config* dosyası uygulamanızla Azure’a dağıtılmadığından bunu yalnızca uygulamayı yerel olarak test ederken kullanırsınız. Önbellek verilerinize kötü amaçlı erişimi önlemek için bu bilgileri olabildiğince güvende tutun.

#### <a name="to-update-the-webconfig-file"></a>*Web.config* dosyasını güncelleştirmek için
1. **Çözüm Gezgini**’nde, *web.config* dosyasına çift tıklayarak dosyayı açarsınız.

    ![Web.config](./media/cache-web-app-howto/cache-web-config.png)

2. *Web.config* dosyasında `<appSetting>` öğesini bulun. Ardından, aşağıdaki `file` özniteliğini ekleyin. Farklı bir dosya adı veya konumu kullandıysanız, örnekte gösterilenlerin yerine bu değerleri koyun.

* Önce: `<appSettings>`
* Sonra  `<appSettings file="C:\AppSecrets\CacheSecrets.config">`

ASP.NET çalışma zamanı, `<appSettings>` öğesindeki biçimlendirmeye sahip harici dosyasının içeriğini birleştirir. Belirtilen dosya bulunamazsa, çalışma zamanı dosya özniteliğini yok sayar. Gizli anahtarlarınız (önbelleğinize bağlantı dizisi) uygulamanız için kaynak kodun bir parçası olarak dahil edilmez. Web uygulamanızı Azure 'a dağıttığınızda *CacheSecrets.config* dosyası dağıtılmaz.

### <a name="to-configure-the-application-to-use-stackexchangeredis"></a>Uygulamayı StackExchange.Redis kullanacak şekilde yapılandırmak için

1. Visual Studio’da [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) NuGet paketi kullanmak üzere uygulamayı yapılandırmak için **Araçlar > NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu**'nu seçin.

2. `Package Manager Console` penceresinden aşağıdaki komutu çalıştırın:

    ```powershell
    Install-Package StackExchange.Redis
    ```

3. NuGet paketi, redsıs için Azure önbelleğine, Redsıs istemcisi için Azure Cache 'e erişmek üzere istemci uygulamanız için gerekli derleme başvurularını yükler ve ekler. `StackExchange.Redis` istemci kitaplığının tanımlayıcı adlı bir sürümünü kullanmak istiyorsanız `StackExchange.Redis.StrongName` paketini yükleyin.

### <a name="to-update-the-homecontroller-and-layout"></a>HomeController ve Layout'u güncelleştirmek için

1. **Çözüm Gezgini**’nde, **Denetleyiciler** klasörünü genişletin ve ardından *HomeController.cs* dosyasını açın.

2. Aşağıdaki `using` deyimlerini dosyanın en üstüne ekleyin.

    ```csharp
    using StackExchange.Redis;
    using System.Configuration;
    using System.Net.Sockets;
    using System.Text;
    using System.Threading;
    ```

3. `HomeController` `RedisCache` Yeni önbellekte bazı komutları çalıştıran yeni bir eylemi desteklemek için aşağıdaki üyeleri sınıfına ekleyin.

    ```csharp
    public ActionResult RedisCache()
    {
        ViewBag.Message = "A simple example with Azure Cache for Redis on ASP.NET.";

        IDatabase cache = GetDatabase();

        // Perform cache operations using the cache object...

        // Simple PING command
        ViewBag.command1 = "PING";
        ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

        // Simple get and put of integral data types into the cache
        ViewBag.command2 = "GET Message";
        ViewBag.command2Result = cache.StringGet("Message").ToString();

        ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET!\"";
        ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET!").ToString();

        // Demonstrate "SET Message" executed as expected...
        ViewBag.command4 = "GET Message";
        ViewBag.command4Result = cache.StringGet("Message").ToString();

        // Get the client list, useful to see if connection list is growing...
        // Note that this requires allowAdmin=true in the connection string
        ViewBag.command5 = "CLIENT LIST";
        StringBuilder sb = new StringBuilder();
        var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
        IServer server = GetServer(endpoint.Host, endpoint.Port);
        ClientInfo[] clients = server.ClientList();

        sb.AppendLine("Cache response :");
        foreach (ClientInfo client in clients)
        {
            sb.AppendLine(client.Raw);
        }

        ViewBag.command5Result = sb.ToString();

        return View();
    }

    private static long lastReconnectTicks = DateTimeOffset.MinValue.UtcTicks;
    private static DateTimeOffset firstErrorTime = DateTimeOffset.MinValue;
    private static DateTimeOffset previousErrorTime = DateTimeOffset.MinValue;

    private static readonly object reconnectLock = new object();

    // In general, let StackExchange.Redis handle most reconnects,
    // so limit the frequency of how often ForceReconnect() will
    // actually reconnect.
    public static TimeSpan ReconnectMinFrequency => TimeSpan.FromSeconds(60);

    // If errors continue for longer than the below threshold, then the
    // multiplexer seems to not be reconnecting, so ForceReconnect() will
    // re-create the multiplexer.
    public static TimeSpan ReconnectErrorThreshold => TimeSpan.FromSeconds(30);

    public static int RetryMaxAttempts => 5;

    private static Lazy<ConnectionMultiplexer> lazyConnection = CreateConnection();

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

    private static Lazy<ConnectionMultiplexer> CreateConnection()
    {
        return new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
    }

    private static void CloseConnection(Lazy<ConnectionMultiplexer> oldConnection)
    {
        if (oldConnection == null)
            return;

        try
        {
            oldConnection.Value.Close();
        }
        catch (Exception)
        {
            // Example error condition: if accessing oldConnection.Value causes a connection attempt and that fails.
        }
    }

    /// <summary>
    /// Force a new ConnectionMultiplexer to be created.
    /// NOTES:
    ///     1. Users of the ConnectionMultiplexer MUST handle ObjectDisposedExceptions, which can now happen as a result of calling ForceReconnect().
    ///     2. Don't call ForceReconnect for Timeouts, just for RedisConnectionExceptions or SocketExceptions.
    ///     3. Call this method every time you see a connection exception. The code will:
    ///         a. wait to reconnect for at least the "ReconnectErrorThreshold" time of repeated errors before actually reconnecting
    ///         b. not reconnect more frequently than configured in "ReconnectMinFrequency"
    /// </summary>
    public static void ForceReconnect()
    {
        var utcNow = DateTimeOffset.UtcNow;
        long previousTicks = Interlocked.Read(ref lastReconnectTicks);
        var previousReconnectTime = new DateTimeOffset(previousTicks, TimeSpan.Zero);
        TimeSpan elapsedSinceLastReconnect = utcNow - previousReconnectTime;

        // If multiple threads call ForceReconnect at the same time, we only want to honor one of them.
        if (elapsedSinceLastReconnect < ReconnectMinFrequency)
            return;

        lock (reconnectLock)
        {
            utcNow = DateTimeOffset.UtcNow;
            elapsedSinceLastReconnect = utcNow - previousReconnectTime;

            if (firstErrorTime == DateTimeOffset.MinValue)
            {
                // We haven't seen an error since last reconnect, so set initial values.
                firstErrorTime = utcNow;
                previousErrorTime = utcNow;
                return;
            }

            if (elapsedSinceLastReconnect < ReconnectMinFrequency)
                return; // Some other thread made it through the check and the lock, so nothing to do.

            TimeSpan elapsedSinceFirstError = utcNow - firstErrorTime;
            TimeSpan elapsedSinceMostRecentError = utcNow - previousErrorTime;

            bool shouldReconnect =
                elapsedSinceFirstError >= ReconnectErrorThreshold // Make sure we gave the multiplexer enough time to reconnect on its own if it could.
                && elapsedSinceMostRecentError <= ReconnectErrorThreshold; // Make sure we aren't working on stale data (e.g. if there was a gap in errors, don't reconnect yet).

            // Update the previousErrorTime timestamp to be now (e.g. this reconnect request).
            previousErrorTime = utcNow;

            if (!shouldReconnect)
                return;

            firstErrorTime = DateTimeOffset.MinValue;
            previousErrorTime = DateTimeOffset.MinValue;

            Lazy<ConnectionMultiplexer> oldConnection = lazyConnection;
            CloseConnection(oldConnection);
            lazyConnection = CreateConnection();
            Interlocked.Exchange(ref lastReconnectTicks, utcNow.UtcTicks);
        }
    }

    // In real applications, consider using a framework such as
    // Polly to make it easier to customize the retry approach.
    private static T BasicRetry<T>(Func<T> func)
    {
        int reconnectRetry = 0;
        int disposedRetry = 0;

        while (true)
        {
            try
            {
                return func();
            }
            catch (Exception ex) when (ex is RedisConnectionException || ex is SocketException)
            {
                reconnectRetry++;
                if (reconnectRetry > RetryMaxAttempts)
                    throw;
                ForceReconnect();
            }
            catch (ObjectDisposedException)
            {
                disposedRetry++;
                if (disposedRetry > RetryMaxAttempts)
                    throw;
            }
        }
    }

    public static IDatabase GetDatabase()
    {
        return BasicRetry(() => Connection.GetDatabase());
    }

    public static System.Net.EndPoint[] GetEndPoints()
    {
        return BasicRetry(() => Connection.GetEndPoints());
    }

    public static IServer GetServer(string host, int port)
    {
        return BasicRetry(() => Connection.GetServer(host, port));
    }
    ```

4. **Çözüm Gezgini**'nde, **Görünümler** > **Paylaşılan** klasörünü genişletin. Ardından *_Layout.cshtml* dosyasını açın.

    Değiştir:
    
    ```csharp
    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

    Yeni değer:

    ```csharp
    @Html.ActionLink("Azure Cache for Redis Test", "RedisCache", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

### <a name="to-add-a-new-rediscache-view"></a>Yeni RedisCache görünümü eklemek için

1. **Çözüm Gezgini**’nde **Görünümler** klasörünü genişletin ve **Giriş** klasörüne sağ tıklayın. Görünüm **Ekle**  >  **...** seçeneğini belirleyin.

2. **Görünüm Ekle** iletişim kutusunda Görünüm Adı olarak **RedisCache** girin. Ardından **Ekle**'yi seçin.

3. *RedisCache.cshtml* dosyasındaki kodu aşağıdaki kodla değiştirin:

    ```csharp
    @{
        ViewBag.Title = "Azure Cache for Redis Test";
    }

    <h2>@ViewBag.Title.</h2>
    <h3>@ViewBag.Message</h3>
    <br /><br />
    <table border="1" cellpadding="10">
        <tr>
            <th>Command</th>
            <th>Result</th>
        </tr>
        <tr>
            <td>@ViewBag.command1</td>
            <td><pre>@ViewBag.command1Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command2</td>
            <td><pre>@ViewBag.command2Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command3</td>
            <td><pre>@ViewBag.command3Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command4</td>
            <td><pre>@ViewBag.command4Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command5</td>
            <td><pre>@ViewBag.command5Result</pre></td>
        </tr>
    </table>
    ```

## <a name="run-the-app-locally"></a>Uygulamayı yerel olarak çalıştırma

Varsayılan olarak, proje, uygulamayı test ve hata ayıklama için [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) yerel olarak barındıracak şekilde yapılandırılmıştır.

### <a name="to-run-the-app-locally"></a>Uygulamayı yerel olarak çalıştırmak için
1. Visual Studio **'da hata**  >  **ayıklamayı Başlat** ' ı seçerek uygulamayı test ve hata ayıklama için yerel olarak derleyin ve başlatın.

2. Tarayıcıda, gezinti çubuğunda **Redsıs testi Için Azure önbelleği** ' ni seçin.

3. Aşağıdaki örnekte, `Message` daha önce, portalda redsıs konsolu Için Azure önbelleği kullanılarak ayarlanan, önceden önbelleğe alınmış bir değer vardı. Uygulama, önbelleğe alınan bu değeri güncelleştirdi. Ayrıca uygulama, `PING` ve `CLIENT LIST` komutlarını da yürüttü.

    ![Yerel olarak basit bir test tamamlanmıştır](./media/cache-web-app-howto/cache-simple-test-complete-local.png)

## <a name="publish-and-run-in-azure"></a>Azure’da yayımlama ve çalıştırma

Uygulamayı yerel olarak başarıyla test ettikten sonra Azure’a dağıtabilir ve bulutta çalıştırabilirsiniz.

### <a name="to-publish-the-app-to-azure"></a>Uygulamayı Azure’da yayımlamak için

1. Visual Studio’da, Çözüm Gezgini’ndeki proje düğümüne sağ tıklayın. Ardından **Yayımla**’yı seçin.

    ![Yayımlama](./media/cache-web-app-howto/cache-publish-app.png)

2. **Microsoft Azure App Service**’i, sonra **Yeni Oluştur**'u ve **Yayımla**’yı seçin.

    ![App Service'te yayımlama](./media/cache-web-app-howto/cache-publish-to-app-service.png)

3. **Uygulama Hizmetini Oluştur** iletişim kutusunda aşağıdaki değişiklikleri yapın:

    | Ayar | Önerilen değer | Açıklama |
    | ------- | :---------------: | ----------- |
    | **Uygulama adı** | Varsayılan değeri kullanın. | Uygulama adı, Azure’a dağıtıldığında uygulamanın ana bilgisayar adı olur. Gerekirse adı benzersiz hale getirmek için ada bir zaman damgası soneki eklenebilir. |
    | **Abonelik** | Azure aboneliğinizi seçin. | Tüm ilgili barındırma maliyetleri bu aboneliğe yansıtılır. Birden çok Azure aboneliğiniz varsa, istediğiniz aboneliğin seçildiğini doğrulayın.|
    | **Kaynak grubu** | Önbelleği oluşturduğunuz aynı kaynak grubunu kullanın (örneğin, *TestResourceGroup*). | Kaynak grubu, tüm kaynakları bir grup olarak yönetmenize yardımcı olur. Daha sonra uygulamayı silmek istediğinizde, grubu silmeniz yeterli olacaktır. |
    | **App Service planı** | **Yeni**’yi seçin ve *TestingPlan* adlı yeni bir App Service planı oluşturun. <br />Önbelleğinizi oluştururken kullandığınız aynı **Konumu** kullanın. <br />Boyut için **Serbest**’i seçin. | App Service planı, bir web uygulamasının birlikte çalıştırılacağı işlem kaynakları kümesini tanımlar. |

    ![App Service iletişim kutusu](./media/cache-web-app-howto/cache-create-app-service-dialog.png)

4. App Service barındırma ayarlarını yapılandırdıktan sonra **Oluştur**'u seçin.

5. Yayımlama durumunu görmek için Visual Studio’da **Çıkış** penceresini izleyin. Uygulama yayımlandıktan sonra uygulamanın URL'si günlüğe kaydedilir:

    ![Yayımlama çıkışı](./media/cache-web-app-howto/cache-publishing-output.png)

### <a name="add-the-app-setting-for-the-cache"></a>Önbellek için uygulama ayarını ekleme

Yeni uygulama yayımlandıktan sonra, yeni uygulama ayarını ekleyin. Bu ayar, önbellek bağlantı bilgilerini depolamak için kullanılır. 

#### <a name="to-add-the-app-setting"></a>Uygulama ayarını eklemek için 

1. Oluşturduğunuz yeni uygulamayı bulmak için Azure portalının üst kısmındaki arama çubuğuna uygulama adını yazın.

    ![Uygulamayı bulma](./media/cache-web-app-howto/cache-find-app-service.png)

2. Önbelleğe bağlanmak için kullanılacak uygulama için **CacheConnection** adlı yeni bir uygulama ayarı ekleyin. *CacheSecrets.config* dosyanızda, `CacheConnection` için yapılandırdığınız aynı değeri kullanın. Değer, önbellek ana bilgisayar adını ve erişim anahtarını içerir.

    ![Uygulama ayarı ekleme](./media/cache-web-app-howto/cache-add-app-setting.png)

### <a name="run-the-app-in-azure"></a>Azure’da uygulamayı çalıştırma

Tarayıcınızda, uygulamanın URL'sine gidin. URL, Visual Studio çıkış penceresindeki yayımlama işleminin sonuçlarında gösterilir. Ayrıca Azure portalında, oluşturduğunuz uygulamanın Genel Bakış sayfasında da sağlanır.

Önbellek erişimini test etmek için gezinme çubuğunda **Redsıs testi Için Azure önbelleği** ' ni seçin.

![Azure’da basit bir test tamamlanmıştır](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki öğreticiyle devam ediyorsanız, bu hızlı başlangıçta oluşturulan kaynakları tutabilir ve sonraki öğreticide yeniden kullanabilirsiniz.

Aksi takdirde, hızlı başlangıç örnek uygulamasını tamamladıysanız ücret yansıtılmaması için bu hızlı başlangıçta oluşturulan Azure kaynaklarını silebilirsiniz. 

> [!IMPORTANT]
> Silinen kaynak grupları geri alınamaz. Kaynak grubunu sildiğinizde, içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. Bu örneği, tutmak istediğiniz kaynakları içeren mevcut bir kaynak grubunda barındırmak için kaynaklar oluşturduysanız, kaynak grubunu silmek yerine her kaynağı kendi ilgili dikey penceresinden tek tek silebilirsiniz.

### <a name="to-delete-a-resource-group"></a>Kaynak grubunu silmek için

1. [Azure portalında](https://portal.azure.com) oturum açın ve **Kaynak grupları**’nı seçin.

2. **Ada göre filtrele...** kutusuna kaynak grubunuzun adını girin. Bu makaledeki yönergelerde *TestResources* adlı bir kaynak grubu kullanılmıştır. Sonuç listesindeki kaynak grubunuzda **...** düğmesini ve sonra **Kaynak grubunu sil**’i seçin.

    ![Sil](./media/cache-web-app-howto/cache-delete-resource-group.png)

Kaynak grubunun silinmesini onaylamanız istenir. Onaylamak için kaynak grubunuzun adını yazın ve ardından **Sil**’i seçin.

Birkaç dakika sonra kaynak grubu ve bu gruptaki kaynakların tümü silinir.

## <a name="next-steps"></a>Sonraki adımlar

Bir sonraki öğreticide, bir uygulamanın performansını geliştirmek için daha gerçekçi bir senaryoda Redsıs için Azure önbelleğini kullanırsınız. ASP.NET ve veritabanı ile edilgen önbellekli düzeni kullanarak puan tablosu sonuçlarını önbelleğe almak için bu uygulamayı güncelleştirirsiniz.

> [!div class="nextstepaction"]
> [ASP.NET üzerinde edilgen önbellekli puan tablosu oluşturma](cache-web-app-cache-aside-leaderboard.md)