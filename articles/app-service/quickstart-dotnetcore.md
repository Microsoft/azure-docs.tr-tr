---
title: 'Hızlı başlangıç: ASP.NET Web uygulaması dağıtma'
description: İlk ASP.NET uygulamanızı dağıtarak Azure App Service Web uygulamaları çalıştırmayı öğrenin.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 482bf6d29fbc1e982ee4d17099d82915ff3a0241
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762470"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET Core 3.1 or .NET 5.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET Core 3.1      | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET 5.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Hızlı başlangıç: ASP.NET Web uygulaması dağıtma

Bu hızlı başlangıçta, [Azure App Service](overview.md)için ilk ASP.NET Web uygulamanızı nasıl oluşturacağınızı ve dağıtacağınızı öğreneceksiniz. App Service .NET uygulamalarının çeşitli sürümlerini destekler ve yüksek düzeyde ölçeklenebilir, kendini yayama bir Web barındırma hizmeti sağlar. ASP.NET Web Apps, platformlar arası ve Linux veya Windows üzerinde barındırılabilir. İşiniz bittiğinde, bir App Service barındırma planından ve dağıtılan bir Web uygulamasına sahip bir App Service içeren bir Azure Kaynak grubunuz olacaktır.

> [!TIP]
> .NET Core 3,1, .NET 'in geçerli uzun süreli destek (LTS) sürümüdür. Daha fazla bilgi için bkz. [.net destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="prerequisites"></a>Önkoşullar

:::zone target="docs" pivot="development-environment-vs"

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/dotnet).
- **ASP.net ve Web geliştirme** iş yüküyle <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019</a> .

    Visual Studio 2019 ' ü zaten yüklediyseniz:

    -   >  **Güncelleştirmeler için yardım denetimi**' ni seçerek en son güncelleştirmeleri Visual Studio 'ya yükler.
    - **Araçlar**  >  **Al araçlar ve Özellikler '** i seçerek iş yükünü ekleyin.

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>.
- <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure Araçları</a> uzantısı.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> En son .NET Core 3,1 SDK 'sını yükler. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> En son .NET 5,0 SDK 'sını yükler. </a>

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> .NET Framework 4,8 Geliştirici paketini yükler. </a>

> [!NOTE]
> Visual Studio Code platformlar arası, ancak .NET Framework değildir. Visual Studio Code sahip .NET Framework uygulamalar geliştiriyorsanız, derleme bağımlılıklarını karşılamak için bir Windows makinesi kullanmayı düşünün.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/dotnet).
- <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>.
- .NET SDK (çalışma zamanı ve CLı içerir).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> En son .NET Core 3,1 SDK 'sını yükler. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> En son .NET 5,0 SDK 'sını yükler. </a>

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> En son .net 5,0 SDK 'Sını yükler. </a> ve <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> .NET Framework 4,8 Geliştirici paketi. </a>

> [!NOTE]
> [.Net CLI](/dotnet/core/tools) , platformlar arası bir, ancak .NET Framework değildir. .NET CLı ile .NET Framework uygulamalar geliştirdiğinizden, derleme bağımlılıklarını karşılamak için bir Windows makinesi kullanmayı düşünün.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>ASP.NET web uygulaması oluşturma

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Visual Studio 'Yu açın ve **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur**' da, **ASP.NET Web Core uygulaması**' nı bulun ve seçin ve ardından **İleri**' yi seçin.
1. **Yeni projenizi yapılandırın** bölümünde, uygulamayı _Myfirstazurewebapp_ olarak adlandırın ve ardından **İleri**' yi seçin.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="ASP.NET Core 3,1 Web uygulamasını yapılandırma" border="true":::

1. **.NET Core 3,1 (uzun süreli destek)** seçeneğini belirleyin.
1. **Kimlik doğrulama türünün** **none** olarak ayarlandığından emin olun. **Oluştur**’u seçin.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio-kimlik doğrulama türü için .NET Core 3,1 ve hiçbiri ' ni seçin." border="true":::

