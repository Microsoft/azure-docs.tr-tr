---
title: Redsıs için Azure önbelleği ile bir ASP.NET Web uygulaması oluşturma
description: Bu hızlı başlangıçta, Redsıs için Azure önbelleği ile bir ASP.NET Web uygulaması oluşturmayı öğreneceksiniz.
author: yegu-ms
ms.service: cache
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 155993bb3da781e698398ed8ddffa626e8f6cb2d
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122590"
---
# <a name="quickstart-use-azure-cache-for-redis-with-an-aspnet-web-app"></a>Hızlı başlangıç: ASP.NET Web uygulamasıyla Redsıs için Azure önbelleğini kullanma 

Bu hızlı başlangıçta, önbellekteki verileri depolamak ve almak üzere Redsıs için Azure önbelleğine bağlanan bir ASP.NET Web uygulaması oluşturmak için Visual Studio 2019 ' u kullanırsınız. Ardından uygulamayı Azure App Service ' ye dağıtırsınız.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- **ASP.net ve Web geliştirme** ve **Azure geliştirme** Iş yükleriyle [Visual Studio 2019](https://www.visualstudio.com/downloads/) .

## <a name="create-the-visual-studio-project"></a>Visual Studio projesini oluşturma

1. Visual Studio'yu açın ve **Dosya** >**Yeni** > **Proje**'yi seçin.

2. **Yeni Proje** iletişim kutusunda aşağıdaki adımları uygulayın:

    ![Proje oluşturma](./media/cache-web-app-howto/cache-create-project.png)

    a. **Şablonlar** listesinde **Visual C#** düğümünü genişletin.

    b. **Bulut**’u seçin.

    c. **ASP.NET Web Uygulaması**'nı seçin.

    d. **.NET Framework 4.5.2** veya daha yüksek bir sürümün seçili olduğunu doğrulayın.

    e. **Ad** kutusunda projeye bir ad verin. Bu örnekte biz **ContosoTeamStats** kullandık.

    f. **Tamam**’ı seçin.
   
3. Proje türü olarak **MVC**’yi seçin.

4. **Kimlik Doğrulama** ayarları için **Kimlik Doğrulaması Yok** seçeneğinin belirtildiğinden emin olun. Visual Studio sürümünüze bağlı olarak, varsayılan **Kimlik Doğrulama** ayarı farklı olabilir. Değiştirmek için **Kimlik Doğrulamasını Değiştir**’i ve ardından **Kimlik Doğrulaması Yok**’u seçin.

5. Projeyi oluşturmak için **Tamam**'ı seçin.

## <a name="create-a-cache"></a>Bir önbellek oluşturma

Daha sonra, uygulama için önbellek oluşturursunuz.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

#### <a name="to-edit-the-cachesecretsconfig-file"></a>*CacheSecrets.config* dosyasını düzenlemek için

1. Bilgisayarınızda *Cachegizlilikler. config*adlı bir dosya oluşturun. Örnek uygulamanızın kaynak koduyla iade edilmeyeceği bir konuma koyun. Bu hızlı başlangıç için *CacheSecrets.config* dosyası şu konumda bulunur: *C:\AppSecrets\CacheSecrets.config*.

1. *CacheSecrets.config* dosyasını düzenleyin. Ardından aşağıdaki içeriği ekleyin:

    ```xml
    <appSettings>
        <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<access-key>"/>
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
* Uygulamayı StackExchange. Redsıs istemcisini kullanacak şekilde yapılandırma
* HomeController ve Düzeni güncelleştirme
* Yeni RedisCache görünümü ekleme

### <a name="update-the-webconfig-file-with-an-app-setting-for-the-cache"></a>Önbellek için uygulama ayarı ile web.config dosyasını güncelleştirme

Uygulamayı yerel olarak çalıştırdığınızda, *Cachegizlilikler. config* dosyasındaki bilgiler redsıs örneği Için Azure önbelleğinize bağlanmak için kullanılır. Daha sonra bu uygulamayı Azure’a dağıtırsınız. O aşamada, uygulamanın bu dosya yerine önbellek bağlantı bilgilerini almak için kullandığı Azure’daki bir uygulama ayarını yapılandırırsınız. 

*CacheSecrets.config* dosyası uygulamanızla Azure’a dağıtılmadığından bunu yalnızca uygulamayı yerel olarak test ederken kullanırsınız. Önbellek verilerinize kötü amaçlı erişimi önlemek için bu bilgileri olabildiğince güvende tutun.

#### <a name="to-update-the-webconfig-file"></a>*Web.config* dosyasını güncelleştirmek için
1. **Çözüm Gezgini**’nde, *web.config* dosyasına çift tıklayarak dosyayı açarsınız.

    ![Web.config](./media/cache-web-app-howto/cache-web-config.png)

2. *Web.config* dosyasında `<appSetting>` öğesini bulun. Ardından, aşağıdaki `file` özniteliğini ekleyin. Farklı bir dosya adı veya konumu kullandıysanız, örnekte gösterilenlerin yerine bu değerleri koyun.

* Önce: `<appSettings>`
* Sonra: `<appSettings file="C:\AppSecrets\CacheSecrets.config">`

ASP.NET çalışma zamanı, `<appSettings>` öğesindeki biçimlendirmeye sahip harici dosyasının içeriğini birleştirir. Belirtilen dosya bulunamazsa, çalışma zamanı dosya özniteliğini yok sayar. Gizli anahtarlarınız (önbelleğinize bağlantı dizisi) uygulamanız için kaynak kodun bir parçası olarak dahil edilmez. Web uygulamanızı Azure 'a dağıttığınızda, *Cachegizlilikler. config* dosyası dağıtılmaz.

### <a name="to-configure-the-application-to-use-stackexchangeredis"></a>Uygulamayı StackExchange.Redis kullanacak şekilde yapılandırmak için

1. Visual Studio’da [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) NuGet paketi kullanmak üzere uygulamayı yapılandırmak için **Araçlar > NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu**'nu seçin.

2. `Package Manager Console` penceresinden aşağıdaki komutu çalıştırın:

    ```powershell
    Install-Package StackExchange.Redis
    ```

3. NuGet paketi indirir ve istemci uygulamanızı Azure Cache Redis StackExchange.Azure önbelleği için Redis istemcisini ile erişmek için gerekli derleme başvurularını ekler. `StackExchange.Redis` istemci kitaplığının tanımlayıcı adlı bir sürümünü kullanmak istiyorsanız `StackExchange.Redis.StrongName` paketini yükleyin.

### <a name="to-update-the-homecontroller-and-layout"></a>HomeController ve Layout'u güncelleştirmek için

1. **Çözüm Gezgini**’nde, **Denetleyiciler** klasörünü genişletin ve ardından *HomeController.cs* dosyasını açın.

2. Önbellek istemci ve uygulama ayarlarını desteklemek için dosyanın üst kısmına şu iki `using` deyimini ekleyin.

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

3. Yeni önbellekte bazı komutları çalıştıran yeni bir `HomeController` eylemini desteklemek için `RedisCache` sınıfına aşağıdaki yöntemi ekleyin.

    ```csharp
        public ActionResult RedisCache()
        {
            ViewBag.Message = "A simple example with Azure Cache for Redis on ASP.NET.";

            var lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
            {
                string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
                return ConnectionMultiplexer.Connect(cacheConnection);
            });

            // Connection refers to a property that returns a ConnectionMultiplexer
            // as shown in the previous example.
            IDatabase cache = lazyConnection.Value.GetDatabase();

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
            ViewBag.command5 = "CLIENT LIST";
            ViewBag.command5Result = cache.Execute("CLIENT", "LIST").ToString().Replace(" id=", "\rid=");

            lazyConnection.Value.Dispose();

            return View();
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

1. **Çözüm Gezgini**’nde **Görünümler** klasörünü genişletin ve **Giriş** klasörüne sağ tıklayın. **Ekle** > **Görünüm...** seçeneğini belirleyin.

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

Varsayılan olarak proje, test ve hata ayıklama için uygulamayı [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview)’te yerel olarak barındırmak üzere yapılandırılır.

### <a name="to-run-the-app-locally"></a>Uygulamayı yerel olarak çalıştırmak için
1. Visual Studio’da **Hata Ayıklama** > **Hata Ayıklamayı Başlat**'ı seçerek test ve hata ayıklama için uygulamayı derleyip başlatın.

2. Tarayıcıda, gezinti çubuğunda **Redsıs testi Için Azure önbelleği** ' ni seçin.

3. Aşağıdaki örnekte, `Message` anahtarında önceden önbelleğe alınmış bir değer vardı ve bu, portalda Redsıs konsolu için Azure önbelleği kullanılarak ayarlanmıştır. Uygulama, önbelleğe alınan bu değeri güncelleştirdi. Ayrıca uygulama, `PING` ve `CLIENT LIST` komutlarını da yürüttü.

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

2. Önbelleğe bağlanmak için kullanılacak uygulama için **CacheConnection** adlı yeni bir uygulama ayarı ekleyin. `CacheConnection`CacheSecrets.config*dosyanızda,* için yapılandırdığınız aynı değeri kullanın. Değer, önbellek ana bilgisayar adını ve erişim anahtarını içerir.

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
