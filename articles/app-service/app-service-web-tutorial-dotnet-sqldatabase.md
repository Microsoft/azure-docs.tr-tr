---
title: SQL veritabanı ile ASP.NET uygulaması derleme-Azure App Service | Microsoft Docs
description: Azure'a SQL Server veritabanı ile C# ASP.NET uygulaması dağıtmayı öğrenin.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2f815d99c32c2dcaa1fd35d00110b27a90f53ab4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73471237"
---
# <a name="tutorial-build-an-aspnet-app-in-azure-with-sql-database"></a>Öğretici: Azure’da SQL Veritabanı ile ASP.NET uygulaması derleme

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Bu öğreticide, veri odaklı bir ASP.NET uygulamasının App Service nasıl dağıtılacağı ve [Azure SQL veritabanı](../sql-database/sql-database-technical-overview.md)'na nasıl bağlanacağı gösterilmektedir. İşiniz bittiğinde, Azure 'da çalışan ve SQL veritabanı 'na bağlı bir ASP.NET uygulamanız vardır.

![Azure App Service yayımlanan ASP.NET uygulaması](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure’da SQL Veritabanı oluşturma
> * ASP.NET uygulamasını SQL Veritabanı'na bağlama
> * Uygulamayı Azure’da dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki günlüklerin terminalinize akışını sağlama
> * Uygulamayı Azure portalında yönetme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

**ASP.net ve Web geliştirme** iş yüküyle <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> ' i yükledikten sonra.

Visual Studio’yu önceden yüklediyseniz, **Araçlar** > **Araçları ve Özellikleri Al** seçeneklerine tıklayarak Visual Studio’da iş yüklerini ekleyin.

## <a name="download-the-sample"></a>Örneği indirme

- [Örnek projeyi indirin](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).
- *Dotnet-sqldb-tutorial-master.zip* dosyasını ayıklayın (sıkıştırmasını açın).

Örnek proje, [Entity Framework Code First](https://www.asp.net/mvc) kullanan temel bir [ASP.NET MVC](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) oluşturma-okuma-güncelleştirme-silme (CRUD) uygulaması içerir.

### <a name="run-the-app"></a>Uygulamayı çalıştırma

Visual Studio'da *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* dosyasını açın. 

Uygulamayı hata ayıklaması yapılmadan çalıştırmak için `Ctrl+F5` yazın. Uygulama varsayılan tarayıcınızda görüntülenir. **Yeni Oluştur** bağlantısını seçin ve *yapılacak* birkaç iş oluşturun. 

![Yeni ASP.NET Projesi iletişim kutusu](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.

Uygulama, veritabanıyla bağlantı kurmak için bir veritabanı bağlamı kullanır. Bu örnekte, veritabanı bağlamı `MyDbConnection` adlı bir bağlantı dizesi kullanır. Bağlantı dizesi *Web.config* dosyasında ayarlanır ve *Models/MyDatabaseContext.cs* dosyasında bu bağlantı dizesine başvurulur. Bağlantı dizesi adı öğreticide daha sonra Azure uygulamasını bir Azure SQL veritabanına bağlamak için kullanılır. 

## <a name="publish-to-azure-with-sql-database"></a>SQL Veritabanı ile Azure'a yayımlama

**Çözüm Gezgini**’nde **DotNetAppSqlDb** projenize sağ tıklayıp **Yayımla**’yı seçin.

![Çözüm Gezgini'nden yayımlama](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

**Microsoft Azure App Service**’in seçili olduğundan emin olup **Yayımla**’ya tıklayın.

![Projeye genel bakış sayfasından yayımlama](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

Yayımlama, ASP.NET uygulamanızı Azure 'da çalıştırmak için gereken tüm Azure kaynaklarını oluşturmanıza yardımcı olan **oluştur App Service** iletişim kutusunu açar.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

**App Service Oluştur** iletişim kutusunda **Hesap ekle**’ye tıklayın ve ardından Azure aboneliğinizde oturum açın. Bir Microsoft hesabında zaten oturum açtıysanız hesabın Azure aboneliğinizi barındırdığından emin olun. Oturum açtığınız Microsoft hesabında Azure aboneliğiniz yoksa, doğru hesabı eklemek için tıklayın. 

> [!NOTE]
> Zaten oturum açtıysanız **Oluştur** öğesini henüz seçmeyin.
>
>
   
![Azure'da oturum açma](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

### <a name="configure-the-web-app-name"></a>Web uygulaması adını yapılandırma

Oluşturulan web uygulaması adını koruyabilir veya başka bir benzersiz adla değiştirebilirsiniz (geçerli karakterler: `a-z`, `0-9` ve `-`). Web uygulaması adı, uygulamanızın varsayılan URL'sinin bir parçası olarak kullanılır (`<app_name>.azurewebsites.net`; burada `<app_name>`, web uygulamanızın adıdır). Web uygulaması adı Azure'daki tüm uygulamalar arasında benzersiz olmalıdır. 

![App Service oluşturma iletişim kutusu](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [resource-group](../../includes/resource-group.md)]

**Kaynak Grubu**’nun yanındaki **Yeni** öğesine tıklayın.

![Kaynak Grubu’nun yanındaki Yeni öğesine tıklayın.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

Kaynak grubunu **myResourceGroup** olarak adlandırın.

### <a name="create-an-app-service-plan"></a>App Service planı oluşturma

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

**App Service Planı**’nın yanındaki **Yeni** öğesine tıklayın. 

**App Service Planını Yapılandır** iletişim kutusunda yeni App Service planını aşağıdaki ayarlarla yapılandırın:

![App Service planı oluşturma](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

| Ayar  | Önerilen değer | Daha fazla bilgi edinmek için |
| ----------------- | ------------ | ----|
|**App Service Planı**| myAppServicePlan | [App Service planları](../app-service/overview-hosting-plans.md) |
|**Konum**| Batı Avrupa | [Azure bölgeleri](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
|**Boyut**| Ücretsiz | [Fiyatlandırma katmanları](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

### <a name="create-a-sql-server-instance"></a>SQL Server örneği oluşturma

Veritabanı oluşturmadan önce, bir [Azure SQL Veritabanı mantıksal sunucusuna](../sql-database/sql-database-features.md) ihtiyacınız vardır. Mantıksal sunucu, grup olarak yönetilen bir veritabanı grubu içerir.

**SQL Veritabanı oluştur**'a tıklayın.

![SQL Veritabanı oluşturma](media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

**SQL Veritabanını Yapılandır** iletişim kutusunda, **SQL Server**'ın yanındaki **Yeni**'ye tıklayın. 

Benzersiz bir sunucu adı oluşturulur. Bu ad, mantıksal sunucunuz için varsayılan URL'nin bir parçası olarak kullanılır (`<server_name>.database.windows.net`). Azure'daki tüm mantıksal sunucu örnekleri arasında benzersiz olmalıdır. Sunucu adını değiştirebilirsiniz, ama bu öğreticide oluşturulan değeri tutun.

Bir yönetici kullanıcı adı ve parolası ekleyin. Parola karmaşıklık gereksinimleri için bkz. [Parola İlkesi](/sql/relational-databases/security/password-policy).

Bu kullanıcı adını ve parolayı unutmayın. Daha sonra mantıksal sunucu örneğini yönetirken bunlara ihtiyacınız olacaktır.

> [!IMPORTANT]
> Bağlantı dizelerindeki (Visual Studio ve ayrıca App Service’te) parolanız maskelenmiş olsa bile, bir yerlerde tutulması uygulamanızın saldırı yüzeyine katkıda bulunur. App Service, kodunuzda veya uygulama yapılandırmanızda gizli dizileri tutma gereksinimini tamamen ortadan kaldırarak bu riski yok etmek için [yönetilen hizmet kimliklerini](overview-managed-identity.md) kullanabilir. Daha fazla bilgi için bkz. [Sonraki adımlar](#next-steps).

![SQL Server örneği oluşturma](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

**Tamam** düğmesine tıklayın. **SQL Veritabanını Yapılandır** iletişim kutusunu henüz kapatmayın.

### <a name="create-a-sql-database"></a>SQL Veritabanı oluşturma

**SQL Veritabanını Yapılandır** iletişim kutusunda: 

* Varsayılan olarak oluşturulmuş **Veritabanı Adı**'nı koruyun.
* **Bağlantı Dizesi Adı** için *MyDbConnection* yazın. Bu ad, *Models/MyDatabaseContext.cs*'de başvurulan bağlantı dizesiyle eşleşmelidir.
* **Tamam**’ı seçin.

![SQL Veritabanı'nı yapılandırma](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

**App Service Oluştur** iletişim kutusunda, yapılandırdığınız kaynaklar gösterilir. **Oluştur**'a tıklayın. 

![oluşturduğunuz kaynaklar](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)

Sihirbaz Azure kaynaklarını oluşturmayı tamamladığında, ASP.NET uygulamanızı Azure'a yayımlar. Varsayılan tarayıcınız dağıtılan uygulamanın URL'siyle başlatılır. 

Yapılacak birkaç işlem ekleyin.

![Azure uygulaması 'nda yayınlanan ASP.NET uygulaması](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Tebrikler! Veri temelli ASP.NET uygulamanız Azure App Service'de çalışıyor.

## <a name="access-the-sql-database-locally"></a>SQL Veritabanı'na yerel olarak erişim

Visual Studio, yeni SQL Veritabanınızı **SQL Server Nesne Gezgini**'nde kolayca incelemenize ve yönetmenize olanak tanır.

### <a name="create-a-database-connection"></a>Veritabanı bağlantısı oluşturma

**Görünüm** menüsünde **SQL Server Nesne Gezgini**'ni seçin.

**SQL Server Nesne Gezgini**'nin üst kısmında **SQL Server Ekle** düğmesine tıklayın.

### <a name="configure-the-database-connection"></a>Veritabanı bağlantısını yapılandırma

**Bağlan** iletişim kutusunda **Azure** düğümünü genişletin. Azure'daki tüm SQL Veritabanı örnekleriniz burada listelenir.

Daha önce oluşturduğunuz SQL Veritabanı'nı seçin. Daha önce oluşturduğunuz bağlantı, en altta otomatik olarak doldurulur.

Daha önce oluşturduğunuz veritabanı yöneticisi parolasını yazın ve **Bağlan**'a tıklayın.

![Visual Studio'dan veritabanı bağlantısını yapılandırma](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>Bilgisayarınızdan istemci bağlantısına izin verme

**Yeni güvenlik duvarı kuralı oluştur** iletişim kutusu açılır. Varsayılan olarak, SQL veritabanı örneğiniz yalnızca Azure uygulamanız gibi Azure hizmetlerinden gelen bağlantılara izin verir. Veritabanınıza bağlanmak için, SQL Veritabanı örneğinde bir güvenlik duvarı kuralı oluşturun. Güvenlik duvarı kuralı yerel bilgisayarınızın genel IP adresine izin verir.

İletişim kutusu bilgisayarınızın genel IP adresiyle önceden doldurulmuştur.

**İstemci IP değerimi ekle**'nin seçili olduğundan emin olun ve **Tamam**'a tıklayın. 

![SQL Veritabanı örneği için güvenlik duvarını ayarlama](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Visual Studio SQL Veritabanı örneğiniz için güvenlik duvarı ayarını oluşturmayı tamamladığında, bağlantınız **SQL Server Nesne Gezgini**'nde gösterilir.

Burada sorgu çalıştırma, görünümler ve saklı yordamlar oluşturma gibi daha birçok yaygın veritabanı işlemini yapabilirsiniz. 

Bağlantınız > **Veritabanları** >  **&lt;veritabanınız>**  > **Tablolar**'ı genişletin. `Todoes` tablosuna sağ tıklayın ve **Verileri Görüntüle**'yi seçin. 

![SQL Veritabanı nesnelerini inceleyin](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Uygulamayı Code First Migrations ile güncelleştirme

Azure 'da veritabanınızı ve uygulamanızı güncelleştirmek için Visual Studio 'daki tanıdık araçları kullanabilirsiniz. Bu adımda, veritabanı şemanızda değişiklik yapmak ve bunu Azure'a yayımlamak için Entity Framework'te Code First Migrations'ı kullanırsınız.

Entity Framework Code First Migrations'ı kullanma hakkında daha fazla bilgi için bkz. [MVC 5 Kullanarak Entity Framework 6 Code First ile Çalışmaya Başlama](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="update-your-data-model"></a>Veri modelinizi güncelleştirme

Kod düzenleyicide _Models\Todo.cs_ dosyasını açın. `ToDo` sınıfına aşağıdaki özelliği ekleyin:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Code First Migrations’ı yerel olarak çalıştırma

Yerel veritabanınızda güncelleştirme yapmak için birkaç komut çalıştırın. 

**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu**'na tıklayın.

Paket Yöneticisi Konsolu penceresinde Code First Migrations'ı etkinleştirin:

```powershell
Enable-Migrations
```

Bir geçiş ekleyin:

```powershell
Add-Migration AddProperty
```

Yerel veritabanınızı güncelleştirin:

```powershell
Update-Database
```

Uygulamayı çalıştırmak için `Ctrl+F5` tuşlarına basın. Düzenle, ayrıntılar ve sil bağlantılarını test edin.

Uygulama hatasız yüklerse, Code First Migrations başarılı olmuştur. Öte yandan, sayfanız yine aynı görünür çünkü uygulama mantığınız henüz bu yeni özelliği kullanmamaktadır. 

### <a name="use-the-new-property"></a>Yeni özelliği kullanma

`Done` özelliğini kullanarak kodunuzda birkaç değişiklik yapın. Bu öğreticide, daha kolay uygulama için, işlemin nasıl çalıştığını görmek üzere yalnızca `Index` ve `Create` görünümlerini değiştireceksiniz.

_Controllers\TodosController.cs_ dosyasını açın.

52. satırda `Create()` yöntemini bulun ve `Done` değerini `Bind` özniteliğindeki özellik listesine ekleyin. Hazır olduğunuzda, `Create()` metot imzanız aşağıdaki koda benzer şekilde görünür:

```csharp
public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
```

_Views\Todos\Create.cshtml_ dosyasını açın.

Razor kodunda, `<div class="form-group">` kullanan bir `model.Description` öğesi ve `<div class="form-group">` kullanan başka bir `model.CreatedDate` öğesi görürsünüz. Bu iki öğenin hemen arkasına `<div class="form-group">` kullanan başka bir `model.Done` öğesi ekleyin:

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

_Views\Todos\Index.cshtml_ dosyasını açın.

Boş `<th></th>` öğesini arayın. Bu öğenin hemen üstüne aşağıdaki Razor kodunu ekleyin:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

`<td>` yardımcı yöntemlerini içeren `Html.ActionLink()` öğesini bulun. Bu _yönteminin_üst kısmına`<td>`, aşağıdaki Razor koduyla başka bir `<td>` öğesi ekleyin:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

`Index` ve `Create` görünümlerindeki değişiklikleri görmek için yapmanız gerekenler bu kadardır. 

Uygulamayı çalıştırmak için `Ctrl+F5` tuşlarına basın.

Artık yapılacak bir öğe ekleyip öğeyi **Bitti** olarak işaretleyebilirsiniz. Daha sonra öğe, ana sayfanızda tamamlanmış bir öğe olarak görünmelidir. `Edit` görünümünü değiştirmediğinizden, `Done` görünümünün `Edit` alanında görünmediğini göz önünde bulundurun.

### <a name="enable-code-first-migrations-in-azure"></a>Azure'da Code First Migrations’ı etkinleştirme

Artık kod değişikyseniz, veritabanı geçişi de dahil olmak üzere, Azure uygulamanızda yayımlayın ve SQL veritabanınızı Code First Migrations de güncelleştirebilirsiniz.

Aynı daha önce yaptığınız gibi, projenize sağ tıklayıp **Yayımla**'yı seçin.

Yayımlama ayarlarını açmak için **Yapılandır**'a tıklayın.

![Yayımlama ayarlarını açma](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

Sihirbazda, **İleri**’ye tıklayın.

**MyDatabaseContext (MyDbConnection)** alanına SQL Veritabanınızın bağlantı dizesinin girildiğinden emin olun. Açılan listeden **myToDoAppDb** veritabanını seçmeniz gerekebilir. 

**Önce Kod Uygulamalı Geçişler (uygulama başlatılırken çalışır)** öğesini seçin ve **Kaydet**'e tıklayın.

![Azure uygulamasında Code First Migrations etkinleştirme](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>Değişikliklerinizi yayımlama

Azure uygulamanızda Code First Migrations etkinleştirdığınıza göre, kod Değişikliklerinizi yayımlayın.

Yayımlama sayfasında **Yayımla**'ya tıklayın.

Yapılacaklar öğelerini yeniden eklemeyi deneyin ve **Bitti**'yi seçin; bunlar giriş sayfanızda tamamlanmış öğe olarak gösteriliyor olmalıdır.

![Code First geçişten sonra Azure uygulaması](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Mevcut yapılacak öğeleriniz görüntülenmeye devam eder. ASP.NET uygulamanızı yeniden yayımladığınızda, SQL Veritabanınızdaki mevcut veriler kaybolmaz. Ayrıca, Code First Migrations yalnızca veri şemasını değiştirir ve mevcut verilerinizde herhangi bir değişiklik yapmaz.


## <a name="stream-application-logs"></a>Uygulama günlüklerinin akışı yapma

İzleme iletilerini doğrudan Azure uygulamanızdan Visual Studio 'ya aktarabilirsiniz.

_Controllers\TodosController.cs_ dosyasını açın.

Her eylem bir `Trace.WriteLine()` yöntemiyle başlar. Bu kod, Azure uygulamanıza nasıl izleme iletileri ekleneceğini göstermek için eklenir.

### <a name="open-server-explorer"></a>Sunucu Gezgini'ni açma

**Görünüm** menüsünde **Sunucu Gezgini**'ni seçin. Azure uygulamanız için günlük kaydını **Sunucu Gezgini**' de yapılandırabilirsiniz. 

### <a name="enable-log-streaming"></a>Günlük akışını etkinleştirme

**Sunucu Gezgini**'nde **Azure** > **App Service**'i genişletin.

Azure uygulamasını ilk oluşturduğunuzda oluşturduğunuz **Myresourcegroup** kaynak grubunu genişletin.

Azure uygulamanıza sağ tıklayın ve **akış günlüklerini görüntüle**' yi seçin.

![Günlük akışını etkinleştirme](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

Şimdi **Çıkış** penceresine günlüklerin akışı yapılır. 

![Çıkış penceresinde günlük akışı](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

Öte yandan, henüz hiçbir izleme iletisi görmezsiniz. Bunun nedeni, **akış günlüklerini görüntüle**' yi Ilk seçtiğinizde Azure uygulamanız, yalnızca hata olaylarını günlüğe kaydeden (`Trace.TraceError()` yöntemi ile) izleme düzeyini `Error`olarak ayarlar.

### <a name="change-trace-levels"></a>İzleme düzeylerini değiştirme

İzleme düzeylerini değiştirip başka izleme iletilerinin de çıkışını almak için, **Sunucu Gezgini**'ne dönün.

Azure uygulamanıza tekrar sağ tıklayın ve **ayarları görüntüle**' yi seçin.

**Uygulama Günlüğü (Dosya Sistemi)** açılan listesinde **Ayrıntılı**'yı seçin. **Kaydet** düğmesine tıklayın.

![İzleme düzeyini Ayrıntılı olarak değiştirme](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> Her düzeyde hangi tür iletilerin gösterildiğini görmek için farklı izleme düzeyleriyle denemeler yapabilirsiniz. Örneğin, **Bilgi** düzeyi `Trace.TraceInformation()`, `Trace.TraceWarning()` ve `Trace.TraceError()` tarafından oluşturulan tüm günlükleri içerir, ama `Trace.WriteLine()` tarafından oluşturulan günlükleri içermez.
>
>

Tarayıcınızda uygulamanıza tekrar gidin *&lt;app name >. azurewebsites. net*' te, Azure 'da yapılacaklar listesi uygulamasının etrafında tıklamanıza çalışın. Artık Visual Studio'da izleme iletileri akışla **Çıkış** penceresine aktarılır.

```console
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```



### <a name="stop-log-streaming"></a>Günlük akışını durdurma

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
> * Azure’da SQL Veritabanı oluşturma
> * ASP.NET uygulamasını SQL Veritabanı'na bağlama
> * Uygulamayı Azure’da dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki günlüklerin terminalinize akışını sağlama
> * Uygulamayı Azure portalında yönetme

Azure SQL Veritabanı bağlantınızın güvenliğini kolayca iyileştirmeyi öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Azure kaynakları için yönetilen kimlikleri kullanarak SQL Veritabanına güvenli bir şekilde erişme](app-service-web-tutorial-connect-msi.md)
