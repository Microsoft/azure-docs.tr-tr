---
title: Komut satırından C# işlevi oluşturma-Azure Işlevleri
description: Komut satırından C# işlevi oluşturmayı ve ardından yerel projeyi Azure Işlevleri 'nde sunucusuz barındırma için yayımlamayı öğrenin.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-csharp-ieux
ms.openlocfilehash: c2585fcef3133c1f07832b13fcae9973cdd8090a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866224"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Hızlı başlangıç: Azure 'da komut satırından C# işlevi oluşturma

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Bu makalede, HTTP isteklerine yanıt veren C# sınıf kitaplığı tabanlı bir işlev oluşturmak için komut satırı araçlarını kullanırsınız. Kodu yerel olarak test ettikten sonra, Azure Işlevlerinin sunucusuz ortamına dağıtırsınız.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

Bu makalenin [Visual Studio Code tabanlı bir sürümü](create-first-function-vs-code-csharp.md) de vardır.

## <a name="configure-your-local-environment"></a>Yerel ortamınızı yapılandırma

Başlamadan önce aşağıdakilere sahip olmanız gerekir:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [.NET Core 3,1 SDK 'sı](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) sürüm 3. x.

+ Azure kaynakları oluşturmak için aşağıdaki araçlardan biri:

    + [Azure CLI](/cli/azure/install-azure-cli) sürüm 2,4 veya üzeri.

    + Sürüm 5,0 veya üzeri [Azure PowerShell](/powershell/azure/install-az-ps) .

### <a name="prerequisite-check"></a>Önkoşul denetimi

Azure CLı veya Azure kaynakları oluşturmak için Azure PowerShell kullanıp kullanmayacağınızı temel alan önkoşulları doğrulayın:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ Bir Terminal veya komut penceresinde, `func --version` Azure Functions Core Tools sürüm 3. x olduğunu denetlemek için komutunu çalıştırın.

+ `az --version`Azure CLI sürümünün 2,4 veya üzeri olduğunu denetlemek için ' i çalıştırın.

+ `az login`Azure 'da oturum açmak ve etkin bir aboneliği doğrulamak için ' i çalıştırın.

+ `dotnet --list-sdks`.NET Core SDK 3.1. x sürümünün yüklü olduğunu denetlemek için çalıştırın

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Bir Terminal veya komut penceresinde, `func --version` Azure Functions Core Tools sürüm 3. x olduğunu denetlemek için komutunu çalıştırın.

+ `(Get-Module -ListAvailable Az).Version`Sürüm 5,0 veya üstünü çalıştırın ve doğrulayın. 

+ `Connect-AzAccount`Azure 'da oturum açmak ve etkin bir aboneliği doğrulamak için ' i çalıştırın.

+ `dotnet --list-sdks`.NET Core SDK 3.1. x sürümünün yüklü olduğunu denetlemek için çalıştırın

---

## <a name="create-a-local-function-project"></a>Yerel işlev projesi oluşturma

Azure Işlevlerinde bir işlev projesi, her birinin belirli bir tetikleyiciye yanıt verdiği bir veya daha fazla bağımsız işlev için bir kapsayıcıdır. Projedeki tüm işlevler aynı yerel ve barındırma yapılandırmalarına sahiptir. Bu bölümde, tek bir işlev içeren bir işlev projesi oluşturursunuz.

1. `func init`Belirtilen çalışma zamanına sahip *Localfunctionproj* adlı klasörde bir işlevler projesi oluşturmak için komutu aşağıdaki gibi çalıştırın:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. Proje klasörüne gidin:

    ```console
    cd LocalFunctionProj
    ```

    Bu klasör, [local.settings.js](functions-run-local.md#local-settings-file) ve [host.js](functions-host-json.md)adlı yapılandırma dosyaları da dahil olmak üzere, proje için çeşitli dosyalar içerir. *local.settings.json* , Azure 'dan indirilen gizli dizileri içerebildiğinden, dosya, *. gitignore* dosyasında varsayılan olarak kaynak denetiminden çıkarılır.

1. Aşağıdaki komutu kullanarak, `--name` bağımsız değişkenin işlevinizin (HttpExample) benzersiz adı olduğu ve `--template` bağımsız değişkeninin işlevin TETIKLEYICISINI (http) belirttiği, projenize bir işlev ekleyin.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` HttpExample. cs kod dosyası oluşturur.

### <a name="optional-examine-the-file-contents"></a>Seçim Dosya içeriğini inceleyin

İsterseniz, [işlevi yerel olarak çalıştırmayı](#run-the-function-locally) atlayıp dosya içeriğini daha sonra incelemenizi sağlayabilirsiniz.

#### <a name="httpexamplecs"></a>HttpExample. cs

*Httpexample. cs* , `Run` değişkende istek verilerini alan bir yöntem içerir `req` , bu da tetikleme davranışını tanımlayan **Httptriggerattribute** ile donatılmış bir [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) 'dir.

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Dönüş nesnesi, bir [Okobjectresult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) veya [Badrequestobjectresult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400) olarak bir yanıt Iletisi döndüren bir [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) . Daha fazla bilgi için bkz. [Azure IŞLEVLERI http Tetikleyicileri ve bağlamaları](./functions-bindings-http-webhook.md?tabs=csharp).

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Azure 'da işlev uygulaması oluşturma:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    [Az functionapp Create](/cli/azure/functionapp#az_functionapp_create) komutu, Azure 'da işlev uygulaması oluşturur. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cmdlet 'i Azure 'da işlev uygulaması oluşturur. 
    
    ---
    
    Önceki örnekte, değerini, `<STORAGE_NAME>` önceki adımda kullandığınız hesabın adıyla değiştirin ve öğesini `<APP_NAME>` sizin için uygun olan bir genel benzersiz adla değiştirin. `<APP_NAME>` aynı zamanda işlev uygulamasının varsayılan DNS etki alanıdır. 
    
    Bu komut, burada yaptığınız kullanım miktarı için ücretsiz olan [Azure Işlevleri tüketim planı](consumption-plan.md)altında, belirtilen dil çalışma zamanında çalışan bir işlev uygulaması oluşturur. Bu komut Ayrıca, aynı kaynak grubunda, işlev uygulamanızı izleyebilmeniz ve günlükleri görüntüleyebileceğiniz ilişkili bir Azure Application Insights örneğini sağlar. Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](functions-monitoring.md). Örnek, siz etkinleştirene kadar hiçbir maliyet vermez.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma]

[Azure depolama kuyruğuna bağlanma]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp
