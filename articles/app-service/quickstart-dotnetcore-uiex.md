---
title: 'Hızlı başlangıç: C# ASP.NET Core uygulaması oluşturma'
description: İlk ASP.NET Core uygulamanızı dağıtarak Azure App Service Web uygulamaları çalıştırmayı öğrenin.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff86bedf47395b50dc25e552b8b3ed4176e23b65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769112"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Hızlı başlangıç: Azure 'da ASP.NET Core Web uygulaması oluşturma

::: zone pivot="platform-windows"  

Bu hızlı başlangıçta, ilk ASP.NET Core Web uygulamanızı oluşturma ve dağıtma hakkında bilgi edineceksiniz. <abbr title="Web uygulamalarını, REST API 'Leri ve mobil arka uç uygulamalarını barındırmak için HTTP tabanlı bir hizmet.">Azure App Service</abbr>. App Service .NET 5,0 uygulamalarını destekler.

İşiniz bittiğinde bir Azure <abbr title="Birim olarak yönetebileceğiniz ilgili Azure kaynakları için mantıksal bir kapsayıcı.">kaynak grubu</abbr>, bir <abbr title="Uygulamanızı barındıran Web sunucusu grubunun konumunu, boyutunu ve özelliklerini belirten plan.">App Service planı</abbr> ve bir <abbr title="Uygulama kodunuzu, DNS ana bilgisayar adlarını, sertifikalarınızı ve ilgili kaynaklarınızı içeren Web uygulamanızın temsili.">App Service uygulaması</abbr> Dağıtılmış bir örnek ASP.NET Core uygulaması ile.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. ortamınızı hazırlayın

- Etkin bir **Azure hesabı alın** <abbr title="Azure 'da, genellikle bir kuruluş içindeki bir birey veya departmanla ilişkili kaynakları yönettiğiniz temel kuruluş yapısıdır.">aboneliği</abbr>. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/dotnet/).
- **ASP.net ve Web geliştirme** iş yüküyle **<a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>** ' i yükledikten sonra.

<details>
<summary>Visual Studio 2019 zaten var mı?</summary>
Visual Studio 2019 zaten yüklüyse:

<ul>
<li>Güncelleştirmeler için <strong>Yardım</strong> denetimi ' ni seçerek <strong>en son güncelleştirmeleri</strong> Visual Studio 'ya yükler &gt; <strong></strong>. En son güncelleştirmeler .NET 5,0 SDK 'sını içerir.</li>
<li><strong>Araçlar</strong> al araçlar ve Özellikler ' <strong>i seçerek iş yükünü ekleyin</strong> &gt; <strong></strong>.</li>
</ul>
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. ASP.NET Core Web uygulaması oluşturma

1. Visual Studio 'Yu açın ve **Yeni proje oluştur**' u seçin.

1. **Yeni proje oluştur**' da, **ASP.NET Core Web uygulaması** ' nı seçin ve **C#** ' ın bu seçime Ilişkin dillerde listelendiğini onaylayın, ardından **İleri**' yi seçin.

