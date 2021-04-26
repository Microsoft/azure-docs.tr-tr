---
title: Komut satırından TypeScript işlevi oluşturma-Azure Işlevleri
description: Komut satırından bir TypeScript işlevi oluşturmayı ve ardından yerel projeyi Azure Işlevleri 'nde sunucusuz barındırma için yayımlamayı öğrenin.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c7b37700d93ea129ba46de81989f06dd2afe52ba
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831825"
---
# <a name="quickstart-create-a-typescript-function-in-azure-from-the-command-line"></a>Hızlı başlangıç: Azure 'da komut satırından TypeScript işlevi oluşturma

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Bu makalede, HTTP isteklerine yanıt veren bir TypeScript işlevi oluşturmak için komut satırı araçlarını kullanırsınız. Kodu yerel olarak test ettikten sonra, Azure Işlevlerinin sunucusuz ortamına dağıtırsınız.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

Bu makalenin [Visual Studio Code tabanlı bir sürümü](create-first-function-vs-code-typescript.md) de vardır.

## <a name="configure-your-local-environment"></a>Yerel ortamınızı yapılandırma

Başlamadan önce aşağıdakilere sahip olmanız gerekir:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) sürüm 3. x.

+ Azure kaynakları oluşturmak için aşağıdaki araçlardan biri:

    + [Azure CLI](/cli/azure/install-azure-cli) sürüm 2,4 veya üzeri.

    + Sürüm 5,0 veya üzeri [Azure PowerShell](/powershell/azure/install-az-ps) .

