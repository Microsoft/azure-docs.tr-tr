---
title: "C kullanarak Azure 'da ilk dayanıklı işlevinizi oluşturma #"
description: Visual Studio veya Visual Studio Code kullanarak bir Azure dayanıklı Işlevi oluşturun ve yayımlayın.
author: jeffhollan
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: azfuncdf
zone_pivot_groups: code-editors-set-one
ms.openlocfilehash: 36566baac2885c449e1eae762924357bf571b39f
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490225"
---
# <a name="create-your-first-durable-function-in-c"></a>C 'de ilk dayanıklı işlevinizi oluşturma\#

*Dayanıklı işlevler* , bir sunucusuz ortamda durum bilgisi olan işlevler yazmanıza olanak sağlayan bir [Azure işlevleri](../functions-overview.md) uzantısıdır. Uzantı sizin için durumu, denetim noktalarını ve yeniden başlatmaları yönetir.

::: zone pivot="code-editor-vscode"

Bu makalede, bir "Hello World" dayanıklı işlevini yerel olarak oluşturmak ve test etmek için Visual Studio Code kullanmayı öğreneceksiniz.  Bu işlev, diğer işlevlere birlikte yapılan çağrıları düzenler ve zincirler. Ardından işlev kodunu Azure’da yayımlayacaksınız. Bu araçlar VS Code [Azure işlevleri uzantısının](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)bir parçası olarak kullanılabilir.

![Ekran görüntüsü, dayanıklı bir işlevle Visual Studio Code penceresi gösterir.](./media/durable-functions-create-first-csharp/functions-vscode-complete.png)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