1.   >  Web uygulamasını yerel olarak çalıştırmak için Visual Studio menüsünden hata ayıklama **olmadan Başlat** ' ı seçin.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio-.NET Core 3,1 yerel olarak gezinme" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Visual Studio 'Yu açın ve **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur**' da, **ASP.NET Web Core uygulaması**' nı bulun ve seçin ve ardından **İleri**' yi seçin.
1. **Yeni projenizi yapılandırın** bölümünde, uygulamayı _Myfirstazurewebapp_ olarak adlandırın ve ardından **İleri**' yi seçin.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio-ASP.NET 5,0 Web uygulamasını yapılandırın." border="true":::

1. **.NET Core 5,0 (geçerli)** seçeneğini belirleyin.
1. **Kimlik doğrulama türünün** **none** olarak ayarlandığından emin olun. **Oluştur**’u seçin.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio-.NET Core 5,0 seçilirken ek bilgi." border="true":::

1.   >  Web uygulamasını yerel olarak çalıştırmak için Visual Studio menüsünden hata ayıklama **olmadan Başlat** ' ı seçin.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio-ASP.NET Core 5,0 yerel olarak çalışıyor." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

1. Visual Studio 'Yu açın ve **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur**' da, **ASP.NET Web uygulaması (.NET Framework)** bulun ve seçin ve ardından **İleri**' yi seçin.
1. **Yeni projenizi yapılandırın** bölümünde, uygulamayı _Myfirstazurewebapp_ olarak adlandırın ve ardından **Oluştur**' u seçin.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio-ASP.NET Framework 4,8 Web uygulamasını yapılandırın." border="true":::

1. **MVC** şablonunu seçin.
1. **Kimlik doğrulamanın** **kimlik doğrulaması yok** olarak ayarlandığından emin olun. **Oluştur**’u seçin.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio-MVC şablonunu seçin." border="true":::

1.   >  Web uygulamasını yerel olarak çalıştırmak için Visual Studio menüsünden hata ayıklama **olmadan Başlat** ' ı seçin.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio-ASP.NET Framework 4,8 yerel olarak çalışıyor." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

_Myfirstazurewebapp_ adlı yeni bir klasör oluşturun ve Visual Studio Code açın. <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">Terminal</a> penceresini açın ve komutunu kullanarak yeni bir .NET Web uygulaması oluşturun [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) .

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> `--target-framework-override`Bayrak, proje için hedef çerçeve bilinen adı (tfd) için serbest biçimli bir metin değiştirme ve destekleyici şablonun mevcut veya derlenir olmasını *garanti* etmez. Yalnızca Windows üzerinde .NET Framework uygulamaları oluşturup çalıştırabilirsiniz.

---

Visual Studio Code **terminalden** komutunu kullanarak uygulamayı yerel olarak çalıştırın [`dotnet run`](/dotnet/core/tools/dotnet-run) .

```dotnetcli
dotnet run
```

Bir web tarayıcısı açın ve `https://localhost:5001` konumundaki uygulamaya gidin.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Şablon ASP.NET Core 3,1 Web uygulamasının sayfada görüntülendiğini görürsünüz.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code .NET Core 3,1 ' i tarayıcıda yerel olarak çalıştırın." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Şablon ASP.NET Core 5,0 Web uygulamasının sayfada görüntülendiğini görürsünüz.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code-tarayıcıda .NET 5,0 ' i yerel olarak çalıştırın." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Şablon ASP.NET Framework 4,8 Web uygulamasının sayfada görüntülendiğini görürsünüz.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code-tarayıcıda .NET 4,8 ' i yerel olarak çalıştırın." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Makinenizde çalışan bir dizine bir Terminal penceresi açın. Komutunu kullanarak yeni bir .NET Web uygulaması oluşturun [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) ve ardından dizinleri yeni oluşturulan uygulamayla değiştirin.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> `--target-framework-override`Bayrak, proje için hedef çerçeve bilinen adı (tfd) için serbest biçimli bir metin değiştirme ve destekleyici şablonun mevcut veya derlenir olmasını *garanti* etmez. Windows üzerinde yalnızca .NET Framework uygulamalar oluşturabilirsiniz.

---

Aynı Terminal oturumundan komutunu kullanarak uygulamayı yerel olarak çalıştırın [`dotnet run`](/dotnet/core/tools/dotnet-run) .

```dotnetcli
dotnet run
```

