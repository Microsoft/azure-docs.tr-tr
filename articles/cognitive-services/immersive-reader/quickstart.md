---
title: 'Hızlı Başlangıç: İle modern okuyucuyu Başlatan bir Web uygulaması oluşturmaC#'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, sıfırdan bir Web uygulaması oluşturup tam ekran okuyucusu API işlevini eklersiniz.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: ed49f834cb7cd4f649d84aea9e549e212771eead
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899360"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Hızlı Başlangıç: Tam ekran okuyucu (C#) başlatan bir Web uygulaması oluşturma

[Tam ekran okuyucu](https://www.onenote.com/learningtools) , okuma kavramasını geliştirmek için kendini kanıtlamış teknikler uygulayan, ve dahil tasarlanmış bir araçtır.

Bu hızlı başlangıçta, sıfırdan bir Web uygulaması oluşturur ve modern Okuyucu SDK 'sını kullanarak tam ekran okuyucuyu tümleştirin. Bu hızlı başlangıç için tam bir çalışma örneğine [buradan](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)ulaşabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Azure Active Directory (Azure AD) kimlik doğrulaması için yapılandırılmış bir tam ekran okuyucu kaynağı. Kurulumunu yapmak için [Bu yönergeleri](./azure-active-directory-authentication.md) izleyin. Örnek proje özellikleri yapılandırılırken burada oluşturulan bazı değerler gerekir. Daha sonra başvurmak üzere oturumunuzun çıkışını bir metin dosyasına kaydedin.

## <a name="create-a-web-app-project"></a>Web uygulaması projesi oluşturma

Yerleşik Model-View-Controller ile ASP.NET Core Web uygulaması şablonunu kullanarak Visual Studio 'da yeni bir proje oluşturun.

![Yeni Proje](./media/vswebapp.png)

![Yeni ASP.NET Core Web uygulaması](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD kimlik doğrulaması belirteci alma

Bu bölüm için yukarıdaki Azure AD kimlik doğrulama yapılandırması önkoşul adımından bazı değerlere ihtiyacınız vardır. Bu oturumu kaydettiğiniz metin dosyasına geri bakın.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

_Çözüm Gezgini_ projeye sağ tıklayın ve **Kullanıcı gizli dizilerini Yönet**' i seçin. Bu, _gizli dizileri. JSON_adlı bir dosya açar. Yukarıdaki özel özellik değerlerinizi sağlayarak bu dosyanın içeriğini aşağıdaki kodla değiştirin.

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

_Controllers\homecontroller.cs_dosyasını açın ve dosyayı aşağıdaki kodla değiştirin.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
        private readonly string TenantId;     // Azure subscription TenantId
        private readonly string ClientId;     // Azure AD ApplicationId
        private readonly string ClientSecret; // Azure AD Application Service Principal password
        private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

        public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
        {
            TenantId = configuration["TenantId"];
            ClientId = configuration["ClientId"];
            ClientSecret = configuration["ClientSecret"];
            Subdomain = configuration["Subdomain"];

            if (string.IsNullOrWhiteSpace(TenantId))
            {
                throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientId))
            {
                throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientSecret))
            {
                throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(Subdomain))
            {
                throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
            }
        }

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
        }

        /// <summary>
        /// Get an Azure AD authentication token
        /// </summary>
        private async Task<string> GetTokenAsync()
        {
            string authority = $"https://login.windows.net/{TenantId}";
            const string resource = "https://cognitiveservices.azure.com/";

            AuthenticationContext authContext = new AuthenticationContext(authority);
            ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

            AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

            return authResult.AccessToken;
        }
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Microsoft. IdentityModel. clients. ActiveDirectory NuGet paketini ekleyin

Yukarıdaki kod, **Microsoft. IdentityModel. clients. ActiveDirectory** NuGet paketindeki nesneleri kullanır, böylece projenizde bu pakete bir başvuru eklemeniz gerekir.

**Araçlar-> NuGet Paket Yöneticisi-> paket yöneticisi konsolundan** NuGet Paket Yöneticisi konsolunu açın ve aşağıdakileri yazın:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

## <a name="add-sample-content"></a>Örnek içerik Ekle

Şimdi bu Web uygulamasına örnek içerik ekleyeceğiz. _Views\home\ındex.cshtml_ dosyasını açın ve otomatik olarak oluşturulan kodu şu örnekle değiştirin:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
    <script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
    <script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
    }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Uygulamayı derleme ve çalıştırma

Menü çubuğundan **hata ayıkla > hata ayıklamayı Başlat**' ı seçin veya **F5** ' e basarak uygulamayı başlatın.

Tarayıcınızda şunları görmeniz gerekir:

![Örnek uygulama](./media/quickstart-result.png)

"Modern okuyucu" düğmesine tıkladığınızda, sayfadaki içerikle birlikte modern okuyucu başlatılır.

![Tam Ekran Okuyucu](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Sonraki adımlar

* Node. js kullanarak modern Okuyucu SDK 'Sı ile neler yapabileceğinizi öğrenmek için [Node. js öğreticisini](./tutorial-nodejs.md) görüntüleyin
* Python kullanarak modern Okuyucu SDK 'Sı ile neler yapabileceğinizi öğrenmek için [Python öğreticisini](./tutorial-python.md) görüntüleyin
* Swift kullanarak tam ekran okuyucu SDK 'Sı ile neler yapabileceğinizi öğrenmek için [iOS öğreticisini](./tutorial-ios-picture-immersive-reader.md) görüntüleyin
* [Modern Okuyucu SDK 'sını](https://github.com/microsoft/immersive-reader-sdk) ve [tam ekran okuyucu SDK başvurusunu](./reference.md) keşfet