* [Visual Studio Code](https://code.visualstudio.com/download)'i yükler.

* Aşağıdaki VS Code uzantılarını yükler:
    - [Azure İşlevleri](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
    - [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

* [Azure Functions Core Tools](../functions-run-local.md)en son sürümüne sahip olduğunuzdan emin olun.

* Dayanıklı İşlevler bir Azure depolama hesabı gerektirir. Bir Azure aboneliğine ihtiyacınız vardır.

* Sürüm 3,1 veya [.NET Core SDK](https://dotnet.microsoft.com/download) yüklü olduğundan emin olun.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Yerel projenizi oluşturma 

Bu bölümde, yerel bir Azure Işlevleri projesi oluşturmak için Visual Studio Code kullanırsınız. 

1. Visual Studio Code, komut paletini açmak için F1 (veya Ctrl/Cmd + SHIFT + P) tuşuna basın. Komut paletinde, araması yapın ve seçin `Azure Functions: Create New Project...` .

    ![İşlev projesi oluşturma](media/durable-functions-create-first-csharp/functions-vscode-create-project.png)

1. Projeniz için boş bir klasör konumu seçin ve **Seç**' i seçin.

1. İstemlerin ardından aşağıdaki bilgileri sağlayın:

    | İstem | Değer | Açıklama |
    | ------ | ----- | ----------- |
    | İşlev uygulaması projeniz için bir dil seçin | C# | Yerel bir C# işlevleri projesi oluşturun. |
    | Sürüm seçin | Azure İşlevleri v3 | Bu seçeneği yalnızca temel araçlar yüklü değilse görürsünüz. Bu durumda, uygulamayı ilk kez çalıştırdığınızda çekirdek araçlar yüklenir. |
    | Projenizin ilk işlevi için bir şablon seçin | Şimdilik atla | |
    | Projenizi nasıl açmak istediğinizi seçin | Geçerli pencerede aç | VS Code seçtiğiniz klasörde yeniden açılır. |

Gerekirse Azure Functions Core Tools Visual Studio Code yüklenir. Ayrıca, bir klasörde işlev uygulaması projesi oluşturur. Bu proje yapılandırma dosyalarında [host.js](../functions-host-json.md) ve [local.settings.js](../functions-run-local.md#local-settings-file) içerir.

## <a name="add-functions-to-the-app"></a>Uygulamaya işlevler ekleyin

Aşağıdaki adımlar, projenizde dayanıklı işlev kodu oluşturmak için bir şablon kullanır.

1. Komut paletinde, araması yapın ve seçin `Azure Functions: Create Function...` .

1. İstemlerin ardından aşağıdaki bilgileri sağlayın:

    | İstem | Değer | Açıklama |
    | ------ | ----- | ----------- |
    | İşleviniz için bir şablon seçin | DurableFunctionsOrchestration | Dayanıklı İşlevler düzenleme oluşturma |
    | Bir işlev adı girin | Merhaba düzenleme | İşlevlerin oluşturulduğu sınıfın adı |
    | Ad alanı belirtin | Company. Function | Oluşturulan sınıfın ad alanı |

1. VS Code bir depolama hesabı seçmenizi isterse **depolama hesabı seç**' i seçin. İstemlerin ardından, Azure 'da yeni bir depolama hesabı oluşturmak için aşağıdaki bilgileri sağlayın.

    | İstem | Değer | Açıklama |
    | ------ | ----- | ----------- |
    | Abonelik seçme | *aboneliğinizin adı* | Azure aboneliğinizi seçin |
    | Bir depolama hesabı seçin | Yeni depolama hesabı oluşturma |  |
    | Yeni depolama hesabının adını girin | *benzersiz ad* | Oluşturulacak depolama hesabının adı |
    | Kaynak grubu seçme | *benzersiz ad* | Oluşturulacak kaynak grubunun adı |
    | Bir konum seçin | *geli* | Size yakın bir bölge seçin |

Projeye yeni işlevleri içeren bir sınıf eklenir. VS Code Ayrıca depolama hesabı bağlantı dizesini *local.settings.jsüzerine* ve [`Microsoft.Azure.WebJobs.Extensions.DurableTask`](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) *. csproj* proje dosyasına bir NuGet paketine bir başvuruya ekler.

İçeriğini görüntülemek için yeni *Helloorchestration. cs* dosyasını açın. Bu dayanıklı işlev, aşağıdaki yöntemlerle basit bir işlev zincirleme örneğidir:  

| Yöntem | Ifadelerini | Açıklama |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `HelloOrchestration` | Dayanıklı düzenleme 'yi yönetir. Bu durumda, düzenleme başlar, bir liste oluşturur ve listeye yapılan üç işlev çağrısının sonucunu ekler.  Üç işlev çağrısı tamamlandığında, listeyi döndürür. |
| **`SayHello`** | `HelloOrchestration_Hello` | İşlev bir merhaba döndürür. Düzenleyen iş mantığını içeren işlevdir. |
| **`HttpStart`** | `HelloOrchestration_HttpStart` | Orchestration örneğini başlatan ve bir denetim durumu yanıtı döndüren [http ile tetiklenen bir işlev](../functions-bindings-http-webhook.md) . |

Artık işlev projenizi ve dayanıklı bir işlevi oluşturduğunuza göre, bunu yerel bilgisayarınızda test edebilirsiniz.

## <a name="test-the-function-locally"></a>İşlevi yerel olarak test etme

Azure İşlevleri Temel Araçları, Azure İşlevleri projenizi yerel geliştirme bilgisayarınızda çalıştırmanıza olanak sağlar. Visual Studio Code'da ilk kez bir işlev başlattığınızda bu araçları yüklemeniz istenir.

1. İşlevinizi test etmek için etkinlik işlev kodunda bir kesme noktası ayarlayın `SayHello` ve F5 'e basarak işlev uygulaması projesini başlatın. Temel Araçlar’daki çıktı, **Terminal** panelinde görüntülenir.

    > [!NOTE]
    > Hata ayıklama hakkında daha fazla bilgi için [dayanıklı işlevler tanılamayı](durable-functions-diagnostics.md#debugging) inceleyin.

1. **Terminal** panelinde, HTTP ile tetiklenen işlevinizin URL uç noktasını kopyalayın.

    ![Azure yerel çıktısı](media/durable-functions-create-first-csharp/functions-vscode-f5.png)

1. [Postman](https://www.getpostman.com/) veya [kıvrık](https://curl.haxx.se/)gibi BIR araç kullanarak, URL uç noktasına bir http post isteği gönderin.

   Yanıt, HTTP işlevinin ilk sonucudur ve dayanıklı düzenleme 'nin başarıyla başlatıldığını öğrenmemize olanak tanıyor. Orchestration 'un nihai sonucu henüz değildir. Yanıt birkaç yararlı URL içerir. Şimdilik düzenleme durumunu sorgulayalım.

1. URL değerini kopyalayın `statusQueryGetUri` ve tarayıcının adres çubuğuna yapıştırın ve isteği yürütün. Alternatif olarak, GET isteğini vermek için Postman kullanmaya de devam edebilirsiniz.

   İstek, bu durum için düzenleme örneğini sorgular. Örneğin, örneğin tamamlandığını gösteren ve dayanıklı işlevin çıkışlarını veya sonuçlarını içeren nihai bir yanıt almanız gerekir. Şöyle görünür: 

    ```json
    {
        "name": "HelloOrchestration",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Hata ayıklamayı durdurmak için VS Code **Shift + F5** tuşlarına basın.

İşlevin yerel bilgisayarınızda düzgün çalıştığını doğruladıktan sonra, projeyi Azure'da yayımlamanın zamanı gelmiştir.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Azure'da işlevinizi test etme

1. **Çıktı** panelinden HTTP tetikleyicisinin URL’sini kopyalayın. HTTP tarafından tetiklenen işlevinizi çağıran URL aşağıdaki biçimde olmalıdır:

    `https://<functionappname>.azurewebsites.net/api/HelloOrchestration_HttpStart`

1. HTTP isteğinin yeni URL’sini tarayıcınızın adres çubuğuna yapıştırın. Yayımlanan uygulamayı kullanmadan önce aynı durum yanıtını almalısınız.

## <a name="next-steps"></a>Sonraki adımlar

Bir C# dayanıklı işlev uygulaması oluşturmak ve yayımlamak için Visual Studio Code kullandınız.

> [!div class="nextstepaction"]
> [Ortak dayanıklı işlev desenleri hakkında bilgi edinin](durable-functions-overview.md#application-patterns)

::: zone-end

::: zone pivot="code-editor-visualstudio"

Bu makalede, "Hello World" dayanıklı işlevini yerel olarak oluşturmak ve test etmek için Visual Studio 2019 ' i nasıl kullanacağınızı öğreneceksiniz.  Bu işlev, diğer işlevlere birlikte yapılan çağrıları düzenler ve zincirler. Ardından işlev kodunu Azure’da yayımlayacaksınız. Bu araçlar, Visual Studio 2019 ' de Azure geliştirme iş yükünün parçası olarak kullanılabilir.

![Ekran görüntüsü, dayanıklı bir işlev içeren bir Visual Studio 2019 penceresini gösterir.](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)’u yükleyin. **Azure geliştirme** iş yükünün de yüklü olduğundan emin olun. Visual Studio 2017 Ayrıca Dayanıklı İşlevler geliştirmeyi destekler, ancak UI ve adımlar farklılık gösterir.

* [Azure Storage öykünücüsü](../../storage/common/storage-use-emulator.md) 'nin yüklendiğini ve çalıştığını doğrulayın.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>İşlev uygulaması projesi oluşturma

Azure Işlevleri şablonu, Azure 'da bir işlev uygulamasına yayımlanmakta olabilecek bir proje oluşturur. İşlev uygulaması, kaynakların daha kolay yönetilmesi, dağıtılması, ölçeklendirilmesi ve paylaşılması için işlevleri bir mantıksal birim olarak gruplandırmanıza olanak tanır.

1. Visual Studio'da **Dosya** menüsünden **Yeni** > **Proje**’yi seçin.

1. **Yeni proje oluştur** iletişim kutusunda, arama `functions` yapın, **Azure Işlevleri** şablonunu seçin ve **İleri**' yi seçin. 

    ![Visual Studio'da işlev oluşturmaya yönelik yeni proje iletişim kutusu](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Projeniz için bir **Proje adı** yazın ve **Tamam**' ı seçin. Proje adı C# ad alanı olarak geçerli olmalıdır, bu nedenle alt çizgi, kısa çizgi veya alfasayısal olmayan karakterler kullanmayın.

1. **Yeni bir Azure Işlevleri uygulaması oluştur** bölümünde, görüntüyü izleyen tabloda belirtilen ayarları kullanın.

    ![Visual Studio 'da yeni bir Azure Işlevleri uygulama iletişim kutusu oluşturma](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Ayar      | Önerilen değer  | Açıklama                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Sürüm** | Azure Işlevleri 3,0 <br />(.NET Core) | .NET Core 3,1 ' yi destekleyen Azure Işlevlerinin sürüm 3,0 çalışma zamanını kullanan bir işlev projesi oluşturur. Daha fazla bilgi için bkz. [Azure İşlevleri çalışma zamanı sürümünün hedefini belirleme](../functions-versions.md).   |
    | **Şablon** | Olmamalıdır | Boş bir işlev uygulaması oluşturur. |
    | **Depolama hesabı**  | Depolama Öykünücüsü | Dayanıklı işlev durumu yönetimi için bir depolama hesabı gereklidir. |

4. Boş bir işlev projesi oluşturmak için **Oluştur** ' u seçin. Bu proje, işlevlerinizi çalıştırmak için gereken temel yapılandırma dosyalarını içerir.

## <a name="add-functions-to-the-app"></a>Uygulamaya işlevler ekleyin

Aşağıdaki adımlar, projenizde dayanıklı işlev kodu oluşturmak için bir şablon kullanır.

1. Visual Studio 'da projeye sağ tıklayın ve   >  **yeni Azure işlevi** Ekle ' yi seçin.

    ![Yeni işlev Ekle](./media/durable-functions-create-first-csharp/functions-vs-add-function.png)

1. Ekle menüsünde **Azure işlevinin** seçili olduğunu doğrulayın, C# dosyanız için bir ad yazın ve ardından **Ekle**' yi seçin.

1. **Dayanıklı işlevler Orchestration** şablonunu seçin ve ardından **Tamam** ' ı seçin.

    ![Dayanıklı şablon seçin](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)

Uygulamaya yeni bir dayanıklı işlev eklenir.  İçeriğini görüntülemek için yeni. cs dosyasını açın. Bu dayanıklı işlev, aşağıdaki yöntemlerle basit bir işlev zincirleme örneğidir:  

| Yöntem | Ifadelerini | Açıklama |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Dayanıklı düzenleme 'yi yönetir. Bu durumda, düzenleme başlar, bir liste oluşturur ve listeye yapılan üç işlev çağrısının sonucunu ekler.  Üç işlev çağrısı tamamlandığında, listeyi döndürür. |
| **`SayHello`** | `<file-name>_Hello` | İşlev bir merhaba döndürür. Düzenleyen iş mantığını içeren işlevdir. |
| **`HttpStart`** | `<file-name>_HttpStart` | Orchestration örneğini başlatan ve bir denetim durumu yanıtı döndüren [http ile tetiklenen bir işlev](../functions-bindings-http-webhook.md) . |

Artık işlev projenizi ve dayanıklı bir işlevi oluşturduğunuza göre, bunu yerel bilgisayarınızda test edebilirsiniz.

## <a name="test-the-function-locally"></a>İşlevi yerel olarak test etme

Azure İşlevleri Temel Araçları, Azure İşlevleri projenizi yerel geliştirme bilgisayarınızda çalıştırmanıza olanak sağlar. Visual Studio'da ilk kez bir işlev başlattığınızda bu araçları yüklemeniz istenir.

1. İşlevinizi test etmek için F5’e basın. İstenirse Visual Studio'dan gelen Azure İşlevleri Temel (CLI) araçlarını indirme ve yükleme isteğini kabul edin. Aracın HTTP isteklerini işleyebilmesi için bir güvenlik duvarı özel durumu etkinleştirmeniz de gerekebilir.

2. Azure İşlevleri çalışma zamanı çıktısından işlevinizin URL'sini kopyalayın.

    ![Azure yerel çalışma zamanı](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. HTTP isteğinin URL 'sini tarayıcınızın adres çubuğuna yapıştırın ve isteği yürütün. İşlevin döndürdüğü yerel GET isteğine tarayıcıda verilen yanıt aşağıda gösterilmiştir:

    ![Ekran görüntüsünde, statusQueryGetUri adlı bir tarayıcı penceresi gösterilmektedir.](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    Yanıt, HTTP işlevinin ilk sonucudur ve dayanıklı düzenleme 'nin başarıyla başlatıldığını öğrenmemize olanak tanıyor.  Orchestration 'un nihai sonucu henüz değildir.  Yanıt birkaç yararlı URL içerir.  Şimdilik düzenleme durumunu sorgulayalım.

4. URL değerini kopyalayın `statusQueryGetUri` ve tarayıcının adres çubuğuna yapıştırın ve isteği yürütün.

    İstek, bu durum için düzenleme örneğini sorgular. Aşağıdakine benzer bir nihai yanıt almanız gerekir.  Bu çıktı, örneğin tamamlandığını gösterir ve dayanıklı işlevin çıkışlarını veya sonuçlarını içerir.

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2019-11-02T07:07:40Z",
        "lastUpdatedTime": "2019-11-02T07:07:52Z"
    }
    ```

5. Hata ayıklamayı durdurmak için **Shift + F5** tuşuna basın.

İşlevin yerel bilgisayarınızda düzgün çalıştığını doğruladıktan sonra, projeyi Azure'da yayımlamanın zamanı gelmiştir.

## <a name="publish-the-project-to-azure"></a>Projeyi Azure'da yayımlama

Projenizi yayımlayabilmeniz için önce Azure aboneliğinizde bir işlev uygulamanızın olması gerekir. Visual Studio'dan bir işlev uygulaması oluşturabilirsiniz.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Azure'da işlevinizi test etme

1. Yayımlama profili sayfasından işlev uygulamasının temel URL'sini kopyalayın. İşlevi yerel olarak test ederken kullandığınız URL’nin `localhost:port` kısmını, yeni temel URL ile değiştirin.

    Dayanıklı işlev HTTP tetikleyicinizi çağıran URL aşağıdaki biçimde olmalıdır:

    `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart`

2. HTTP isteğinin yeni URL’sini tarayıcınızın adres çubuğuna yapıştırın. Yayımlanan uygulamayı kullanmadan önce aynı durum yanıtını almalısınız.

## <a name="next-steps"></a>Sonraki adımlar

C# dayanıklı bir işlev uygulaması oluşturmak ve yayımlamak için Visual Studio 'Yu kullandınız.

> [!div class="nextstepaction"]
> [Ortak dayanıklı işlev desenleri hakkında bilgi edinin](durable-functions-overview.md#application-patterns)

::: zone-end