+ [Node.js](https://nodejs.org/), ETKIN LTS ve bakım LTS sürümleri (8.11.1 ve 10.14.1 önerilir).

### <a name="prerequisite-check"></a>Önkoşul denetimi

Azure CLı veya Azure kaynakları oluşturmak için Azure PowerShell kullanıp kullanmayacağınızı temel alan önkoşulları doğrulayın:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ Bir Terminal veya komut penceresinde, `func --version` Azure Functions Core Tools sürüm 3. x olduğunu denetlemek için komutunu çalıştırın.

+ `az --version`Azure CLI sürümünün 2,4 veya üzeri olduğunu denetlemek için ' i çalıştırın.

+ `az login`Azure 'da oturum açmak ve etkin bir aboneliği doğrulamak için ' i çalıştırın.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Bir Terminal veya komut penceresinde, `func --version` Azure Functions Core Tools sürüm 3. x olduğunu denetlemek için komutunu çalıştırın.

+ `(Get-Module -ListAvailable Az).Version`Sürüm 5,0 veya üstünü çalıştırın ve doğrulayın. 

+ `Connect-AzAccount`Azure 'da oturum açmak ve etkin bir aboneliği doğrulamak için ' i çalıştırın.

---

## <a name="create-a-local-function-project"></a>Yerel işlev projesi oluşturma

Azure Işlevlerinde bir işlev projesi, her birinin belirli bir tetikleyiciye yanıt verdiği bir veya daha fazla bağımsız işlev için bir kapsayıcıdır. Projedeki tüm işlevler aynı yerel ve barındırma yapılandırmalarına sahiptir. Bu bölümde, tek bir işlev içeren bir işlev projesi oluşturursunuz.

1. `func init`Belirtilen çalışma zamanına sahip *Localfunctionproj* adlı klasörde bir işlevler projesi oluşturmak için komutu aşağıdaki gibi çalıştırın:  

    ```console
    func init LocalFunctionProj --typescript
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
    
    `func new` Projenin seçtiği dile uygun bir kod dosyası ve *üzerindefunction.js* adlı bir yapılandırma dosyası içeren işlev adıyla eşleşen bir alt klasör oluşturur.

### <a name="optional-examine-the-file-contents"></a>Seçim Dosya içeriğini inceleyin

İsterseniz, [işlevi yerel olarak çalıştırmayı](#run-the-function-locally) atlayıp dosya içeriğini daha sonra incelemenizi sağlayabilirsiniz.

#### <a name="indexts"></a>Dizin. TS

*index. TS* , *function.jsüzerindeki* yapılandırmaya göre tetiklenen bir işlevi dışarı aktarır.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Bir HTTP tetikleyicisi için işlev, `req` *function.jsüzerinde* tanımlanan **HttpRequest** türünde değişkende istek verilerini alır. function.jsüzerinde olarak tanımlanan Return nesnesi `$return` Yanıt olur. 

#### <a name="functionjson"></a>function.json

*function.js* , `bindings` tetikleyici türü de dahil olmak üzere işlevin giriş ve çıkışını tanımlayan bir yapılandırma dosyasıdır. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

Her bağlama bir yön, tür ve benzersiz bir ad gerektirir. HTTP tetikleyicisinin tür ve çıkış bağlaması türünde bir giriş bağlaması vardır [`httpTrigger`](functions-bindings-http-webhook-trigger.md) [`http`](functions-bindings-http-webhook-output.md) .

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

1. Yerel Azure Işlevleri çalışma zamanı konağını *Localfunctionproj* klasöründen başlatarak işlevinizi çalıştırın:

    ```console
    npm install
    npm start
    ```
    
    Çıktının sonuna doğru, aşağıdaki satırlar görünmelidir:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > HttpExample aşağıda gösterildiği gibi görünmezse, büyük olasılıkla ana bilgisayarı projenin kök klasörü dışından başlatmış olursunuz. Bu durumda,  + Konağı durdurmak için CTRL **C** 'yi kullanın, projenin kök klasörüne gidin ve önceki komutu yeniden çalıştırın.

1. `HttpExample`İşlevinizin URL 'sini bu çıktıdan tarayıcıya kopyalayın ve sorgu dizesini ekleyerek `?name=<your-name>` tam URL 'yi yapın `http://localhost:7071/api/HttpExample?name=Functions` . Tarayıcı şöyle bir ileti görüntülemelidir `Hello Functions` :

    ![İşlevin sonuçları tarayıcıda yerel olarak çalıştırıldı](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    Projenizi başlattığınız Terminal Ayrıca, istek yaptığınız sürece günlük çıktısını gösterir.

1. Hazırsanız, **CTRL** + **C** 'yi kullanın ve `y` işlevler ana bilgisayarını durdurmayı seçin.

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Azure 'da işlev uygulaması oluşturma:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    [Az functionapp Create](/cli/azure/functionapp#az_functionapp_create) komutu, Azure 'da işlev uygulaması oluşturur. Node.js 10 kullanıyorsanız, olarak da değiştirin `--runtime-version` `10` .
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cmdlet 'i Azure 'da işlev uygulaması oluşturur. Node.js 10 kullanıyorsanız, ' `-RuntimeVersion` ye geçin `10` .

    ---
        
    Önceki örnekte, değerini, `<STORAGE_NAME>` önceki adımda kullandığınız hesabın adıyla değiştirin ve öğesini `<APP_NAME>` sizin için uygun olan bir genel benzersiz adla değiştirin. `<APP_NAME>` aynı zamanda işlev uygulamasının varsayılan DNS etki alanıdır. 
    
    Bu komut, burada yaptığınız kullanım miktarı için ücretsiz olan [Azure Işlevleri tüketim planı](consumption-plan.md)altında, belirtilen dil çalışma zamanında çalışan bir işlev uygulaması oluşturur. Bu komut Ayrıca, aynı kaynak grubunda, işlev uygulamanızı izleyebilmeniz ve günlükleri görüntüleyebileceğiniz ilişkili bir Azure Application Insights örneğini sağlar. Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](functions-monitoring.md). Örnek, siz etkinleştirene kadar hiçbir maliyet vermez.

## <a name="deploy-the-function-project-to-azure"></a>İşlev projesini Azure 'a dağıtma

Projenizi Azure 'a dağıtmak için temel araçları kullanmadan önce, TypeScript kaynak dosyalarından bir dizi JavaScript dosyası oluşturun.

1. TypeScript projenizi dağıtıma hazırlamak için aşağıdaki komutu kullanın:

    ```console
    npm run build:production 
    ```

1. Gerekli kaynaklarla, artık yerel işlevler projenizi, [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) komutunu kullanarak Azure 'daki işlev uygulamasına dağıtmaya hazırsınız demektir. Aşağıdaki örnekte, değerini `<APP_NAME>` uygulamanızın adıyla değiştirin.

    ```console
    func azure functionapp publish <APP_NAME>
    ```
    
    "Adı olan uygulama bulunamıyor..." hatasını görürseniz, birkaç saniye bekleyip yeniden deneyin. Bu işlem, Azure önceki komuttan sonra uygulamayı tam olarak başlatılamayabilir `az functionapp create` .
    
    Yayımla komutu aşağıdaki çıktıya benzer sonuçları gösterir (basitlik için kesildi):
    
    <pre>
    ...
    
    Getting site publishing info...
    Creating archive for current directory...
    Performing remote build for functions project.
    
    ...
    
    Deployment successful.
    Remote build succeeded!
    Syncing triggers...
    Functions in msdocs-azurefunctions-qs:
        HttpExample - [httpTrigger]
            Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
    </pre>

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-typescript)