1. **Yeni projenizi yapılandırın** bölümünde, Web uygulaması projenizi *Myfirstazurewebapp* olarak adlandırın ve **Oluştur**' u seçin.

   ![Web uygulaması projenizi yapılandırma](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Bir .NET 5,0 uygulaması için, açılan menüde **ASP.NET Core 5,0** ' i seçin. Aksi takdirde, varsayılan değeri kullanın.

1. Azure 'a herhangi bir türde ASP.NET Core Web uygulaması dağıtabilirsiniz, ancak bu hızlı başlangıç için, **ASP.NET Core Web uygulaması** şablonunu seçin. **Kimlik doğrulamasının** **kimlik doğrulaması yok** olarak ayarlandığından ve başka bir seçeneğin seçilolmadığından emin olun. Ardından **Oluştur**’u seçin.

   ![Yeni bir ASP.NET Core Web uygulaması oluşturma](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1.   >  Web uygulamanızı yerel olarak çalıştırmak için Visual Studio menüsünden hata ayıklama **olmadan Başlat** ' ı seçin.

   ![Yerel olarak çalışan Web uygulaması](./media/quickstart-dotnetcore/web-app-running-locally.png)

<hr/> 

## <a name="3-publish-your-web-app"></a>3. Web uygulamanızı yayımlayın

1. **Çözüm Gezgini**, **Myfirstazurewebapp** projesine sağ tıklayın ve **Yayımla**' yı seçin. 

1. **Yayımla**' da **Azure** ' ı seçin ve **İleri**' ye tıklayın.

1. Seçenekleriniz, Azure 'da oturum açmış olup olmadığınız ve bir Azure hesabına bağlı bir Visual Studio hesabınız olup olmadığına bağlıdır. Azure aboneliğinizde oturum açmak için **Hesap Ekle** veya **oturum aç** seçeneklerinden birini belirleyin. Zaten oturum açtıysanız istediğiniz hesabı seçin.

   ![Azure'da oturum açma](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. **App Service örneklerinin** sağında, ' ye tıklayın **+** .

   ![Yeni App Service uygulaması](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. **Abonelik** için, listelenen aboneliği kabul edin veya açılan listeden yeni bir tane seçin.

1. **Kaynak grubu** için **Yeni**' yi seçin. **Yeni kaynak grubu adı** alanına *Myresourcegroup* yazın ve **Tamam**' ı seçin. 

1. **Barındırma planı** için **Yeni**' yi seçin. 

1. **Barındırma planı: Yeni oluştur** iletişim kutusunda, aşağıdaki tabloda belirtilen değerleri girin:

   | Ayar  | Önerilen Değer |
   | -------- | --------------- |
   | **Barındırma Planı**  | *myFirstAzureWebAppPlan* |
   | **Konum**      | *West Europe* |
   | **Boyut**          | *Ücretsiz* |
   
   ![Yeni barındırma planı oluştur](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. **Ad** alanına benzersiz bir uygulama adı girin.

    <details>
        <summary>Hangi karakterleri kullanabilirim?</summary>
        Geçerli karakterler şunlardır-z, A-Z, 0-9 ve-. Otomatik olarak oluşturulan benzersiz adı kabul edebilirsiniz. Web uygulamasının URL 'SI http:// <code>&lt;app-name&gt;.azurewebsites.net</code> , burada <code>&lt;app-name&gt;</code> uygulamanızın adıdır.
    </details>

1. Azure kaynaklarını oluşturmak için **Oluştur** ' u seçin. 

   ![Uygulama kaynakları oluşturma](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Sihirbazın Azure kaynaklarını oluşturma işleminin bitmesini bekleyin. Sihirbazı kapatmak için **son** ' u seçin.

1. **Yayımla** sayfasında, projenizi dağıtmak için **Yayımla** ' ya tıklayın. 

    <details>
        <summary>Visual Studio ne yapıyor?</summary>
        Visual Studio uygulamayı oluşturur, paketler ve Azure 'da yayımlar ve ardından uygulamayı varsayılan tarayıcıda başlatır.
    </details>

   ![Azure 'da çalışan yayımlanmış ASP.NET Web uygulaması](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. uygulamayı güncelleştirme ve yeniden dağıtma

1. **Çözüm Gezgini**' de, projeniz altında, **Sayfalar**  >  **Index. cshtml** dosyasını açın.

1. Tüm `<div>` etiketi aşağıdaki kodla değiştirin:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Azure’a yeniden dağıtmak için **Çözüm Gezgini**’nde **myFirstAzureWebApp** projesine sağ tıklayıp **Yayımla**’yı seçin.

1. **Yayımla** Özeti sayfasında **Yayımla**' yı seçin.

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

    Yayımlama tamamlandığında Visual Studio, web uygulamasının URL’si ile bir tarayıcı başlatır.

    ![Azure 'da çalışan güncelleştirilmiş ASP.NET Web uygulaması](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

<hr/> 

## <a name="5-manage-the-azure-app"></a>5. Azure uygulamasını yönetme

1. [Azure Portal](https://portal.azure.com)gidin ve **uygulama hizmetleri**' ni arayıp seçin.

    ![Uygulama hizmetlerini seçin](./media/quickstart-dotnetcore/app-services.png)
    
1. **Uygulama hizmetleri** sayfasında, Web uygulamanızın adını seçin.

    :::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Örnek bir Web uygulaması seçili olan App Services sayfasının ekran görüntüsü.":::

1. Web uygulamanıza ilişkin **genel bakış** sayfası, göz at, durdur, Başlat, yeniden Başlat ve Sil gibi temel yönetime yönelik seçenekler içerir. Sol menü, uygulamanızı yapılandırmak için daha fazla sayfa sağlar.

    ![Azure portal App Service](./media/quickstart-dotnetcore/web-app-overview-page.png)
    
<hr/> 

## <a name="6-clean-up-resources"></a>6. Kaynakları Temizleme

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak grupları**' nı seçin. Ardından, **kaynak grupları** sayfasında, **myresourcegroup** öğesini seçin.

1. **Myresourcegroup** sayfasında, listelenen kaynakların silmek istedikleriniz olduğundan emin olun.

1. **Kaynak grubunu sil**' i seçin, onaylamak için metin kutusuna **myresourcegroup** yazın ve ardından **Sil**' i seçin.

<hr/> 

## <a name="next-steps"></a>Sonraki adımlar

Bir .NET Core uygulaması oluşturmayı ve bir SQL veritabanına bağlamayı öğrenmek için bir sonraki makaleye ilerleyin:

- [SQL Veritabanı ile ASP.NET Core](tutorial-dotnetcore-sqldb-app.md)
- [ASP.NET Core uygulamasını yapılandırma](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
Bu hızlı başlangıçta bir [.NET Core](/aspnet/core/) uygulamasının nasıl oluşturulacağı gösterilmektedir <abbr title="Linux’ta App Service Linux işletim sistemini kullanan yüksek oranda ölçeklenebilir, otomatik olarak düzeltme eki uygulayan bir web barındırma hizmeti sağlar.">Linux’ta App Service</abbr>. Uygulamayı [Azure CLI](/cli/azure/get-started-with-azure-cli)kullanarak oluşturur ve .NET Core kodunu uygulamaya dağıtmak için git 'i kullanırsınız.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. ortamınızı hazırlayın

- Etkin aboneliğe sahip **bir Azure hesabı alın** . [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/dotnet/).
- En son <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">.NET Core 3,1 SDK</a> veya <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">.NET 5,0 SDK 'yı</a> **yükler** .
- **<a href="/cli/azure/install-azure-cli" target="_blank">En son Azure CLI 'Yı yükler</a>**.

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="2-create-the-app-locally"></a>2. uygulamayı yerel olarak oluşturun

1. `mkdir hellodotnetcore`Dizinini oluşturmak için öğesini çalıştırın.

    ```bash
    mkdir hellodotnetcore
    ```

1. `cd hellodotnetcore`Dizini değiştirmek için ' i çalıştırın. 

    ```bash
    cd hellodotnetcore
    ```

1. `dotnet new web`Yeni bir .NET Core uygulaması oluşturmak için öğesini çalıştırın.

    ```bash
    dotnet new web
    ```

<hr/> 

## <a name="3-run-the-app-locally"></a>3. uygulamayı yerel olarak çalıştırın

1. `dotnet run`Azure 'a dağıtırken nasıl göründüğünü görmek için ' i çalıştırın.

    ```bash
    dotnet run
    ```
    
1. **Bir Web tarayıcısı açın** ve konumundaki uygulamaya gidin `http://localhost:5000` .

![Tarayıcı ile test etme](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="4-sign-into-azure"></a>4. Azure 'da oturum açın

`az login`Azure 'da oturum açmak için ' i çalıştırın.

```azurecli
az login
```

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="5-deploy-the-app"></a>5. uygulamayı dağıtın

1. **Çalıştır** `az webapp up` yerel klasörünüzde. <app-name> değerini genel olarak benzersiz bir adla **değiştirin** .

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Sorun giderme</summary>
    <ul>
    <li><code>az</code>Komut tanınmazsa, <a href="#1-prepare-your-environment">ortamınızı hazırlama</a>bölümünde AÇıKLANDıĞı gibi Azure CLI 'nın yüklü olduğundan emin olun.</li>
    <li><code>&lt;app-name&gt;</code>Tüm Azure genelinde benzersiz olan bir adla değiştirin ( <em> geçerli karakterler <code>a-z</code> , <code>0-9</code> ve <code>-</code> </em> ). İyi bir model, şirketinizin adının ve uygulama tanımlayıcısının bir birleşimini kullanmaktır.</li>
    <li><code>--sku F1</code>Bağımsız değişkeni, ücretsiz fiyatlandırma katmanında Web uygulaması oluşturur. Saatlik maliyet içeren daha hızlı bir Premium katmanı kullanmak için bu bağımsız değişkeni atlayın.</li>
    <li>İsteğe bağlı olarak <code>--location &lt;location-name&gt;</code> <code>&lt;location-name&gt;</code> , kullanılabilir bir Azure bölgesi olan bağımsız değişkeni ekleyebilirsiniz. Komutunu çalıştırarak, Azure hesabınız için izin verilen bölgelerin bir listesini alabilirsiniz <a href="/cli/azure/appservice#az_appservice_list_locations"> <code>az account list-locations</code> </a> .</li>
    </ul>
    </details>
    
1. Komutun tamamlanmasını bekleyin. Bu işlem birkaç dakika sürebilir ve "uygulamayı http:// &lt; app-name. azurewebsites.net konumunda başlatabilirsiniz" ile sona erer &gt; .

    <details>
    <summary>Neler <code>az webapp up</code> yapılıyor?</summary>
    <p><code>az webapp up</code> komutu şu eylemleri gerçekleştirir:</p>
    <ul>
    <li>Varsayılan kaynak grubunu oluşturur.</li>
    <li>Varsayılan bir App Service planı oluşturun.</li>
    <li>Belirtilen ada sahip <a href="/cli/azure/webapp#az_webapp_create">bir App Service uygulaması oluşturun</a> .</li>
    <li><a href="/azure/app-service/deploy-zip">ZIP</a> dosyalarını geçerli çalışma dizininden uygulamaya dağıtın.</li>
    <li>Çalıştırılırken, kaynak oluşturma, günlüğe kaydetme ve ZIP dağıtımı hakkında iletiler sağlar.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Az WebApp up komutunun örnek çıktısı](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

![Az WebApp up komutunun örnek çıktısı](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="6-browse-to-the-app"></a>6. uygulamaya gidin

Web tarayıcınızı kullanarak **dağıtılan uygulamaya gidin** .

```bash
http://<app_name>.azurewebsites.net
```

![Azure'da çalışan örnek uygulama](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="7-update-and-redeploy-the-code&quot;></a>7. kodu güncelleştirin ve yeniden dağıtın

1. Yerel dizinde **_Startup. cs_ dosyasını açın** . 

1. Yöntem çağrısındaki metinde **küçük bir değişiklik yapın** `context.Response.WriteAsync` .

    ```csharp
    await context.Response.WriteAsync(&quot;Hello Azure!");
    ```
    
1. **Değişikliklerinizi kaydedin**.

1. **Çalıştır** `az webapp up` yeniden dağıtmak için:

    ```azurecli
    az webapp up --os-type linux
    ```
    
    <details>
    <summary><code>az webapp up</code>Bu süre ne yapıyor?</summary>
    Komutu ilk kez çalıştırdığınızda, uygulama adı, kaynak grubu ve App Service planını proje kökünden <i>. Azure/config</i> dosyasında kaydettiniz. Projeyi proje kökünden yeniden çalıştırdığınızda, <i>. Azure/config</i>' de kaydedilen değerleri kullanır, App Service kaynakların zaten var olduğunu algılar ve ZIP dağıtımını yeniden gerçekleştirir.
    </details>
    
1. Dağıtım tamamlandıktan sonra, daha önce açılan tarayıcı penceresinde Yenile ' ye **basın** .

    ![Azure'da çalışan güncelleştirilmiş örnek uygulama](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)
    
[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. yeni Azure uygulamanızı yönetin

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> gidin.

1. Sol menüden **uygulama hizmetleri**' ne ve ardından Azure uygulamanızın adına tıklayın.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Uygulama Hizmetleri sayfasının seçili örnek bir Azure uygulaması gösteren ekran görüntüsü.":::

1. Genel Bakış sayfası, göz at, durdur, Başlat, yeniden Başlat ve Sil gibi temel yönetim görevlerini gerçekleştirebileceğiniz yerdir. Soldaki menü, uygulamanızı yapılandırmak için farklı sayfalar sağlar. 

    ![Azure portalında App Service sayfası](media/quickstart-dotnetcore/portal-app-overview-up.png)
    
<hr/> 

## <a name="9-clean-up-resources"></a>9. Kaynakları Temizleme

**Çalıştır** `az group delete --name myResourceGroup` kaynak grubunu silmek için.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: SQL veritabanı ile uygulama ASP.NET Core](tutorial-dotnetcore-sqldb-app.md)
- [ASP.NET Core uygulamasını yapılandırma](configure-language-dotnetcore.md)

::: zone-end
