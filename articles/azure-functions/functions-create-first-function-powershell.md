---
title: Azure 'da ilk PowerShell işlevinizi oluşturma
description: Visual Studio Code kullanarak Azure 'da ilk PowerShell işlevinizi oluşturmayı öğrenin.
author: joeyaiello
manager: gwallace
ms.author: jaiello
ms.reviewer: glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.openlocfilehash: 1d6d641e141862b12fed40b800589aad70af2789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469416"
---
# <a name="create-your-first-powershell-function-in-azure"></a>Azure 'da ilk PowerShell işlevinizi oluşturma

Bu hızlı başlangıç makalesinde Visual Studio Code kullanarak ilk [sunucusuz](https://azure.com/serverless) PowerShell işlevinizi nasıl oluşturacağınız anlatılmaktadır.

![Visual Studio Code projesindeki Azure Işlevleri kodu](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

Yerel olarak bir PowerShell işlevi oluşturmak ve ardından bunu Azure 'da yeni bir işlev uygulamasına dağıtmak için [Visual Studio Code için Azure İşlevleri uzantısı] kullanırsınız. Uzantı şu an önizleme aşamasındadır. Daha fazla bilgi edinmek için [Visual Studio Code için Azure İşlevleri uzantısı] sayfasına bakın.

> [!NOTE]  
> Visual Studio Code için Azure işlevleri [Uzantısı][visual studio code için azure işlevleri uzantısı] için PowerShell desteği şu anda varsayılan olarak devre dışıdır. PowerShell desteğinin etkinleştirilmesi, bu makaledeki adımlardan biridir.

Aşağıdaki adımlar macOS, Windows ve Linux tabanlı işletim sistemlerinde desteklenir.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için:

* [PowerShell çekirdeğini](/powershell/scripting/install/installing-powershell-core-on-windows) yükler

* [Desteklenen platformlardan](https://code.visualstudio.com/) birinde [Visual Studio Code](https://code.visualstudio.com/docs/supporting/requirements#_platforms)’u yükleyin. 

* [Visual Studio Code Için PowerShell uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)yükler.

* [.NET Core SDK 2.2 +](https://www.microsoft.com/net/download) (Azure Functions Core Tools için gereklidir ve desteklenen tüm platformlarda kullanılabilir).

* [Azure Functions Core Tools](functions-run-local.md#v2)sürüm 2. x ' i yükler.

* Ayrıca etkin bir Azure aboneliğine de ihtiyacınız vardır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>İşlev uygulaması projesi oluşturma

Visual Studio Code'daki Azure İşlevleri proje şablonu, Azure'daki bir işlev uygulamasında yayımlanabilen bir proje oluşturur. İşlev uygulaması, kaynakların yönetilmesi, dağıtılması ve paylaşılması için işlevleri bir mantıksal birim olarak gruplandırmanıza olanak tanır. 

1. Visual Studio Code’da **Azure: İşlevler** alanını görüntülemek için Azure logosunu seçin ve Yeni Proje Oluştur simgesini seçin.

    ![İşlev uygulaması projesi oluşturma](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Işlevleriniz proje çalışma alanınız için bir konum seçin ve **Seç**' i seçin.

    > [!NOTE]
    > Bu makale, bir çalışma alanının dışında tamamlanacak şekilde tasarlanmıştır. Bu örnekte, bir çalışma alanının parçası olan bir proje klasörünü seçmeyin.

1. İşlev uygulaması projeniz için dil olarak **PowerShell** 'i ve ardından **Azure işlevleri v2**'yi seçin.

1. İlk işleviniz için şablon olarak **http tetikleyicisi** ' ni seçin **, işlev adı**olarak `HTTPTrigger` kullanın ve bir Yetkilendirme düzeyi seçin.

    > [!NOTE]
    > İşlev **Yetkilendirme düzeyi** , Azure 'da işlev uç noktası çağrılırken bir [işlev anahtarı](functions-bindings-http-webhook.md#authorization-keys) değeri gerektirir. Bu, yalnızca birisinin işlevinizi çağırazorlamanızı sağlar.

1. İstendiğinde **Çalışma alanına ekle**’yi seçin.

Visual Studio Code, yeni bir çalışma alanında PowerShell işlevi uygulama projesi oluşturur. Bu proje, projedeki tüm işleve uygulanan [Host. JSON](functions-host-json.md) ve [Local. Settings. JSON](functions-run-local.md#local-settings-file) yapılandırma dosyalarını içerir. Bu [PowerShell projesi](functions-reference-powershell.md#folder-structure) , Azure 'da çalışan bir işlev uygulamasıyla aynıdır.

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

Azure Functions Core Tools, bir Azure Işlevleri projesinde yerel olarak çalıştırmanıza ve hata ayıklamanıza olanak sağlamak için Visual Studio Code tümleştirilir.  

1. İşlevinizde hata ayıklamak için, hata ayıklayıcıyı iliştirmek istediğiniz işlev kodundaki [`Wait-Debugger`] cmdlet 'ine bir çağrı ekleyin ve ardından F5 tuşuna basarak işlev uygulaması projesini başlatın ve hata ayıklayıcıyı ekleyin. Temel Araçlar’daki çıktı, **Terminal** panelinde görüntülenir.

1. **Terminal** panelinde, HTTP ile tetiklenen işlevinizin URL uç noktasını kopyalayın.

    ![Azure yerel çıktısı](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. `?name=<yourname>` sorgu dizesini bu URL 'ye ekleyin ve ardından `Invoke-RestMethod` kullanarak isteği yürütün:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    GET isteğini bir tarayıcıdan da yürütebilirsiniz.

    Sorgu parametresi veya gövdede bir `name` parametresi geçirmeden HttpTrigger uç noktasını çağırdığınızda, işlev bir [HttpStatusCode]:: BadRequest hatası döndürür. Run. ps1 içinde kodu gözden geçirdikten sonra, bu hatanın tasarım tarafından oluştuğunu görürsünüz.

1. Hata ayıklamayı durdurmak için Shift + F5 tuşuna basın.

İşlevin yerel bilgisayarınızda düzgün çalıştığını doğruladıktan sonra, projeyi Azure'da yayımlamanın zamanı gelmiştir.

> [!NOTE]
> İşlevlerinizi Azure 'da yayımlamadan önce `Wait-Debugger` yapılan çağrıları kaldırmayı unutmayın. 
>
> Azure 'da bir işlev uygulaması oluşturmak yalnızca işlev uygulamanızın adını ister. Diğer değerler sizin için tanımlanır.
> `azureFunctions.advancedCreation` diğer tüm değerler için sorulması için `true` olarak ayarlayın.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>İşlevi Azure 'da çalıştırma

Yayınlanan işlevinizin Azure 'da çalıştığını doğrulamak için aşağıdaki PowerShell komutunu yürütün ve `Uri` parametresini önceki adımdaki HTTPTrigger işlevinin URL 'siyle değiştirin. Daha önce olduğu gibi, aşağıdaki örnekte olduğu gibi sorgu dizesini URL 'ye `&name=<yourname>` ekleyin:

```powershell
PS > Invoke-WebRequest -Method Get -Uri "https://glengatest-vscode-powershell.azurewebsites.net/api/HttpTrigger?code=nrY05eZutfPqLo0som...&name=PowerShell"

StatusCode        : 200
StatusDescription : OK
Content           : Hello PowerShell
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 16
                    Content-Type: text/plain; charset=utf-8
                    Date: Thu, 25 Apr 2019 16:01:22 GMT

                    Hello PowerShell
Forms             : {}
Headers           : {[Content-Length, 16], [Content-Type, text/plain; charset=utf-8], [Date, Thu, 25 Apr 2019 16:01:22 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 16
```

## <a name="next-steps"></a>Sonraki adımlar

Basit HTTP ile tetiklenen bir işlevle PowerShell işlevi uygulaması oluşturmak için Visual Studio Code kullandınız. Ayrıca, Azure Functions Core Tools kullanarak [bir PowerShell işlevindeki yerel olarak hata ayıklama](functions-debug-powershell-local.md) hakkında daha fazla bilgi edinmek isteyebilirsiniz. [Azure Işlevleri PowerShell Geliştirici Kılavuzu ' na](functions-reference-powershell.md)göz atın.

> [!div class="nextstepaction"]
> [Application Insights tümleştirmesini etkinleştirme](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Visual Studio Code için Azure İşlevleri uzantısı]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[' Wait-Debugger ']: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6