Bir web tarayıcısı açın ve `https://localhost:5001` konumundaki uygulamaya gidin.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Şablon ASP.NET Core 3,1 Web uygulamasının sayfada görüntülendiğini görürsünüz.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Yerel tarayıcıda Visual Studio Code-ASP.NET Core 3,1." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Şablon ASP.NET Core 5,0 Web uygulamasının sayfada görüntülendiğini görürsünüz.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Yerel tarayıcıda Visual Studio Code-ASP.NET Core 5,0." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Şablon ASP.NET Framework 4,8 Web uygulamasının sayfada görüntülendiğini görürsünüz.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Yerel tarayıcıda Visual Studio Code-ASP.NET Framework 4,8." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Web uygulamanızı yayımlama

Web uygulamanızı yayımlamak için öncelikle uygulamanızı yayımlayacağınız yeni bir App Service oluşturmanız ve yapılandırmanız gerekir.

App Service ayarlamanın bir parçası olarak şunları oluşturursunuz:

- Hizmet için tüm Azure kaynaklarını içerecek yeni bir [kaynak grubu](../azure-resource-manager/management/overview.md#terminology) .
- Uygulamanızı barındıran Web sunucusu grubunun konumunu, boyutunu ve özelliklerini belirten yeni bir [barındırma planı](overview-hosting-plans.md) .

App Service oluşturup Web uygulamanızı yayımlamak için aşağıdaki adımları izleyin:

:::zone target="docs" pivot="development-environment-vs"

1. **Çözüm Gezgini**, **Myfirstazurewebapp** projesine sağ tıklayın ve **Yayımla**' yı seçin.
1. **Yayımla**' da **Azure** ' u ve ardından **İleri**' yi seçin.

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio-Web uygulamasını yayımlayın ve Azure 'ı hedefleyin." border="true":::

1. Seçenekleriniz, Azure 'da oturum açmış olup olmadığınız ve bir Azure hesabına bağlı bir Visual Studio hesabınız olup olmadığına bağlıdır. Azure aboneliğinizde oturum açmak için **Hesap Ekle** veya **oturum aç** seçeneklerinden birini belirleyin. Zaten oturum açtıysanız istediğiniz hesabı seçin.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio-Azure 'da oturum aç iletişim kutusunu seçin.":::

1. Belirli bir **hedefi** **(Azure App Service (Linux)** veya **Azure App Service (Windows)** seçin.

    > [!IMPORTANT]
    > ASP.NET Framework 4,8 hedeflenirken **Azure App Service (Windows)** kullanacaksınız.

1. **App Service örneklerinin** sağında, öğesini seçin **+** .

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio-yeni App Service uygulaması iletişim kutusu.":::

1. **Abonelik** için, listelenen aboneliği kabul edin veya açılan listeden yeni bir tane seçin.
1. **Kaynak grubu** için **Yeni**' yi seçin. **Yeni kaynak grubu adı** alanına *Myresourcegroup* yazın ve **Tamam**' ı seçin.
1. **Barındırma planı** için **Yeni**' yi seçin.
1. **Barındırma planı: Yeni oluştur** iletişim kutusunda, aşağıdaki tabloda belirtilen değerleri girin:

    | Ayar          | Önerilen değer          | Açıklama                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Barındırma Planı** | *MyFirstAzureWebAppPlan* | App Service planının adı.                                         |
    | **Konum**     | *West Europe*            | Web uygulamasının barındırıldığı veri merkezi.                           |
    | **Boyut**         | *Ücretsiz*                   | [Fiyatlandırma katmanı][app-service-pricing-tier], barındırma özelliklerini belirler. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="Yeni barındırma planı oluştur":::

1. **Ad** alanına,,, ve yalnızca geçerli karakterleri içeren benzersiz bir uygulama adı girin `a-z` `A-Z` `0-9` `-` . Otomatik olarak oluşturulan benzersiz adı kabul edebilirsiniz. Web uygulamasının URL'si `http://<app-name>.azurewebsites.net` şeklindedir; burada `<app-name>`, uygulamanızın adıdır.
1. Azure kaynaklarını oluşturmak için **Oluştur** ' u seçin.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio-uygulama kaynakları oluştur iletişim kutusu.":::

   Sihirbaz tamamlandıktan sonra Azure kaynakları sizin için oluşturulur ve yayımlamaya hazırlarsınız.

1. Sihirbazı kapatmak için **son** ' u seçin.
1. **Yayımla** sayfasında **Yayımla**' yı seçin. Visual Studio uygulamayı oluşturur, paketler ve Azure 'da yayımlar ve ardından uygulamayı varsayılan tarayıcıda başlatır.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    ASP.NET Core 3,1 Web uygulamasının sayfada görüntülendiğini görürsünüz.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Azure 'da Visual Studio-ASP.NET Core 3,1 Web uygulaması.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    ASP.NET Core 5,0 Web uygulamasının sayfada görüntülendiğini görürsünüz.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Azure 'da Visual Studio-ASP.NET Core 5,0 Web uygulaması.":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Sayfada ASP.NET Framework 4,8 Web uygulamasının görüntülendiğini görürsünüz.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Azure 'da Visual Studio-ASP.NET Framework 4,8 Web uygulaması.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Visual Studio Azure Araçları uzantısını kullanarak Web uygulamanızı dağıtmak için:

1. Visual Studio Code ' de, <kbd>CTRL</kbd>SHIFT P [**komut paletini**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)açın + <kbd></kbd> + <kbd></kbd>.
1. Arama yapın ve "Azure App Service: Web uygulamasına dağıt" ı seçin.
1. İstemlere aşağıdaki gibi yanıt verin:

    - Dağıtılacak klasör olarak *Myfirstazurewebapp* öğesini seçin.
    - İstendiğinde **yapılandırma Ekle** ' yi seçin.
    - İstenirse, mevcut Azure hesabınızda oturum açın.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code Azure 'da oturum açın." border="true":::

    - **Aboneliğinizi** seçin.
    - **Yeni Web uygulaması oluştur ' u seçin... Gelişmiş**.
    - **Genel olarak benzersiz bir ad girin**, tüm Azure genelinde benzersiz bir ad kullanın (*geçerli karakterler `a-z` , `0-9` ve `-`*). İyi bir model, şirketinizin adının ve uygulama tanımlayıcısının bir birleşimini kullanmaktır.
    - **Yeni kaynak grubu oluştur** ' u seçin ve gibi bir ad sağlayın `myResourceGroup` .
    - **Çalışma zamanı yığını seçmeniz** istendiğinde:
      - *.Net core 3,1* Için **.NET Core 3,1 (LTS)** öğesini seçin
      - *.Net 5,0* için **.NET 5** ' i seçin
      - *.NET Framework 4,8* Için **ASP.net v 4.8** ' yi seçin.
    - Bir işletim sistemi (Windows veya Linux) seçin.
        - *.NET Framework 4,8* için Windows örtük olarak seçilecek.
    - **Yeni App Service planı oluştur**' u seçin, bir ad girin ve **F1 ücretsiz** [fiyatlandırma katmanını][app-service-pricing-tier]seçin.
    - Application Insights kaynağı için **Şimdi atla** ' yı seçin.
    - Size yakın bir konum seçin.

1. Yayımlama tamamlandığında, bildirimde **Web sitesine gözatıp** ' yi seçin ve istendiğinde **Aç** ' ı seçin.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    ASP.NET Core 3,1 Web uygulamasının sayfada görüntülendiğini görürsünüz.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Azure 'da Visual Studio Code-ASP.NET Core 3,1 Web uygulaması.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    ASP.NET Core 5,0 Web uygulamasının sayfada görüntülendiğini görürsünüz.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Azure 'da Visual Studio Code-ASP.NET Core 5,0 Web uygulaması.":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Sayfada ASP.NET Framework 4,8 Web uygulamasının görüntülendiğini görürsünüz.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Azure 'da Visual Studio Code-ASP.NET Framework 4,8 Web uygulaması.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Şu komutu kullanarak kodu yerel *Myfirstazurewebapp* dizininizde dağıtın [`az webapp up`](/cli/azure/webapp#az_webapp_up) :

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- `az`Komut tanınmazsa, [Önkoşullar](#prerequisites)bölümünde AÇıKLANDıĞı gibi Azure CLI 'nın yüklü olduğundan emin olun.
- `<app-name>`Tüm Azure genelinde benzersiz olan bir adla değiştirin (*geçerli karakterler `a-z` , `0-9` ve `-`*). İyi bir model, şirketinizin adının ve uygulama tanımlayıcısının bir birleşimini kullanmaktır.
- `--sku F1`Bağımsız değişkeni, **ücretsiz** [fiyatlandırma katmanında][app-service-pricing-tier]Web uygulaması oluşturur. Saatlik maliyet içeren daha hızlı bir Premium katmanı kullanmak için bu bağımsız değişkeni atlayın.
- `<os>`Ya da ile `linux` değiştirin `windows` . `windows` *ASP.NET Framework 4,8*' i hedeflerken kullanmanız gerekir.
- İsteğe bağlı olarak `--location <location-name>` `<location-name>` , kullanılabilir bir Azure bölgesi olan bağımsız değişkeni ekleyebilirsiniz. Komutunu çalıştırarak, Azure hesabınız için izin verilen bölgelerin bir listesini alabilirsiniz [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations) .

Komutun tamamlanması birkaç dakika sürebilir. Çalıştırılırken, kaynak grubu oluşturma, App Service planı ve barındırma uygulaması, günlüğe kaydetmeyi yapılandırma ve ardından ZIP dağıtımı gerçekleştirme hakkında iletiler sağlar. Daha sonra uygulamanın URL 'SI ile bir ileti verir:

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Bir Web tarayıcısı açın ve URL 'ye gidin:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3,1 Web uygulamasının sayfada görüntülendiğini görürsünüz.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Azure 'da CLı-ASP.NET Core 3,1 Web uygulaması.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5,0 Web uygulamasının sayfada görüntülendiğini görürsünüz.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Azure 'da CLı-ASP.NET Core 5,0 Web uygulaması.":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Sayfada ASP.NET Framework 4,8 Web uygulamasının görüntülendiğini görürsünüz.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="Azure 'da CLı-ASP.NET Framework 4,8 Web uygulaması.":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>Uygulamayı güncelleştirme ve yeniden dağıtma

Web uygulamanızı güncelleştirmek ve yeniden dağıtmak için aşağıdaki adımları izleyin:

:::zone target="docs" pivot="development-environment-vs"

1. **Çözüm Gezgini**, projeniz altında *Index. cshtml* dosyasını açın.
1. İlk `<div>` öğeyi aşağıdaki kodla değiştirin:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Yaptığınız değişiklikleri kaydedin.

1. Azure 'a yeniden dağıtmak için, **Çözüm Gezgini** ' de **Myfirstazurewebapp** projesine sağ tıklayın ve **Yayımla**' yı seçin.
1. **Yayımla** Özeti sayfasında **Yayımla**' yı seçin.

    Yayımlama tamamlandığında Visual Studio, web uygulamasının URL’si ile bir tarayıcı başlatır.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Güncelleştirilmiş ASP.NET Core 3,1 Web uygulamasının sayfada görüntülendiğini görürsünüz.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio-Azure 'da ASP.NET Core 3,1 Web uygulaması güncelleştirildi.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Güncelleştirilmiş ASP.NET Core 5,0 Web uygulamasının sayfada görüntülendiğini görürsünüz.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio-Azure 'da ASP.NET Core 5,0 Web uygulaması güncelleştirildi.":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Güncelleştirilmiş ASP.NET Framework 4,8 Web uygulamasının sayfada görüntülendiğini görürsünüz.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio-Azure 'da ASP.NET Framework 4,8 Web uygulaması güncelleştirildi.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. *Index.cshtml* sayfasını açın.
1. İlk `<div>` öğeyi aşağıdaki kodla değiştirin:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Yaptığınız değişiklikleri kaydedin.

1. Visual Studio Code **yan çubuğunu** açın, seçeneklerini genişletmek için **Azure** simgesini seçin.
1. **App SERVICE** düğümü altında, aboneliğinizi genişletin ve **Myfirstazurewebapp** öğesine sağ tıklayın.
1. **Web uygulamasına dağıt...** öğesini seçin.
1. İstendiğinde **Dağıt** ' ı seçin.
1. Yayımlama tamamlandığında, bildirimde **Web sitesine gözatıp** ' yi seçin ve istendiğinde **Aç** ' ı seçin.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Güncelleştirilmiş ASP.NET Core 3,1 Web uygulamasının sayfada görüntülendiğini görürsünüz.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code-Azure 'da ASP.NET Core 3,1 Web uygulaması güncelleştirildi.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Güncelleştirilmiş ASP.NET Core 5,0 Web uygulamasının sayfada görüntülendiğini görürsünüz.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code-Azure 'da ASP.NET Core 5,0 Web uygulaması güncelleştirildi.":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Güncelleştirilmiş ASP.NET Framework 4,8 Web uygulamasının sayfada görüntülendiğini görürsünüz.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code-Azure 'da ASP.NET Framework 4,8 Web uygulaması güncelleştirildi.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Yerel dizinde *Index. cshtml* dosyasını açın. İlk öğeyi Değiştir `<div>` :

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Değişikliklerinizi kaydettikten sonra `az webapp up` komutu tekrar kullanarak uygulamayı yeniden dağıtın:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3,1, önceki dağıtımınıza bağlı olarak, `<os>` veya ile değiştirin `linux` `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5,0, önceki dağıtımınıza bağlı olarak, `<os>` veya ile değiştirin `linux` `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4,8 çerçeve bağımlılıklarına sahiptir ve Windows üzerinde barındırılmalıdır.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> .NET uygulamalarınızı Linux üzerinde barındırmakla ilgileniyorsanız, [ASP.NET Framework 'ten ASP.NET Core](/aspnet/core/migration/proper-to-2x)geçiş yapmayı düşünün.

---

Bu komut, uygulama adı, kaynak grubu ve App Service planı dahil olmak üzere *. Azure/config* dosyasında yerel olarak önbelleğe alınan değerleri kullanır.

Dağıtım tamamlandıktan sonra **Uygulamaya göz at** adımında açılan tarayıcı penceresine dönüp yenile öğesine dokunun.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Güncelleştirilmiş ASP.NET Core 3,1 Web uygulamasının sayfada görüntülendiğini görürsünüz.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLı-Azure 'da ASP.NET Core 3,1 Web uygulaması güncelleştirildi.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Güncelleştirilmiş ASP.NET Core 5,0 Web uygulamasının sayfada görüntülendiğini görürsünüz.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLı-Azure 'da ASP.NET Core 5,0 Web uygulaması güncelleştirildi.":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Güncelleştirilmiş ASP.NET Framework 4,8 Web uygulamasının sayfada görüntülendiğini görürsünüz.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLı-Azure 'da ASP.NET Framework 4,8 Web uygulaması güncelleştirildi.":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>Azure uygulamasını yönetme

Web uygulamanızı yönetmek için [Azure Portal](https://portal.azure.com)gidin ve **uygulama hizmetleri**' ni arayıp seçin.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure portalı-uygulama hizmetleri seçeneğini belirleyin.":::

**Uygulama hizmetleri** sayfasında, Web uygulamanızın adını seçin.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure Portal-örnek bir Web uygulaması seçili olan App Services sayfası.":::

Web uygulamanıza ilişkin **genel bakış** sayfası, göz at, durdur, Başlat, yeniden Başlat ve Sil gibi temel yönetime yönelik seçenekler içerir. Sol menü, uygulamanızı yapılandırmak için daha fazla sayfa sağlar.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure Portal-App Service Genel Bakış sayfası.":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Azure App Service için bir ASP.NET Web uygulaması oluşturdunuz ve dağıttınız.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Bir .NET Core uygulaması oluşturmayı ve bir SQL veritabanına bağlamayı öğrenmek için bir sonraki makaleye ilerleyin:

> [!div class="nextstepaction"]
> [Öğretici: SQL veritabanı ile uygulama ASP.NET Core](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core 3,1 uygulamasını yapılandırma](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Bir .NET Core uygulaması oluşturmayı ve bir SQL veritabanına bağlamayı öğrenmek için bir sonraki makaleye ilerleyin:

> [!div class="nextstepaction"]
> [Öğretici: SQL veritabanı ile uygulama ASP.NET Core](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core 5,0 uygulamasını yapılandırma](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Bir .NET Framework uygulamasının nasıl oluşturulduğunu ve bir SQL veritabanına nasıl bağlanacağını öğrenmek için bir sonraki makaleye ilerleyin:

> [!div class="nextstepaction"]
> [Öğretici: SQL veritabanı ile ASP.NET uygulaması](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [ASP.NET Framework uygulamasını yapılandırma](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
