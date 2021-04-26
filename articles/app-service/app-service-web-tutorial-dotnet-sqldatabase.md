---
title: 'Öğretici: Azure SQL veritabanı ile ASP.NET uygulaması'
description: C# ASP.NET uygulamasını Azure 'a ve Azure SQL veritabanı 'na dağıtmayı öğrenin
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 533bd817b704db9976624b356a9950a9c48b8339
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606028"
---
# <a name="tutorial-deploy-an-aspnet-app-to-azure-with-azure-sql-database"></a>Öğretici: Azure SQL veritabanı ile ASP.NET uygulamasını Azure 'a dağıtma

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Bu öğreticide, veri odaklı bir ASP.NET uygulamasının App Service nasıl dağıtılacağı ve [Azure SQL veritabanı](../azure-sql/database/sql-database-paas-overview.md)'na nasıl bağlanacağı gösterilmektedir. İşiniz bittiğinde, Azure 'da çalışan ve SQL veritabanı 'na bağlı bir ASP.NET uygulamanız vardır.

![Azure App Service yayımlanan ASP.NET uygulaması](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Azure SQL veritabanı 'nda veritabanı oluşturma
> * ASP.NET uygulamasını SQL Veritabanı'na bağlama
> * Uygulamayı Azure’da dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki günlüklerin terminalinize akışını sağlama
> * Uygulamayı Azure portalında yönetme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

**ASP.net ve Web geliştirme** iş yüküyle <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> ' i yükledikten sonra.

Visual Studio 'yu zaten yüklediyseniz **Araçlar**  >  **ve Özellikler al**' a tıklayarak iş yüklerini Visual Studio 'ya ekleyin.

## <a name="download-the-sample"></a>Örneği indirme

1. [Örnek projeyi indirin](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

1. *dotnet-sqldb-tutorial-master.zip* dosyasını ayıklayın (sıkıştırmasını açın).

Örnek proje, [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) kullanan temel bir [ASP.NET MVC](https://www.asp.net/mvc) oluşturma-okuma-güncelleştirme-silme (CRUD) uygulaması içerir.

### <a name="run-the-app"></a>Uygulamayı çalıştırma

1. Visual Studio'da *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* dosyasını açın.

1. Uygulamayı hata ayıklaması yapılmadan çalıştırmak için `Ctrl+F5` yazın. Uygulama varsayılan tarayıcınızda görüntülenir.

1. **Yeni Oluştur** bağlantısını seçin ve *yapılacak* birkaç iş oluşturun.

    ![Yeni ASP.NET Projesi iletişim kutusu](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

1. **Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.

Uygulama, veritabanıyla bağlantı kurmak için bir veritabanı bağlamı kullanır. Bu örnekte, veritabanı bağlamı `MyDbConnection` adlı bir bağlantı dizesi kullanır. Bağlantı dizesi *Web.config* dosyasında ayarlanır ve *Models/MyDatabaseContext.cs* dosyasında bu bağlantı dizesine başvurulur. Bağlantı dizesi adı öğreticide daha sonra Azure uygulamasını bir Azure SQL veritabanına bağlamak için kullanılır.

## <a name="publish-aspnet-application-to-azure"></a>ASP.NET uygulamasını Azure 'da yayımlama

1. **Çözüm Gezgini**’nde **DotNetAppSqlDb** projenize sağ tıklayıp **Yayımla**’yı seçin.

    ![Çözüm Gezgini'nden yayımlama](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. Hedef olarak **Azure** ' u seçin ve **İleri**' ye tıklayın.

1. **Azure App Service (Windows)** seçili olduğundan emin olun ve **İleri**' ye tıklayın.

#### <a name="sign-in-and-add-an-app"></a>Oturum açın ve uygulama ekleyin

1. **Yayımla** iletişim kutusunda Hesap Yöneticisi açılan listesinden **Hesap Ekle** ' ye tıklayın.

1. Azure aboneliğinizde oturum açın. Bir Microsoft hesabında zaten oturum açtıysanız hesabın Azure aboneliğinizi barındırdığından emin olun. Oturum açtığınız Microsoft hesabında Azure aboneliğiniz yoksa, doğru hesabı eklemek için tıklayın.

1. **App Service örnekleri** bölmesinde, öğesine tıklayın **+** .

    ![Azure'da oturum açma](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

#### <a name="configure-the-web-app-name"></a>Web uygulaması adını yapılandırma

Oluşturulan web uygulaması adını koruyabilir veya başka bir benzersiz adla değiştirebilirsiniz (geçerli karakterler: `a-z`, `0-9` ve `-`). Web uygulaması adı, uygulamanızın varsayılan URL'sinin bir parçası olarak kullanılır (`<app_name>.azurewebsites.net`; burada `<app_name>`, web uygulamanızın adıdır). Web uygulaması adı Azure'daki tüm uygulamalar arasında benzersiz olmalıdır.

> [!NOTE]
> Henüz **Oluştur** seçeneğini seçmeyin.

![App Service oluşturma iletişim kutusu](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

#### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [resource-group](../../includes/resource-group.md)]

1. **Kaynak grubu**' nun yanındaki **Yeni**' ye tıklayın.

   ![Kaynak Grubu’nun yanındaki Yeni öğesine tıklayın.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

1. Kaynak grubunu **myResourceGroup** olarak adlandırın.

#### <a name="create-an-app-service-plan"></a>App Service planı oluşturma

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. **Barındırma planı**' nın yanındaki **Yeni**' ye tıklayın.

1. **App Service planı Yapılandır** iletişim kutusunda, yeni App Service planını aşağıdaki ayarlarla yapılandırın ve **Tamam**' a tıklayın:

   | Ayar  | Önerilen değer | Daha fazla bilgi edinmek için |
   | ----------------- | ------------ | ----|
   |**App Service Planı**| myAppServicePlan | [App Service planları](../app-service/overview-hosting-plans.md) |
   |**Konum**| West Europe | [Azure bölgeleri](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
   |**Boyut**| Ücretsiz | [Fiyatlandırma katmanları](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

   ![App Service planı oluşturma](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

1. **Oluştur** ' a tıklayın ve Azure kaynaklarının oluşturulmasını bekleyin.

1. **Yayımla** iletişim kutusunda yapılandırdığınız kaynaklar gösterilir. **Finish (Son)** düğmesine tıklayın.

   ![oluşturduğunuz kaynaklar](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)


#### <a name="create-a-server-and-database"></a>Sunucu ve veritabanı oluşturma

Bir veritabanı oluşturmadan önce, [mantıksal BIR SQL Server](../azure-sql/database/logical-servers.md)gerekir. Mantıksal SQL Server, Grup olarak yönetilen bir veritabanı grubunu içeren mantıksal bir yapıdır.

1. **Yayımla** iletişim kutusunda, **hizmet bağımlılıkları** bölümüne gidin. **SQL Server veritabanı**' nın yanındaki **Yapılandır**' ı tıklatın.

   ![SQL veritabanı bağımlılığını yapılandırma](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sqldb-dependency.png)

1. **Azure SQL veritabanı** ' nı seçin ve **İleri**' ye tıklayın.

1. **Azure SQL veritabanı 'Nı Yapılandır** iletişim kutusunda, öğesine tıklayın **+** .

1. **Veritabanı sunucusu**' nun yanındaki **Yeni**' ye tıklayın.

   Sunucu adı oluşturulur. Bu ad, sunucunuz için varsayılan URL 'nin bir parçası olarak kullanılır `<server_name>.database.windows.net` . Azure SQL 'deki tüm sunucular genelinde benzersiz olmalıdır. Sunucu adını değiştirebilirsiniz, ama bu öğreticide oluşturulan değeri tutun.

1. Bir yönetici kullanıcı adı ve parolası ekleyin. Parola karmaşıklık gereksinimleri için bkz. [Parola İlkesi](/sql/relational-databases/security/password-policy).

   Bu kullanıcı adını ve parolayı unutmayın. Sunucuyu daha sonra yönetmeniz gerekir.

   ![Sunucu oluşturma](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

   > [!IMPORTANT]
   > Bağlantı dizelerindeki (Visual Studio ve ayrıca App Service’te) parolanız maskelenmiş olsa bile, bir yerlerde tutulması uygulamanızın saldırı yüzeyine katkıda bulunur. App Service, kodunuzda veya uygulama yapılandırmanızda gizli dizileri tutma gereksinimini tamamen ortadan kaldırarak bu riski yok etmek için [yönetilen hizmet kimliklerini](overview-managed-identity.md) kullanabilir. Daha fazla bilgi için bkz. [Sonraki adımlar](#next-steps).

1. **Tamam**'a tıklayın.

1. **Azure SQL veritabanı** iletişim kutusunda, varsayılan olarak oluşturulan **veritabanı adını** saklayın. **Oluştur** ' u seçin ve veritabanı kaynaklarının oluşturulmasını bekleyin.

    ![Veritabanını yapılandır](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

#### <a name="configure-database-connection"></a>Veritabanı bağlantısını yapılandır

1. Sihirbaz veritabanı kaynaklarını oluşturmayı bitirdiğinde **İleri**' ye tıklayın.

1. **Veritabanı bağlantı dizesi adı**' nda, _mydbconnection_ yazın. Bu ad, _Models/MyDatabaseContext.cs_'de başvurulan bağlantı dizesiyle eşleşmelidir.

1. **Veritabanı bağlantısı Kullanıcı adı** ve **veritabanı bağlantısı parolası**' nda, [sunucu oluşturma](#create-a-server-and-database)bölümünde kullandığınız yönetici kullanıcı adını ve parolasını yazın.

1. **Azure uygulama ayarlarının** seçili olduğundan emin olun ve **son**' a tıklayın.

    ![Veritabanı bağlantı dizesini Yapılandır](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-connection.png)

1. Yapılandırma sihirbazının bitmesini bekleyin ve **Kapat**' a tıklayın.

#### <a name="deploy-your-aspnet-app"></a>ASP.NET uygulamanızı dağıtma

1. **Yayımla** sekmesinde yukarı doğru kaydırın ve **Yayımla**' ya tıklayın. ASP.NET uygulamanız Azure 'a dağıtıldıktan sonra. Varsayılan tarayıcınız dağıtılan uygulamanın URL'siyle başlatılır.

1. Yapılacak birkaç iş ekleyin.

    ![Azure uygulaması 'nda yayınlanan ASP.NET uygulaması](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

    Tebrikler! Veri temelli ASP.NET uygulamanız Azure App Service'de çalışıyor.

## <a name="access-the-database-locally"></a>Veritabanına yerel olarak erişin

Visual Studio, **SQL Server Nesne Gezgini** Azure 'da yeni veritabanınızı kolayca araştırmanıza ve yönetmenize olanak sağlar. Yeni veritabanı, güvenlik duvarını oluşturduğunuz App Service uygulamasına zaten açtı, ancak yerel bilgisayarınızdan (Visual Studio 'da olduğu gibi) erişmek için yerel makinenizin genel IP adresi için bir güvenlik duvarı açmanız gerekir. Internet servis sağlayıcınız genel IP adresinizi değiştirirse Azure veritabanına tekrar erişmek için güvenlik duvarını yeniden yapılandırmanız gerekir.

#### <a name="create-a-database-connection"></a>Veritabanı bağlantısı oluşturma

1. **Görünüm** menüsünde **SQL Server Nesne Gezgini**'ni seçin.

1. **SQL Server Nesne Gezgini**'nin üst kısmında **SQL Server Ekle** düğmesine tıklayın.

#### <a name="configure-the-database-connection"></a>Veritabanı bağlantısını yapılandırma

1. **Bağlan** iletişim kutusunda **Azure** düğümünü genişletin. Azure'daki tüm SQL Veritabanı örnekleriniz burada listelenir.

1. Daha önce oluşturduğunuz veritabanını seçin. Daha önce oluşturduğunuz bağlantı, en altta otomatik olarak doldurulur.

1. Daha önce oluşturduğunuz veritabanı yöneticisi parolasını yazın ve **Bağlan**'a tıklayın.

    ![Visual Studio'dan veritabanı bağlantısını yapılandırma](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

#### <a name="allow-client-connection-from-your-computer"></a>Bilgisayarınızdan istemci bağlantısına izin verme

**Yeni güvenlik duvarı kuralı oluştur** iletişim kutusu açılır. Varsayılan olarak, bir sunucu, Azure uygulamanızla ilgili olarak yalnızca Azure hizmetlerinden veritabanlarına bağlantılara izin verir. Azure dışından veritabanınıza bağlanmak için sunucu düzeyinde bir güvenlik duvarı kuralı oluşturun. Güvenlik duvarı kuralı yerel bilgisayarınızın genel IP adresine izin verir.

İletişim kutusu bilgisayarınızın genel IP adresiyle önceden doldurulmuştur.

1. **İstemci IP değerimi ekle**'nin seçili olduğundan emin olun ve **Tamam**'a tıklayın.

    ![Güvenlik duvarı kuralı oluşturma](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

    Visual Studio SQL Veritabanı örneğiniz için güvenlik duvarı ayarını oluşturmayı tamamladığında, bağlantınız **SQL Server Nesne Gezgini**'nde gösterilir.

    Burada sorgu çalıştırma, görünümler ve saklı yordamlar oluşturma gibi daha birçok yaygın veritabanı işlemini yapabilirsiniz.

1.   >  **&lt; Veritabanınızın>** tablolarınızı > bağlantınızı genişletin  >  . `Todoes` tablosuna sağ tıklayın ve **Verileri Görüntüle**'yi seçin.

    ![SQL Veritabanı nesnelerini inceleyin](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Uygulamayı Code First Migrations ile güncelleştirme

Azure 'da veritabanınızı ve uygulamanızı güncelleştirmek için Visual Studio 'daki tanıdık araçları kullanabilirsiniz. Bu adımda, veritabanı şemanızda değişiklik yapmak ve bunu Azure'a yayımlamak için Entity Framework'te Code First Migrations'ı kullanırsınız.

Entity Framework Code First Migrations'ı kullanma hakkında daha fazla bilgi için bkz. [MVC 5 Kullanarak Entity Framework 6 Code First ile Çalışmaya Başlama](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

#### <a name="update-your-data-model"></a>Veri modelinizi güncelleştirme

Kod düzenleyicide _Models\Todo.cs_ dosyasını açın. `ToDo` sınıfına aşağıdaki özelliği ekleyin:

```csharp
public bool Done { get; set; }
```
    
#### <a name="run-code-first-migrations-locally"></a>Code First Migrations’ı yerel olarak çalıştırma

Yerel veritabanınızda güncelleştirme yapmak için birkaç komut çalıştırın.

1. **Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu**'na tıklayın.

1. Paket Yöneticisi Konsolu penceresinde Code First Migrations'ı etkinleştirin:

    ```powershell
    Enable-Migrations
    ```
    
1. Bir geçiş ekleyin:

    ```powershell
    Add-Migration AddProperty
    ```
    
1. Yerel veritabanınızı güncelleştirin:

    ```powershell
    Update-Database
    ```
    
1. Uygulamayı çalıştırmak için `Ctrl+F5` tuşlarına basın. Düzenle, ayrıntılar ve sil bağlantılarını test edin.

Uygulama hatasız yüklerse, Code First Migrations başarılı olmuştur. Öte yandan, sayfanız yine aynı görünür çünkü uygulama mantığınız henüz bu yeni özelliği kullanmamaktadır.

#### <a name="use-the-new-property"></a>Yeni özelliği kullanma

`Done` özelliğini kullanarak kodunuzda birkaç değişiklik yapın. Bu öğreticide, daha kolay uygulama için, işlemin nasıl çalıştığını görmek üzere yalnızca `Index` ve `Create` görünümlerini değiştireceksiniz.

1. _Controllers\TodosController.cs_ dosyasını açın.

1. 52. satırda `Create()` yöntemini bulun ve `Done` değerini `Bind` özniteliğindeki özellik listesine ekleyin. Hazır olduğunuzda, `Create()` metot imzanız aşağıdaki koda benzer şekilde görünür:

    ```csharp
    public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
    ```
    
1. _Views\Todos\Create.cshtml_ dosyasını açın.

1. Razor kodunda, `model.Description` kullanan bir `<div class="form-group">` öğesi ve `model.CreatedDate` kullanan başka bir `<div class="form-group">` öğesi görürsünüz. Bu iki öğenin hemen arkasına `model.Done` kullanan başka bir `<div class="form-group">` öğesi ekleyin:

    ```csharp
    <div class="form-group">
        @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
        <div class="col-md-10">
            <div class="checkbox">
                @Html.EditorFor(model => model.Done)
                @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
            </div>
        </div>
    </div>
    ```
    
1. _Views\Todos\Index.cshtml_ dosyasını açın.

1. Boş `<th></th>` öğesini arayın. Bu öğenin hemen üstüne aşağıdaki Razor kodunu ekleyin:

    ```csharp
    <th>
        @Html.DisplayNameFor(model => model.Done)
    </th>
    ```
    
1. `<td>` yardımcı yöntemlerini içeren `Html.ActionLink()` öğesini bulun. Bu `<td>` yönteminin _üst kısmına_, aşağıdaki Razor koduyla başka bir `<td>` öğesi ekleyin:

    ```csharp
    <td>
        @Html.DisplayFor(modelItem => item.Done)
    </td>
    ```
    
    `Index` ve `Create` görünümlerindeki değişiklikleri görmek için yapmanız gerekenler bu kadardır.

1. Uygulamayı çalıştırmak için `Ctrl+F5` tuşlarına basın.

Artık yapılacak bir öğe ekleyip öğeyi **Bitti** olarak işaretleyebilirsiniz. Daha sonra öğe, ana sayfanızda tamamlanmış bir öğe olarak görünmelidir. `Edit` görünümünü değiştirmediğinizden, `Edit` görünümünün `Done` alanında görünmediğini göz önünde bulundurun.

#### <a name="enable-code-first-migrations-in-azure"></a>Azure'da Code First Migrations’ı etkinleştirme

Artık kod değişikyseniz, veritabanı geçişi de dahil olmak üzere, Azure uygulamanızda yayımlayın ve SQL veritabanınızı Code First Migrations de güncelleştirebilirsiniz.

1. Aynı daha önce yaptığınız gibi, projenize sağ tıklayıp **Yayımla**'yı seçin.

1. Yayımla ayarlarını açmak için **diğer eylemler**  >  **Düzenle** ' ye tıklayın.

    ![Yayımlama ayarlarını açma](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

1. **MyDatabaseContext** açılan menüsünde Azure SQL veritabanınızın veritabanı bağlantısını seçin.

1. **Önce Kod Uygulamalı Geçişler (uygulama başlatılırken çalışır)** öğesini seçin ve **Kaydet**'e tıklayın.

    ![Azure uygulamasında Code First Migrations etkinleştirme](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

#### <a name="publish-your-changes"></a>Değişikliklerinizi yayımlama

Azure uygulamanızda Code First Migrations etkinleştirdığınıza göre, kod Değişikliklerinizi yayımlayın.

1. Yayımlama sayfasında **Yayımla**'ya tıklayın.

1. Yapılacaklar öğelerini yeniden eklemeyi deneyin ve **Bitti**'yi seçin; bunlar giriş sayfanızda tamamlanmış öğe olarak gösteriliyor olmalıdır.

    ![Code First geçişten sonra Azure uygulaması](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Mevcut yapılacak öğeleriniz görüntülenmeye devam eder. ASP.NET uygulamanızı yeniden yayımladığınızda, SQL Veritabanınızdaki mevcut veriler kaybolmaz. Ayrıca, Code First Migrations yalnızca veri şemasını değiştirir ve mevcut verilerinizde herhangi bir değişiklik yapmaz.

## <a name="stream-application-logs"></a>Uygulama günlüklerinin akışı yapma

İzleme iletilerini doğrudan Azure uygulamanızdan Visual Studio 'ya aktarabilirsiniz.

_Controllers\TodosController.cs_ dosyasını açın.

Her eylem bir `Trace.WriteLine()` yöntemiyle başlar. Bu kod, Azure uygulamanıza nasıl izleme iletileri ekleneceğini göstermek için eklenir.

#### <a name="enable-log-streaming"></a>Günlük akışını etkinleştirme

1. **Görünüm** menüsünde, **bulut Gezgini**' ni seçin.

1. **Cloud Explorer**'da, uygulamanız olan Azure aboneliğini genişletin ve **App Service** genişletin.

1. Azure uygulamanıza sağ tıklayın ve **akış günlüklerini görüntüle**' yi seçin.

    ![Günlük akışını etkinleştirme](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

    Şimdi **Çıkış** penceresine günlüklerin akışı yapılır.

    ![Çıkış penceresinde günlük akışı](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

    Öte yandan, henüz hiçbir izleme iletisi görmezsiniz. Bunun nedeni, **akış günlüklerini görüntüle**' yi ilk kez seçtiğinizde, Azure uygulamanız izleme düzeyini olarak ayarlar ve `Error` yalnızca hata olaylarını günlüğe kaydeder ( `Trace.TraceError()` yöntemiyle).

#### <a name="change-trace-levels"></a>İzleme düzeylerini değiştirme

1. Diğer izleme iletilerini çıkarmak üzere izleme düzeylerini değiştirmek için, **Cloud Explorer**'a geri dönün.

1. Uygulamanıza tekrar sağ tıklayın ve **portalda aç**' ı seçin.

1. Uygulamanızın Portal Yönetim sayfasında, sol menüden **App Service Günlükler**' i seçin.

1. **Uygulama günlüğü (dosya sistemi)** altında, **düzey** olarak **verbose** ' i seçin. **Kaydet**’e tıklayın.

    ![İzleme düzeyini Ayrıntılı olarak değiştirme](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

    > [!TIP]
    > Her düzeyde hangi tür iletilerin gösterildiğini görmek için farklı izleme düzeyleriyle denemeler yapabilirsiniz. Örneğin, **bilgi** düzeyi,, ve tarafından oluşturulan tüm günlükleri içerir, `Trace.TraceInformation()` `Trace.TraceWarning()` `Trace.TraceError()` ancak tarafından oluşturulan günlükleri içermez `Trace.WriteLine()` .

1. Tarayıcınızda uygulamanıza yeniden gidin *&lt;>. azurewebsites.net uygulamanızın adını http://* ve ardından Azure 'da yapılacaklar listesi uygulamasının etrafında tıklatmayı deneyin. Artık Visual Studio'da izleme iletileri akışla **Çıkış** penceresine aktarılır.

    ```console
    Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
    Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
    Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
    Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
    ```
    
#### <a name="stop-log-streaming"></a>Günlük akışını durdurma

Günlük akışı hizmetini durdurmak için, **Çıkış** penceresinde **İzlemeyi durdur** düğmesine tıklayın.

![Günlük akışını durdurma](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Azure uygulamanızı yönetme

Web uygulamasını yönetmek için [Azure portalına](https://portal.azure.com) gidin. **Uygulama hizmetleri**' ni arayıp seçin.

![Azure Uygulama Hizmetleri 'ni arayın](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

Azure uygulamanızın adını seçin.

![Azure uygulamasına portal gezintisi](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Uygulamanızın sayfasına ulaştınız.

Varsayılan olarak, portalda **Genel Bakış** sayfası gösterilir. Bu sayfa, uygulamanızın nasıl çalıştığını gösterir. Buradan ayrıca göz atma, durdurma, başlatma, yeniden başlatma ve silme gibi temel yönetim görevlerini gerçekleştirebilirsiniz. Sayfanın sol tarafındaki sekmeler, açabileceğiniz farklı yapılandırma sayfalarını gösterir.

![Azure portalında App Service sayfası](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
>
> * Azure SQL veritabanı 'nda veritabanı oluşturma
> * ASP.NET uygulamasını SQL Veritabanı'na bağlama
> * Uygulamayı Azure’da dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki günlüklerin terminalinize akışını sağlama
> * Uygulamayı Azure portalında yönetme

Azure SQL Veritabanı bağlantınızın güvenliğini kolayca iyileştirmeyi öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Azure kaynakları için yönetilen kimlikleri kullanarak SQL Veritabanına güvenli bir şekilde erişme](app-service-web-tutorial-connect-msi.md)

Daha fazla kaynak:

> [!div class="nextstepaction"]
> [ASP.NET uygulamasını yapılandırma](configure-language-dotnet-framework.md)

Bulut harcamalarınızı iyileştirmek ve kaydetmek istiyor musunuz?

> [!div class="nextstepaction"]
> [Maliyet yönetimi ile maliyetleri çözümlemeye başlayın](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)